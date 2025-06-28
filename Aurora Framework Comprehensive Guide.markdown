# Aurora Framework Comprehensive Guide

This document consolidates all useful content from the Aurora Framework documentation, including API usage, examples, best practices, common patterns, and other relevant details for easy reference.

## Project Setup

### File Structure
```
📂 scripts/
└── 📂 AuroraRoutines/
    ├── 📄 loadorder.json
    ├── 📄 Main.lua
    └── 📂 Routines/
        └── 📂 Warrior/
            └── 📂 Specialisation/
                ├── 📄 Spellbook.lua
                ├── 📄 Interface.lua
                ├── 📄 Rotation.lua
```

### Initial Setup
```bash
mkdir -p scripts/AuroraRoutines
```

### loadorder.json
```json
[
    "Main",
    "Routines/Warrior/Specialisation/Spellbook",
    "Routines/Warrior/Specialisation/Interface",
    "Routines/Warrior/Specialisation/Rotation"
]
```

### Best Practices
- **File Organization**: Keep related functionality in separate files, use descriptive names, maintain logical load order, place shared utilities in `Main.lua`.
- **Common Mistakes**: Update `loadorder.json` for new files, ensure exact file name matches, handle dependencies properly.

### Next Steps
- Set up development environment.
- Create basic file structure.
- Implement core functionality.
- Add rotation logic.
- Test and refine.

## Namespace Setup

### Why Use a Namespace?
- Avoid conflicts with other routines.
- Share data between files.
- Keep code organized and modular.
- Prevent variable collisions.

### Passing Namespace
```lua
YourNamespace = {}
```

- Automatically passed to routines when loaded.

## Spell Registration

### API Usage
```lua
local NewSpell = Aurora.SpellHandler.NewSpell
Aurora.SpellHandler.PopulateSpellbook({
    spells = {
        [SpellName] = NewSpell(spellID, { [options] }),
    },
    auras = { [Aura Definitions] },
    talents = { [Talent Definitions] },
}, "CLASS", specID, "Namespace")
```

### Example
```lua
local NewSpell = Aurora.SpellHandler.NewSpell
Aurora.SpellHandler.PopulateSpellbook({
    spells = {
        AutoAttack = NewSpell(6603),
        Charge = NewSpell(100),
        ShieldSlam = NewSpell(23922),
        Ravager = NewSpell(228920, { radius = 8 }),
    },
    auras = {},
    talents = {},
}, "WARRIOR", 3, "YourNamespace")
```

### Best Practices
- Keep spellbook in a dedicated file (e.g., `Spellbook.lua`).
- Use clear, descriptive spell names.

## Accessing Spells

### API Usage
```lua
local spells = Aurora.SpellHandler.Spellbooks.[class][specID].Namespace.spells
local auras = Aurora.SpellHandler.Spellbooks.[class][specID].Namespace.auras
local talents = Aurora.SpellHandler.Spellbooks.[class][specID].Namespace.talents
```

### Example
```lua
-- Warrior spells
local spells = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.spells
local auras = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.auras
local talents = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.talents

-- Priest spells
local spells = Aurora.SpellHandler.Spellbooks.priest["1"].YourNamespace.spells
local auras = Aurora.SpellHandler.Spellbooks.priest["1"].YourNamespace.auras
local talents = Aurora.SpellHandler.Spellbooks.priest["1"].YourNamespace.talents
```

## Registering Rotation

### API Usage
```lua
Aurora:RegisterRoutine(function()
    if player.dead or player.aura("Food") or player.aura("Drink") then return end
    if player.combat then
        Dps()
    else
        Ooc()
    end
end, "CLASS", specID, "Namespace")
```

### Example
```lua
local target = Aurora.UnitManager:Get("target")
local player = Aurora.UnitManager:Get("player")
local spells = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.spells
local auras = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.auras
local talents = Aurora.SpellHandler.Spellbooks.warrior["3"].YourNamespace.talents

local function Dps()
    if spells.ShieldBlock:execute() then return true end
    if spells.Ravager:execute() then return true end
    if spells.AutoAttack:execute() then return true end
    if spells.ShieldCharge:execute() then return true end
    if spells.ShieldSlam:execute() then return true end
    if spells.ThunderClap:execute() then return true end
    if spells.Execute:execute() then return true end
    if spells.Revenge:execute() then return true end
    if spells.HeroicThrow:execute() then return true end
    return false
end

local function Ooc()
    -- Out of combat logic
end

Aurora:RegisterRoutine(function()
    if player.dead or player.aura("Food") or player.aura("Drink") then return end
    if player.combat then
        Dps()
    else
        Ooc()
    end
end, "WARRIOR", 3, "YourNamespace")
```

### Best Practices
- Keep rotation logic clean and organized.
- Use separate functions for DPS, healing, utility.
- Check critical conditions first (dead, eating/drinking).
- Return `true` when an action is taken.

