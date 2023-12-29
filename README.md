# CFA Database

This repository represents the latest database edition for CFA. It can be used in conjunction with auto updates or on its own.

## How to use with CFA

To use the database without auto updates you have to:

1. Download or clone the whole repository and then extract it on top of your CFA.
2. Copy Version.ini from the root into Text folder.

This will provide you with a working copy of CFA even if your auto updates are not working for some reason (e.g. you are using a Windows emulator).

For new updates, you will have to redownload the repository every time a new update releases, so it is better to just clone the repository and pull from it.

## Development

### Text Data

The `Text` folder contains individual fraction text files written in GML. New entries are added consecutively into their respective files, depending on clan/nation. Please be aware that all of the text files in this repository use Windows-1251 encoding for compatibility purposes. The exact syntax of those files will be outlined in a section below.

### Image Data

`CardSprite` and `CardSpriteMini2` contains images that correspond to the database entries, every image is stored in `.jpg` format and their names start with an `n` and then contains a card number.

`Sprite` folder contains sleeve images, which are all named with letter `s` and then their respective number, in `.png` format. New sleeves should be added consequitevely.

- `CardSprite` and `Sleeves` images should be 300 pixels in width and approximately 437 pixels in height
- `SleevesMini` folder is only present for sleeves. Images here should be 60 pixels in width and approximately 86 pixels in height.
- `CardSpriteMini2` and `SleevesMini2` images should be 75 pixels in width and approximately 109 pixels in height.

