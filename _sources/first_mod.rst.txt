Your First Mod
==============

We're going to make something simple for our first--how about turning a bar
of iron back into ore? This means we'll need to add a recipe and decide where
recipe is available (how about the smelter?). Please note that the length of
this guide doesn't reflect the difficulty of modding Aground! Modding itself is
easy, but there is some starter knowledge required (like how to read XML), which
I cover.


Quickie: how to read paths
--------------------------

A path is the sequence of folders you need to go through to reach some
destination (like a file). Each folder is separated by / (\ on Windows).
For example, if you're starting from the Aground folder we found through Steam,
the path to the adult dragon's art file is :code:`data/core/enemies/adult_dragon.png`.
In this example, we first go to the "data" folder, then the folder named "core" inside it,
then the folder named "enemies" inside *that*. The file we're after is found there,
and is named "adult_dragon.png". Meanwhile the path to the "enemies" folder is
:code:`data/core/enemies`.

I'll try to always give paths relative to the Aground root folder (again,
the one we found through Steam). Make sure you can read and follow paths, as
they're used extensively in this guide.


Setting up a minimal mod
------------------------

Each mod consists of a minimum of two things: a folder to contain it, and a mod.xml
to describe it. Go ahead and create a new folder in :code:`data/mods/` and give it
a name (what about "my_mod"?). Now we just need to create the mod xml. While we
could type it by hand, there's one we could use--copy :code:`data/mods/full/mod.xml`
and paste it into our own folder, making :code:`data/mods/my_mod/mod.xml`. Open it:

.. code-block:: xml

  <?xml version="1.0" encoding="utf-8" ?>
  <mod>
      <name>Full Version</name>
      <description>Expands the core game into the full version.</description>
      <author>Fancy Fish Games</author>
      <version>1.4.0</version>
    <website>http://fancyfishgames.com/Aground/</website>
    <disableWarning>This will disable all new islands and features after the demo of the game.</disableWarning>
      <init>
          <include id="music/music.xml" />
       <include id="achievements.xml" />
      <include id="items/items.xml" />
      <include id="lang/languages.xml" />
      <include id="vehicles/vehicles.xml" />
      <include id="debris/debris.xml" />
      <include id="structures/structures.xml" />
          <include id="enemies/enemies.xml" />
          <include id="science2/science2.xml" />
      <include id="scenes/scenes.xml" />
      <include id="npcs/npcs.xml" />
      <include id="ships/ships.xml" />
      </init>
  </mod>

This isn't in the form we need--it describes another mod. For now.


Reading and modding XML (the basics)
------------------------------------

If you haven't seen xml before, mod.xml might be a bit overwhelming, but it's
straightforward once you can recognize its structure. XML is made primarily
of **elements**. Elements are started and ended by tags, or contained all within
one (and ended by a backslash). Elements can contain other elements (their "children"),
and thus be contained by tags (their "parents"), giving us a kind of hierarchy. It's
clearer with examples:

 - :code:`<name>Full Version</name>` is an **element**. See how it starts with the **tag** :code:`<name>` and ends with the same **tag**, but with a :code:`/` out front: :code:`</name>`
 - :code:`<include id="items/items.xml" />` is also an **element**. Notice that it's all in the same pair of <braces>, but ends with a :code:`/`, just like name's closing tag. It has a parent (init)
 - :code:`<init> ... </init>` is also an **element**. Elements can span multiple lines and contain other elements. The "include" elements are its children
 - :code:`<mod> ... </mod>` is a special type of **element**: a **root element**. Mod is the parent of all other elements in this xml

We need to change this file to suit us, and we can reason about what we do and
don't need based on the names of the elements:

- **mod**: the root element. Since it contains everything else, we assume it defines how everything else should be read by the game. We definitely need this.
- **name**: This is how the game knows what our mod is called. Again, obviously necessary.
- **description**, **author**, and **version**: Extra information that's more for the user. It's not immediately clear what here we need--I experimented, and it seems **author** and **description** are mandatory, **version** is optional (still use it!).
- **website**: The author's personal website. Seems optional, and is.
- **disableWarning**: Sounds like a warning that pops up if the mod is disabled. Disabling our mod won't do anything strange (we're only adding a recipe), so we don't need this.
- **init**: "Init" (short for "initialize"?) seems like it includes instructions on how to *initialize* the mod. Since a mod has to be initialized to do anything, necessary.
- **include**: While we'll definitely need to **include** things in our mod for it to do anything useful, we don't yet have anything to include; we can remove this.

Reasoning based on element names is foundational to modding Aground. The
devs did a great job choosing names for things, so apart from the occasional
oddity (version is optional, author is not), the process is nice and logical.


Customizing mod.xml and launching your mod
------------------------------------------

It's time to tweak :code:`data/mods/my_mod/mod.xml` until it's in the form you want.
Mine would look like:

.. code-block:: xml

  <?xml version="1.0" encoding="utf-8" ?>
  <mod>
      <name>Tutorial Mod</name>
      <description>Adds a recipe for un-smelting iron.</description>
      <author>Your Name Here</author>
      <version>0.1.0</version>
      <init></init>
  </mod>

(One thing we haven't covered is the first line, ""<?xml"...--don't worry about it for now,
just know you need to include that line in every xml document).

Our mod doesn't do anything, but it's still a valid mod, so go ahead and launch the game!

Once it's up, go to the mods tab. You should see your mod there. If you don't,
there's something wrong with its *structure*: make sure there's two folders in
the mods folder ("full" and "my_mod" or whatever you named it), and that "my_mod"
contains a file named "mod.xml". If you don't see anything on hovering over your
mod, there's something wrong with the mod.xml itself--compare yours to my example
and try again. There's also xml parsers online to make sure your formatting's alright.

If it works, congratulations! You've just made your own valid mod!


Adding a recipe
---------------

Just like before, we're going to let the dev team do the heavy lifting for us.
This time, though, we're going into the core files of the game itself (sort of).
Go to :code:`data/core` and see if anything seems reasonable. There's several
folders, but nothing explicitly called "recipes". That said, recipes belong
to structures, and there is a :code:`data/core/structures`. Sure enough, it
contains "crafting.xml". Perfect! Open that up. We're looking to
un-craft an iron bar, so all we need to do is find the recipe for an iron bar and
reverse it. I found it right at the top (around line 5):

.. code-block :: xml

  <recipe id="smelt_iron" creates="iron_bar" type="smelter">
    <item id="iron" count="2" />
  </recipe>

Beautiful. Let's employ our xml skills to parse what's going on. First off, the
"root" of this little snippet is a recipe element. Unlike the elements we saw in
mod.xml, this element has **attributes**. These are just named bits of information
within the tag itself. For example, our recipe tag has the attribute 'id="smelt_iron"'.
Again, devs did a great job with the naming, so we immediately know that this
recipe object is identified by the game as "smelt_iron". We also have what it creates
(an iron bar) and the type of recipe (a smelter recipe).

The recipe has its ingredients as its children, and in this case, that's two
pieces of iron. This is almost everything we need! The one piece "missing" is how
many iron bars the recipe creates. If we've played the game, we know that this makes
1 iron bar, but we need to make **2** iron ore. **Recipes create 1 thing by default,**
but if we can think of a recipe that makes more than that, we're all set. What about arrows?

.. code-block :: xml

  <recipe id="craft_arrow" creates="arrow" count="2" type="workbench">
    <item id="wood" count="1" />
  </recipe>

We've learned two important things. One, the recipe element can have a count tag,
just like the item element. Two, the item element has a count of 1 specified, so
it *seems* like it won't default to 1...it does, but it's important to keep an
eye open for possible issues like that.

Now we have all the info we need to "reverse" the iron-smelting recipe. Do it
yourself and then check against mine.

.. code-block :: xml

  <recipe id="unsmelt_iron" creates="iron" count="2" type="smelter">
    <item id="iron_bar"/>
  </recipe>

We're not quite done. Remember how I mentioned that recipe is the "root" of this
snippet? It's not the root of the whole crafting.xml document. We need to put things in a
structure the game can understand and, for sanity's sake, mimic the way the devs
do things whenever possible. That means we go back to our "my_mod" folder and create
a "structures" folder, then a "crafting.xml" inside that
(so :code:`data/mods/my_mod/structures/crafting.xml`). We'll also mimic the
setup of the core crafting.xml--there, the iron smelting recipe is a child of
the "recipes" element, and "recipes" is the root. Using all that together, our
crafting.xml file looks like this:

.. code-block :: xml

  <?xml version="1.0" encoding="utf-8" ?>
  <recipes>
    <recipe id="unsmelt_iron" creates="iron" count="2" type="smelter">
      <item id="iron_bar"/>
    </recipe>
  </recipes>


Making the mod do something
---------------------------

The only thing left to do is make sure our mod loads this file when it initializes.
Go back to our mod.xml and add it:

.. code-block:: xml

  <?xml version="1.0" encoding="utf-8" ?>
  <mod>
      <name>Tutorial Mod</name>
      <description>Adds a recipe for un-smelting iron.</description>
      <author>Your Name Here</author>
      <version>0.1.0</version>
      <init>
        <include id="structures/crafting.xml" />
      </init>
  </mod>

Another quick note: the path to our crafting.xml is "relative" to the my_mod
folder, just like the paths in this tutorial are relative to the Aground folder.
Because my_mod's within the Aground folder, it's just the tutorial path minus the
path to my_mod: :code:`data/mods/my_mod/structures/crafting.xml` -> :code:`structures/crafting.xml`.

We're all set! Launch the game again and see what happens. You should have
a brand new recipe in your smelter...congratulations on modding Aground!