### Common Patterns
```lua
-- Basic Combat Check
if player.combat then
    Dps()
else
    Ooc()
end

-- Skip Conditions
if player.dead or player.aura("Food") or player.aura("Drink") then return end

-- Action Priority
local function Dps()
    if spells.ShieldBlock:execute() then return true end
    if spells.Ravager:execute() then return true end
    if spells.ShieldSlam:execute() then return true end
    if spells.ThunderClap:execute() then return true end
    return false
end
```

## Spell Callbacks

### API Usage
```lua
spells.SpellName:callback(function(spell, logic)
    if condition then
        spell:cast(target)
    end
end)
```

### Examples
```lua
-- Healing spell
spells.ImpendingVictory:callback(function(spell, лучшие практикиlogic)
    local healAmount = player.healthmax * 0.2
    local healthMissing = player.healthmax - player.health
    if healthMissing >= healAmount * 0.8 then
        spell:cast(player)
    end
end)

-- Basic target spell
spells.ShieldSlam:callback(function(spell, logic)
    spell:cast(target)
end)

-- AOE spell
spells.ThunderClap:callback(function(spell, logic)
    if player.enemiesaround(8) > 0 then
        spell:cast(player)
    end
end)

-- Distance and AOE check
spells.ShieldCharge:callback(function(spell, logic)
    if target.enemiesaround(8) > 0 and target.distanceto(player) <= 10 then
        spell:cast(target)
    end
end)

-- Arc check for cleave
spells.Revenge:callback(function(spell, logic)
    Aurora.activeenemies:each(function(enemy)
        if enemy.inarcof(player, 8, 180) then
            spell:cast(player)
        end
    end)
end)

-- Execute phase
spells.Execute:callback(function(spell, logic)
    Aurora.activeenemies:each(function(enemy)
        if enemy.hp <= 20 then
            spell:cast(enemy)
        end
    end)
end)
```

### Common Patterns
```lua
-- Health-based decisions
spell:callback(function(spell, logic)
    if player.hp < 40 then
        spell:cast(player)
    end
end)

-- Resource-based decisions
spell:callback(function(spell, logic)
    if player.rage >= 40 then
        spell:cast(target)
    end
end)

-- Enemy count checks
spell:callback(function(spell, logic)
    if player.enemiesaround(8) >= 3 then
        spell:cast(player)
    end
end)
```

## Unit Properties

### Key Properties
- **Identification**: `guid`, `unit`, `id`, `name`, `pointer`, `creator`, `effects`
- **Health & Status**: `health`, `healthmax`, `healthpercent`, `hp`, `effectivehp`, `healabsorb`, `stagger`, `staggerpct`, `staggerhealth`, `dead`, `alive`, `exists`
- **Combat & Threat**: `combat`, `threat`, `aggro`, `aggressive`, `enemy`, `friend`, `reaction`
- **Movement & Position**: `position`, `speed`, `moving`, `movingatall`, `invehicle`, `standing`, `height`, `combatreach`, `rotation`, `reach`, `mounted`, `timemoving`, `timestanding`, `timecombat`, `timeaggro`
- **Unit Type & Classification**: `class`, `class2`, `classid`, `spec`, `unitspecid`, `race`, `race2`, `level`, `type`, `typestring`
- **Unit Type Checks**: `isboss`, `ishumanoid`, `isbeast`, `isundead`, `isdemon`, `isdragonkin`, `iselemenetal`, `isgiant`, `ismechanical`, `istotem`, `isaberration`, `isuncategorized`
- **Player-Facing**: `facing`, `playerfacing`, `playerfacing30`, `playerfacing45`, `playerfacing60`, `playerfacing90`, `playerfacing180`
- **Unit Counting**: `enemiesaround(radius)`, `friendsaround(radius)`, `friendsaroundhp(distance, hp)`
- **Group Status**: `party`, `group`, `raid`, `ininstance`, `inraid`
- **Role Properties**: `role`, `ishealer`, `isdps`, `istank`
- **Visibility & Interaction**: `visible`, `los`, `lootable`, `skinnable`, `tapdenied`, `charmed`
- **Casting Information**: `casting`, `casting1`, `casting2`, `castingstart`, `castingend`, `castingtrade`, `castingid`, `castingnotinterruptible`, `castinginterruptible`, `castingspellid`, `castingremains`, `castingpct`, `channeling`, `channeling1`, `channeling2`, `channelingstart`, `channelingend`, `channelingid`, `channelingnotinterruptible`, `channelinginterruptible`, `channelingspellid`, `channelingremains`, `channelingpct`
- **Combat Statistics**: `ttd`
- **Unit Characteristics**: `istauntable`, `isgrippable`, `isfearable`, `isrootable`, `issilenceable`, `isslowable`, `isstunable`, `isincapacitable`