It is strongly adivsed to compress the `.jpg` card images after resizing them to reduce the amount of time and traffic needed to download the images. You can use [kraken.io](https://kraken.io/web-interface) website to resize images en masse, for example.

After new card images are added, do not forget to update the `.md5sums` files by running `md5.bat` file if you're on Windows or refer to **Checksum Files** section if you're using other OS. **This is mandatory if you are updating already existing images.**

### Versioning File

`Version.ini` represents current Text and Client versions and is used for update purposes:

- `Text` version is used to trigger database updates, should be incremented by 1 upon every new release
- `Client` version is used to trigger client updates, should be incremented by 1 upon every new release
- `Sleeve` is used as a counter for maximum amount of sleeves in current version. Changing it will trigger sleeve updates.

### Branch workflow

The first person starting to work on the next update should create that branch's update from `master`. The branch should be named `vXXX`, where `XXX` is the current Text version number + 1.

Contributors that would like to add like something to that update should branch off of that branch. Preferably, the branch should be named something like `vXXX-feature-description`, but it doesn't really matter. After you pushed changes to your branch, create a Pull Request targeting the original `vXXX` branch. If that branch is already gone, re-merge with the latest vXXX branch (or create it from master) and re-target the Pull Request.

### Checksum Files

`.md5sums` files are being auto generated by md5deep software, individual lines are ordered alphabetically by file name. They are used to perform a client-side integrity check of the images folder and then sync missing or outdated images. To re-generate the files, from the root execute two commands:

`.\md5deep64.exe -l .\CardSprite\* | sort /+37 > CardSprite.md5sums`

`.\md5deep64.exe -l .\CardSpriteMini2\* | sort /+37 > CardSpriteMini2.md5sums`

## Tools

The primary text editor of choice that most of the team uses is [VSCode](https://code.visualstudio.com/). You can download it to work with CFA database locally or launch it right from this repository's page by hitting the dot (period) key on your keyboard.

There are a few reasons for that, but mainly it's that it can support Windows-1251 encoding without issues, allows for quick search among multiple text files, but most importantly, it has support for GML syntax as a plug-in. It also supports git out of the box and has UI that does not require command line knowledge.

CFA repository is already configured to support WIndows-1251 encoding for VSCode, it is also configured for this extension to highlight the GML syntax: [GML Support](https://marketplace.visualstudio.com/items?itemName=liaronce.gml-support) (if you're working locally you have to install it first).

If you use any other editors and will be able to configure it in a similar fashion, don't hesitate to add the configuration to this repository as well.

## Basic Card Syntax

Any `.txt` file in this repository represents a script that is being executed at the launch of CFA. Every script is being executed line by line, from top to bottom. Here is a sample card syntax:

```
CardStat = 8230
{
global.CardName[CardStat] = 'Chakrabarthi Divine Dragon, Nirvana'
global.CardText[CardStat] = 'Dragon Empire/Flame Dragon

ACT [VC] 1/Turn: COST [Discard a card from your hand], choose a grade 0 card from your drop, and call it to RC.

AUTO [VC]: When this unit attacks, COST [Counter Blast 1], and this unit and all of your units with the overDress ability get power +10000 until end of turn.'
global.UnitGrade[CardStat] = 3
global.DCards[CardStat] = 1
}
global.PersonaRide[CardStat] = 1
global.PowerStat[CardStat] = 13000
global.DefensePowerStat[CardStat] = 0
```

First, this script assigns number 8230 to CardStat variable, this is the card's number in the database.
Then it writes a bunch of properties to arrays that are added to the global visibility scope (hence the `global.` syntax). Let's take a look at them one by one:

```
global.CardName[CardStat] = 'Chakrabarthi Divine Dragon, Nirvana'
```

This line writes the card's name into the global array `CardName`.

Notice how it uses single quotes to delimit string values. Single quotes in GML allow for use of double quotes inside the text, which can be quite useful for CFA. It also allows to use line breaks inside the string (which we will see actively being used in the next line).

But it also means that single quotation marks cannot be used as apostrophes. Instead, CFA database uses a special character from Windows-1251 encoding called **"right single quotation mark"** - `’`. If you use Windows and have a numpad on your keyboard, you can add that character by inputting number 39 on your numpad while holding left Alt key. Alternatively, you can just copy it from somewhere. VSCode usually highlights this character in a yellow box as a warning for it's visual similarity to a normal single quote.

By the way, those curly braces before this line don't add anything to the syntax, they are just used for separating main database properties from mechanical properties such as power and shield, a tradition that goes back to the early days of CFA when power and shield values were added into a separate file `UnitPower.txt`.

```
global.CardText[CardStat] = 'Dragon Empire/Flame Dragon

ACT [VC] 1/Turn: COST [Discard a card from your hand], choose a grade 0 card from your drop, and call it to RC.

AUTO [VC]: When this unit attacks, COST [Counter Blast 1], and this unit and all of your units with the overDress ability get power +10000 until end of turn.'
```

This line writes the text of the card into a global array `CardText`. On the first line of the text we always write first clan or nation and then card's races, each is separated by a slash (`/`).

After that comes the card's restrictions in parentheses, if there are any, like this:

```
(PREMIUM JP & EN: Restricted to 1.)
```

Then come the card's effects like they are written on the card. CFA has a syntax parser that replaces some words with icons so that the CFA textbox would look close to the real card's textbox.

Notice how `ACT`, `AUTO`, `CONT`, `1/Turn` or other effects and abilities are never surrounded by square brackets (which are only reserved for zone requirements) and circles are only written specifically as `VC`, `RC` and `GC`. Also numbers after cost, like `Counter Blast 1` never come in parentheses. Words like `power` are usually separated by spaces, even if they come after a slash so always try to go for `power +5000 / shield +5000`.

```
global.UnitGrade[CardStat] = 3
```

This line assigns the card's grade to `UnitGrade` array _(even if it isn't a unit, the name comes from a time when there were only Units in the game)_.

```
global.DCards[CardStat] = 1
```

This line assigns the card's nation code. All cards in a file usually come from the same nation so they all have the same number, just copy it.

If a card has multiple nations, add it to the file of the nation that comes first on the card itself, and then write the second one into `global.DCards2[CardStat]` property.

If a card has a clan instead of or in addition to the nation, we add it using `global.CardInClan[CardStat]` property. One can be in multiple clans as well, using syntax similar to nations: `global.CardInClan2[CardStat]`. The game supports clan definitions of up to `global.CardInClan9[CardStat]`, but only two nations at the same time.

For cards that belong to all clans and nations we use `Cray Elemental.txt` text file and a specific number.

Also, keep in mind, that cards that have `DCards` parameter added to them are considered legal in the Standard format. Legality in formats such as V and Premium will be outlined later in this document.

```
global.PersonaRide[CardStat] = 1
```

This next line adds the Persona Ride ability to the card, which is usually present for all Grade 3 cards in the Standard format (as of writing of this document). There are also properties for other card abilities that will be discussed later in this document as well.

```
global.PowerStat[CardStat] = 13000
global.DefensePowerStat[CardStat] = 0
```

These two lines add the card's original power to the `PowerStat` and original shield value to the `DefensePowerStat` arrays. If a card has no shield or it's original power is 0, you can just omit this parameter, but there are no guidelines as to keep it or not, so you can either omit it or declare it as 0, both are functionally identical.

After adding new cards, do not forget to update the `global.AllCard` parameter in `NoUse.txt` file to increase the maximum amount of cards allowed in CFA.

## Advanced Card Syntax

This section covers specific syntax that is used by some cards in the database, starting from more common parameters to more obscure ones.

### :star: Triggers types

```
global.TriggerUnit[CardStat] = 1 // for Draw triggers
global.TriggerUnit[CardStat] = 2 // for Critical triggers
global.TriggerUnit[CardStat] = 3 // for Stand triggers
global.TriggerUnit[CardStat] = 4 // for Heal triggers
global.TriggerUnit[CardStat] = 5 // for Front triggers
global.TriggerUnit[CardStat] = 6 // for Over triggers
```

### :gear: Add card to G Deck

```
global.ExtraDeck[CardStat] = 1
```

### :shield: Quick shield (for first vanguards from V Premium format)

```
global.QuickShieldAdd[CardStat] = 1
```

### :gift: Imaginary Gifts on ride (for cards from V Premium format)

```
global.BlueTokenAdd[CardStat] = 1 // for Force
global.GreenTokenAdd[CardStat] = 1 // for Protect
global.OrangeTokenAdd[CardStat] = 1 // for Accel
global.BlueTokenBoth[CardStat] = 1 // gain both Force markers
```

`BlueTokenBoth` is always used with `BlueTokenAdd`.

### :gift::heavy_plus_sign: Imaginary Gift generator - add to hand by clicking the card while holding Shift

```
global.ForceAdd[CardStat] = 1 // for Force
global.ProtectAdd[CardStat] = 1 // for Protect
global.AccelAdd[CardStat] = 1 // for Accel
global.GiftAdd[CardStat] = 1 // one of each (for VC only)
global.GiftAddSelect[CardStat] = 1 // one of any kind (for VC only)
```

### :busts_in_silhouette: Activated Persona Ride

```
global.PersonaRideAct[CardStat] = 1
```

Don't forget to add the tip after the card text

### :busts_in_silhouette::twisted_rightwards_arrows: Cross Persona Ride

```
global.PersonaRideCardName[CardStat] = "Some Card Name"
```

The card will be considered having "Some Card Name" in addition to its original name when riding upon

```
global.PersonaRideCardName[CardStat] = "CommonName"
```

If two cards have the same `PersonaRideCardName`, Persona Ride will trigger (e.g. DressUp cards from Lyrical Monasterio).

```
global.ForbidCrossPersonaRideUpon[CardStat] = 1
```

Forbids cross persona riding upon this card specifically (e.g. Epee Du Justice, Thegrea)

### :dagger: Power buff on attack (prompt)

```
global.AttackFromVBuff[CardStat] = <power_value>
```

`<power_value>` is a value that can be added to the card when it attacks from VC.

```
global.AttackFromRBuff[CardStat] = <power_value>
```

Same, but works from RC in this case.

```
global.AttackFromVRBuff[CardStat] = <power_value>
```

Works both from VC and RC

Note that older cards have these properties hard-coded into CFA client.

### :arrow_double_down: Attack from the back row

```
global.CanAttackFromBackRow[CardStat] = 1
```

Display the "Attack" button next to this card if it is in the back row.

```
global.EnableAttackFromBackRow[CardStat] = 1
```

If this card is on VC or RC, displays the "Attack" button for all of the back row cards.

### :wastebasket: Auto remove from Drop (for Tokens, Tickets, Markers)

```
global.RemoveFromDrop[CardStat] = 1
```

### :arrow_up_small: Increase Unit Grade in Deck (for Search Effect)

```
global.UnitGradeIncrementInDeck[CardStat] = <number>
```

The number can be used to increase the grade of a card in deck. To decrease the grade, assign negative number instead of positive. Can be used for Orders as well.

### :purple_square::heavy_plus_sign: Increase Front Trigger Power

```
global.TriggerPowerUpEffect[CardStat, 0] = <power> // for VC or RC
global.TriggerPowerUpEffect[CardStat, 1] = <power> // for VC only
```

`<power>` is a number that will be added if the card is present

### :crossed_swords: Arms

```
global.Arms[CardStat] = 1
```

This parameter signifies that the card can be used as arms. You can also assign a fixed position to Arms by using one of the two next parameters:

```
global.LeftArms[CardStat] = 1
```

Only for Left Deity Arms (will always be attached to the RIGHT side of VC)

```
global.RightArms[CardStat] = 1
```

Only for Right Deity Arms (will always be attached to the LEFT side of VC)

```
global.ArmsAsUnit[CardStat] = 1
```

Arm as a Unit (it will give its power to the vanguard upon attacking) - e.g. "Armed Arms"

### :trophy: Regalis Piece

```
global.RegalisPiece[CardStat] = 1
```

This parameter lets the game display an icon after this card is placed in Order Zone or GC to signify that the Regalis Piece was already played this game.

### :milky_way: Astral Plane

```
global.Uranus[CardStat] = 1
```

Lets the player select between opening Astral Plane or generating Force Gift by Shift + clicking the card (don't forget to add the tip).

### :pirate_flag: Treasure Markers

```
global.MimicNotEvil[CardStat] = 1
```

Lets the player place Treasure markers by Shift + clicking the card (don't forget to add the tip).

### :exclamation: Card legality in Premium/V Premium

```
global.OldCardStat[CardStat] = 1
```

By default, all newly added cards without DCards are legal in V. But this parameter makes the card illegal in V Premium (i.e. only legal in Premium).

```
global.NewTriggerStat[CardStat] = 1
```

For Premium-only cards, this parameter makes the card's trigger power bonus equal to 10000 instead of 5000. V Premium only Cards give 10000 by default and this parameter should not be used.

Note that older cards have these properties hard-coded into CFA client.

### :skull: Ultimate Stride G Zone auto removal

```
global.DeleteAllExtraInEndPhase[CardStat] = 1
```

### :x: Card restriction label (in deck editor)

```
global.CardLimidet[CardStat] = 1
```

Display the orange "Restricted" label.

```
global.CardBanned[CardStat] = 1
```

Display the red "Banned" label.

### :eye: Cards hidden in the editor

```
global.CardInClan[CardStat] = 29
```

**29** is a technical clan category that is never shown in the editor.

```
global.DCards[CardStat] = 8
```

**8** is technical nation category that is only shown if it has another clan or nation (e.g. Touken Ranbu).

```
global.DontShowInDeckEditor[CardStat] = 1
```

This parameter explicitly hides the card in the editor regardless of its clan/nation.

### :performing_arts: Double-faced cards

```
global.AnotherSide[CardStat] = <card_id>
```

That other side card referenced by `<card_id>` should be hidden in the deck editor (see previous section).

### :page_facing_up::arrow_up_down: Extended text box

```
global.ExtendedTextBox = 1
```

Should only be used as last resort in situations when the text can absolutely not fit inside the normal text box even by removing extra line breaks.

### :recycle: Re-assigned cards (for accidentally added duplicate cards)

```
global.CardReassignedId[CardStat] = <card_id>
```

The card that has this parameter added will no longer be displayed in the deck editor.

If a player loads a deck containing this card, it will be replaced with the card that has `<card_id>` specified by this parameter.

This honours the constraints made on individual card quantity, so if total sum of old and newly replaced cards exceeds the maximum allowed amount, only that maximum amount of cards will be added to the deck and the player will be shown a message about insufficent deck size.

### :green_circle: Counters

```
global.UseCounters[CardStat] = 1
```

The card that has this parameter will be able to store counters. To add counters, player has to Shift + Left Click the card. To remove counters, player has to Shift + Right Click the card. Counters only work for cards that are in VC, RC or Order/Crest zone.

### :information_source: Card-specific effects

```
global.Reveal[CardStat] = 1
```

Effect specific to **Starhulk, Gicurs** and **Starhulk, Letaluk**.

```
global.SceneEffect[CardStat] = 1
```

Effect specific to **Masked Magician, Harri (V)**.

```
global.CocoAdd[CardStat] = 1
```

Effect specific to **Scarlet Witch, Coco**

Functionally it is identical to `global.ProtectAdd[CardStat] = 1` but is kept for backwards compatibility purposes.

## Token Generator Syntax

This section covers what is called "token generators". Those are the cards that have the ability to create other card - usually tokens, but sometimes is used for markers as well (such as Dragontree marker).

Note that it is not used for Imaginary Gift generation, those are covered separately in one of the sections before.

### :black_joker::heavy_plus_sign: Basic token generator

```
global.TokenSummoner[CardStat] = <card_id>
```

`<card_id>` is a reference to the card that will be generated by the card that has this parameter.

```
global.TokenSummonerPosition[CardStat] = '<V/R/VR/SHIFT/RodeUpon>'
```

`'V'` for when placed on VC, `'R'` for when placed on RC, `'VR'` for when placed on either VC or RC. `'SHIFT'` allows to generate tokens by clicking the card while holding Shift key.

Don't forget to add the tip to the card text if you the use `'SHIFT'` option, the tip is mandatory unless it really doesn't fit into the text box.

The last option for this parameter is `'RodeUpon'`, and it should be only specifically used in conjunction with `TokenSummonerRodeUponNumber` parameter:

```
global.TokenSummonerRodeUponNumber[CardStat] = <card_id>
```

The token summoner effect is triggered when the card is ridden by a card with ID equals to `<card_id>`.

```
global.TokenSummonerQuantity[CardStat] = <1/2>
```

This is an optional parameter which is used to assign fixed quantity of tokens to generate. Currently only supports values of 1 and 2.

If you assign this parameter, the first question when the player clicks the card will be: "Activate the skill of "[CARDNAME]"?" with possible answers - Yes/No.

Otherwise, the first question will be: "How many tokens do you want to call?" with possible answers - 0/1/2.

### :black_joker::heavy_plus_sign::thinking: Token generator with choice options

```
global.TokenSummoner[CardStat] = <card_id_1>
global.TokenSummoner2[CardStat] = <card_id_2>
global.TokenSummoner3[CardStat] = <card_id_3>
```

`<card_id_1>`, `<card_id_2>` and `<card_id_3>` are IDs of the cards that can be generated by this card, assigned to different buttons for player to choose from.

Having `TokenSummoner2` parameter is mandatory for multiple choice, while `TokenSummoner3` is optional. Maximum of three options are supported.

```
global.TokenSummonerPosition[CardStat] = '<V/R/VR/SHIFT/RodeUpon>'
```

Works exactly the same as for token generator without choice options.

```
global.TokenSummoner2Text[CardStat] = "Which token do you want to call?"
global.TokenSummoner2Button1[CardStat] = "5000"
global.TokenSummoner2Button2[CardStat] = "10000"
global.TokenSummoner2Button3[CardStat] = "15000"
```

Text values for the prompt that allows choice selection. `TokenSummoner2Text` should contain the text of the question, `TokenSummoner2Button1` through 3 contain text for the three options (should be relatively short to fit in, please try it out in the Test room before submitting).

```
global.TokenSummonerQuantity[CardStat] = <1/2>
```

Works exactly the same as for token generator without choice options.

## Card Search Effect Syntax

This section covers the syntax of a special effect that can be used to search the top cards of the library at will.

The current convention is to only use this effect **when necessary**, for example when the effect instructs to "shuffle the rest and put them on the bottom of your deck". Effects like that **cannot be executed manually** so we add them per se.

```
global.SearchEffect[CardStat] = 1
```

Should be assigned to 1 to make the effect work.

```
global.SearchEffectPosition[CardStat] = '<V/R/VR/SHIFT>'
```

`'SHIFT'` / `'VR'` / `'V'` / `'R'` - position of activation, works similarly to `TokenSummoner`.

```
global.SearchEffectLookAtQuantity[CardStat] = <number>
```

`<number>` is a number of cards to look at.

```
global.SearchEffectMode[CardStat] = <search_mode>
```

`<search_mode>` can be one of the following (specifically as a string):

- `'Card'` - find by specified card ID (arguments 1, 2 and 3).
- `'Clan'` - find by Clan ID (argument 1) and grade ? argument 2.
- `'NoClan'` - find any card with grade ? argument 2 (argument 1 should be omitted).
- `'CheckGrade'` - find a card of specified grade only.
- `'CheckGradeOrLess'` - find any card with grade ? argument 2 (argument 1 should be omitted).
- `'CheckNameAndGradeOrLess'` - find a card that has argument 1 as a substring in its name with grade ? argument 2. Set argument 1 to arbitrary huge number (e.g. 1000) to use just as a name search.
- `'CheckCrit'` - checks if card has a crit, arguments are ignored.
- `'CheckRace'` - checks if a unit has said race (looks at the combination of forward slash "/" + name in card text). Argument 2 can be ignored or used as additional race.
- `'CheckRaceOrName'` - checks if a unit has said race (looks at the combination of forward slash "/" + name in card text) or has argument 2 as a substring in its name.

```
global.SearchEffectArgument1[CardStat] = <argument_1>
global.SearchEffectArgument2[CardStat] = <argument_2>
global.SearchEffectArgument3[CardStat] = <argument_3>
```

Search function arguments, use depends on `SearchEffectMode` parameter and can be either a string or a number. Third argument is omitted unless `'Card'` search mode is used.

```
global.ActivateSearchFoundAction[CardStat] = <found_action>
```

Where to put found cards. `<found_action>` can be one of the following (specifically as a string):

- `'AddToHand'` - put found cards into hand.
- `'V'` - ride found cards.
- `'SendToTopCard'` - send found cards to top of the deck.
  - `UpTo` can be added to the end of any of those arguments to make the choice cancellable at any point, e.g. `'AddToHandUpTo'`, `'VUpTo'` and `'SendToTopCardUpTo'`.

```
global.ActivateSearchRestAction[CardStat] = <rest_action>
```

What to do with the rest of the cards. `<rest_action>` can be one of the following:

- `'Shu'` - shuffle into the deck.
- `'SelectBot'` - select in which order to put cards to the bottom of the deck.
- `'ShuffleBotDeck'` - shuffle the rest and put them on the bottom of the deck (primary mode at this point).

```
global.SearchEffectFindQuantity[CardStat] = <number>
```

Maximum number of cards allowed to be taken.

## Additional Clans/Nations

### Additional Clans/Nations Syntax

CFA supports adding more Clans and Nations dynamically! It can be used both by CFA database maintainers and for custom card creation (more on that in the next section)! Syntax is as follows:

```
global.CustomFactionClanId[N] = <CardInClan number>
global.CustomFactionNationId[N] = <DCards number>
global.CustomFactionName[N] = 'Faction Name As String'
global.CustomFactionFile[N] = 'Faction Text File.txt'

global.MaxCustomFaction = N(max) + 1
```

N(max) here is just the biggest N value used as index, and you have to add 1 to it. So if the biggest custom faction number is 15, the `global.MaxCustomFaction` should be set to 16.

New factions built into CFA are added to `NoUse.txt` file. You can find more information on existing Clans and Nations in the file as well.

### Custom Clans/Nations

If you wish to add custom clans and nations on top of existing ones into CFA, we suggest using a new mechanism built into the engine called **"custom overrides"**.

Basically, custom overrides are the commands you write into a new special file called `Custom Overrides.txt`. Create this file inside the `Text` folder of your CFA. It will never get overwritten by the auto update system of CFA.

Inside `Custom Overrides.txt` you can override existing parameters with custom ones. For example, you can increase the `global.AllCard` value.

We suggest adding new custom cards with numbers that start with at least 20,000 to avoid conflict with possible future CFA cards. Maximum allowed number of cards is 32000.

Here is an example of stuff you can add into your custom overrides file:

```
global.CustomFactionClanId[100] = 100
global.CustomFactionNationId[100] = -1
global.CustomFactionName[100] = 'My Custom Clan'
global.CustomFactionFile[100] = 'Custom Clan.txt'

global.CustomFactionClanId[101] = 0
global.CustomFactionNationId[101] = 100
global.CustomFactionName[101] = 'My Custom Nation'
global.CustomFactionFile[101] = 'Custom Nation.txt'

global.MaxCustomFaction = 102
global.AllCard = 20123
```

These commands will add a custom clan and a custom nation. Pay attention to how custom clans without a nation are added as having -1 as their nation; and new custom nations are added with a clan id of 0 — you can also ignore the `global.CustomFactionClanId` parameter altogether, it has the same effect as assigning it to 0.

Your custom text files you declared with `global.CustomFactionFile` will not be overwritten by CFA unless it just so happens that the new faction text file has the same exact name (which is unlikely).
In addition, you can create your own folders inside `Text` and place your custom files in them for better organization. You can have as many subfolders as you want inside of the `Text` folder or any of your own subfolders.
In cases where you have subfolders within subfolders, they will need to be declared within the `CustomFactionFile` string. For example:

`global.CustomFactionFile[101] = 'Custom1/Custom2/Custom3/Custom Nation.txt'`, where Custom1, Custom2 and Custom3 are folder names.

`global.MaxCustomFaction` is set to 102 because maximum N is now 101 and 101 + 1 = 102. We suggest you start with at least 100 as the first custom faction to avoid future conflict. Max faction number is 32000. Realistically you should not add more than 30 new factions as the list might get too long for the user to see more than 30. Unused numbers between the last built-in faction and the custom ones will be skipped.

`global.AllCard = 20123` — the max card value is also increased. This means that the CFA engine will now look at the first 20,123 entries in the database (including card with ID 20,123). Entries that do not have cards in the database will be skipped, just like with custom factions.
