# QuickStackPlus

## About
* **Need EAC disabled. Single-player only.**
* modded version of QuickStack
    * https://www.nexusmods.com/7daystodie/mods/1357
* Can specify items to be QuickStacked with Container label.
* Can specify items to be QuickRestocked with config file.
* Default QuickStack range is 7(15x15x15 cube)
    * Edit QuickStackPlus.txt to configure.
    * [Alt+QuickStack button] to reload config.

## Lock states

| Marker          | Name                 | memo  |
| :----           | :---                 | :---- |
| Red Brackets    | LockedSlot           | from vanilla. |
| Green Brackets  | UnlockedToolbeltSlot | MoveAll/etc can move items from Toolbelt.|
| Orange Icon     | ItemClassLock        | By Item class. |


## Keyboard/Mouse assign
### Not Configurable
* [ToggleFlashLight] to MoveFillAndSmart while opening container.
* [Ctrl+ToggleFlashlight] to execute QuickStack and Restock from nearby containers while opening backpack or container.
    * If you opening container, it will be excluded from QuickStack target.
* [Ctrl+Space] while opening container to MoveAll items from backpack.
* [Alt+LMB] backpack/toolbelt slot to toggle lock.
* [Ctrl+Alt+LMB] to toggle all slots until.
* [Alt+RMB] backpack slot item to shift lock state.
    * Unlocked -> ItemClassLock -> PositionLock -> Unlocked
    * setting is saved/loaded(saved in Player folder).
* [Ctrl+Alt+RMB] to shift Restock amount
    * 0 -> 0.5stack -> 1.0stack -> 0
    * Can be shifted with item action(CraftingInfo / ItemInfo panel)
* [Ctrl+C] while dragging item to copy item info to clipboard.

### Configurable
* [Ctrl+C] to Sort Backpack(Not work in LootWindow etc).
* [Ctrl+X] to Restock.

## Config and PatternFile path priority

1. PlayerData folder(``%APPDATA%\7DaysToDie\Saves\[Map]\[Save]\Player\[PlayerID]_TywConfig\[ModName]``)
1. Mod folder(``[Folder of 7dtd exe]\Mods\[ModName]\``)
* If you have config file in both folders, the one in PlayerData will be used.
* If Config does not exist but Config.default does exist in Mod folder, it is copied to PlayerData folder.

* PatternFile is below PatternFiles folder.

## QuickStack Syntax

* Used in Storage Crate Label and PatternFile.
* Multiple lines can be processed from top to bottom.
* Can specify multiple lines with labels.

|Header|At   |Example|Function|
|:----:|:---:|:--    |:--     |
|[none]|Label| Unsorted | Simple label. Does nothing.|
|[none]|File | resourceSewingKit| UnlocalizedName(ItemClassName) exact matching without header.|
| @    |Both | @Sewing Kit|Localized name exact matching.|
| /    |Both | /ammo  |Regex ItemClassName matching from head(/^ammo/). Partial match.|
| =    |Both | =Ammo |Include PatternFile(Ammo.txt)|
| !    |Both | !ammoGasCan<br> !/modArmor<br>!@Localized Name|Negate match. Matched item is not QuickStacked.|
| #    |Label| #Reserved | Ignore this chest from QuickStack/restocking.|


|Middle|At   |Example|Function|
|:----:|:---:|:--    |:--     |
| #    |Both | /modDye # dye| All text after '#' consider as comment.|
| $    |Both | /food.+$IsIngredient == true | ConditionalExpression| 
| %    |Label| /modGunMelee %2<br>/modGun %1<br>/modMelee<br>/mod %-1 | Container priority. Orderby descending. default=0.|

* All matches are case insensitive.

### ConditionalExpression
* Syntax : `<ITEM_PATTERN>$<ATTR>[:<PARAM>] [<OP> <VALUE>]`
* ITEM_PATTERN canbe either ItemClassName or LocalizedName
* When using regex, need to pattern to be entire match like "/food.+$"
* OP and VALUE canbe omitted with boolean attribute.

#### ATTR : Supported attributes

|Attribute       | Type  | memo |
|:--             | :--   | :--  |
|IsIngredient    | bool  | Has recipe which use this.|
|HasRecipe       | bool  | Can craft with recipe. |
|Quality         | int   | 
|PercentUsesLeft | float | 0.0-1.0 |
|Prop:propName   |       | Property tag value.|

#### OP : Operands
* Standards : `==, !=, <=, >=, < , >`

|string Operand | function      |
| :--:          | :--           |
| ^=            | StartsWith    |
| $=            | EndsWith      |
| *=            | Contains      |
| ~=            | Contains(CSV) |

#### VALUE

| type   | value example |
| :--:   | :--           |
| bool   | true, false   |
| int    | 0,1,2,-1,-2   |
| float  | 0.1, 3f       |
| string | "foo",  bar   |

### PatternFile
* Filepath : `PatternFiles\[basename].txt`
* OpenContainer + Drag Item + Ctrl+V : Add item to PattenFile which container loads first


### Examples
```
# All mods(Attachments)
/mod

# All ammo but gas can
!ammoGasCan
/ammo

# Seeds
/treePlanted
/planted.*1$
/DFseed

# All you can do is eat it
/food.+$IsIngredient == false

# Melee items which have longshaft
/melee.+$Prop:Tags ~= longshaft
```

---

## QuickRestock
* Can specify amount of restock.
* Items will be collected from nearby containers.
* Select nRestock from action menu or Alt+RMB item to toggle amount of restock.
    * Default cycle is 0, half, full, ClassLock
* ClassLocked items are not restocked.
* To specify exact number, edit Restock.txt
### RestockFile
* Filename : QuickRestock.txt
* Syntax : `[NUM](,num,...) <ItemClassName>`
* Specify multiple nums to cycle amount what you want.