### Example
```lua
local target = Aurora.UnitManager:Get("target")
local player = Aurora.UnitManager:Get("player")

-- Basic target checks
if target.exists and target.enemy and target.alive then
    if target.hp < 20 and target.distanceto(player) < 30 then
        -- Execute logic
    end
end

-- Type checks and positioning
if target.ishumanoid and not target.ismechanical then
    if target.playerfacing then
        -- Use frontal cone melee ability
    end
end

-- Aura checks
if target.exists then
    local hasDeBuff = target.aura(12345)
    if hasDeBuff then
        local remaining = target.auraremains(12345)
        if remaining < 3 then
            -- Refresh debuff
        end
    end
    local stacks = target.auracount(67890)
    if stacks >= 5 then
        -- Use ability that consumes stacks
    end
    local auraList = {12345, 67890, 11223}
    if target.aurafrom(auraList) then
        -- Unit has at least one of the auras
    end
end

-- Combat and casting checks
if target.exists and target.casting then
    if target.castinginterruptible and target.castingpct > 50 then
        if player.distanceto(target) < 30 then
            -- Interrupt the cast
        end
    end
end
```

### Notes
- Properties are accessed via dot notation (`unit.health`) but are internally method calls.
- Can use method notation (`unit:health()`) if preferred.
- Always check `exists` before accessing other properties.

## Unit Methods

### API Usage
```lua
unit:behind(target)
unit:infront(target)
unit:melee(target)
unit:distanceto(target)
unit:distancetoliteral(target)
unit:futureposition(time)
unit:inarcof(target, size, arc, rotation)
unit:enemiesaround(radius)
unit:haslos(target)
unit:settarget(target)
unit:isunit(target)
unit:interact()
```

### Example
```lua
local target = Aurora.UnitManager:Get("target")
local player = Aurora.UnitManager:Get("player")

-- Check if player is behind target
if player.behind(target) then
    -- Use backstab ability
end

-- Check distance and line of sight
if target.distanceto(player) < 30 and target.haslos(player) then
    -- Cast ranged ability
end

-- Count nearby enemies
local enemyCount = player.enemiesaround(8)
if enemyCount >= 3 then
    -- Use AoE ability
end

-- Set target and interact
local enemy = Aurora.UnitManager:Get("target")
if enemy.exists and player.settarget(enemy) then
    player.interact()
end
```

### Best Practices
- Always check unit existence before calling methods.
- Use appropriate range checks before interactions.
- Consider line of sight for ranged abilities.
- Cache frequently accessed values for performance.

### Common Pitfalls
- Don’t assume target existence.
- Check range before melee abilities.
- Verify line of sight for ranged abilities.
- Consider unit type restrictions for interactions.

## Aura Properties

### Key Properties
- **Basic**: `name`, `icon`, `count`, `charges`, `maxCharges`, `dispelType`, `duration`, `expirationTime`, `sourceUnitGuid`, `spellId`, `startTime`
- **State**: `isStealable`, `nameplateShowPersonal`, `nameplateShowAll`, `canApplyAura`, `isBossAura`, `isFromPlayerOrPlayerPet`, `isHarmful`, `isHelpful`, `isNameplateOnly`, `isRaid`, `type`
- **Technical**: `timeMod`, `auraInstanceID`, `points`

### Example
```lua
local target = Aurora.UnitManager:Get("target")
local myDebuff = target.aura(12345)

if myDebuff then
    print("Aura name:", myDebuff.name)
    print("Charges:", myDebuff.charges)
    print("Duration:", myDebuff.duration)
    print("Is from player:", myDebuff.isFromPlayerOrPlayerPet)
    
    if myDebuff.isStealable then
        -- Can be stolen by Mage
    end
end
```

## Aura Collection Methods

### API Usage
```lua
unit.allauras
unit.alldispelauras
unit.aura(id, sourceObject)
unit.aurauptime(id, sourceObject)
unit.auraremains(id, sourceObject)
unit.auracount(id, even sourceObject)
unit.aurafrom(array, sourceObject)
```

### Example
```lua
local target = Aurora.UnitManager:Get("target")

-- Check specific aura
local myDebuff = target.aura(12345)
if myDebuff and myDebuff.remaining < 3 then
    -- Refresh debuff
end

-- Check for any aura from a list
local auraList = {12345, 67890, 11223}
if target.aurafrom(auraList) then
    -- Unit has at least one of the auras
end

-- Get all dispellable auras
local dispellableAuras = target.alldispelauras
for _, aura in ipairs(dispellableAuras) do
    print("Can dispel:", aura.name)
end
```

## Aura Tracking

### API Usage
```lua
Aurora.TrackAuras(auraIds, callback)
```

### Example
```lua
-- Track single aura
local unregisterTracker = Aurora.TrackAuras(462854, function(unit, aura, event)
    if event == "ADD_OR_UPDATE" then
        print("Aura detected:", aura.name, "on", unit.name)
    elseif event == "REMOVE" then
        print("Aura removed:", aura.name, "from", unit.name)
    end
end)

-- Track multiple auras
local aurasToTrack = {462854, 123456, "Power Word: Shield"}
local unregisterTracker = Aurora.TrackAuras(aurasToTrack, function(unit, aura, event)
    -- Handler code
end)

-- Stop tracking
unregisterTracker()
```

## Hidden Auras

### API Usage
```lua
Aurora.hiddenauras[spellId]
```

### Example
```lua
-- Check specific hidden aura
local tierSetBonus = Aurora.hiddenauras[123456]
if tierSetBonus then
    print("Tier set bonus is active with " .. tierSetBonus.stacks .. " stacks")
end

-- Iterate through all hidden auras
for spellId, auraInfo in pairs(Aurora.hiddenauras) do
    print(spellId .. ": " .. auraInfo.name)
end
```

