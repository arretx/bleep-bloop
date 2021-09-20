## What are Templates and why should I care?

I have to admit that the first time I looked into this topic I was completely lost.  So, I'll bring you up to speed as quickly as possible.

If you're familiar with Microsoft Word Templates, or Google Doc Templates, or Photoshop templates, or a cardboard stencil, then you have a clue...let's begin there.

A template is a pre-formatted document...or...thing, that references information from somewhere else, then displays it based upon how you want it displayed.  Think mail merge.  You write a document, then you add some sort of goofy place-holder for the salutation, and magically the list of names you want to send information is inserted one at a time into that spot.  Dear Fred.  Dear Nancy.  Dear Bob.

## Templates in Home Assistant

So, let's put the metaphors aside and go try out a template from scratch, because while the documentation at [Home-Assistant.io](https://www.home-assistant.io/docs/) is ample, it can be rather confusing sometimes, because it's written by many people with many different writing styles, and I teach in metaphors.  So, that's my approach.  Hopefully you'll get the picture.

## Syntax

Yes.  There is a syntax.  That means you gotta type things out correctly or you get errors.  I learned what I know from [this page](http://jinja.pocoo.org/docs/2.10/templates/).

## Intro to Templates

Before I can give you an example, you need to go visit your Home Assistant dashboard, then click the little `< >` at the bottom of the left side-bar. This area is called the **Developer Tools**.  You'll use it quite often to test things, get frustrated, test more things, bang your head on the table, and eventually understand something.

Click it now to view a list of your device `States`.

Pick any entity in that list (not with your mouse...with your _mind_!)  Now if you need to, copy all of the crap in the attributes column and stick it in a notepad, or Evernote, or nano it, or write it down, or something.  Maybe even just open a whole new tab and go to your instance of Home Assistant again.  I call this **"Two Tabs Access"**.  That's not actually true.  I don't call it anything.  In fact, I probably have 8 identical tabs open right now.

## The Template Testing Grounds

Now, in one of your browser tabs, go to the **Developer Tools** area in Home Assistant and click on the fourth icon from the left.  It looks like a gray piece of paper with the `< >` brackets in it and a dog-eared corner.  That takes you to the page called **Templates**.

## Example

### Explanation

For this example, I'm going to use the sun component because it's installed by default on everyone's Home Assistant instance.  If you've uninstalled it, then you're screwed.  Well, not really, but you'll need to deduce a new solution based upon my example.

In your `States` list, you should see `sun.sun` with a state of something like `above_horizon` or `below_horizon` or something else depending upon what time of day it is around your parts.  In the attributes column you'll seee a list of data pairs.

~~~
next_dawn: <some big ugly time code>
next_dusk: <more big fat ugly time code>
...
...
elevation: <a number that represents the angle of the sun>
azimuth: <if I say this word in my head one more time I'm going to go insane>
rising: <the direction the sun is going relative to our little blue planet>
...etc.
~~~

These are called attributes.  You can use **Templates** to create place-holders for exactly the information you want inside of automations and scripts and NodeRED, etc.  It's amazing.

### Let's Make That Example Already Dude

Okay.  You've got the **Template** page open in one of your Chrome tabs (see above if you have a short term memory) I would presume.

When you first load the **Templates** page, it pre-populates the editor with a bunch of crap that's confusing.  There's a little header tucked in there called "Template Editor."  Select ALL of the text under the "Template Editor" and delete it.  Now you have a blank canvas.

Where were we?  

Oh...right...the sun component.  This isn't limited to the sun component.  What I'm about to show you works with any entity in Home Assistant.  The syntax for a basic template looks something like this:

`{{ states.sun.sun.state }}`

Typing this into the **Template Editor** should produce a result in the right column of the **Templates** page that matches the state of the device on the `States` page.  For example, it may report `above_horizon`.

Congratulations.  You've just written your first template.  This format can be used throughout Home Assistant to reference specific data and uses it logical statements (if this is that then do this but not if that is that, because if that is that and this plus that isn't this then do that instead.)  Relax.  Take a deep breath.  Let's move on.

### Accessing Attributes

So...you don't care if the sun is "above the horizon."  What you want to know is how to extract the angle of the sun from the sun component and use it as a condition to tell Home Assistant whether or not to do something.

Let's get the angle of the sun (or, the brightness of a light, or the temperature of your underwear...you name it.)

### Angle of the Sun from a Template

The format is simple.  First, let's make a template that lists an entitie's attributes in a messy array.  _This isn't a pre-requisite.  It's simply an example outlining one of the ways to quickly list the attributes of an entity_.

`{{ states.sun.sun.attributes }}`

Typing this into your **Template Editor** will show you a list of attributes in a gnarled array format (if you're not familiar with this format you soon will be.)

It should look something like this:

`{'next_dawn': '2019-06-22T11:47:20+00:00', 'next_dusk': '2019-06-23T03:11:34+00:00', 'next_midnight': '2019-06-22T07:29:37+00:00', 'next_noon': '2019-06-22T19:29:27+00:00', 'next_rising': '2019-06-22T12:16:24+00:00', 'next_setting': '2019-06-23T02:42:30+00:00', 'elevation': -23.61, 'azimuth': 324.13, 'rising': False, 'friendly_name': 'Sun'}`

**But that's not what you want!**  You want _only_ the elevation of the sun.  Or...you want someething else...right?

To get _only_ the elevation of the sun according to the sun component, you would type this:

`{{ states.sun.sun.attributes.elevation }}`

Try it.  If you don't see only the value of the elevation, then you have a syntax error (mua ha ha ha ha...damn syntax.)

!!! warning "Disclaimer: I Am Not A Programmer..."
    Well, at least I wasn't.  Home Assistant is slowly forcing me to learn new languages.  So, if I do something that's not `elegant` or `optimal` or `acceptable by the standards of the gods of the interwebs` but it still accomplishes a goal, then I'm winning, but I am definitely not an authority on this stuff beyond what I know is working.

There's another way to report the same information:

`{{ state_attr('sun.sun','elevation') }}`

Either way, you get the same information.  

!!! note
    The masters in charge of this whole universe have suggested that the second method is the preferred method, fyi.

### Conditionals: Is a State This, or That...and Now What?

In the **Template Editor**, you can test a state or attribute resulting in either a `True` or `False` result.  Or you can create an `if then` statement to define your own custom results.  Here's how.

In the **Template Editor**, type:

`{{ is_state('sun.sun','above_horizon') }}`

The result is either going to be `True` or `False`.  You can use this in conditional statements, e.g., "If this is True, then do this, otherwise, dont."

You can also build small algorithms like the following which defines custom results in the form of a text string:

~~~
{% if is_state('sun.sun','above_horizon') %}
Awesome, the sun is up!
{% endif %}
~~~

-or-

~~~
{% if is_state('sun.sun','above_horizon') %}
Do this
{% else %}
Do this instead.
{% endif %}
~~~

-or-

~~~
{% if is_state_attr('sun.sun','elevation') > 20 %}
Do this
{% else %}
Do thiss instead.
{% endif %}
~~~

This last example uses the value of the elevation attribute as the test.

## Conclusion

Did you understand any of that?  If so, share it up.  There are a lot of people like you who were as confused as I was when I first started.  If not, read it again and keep trying.  I assure you if you persevere, you will figure it out.



