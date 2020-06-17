# DMG Adventure

A port of Zelda's Adventure for the Philips CD-i to the Gameboy created with [GB Studio](https://www.gbstudio.dev/).

![screenshot 1](https://github.com/john-lay/dmg-adventure/raw/develop/screenshots/screen-1.png)
![screenshot 2](https://github.com/john-lay/dmg-adventure/raw/develop/screenshots/screen-2.gif)
![screenshot 3](https://github.com/john-lay/dmg-adventure/raw/develop/screenshots/screen-3.gif)

# Developer notes

## Graphics
Sprites created/modified with Photoshop and exported with the following settings:
* "Save for Web..." select PNG-8 from the template dropdown
* Tilemaps created with [Tiled](https://www.mapeditor.org/)
* All tiles and sprites reused from [Link's awakening disassembly project](https://github.com/zladx/LADX-Disassembly/tree/master/src/gfx) with the exception of:
  * Zelda's sprite (tweaked to fit in a 16x16 grid) from Oracle of Ages/Seasons
  * Collectable Ladder inspired by original Zelda ladder, but resized to 8x16
  * Candle inspired by original Zelda
  * Empty/full Bottle inspired by Tracy's Secret Medicine
  * Jade ring inspired by Oracle of Ages/Seasons
  * Game logo

## Compromises due to imposed restrictions
Each scene can only have 9 actors, 25 Frames and 9 triggers. The actor and frame restrictions are the most challenging and because of there are a number of compromises
* On screens without an enemy the wand animation is 3 frames in each direction. On scenes with enemies it is reduced to a single frame
* The collision detection triggers can only handle 1 enemy per screen. Any more triggers significant slowdown. 2 enemies is passable, any more is unbearable
* Screen transitions start Zelda at fixed central points, not where she left the screen
* The unit counter of ruppees is baked into the background
* The empty hearts indicator is baked into the background
* The boomerang can only be thrown on the screen it's found on. (Unlike the original game, it does not cost rupees to throw. Keeping with the original game, it doesn't return to Zelda when thrown)

## Improvements (wishlist)
A list of suggestions to improve the game/engine:
* Support multiple enemies per screen
* Use collected items on any screen
* Implement menu screen
* Implement save game
* The overworld walls should be drawn from a bottom up perspective
* Sound

## Artistic licence
Personal preference deviations from the original game:
* Ogbam forest shop restores all health when the shop keeper offers free Ogbam herb.
* Mobilin Inn restores all health when the barkeep offers some Andor cider.

# Technical notes

## Variable list

A listing of variables and their types in re-usable components (Actors/Scenes)

### Enemy
* `$L0$: Local 0` Enemy positionX - `number`. (The enemies on screen x position with respects to an origin of top-left)
* `$L0$: Local 1` Enemy positionY - `number`. (The enemies on screen y position with respects to an origin of top-left)
* `$L0$: Local 2` Spawn collectable - `number`. (0-2 Randomly set value. With 0 meaning the enemy dropped a rupee, 1 a heart and 2 nothing)
* `$L0$: Local 3` hitArea - `number`. (0-8 used to check a wand/enemy collision in surrounding tiles, not just 1-2-1 overlap which is rare)

### Global
* `$00$: Variable 000` has wand - `boolean`. (Indicates whether the player has collected the wand)
* `$01$: Variable 001` health - `number`. (0-6 Indicates the number of (half) hearts Zelda has)
* `$02$: Variable 002` wand posX - `number`. (The wand's on screen x position with respects to an origin of top-left)
* `$03$: Variable 003` wand posY - `number`. (The wand's on screen y position with respects to an origin of top-left)
* `$04$: Variable 004` zelda posX - `number`. (Zelda's on screen x position with respects to an origin of top-left)
* `$05$: Variable 005` zelda posY - `number`. (Zelda's on screen y position with respects to an origin of top-left)
* `$06$: Variable 006` enemy isAlive - `number`. (Indicates whether an on screen enemy is alive. Prevents the collision detection from firing again after an enemy is hidden. - should be a local variable)
* `$07$: Variable 007` collectable - `byte`.
  * (Flag 1 represents the enemy dropping a rupee)
  * (Flag 2 represents the enemy dropping a heart)
  * (Flag 3 is the boomerang which can only be used on the screen it's found on)
  * (Flag 4 is the empty bottle)
  * (Flag 5 is the filled bottle)
  * (Flag 6 is the vial of wind)
  * (Flag 7 is the firestorm spell)
  * (Flag 8 is the dagger)
* `$08$: Variable 008` rupees - `number`. (Indicates the number of rupees Zelda has collected)
* `$09$: Variable 009` has ladder - `boolean`. (Indicates whether the player has collected the ladder)
* `$10$: Variable 010` purchasable - `byte`.
  * (Flag 1 represents the shield bought from Ogbam forest shop)
  * (Flag 2 represents the candle bought from Ogbam forest shop)
  * (Flag 3 represents the Calm spell bought from the Moblin inn)
* `$11$: Variable 011` dungeon items - `byte`.
  * (Flag 1 represents the map)
  * (Flag 2 represents the compass)
  * (Flag 3 represents the red boots)
  * (Flag 4 represents the jade ring)
* `$12$: Variable 012` defeated sheepmen - `byte`.
  * (Flag 1 represents the first (red) sheepman - encountered 1 room north of the jade ring)
  * (Flag 2 represents the second (blue) sheepman - encountered east, north, east from the jade ring)
  * (Flag 3 represents the third (yellow) sheepman - encountered just before the fight with Lort)
* `$13$: Variable 013` lort given speech - `boolean`. (Indicates whether Lort has given his monologue)
* `$14$: Variable 014` bone1 posX - `number`. (Lort's first bone's screen x position)
* `$15$: Variable 015` bone1 posY - `number`. (Lort's first bone's screen y position)
* `$16$: Variable 016` bone offscreen - `boolean`. (Indicates whether Lort's bones have been thrown off screen)
* `$17$: Variable 017` bone2 posX - `number`. (Lort's second bone's screen x position)
* `$18$: Variable 018` bone2 posY - `number`. (Lort's second bone's screen y position)
* `$18$: Variable 018` lort health - `number`. (Lort's health, starts and 10)

## New Scene checklist
The best way to create a new scene is to copy and paste another scene. If you need to create one from scratch, there are a number of steps that need to be taken.

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