### Common Use Case
```lua
local function HasTierSetBonus(tier, setPieces)
    local tierBonusAuras = {
        [2] = 123456, -- 2-piece bonus aura
        [4] = 123457  -- 4-piece bonus aura
    }
    return Aurora.hiddenauras[tierBonusAuras[setPieces]] ~= nil
end

if HasTierSetBonus("T30", 4) then
    -- Adjust rotation for 4-piece bonus
end
```

## Lists

### Available Lists
- `enemies`: Relevant enemy units.
- `friends`: Friendly units, including non-group.
- `group`: Group members (excluding player).
- `fgroup`: Group members (including player).
- `activeenemies`: Enemies in combat with threat.
- `dead`: All dead units.
- `units`: All units.
- `objects`: All game objects.
- `missiles`: All missiles.
- `areatriggers`: All area triggers.

### API Usage
```lua
list:each(callback)
list:filter(callback)
list:first(callback)
list:random()
list:sort(callback)
```

### Example
```lua
-- Loop through enemies
Aurora.enemies:each(function(enemy, index, uptime)
    print("Processing enemy #" .. index .. " visible for " .. uptime .. " seconds")
    if enemy.hp < 20 and enemy.distance < 30 then
        return true
    end
end)

-- Filter low health enemies
local lowHealth = Aurora.enemies:filter(function(unit)
    return unit.hp < 50
end)
lowHealth:each(function(unit)
    -- Heal or damage low health units
end)

-- Find first low health enemy
local target = Aurora.enemies:first(function(unit)
    return unit.hp < 20
end)

-- Get random enemy
local randomEnemy = Aurora.enemies:random()
print(randomEnemy.name)

-- Sort enemies by health
Aurora.enemies:sort(function(a, b)
    return a.hp < b.hp
end)
```

### Best Practices
- Use `each` instead of for loops for better control flow.
- Chain methods to reduce iterations.
- Use `filter` for reusable filtered lists.
- Break loops early when needed.

### Common Pitfalls
- Always check unit existence.
- Consider range and line of sight.

## Virtual Units

### Built-in Virtual Units
- `tank`: Resolves to main tank, group tank, or player if tank.
- `healer`: Resolves to group healer or player if healer.
- `target`: Unit’s current target.

### API Usage
```lua
Aurora.UnitManager.tank
Aurora.UnitManager.healer
Aurora.UnitManager:RegisterVirtualUnit(name, callback)
```

### Example
```lua
-- Use tank virtual unit
local tank = Aurora.UnitManager.tank
if tank.exists then
    if tank.hp < 50 then
        -- Heal the tank
    end
end

-- Register custom virtual unit
Aurora.UnitManager:RegisterVirtualUnit("lowestHealth", function(self)
    local lowest = nil
    local lowestHP = 100
    Aurora.fgroup:each(function(unit)
        if unit.alive and unit.hp < lowestHP then
            lowest = unit
            lowestHP = unit.hp
        end
        return false
    end)
    return lowest or self:Get("none")
end)
local needsHealing = Aurora.UnitManager.lowestHealth
if needsHealing.exists and needsHealing.hp < 50 then
    -- Heal them
end
```

### Best Practices
- Cache virtual units for repeated use.
- Avoid accessing in tight loops.
- Use for dynamic unit resolution.

## Spell Class

### Installation
```lua
local spell = Aurora.SpellHandler.NewSpell(spellID, payload)
```

### Payload Options
- `ignoreCasting`: `boolean` (false)
- `ignoreChanneling`: `boolean` (false)
- `ignoreCost`: `boolean` (false)
- `ignoreFacing`: `boolean` (false)
- `ignoreMoving`: `boolean` (false)
- `isSkillshot`: `boolean` (false)
- `maxOffset`: `number` (0)
- `minOffset`: `number` (0)
- `nextMelee`: `boolean` (false)
- `radius`: `number` (0)
- `queued`: `boolean` (false)
- `timeToHit`: `number` (0)
- `magicDispel`, `curseDispel`, `poisonDispel`, `diseaseDispel`: `boolean` (false)

### API Usage
```lua
spell:cast(unit)
spell:castable(unit)
spell:ready()
spell:getcd()
spell:charges()
spell:maxcharges()
spell:chargesleft()
spell:timetofull()
spell:timetonextcharge()
spell:wasLastCast(timeWindow)
spell:wasSecondLastCast()
spell:timeSinceLastCast()
spell:smartaoe(target, options)
spell:smartaoeposition(target, options)
spell:inrange(unit)
spell:rank()
spell:overlayed()
spell:getcasttime()
spell:isusable()
```

### Example
```lua
-- Fireball spell
local fireball = Aurora.SpellHandler.NewSpell(133, {})
if fireball:castable("target") then
    fireball:cast("target")
end

-- Blizzard AOE spell
local blizzard = Aurora.SpellHandler.NewSpell(190356, { isSkillshot = true, radius = 8 })
blizzard:smartaoe("target", {
    offsetMax = 30,
    filter = function(unit, distance, position)
        return unit.enemy and not unit.dead
    end
})
```

