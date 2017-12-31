---
title:  "Toolstripmenuitem click handlers"
permalink: /2010/how-to-determine-if-a-toolstripmenuitem-has-handlers-registered-to-its-click-event
---

## How to determine if a toolstripmenuitem has handlers registered to its click event

In one of the WinForms applications I maintain there's something that annoyed me for quite
some time (but I never took the time to fix it). This application has a large menu with
lots of items. It contains some wellknown submenus (injection points) and there's a
mechanism to let modules dynamically inject their menu items. The fact is that the wellknown
submenus were rendered even if they didn't have any children resulting in useless menu items
cluttering up my screen.

So all I had to do is remove the menuitems that have no children and that have no registered
handlers responding to the Click event, sounds easy. The problem is that there is no easy way
to determine if there are handlers registered to the click event of a ToolStripMenuItem. This
seems to be by design, you can only add and remove handlers to an event but you cannot iterate
them or so. By using Reflector I found out how the ToolStripMenuItem works internally.
System.Windows.Forms.ToolStripMenuItem is derived from System.Windows.Forms.ToolStripItem and
then from System.Component.ComponentModel.

When you write in your form:

``` cs
// In the actual form
myToolStripMenuItem.Click += new System.EventHandler(OnMyToolStripIMenuItemClick);
```

the ToolStripItem class adds the handler to the protected property Events of its base class
(ComponentModel) using EventClick as a key.

``` cs
// In ToolStripItem
internal static readonly object EventClick;

public event EventHandler Click
{
   add { base.Events.AddHandler(EventClick, value); }
   remove { base.Events.RemoveHandler(EventClick, value); }
}

// In ComponentModel
protected EventHandlerList Events
{
   get
   {
      if (this.events == null) this.events = new EventHandlerList(this);
      return this.events;
   }
}
```

My first idea was to create a derived class MyToolStripMenuItem so that I could access the
Events property:

``` cs
public class MyToolStripMenuItem : ToolStripMenuItem
{
   public bool HasRegisteredHandlersForClickEvent
   {
      get
      {
         // THIS IS NOT WORKING!!!
         return this.Events[EventClick] != null && this.Events[EventClick].GetInvocationList().Length > 0;
      }
   }
}
```

The problem is the the key used to access the handlers in the EventHandlerList is
marked as internal so that my derived class cannot use it. Fortunately reflection can solve
this, it's more or less a hack and it is not guaranteed to work in future versions of the
framework but for now it does the job exactly as I want to. I don't need a derived class
anymore to access the Events property so I created a static helper method somewhere in our
framework:

``` cs
public static bool HasRegisteredHandlersForClickEvent(ToolStripItem item)
{
   // Get the protected Events property and the internal static readonly field EventClick via reflection.
   var eventsProperty = item.GetType().GetProperty("Events", BindingFlags.Instance | BindingFlags.NonPublic | BindingFlags.FlattenHierarchy);
   var keyField = item.GetType().GetField("EventClick", BindingFlags.Static | BindingFlags.NonPublic | BindingFlags.FlattenHierarchy);

   var eventList = (EventHandlerList)eventsProperty.GetGetMethod(true).Invoke(item, null);
   var theDelegate = eventList[keyField.GetValue(item)];

   return theDelegate != null && theDelegate.GetInvocationList().Length > 0;
}
```

And finally the method to remove the nonworking menu items (ie. no children and no handlers
registered for click event):

``` cs
// First call:  RemoveNonWorkingMenuItems(mainMenu.Items);

public static void RemoveNonWorkingMenuItems(ToolStripItemCollection toolStripItems)
{
   if (toolStripItems == null) return;

   var toBeRemoved = new List< ToolStripItem>();
   foreach (ToolStripItem item in toolStripItems)
   {
      var dropdownItem = item as ToolStripDropDownItem;
      if (dropdownItem != null && dropdownItem.HasDropDownItems)
      {
         // Recursive walk through the submenus.
         RemoveNonWorkingMenuItems(dropdownItem.DropDownItems);
         // After all the children are removed,
         // this item may be empty.
         if (!HasRegisteredHandlersForClickEvent(dropdownItem) && !dropdownItem.HasDropDownItems)
         {
            toBeRemoved.Add(dropdownItem);
         }
      }
      else
      {
         if (!HasRegisteredHandlersForClickEvent(item))
         {
            toBeRemoved.Add(item);
         }
      }
   }
   foreach (ToolStripItem item in toBeRemoved)
   {
      toolStripItems.Remove(item);
   }
}
```

Sorry for the layout of the code snippets but I hope you find it useful.
