Getting Started
===============
Instructions for setting up your modding environment. Since the modding's done
in xml, there's no need for compilation/etc, you can use whatever editor
sounds good (I like Atom). There's no Aground-specific tools (yet) either, so
for now, this is all about learning Aground's structure.


Finding the Aground folder
--------------------------

Because install location can differ, I'm going to give the instructions
for finding your game files via Steam:

 1. Log on to Steam
 2. Right-click "Aground" in your Library
 3. Go to "Properties" (bottom of the list)
 4. Go to "Local Files" (tab at the top of the screen that pops up)
 5. Hit "Browse Local Files"

This should open a file browser right in your Aground folder.


Structure of the Aground folder
-------------------------------

We'll be ignoring most of the files and going directly to that "data" folder, which contains:

 - **core**: The core files of the game. Think of it as a "mod" that consists of all the game's pre-early-release content.
 - **fonts**: Font files, of course. Not used by most mods.
 - **keyboards**: Seems to be mappings of keyboards keys' "real names" to the symbols the game uses to represent them, ex: "BACK_QUOTE" to "`"
 - **mods**: Our home! This is where you put mods to load them.
 - **ui**: images used for the user interface, plus their mappings (uses same system as mods)

Take some time to browse around and get your bearings. Once you're ready, check out
the section on writing your first mod.