### Important Notes
- `cast()` and `smartaoe()` automatically handle checks like `ready()`, `castable()`, `inrange()`, etc.
- Avoid redundant manual checks:
  ```lua
  -- Incorrect
  if spell:ready() and spell:castable("target") and target.los then
      spell:cast("target")
  end
  -- Correct
  spell:cast("target")
  ```

## Draw System

### API Usage
```lua
Draw:Enable()
Draw:GetColor(colorName, opacity)
Draw:RegisterCallback(name, callback, targetType)
```

### Example
```lua
-- Enemy markers
Draw:RegisterCallback("unitMarkers", function(canvas, unit)
    if unit.enemy and unit.alive then
        local r, g, b, a = Draw:GetColor("Red", 70)
        canvas:SetColor(r, g, b, a)
        canvas:Circle(unit.position.x, unit.position.y, unit.position.z, 1)
    end
end, "units")

-- Class-colored names
Draw:RegisterCallback("classNames", function(canvas, unit)
    if unit.player then
        local r, g, b, a = Draw:GetColor(unit.class2, 255)
        canvas:SetColor(r, g, b, a)
        canvas:Text(unit.name, "GameFontHighlight", unit.position.x, unit.position.y, unit.position.z + 2)
    end
end, "units")
```

### Best Practices
- Minimize draw operations in callbacks.
- Cache color values.
- Remove unneeded callbacks.
- Consider distance and visibility.

### Common Pitfalls
- Avoid heavy calculations in callbacks.
- Be mindful of opacity stacking.
- Drawing happens every frame.

## Macro System

### API Usage
```lua
Macro:RegisterCommand(command, callback, description)
```

### Example
```lua
-- Greet command
Macro:RegisterCommand("greet", function(name)
    print("Hello, " .. (name or "stranger") .. "!")
end, "Greets a player")

-- Cast command
Macro:RegisterCommand("cast", function(spell)
    if spell then
        Aurora.Spell:Cast(spell)
    end
end, "Casts the specified spell")
```

### Best Practices
- Use clear command names.
- Provide helpful descriptions.
- Handle missing arguments gracefully.
- Keep commands simple.

## Event Handler

### API Usage
```lua
EventHandler:RegisterEvent(eventType, handlerFunction)
EventHandler:RegisterNormalEvent(eventType, handlerFunction)
EventHandler:AddFilter(eventType, filterFunction)
```

### Example
```lua
-- Track player spells
EventHandler:RegisterEvent("SPELL_CAST_SUCCESS", function(eventData)
    if eventData.source.guid == UnitGUID("player") then
        local spellId, spellName = unpack(eventData.params)
        print(string.format("Cast %s (ID: %d)", spellName, spellId))
    end
end)

-- Monitor interrupts
EventHandler:RegisterEvent("SPELL_INTERRUPT", function(eventData)
    local _, _, _, extraSpellId, extraSpellName = unpack(eventData.params)
    print(string.format("%s interrupted %s's %s", 
        eventData.source.name,
        eventData.dest.name,
        extraSpellName))
end)

-- Track group changes
EventHandler:RegisterNormalEvent("GROUP_ROSTER_UPDATE", function()
    print("Group composition changed")
end)
```

### Best Practices
- Use filters to reduce unnecessary processing.
- Keep handlers lightweight.
- Consider batching frequent events.
- Clean up unneeded handlers.

## Toast Notifications

### API Usage
```lua
Aurora.Toast:Show(title, message)
```

### Example
```lua
local playerName = UnitName("player")
Aurora.Toast:Show(
    string.format("Welcome, %s!", playerName),
    "Type /aurora for settings"
)
```

### Configuration
- Positions: Top Right, Top Left, Bottom Right (default), Bottom Left, Hidden.
- Set via Aurora settings under "General" tab.

## Status Frame

### API Usage
```lua
Aurora:AddGlobalToggle(options)
toggle:GetValue()
toggle:SetValue(value)
toggle:SetCustomText(text)
```

### Example
```lua
local myToggle = Aurora:AddGlobalToggle({
    label = "AoE:",
    var = "my_aoe_toggle",
    icon = "Interface/Icons/Spell_Holy_HolyNova",
    tooltip = "Toggle AoE mode",
    onClick = function(value)
        print("AoE mode:", value)
    end
})

if myToggle:GetValue() then
    -- AoE mode enabled
end
```

### Toggle Options
- `label`: Display text (max 11 chars).
- `var`: Unique identifier for saving state.
- `icon`: Texture path or spell ID.
- `tooltip`: Hover tooltip text.
- `onClick`: Callback function.

### Best Practices
- Keep labels short (max 11 chars).
- Use descriptive tooltips.
- States are automatically saved.

## Alert System

### API Usage
```lua
Aurora.alert(message, icon, options)
```