|NUM    |Amount|memo|
|:--    |:--   |:--|
|[empty]|StackMax| Empty means 1 full stack(StackMax)|
| 1 &lt; NUM &lt;= StackMax| NUM | Exact integer number till StackMax|
|0.0s &lt; NUM &lt;= 1.0s  | NUM*StackMax | Less than equal 1.0|
|2s, 3s, ...| n*StackMax |Interger greater than 2 |
|-1 ||ClassLocked. Not restocked nor QuickStacked|

* You can copy ItemClassNames by
  * Shift+Alt+Sort : Copy all items in container
  * Drag Item+Ctrl+C : Dragged item

### Examples
* casinoCoin # @Duke's Casino Token, 1stack without number
* 15 resourceRepairKit # @Repair Kit, Exact number
* 0.5s resourceRockSmall # @Small Stone, 0.5 stack, below 1s, can specify 0.x
* 2s ammo762mmBulletBall # @7.62mm Ammo, 2 stacks




## Changelog
### 2025.05/30 v1.2.25
* Change config path.
* Change PatternFile path.

### 2025.04/xx v1.2.24

### 2025.04/26 v1.2.23
* Remove position lock. 
* Change Alt+RMB to shift Restock and ClassLock.

### 2025.04/22 v1.2.22
* Fix bug of Item merging to toolbelt preferred slot.

### 2025.04/18 v1.2.20
* Change order of slot to be added from container.

### 2025.04/16 v1.2.18
* Update ModTemplate to v1.2.8.

### 2025.04/xx v1.2.17
* Implement ConditionanExpression attr=Prop

### 2025.04/09 v1.2.16
* Implement Container priority.

### 2025.04/09 v1.2.15
* Implement DroneSlotLock
* Implement LastItemGhost

### 2025.03/20 v1.2.12
* Implement ConditionalExpression

### 2025.03/14 v1.2.10
* Add Restock overflow notification(Yellow Bucket icon)

### 2025.03/13 v1.2.8
* Overflow restock will be stashed if neither ItemClassLock nor PositionLock.

### 2025.03/11 v1.2.6
* Change lock icon(pin from ocb's mod).

### 2025.03/09 v1.2.5
* Added option QuickStackAndRestock=true. Used when Ctrl+ToggleFlashlight.

### 2025.03/09 v1.2.4
* Added a bar of Restock sufficiency status.
* Added Ctrl+Alt+RMB to shift Restock amount(0 -> 0.5 -> 1.0 -> 0)

### 2025.03/06 v1.2.3
* Swap Alt+L/R Click

### 2025.02/17 v1.2.2
* Alt + Sort button to dump PatternFile text from items in container. Shift+Alt to copy to clipboard.

### 2025.02/14 v1.2.1
* for v1.2

### 2024.07/26 v1.1.3
* Add config option to hide ToggleLockMode Button.

### 2024.07/21 v1.1.2
* Fix Alt+RMB Lock toggle.

### 2024.07/21 v1.1.1
* for b326

### 2024.07/05 v1.0.16
* Implement Toolbelt Lock toggle. If you unlock toolbelt slot, MoveAll/etc move items from there.
* for b316(Don't work with b313)

### 2024.07/05 v1.0.15
* Add Hotkey for Sort and Restock.
* Lock state is displayed in LootWindow/Toolbelt now.

### 2024.07/02 v1.0.14
* Now, lock state is saved.
* Add new Lock state : PositionLock.

### 2024.06/25 v1.0.13
* For v1.0 experimental.
* Add LockedSlots icon and LockedItemType icon(vanilla backpack only).

### 2023.12/12 v0.12.2
* Adjust QuickStack button position for DarknessFalls v5.0.1

### 2023.12/09 v0.12.1
* for a21.2(b30)
* Some feature from v0.9.1 to v0.11.3 may have been lost due to HDD Crash.

### 2023.06/24 ver 0.11.3
* Localization.txt : Update German translation(by Alfimail)

### 2023.06/22 ver 0.11.2
* Localization.txt : Add German translation(by Alfimail)

### 2023.06/14 ver 0.11.1
* Disable quickstack while not opening backpack to avoid items vanishing.

### 2023.06/14 ver 0.11
* for a21(b313)
* Implement my own LockedSlot combobox.

### 2023.06/02 ver 0.10.1
* Reset LockedSlot to 0 when starting NewGame.

### 2023.06/02 ver 0.10
* Patch for SorceryMod v1.72

### 2022.11/06 ver 0.9.3
* Change SortKey of newly locked item.
* Fix archive structure.

### 2022.09/27 ver 0.9.2
* Remove Lockslots save/load to use with Quartz Mod.

### 2022.09/27 ver 0.9.1
* remove needless DLLs from archive

### 2022.05/03 ver 0.9
* Make range configurable.

### 2022.04/29 ver 0.8
* Change QuickStack hotkey to Ctrl+ToggleFlashLight from Ctrl+T
* Add hotkey for MoveAll : Ctrl+Space
* Add hotkey for MoveFillAndSmart : ToggleFlashLight

### 2022.04/21 ver 0.7
* Change hotkey for QuickStack to Ctrl+T.
* Added Lock by type feature.

### 2022.04/19 ver 0.6
* Added hotkey for QuickStack : Ctrl+R

### 2022.02/20 ver 0.5
* Alt+Right click to lock to there.

### 2022.02/09 ver 0.4
* [DarknessFalls v4] Hide MoveAll and MovFillandSmart buttons while opening backpack.

### 2022.02/08 ver 0.3
* [DarknessFalls v4] Move QuickStack button right to LockedSlot.

### 2022.01/15 ver 0.2
* Hide QuickStack button while opening chests.

