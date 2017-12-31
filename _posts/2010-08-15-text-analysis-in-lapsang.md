---
title:  "Text analysis in Lapsang"
permalink: /2010/text-analysis-in-lapsang
---

Lapsang is my personal programming project that will download article titles from
an RSS-feed and then recommends which ones are probably the most interesting for the
user. Initially it will only use the title for analysis (later versions may use the
actual article content, url, poster, etc.). For every title the user must tell if it
sounds attractive to read and based on this input the program will learn the interests
of the user. As soon as the user rates a title, the scores of the individual words of
this title will be adjusted. For attractive titles the word score will increase and
for uninteresting titles it will decrease. Based on this word scores the program can
give a recommendation for new titles. As soon as a title contains words that the program
saw before it will use the word scores to calculate a recommendation.

Not all words in a title will be used for scoring. Stop words like 'a', 'the', 'and', etc.
can be ignored as they add no significant value. How about singular and plural, should
'language' be considered the same word and thus have the same score as 'languages'? And how
to handle verbs, how should I deal with present and past? For simplicity matters I will
assume all titles will be in English as multilingual text analysis is way over my head for
now :-) Probably it is sufficient to store only the stem of a word. There are various
[stemming](http://en.wikipedia.org/wiki/Stemming) algorithms available and I found a C#
implementation for Porter stemming that I could integrate in my own tokenizer. As this text
analysis is getting more and more complex I decided to take a step back and stop my own
implementation and have a look at already available open source libraries.

Lucene.NET (a port of the Java search engine) is a mature project and contains various text
analyzers. Maybe other parts of this library are useful in my program, too, but first I will
focus on the text analysis. I downloaded the binaries from the
[Lucene.NET website](http://lucene.apache.org/lucene.net/) to start experimenting. The library
is literally ported from the Java version (easier to maintain for them, they say) so the API
feels very Java-ish and not so .NET-ish. The download contains the core Luce.Net.dll and various
additional libraries for more advanced analysis purposes.

I created a temporary project and added references to the Lucene.Net.dll and the Snowball.Net.dll
(for language-specific analysis using stemming). The following code shows the results using
various analyzers to tokenize a text:

``` cs
using System;
using System.IO;

using Lucene.Net.Analysis;

namespace TryTextAnalysis
{
class MainClass
{
static void Main (string[] args)
{
    string title =  "My husband is a programmer; I have no idea what that means.";
    Console.WriteLine(title);

    ShowTokens(title, new WhitespaceAnalyzer());
    ShowTokens(title, new SimpleAnalyzer());
    ShowTokens(title, new StopAnalyzer());
    ShowTokens(title, new Lucene.Net.Analysis.Standard.StandardAnalyzer());
    ShowTokens(title, new Lucene.Net.Analysis.Snowball.SnowballAnalyzer("English", StopAnalyzer.ENGLISH_STOP_WORDS));
}

static void ShowTokens(string text, Analyzer analyzer)
{
    Console.WriteLine(analyzer.GetType());
    TokenStream stream = analyzer.TokenStream("text", new StringReader(text));
    while (true)
    {
        Token token = stream.Next();
        if (token == null)
        {
            break;
        }
        Console.Write(" [{0}]", token.TermText());
    }
    stream.Close();
    Console.WriteLine();
}
}
}
```

The results when you run this program are as follows:

```
My husband is a programmer; I have no idea what that means.
Lucene.Net.Analysis.WhitespaceAnalyzer
 [My] [husband] [is] [a] [programmer;] [I] [have] [no] [idea] [what] [that] [means.]
Lucene.Net.Analysis.SimpleAnalyzer
 [my] [husband] [is] [a] [programmer] [i] [have] [no] [idea] [what] [that] [means]
Lucene.Net.Analysis.StopAnalyzer
 [my] [husband] [programmer] [i] [have] [idea] [what] [means]
Lucene.Net.Analysis.Standard.StandardAnalyzer
 [my] [husband] [programmer] [i] [have] [idea] [what] [means]
Lucene.Net.Analysis.Snowball.SnowballAnalyzer
 [my] [husband] [programm] [i] [have] [idea] [what] [mean]
```

The SnowBallAnalyzer suits my needs so now I can start coding the word scoring algorithm.