### Example
```lua
-- Simple alert
Aurora.alert("Your message here")

-- Spell icon alert
Aurora.alert("Spell Ready!", 12345)

-- Customized alert
Aurora.alert({
    string = "Critical Hit!",
    time = 3,
    fadeTime = 0.7,
    fadeInTime = 0.2
}, "Interface\\Icons\\ability_warrior_rampage", {
    soundID = 12345,
    iconSizeOverride = 40,
    circularMaskOverride = true
})
```

### Best Practices
- Keep messages short.
- Use icons for quick identification.
- Avoid spamming alerts.
- Use sounds sparingly.

## Utility Functions

### API Usage
```lua
Aurora.texture(idOrPath, size)
Aurora.groupttd()
Aurora.grouphp(distance)
Aurora.grouphpcount(distance, percent)
Aurora.gcd()
Aurora.random(min, max)
Aurora.bin(value)
Aurora.blockmovement(timewindow)
```

### Example
```lua
-- Texture
local spellTexture = Aurora.texture(100)
print("Spell icon: " .. spellTexture .. " Charge")

-- Group TTD
local avgTTD = Aurora.groupttd()
if avgTTD > 10 then
    -- Use longer cooldowns
end

-- Group health
local avgHealth = Aurora.grouphp()
if avgHealth < 70 then
    -- Use AoE healing
end

-- Group health count
local healthyCount = Aurora.grouphpcount(40, 80)
if healthyCount < 3 then
    -- Use emergency healing
end

-- GCD
local gcdTime = Aurora.gcd()
if ability.cooldown < gcdTime then
    -- Ability ready by GCD end
end

-- Random number
if Aurora.random(1, 100) > 70 then
    -- 30% chance action
end

-- Boolean to binary
local score = Aurora.bin(unit.aura(100)) + Aurora.bin(unit.combat)
if score == 2 then
    -- Both conditions true
end

-- Block movement
Aurora.blockmovement(0.5)
```

## Frame and Callback System

### API Usage
```lua
Aurora:OnUpdate(callback, enabled)
Aurora:OnTick(callback, enabled)
Aurora:RemoveCallback(callbackId, force)
```

### Example
```lua
local updateId = Aurora:OnUpdate(function(elapsed)
    print("Time elapsed: " .. elapsed)
end)

local tickId = Aurora:OnTick(function(elapsed)
    if player.casting then
        -- Track casting progress
    end
end)

if inCombat then
    Aurora.updateCallbacks[myCallbackId].enabled = true
else
    Aurora.updateCallbacks[myCallbackId].enabled = false
end
```

### Best Practices
- Use `OnUpdate` for non-critical operations (interface updates, status checks).
- Use `OnTick` for time-sensitive operations (cast tracking, interrupts).
- Enable/disable as needed.
- Remove unneeded callbacks.

## GUI Builder

### API Usage
```lua
local gui = Aurora.GuiBuilder:New()
gui:Category(name):Tab(name):[Element](options)
```

### Example
```lua
local gui = Aurora.GuiBuilder:New()
gui:Category("Graphics")
   :Tab("Quality")
   :Checkbox({
        text = "Enable Shadows",
        key = "graphics.shadows",
        default = true
   })
   :Slider({
        text = "View Distance",
        key = "graphics.viewDistance",
        min = 1,
        max = 10,
        default = 5
   })
   :Dropdown({
        text = "Texture Quality",
        key = "graphics.textures",
        options = {
            { text = "Low", value = "low" },
            { text = "High", value = "high" }
        },
        default = "high"
   })
```

### UI Elements
- **Button**: `{text, width, height, tooltip, onClick}`
- **Checkbox**: `{text, key, default, tooltip, onChange}`
- **Slider**: `{text, key, min, max, step, default, tooltip, onChange}`
- **Dropdown**: `{text, key, options, default, multi, width, tooltip, onChange}`
- **Header**: `{text}`
- **Half Label**: `{text}`
- **Spacer**: `()`
- **ColorPicker**: `{text, key, default, width, height, tooltip, onChange}`
- **DoubleSlider**: `{text1, text2, key, min1, max1, min2, max2, default1, default2, step, step1, step2, tooltip, onChange}`
- **Hotkey**: `{text, key, default, tooltip}`

### Best Practices
- Always provide `key` for persistence.
- Use `Aurora.texture()` for icons.
- Keep labels concise.

## Configuration System

### API Usage
```lua
Aurora.Config:Read(key)
Aurora.Config:Write(key, value)
Aurora.Config:SetDefault(key, value)
Aurora.Config:GetAll()
```

### Example
```lua
-- Set defaults
Aurora.Config:SetDefault("combat.autoTarget", true)
Aurora.Config:SetDefault("combat.minHealth", 50)

-- Read values
local autoTarget = Aurora.Config:Read("combat.autoTarget")
local minHealth = Aurora.Config:Read("combat.minHealth")

-- Write values
Aurora.Config:Write("combat.autoTarget", false)
Aurora.Config:Write("ui.scale", 1.2)
```

### Best Practices
- Organize keys hierarchically (e.g., `healing.tanks.minHealth`).
- Set defaults early.
- Use constants for keys.
- Handle loading errors.

