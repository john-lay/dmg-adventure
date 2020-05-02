# DMG Adventure

A port of Zelda's Adventure for the Philips CD-i to the Gameboy created with [GB Studio](https://www.gbstudio.dev/).

![screenshot 1](https://github.com/john-lay/dmg-adventure/raw/develop/screenshots/screen-1.png)
![screenshot 2](https://github.com/john-lay/dmg-adventure/raw/develop/screenshots/screen-2.gif)

## Graphics
Sprites created/modified with Photoshop and exported with the following settings:
* "Save for Web..." select PNG-8 from the template dropdown
* Tilemaps created with [Tiled](https://www.mapeditor.org/)


## New Scene checklist
Due to a bug where copy and pasted events default the affected actor to the player (currently using GB Studio 1.2.1) a number of scene initialisation scripts need to be manually adjusted. Here's a reminder (mainly for me) of what needs to be set up:
* Copy the `firerod animated` actor to the new scene
* Copy the `hide wand` script from the _vision henge_ `On Init` to the new scene's `On Init`
* Edit the copied `hide wand` script and change to actor reference to `Self (firerod) animated`
* Edit the copied `firerod animated` actor. For all actors _except_ the `zelda reference position` and the reference to the `Player`'s direction need to be set to the `Self (firerod) animated` actor
* Copy the `Joypad Input: Attach Script To Button` script from the _vision henge_ `On Init` to the new scene's `On Init`
* Edit the copied `Joypad Input: Attach Script To Button` script and change the parameter for the `Actor: Invoke Script` to the `firerod animated`
* Copy the `rupee - hundreds` and the `rupee - tens` actors to the new scene
* Copy the `heart 1`, `heart 2` and the `heart 3` actors to the new scene
* Copy the `initialise scene` script from the _vision henge_ `On Init` to the new scene's `On Init`
* Edit the copied `initialise scene` script:
  * (Under `set rupee counter`) Change the `init hundreds` script and rename `$L0$: Local 0` to `rupee - hundreds`
  * (Under `set rupee counter`) Change the `init tens` script and rename `$L1$: Local 1` to `rupee - tens`
  * (Under `set rupee counter`)Change the `init units` script and rename `$L2$: Local 2` to `rupee - units`
  * (Under `set rupee counter`) Change the `Actor: Set Animation Frame Using Variables` reference to `rupees - hundreds` and `rupees - tens` respectively 
  * (Under `hide hearts`) Change the `Actor: Hide` to `heart 1`, `heart 2` and `heart 3` respectively
  * (Under `set hearts`) For switch cases 1 and 2, change all actor references to `heart 1`
  * (Under `set hearts`) For switch cases 3 and 4, change actor references inside `full heart 1` to `heart 1` and all other actor references to `heart 1`
  * (Under `set hearts`) For switch cases 5 and 6, change actor references inside `full heart 1` to `heart 1` and `full heart 2` to `heart 2`. Change all other actor references to `heart 3`