### Example with Error Handling
```lua
local function SafeLoadConfig(key, default)
    local value = Aurora.Config:Read(key)
    if value == nil then
        print("Warning: Failed to load config for " .. key)
        return default
    end
    return value
end
```

## Enums

### Class Enum
```lua
Aurora.Enums.Class = {
    DEATHKNIGHT = 1,
    DEMONHUNTER = 2,
    DRUID = 3,
    HUNTER = 4,
    MAGE = 5,
    MONK = 6,
    PALADIN = 7,
    PRIEST = 8,
    ROGUE = 9,
    SHAMAN = 10,
    WARLOCK = 11,
    WARRIOR = 12,
    EVOKER = 13
}
```

### Power Types
```lua
Aurora.Enums.PowerTypes = {
    ["mana"] = 0,
    ["rage"] = 1,
    ["focus"] = 2,
    ["energy"] = 3,
    ["combopoints"] = 4,
    ["cp"] = 4,
    ["runes"] = 5,
    ["runicpower"] = 6,
    ["soulshards"] = 7,
    ["shards"] = 7,
    ["astralpower"] = 8,
    ["ap"] = 8,
    ["lunarpower"] = 8,
    ["holypower"] = 9,
    ["alternatepower"] = 10,
    ["maelstrom"] = 11,
    ["chi"] = 12,
    ["insanity"] = 13,
    ["arcanecharges"] = 16,
    ["fury"] = 17,
    ["pain"] = 18,
    ["essence"] = 19
}
```

### Role Detection
```lua
Aurora.Enums.IsHealer(specID)
Aurora.Enums.IsTank(specID)
```

## Spell Lists

### Lists
- `Aurora.Lists.DefaultInterruptSpells`, `Aurora.Lists.InterruptSpells`
- `Aurora.Lists.DefaultDispelSpells`, `Aurora.Lists.DispelSpells`
- `Aurora.Lists.DefaultPurgeSpells`, `Aurora.Lists.PurgeSpells`
- `Aurora.Lists.DefaultSootheSpells`, `Aurora.Lists.SootheSpells`
- `Aurora.Lists.Ores`
- `Aurora.Lists.Herbs`

### Storage
- Saved in `configs/DispelSpells.json`, `configs/PurgeSpells.json`, `configs/SootheSpells.json`.

## Boss Mods API

### API Usage
```lua
Aurora.BossMod:getactivetimers()
Aurora.BossMod:gettimer(text)
Aurora.BossMod:gettimers(text)
Aurora.BossMod:hastimer(text)
Aurora.BossMod:gettimerremaining(text)
Aurora.BossMod:getpulltimer()
Aurora.BossMod:ispulling()
Aurora.BossMod:getpulltimeremaining()
Aurora.BossMod:getpullstring()
```

### Example
```lua
-- React to timer
function CheckImportantTimer()
    if Aurora.BossMod:hastimer("Important Ability") then
        local remaining = Aurora.BossMod:gettimerremaining("Important Ability")
        if remaining < 5 then
            PrepareForAbility()
        end
    end
end

-- Pull timer coordination
function CheckPullStatus()
    if Aurora.BossMod:ispulling() then
        local remaining = Aurora.BossMod:getpulltimeremaining()
        if remaining <= 3 then
            CastPrePullAbility()
        end
    end
end
```

## Keyboard Hook API

### API Usage
```lua
Aurora.KeyBindingSystem:RegisterWoWHotkey(hotkey, callback)
Aurora.KeyBindingSystem:RegisterKeyBinding(keyCodes, callback)
Aurora.KeyBindingSystem:RegisterHotkeyString(hotkeyString, callback)
Aurora.KeyBindingSystem:UnregisterHotkeyString(hotkeyString)
Aurora.KeyBindingSystem:UnregisterKeyBinding(keyCodes)
Aurora.KeyBindingSystem:ClearKeyBindings()
Aurora.HotkeyEnum:ParseHotkeyString(hotkeyString)
Aurora.HotkeyEnum:CreateHotkeyString(modifiers, key)
Aurora.HotkeyEnum:FromWoWKey(key)
Aurora.HotkeyEnum:ToWoWKey(keyCode)
Aurora.HotkeyEnum:HotkeyStringToKeyCodes(hotkeyString)
Aurora.HotkeyEnum:KeyCodesToHotkeyString(keyCodes)
```

### Example
```lua
-- Register WoW hotkey
Aurora.KeyBindingSystem:RegisterWoWHotkey("SHIFT-A", function(isPressed)
    if isPressed then
        print("Shift+A pressed")
    end
end)

-- Register key codes
local keyCodes = {Aurora.HotkeyEnum.CONTROL, Aurora.HotkeyEnum.C}
Aurora.KeyBindingSystem:RegisterKeyBinding(keyCodes, function(isPressed)
    if isPressed then
        print("Control+C pressed")
    end
end)

-- Parse hotkey string
local hotkeyComponents = Aurora.HotkeyEnum:ParseHotkeyString("CTRL-SHIFT-A")
-- Result: { modifiers = {"CTRL", "SHIFT"}, key = "A" }
```

## Encounter Manager API

### API Usage
```lua
Aurora.EncounterManager:GetCurrentEncounter()
Aurora.EncounterManager:GetEncounterElapsedTime()
Aurora.EncounterManager:GetActiveBossUnits()
Aurora.EncounterManager.debug = true/false
Aurora.EncounterManager.updateRate = number
```

### Example
```lua
Aurora.EncounterManager.Encounters = {
    [2816] = {
        name = "Kyrioss",
        timing = true,
        track_bosses = true,
        callback = function(encounter)
            print("Encounter started:", encounter.name)
        end,
        pulse = function(encounter, deltaTime)
            for guid, unit in pairs(encounter.bossUnits) do
                if unit.exists then
                    local pos = unit.position
                    print(string.format("%s: HP: %.1f%%, Pos: %.1f, %.1f, %.1f, Casting: %s", 
                        unit.name, unit.hp, pos.x, pos.y, pos.z, unit.casting or "None"))
                end
            end
            if encounter.elapsedTime >= 60 then
                print("One minute into the fight!")
            end
        end
    }
}
```

## Discord Webhook

### API Usage
```lua
Discord:SetWebhookUrl(url)
Discord:SendEmbed(embed)
Discord.DiscordEmbed.new()
embed:SetTitle(title)
embed:SetDescription(description)
embed:SetUrl(url)
embed:SetTimestamp(timestamp)
embed:SetColor(color)
embed:SetFooter(footer)
embed:SetImage(image)
embed:SetThumbnail(thumbnail)
embed:SetAuthor(author)
embed:AddField(field)
Discord.EmbedFooter.new(text, icon_url, proxy_icon_url)
Discord.EmbedImage.new(url, proxy_url, height, width)
Discord.EmbedAuthor.new(name, url, icon_url, proxy_icon_url)
Discord.EmbedField.new(name, value, inline)
```

### Example
```lua
local Discord = Aurora.Discord
Discord:SetWebhookUrl("your-webhook-url")

local embed = Discord.DiscordEmbed.new()
    :SetTitle("Hello World")
    :SetDescription("This is a complete embed example")
    :SetUrl("https://aurora-wow.wtf")
    :SetColor(0xFF0000)
    :SetFooter(Discord.EmbedFooter.new("Footer Text", "https://example.com/footer-icon.png"))
    :SetImage(Discord.EmbedImage.new("https://example.com/image.png"))
    :SetThumbnail(Discord.EmbedImage.new("https://example.com/thumbnail.png"))
    :SetAuthor(Discord.EmbedAuthor.new("Author Name", "https://example.com", "https://example.com/author-icon.png"))
    :AddField(Discord.EmbedField.new("Field 1", "Value 1", true))
    :AddField(Discord.EmbedField.new("Field 2", "Value 2", true))

Discord:SendEmbed(embed)
```

### Best Practices
- Set webhook URL first.
- Use decimal color values.
- Respect Discord field limits (max 25).
- Keep content within 2000 characters.
- Use direct image URLs.

## State Registrar

### API Usage
```lua
local state = Aurora.StateExpression:New(name, default)
state:RegisterHandler(eventName, handler)
state:Update(eventName, ...)
state:SetWindow(seconds)
state() -- Get value
state.value -- Direct value access
```

### Example
```lua
local damageTaken = Aurora.StateExpression:New("damage_taken")
damageTaken.hits = {}

local function cleanupOldHits(self, now)
    local windowStart = now - self.window
    local removed = 0
    while self.hits[1] and self.hits[1].time < windowStart do
        table.remove(self.hits, 1)
        removed = removed + 1
    end
    local total = 0
    for _, hit in ipairs(self.hits) do
        total = total + (tonumber(hit.amount) or 0)
    end
    if total ~= self.value then
        self.value = total
    end
    return removed
end

local f = CreateFrame("Frame")
f:RegisterEvent("UNIT_COMBAT")
local timerFrame = CreateFrame("Frame")
timerFrame:SetScript("OnUpdate", function(self, elapsed)
    if #damageTaken.hits > 0 then
        cleanupOldHits(damageTaken, GetTime())
    end
end)

f:SetScript("OnEvent", function(self, event, ...)
    damageTaken:Update(event, ...)
end)

damageTaken:RegisterHandler("UNIT_COMBAT", function(self, now, unitTarget, event, flagText, amount, schoolMask)
    if unitTarget ~= "player" then return end
    local damageAmount = tonumber(amount) or 0
    local damageType = schoolTypes[schoolMask] or "Unknown"
    table.insert(self.hits, {
        time = now,
        amount = damageAmount,
        type = damageType,
        isCrit = flagText == "CRITICAL",
        isCrushing = flagText == "CRUSHING",
        isGlancing = flagText == "GLANCING"
    })
    cleanupOldHits(self, now)
end)

Aurora.state.damage_taken = damageTaken
```

### Usage
```lua
local totalDamage = damageTaken()
for _, hit in ipairs(damageTaken.hits) do
    if hit.isCrit then
        print(string.format("%s critical hit for %d!", hit.type, hit.amount))
    end
end
```