# Learning
### Prerequisites

- If you haven't already, [download awful](https://awful.wtf/download).

- _If you don't already have a code editor, grab [VSCode](https://code.visualstudio.com/)._

- Get these AddOns; they're invaluable tools that'll save you a lot of time:

> [idTip](https://www.curseforge.com/wow/addons/idtip-community-fork) - Displays IDs in spell, talent, aura, and item tooltips.
>
> [BugSack](https://www.curseforge.com/wow/addons/bugsack) - Stores Lua errors & their full debug stack to view later.
>
> [BugGrabber](https://www.curseforge.com/wow/addons/bug-grabber) - Required dependency of BugSack.

### **Setting up your directory**

#### Gimme your project's name as a variable:

<Observe>
  {({ setState, ...state }) => (
    <input className='bg-slate-800 rounded-lg px-4 py-2 mt-5 text-white' value={state.name} onChange={(e) => { 
        const v = e.target.value?.replace(/\s/g, '').replace(/[^a-zA-Z0-9]/g, '')
        setState(s => ({ name: v })) } 
    } />
  )}
</Observe>

e.g, `awful`, `awfulRoutines`, `awfulBot`, `MyProject`

- Once awful is installed in your unlocker folder, create the awful/**routines** folder if it doesn't already exist.

- Inside of `routines/`, create your project folder: awful/routines/**<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>**.

#### Our project directory:

> .../awful/routines/**<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>**

## **Getting our code to load (bars)**

Now, open the folder you made in your preferred code editor. [VSCode](https://code.visualstudio.com/) is a great option if you're unsure.

### First .lua file

You'll need to write some **Lua** to create awful scripts. Go ahead and create **<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua** in your directory.

> awful/routines/<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/**<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua**

Nice! Now, we're going to write some stuff in there!

```lua
local Unlocker, awful, project = ...

awful.DevMode = true
```

<Callout emoji="ℹ️" type="info">
  This first line will be consistent throughout all files going forward, you're importing required **namespaces** into the file, including **your own project's**. You also enabled `awful.DevMode`, which will do a few neat things to make life as a dev easier.
</Callout>

> 

### Now, we need an `awful-config.json`

Your `awful-config.json` file tells awful which Lua files it should load, and in which order (which is important, more on that later). Any files that aren't explicitly defined here will not be loaded. It also unlocks some other cool goodies down the line with our CI/CD integrations. Go ahead and create `awful-config.json` in your directory.

> awful/routines/<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/**awful-config.json**

```json filename=awful-config.json 
{
    "load": [
        "example.lua"
    ]
}
```

<Callout emoji="ℹ️" type="info">
  The string `"example.lua"` in the `awful-config` load array refers to the **<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua** file you created earlier. Make sure it's actually set to the name of your file, in this case: **<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua**
</Callout>

## **Hello world?**

Let's see if we're in. When you set `awful.DevMode` to `true`, the `awful` namespace became accessible globally. You should now be able to use `/dump` to read its contents via in-game chat. Try running the command below in WoW chat:

> /dump awful.hello

- If this printed something real nice to chat, **you're in!** Otherwise, back to step 1..

> If you ever have questions or need help, we have an **amazing** community of skilled, active, and helpful routine developers in our discord: https://discord.gg/JkQEPpED6W

## **Setting up our routine**

Now we're gonna make a **couple more files & directories**, and some changes to our core file, **<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua**.

This should only take a couple of minutes. By the time you're done, you'll have a legit routine casting spells.

### **Create a directory for your class:**

> <Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/**warrior**

### **...and these files for your spec:**

> <Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/warrior/**arms-actor.lua**

> <Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/warrior/**arms-spells.lua**

<Callout emoji="⚠️" type="warning">
  _Make sure you include these files in your `awful-config.json` or they will not be loaded!_
</Callout>

```json filename=awful-config.json 
{
    "load": [
        "example.lua",
        "warrior/arms-actor.lua",
        "warrior/arms-spells.lua"
    ]
}
```

## **Initializing the routine**

First, we'll create a **routine actor** object. You can create one for each specialization, it gives awful something to run when toggled on the correct class and spec.

Then we'll use `actor:Init(callback, tickRate)` to assign it a function to run on each tick.

### What???

Don't stress the details, **you'll have working code in a sec**. Everything else is explained elsewhere in the docs.

> For this example, I'm making a basic **Arms Warrior** routine on a class trial. If you're feeling confident, I encourage you to try a **different class** or throw in some **extra spells & logic** to challenge yourself!

Let's write some code lmao.

### **Core File**: .../<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/**<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>.lua**

- First, we're going to create a **table** for our **class** inside our project's **namespace**. We're doing this in our **core file**, as we assigned it to be loaded first in our `awful-config`.

- We'll also create our **routine actor** here, before initializing it in the actor file. Since we're playing **Arms**, our **specialization index** is `1`. Be sure to replace `1` in this file with the **correct spec index** and **"warrior"** with the correct class, if you're playing something else.

> Your **specialization index** can be acquired from the same **dump command** we used in-game, like this: `/dump GetSpecialization()`, or you can just **count from top to bottom, or left to right spec, depending on game version** in your **talents frame** under the **specialization tab** in-game.

```lua
local Unlocker, awful, project = ...

awful.DevMode = true

project.warrior = {}
project.warrior.arms = awful.Actor:New({ spec = 1, class = "warrior" })
```

### **Actor File**: .../<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/warrior/**arms-actor.lua**

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

-- stuff out here only runs once, when the file is first loaded.
print("Example warrior locked and loaded!")

-- this is the routine actor.
arms:Init(function()
    -- everything in here is running *on every tick*
    -- its goal is to "act" every frame
    -- that's why you will be spammed with this print when you toggle your routine.
    -- these are comments and you can remove them :)
    print("Wow, it's really running!")
end)
```

> Now try a **/reload**. You should see your little OnLoad class print first. Go ahead and **enable the routine** by typing **/awful toggle** ... You should be spammed with that print you made above! If not, go back and make sure you followed every step **perfectly**. If even one character is wrong, **it will not work**.

## **Creating our spell objects**

#### .../<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/warrior/**arms-spells.lua**

- We're gonna call this file our **spell book** - it'll be where we create all of our **spell objects** with `awful.Spell`

You'll always need to acquire the correct **spell IDs** for spell objects to work properly. This is where idTip comes in handy! If you didn't get the AddOns I listed at the start of this guide, go get 'em.

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

local Spell = awful.Spell
awful.Populate({
    execute = Spell(163201),
    slam = Spell(1464),
    mortalStrike = Spell(12294),
    overpower = Spell(7384),
    warbreaker = Spell(262161), -- <-- don't forget the comma here when you add more spells, this is a table!
}, arms, getfenv(1))
-- ^^^ make sure you replace "arms" here with your specialization's routine actor!
```

Here's what we've done in this file so far:

- Created a local reference to our actor, **arms**

- Populated it, as well as our current environment with some delicious **spell objects**.

Next, we'll initialize some **callbacks** for all of these spells. Prepare for some more code.

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

local Spell = awful.Spell
awful.Populate({
    execute = Spell(163201),
    slam = Spell(1464),
    mortalStrike = Spell(12294),
    overpower = Spell(7384),
    warbreaker = Spell(262161),
}, arms, getfenv(1))

execute:Callback(function(spell)
    spell:Cast(target)
end)

overpower:Callback(function(spell)
    spell:Cast(target)
end)

warbreaker:Callback(function(spell)
    spell:Cast(target)
end)

mortalStrike:Callback(function(spell)
    spell:Cast(target)
end)

slam:Callback(function(spell)
    spell:Cast(target)
end)
```

> Nice! Now we have some **usable spell objects**! Now let's pull them into our actor.

## **It's ALIVE!!!**

Back in our **routine actor** file, let's start by just **calling** all of our **spell objects** in a basic priority arrangement and make it **auto attack** to generate **rage**.

You'll want to [pull up](https://www.youtube.com/watch?v=jjyDfLPKmNk) on a Training Dummy for this.

> Lua is interpreted line by line, **left to right**, then **top to bottom**. Be sure to consciously sort your **actions** based on **highest** to **lowest** priority!

> #### .../<Observe>{({ setState, ...state }) => (<>{state.name}</>)}</Observe>/warrior/**arms-actor.lua**

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

print("Example warrior locked and loaded!")

arms:Init(function()
    -- only do this stuff if our target is an enemy
    if target.enemy then
        -- auto attack to generate rage
        StartAttack()
        -- spells we created in the spell book are magically available in our actor!
        execute()
        overpower()
        warbreaker()
        mortalStrike()
        slam()
    end
end)
```

> Now, hit up that **/reload**, enable it by typing **/awful toggle** in chat, and you might notice... **you have a functioning routine!!!** 🎉

## **Souping it up a bit...**

> Seriously, if you made it this far... **amazing work.**
>
> I'm really excited to see what you come up with in the future!

![clappa](https://i.pinimg.com/originals/01/d9/a4/01d9a44af5aa852624b87f8f280f4942.gif ":size=128")

Before we wrap things up, let's clean this routine up just a bit... We're gonna add some _relatively_ advanced logic like it's nothing:

- Make it avoid attacking into physical immunities by **defining the damage type** of our **damaging spells**

- Make it auto charge / leap to the target in a way that feels natural, by **tracking player movement history**

- Make it cast warbreaker and avatar at high priority when `/awful burst` is used

- Add some **fancy alerts** for our gap closers and burst abilities

- Apply **labels** to some of our **spell callbacks** and pass them in the actor, to have various logic at different priorities for the same spell

- Make slam stop eating up all of our rage

- Have it maintain Rend on target

- Use sweeping strikes when there's nothing else to do (lowest priority)

### **The spell book expands**

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

local Spell = awful.Spell
awful.Populate({
    execute = Spell(163201, { damage = "physical" }),
    slam = Spell(1464, { damage = "physical" }),
    mortalStrike = Spell(12294, { damage = "physical" }),
    overpower = Spell(7384, { damage = "physical" }),
    warbreaker = Spell(262161, { damage = "physical", facingNotRequired = true }),
    rend = Spell(772, { damage = "physical", bleed = true }),
    sweepingStrikes = Spell(260708),
    charge = Spell(100),
    leap = Spell(6544),
    avatar = Spell(107574),
}, arms, getfenv(1))

charge:Callback("gapclose", function(spell)
    -- don't charge if we recently leaped
    if leap.cd > 29 then return end
    if target.distance > 12 and player.movingToward(target, { angle = 45, duration = 0.15 })  then
        if spell:Cast(target) then
            awful.alert("Charge (Gapclose)", spell.id)
        end
    end
end)

leap:Callback("gapclose", function(spell)
    -- don't leap if we recently charged
    if charge.recentlyUsed(2) then return end
    if target.distance > 12 and player.movingToward(target, { angle = 45, duration = 0.15 }) then
        if spell:AoECast(target) then
            awful.alert("Leap (Gapclose)", spell.id)
        end
    end
end)

execute:Callback(function(spell)
    spell:Cast(target)
end)

overpower:Callback(function(spell)
    spell:Cast(target)
end)

-- avatar during burst
avatar:Callback("burst", function(spell)
    if target.meleeRange and spell:Cast() then
        awful.alert("Avatar (Burst)", spell.id)
    end
end)

-- high priority warbreaker on /awful burst
warbreaker:Callback("burst", function(spell)
    if spell:Cast(target) then
        awful.alert("Warbreaker (Burst)", spell.id)
    end
end)

-- non-labelled warbreaker falls lower in prio list
warbreaker:Callback(function(spell)
    spell:Cast(target)
end)

mortalStrike:Callback(function(spell)
    spell:Cast(target)
end)

slam:Callback(function(spell)
    -- only use slam while capping out on rage so we can always MS on cooldown...
    if mortalStrike.cd > 3 or player.rage > 70 then
		spell:Cast(target)
    end
end)

-- maintain rend single target, high priority
rend:Callback("maintain", function(spell)
    if target.debuffRemains(spell.id, player) < 4 then
        spell:Cast(target)
    end
end)

-- spread rend, low priority
-- (rend spreading is probably not great, it's just an example of how we *can* do it)
rend:Callback("spread", function(spell)
    if player.rage > 55 then
        for _, enemy in ipairs(enemies) do
            if not enemy.isUnit(target) and enemy.debuffRemains(772, player) < 4 then
                if spell:Cast(enemy) then
                    awful.alert("Rend " .. enemy.class .. " (Spread)", spell.id)
                    return true
                end
            end
        end
    end
end)

sweepingStrikes:Callback(function(spell)
    if player.combat then
        spell:Cast()
	end
end)
```

- See how we're able to set up **callback functions** for each **spell object** in a way that will allow us to stay organized? We can group our **conditions** and/or **actions** into neat little modular components that we can easily access from the actor via **callback labels**, like **"burst"**, **"maintain"**, or **"spread"** above. This naturally follows best practices for staying organized, maximizes performance by restricting the block of code from running unless the spell is off cd and usable, and gives us the ability to easily move around the **priority** of what may soon be huge blocks of code.

- When we have another **reason** that a spell should be cast, we can add another **callback** under a different **label** and fit it into our existing priority list.

### **It does more stuff!**

```lua
local Unlocker, awful, project = ...
local arms = project.warrior.arms

print("Example warrior locked and loaded!")

arms:Init(function()

	-- keep in mind we're only doing *anything* in this if/then statement if there is an enemy target.
	if target.enemy then
		StartAttack()
		-- burst!
		if awful.burst then
			avatar("burst")
			warbreaker("burst")
		end
		charge("gapclose")
		leap("gapclose")
		execute()
		rend("maintain")
		overpower()
		warbreaker()
		mortalStrike()
		-- rend("spread") -- hey, this is commented out, so it won't spread rend!
		slam()
		sweepingStrikes()
	end

end)
```

> **Sheeesh**, this thing really cranks now. Nice!

- I'm sure you'll come up with **much** better than this though. This is just an **ultra-basic** routine whipped up to get you introduced, it doesn't cover the tiniest fraction of what you'll find **awful framework** is capable of. I'll be doing my best to keep things **thoroughly** documented going into the future. So keep an eye out!

**Keep playing around, don't be afraid to try new things. Experiment and have a good time. Most importantly, _always_ show off what you make!**

#### I hope this has been helpful. Send any feedback my way! Alexei#1234 on Discord.

Hop in the [**awful devs** Discord](https://discord.gg/H7HwTc6aZA), our community is growing and we'd love to have you!

...Now, **click around through the stuff on the left** and explore the **power** of awful framework!!!

</State>

----
pages/_document.js
import React from 'react'
import Document, { Html, Head, Main, NextScript } from 'next/document'
import { SkipNavLink } from '@reach/skip-nav'

class MyDocument extends Document {
  render() {
    return (
      <Html lang="en">
        <Head />
        <body>
          <SkipNavLink />
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}

export default MyDocument

----
pages/awful_toolbox/toolbox_tools.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

> awful has a lot of useful tools to help clean up code and make complex tasks easier to perform
>
> i'm throwing them all in here, let's call it **the toolbox**

# **important variables**

## time

> `awful.time`
>
> current time in seconds, equal to `GetTime()`, but only updated when awful framework ticks.

## buffer

> `awful.buffer`
>
> a combination of dynamic values which is roughly equal to **the time it should take for what's displayed on your client to reach the servers, plus 1 tick of buffer for your script to react before it's too late, and 30ms to account for any latency jitter**:
>
> buffer = **latency** + **tickRate** + **jitter**

```lua
-- buffer is used in many places within the framework to dynamically account for latency.

-- here's a quick example of how you can use it yourself:
-- calculating when to time a spell reflection or interrupt at the VERY latest possible moment:
if enemy.castRemains <= awful.buffer then
    if interrupt.cd == 0 and interrupt:Cast(enemy) then
        awful.alert("interrupted thing")
    elseif reflect:Cast() then
        awful.alert("reflected thing!")
    end
end
```

## latency

> `awful.latency`
>
> Your latency to the game server, averaged out over the last 30 seconds, with HTTP overhead accounted for.

## tickRate

> `awful.tickRate`
>
> The time between each awful framework **tick**.
>
> Generally, if an event will happen before `time` + `tickRate`, your script will not 'tick' again to react in time. That's why this is an important variable, it determines how many `ticks` you want to think ahead.
>
> The value always varies for two reasons:
>
> 1.) [Routine Actors](actor) can set a static tickrate, and the rest of the framework will comply with it, but if framerate is too low, the fastest it can run is once per frame.
>
> 2.) If an Actor is running once per frame, the value will still be > 0, roughly equal to the time between each frame `1/GetFramerate()`, but the value is actually calculated each tick (`current tick time` - `last tick time`) then averaged out between the last 5 ticks.

## spellCastBuffer

> `awful.spellCastBuffer`
>
> The Spell Queue Window duration set by awful
>
> This is also the maximum amount of time remaining on GCD or cast time that it will queue the next spell within.

## gcd

> `awful.gcd`
>
> The total expected GCD duration per general 1.5s GCD incurring spell cast
>
> Most spells on GCD incur a base 1.5s cooldown modified by haste, some incur their own shorter one
>
> ```lua
> 1.5 / ( 1 + player.haste )
> ```
>
> see also: Spell Object Attribute `spell.gcd` - the gcd that the queried spell incurs

## hasControl

> `awful.hasControl`
>
> whether or not the player has control of their character

## zone

> `awful.zone`
>
> current zone text

## mapID

> `awful.mapID`
>
> current mapID

## powerTypes

```lua
awful.powerTypes = {
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

# **awesome functions**

## onTick

Adds a callback function to the main framework ticker. These are called in the order added, just before the routine actor.

```lua
awful.onTick(callback[,enabled])
```

```lua
-- only running while routine is enabled
awful.onTick(function()
    print("This is being called a lot, but only while my routine is on!")
end, true)

-- running every tick, all the time
awful.onTick(function()
    print("This is being called a lot, all the time!")
end)
```

## onUpdate

Does the same thing as `onTick`, but runs every frame instead (almost always faster).

```lua
awful.onUpdate(callback[,enabled])
```

## onEvent

Assign a callback to a combat log or other event firing.

```lua
-- passing it a specific event type will create a frame, register that frame for the event, and assign it your callback as an OnEvent handler
local function printSpec()
	print(awful.player.spec)
end
awful.onEvent(printSpec, "PLAYER_SPECIALIZATION_CHANGED")

-- if a specific event type is not passed, it will fire on combat log events,
-- passing event info, type, as well as source (and dest if applicable) units as awful objects.
awful.onEvent(function(info, event, source, dest)
  local time = GetTime()
	if event == "SPELL_CAST_SUCCESS" then
		-- combat log event info is passed as a table, and can be unpacked like so
		-- this is starting at the 12th entry of SPELL_CAST_SUCCESS, which is the spellID
		local spellID, spellName = select(12, unpack(info))
		print(time, spellID, spellName, source.name, source.class, dest.name)
	end
end)
```

## bin

Converts value of given expression / variable into binary.

- Any value (bool, string, number, float, function) is 1
- No value (nil, false) is 0.

> bin(**nil / false**) : **0**

> bin(**any value**) : **1**

```lua
awful.bin(conditions) : 1 | 0
```

```lua
local bin = awful.bin

print( bin(player.isPlayer) )
-- 1

print( bin(player.enemy) )
-- 0

print( bin(player.isPlayer) * 30 )
-- 30

print( bin(player.enemy) * 30 )
-- 0

-- example use case: only cast quarter DR sheep if 17.5/18s remains on the DR reset.
if target.incapDR == 1 or target.idrRemains > 14 + bin(target.idr == 0.25) * 3.5 and target.idr >= 0.25 then
    sheep:Cast(target)
end

-- what this would have looked like without bin
if target.incapDR == 1 or target.idrRemains > 14 + (target.idr == 0.25 and 3.5 or 0) and target.idr >= 0.25 then
    sheep:Cast(target)
end
```

## controlMovement

Temporarily disables movement (and optionally facing) input from the player.

```lua
awful.controlMovement(duration[,facing])
```

> see also: `stopMoving` spell:Cast option

```lua
-- somewhere in our actor, using super basic logic to take control of movement to finish a sheep cast
-- only controlling it for 2 frames, so that we
if player.castID == sheep.id and player.castTarget.los then
    awful.alert("Controlling Movement (Sheep)", sheep.id)
    awful.controlMovement(awful.tickRate * 2)
end
```

## controlFacing

Temporarily disables facing input from the player, including right mouse button movement and keyboard turning actions.

```lua
awful.controlFacing(duration)
```

## StopMoving

Immediately stops the player from moving, as long as they're not in the air.

**`awful.StopMoving()`**

## Populate

Creates a reference to each entry within given associative array (Param 1) inside of all other given tables, namespaces, scopes, etc. as well (Params 2 - N)

```lua
awful.Populate(aArray, t1[, t2, t3, t4, t5, ...])
```

> This is a useful tool when used correctly, that allows you to 'merge' references to all values (including functions, objects, tables, etc.) between multiple tables - which can be namespaces, lists of objects, etc.
>
> For example, you can create a list of spells as an associative array like this:
>
> ```lua
> local list = {
>    healingTouch = Spell(1234),
>    mightyBash = Spell(12345),
>    ...
> }
> ```
>
> Then make a reference to all keys in the list available to other table(s), namespaces, or scopes using Populate, like this:
>
> ```lua
> local actor = project.druid.feral
> local otherList = {}
>
> -- param 1, the list we want to copy references from
> -- all other params are being copied to
> awful.Populate(list, otherList, actor, getfenv(1))
>
> -- now otherList AND actor can access healingTouch & mightyBash!
> print(otherList.mightyBash.cooldown)
> print(actor.healingTouch.id)
> -- also, since we copied to `getfenv(1)`, they're available directly to the scope of this file!
> print(mightyBash.name)
> ```
>
> Note: The key `parent` will be written to each table within the associative array. Mainly to allow objects to find their siblings in the list. For example, all spell objects in a list automatically have direct access to each other within their :Callback function environments after `Populate` is called
>
> **tl;dr**: **This thing puts a copy of all the stuff in one table into other tables**

## Pull Timer

Creates a reference to a pull timer that has been used by things like DBM or BigWigs. Defaults to 0 if no pull timer.

```lua
awful.pullTimer : 0
awful.pullBradsWilly : 0
```

----
pages/awful_toolbox/toolbox_alerts_visuals.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

These are our visual tools that may help to keep the user aware of what's happening, in style.

- These functions are located directly under the `awful` namespace.

# **alert**

Displays a toast alert for the user.

![alerts](/gifs/alerts.gif)

- Trying to recreate an identical alert while it's still displayed will just extend the duration, so feel free to spam it while it's relevant!

- We included a lot of customization options so you can get the tone & delivery of your message across clearly, but we also want to keep the theme consistent with other alerts.

- You can opt for as much or as little customization as you want. All that is required is a message, even the texture is optional.

- Always returns true so you can include it as part of your conditional expressions for cleaner code.

```lua
awful.alert([message / {options}], [texture]) : true
```

Options:

> `message` _string_ - The message of your alert.

> `texture` _integer_ - SpellID to use as the texture for this alert.

> `duration` _float_ - Lifespan of the alert (not including fade in and fade out animations)

> `fadeIn` _float_ - Duration of the fade in animation. Default 0.175 (cubic-bezier easing)

> `fadeOut` _float_ - Duration of the fade out animation. Default 0.3 (cubic-bezier easing)

> `bgColor` _array_ - The rgb[a] color value (0-1) of the texture background (essentially the shadow, the "mood" of the alert) inside of an array. `{ r, g, b [,a]}`

> `imgX` _float_ - Number of pixels to move the texture on the X axis behind the circular mask.

> `imgY` _float_ - Number of pixels to move the texture on the Y axis behind the circular mask.

> `imgScale` _float_ - The scale of the texture behind the circular mask.

- **Here are some example alerts. I encourage you to `/run` them in-game and see how they look.**

```lua
-- nice basic alert, just some text
awful.alert("hi mom")

-- adding a spell texture to the mix.
awful.alert("Sheepy Sheep", 118)

-- some text colors
awful.alert("Sheepy? "..awful.colors.red.."NO.", 118)

-- now a red background to go with that. we'll have to use options now.
awful.alert({
    message = "Sheepy? "..awful.colors.red.."NO.",
    texture = 118,
    bgColor = awful.rgbColors.red
})

-- passing our own custom colors... the color escape won't work through /run in chat. you'll have to write & run this from your routine to test it.
awful.alert({
    message = "Sheepy? |cFF5c9affYES!",
    texture = 118,
    bgColor = {30/255, 60/255, 120/255, 0.95}
})

-- let's see what Meteor looks like...
awful.alert("Meteor!", 153561)

-- hmm, i don't like how the meteor itself is cut off by the mask. i want to move it up and to the left a bit. maybe scale it down too.
awful.alert({
    message="Meteor!",
    texture=153561,
    imgX = 1,
    imgY = 0.55,
    imgScale = 0.875
})
-- nice, that's much better...
```

## textureEscape

Converts spellID or texture ID into a texture escape sequence string for use in alerts, other frames, or prints.

```lua
awful.textureEscape(spellID/FileDataID[,size,offsets])
```

```lua
-- texture = freezing trap spellID
-- size 16px
-- offsets "x:y", here am moving it up 2 pixels
local trap = awful.textureEscape(187650, 16, "0:2")

-- now gonna alert and print this
awful.alert(trap .. " the healer!", 187650)
print(trap .. " in the chat!")
```

## Draw
> awful.Draw
Draws various things ingame - to be expanded with more examples.
```lua
local Draw = awful.Draw
Draw(function(draw)
    draw:SetWidth(number) -- Set the width of the draw, such as the width of a Line
    draw:SetColor(r, g, b, a) -- Set the color of the draw, such as the color of a Line

    draw:Line(x1, y1, z1, x2, y2, z2, maxDistance) -- Draw a line from xyz1 to xyz2, maxDistance being how many yards a line can be before it attaches a "new line" to the end of the previous line
    draw:Circle(x, y, z, radius, steps) -- Draw a circle at xyz with a radius and steps, steps being how many lines are used to draw the circle
    draw:Cylinder(x, y, z, radius, height) -- Draw a cylinder at xyz with a radius and height
    draw:Arc(x, y, z, size, arc, rotation) -- Draw an arc at xyz with a size (i.e. length of the arc), arc being how many degrees the arc is, and rotation being the "facing direction" of the arc
    draw:Rectangle(x, y, z, width, length, rotation) -- Draw a rectangle at xyz with a width, length, and rotation
    draw:Outline(x, y, z, radius) -- Draw an outline at xyz with a radius, basically a thick circle
    draw:FilledCircle(x, y, z, radius, steps) -- Draw a filled circle at xyz with a radius and steps
    draw:Triangle(x, y, z, v1, v2, v3, cull, wireframe) -- Draw a triangle at xyz with vertices v1, v2, and v3, cull true/false backface culling, wireframe true/false wireframe around the triangle
    draw:Text(string, font, x, y, z) -- Draw a string of text using the defined font on the provided coordinates. You can reference the awful font by creating it outside of this function as a local, e.g. local AwfulFont = awful.createFont(10, "OUTLINE")
    draw:Texture(config, x, y, z, alphaA) -- Draw a texture at xyz with an alpha value, config being the texture path e.g. {texture = path, width = number, height = number}
end)
```

----
pages/awful_toolbox/config.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

## Awful Config

Some users may wish to store certain things in between reloads or per character. This can be accomplished by using the `awful.config` method.

A basic example to save any type of data:

```lua
local Unlocker, awful, projectName = ...

-- create the config file within awful
local config = awful.NewConfig("projectName")

-- use that config file globally
projectName.config = config

-- in another file you can use, or add to the config
local config = projectName.config

-- such as storing a position for a widget frame you create
config.savedPos = {
    point = "CENTER",
    x = 0,
    y = 0,
}

```

For example if you'd like to store config options per character this is what you would do:

```lua
local Unlocker, awful, projectName = ...

local name, realm = UnitFullName("player")
local config = awful.NewConfig('project' .. name .. realm)

-- any primitive type or table containing primitive types can be persisted in config
config.savedBool = true
config.savedInt = 123
config.savedArray = { 
    123,
    'abc',
    true,
    false
}
config.savedAssociativeArray = {
    cats = "cool",
    dogs = "drool" 
}

-- reference types like functions can technically be stored in
-- a config table, but will not persist through reloads as
-- the value behind it is just a temporary memory address
config.savedFunction = function(...)
    print('args:', ...)
end
-- this redeclaration is required on load for the function to retain its functionality

```

## Triggering a Save

Any shallow write to the config will trigger a config save, for example:

```lua

-- config.savedVar value is updated and saved to persist through reloads and restarts
config.savedVar = true

-- config.newSavedTable is updated and saved to persist
config.newSavedTable = { 'a', 'b' }

-- config.newSavedTable is updated for the current session, but is NOT saved and will not persist
table.insert(config.newSavedTable, 'c')

-- you must do a shallow write to the config table to save and persist
local function insertSubConfigValue(key, value)
    -- nested write
    tinsert(config[key], value)

    -- shallow write saves and persists
    config[key] = config[key]
end

-- saves and persists
insertSubConfigValue("newSavedTable", 'c')

```
----
pages/awful_toolbox/meta.en-US.json
{
    "toolbox_tools": "General Tools",
    "toolbox_alerts_visuals": "Alerts & Visuals",
    "config": "Config"
}
----
pages/awful_toolbox/navigation.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

### **awful.path**

- Returns an [awful path](navigation#path)

> Create path from player to target

```lua
local player, target = awful.player, awful.target

-- path from player to target
local path = awful.path(player, target)

-- simplify path
path = path.simplify()

-- iterate path
path.loop(function(p)
  local x,y,z = p.x, p.y, p.z

  -- do stuff with point in path

  -- break loop by returning true
  return true
end)
```

> Path between player and coords, plus some other methods of path

```lua
awful.onTick(function()
  local path = awful.path(player, x, y, z)
  -- simplify path, both args (tolerance, highestQuality) optional
  path = path.simplify(1, 1)
  -- draw line between each point in the path
  path.draw()
  -- follow the path (moves your character along the path)
  path.follow()
end)
```

### path object

- path object is first return of `awful.path`
- an array of nodes `{ x = 1093.3312, y = 1996.940, z = 92.01355 }`
- contains some built in methods for manipulating and interacting with the path
- unlocker agnostic, should work nearly the same with any unlocker supported

```lua
-- returns the path simplified with Douglas Peucker path simplification algorithm
path.simplify(tolerance, highestQuality)

-- draws the path for this tick
path.draw()

-- follow the path, moving your character along it each time called
path.follow()
```

----
pages/item_objects/item-object-methods.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they

Item Objects contain some very useful methods (object-oriented functions) that allow you to use the underlying Item in many different ways, simple and complex. They also contain methods that help organize and structure your code by item in some super neat ways.

## Quick example

- `:Use`, `:Update`, and `:Usable` are all methods used below

```lua
local healthstone = awful.Item(5512)


-- use method
if healthstone:Use() then awful.alert("Using Healthstone!") end

-- some update methods
healthstone:Update(function(item)
  if not item:Usable then return end
  if player.hp < 50 then
    return item:Use() and alert("Used Healthstone!")
  end
end)

healthstone() -- invoking that update function, will use Healthstone below 50% HP when usable
```

# Methods

# Casting Methods

## Use

> Checks if the spell is usable, then attempts to use it. Returns true if use attempted.

```lua
item:Use(unit) -- params are optional
```

## Usable

> Returns true if the item is usable on given unit.

```lua
item:Usable(unit) -- params are optional
```

## UseAoE

> Uses an AoE item at given object or coords

```lua
-- accepts 3d coords
spell:UseAoE(x,y,z)

-- or awful object
spell:UseAoE(unit)
```

- Uses directly at the coords [of the unit] given
- Generally makes no modifications to the coords or anything, just does a direct cast -> click operation as smoothly as possible.
  - Some exceptions rarely apply when passing awful object, like minor bug fixes baked in for some weirdly positioned PvE bosses

```lua
if resonator:UseAoE(target) then
  alert("nice!")
end
```

----
pages/item_objects/item-objects.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they?

> They are another special type of **awful object**, which provide a powerful, customized, object-oriented set of tools **for each item** your routine will be using.
>
> You create them by providing a item ID.

## Making them

### awful.Item

```lua
awful.Item(itemID) -> Item Object
```

### Example

```lua
local healthstone = awful.Item(5512)
```

Now `healthstone` contains all attributes, methods, and functions of a **item object**. We can use it to gather all kinds of information about Healthstone, as well as actually use it and set up various conditions for using it. Most relevant information about the item is gathered from WoW's API by the item object, and presented in the form of **attributes**.

## More about them

### Benefits of Item Objects

> Many complex calculations and actions are handled effortlessly by **Item Objects**. Healthstone is a super basic example, but the `:Use` method of `healthstone` will already make sure the item is off cooldown, we have it equipped or in our bags and more before using it.
>
> Item objects allow you to modularize code related to the item into neat little packages within itself. It makes for a fantastic organization method (code related to each item is within its own item object - your actor becomes an easy to digest item-related stack of priorities, as routines should be!), and a major performance benefit (code related to items only runs when the underlying item is ready to be cast)!

## Populating the Actor

> You can use **[awful.Populate](../awful_toolbox/toolbox_tools?#Populate)** to make a list of item objects available to your routine actor and the scope of your SpellBook file.
>
> ```lua
> local actor = project.hunter.survival
> local items = {
>   healthstone = awful.Item(5512),
> }
> -- populate actor and scope of this file
> awful.Populate(items, actor, getfenv(1))
> ```
>
> It's important to use Populate if you want to access your item objects without creating additional local references to them or storing and grabbing them from tables.
>
> [Read more about populate here](../awful_toolbox/toolbox_tools?#Populate)

### [Item Object Methods](item-object-methods)

----
pages/item_objects/meta.en-US.json
{
    "item-objects": "Creation",
    "item-object-methods": "Methods",
    "item-object-attributes": "Attributes"
}
----
pages/item_objects/item-object-attributes.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they?
> They function the same as [awful object attributes](object-attributes), so it's worth reading up on those if you haven't.
>
> Item Objects have many attributes of their own though, usually relating to some **information about the item**, most of which is derived from the **Item ID** using WoW API, which is why you must provide it instead of a item name. Here are some examples of item attributes:
>
> **item.cd** - Current cooldown of the item
>
> **item.name** - Name of the item
>
> **item.range** - Max range of the item
>
> Simple and effective :)

# Item Attributes

## castTime

> The cast time of the item (0 for instant cast items)

```lua
if target.magicImmunityRemains < item.castTime then
  item:Cast(target)
end
```

## cd

> Current cooldown of the item

- `cooldown` is another valid key for the same attribute

```lua
if item.cd > 10 then
  print("dang it, gonna be a while before i can use this one")
end
```

## id

> The ItemID of the item. You provided it we're just giving it back.

## equipped

> Whether or not we have the item equipped, which can be surprisingly annoying to find out.

```lua
if not item.equipped then
  print("check your bags, did you forget to equip your weapon again homie?")
end
```

## numequipped

> How many of the provided items you have equipped, such as tier pieces.

```lua
local tierPieces = awful.Item({1234, 5678, 9012, 3456})
if item.numequipped < 2 then
  print("you don't even have 2-set, what are you doing?")
end
```

## count

> How many of the item we have, such as amount of Health Potions

```lua
if item.count < 10 then
  print("better hit the auction house, you're running low on pots!")
end
```

## name

> The proper name of the item

```lua
awful.alert(item.name)
```

## range

> The max range the item can be cast at

```lua
if target.dist < item.range then
  awful.alert('hallelujah')
end
```

## usable

> Whether or not the item is currently "usable"

```lua
if item.usable then
  item:Use()
end
```


----
pages/lua_tutorials/meta.en-US.json
{
    "lua-tutorials-intro-to-metatables": "Intro to Metatables"
}
----
pages/lua_tutorials/lua-tutorials-intro-to-metatables.en-US.mdx
# Intro to Metatables

> **Time Estimate:** 5-10 mins.

## What is a "metatable"?

At the most basic level: a table!

The "metatable" of a table is a pointer<sup>1</sup> which tells the Lua interpreter where to find answers when a metamethod (we'll get to these later, stay with me here!) is invoked.

*<sup>1</sup> - A pointer is a reference to something else, typically in programming a pointer references the memory address for something like a variable.*

## How do metatables magically appear?

Metatables are set and retrieved via two functions: `setmetatable` and `getmetatable`.

Let's do a quick real-world test:
```lua
-- declare a generic table
local x = { }
-- set a value
x.a = 1
-- assign a metatable for table x, pointing to table x
setmetatable( x, x )
-- query and dump the metatable
DevTools_Dump( getmetatable( x ) )
```

You'll find the result of the `DevTools_Dump` command looks like this:
```lua
a = 1
```

Because remember, the metatable is only a pointer telling the interpreter **where metamethods live**!

#### So what happened here?

We created table `x`. We told the interpreter the location of the metatable for table `x` is **ALSO** table `x`!

#### Hang on, my brain hurts.. Why would we tell the interpreter the metatable for a table is.. the table?

That brings us to the second part I referenced above, and soon all shall be revealed. Feel free to take a break, apply some ice to your forehead and continue on when you're ready!

## What is a "metamethod"?

Metamethods are the second half of the puzzle that allow metatables to make sense! Similarly to the metatable, at their most basic levels metamethods are just that: methods<sup>1</sup>!

We mentioned in the previous section setting a metatable tells the Lua interpreter where to find answers when a metamethod is invoked. But, what does this mean?

There are a number of "metamethods" known to the Lua interpreter. Some of the more popular of these metamethods include: `__index`, `__call` and `__newindex`.

*<sup>1</sup> - Methods are very similar to functions, the key difference being methods receive their parent object as the first arg!*

## Let's start with __call!

Have you ever made a table where it could be useful to be able to use it like a function? This is exactly what `__call` provides!

So how do we implement `__call`? It's as simple as creating any other function within a table. Let's work through a quick example:
```lua
-- declare a generic table
local x = { }
-- assign an arbitrary value
x.a = 2
-- let's assign __call to multiply a by a value we provide
x.__call = function( self, multiple )
    -- set a default value for multiple
    multiple = multiple or 1
    -- multiply by our arg and print!
    print( self.a * multiple )
end
-- invoke it!
x()
```

Hmm.. I've done the things, and invoked my `x` table like `x()` but nothing happened... what gives? Remember your training young padawan! In the previous section we mentioned the purpose of `setmetatable`, which is to tell the interpreter where to find answers when a metamethod is invoked! So let's take another crack at it...
```lua
-- declare a generic table
local x = { }
-- assign an arbitrary value
x.a = 2
-- let's assign __call to multiply a by a value we provide
x.__call = function( self, multiple )
    -- set a default value for multiple
    multiple = multiple or 1
    -- multiply by our arg and print!
    print( self.a * multiple )
end
-- set the metatable!
setmetatable( x, x )
-- invoke it!
x()
x( 2 )
```

Oh boy it did stuff! We now see output like this:
```lua
2
4
```

#### OK, help me understand what just happened...

Well let's recap!

**Remember: the Lua interpreter is built to go looking for answers in the metatable when certain things are done with tables.**

Following our above `__call` exercise, if you take a table and slap some parenthesis on the end of it `()` - this tells the interpreter to go looking **in the metatable for a `__call` method**!

**So taking it from the top:**
* We created table `x`
* We told the interpreter, "Hey, there's a metatable for `x` here!", by using `setmetatable( x, x )`
* We created metamethod `__call` by simply doing `x.__call = function( self, multiple ) -- doStuffHere end`
* We invoked `x` like a method `x()`, indicating to the Lua interpreter to check the metatable (`getmetatable(x)`) for a `__call` method
* Since we set our metatable - `x.__call()` is invoked and voila!

Now go let your head cool off, pat yourself on the back and stay tuned for more on the other metamethods shortly!

----
pages/namespaces_and_loading/namespaces.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What's a namespace?

- Namespaces act as a modular, self-contained scope (**table**) to the identifiers (**variables**) inside of it. These identifiers can represent **functions**, **tables**, or **any other value type** in Lua. They are essentially **modules** containing all of these things packaged together, in a way that will not interfere with anything outside of them.

> > ```lua
> > local Unlocker, awful, project = ...
> > ```
>
> - Example of **importing** the 3 common namespaces into your file. You are **destructuring** varargs (**...**) passed to your file, and **declaring upvalues** for each namespace within. Now you have access to all functions, tables, values, etc. made available by `awful`!
>
> **note:** This is just declaring local variables. You aren't restricted to what you name them. If you want to access awful API via `af` instead of `awful` for example, you can!

If you are familiar with web development, this is similar to **state management** often handled by libraries such as Redux, but a lot simpler to use.

## What's the purpose?

> - The **entire framework** is made available within **one table**.
>
> - Using namespaces ensures your code will **never** interfere with addons or other routines.
>
> - You can **organize your project however you see fit**, have as many folders and files as you want, and **easily maintain all of your own API / data** within your namespace.
>
> - When you make a change to your namespace in one file, it is **immediately reflected in all others**.

# Common Namespaces

## Unlocker

### The first namespace passed to each file by awful.

- On an unlocker like Tinkr, you'll find the unlocker's API within. On most others, like Daemonic, its only purpose is to inform you which unlocker is in use.

> The unlocker name is always available as a string in camelCase under `Unlocker.type`, so if you are doing anything unlocker-specific, outside of the scope of the framework, you can follow [best practices](#) by checking the unlocker type first to ensure compatibility across all platforms.

**`Unlocker.type : nil | "daemonic" | "unlockerName"`**

```lua
-- this is kinda pseudocode fyi
local Unlocker, awful, project = ...

-- adding some api to our project to run protected funcs on either unlocker
function project.runProtected(f, ...)
    -- run some protected function on tinkr
    if not Unlocker.type then
        local Eval = Unlocker.Util.Evaluator
        return Eval:CallProtectedFunction(f, ...)
    -- or do it differently on daemonic!
    elseif Unlocker.type == "daemonic" then
        return CallProtectedApi(f, ...)
    end
end

if Unlocker.type == "daemonic" then
    print("doing daemonic stuff!")
end
```

## awful

### The second namespace passed to each file by awful.

- #### Look!!! That's my namespace!!! ![pugpls](https://i.pinimg.com/originals/01/d9/a4/01d9a44af5aa852624b87f8f280f4942.gif ":size=40")

> I'm sure you've already caught on by now, but **all awful API referenced in these docs will be found within this namespace**.

```lua
local unlocc, awful, memes = ...

print(awful.hello)
```

## project

### The third namespace passed to each file by awful.

- #### This one is special compared to the others, and it's all yours!

> This namespace is made available to **your project, and only your project**. It allows you to **build your own extensive API**, **storing and accessing data of any type between all of your files**!

```lua
local Unlocker, awful, McDonalds = ...
local colors = awful.colors

function McDonalds.Print(str)
    print(colors.red .. "[McDonalds]:", colors.yellow .. str)
end

function McDonalds.CreateOrder(order)
    order.append({
        type = "Cheeseburger",
        lettuce = true,
        pickles = false,
    })
    McDonalds.Print("Your order is ready, sir.")
    return order
end
```

- Other awful projects get passed their own namespace and won't interfere with the one passed to your project if they're loaded simultaneously.

- If you set up multiple projects (requires new file in **awful/routines** folder) you will receive a separate namespace for each one.

----
pages/namespaces_and_loading/meta.en-US.json
{
    "loading": "Loading",
    "namespaces": "Namespaces"
}
----
pages/namespaces_and_loading/loading.en-US.mdx
This code includes two files, project.lua and fire.lua, which demonstrate a dependency relationship. project.lua defines a project.print function, and fire.lua calls it using project.print('Fire Mage Rotation Loaded'). Both files have access to the same project table, which is passed as a vararg by awful during loading.

--.../project/project.lua (Dependency)
local unlocker, awful, project = ...

project.print = function(str) print("(epicProject) >", str) end

--.../project/mage/fire.lua (Dependent)
local unlocker, awful, project = ...
local mage = project.mage

-- routine actor
local fire = mage.fire

-- if player is not mage, stops code below from running
if not fire.ready then return end

-- Fancy Load Print!
project.print('Fire Mage Rotation Loaded')

-- Defining an explicit load order is vital, and now mandatory for each awful project.
-- If you've encountered `No awful-config.json` or `Missing awful-config` errors, keep reading.
-- On load, awful immerses each file in an isolated bubble environment. Here are some things to note about it:
--   - Most protected API, by direct reference, is unlocked and/or modified to be consistent between all supported unlockers - default reads/writes are NOT from `_G (global)` environment.
--   - Each file is passed common namespaces as varargs to make use of our API and establish your own.

-- You should plan an appropriate order for your files to load based on dependencies and dependents.
-- Here is a quick example:

-- .../project/project.lua (Dependency)
local unlocker, awful, project = ...

project.print = function(str) print("(epicProject) >", str) end

-- This file declares `project.print` as a function when first loaded, setting up a function we can use to make prints from our project fancier!

-- .../project/mage/fire.lua (Dependent)
local unlocker, awful, project = ...
local mage = project.mage

-- routine actor
local fire = mage.fire

-- if player is not mage, stops code below from running
if not fire.ready then return end

-- Fancy Load Print!
project.print('Fire Mage Rotation Loaded')

-- The above file calls `project.print`, the function we declared above, to print an OnLoad message for the mage rotation, as long as the player is on a mage. Note we declared and are accessing it through our project's namespace.

-- In this example, it's clearly `project.lua`, but with a huge project containing many more files, it could become a much harder question to answer!
-- We first have to declare the `project.print` function (as well as the mage table, our routine actor, etc.) before using them in `fire.lua`. 
-- Now you have to consider this for your own personal project. Your variables, api, file structure, etc. will be unique to you, and the sooner you consider your explicit load order, the better.

-- Creating `awful-config.json`
-- Within your project's base directory ( awful/routines/project ), create a file named `awful-config.json`.
-- You can assign a load order within this file. It must have either the `.json` or `.jsonc` extension, or it will not be recognized.
-- The base directory is your project's base folder, ( awful/routines/project ). The load order is from top to bottom. Here is an example of the contents of a basic `awful-config` file:

{
    "load": [
        "awful.lua",
        "spells/mage/mage.lua",
        "spells/mage/fire.lua",
        "actors/mage/fire.lua"
    ]
}

-- Now the listed files will be loaded from top-bottom.
-- Important: If you don't list a file in your load order, it won't be loaded by Awful.
-- Remember to always come back and add new files to your load order when you create them.

-- First, any file being loaded by Awful will be of the `.lua` extension.
-- You can omit these redundant file extensions from your awful-config if you want. Leading/trailing slashes will also be ignored.

{
     "load": [
        "awful",
        "spells/mage/mage",
        "spells/mage/fire",
        "actors/mage/fire"
    ]
}


----
pages/awful_objects/static-objects.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they?

> They are another special type of **awful object**, which provide a powerful, customized, object-oriented set of tools **for each spell** your routine will be casting.
>
> You create them by providing a spell ID and traits (options) that apply to your spell. Then for the life of the spell object, all methods, attributes, etc. will act according to these traits.

## Making them

### awful.Spell

```lua
awful.Spell(spellID, traits) -> Spell Object
```

### Example

```lua
local mortalStrike = awful.Spell(12294, {damage = "physical"})
```

Now `mortalStrike` contains all attributes, methods, and functions of a **spell object**. We can use it to gather all kinds of information about Mortal Strike, as well as actually cast it and set up various conditions for casting it. Most relevant information about the spell is gathered from WoW's API by the spell object, and presented in the form of **attributes**. We also told it that it's a physical damage ability - so it will make no mistake about which immunities to avoid attacking into.

## More about them

### Traits

> There's a big ol' ever-growing list of spell object **traits** which tell the framework more about the spell behind the object, and how to use it. Many traits sole purpose is to avoid casting into immunities, and many define different rules for when it's possible to cast the spell.
>
> By default, `:Cast` won't even try to cast while you're in CC, but some abilities can be cast while in CC, like Divine Shield or Ice Block. So you pass `ignoreControl = true` to the options, and bam - it will cast it while in CC.
>
> ```lua
> -- may as well have it cancel channels too, if we're trying to cast it
> iceBlock = awful.Spell(123, {ignoreControl = true, ignoreChanneling = true})
> ```
>
> Same for moving and casting, while it does understand you can cast with things like Spiritwalker's Grace or Ice Floes - some spells can always be cast while moving, like scorch or steady shot.
>
> ```lua
> scorch = awful.Spell(123, {ignoreMoving = true})
> ```

### Benefits of Spell Objects

> Many complex calculations and actions are handled effortlessly by **Spell Objects**. Mortal Strike is a super basic example, but the `:Cast` method of `mortalStrike` will already make sure the spell is off cooldown, we have enough rage, we're in melee range of the target and facing them, they aren't immune to physical damage (even from something like evasion or die by the sword while the target is facing us,) and more before casting it.
>
> Spell objects allow you to modularize code related to the spell into neat little packages within itself. It makes for a fantastic organization method (code related to each spell is within its own spell object - your actor becomes an easy to digest spell-related stack of priorities, as routines should be!), and a major performance benefit (code related to spells only runs when the underlying spell is ready to be cast)!
>
> Spells with cast times already know to start `:Cast` perfectly timed as an immunity to it will fall, complex AoE positioning around corners and out of range is all handled automatically by `CastEdge`, and soooo much more.

## Populating the Actor

> You can use **[awful.Populate](../awful_toolbox/toolbox_tools#Populate)** to make a list of spell objects available to your routine actor and the scope of your SpellBook file.
>
> ```lua
> local actor = project.hunter.survival
> local spells = {
>   exhilaration = awful.Spell(1234, {heal = true}),
>   intimidation = awful.Spell(12345, {effect = "physical", stun = true}),
>   trap = project.hunter.trap, -- grabbing this from hunter file!
> }
> -- populate actor and scope of this file
> awful.Populate(spells, actor, getfenv(1))
> ```
>
> It's important to use Populate if you want to access your spell objects without creating additional local references to them or storing and grabbing them from tables.
>
> [Read more about populate here](../awful_toolbox/toolbox_tools?#Populate)

## Raw Spell List Example

```lua
local NS = awful.Spell
awful.Populate({

  -- static objects [not req. but tiny perf. increase and takes care of declaration where i use them]
  target = awful.target,
  focus = awful.focus,
  player = awful.player,
  healer = awful.healer,
  pet = awful.pet,
  enemyHealer = awful.enemyHealer,

  -- dmg
  kill = NS(53351, { damage = "physical", ranged = true, targeted = true }),
  barbed = NS(217200, { damage = "physical", ranged = true, targeted = true }),
  cobra = NS(193455, { damage = "physical", ranged = true, targeted = true }),
  flayed = NS(324149, { damage = "physical", ranged = true, targeted = true, bleed = true }),
  killCommand = NS(34026, { damage = "physical", targeted = true }),
  conc = NS(5116, { effect = "physical", ranged = true, targeted = true, slow = true }),

  -- cc
  trap = hunter.trap,
  tar = NS(187698, { effect = "magic", slow = true }),
  cs = NS(147362, { effect = "physical" }),

  -- offensive
  wrath = NS(19574),
  wild = NS(193530),
  tranq = NS(19801),
  bassy = NS(205691, { damage = "physical", targeted = true }),

  -- defensive
  feign = hunter.feign,
  freedom = NS(53271, { ignoreFacing = true, ignoreLoS = true, beneficial = true }),
  turtle = NS(186265),

  -- misc
  flare = NS(1543),
  mendPet = NS(136, { heal = true }),
  camo = hunter.camo,
  ros = hunter.ros,
  disengage = hunter.disengage,
  racials = {
    -- blood fury
    Orc = NS(20572),
    -- berserking
    Troll = NS(26297),
  },

}, bm, getfenv(1))
```

### [Spell Object Traits](spell-object-traits)

----
pages/awful_objects/objects.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

## What are they?

### Basically tables, containing attributes & functions

- Used to easily **get information** about and **interact with** units, players, and other objects in game.

> All **awful objects** with an **existing underlying unit** will have all of the expected [attributes](object-attributes) and [functions](object-functions) available for you to access.

> There are a number of [static awful objects](static-objects) always available for use for **common units** such as **target**, **focus**, etc. You can make an amazing routine using mostly these.

> [Object lists](object-lists) are **filtered arrays** of **awful objects** by type e.g, [[enemies](object-lists#enemies), [friends](object-lists#friends), [objects](object-lists#objects)] generated and returned each frame they are referenced.

> We access _attributes_ by referencing them, and _functions_ by calling them.

- This is an attribute: `target.hp`

- This is a function: `target.isUnit(focus)`

## Empty objects

> Empty objects are a shell, containing **only** [isUnit](object-functions#isUnit). They are only generated by functions which generate an object, such as these, when there is no relevant underlying object:
>
> - [`object.castTarget`](object-attributes#casttarget) : Returns the destination target of a spell cast as an awful object
>
> - [`object.target`](object-attributes#target) : Returns the object's target as an awful object

- Tip: This is so you can always **rule out** things like `object.castTarget.isUnit(healer)` without the need to redundantly check for existence of the castTarget.

## Existence checking

> You should always check if an object exists before attempting to query more information about it. **It makes no sense to check something like `object.distance < 15` if there is no object, right?** Plus, some functions may just be **entirely missing** from empty awful objects.

```lua
if target.enemy then
  -- now we know the target exists, and they're an enemy.
end

if target.combat then
  -- now we know the target exists, and they're in combat.
end

if not target.combat then
  -- ⚠️ We still don't know if the target exists or not, we only know that if they do exist, they aren't in combat. ⚠️
end

if target.hp < 30 then
  -- ⚠️ This will cause a "graceful failure" if the object doesn't exist. It won't break your routine, but the code here may or may not run, based on the comparison type. What we know for sure is it's not working as we intended! ⚠️
end
```

- Receiving **true** from most **bool attributes**, such as `object.enemy` or `object.friend` also **proves existence**, so you don't need to **redundantly** check existence in other ways if you are already doing it with another **"bool must have value"** statement.

> You **cannot** do math comparisons on nil (empty) values in Lua, and a lot of attributes return numbers.
>
> Most number attributes will return **default number values** when object **does not exist** to cause a **graceful failure** that can later be fixed with our debugging tools.
>
> You should **always** do a **proper existence check** anyways **before anything else**, otherwise your following logic is rendered **completely arbitrary**, and **things will not be running for the reason you intended**.

----
pages/awful_objects/Parse
import re
import json

# Initialize variables
data = {}
current_category = ""

# Regular expressions
category_regex = re.compile("^# (.+)")
attribute_regex = re.compile("^## (.+)")
code_regex = re.compile("\*\*`(.*?)`\*\*")
lua_regex = re.compile("```lua\n(.*?)```", re.DOTALL)

# Open and read file
with open("filename.txt", "r") as file:
    for line in file:
        # Check for new category
        category_match = category_regex.match(line)
        if category_match:
            current_category = category_match.group(1)
            data[current_category] = {}
        else:
            # Check for new attribute
            attribute_match = attribute_regex.match(line)
            if attribute_match:
                current_attribute = attribute_match.group(1)
                data[current_category][current_attribute] = {}
            else:
                # Get code and lua
                code_match = code_regex.search(line)
                lua_match = lua_regex.search(line)
                if code_match:
                    code = code_match.group(1)
                    data[current_category][current_attribute]["code"] = code
                elif lua_match:
                    lua = lua_match.group(1)
                    data[current_category][current_attribute]["lua"] = lua

# Output as JSON
with open("output.json", "w") as file:
    json.dump(data, file, indent=2)

----
pages/awful_objects/object-functions.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Introduction

## What's an object func?

> An object function is a **key** within an [awful object](objects.md) that must be **called** `call()` to return a **value**.
>
> Most functions are **non-case-sensitive** and many have **fallback aliases** for added intuitiveness factor.

## Function Performance

> Functions are **less performant** than **attributes**, as a lot of them cannot / do not use the same caching techniques, but they do not face the same limitations.

# General Functions

## canAttack

Checks if the unit can attack another unit

**`unit.canAttack(otherUnit) : true | false`**

```lua
if target.canAttack(focus) then
    print("My target unit and focus unit don't like each other!")
end
```

## isUnit

Compares the object with another to determine if they're the same.

**`unit.isUnit(otherUnit) : true | nil`**

```lua
if target.isUnit(focus) then
    print("My target unit and focus unit are the same!")
end
```

## friendOf

Checks if the unit is friends with another unit

**`unit.friendOf(otherUnit) : true | false`**

```lua
if target.friendOf(focus) then
    print("My target unit and focus unit are best friends!")
end
```

## hasTalent

Checks if the object has the given talent or PvP talent selected.

**`unit.hasTalent(talent) : true | false`**

> Note: Accepts talent name (non-case-sensitive string) or SpellID.

> Note: This _only_ works with members of your [`group`](/object-lists.md?id=group), including the `player` object.

```lua
print(healer.hasTalent("Ascendance")) -- checking if your friendly healer is spec'd into ascendance.
-- res: true

print(player.hasTalent("ring of frost")) -- is the player spec'd into ring of frost?
-- res: true

for _, member in ipairs(awful.group) do
    if member.class == "Shaman" and member.hasTalent(204336) then
        print("wow, our shaman has grounding totem!")
    end
end
```

# Buffs & Debuffs

#### **Note:**

> All buff/debuff functions accept SpellID **or** Spell Names, and _optionally_ any "[Awful Object](/what-are-units.md)" as the second arg, which will only return a value if your given object was the caster of the buff/debuff. Spell name queries are _non-case-sensitive_. `target.buff('combustion')` works fine. SpellID is generally the only assuredly accurate method though, as there are several buffs & debuffs in game with the same name.

> Be sure to check out what's available under the [Buffs & Debuffs](/object-attributes.md?id=buffs-amp-debuffs) section in _attributes_ before beginning your implementation.

## buff

Provides information about a specific buff on the object.

**`unit.buff(spell[,sourceObject]) : "buff", ... | nil`**

> Returns: Identical to [UnitBuff](https://wowwiki-archive.fandom.com/wiki/API_UnitBuff) - [1] string `name`, ... | `nil`

```lua
if enemy.buff("combustion") then
    print("Wowie, I'm gonna die!")
    iceBlock:Cast()
end
```

## buffRemains

The time remaining of a specific buff on the object.

**`unit.buffRemains(spell[,sourceObject]) : remains | 0`**

```lua
if ourHealer.buffRemains("avenger's wrath") > 10 then
    print("I feel very safe.")
end
```

## buffStacks

Number of stacks the object has of the given buff.

**`unit.buffStacks(spell[,sourceObject]) : stacks | 0`**

```lua
if player.buffStacks("Well Fed") > 1 then
    print("It's probably time to start counting our calories.")
end
```

## buffUptime

The amount of time that the given buff has been active on the object.

**`unit.buffUptime(spell[,sourceObject]) : uptime | 0`**

```lua
if hunterPet.buffUptime("frenzy") > 10 then
    print("That hunter has been keeping up his frenzy stacks for a while!")
end
```

## buffFrom

Query the object for a list of any active buffs matching the Spell IDs / Spell Names within the given array.

**`unit.buffFrom(spellList[,sourceObject]) : { buff, buff, ... } | nil`**

> Similar: [buffsFrom](object-functions#buffFrom) - Same as buffFrom, except it returns the **number** of buffs from the list that are active

```lua
if friend.buffFrom({980, 172, 1822}) then
    print("My friend is dotted! Oh no!")
end
```

## debuff

Provides information about a specific debuff on the object.

**`unit.debuff(spell[,sourceObject]) : "debuff", ... | nil`**

> Returns: Identical to [UnitDebuff](https://wowwiki-archive.fandom.com/wiki/API_UnitDebuff) - [1] string `name`, ... | `nil`

```lua
if ourHealer.debuff("freezing trap") then
    print("My mans is trapped")
end
```

## debuffRemains

The time remaining of a specific debuff on the object.

**`unit.debuffRemains(spell[,sourceObject]) : remains | 0`**

```lua
if target.debuffRemains("Kidney Shot") > 4 then
    print("time for a double wide surprise.")
end
```

## debuffStacks

Number of stacks the object has of the given debuff.

**`unit.debuffStacks(spell[,sourceObject]) : stacks | 0`**

```lua
if friend.debuffStacks(980) > 3
and decurse:Cast(friend) then
    return awful.alert("Decursed them stacks!", 475)
end
```

## debuffUptime

The amount of time that the given debuff has been active on the object.

**`unit.debuffUptime(spell[,sourceObject]) : uptime | 0`**

```lua
if player.debuffUptime("Agony") > 25 then
    decurse:Cast(player)
    print("Agony's been up for way too long, yo!")
end
```

## debuffFrom

Query the object for a list of any active buffs matching the Spell IDs / Spell Names within the given array.

**`unit.debuffFrom(spellList[,sourceObject]) : { debuff, debuff, ... } | nil`**

> Similar: [debuffsFrom](object-functions#debuffFrom) - Same as debuffFrom, except it returns the **number** of debuffs from the list that are active

```lua
local badStuff = {"Storm Bolt", 408, "Cheap Shot"}
if player.debuffFrom(badStuff) then
    if iceBlock:Cast() then
        return awful.alert("I've had enough of that.", 45438)
    end
end
```

# Spells / Casting

## cooldown

> Estimates the **cooldown of any Unit's spell** based on **combat log event tracking** happening in the background by the framework.

**`unit.cooldown(spellID | spellName) : cooldown | 0`**

```lua
-- most performant with spellIDs
if enemy.cooldown(22812) > 6 then
    if kidney:Cast(enemy) then
        awful.alert("Kidney Shot (No Skin)", kidney.id)
    end
end

-- works with spell names tho, non-case-sensitive
if enemy.cooldown("avenging wrath") > 30 then
    print("we're probably safe yo")
end
```

## used

> Checks if the Unit has cast the given spell in the past **x** seconds, based on **combat log event tracking** happening in the background by the framework.

**`unit.used(spellID | spellName[,durationSeconds]) : true | nil`**

```lua
-- most performant with spellIDs
if player.used(22812, 15) then
    print("we used barkskin in the last 15 sec")
end

-- works with spell names tho, non-case-sensitive
if enemy.used("avatar", 5) then
    print("They used avatar recently yo")
end
```

# Movement & Position

## distanceTo

Distance between the object and another object, accounting for combat reach and bounding radius.

**`unit.distanceTo(otherObject) : distance | 9999`**

> Sister Attribute: [distance](/object-attributes.md?id=distance) - checks distance from the player to the object

> Similar: [distanceToLiteral](object-functions#distanceTo) - same as distanceTo but ignores **combatReach**

```lua
if target.distanceTo(enemyHealer) > 40 then
    print("He's out of range of heals!")
    stormBolt:Cast(target)
end
```

## facing

Checks if the object is facing another object _[at a 180 degree angle by default - the required facing angle to cast spells]_. You can check a specific angle (in degrees) by passing it as the 2nd arg.

**`unit.facing(otherUnit[,angle]) : isFacing | false`**

> Sister Attribute: [`playerFacing[,Angle]`](/object-attributes.md?id=playerFacing-angle) - checks if the player is facing the object. e.g, `target.playerFacing` or `target.playerFacing45`

```lua
if player.facing(target) then
    print("I'm facing my target, so can cast normal spells that require facing!")
end

if player.facing(target, 45) then
    print("I'm facing my target at a <45 degree angle!")
end

-- shockwave example
local bin = awful.bin
local angle = 45 + bin(30, player.hasTalent("big shockwave")) -- 75 degree angle with big shockwave!
local caught = 0
for _, enemy in ipairs(awful.enemies) do
    caught = caught + bin(enemy.distance < 8 and player.facing(enemy, shockwaveAngle))
end
if caught >= 3 then
    shockwave:Cast()
end
```

## facingPosition

Same as **facing**, but you pass it an **x, y, z position**.

```lua
local x,y,z = example.position()
if enemy.facingPosition(x,y,z,45) then
    print("nice!")
end
```

> From the example above: `awful.bin`

## losOf

Checks if the object and another object are in line of sight of each other.

**`unit.losOf(otherUnit) : isLoS | false`**

> Sister Attribute: [los](/object-attributes.md?id=los) - checks between _player_ & object

```lua
if not target.losOf(enemyHealer) then
    print("Target is out of LoS of his healer, kill!")
end
```

## losOfLiteral

Checks if the object is in line of sight of the given object, but ignores line of sight impairing effects like smoke bomb.

**`unit.losOfLiteral(otherUnit) : isLoS | false`**

> Sister Attribute: [losLiteral](/object-attributes.md?id=losLiteral) - checks between _player_ & object

```lua
if target.debuff("Smoke Bomb") then
    print(target.losOfLiteral(player))
end
-- true ... (ignores los-impairing effects!)
```

## position

Current 3D position of the object.

**`unit.position() : x, y, z | nil`**

```lua
local x, y, z = target.position()
print(x, y, z)
-- 2039.393103, 1083.11938, 81.9
```

## predictPosition

The object's estimated position after the given time, based on current velocity & moving direction.

**`unit.predictPosition(timeInSeconds) : x, y, z | curX, curY, curZ | nil`**

```lua
-- cast spell where linear prediction determines the object will be in 0.5s
local x,y,z = unit.predictPosition(0.5)
spell:AoECast(x,y,z)
```

## predictDistance

The object's estimated distance from the given Awful Object [or `player`] after the given time has elapsed. Based on current velocity and moving direction.

**`unit.predictPosition(timeInSeconds[,otherObject]) : dist | 9999`**

> Similar:
>
> **predictDistanceLiteral** - Same thing but ignores combat reach
>
> **predictDistanceToPosition** - Same thing but passed position (**x, y, z, elapsed**)

```lua
print(healer.predictDistance(0.5))
print(healer.distance)
-- 41
-- 38
```

## predictLoS

Estimates whether the object will be in line of sight of the given Awful Object [or `player`] after the given time has elapsed. Based on current velocity and moving direction.

**`unit.predictLoS(timeInSeconds[,otherObject]) : isLoS | false`**

```lua
print(target.predictLoS(0.5, enemyHealer))
print(target.losOf(enemyHealer))
-- false
-- true
-- ^ Random use case: Target is moving out of LoS of their healer, prepare a swap to them.
```

## meleeRangeOf

Checks if the unit is in melee range of another unit.

**`unit.meleeRangeOf(otherUnit) : true | false`**

> Sister Attribute: [`meleeRange`](/object-attributes.md?id=meleeRange) - Checks if the player is in melee range of the unit

```lua
if enemy.meleeRangeOf(healer) then
    print("oh no, he's connected to our healer!")
end
```

## movingToward

Checks if the unit is / has been moving toward another unit, given the angle and duration passed (if any) ...

**`unit.movingToward(otherUnit[,{ angle = degrees, duration = seconds }]) : true | false`**

> Both angle and duration are optional. You may check one or the other, or neither.
>
> Default duration is 0 (Immediately returns true when movement direction meets angle)
>
> Default angle is 30deg (Returns true when object is moving toward at an acute angle)

```lua
if player.movingToward(target) then
    print("Nice!")
end

if enemy.movingToward(player, { angle = 45 }) then
    print("he wants my asshole!")
end

if enemy.movingToward(healer, { angle = 90, duration = 0.5 }) then
    print("he wants my healer's pp real bad!")
end
```

## movingAwayFrom

Checks if the unit is / has been moving away from another unit, given the angle and duration passed (if any) ...

**`unit.movingAwayFrom(otherUnit[,{ angle = degrees, duration = seconds }]) : true | false`**

> Both angle and duration are optional. You may check one or the other, or neither.
>
> Pass angle inverse of movingToward, **more obtuse = more acute to opposing direction**
>
> Default duration is 0 (Immediately returns true when movement direction meets angle)
>
> Default angle is 220deg (Returns true when object is moving away from at a 140deg angle)

```lua
if player.movingAwayFrom(enemy, { angle = 300, duration = 0.25 }) then
    print("it looks like we're really fleeing from this guy")
end

if enemy.movingAwayFrom(player, { angle = 320 }) then
    print("they are running from me lolz")
end

if healer.movingAwayFrom(enemy, { angle = 280, duration = 0.5 }) then
    print("my healer is trying to kite them, maybe I should root them or somethin idk")
end
```

# Actions

## face

Faces the unit or angle as given

**`unit.face() : void`**

**`player.face(unit) : void`**

**`player.face(angle) : void`**

```lua
local exampleAngle = getExampleAngle
player.face(exampleAngle)

if enemy.stupid then
    enemy.face() -- this works
    player.face(enemy) -- this also works
end
```

## setFocus

Sets the unit as focus

**`unit.setFocus() : void`**

## setTarget

Sets the unit as target

**`unit.setTarget() : void`**

----
pages/awful_objects/meta.en-US.json
{
    "objects": "Objects",
    "static-objects": "Static Objects",
    "object-lists": "Object Lists",
    "object-attributes": "Object Attributes",
    "object-functions": "Object Functions",
    "player-attributes": "Player Attributes"
}
----
pages/awful_objects/player-attributes.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# These are attributes & functions only available to the `player` object.

## covenant

Your chosen covenant as a string.

**`player.covenant : "covenant" | nil`**

```lua
local player = awful.player
print(player.covenant)
-- res: "Night Fae"
```

## specialization

Your specialization index (number value)

**`player.specialization : specID`**

```lua
print(player.spec)
-- res: 2
```

## falling

Returns true if the player is in the air.

**`player.falling : falling | false`**

```lua
if player.falling then
    print("we're gonna die!")
end
```

## hasConduit

Checks if the player has conduit by given name or ID

**`player.hasConduit("conduit name" | conduitSpellID) : true | false`**

## hasTalent

Checks if the object is spec'd into the given talent or PvP talent.

**`player.hasTalent(talent) : true | false`**

> Note: Accepts talent name (non-case-sensitive string) or SpellID.

> Note: This also works with members of your [group](/object-lists.md?id=group). Putting it here for more visibility. More about it [here](/object-functions.md?id=hasTalent).

```lua
print(player.hasTalent(113724)) -- ring of frost spellID
-- res: true

print(player.hasTalent("ring of frost")) -- ring of frost string!
-- res: true

print(Player.HasTalent("Ring of Frost")) -- PascalCase & proper case-sensitivity
-- res: true
```

## mainHandEnchant

Checks if the player has enchant on mainhand (hello enhance players)

**`player.mainHandEnchant : true | false`**

> Similar: **mainHandEnchantRemains** : returns remaining duration of mh enchant

## offHandEnchant

Checks if the player has enchant on offhand

**`player.offHandEnchant : true | false`**

> Similar: **offHandEnchantRemains** : returns remaining duration of oh enchant

## mounted

Checks if the player is mounted

**`player.mounted : true | false`**

## timeStandingStill

Returns the amount of time the player has been standing still

**`player.timeStandingStill : tss | 0`**

----
pages/awful_objects/object-lists.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Awful Object Lists

## What are they?

> They're arrays containing awful objects and their expected [attributes](object-attributes) and [functions](object-functions). Each list is filtered based on its own specific criteria, which is described below for each one.

### Performance

> Most lists remain completely empty until referenced. They're generated on-demand by filtering the unlocker's OM, then cached for constant time re-reads the rest of the current 'tick'.

### Freshness

> You must always grab lists fresh where you need them. You can't declare them locally outside of your working scope like you can [static objects](static-objects), or they'll become stale. Generally you'll wanna access them directly from the awful namespace, e.g, `awful.enemies`. Though there are a couple of exceptions where they can be accessed by name directly (like `enemies` or `group`), namely inside the **Routine Actor** and your Spell Object **:Callback** functions.

### Magic Methods

> Object Lists contain several **Magic Methods** which allow you to do powerful searches, iterations, and more with the objects contained. Some examples:
>
> [around](object-lists#around): finds objects from the list that are within a given distance of another awful object
>
> [loop](object-lists#loop): an iteration alternative to standard **for loops**, allowing you to mimic and reap the benefits of a [**continue statement**](https://www.geeksforgeeks.org/continue-statement-cpp/) to write cleaner, significantly less indented ~~spaghetti~~ code.
>
> We go over the rest at the end of this section, so stick around.

# Lists

## group

Other players in our party or raid, **not including the player**.

> See also:
>
> **fgroup** | Players in our group, **including the player**. (fullGroup)

- **Everywhere:** Players in your party or raid.

```lua
-- mega basic rejuv
rejuvenation:Callback(function(spell)
    group.loop(function(friend)
        -- guard clause for members who already have rejuv.. ('continue' to next unit in the loop)
        if friend.buffRemains(spell.id, player) > 4 then return end
        -- otherwise rejuv & break loop on successful cast
        return spell:Cast(friend)
    end)
end)
```

## enemies

Contains enemy units that are relevant to the player's current environment.

- **Arena / Battleground:** Enemy players only.

- **PvE / Open World:** Enemy **units** that are in combat or targeting a player, or enemy **players** that are targeting or attacking the player.

- **Table Population:** In regard to the enemy table being populated. Keep in mind that unless explicitly stated otherwise, special mobs like Shades, and other things will be included in the enemies table. There are also specific mobs in certain instances that never have a combat status nor target a player, such as the Phase 2 images of Mueh'zala, these will also be populated in the table.

- **Excluded from enemies:** Explosives, can be found in awful.explosives.

```lua
-- basic corruption maintenance on enemies
awful.enemies.loop(function(enemy)
    -- dbr = debuffRemains
    if enemy.dbr(corruption.id, 'player') < 3 then return end
    return corruption:Cast(enemy)
end)
```

## friends

Contains all friendly players within render distance.

- **Everywhere:** All friendly **players**, even ones that are not in your group. This can be a needlessly large list in the open world.

```lua
-- casting some heal on any friend below 40%
awful.friends.loop(function(friend)
    if not friend.combat or friend.hp > 40 then return end
    return spell:Cast(friend)
end)
```

## dead

Contains all targetable dead units within render distance.

- **Everywhere:** All targetable valid dead units.

```lua
-- casting a battle res on a dead friend
awful.dead.loop(function(unit)
    if not unit.friend then return end
    if not unit.player then return end
    return spell:Cast(unit)
end)
```

## totems

Contains enemy totems or related objects (PvP).

> **tip:** the list of totems and corresponding object IDs that populate this list can be found in `lists.lua` near awful loader.

- **Everywhere:** All enemy totems, psyfiend, banner, etc. Totems flagged as dead or despawning but still visible are automatically filtered out.

```lua
awful.totems.stomp(function(totem, uptime)
    -- only totems that have been up for 300+ms
    if uptime < 0.3 then return end
    -- stomp 'em
    return spell:Cast(totem)
end)
```

## seeds

Contains only living night fae wildseed units.

- **Everywhere:** Living night fae wildseed units. Both friendly and enemy seeds are in the list.

```lua
-- some auto enemy wildseed targeting and stomping!
awful.seeds.track(function(seed, uptime)
    -- only enemy seeds
    if not seed.enemy then return end
    -- auto target it
    if not seed.isUnit(target) then seed.setTarget() end
    -- only seeds that have been up for 300+ms
    if uptime < 0.3 then return end
    -- stomp 'em
    return spell:Cast(seed)
end)
```

## explosives

Contains M+ affix explosive objects, doesn't appear in awful.enemies.

```lua
someSpell:Callback("kill explosives", function(spell)
    awful.explosives.loop(function(explosive)
        return spell:Cast(explosive)
    end)
end)
```

## shades

Contains M+ affix spiteful shade objects

```lua
someSpell:Callback("kill shades", function(spell)
    awful.shades.loop(function(shade)
        return spell:Cast(shade)
    end)
end)
```

## objects

Contains all objects of type `GameObject`

```lua
-- interact with soulwell
awful.objects.loop(function(obj)
    if obj.name == "Soulwell" then
        obj:Interact()
    end
end)

-- find some quest object
local quest_object_id = 123
awful.objects.loop(function(obj)
    if obj.id == quest_object_id then
        print("we found our thingy!")
        return true -- breaks the loop
    end
end)
```

## triggers

Contains all objects of type **area trigger**, both freindly and enemy.

```lua
-- find enemy freezing trap (idk if the ID is correct lol)
awful.triggers.loop(function(trigger)
    -- continue to next object if this one isn't freezing trap
    if trigger.id ~= 187650 then return end

    -- do stuff with the trap
    local x,y,z = trigger.position()
    draw:Circle(x,y,z,2.5)
end)
```

## units

Contains all objects of type **unit**, both friendly and enemy.

- **Everywhere:** All objects of type **unit**.

## pets

Contains all units that classify as another player's pet, both friendly and enemy.

- **Everywhere:** All units that are pets, based on on WoW's `UnitIsOtherPlayersPet`

## players

Contains all objects of type **ActivePlayer**, both friendly and enemy.

## allEnemies

Contains all enemy units/players regardless of their combat status, excluding Charmed enemies such as Mindcontrolled group members.

```lua
-- check if we're gonna end up pulling any mobs with our aoe before casting it
local ooc = function(obj) return not obj.combat end
aoeSpell:Callback(function(spell)
    -- guard clause for casting the spell when it would pull any OOC enemies
    if allEnemies.around(player, 10, ooc) > 0 then return end
    return spell:Cast()
end)
```

## wwClones

Contains windwalker clones (from storm, earth, and fire)

## tyrants

Contains Demonic Tyrants summoned by warlocks

## rocks

Contains enemy Earth Elementals (dwayne [the rock] johnson(s))

```lua
-- haha now you have to type awful rocks :P haha xd
awful.rocks.loop(function(rock)
    if rock.target.isUnit(player) then
        print("there's a fucking rock attacking me bruv")
    end
end)
```

## imps

Contains enemy warlock imp pets [mainly added so i could just check `#awful.imps` to know if lock was playing imp]

# **Magic Methods**

> They're available for **every single awful object list**
>
> You can make them available to a normal indexed array with `awful.immerseOL(table)`
>
> All **Magic Methods** return `true`, so you can weave them into conditional statements if you want

## around

> Find the number of units around a unit or position
>
> You can set specific criteria (filter) they must meet to be added to the count
>
> Receive a new list back with the units around

```lua
local count, total, objects = list.around(unit, distance, criteria)
```

- Parameters:
  - 1. **unit** (awful object) or **position** `{x, y, z}`: **required** - the unit or position to check that objects in the list are around
  - 2. **distance**: `{number}`, **required** - the distance from the given unit/position an object must be within to be added to the count
  - 3. **criteria**: `{function}`, optional - criteria to check against each object before adding them to the **main count** and **objects list**
- Returns:
  - 1. **main count**: `{number}`, the number of units around who met the criteria (if any)
  - 2. **total count**: `{number}`, the total number of units around, even if they did not meet the criteria
  - 3. **objects**: `{array}`, array of objects that were around and met criteria (if any)

### Examples:

```lua
-- checking breakable cc around me for brutal slash
local bcc, bsCount = enemies.around(player, 8, function(obj) return obj.bcc end)
-- now we know 0 bcc is around, but we also hit 2 targets with bslash
if bcc == 0 and bsCount >= 2 then
    brutalSlash:Cast()
end

-- checking breakable cc around expected stomp position
local bcc = function(obj) return obj.bcc end

local stompPos = function(unit)
  if not pet.exists then return unit.position() end
  local px,py,pz = pet.position()
  if pet.rooted then return px, py, pz end
  local x,y,z = unit.position()
  local angle = awful.AnglesBetween(x,y,z,px,py,pz)
  local reach = unit.combatReach
  return x + reach * cos(angle), y + reach * sin(angle), z
end

local sx, sy, sz = stompPos(target)
if enemies.around({sx, sy, sz}, 12, bcc) == 0 then
    barbedShot:Cast(target)
end
```

## filter

> The filter method creates a new object list containing all objects that pass the test implemented by the provided function.

```lua
local enemyMelee = enemies.filter(function(obj)
    return obj.role == "melee"
end)

enemyMelee.loop(function(obj)
    -- do stuff with melee objects
end)
```

## loop

> Iterate the object list, calling the provided function for each object.
>
> When any truthy value is returned, the loop will break.

- Function is passed 3 arguments:
  - 1. **object** each object in the list
  - 2. **index** current index in the iteration
  - 3. **uptime** the amount of time since we first iterated over this object (**0** for new object detections)
- Arguably better solution than **for loops** for iterating object lists.
  - Allows you to mimic the functionality of a [continue statement](https://www.geeksforgeeks.org/continue-statement-cpp/), an invaluable tool not available to Lua, by simply closing eliminating conditions with a **return**
  - This (on average) reduces code spaghetti by a breathtaking 42.069%.
  - Fr tho, way less indenting. Check the example below.
- Check [best practices - spaghettification](#) for more tips on reducing spaghetti-sauce in your code.
- `forEach` is a valid alternate key for the same method

```lua
-- this code is already over-indented, but it can get way crazier real fast
for _, unit in ipairs(list) do
    if unit.enemy and unit.distance < 40 then
        if not unit.immuneMagic and unit.los then
            if not unit.isUnit(target) then
                if hotStreak or combustion then
                    if not unit.facing(player) then
                        if helpMePlease then
                            if spell:Cast(unit) then
                                awful.alert("Casted the thing!", spell.id)
                                break
                            end
                        end
                    end
                end
            end
        end
    end
end

-- this accomplishes the exact same thing, it's just way easier to work with
-- ...for both the eyes and fingies when we need to move things around!
list.loop(function(unit)
    if not hotStreak and not combustion then return end
    if not unit.enemy or unit.distance > 40 or not unit.los then return end
    if unit.immuneMagic or unit.isUnit(target) then return end
    if unit.facing(player) or not helpMePlease then return end
    return spell:Cast(unit) and awful.alert("Casted the thing!", spell.id)
end)

-- also, has those neat extra features baked in for free :)
awful.friends.loop(function(unit, i, uptime)
    if uptime == 0 then
        print("New unit detected: " .. unit.name .. ", they're a " .. unit.classString)
    end
end)
```

![lol](/img/lol.png)

## stomp

> Very similar to `loop`, but with a couple of key differences:
>
> 1.) Eliminates totems that are stuck at 1 hp or in the process of despawning from the iteration.
>
> 2.) Does not give back `index` - only `object` and `uptime`

```lua
awful.totems.stomp(function(totem, uptime)
    if uptime < 0.25 then return end
    if totem.hp < spell.damage * 2 then
        spell:Cast(totem) -- noice!
    end
end)
```

## sort

> Sorts the list using basic Lua `table.sort`
>
> Not very performant, should be used sparingly.

```lua
awful.enemies.sort(function(x,y) return x.hp < y.hp end)
```

----
pages/awful_objects/object-attributes.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Introduction

## What's an attribute?

> An attribute is just a **key** within an [awful object](objects) that is expected to return some **value**.
>
> All attributes are **non-case-sensitive** and many have **fallback aliases** for added intuitiveness factor.
>
> This means that `object.enemy` will work, but so will `object.Enemy` and `object.isEnemy`

### Attribute Performance

> Reading attributes is **extremely performant**. They act as normal **key-value pairs**, but their value is **generated** upon **reference of the key**, and the **value** is **cached** until the next **"tick"**.

### Multi-returns

> A few attributes have **multiple returns** that are only available by **selection #**, which you can add to the **end of the key**. Example, `player.casting9` **==** `select(9,UnitCastingInfo("player"))`. You can also use **bracket notation** to access them variably `player["casting" .. selection]`. It will be documented where all it is possible to use **multi-return selection** on attributes.

# General Attributes

## exists

**`unit.exists : true | nil`**

```lua
print(target.exists)
-- true
```

## absorbs

number: **`unit.absorbs : absorbs | 0`**

Amount of absorption (shields) remaining on the object

```lua
if execute.damage > target.health + target.absorbs then
    print("they gon die")
end
```

#### Limitations:

- Not available in Classic due to lack of supported WoW API to fetch asborbs.

## class

The class of the object, plus all returns of `UnitClass` available by multi-selection #

**`unit.class : "class" | nil`**

**`unit.class2 : "CLASS" | nil`**

> Alternative: `classLiteral` - 2nd return from `UnitClass`. Upper case class name which is always the same across different localizations. Equivalent of `class2`.

```lua
if target.class == "Rogue" then
    print("oh boy, it's a rogue!")
elseif target.class2 == "MAGE" then
    print("wow, it's a mage!")
end
```

## combat

Returns true if the unit is in combat

**`unit.combat : isInCombat | nil`**

```lua
if not enemy.combat then
    sap:Cast(enemy)
end
```

## covenant

The unit's covenant

**`unit.covenant : "Covenant Name" | nil`**

```lua
target.covenant
```

## dead

Returns true if the unit is dead

**`unit.dead : isDead | false`**

```lua
if player.dead then
    awful.alert("rez pls")
end
```

## enemy

Returns true if the unit is an enemy

**`unit.enemy : isEnemy | false`**

```lua
if target.enemy then
    -- do things to your enemy target!
end
```

## friend / friendly

Returns true if the unit is friendly

**`unit.friend : isFriend | false`**

```lua
if target.friendly then
    -- do things to your friendly target!
end
```

## guid

The GUID of the object

**`unit.guid : "UnitGUID" | nil`**

## hp

The health of the object in percentage

**`unit.hp : hpPercentage | 0`**

```lua
print(target.hp)
-- 69
```

> Other HP attributes:
>
> **health** - actual current health of object, as returned from `UnitHealth` (instead of percentage)
>
> **healthMax** - max health of object, as returned from `UnitHealthMax`

## height

The height of the object.

**`unit.height : height | nil`**

```lua
print(target.height)
-- 2.29
```

## id

Returns ObjectID of the object

**`unit.id : ObjectID | false`**

- not for use on players

```lua
if object.id == 123 then
    print("found the object/unit i was lookin for")
end
```

## isHealer

Checks if the object is a healer.

- Bound by limitations of [role](object-attributes#role)

**`unit.healer : isHealer | false`**

```lua
if unit.healer then
    print("wow, it's a healer!")
end
```

## isMelee

Checks if the object is a melee dps.

- Bound by limitations of [role](object-attributes#role)

**`unit.melee : isMelee | false`**

```lua
if unit.melee then
    print("wow, it's a melee!")
end
```

## isPlayer

Checks if the object is a player.

**`unit.player : isPlayer | false`**

```lua
if unit.player then
    print("wow, it's a player!")
end
```

## isRanged

Checks if the object is a player.

- Bound by limitations of [role](object-attributes#role)

**`unit.isRanged : isRanged | false`**

```lua
if unit.ranged then
    print("wow, it's a ranged dps!")
end
```

## isPet

Checks if the object is another player's pet

**`unit.isPet : isPet | false`**

```lua
if unit.pet then
    print("wow, it's a pet!")
end
```

## level

The level of the object via `UnitLevel`

**`unit.level : level`**

```lua
if player.level < 60 then
    print("wow, i am leveling")
end
```

## name

The name of the object

**`unit.name : "name" | nil`**

```lua
print(target.name)
-- Awfulplayer
```

## pointer

> Pointer should be used when passing the object to any non-awful API, as any "normal" function like `UnitHealth` will not work with an awful object.

Uniquely identifying pointer to the object in memory. In the case of Tinkr, returns the associated "WoWGameObject". In the case of Daemonic, returns the unit's GUID.

**`unit.pointer : pointer | nil`**

```lua
print( UnitName(target.pointer) )
-- Awfulplayer, Awfulplayer
```

## race

> The race of the object (in game)

## role

> The role of the object, either `healer`, `melee`, `ranged`, `tank`, or `pet`.

**`unit.role : role | nil`**

```lua
if enemy.role == "healer" then
    kidneyShot:Cast(enemy)
end
```

**Limitations:**

- Built primarily for use **in arena or on party members**. For enemies in BG, World PVP, etc. or for friendly players who are not in your group, it is essentially guessing based on buffs, power resources, etc, and could be wrong sometimes.

## spec

> The unit's specialization as a string, acquired by [inspect](object-attributes#inspect), so it faces the same limitations (group/player only..)

**`unit.spec : "Spec" | "Unknown"`**

```lua
-- print spec when spec changes
awful.onEvent(function() print(player.spec) end, "PLAYER_SPECIALIZATION_CHANGED")

if player.spec == "Fire" then
    -- do fire stuff
end
```

## stealth

If the object has an active stealth or invisibility effect, returns the spellID of that effect. Otherwise returns false.

> Does not use a specific stealth list, but instead efficiently parses [`debuffDescriptions`](object-attributes#debuffDescriptions) - should work on _all_ current & future stealth/invis effects in the game. If it does not, let us know the specific stealth effect.

**`unit.stealthed : stealthID | false`**

```lua
if enemy.stealth and enemy.distance < 8 then
    if frostNova:Cast() then
        local stealthName = GetSpellInfo(enemy.stealth)
        awful.alert("Nova (".. stealthName ..")", 122)
    end
end
```

## target

The target of the object, returned as an [Awful Object](/what-are-units.md) with all attributes & functions available.

**`unit.target : targetObject | emptyObject`**

```lua
if enemy.target.isUnit(player) then
    barkskin:Cast()
end
```

## visible

Checks if the unit is visible (within render range) using `UnitIsVisible`

**`unit.visible : visible | false`**

## v2attackers()

The amount of attackers for the provided object and their combined cooldown usage.

**`unit.v2attackers() : totalUnits, meleeUnits, rangedUnits, cooldowns | 0, 0, 0, 0`**

```lua
local total, melee, ranged, cooldowns = player.v2attackers()
if melee > 1 and cooldowns > 0 then
    barkskin:Cast()
end
```

## threat

The player's threat for the unit, based on UnitThreatSituation.
https://wowpedia.fandom.com/wiki/API_UnitThreatSituation

**`unit.threat : status | -1`**

```lua
if target.threat < 2 then
    taunt:Cast()
end
```

## aggro

The player's aggro status for the unit, based on threat returning >= 2.

**`unit.aggro : aggro | false`**

```lua
if not target.aggro then
    taunt:Cast()
end
```

# Buffs & Debuffs

> There are also powerful [Buff & Debuff](/object-functions.md?id=buffs-amp-debuffs) related **functions**, rather than **attributes**... Be sure to check those out before beginning your implementation.

## buffCount

Returns number of buffs the unit has

**`unit.buffCount : numBuffs | 0`**

## buffs

Returns an array of all buffs the unit has. Each buff is indexed appropriately, and contains all [UnitBuff](https://wowwiki-archive.fandom.com/wiki/API_UnitBuff) returns.

**`unit.buffs : { { buffName, ... }, { buffName, ... }, ... } | {}`**

```lua
for i, buff in ipairs(player.buffs) do
    local name, rank, icon, count = unpack(buff)
    -- do stuff with cached, performant buffs!
end
```

## debuffCount

Returns number of debuffs the unit has

**`unit.debuffCount : numDebuffs | 0`**

## debuffs

Returns an array of all debuffs the unit has. Each debuff is indexed appropriately, and contains all [UnitDebuff](https://wowwiki-archive.fandom.com/wiki/API_UnitDebuff) returns.

**`unit.debuffs : { { debuffName, ... }, { debuffName, ... }, ... } | {}`**

```lua
for i, debuff in ipairs(player.debuffs) do
    local name, rank, icon, count = unpack(debuff)
    -- do stuff with cached, performant debuffs!
end
```

## buff[index]

Returns an array of all returns provided by [`UnitBuff`](https://wowwiki-archive.fandom.com/wiki/API_UnitBuff) on the object at the given index. You must provide a specific index to this attribute to get the appropriate return.

> Sister Function: [buff](/object-functions.md?id=buff) - Check the unit for a specific buff by spellID or spellName, and get back all the normal `UnitBuff` returns.

**`(unit.buff3 / unit["buff"..index]) : { buffName, ... } | nil`**

```lua
for i=1,#player.buffs do
    -- buffs by index
    local name, _, _, count = unpack(player['buff'..i])
    print(name, count)
end
```

## debuff[index]

Returns an array of all returns provided by [UnitDebuff](https://wowwiki-archive.fandom.com/wiki/API_UnitDebuff) on the object at the given index. You must provide a specific index to this attribute to get the appropriate return.

> Sister Function: [debuff](/object-functions.md?id=debuff) - Check the unit for a specific debuff by spellID or spellName, and get back all the normal `UnitDebuff` returns.

**`(unit.debuff3 / unit["debuff"..index]) : { debuffName, ... } | nil`**

```lua
for i=1,#player.debuffs do
    -- debuffs by index
    local name, _, _, count = unpack(player['debuff'..i])
    print(name, count)
end
```

## buffDescriptions

Returns an array of description text for each buff the unit has. Can parse these descriptions to determine whether or not the unit has an effect of a certain type, or get creative with it! The indexing of the descriptions correspond with their parent buffs, so you can use something like `unit["buff" .. index]` to obtain more info about the buff after finding a match by string.

**`unit.buffDescriptions : { "desc", "desc", ... } | {}`**

```lua
local desc = enemy.buffDescriptions
for i, str in ipairs(desc) do
    local lower = str:lower()
    if lower:match("stealthed") or lower:match("invisible") then
        print("That man is in stealth! Wow!")
        local thisStealth = enemy["buff" .. i]
        print("Stealth Name: " .. thisStealth[1])
        print("Stealth SpellID: " .. thisStealth[10])
    end
end
```

## debuffDescriptions

Returns an array of description text for each debuff the unit has. Can parse these descriptions to determine whether or not the unit has an effect of a certain type, or get creative with it! The indexing of the descriptions correspond with their parent debuffs, so you can use something like `unit["debuff" .. index]` to obtain more info about the debuff after finding a match by string.

**`unit.debuffDescriptions : { "desc", "desc", ... } | {}`**

```lua
local desc = player.debuffDescriptions
for index, str in ipairs(desc) do
    if str:match("Disarmed.") then
        print("We have no arms, wow!")
        local thisDisarm = player["debuff" .. index]
        print("Disarm Name: " .. thisDisarm[1])
        print("Disarm SpellID: " .. thisDisarm[10])
    end
end
```

## cds

Returns true if the object has offensive cooldowns up, false if not.

```lua
if obj.cds and obj.target.isUnit(player) then
    print("uh oh we in troub")
    turtle:Cast()
end
```

## charmed

Checks if the object is charmed (mind control, etc.) using `UnitIsCharmed`

**`unit.charmed : charmed | false`**

## dotted

Returns true if the target has an active damage over time effect, false if not.

> Does not use a specific DoT list, but instead efficiently parses [`debuffDescriptions`](object-attributes#debuffDescriptions) - should work on _all_ current & future DoT effects in the game. If it does not, let us know which DoT.

**`unit.dotted : hasDot | false`**

```lua
if not enemy.dotted and not enemy.combat then
    sap:Cast(enemy)
end
```

## purgeCount

Returns the number of purgeable buffs on the unit

**`unit.purgeCount : purgeCount | 0`**

```lua
if enemy.purgeCount > 0 then
    purge:Cast(enemy)
end
```

# Power / PowerTypes

## powerTypes

> First, know your **powerType** aliases (**these are non-case-sensitive**)

```lua
awful.powerTypes = {
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

## power attributes

> Now you can check **power**, **powerMax**, or **powerPct** for any powerType on the unit - just replace **power** with your **powerType alias.**

```lua
unit.power -- default UnitPower return w/ no powerType specified
unit.energy -- energy
unit.energyMax -- max energy
unit.mana -- mana
unit.manaPct -- mana percentage (mana / manaMax) * 100
unit.comboPoints -- combo points
unit.comboPointsMax -- max combo points
unit.cpMax -- also combo points
unit.rage -- current rage
unit.astralPowerMax -- you get it now ;) this works for all powerTypes..

if player.energy < 30 then
    -- pooling energy! blabla!
end

if player.cp >= 5 then
    kidney:Cast()
end
```

# Casts / Channels

## casting

If the object is casting, returns the Casting Info from [`UnitCastingInfo`](https://wowwiki-archive.fandom.com/wiki/API_UnitCastingInfo)

**`unit.casting : "castName" | nil`**

> Returns: string `spellName` | `nil` // Every return of [UnitCastingInfo](https://wowwiki-archive.fandom.com/wiki/API_UnitCastingInfo) can be queried with _selection #_ e.g, `unit.casting9`.

```lua
-- target is casting a polymorph...
print(target.casting)
-- "Polymorph"
print(target.casting9) -- same as target.castID
-- 118
print(target.casting8) -- same as target.castInt
-- nil
```

## castID

The spellID of the spell being cast by the object, if they are casting.

**`unit.castID : castID | nil`**

```lua
if player.castID == 118 and player.castRemains < awful.latency + awful.tickRate + 0.03 then
    blinkTo(player.castTarget)
end
```

## castTarget

If the object is casting, returns the [Awful Object](what-are-units.md) being targeted by that spellcast.

**`unit.castTarget : castTargetObject | nil`**

```lua
if enemy.cast == "Chaos Bolt" and enemy.castTarget.isUnit(player) and enemy.castTimeLeft < awful.buffer then
    blazingBarrier:Cast()
end
```

## castPct

Current percentage of total cast time completed

**`unit.castPct : castPctComplete | 0`**

```lua
-- 70% kick i guess lolz
if target.casting == "Polymorph" and target.castPct > 70 then
    kick:Cast(target)
end
```

## castRemains

The time remaining on the object's cast, minus latency.

**`unit.castRemains : castTimeLeft | 0`**

```lua
local target = awful.target

if target.casting == "Polymorph" and target.castRemains < 0.5 then
    kick:Cast(target)
end
```

## channeling

If the object is channeling, returns the Channeling Info from [`UnitChannelInfo`](https://wowpedia.fandom.com/wiki/API_UnitChannelInfo)

**`unit.channel : "channelName" | nil`**

> Returns: string `spellName` | `nil` // Every return of [UnitChannelInfo](https://wowpedia.fandom.com/wiki/API_UnitChannelInfo) can be queried with _selection #_ e.g, `unit.channel3`.

```lua
-- target is channeling soothing mist...
print(target.channel)
-- "Soothing Mist"
print(target.channel3) -- equivalent of the 3rd return of UnitChannelInfo, 'texture' - The texture path associated with the spell icon.
-- "Interface\\Icons\\Soothing-Mist-Icon.blp" < not accurate, just example
```

## channelID

The spellID of the spell being channeled by the object, if they are channeling.

**`unit.channelID : channelID | nil`**

```lua
if enemy.channel == "Lightning Lasso" then
    para:Cast(enemy)
end
```

## channelRemains

The time remaining on the object's channel, minus latency.

**`unit.channelRemains : channelTimeLeft | 0`**

```lua
if target.channel == "Soothing Mist" and target.channelRemains < 0.5 then
    print("The soothing mist is about to finish!")
end
```

## gcdRemains

Time until the unit's next GCD is available (if any)

- only works on **players** (duh)

**`unit.gcdRemains : gcdRemains | 0`**

```lua
if enemyHealer.gcdRemains > trapTravelTime then
    if trap:AoECast(enemyHealer) then
        awful.alert("haha gotcha on gcd idiot")
    end
end
```

> If you like this one, check out the [cooldown](object-functions#cooldown) function

## lastCast

Latest spellID cast by the object

- only works on **players** (duh)

> Similar: [recentlyCast](object-functions.md#recentlyCast) - Checks if unit has cast X spell in the past Y seconds

**`unit.lastCast : lastCast | nil`**

```lua
if enemy.lastCast == 118 then
    print("they just cast sheep!!!")
end
```

# Movement & Positioning

## distance

Distance between the player and the object, accounting for combat reach and bounding radius.

**`unit.distance : distance | 9999`**

> Sister Attribute: [distanceTo](/object-functions.md?id=distanceTo) - checks distance from the object to another object

> Similar: [`distanceLiteral`](/object-attributes.md?id=distanceLiteral) - checks distance without accounting for combat reach or bounding radius

```lua
if target.distance > 15 then
    charge:Cast(target)
end
```

## combatReach

The combat reach of the object

**`unit.combatReach : combatReach | 0`**

## boundingRadius

The bounding radius of the object

**`unit.boundingRadius : boundingRadius | 0`**

## playerFacing [,angle]

Checks if the player is facing the object _[at a 180 degree angle by default - the required facing angle to cast spells]_, however, you can check a specific angle by adding it to the referenced key.

**`unit.playerFacing : playerIsFacing | false`**

**`unit.playerFacing45 : facing45Degrees | false`**

> Sister Function: [`facing(otherUnit[,angle])`](/object-functions.md?id=facing) - checks if one object is facing _another_ object. e.g, `player.facing(target)` or `target.facing(player, 45)`

> Alternate Aliases: `amIFacing`, `iAmFacing`,

```lua
if target.playerFacing then
    print("I'm facing my target, so can cast normal spells that require facing!")
end

if target.playerFacing45 then
    print("I'm facing my target at a <45 degree angle!")
end

-- Passing a dynamic angle variable using the power of concatenation and bracket notation! Wow!
local shockwaveAngle = 45 + awful.bin(player.hasTalent("Double-Wide Shockwave Surprise")) * 30
local caught = 0
for _, enemy in ipairs(awful.enemies) do
    if enemy.distance < 8 and enemy["playerFacing" .. shockwaveAngle] then
        caught = caught + 1
    end
end
if caught >= 3 then
    shockwave:Cast()
end
```

## los

Checks if the object is in line of sight of the player.

**`unit.los : isLoS | false`**

> Sister Function: [`losOf(otherObject)`](/object-functions.md?id=losOf) - checks this between the object and _another_ object

> Similar: [`losLiteral`](/object-attributes.md?id=losLiteral) - checks LoS without accounting for LoS-impairing effects like smoke bomb.

```lua
if enemy.los then
    print("he can see me!")
end
```

## rotation

Facing direction (rotation) of the object in _radians_.

**`unit.rotation : facingDirection | nil`**

```lua
print(target.rotation)
-- 4.24520301...
```

## meleeRange

Returns true if the player is in melee range of the object.

**`unit.meleeRange : true | false`**

> Sister Function: [`meleeRangeOf(otherObject)`](/object-functions.md?id=meleeRangeOf) - Checks if object is in meleeRange of another object

```lua
if enemy.meleeRange then
    warbreaker:Cast()
end
```

## movementFlags

Movement flags of the object as an **integer**.

**`unit.movementFlags : flags | 0`**

```lua
-- convert integer flags to direction in radians
local flags = band(player.movementFlags, 0xF)
local dir = player.rotation
local mod = 0
if flags == 0x2 then
    mod = pi
elseif flags == 0x4 then
    mod = pi * 0.5
elseif flags == 0x8 then
    mod = pi * 1.5
elseif flags == bor(0x1, 0x4) then
    mod = pi * (1 / 8) * 2
elseif flags == bor(0x1, 0x8) then
    mod = pi * (7 / 8) * 2
elseif flags == bor(0x2, 0x4) then
    mod = pi * (3 / 8) * 2
elseif flags == bor(0x2, 0x8) then
    mod = pi * (5 / 8) * 2
end
return (dir + mod) % (pi * 2)
```

## moving

Checks if the unit is moving : ([speed](object-attributes#speed) > 0)

**`unit.moving : isMoving | false`**

```lua
if player.moving then
    print("hey, stop moving buddy")
end
```

## movingDirection

Moving direction of the object as an angle in **radians**.

**`unit.direction : movingDirection | nil`**

```lua
print(enemy.direction)
-- 3.189384
```

## speed

The current speed of the object (in yards per second), plus all other returns of `GetUnitSpeed` available by selection #, e.g `unit.speed2`

**`unit.speed : currentSpeed | 0`**

**`unit.speed2 : runSpeed | 0`**

```lua
if target.speed < player.speed then
    print("we are moving faster than our target!")
end

if target.speed2 < player.speed2 then
    print("we are able to move faster than our target!")
end
```

# Crowd Control

## bcc

If the object is in breakable crowd control, returns the spellID of that crowd control debuff.

- If there is more than one bCC effect active, the one with the longest remaining duration's ID is returned.

**`unit.bcc : ccDebuffID | nil`**

```lua
if target.bcc then
    print("i'm not gonna attack that, it's in breakable cc")
end
```

## bccRemains

The remaining time of breakable cc effects on the object.

**`unit.bccRemains : remains | 0`**

```lua
-- only cast spell if bcc remains < spell cast time
if target.bccRemains < spell.castTime then
    spell:Cast(target)
end
```

## cc

If the object is in crowd control, returns the spellID of that crowd control debuff.

- If there is more than one CC effect active, the one with the longest remaining duration's ID is returned.

**`unit.cc : ccDebuffID | nil`**

```lua
if healer.cc then
    print("our healer is in cc! help!")
end
```

## ccRemains

The remaining time of crowd control effects on the object.

**`unit.ccRemains : remains | 0`**

```lua
if enemyHealer.ccRemains < awful.buffer + awful.latency and enemyHealer.incapDR >= 0.25 then
    sheep:Cast(enemyHealer)
end
```

## ccInfo

Returns an array containing detailed info about the longest remaining crowd control effect on the object.

**`unit.ccInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

> note: The contents can't be accessed with selection #, it is a single array being returned

```lua
if healer.cc then
    local id, name, remains, drCat, source = unpack(healer.ccInfo)
    print(id, name, remains, drCat, source)
end
-- 118, "Polymorph", 3.839, "Incapacitate", SourceGUID
```

## disarmed

If the object is currently affected by a disarm, returns the spellID of that effect. Otherwise returns false.

> Does not use a specific disarm list, but instead efficiently parses [`debuffDescriptions`](object-attributes#debuffDescriptions) - should work on _all_ current & future disarm effects in the game. If it does not, let us know which disarm.

**`unit.disarm : disarmID | false`**

```lua
if player.disarm then
    print("wow, i ain't got no arms!")
end
```

1

## disorient

If the object is disoriented, returns the spellID of the disorient debuff.

**`unit.disorient : disorientID | nil`**

```lua
if player.disorient then
    print("wow, i'm disoriented!")
end
```

## disorientRemains

The remaining time of disorient CC effects on the object.

**`unit.disorientRemains : remains | 0`**

```lua
if player.disorientRemains > 3 then
    print("wow, i'm disoriented for a long time!")
end
```

## disorientInfo

If the object is affected by a disorient crowd control, returns the [ccInfo](/object-attributes.md?id=ccInfo) of the effect.

**`unit.disorientInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

```lua
local id, name, remains, drCat, source = healer.disorientInfo
if name == "Mind Control"
    and remains > gcd
    and purge:Cast(source)
or name == "Psychic Scream"
    and remains > gcd
    and tremor:Cast() then
        print("wow, we really took care of 'em!")
end
```

## incap

If the object is incapacitated, returns the spellID of the incapacitate debuff.

**`unit.incap : incapID | nil`**

```lua
-- player.incap is also valid, this is another alias (obj.incapacitated as well)
if player.incapped then
    print("wow, i'm incapacitated!")
end
```

## incapacitateRemains

The remaining time of incapacitate CC effects on the object.

**`unit.incapRemains : remains | 0`**

```lua
if player.incapRemains > 3 then
    print("wow, i'm incapacitated for a long time!")
end
```

## incapacitateInfo

If the object is affected by an incapacitate crowd control, returns the [ccInfo](/object-attributes.md?id=ccInfo) of the effect.

**`unit.incapacitateInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

```lua
if healer.incap then
    local id, name, remains, drCat, source = unpack(healer.incapacitateInfo)
    print(id, name, remains, drCat, source)
end
-- 118, "Polymorph", 3.839, "Incapacitate", SourceGUID
```

## slowed

If the object is slowed, returns the spellID of the slow debuff.

**`unit.slowed : slowID | nil`**

```lua
if player.slowed then
    print("wowie, i'm real slow!")
end
```

## stunned

If the object is stunned, returns the spellID of the stun debuff.

**`unit.stunned : stunID | nil`**

```lua
if player.stun then
    print("wow, i'm stunned!")
end
```

## stunRemains

The remaining time of stun effects on the object.

**`unit.stunRemains : remains | 0`**

```lua
if player.stunRemains > 3 then
    print("wow, i'm stunned for a long time!")
end
```

## stunInfo

If the object is affected by a stun, returns the [ccInfo](/object-attributes.md?id=ccInfo) of the effect.

**`unit.stunInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

```lua
-- same thing as other cc info :P
```

## rooted

If the object is rooted, returns the spellID of the root debuff.

**`unit.rooted : rootID | nil`**

```lua
if player.rooted then
    print("wow, i'm rooted!")
end
```

## rootRemains

The remaining time of root effects on the object.

**`unit.rootRemains : remains | 0`**

```lua
if player.rootRemains > 3 then
    print("wow, i'm rooted for a long time!")
end
```

## rootInfo

If the object is affected by a root, returns the [ccInfo](/object-attributes.md?id=ccInfo) of the effect.

**`unit.rootInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

```lua
-- same thing as other cc info :P
```

## silenced

If the object is silenced, returns the spellID of the silence debuff.

**`unit.silenced : silenceID | nil`**

```lua
if player.silence then
    print("wow, i'm silenced!")
end
```

## silenceRemains

The remaining time of silence effects on the object.

**`unit.silenceRemains : remains | 0`**

```lua
if player.silenceRemains > 3 then
    print("wow, i'm silenced for a long time!")
end
```

## silenceInfo

If the object is affected by a silence, returns the [ccInfo](/object-attributes.md?id=ccInfo) of the effect.

**`unit.silenceInfo : { debuffID, debuffName, debuffRemains, drCategory, castSource } | {}`**

```lua
-- same thing as other cc info :P
```

# Diminishing Returns

## disorientDR

The disorient DR of the object. `0.25` is quarter DR, `0.5` is half DR, `1` is full DR.

**`unit.ddr : disorientDR | 1`**

```lua
if enemy.disorientDR == 1 then
    fear:Cast(enemy)
end
```

## disorientDRRemains

The time remaining before the disorient DR of the object resets.

**`unit.ddrr : remainingDR | 18`**

```lua
if enemy.ddrRemains >= 16 or enemy.disorientDR == 1 then
    fear:Cast(enemy)
end
```

## incapacitateDR

The incapacitate DR of the object. `0.25` is quarter DR, `0.5` is half DR, `1` is full DR.

**`unit.idr : incapacitateDR | 1`**

```lua
if enemy.incapDR == 1 then
    sheep:Cast(enemy)
end
```

## incapacitateDRRemains

The time remaining before the incapacitate DR of the object resets.

**`unit.idrr : remainingDR | 18`**

```lua
if enemy.idrRemains >= 16 or enemy.idr == 1 then
    sheep:Cast(enemy)
end
```

## stunDR

The stun DR of the object. `0.25` is quarter DR, `0.5` is half DR, `1` is full DR.

**`unit.sdr : incapacitateDR | 1`**

```lua
if enemy.stunDR >= 0.25 and enemy.stunRemains < 0.4 then
    cheapShot:Cast(enemy)
end
```

## stunDRRemains

The time remaining before the stun DR of the object resets.

**`unit.stunDRRemains : remainingDR | 18`**

```lua
if enemy.sdrr >= 16 or enemy.stunDR == 1 then
    cheapShot:Cast(enemy)
end
```

## rootDR

The root DR of the object. `0.25` is quarter DR, `0.5` is half DR, `1` is full DR.

**`unit.rdr : rootDR | 1`**

```lua
if enemy.rootDR >= 0.5 and enemy.rootRemains < 0.4 then
    root:Cast(enemy)
end
```

## rootDRRemains

The time remaining before the root DR of the object resets.

**`unit.rdrr : remainingDR | 18`**

```lua
if enemy.rootDRRemains >= 12 or enemy.rootDR == 1 then
    root:Cast(enemy)
end
```

## silenceDR

The silence DR of the object. `0.25` is quarter DR, `0.5` is half DR, `1` is full DR.

**`unit.silenceDR : rootDR | 1`**

```lua
if enemy.silenceDR == 1 then
    silence:Cast(enemy)
end
```

## silenceDRRemains

The time remaining before the silence DR of the object resets.

**`unit.silenceDRR : remainingDR | 18`**

```lua
if enemy.silenceDRR >= 12 or enemy.silenceDR == 1 then
    silence:Cast(enemy)
end
```

# Immunities & Defensives

## About immunities...

### For the most common applications, you don't need to use these...

> [Spell Objects](spell-objects.md) handle **literally all immunity checking** you would otherwise need to do when **attempting to cast a spell**, you just have to set your **options** when initializing the spell object (or when casting). Definitely read more on the available [spell object options](spell-object-options.md) before making too much use of the following immunity attributes.

> Immunity to **effects** means things like **damage over time debuffs**, **crowd control debuffs**, etc. Immunity to **damage** is as it sounds, **immunity to damage**. Most immunities are to **both effects and damage**, but we offer separate attributes for them because it's better to be **precise**.

## beast

Checks if the unit is currently immune to polymorph effects.

> Does not use a specific buff list, but instead efficiently parses [`debuffDescriptions`](object-attributes#debuffDescriptions) - should work on _all_ current & future polymorph immunity effects in the game. If it does not, let us know which one.

> Alternatively: `immuneSheep`, `sheepImmune`

**`unit.beast : true | false`**

```lua
if target.beast then
    print("damn bruh, i can't sheep the man")
end
```

## ccImmunityRemains

Remaining duration of CC immunity effects on the unit

**`unit.ccImmunityRemains : remains | 0`**

- specifically checks for pvp-related buffs/debuffs that make the unit immune to cc (holy ward, bladestorm, fleshcraft, etc.)

```lua
if target.ccImmunityRemains > spell.castTime then
    print("sry mate can't cc ya")
else
    spell:Cast(target)
    print("noice")
end
```

## healingImmunityRemains

Remaining duration of healing immunity effects on the unit

**`unit.healingImmunityRemains : remains | 0`**

- specifically checks for pvp-related buffs/debuffs that make the unit immune to healing (cyclone, etc.)

```lua
if target.healingImmunityRemains > spell.castTime then
    print("sry mate can't heal ya")
else
    spell:Cast(target)
    print("noice")
end
```

## immuneCC

Checks if the unit is currently immune to crowd control effects

**`unit.immuneCC : true | false`**

```lua
if target.immuneCC then
    print("shieeee dawg, I can't cc them!")
end
```

## immuneHealing

Checks if the unit is currently immune to healing effects

**`unit.immuneHealing : true | false`**

- specifically checks for pvp-related buffs/debuffs that make the unit immune to healing (cyclone, etc.)

```lua
if target.immuneHealing then
    print("sry mate can't heal ya")
end
```

## immuneMagic

Checks if the unit is currently **immune to magic damage or effects**

**`unit.immuneMagic : true | false`**

```lua
if target.immuneMagic then
    print("I cannot do anything magic to this man")
end
```

## immuneMagicDamage

Checks if the unit is currently **immune to magic damage**

**`unit.immuneMagicDamage : true | false`**

```lua
if target.immuneMagicDamage and not target.immuneMagicEffects then
    print("I cannot do magic damage to the man, but I can sheep him")
end
```

## immuneMagicEffects

Checks if the unit is currently **immune to magic effects**

**`unit.immuneMagicEffects : true | false`**

## magicDamageImmunityRemains

Remaining duration of **magic damage immunities**

**`unit.magicDamageImmunityRemains : remains | 0`**

## magicEffectImmunityRemains

Remaining duration of **magic effect immunities**

**`unit.magicEffectImmunityRemains : remains | 0`**

## immunePhysical

Checks if the unit is currently **immune to physical damage or effects**

**`unit.immunePhysical : true | false`**

```lua
if target.immunePhysical then
    print("I cannot do anything physical to this man :eggplant:")
end
```

## immunePhysicalDamage

Checks if the unit is currently **immune to physical damage**

**`unit.immunePhysicalDamage : true | false`**

```lua
if target.immunePhysicalDamage and not target.immunePhysicalEffects then
    print("I cannot do physical damage to the man, but I storm bolt him")
end
```

## immunePhysicalEffects

Checks if the unit is currently **immune to physical effects**

**`unit.immunePhysicalEffects : true | false`**

## physicalDamageImmunityRemains

Remaining duration of **physical damage immunities**

**`unit.physicalDamageImmunityRemains : remains | 0`**

## physicalEffectImmunityRemains

Remaining duration of **physical effect immunities**

**`unit.physicalEffectImmunityRemains : remains | 0`**

## immuneSlows

Checks if the unit is currently immune to **slows / snares**

**`unit.immuneSlows : true | false`**

## immuneStuns

Checks if the unit is currently immune to **stuns**

**`unit.immuneStuns : true | false`**

# Misc. Attributes

## inspect

Returns detailed information gathered from inspecting a unit.

> Can only be used on players who are in your party and visible

```lua
-- return value from player.inspect
{
 class_id = 11,
 glyphs = {
 },
 guid = "Player-11-0D13D216",
 class = "DRUID",
 spec_description = "bla bla",
 spec_icon = 132115,
 race = "Tauren",
 spec_background = "DAMAGER",
 class_localized = "Druid",
 lku = "player",
 global_spec_id = 103,
 pvp_talents = {
  3751 = {
   spell_id = 202626,
   name_localized = "Leader of the Pack",
   icon = 135881,
   talent_id = 3751,
  },
  ...etc
 },
 spec_index = 2,
 gender = 3,
 spec_role = "DAMAGER",
 spec_group = 1,
 name = "Awfulcat",
 talents = {
  18571 = {
   name_localized = "Wild Charge",
   talent_id = 18571,
   spell_id = 102401,
   column = 3,
   icon = 538771,
   tier = 2,
  },
  21778 = {
   name_localized = "Mighty Bash",
   talent_id = 21778,
   spell_id = 5211,
   column = 1,
   icon = 132114,
   tier = 4,
  },
  ...etc
 },
 race_localized = "Tauren",
 spec_role_detailed = "melee",
 spec_name_localized = "Feral",
}
```

## mgr

Remaining healing absorption effect of Mindgames

> **mindGamesRemains** works too

**`unit.mgr : mgr | 0`**

## ttd

Remaining time to die of the unit, based on **linear regression algorithm lifted from HeroLib**

> Must set `awful.ttd_enabled` to `true` for ttd functions / attributes to work.

```lua
if target.ttd < 20 then
    -- do pve stuff idk
end
```

----
pages/awful_ui/ui.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Awful includes a powerful, sleek in-game GUI library built entirely from scratch.

> **You can build a sleek, highly-customizable GUI for your project in minutes, with advanced elements and features like multi-selection-dropdowns, sliders, a powerful independent saved variable system, and more. All baked in and brought to life with just a few lines of code.**
>
> Awful UI uses no external textures, and saved variables are handled by **awful config**, which uses basic filesystem API to store and retrieve settings, so the installation of an addon is never required.

# Creating a UI

It's insanely easy. Here's an example UI to get started:

```lua
-- RGBA color scheme
local yellow = {245, 235, 55, 1}
local white = {255, 255, 255, 1}
local dark = {21, 21, 21, 0.45}
-- all ui saved variables are stored in `settings`
-- slash command to open the GUI is now `/example`
local gui, settings, cmd = awful.UI:New("example", {
	title = "example ui",
	show = true, -- show on load by default
	colors = {
		-- color of our ui title in the top left
		title = yellow,
		-- primary is the primary text color
		primary = white,
		-- accent controls colors of elements and some element text in the UI. it should contrast nicely with the background.
		accent = yellow,
		background = dark,
	}
})

-- declaring tabs locally can be cleaner
local example = gui:Tab("Example")
example:Checkbox({
	text = "example box",
	var = "test", -- checked bool = settings.test
	default = true
})

-- but you can also access tab objects like this
gui:Tab("Test 123")
gui.tabs["Test 123"]:Slider({
	text = "hi mom",
	var = "mom", -- selected number = settings.mom
	min = 0,
	max = 100,
	default = 69,
	valueType = "%",
	tooltip = "hi mom :)"
})
```

> ![example ui](/gifs/example-ui.gif)
>
> This is what you should be presented with from the above code.
>
> You can now open and close this GUI with **`/example`** since that's what we passed as the first arg to `UI:New()` .. Change that to something else and play around with it a bit, change the colors and stuff. Customize it and get familiar. That's the fun part.

# UI:New

## 2 parameters:

> - 1.) The **name of your ui** (string)
>
> - 2.) The **configuration** of your UI (table)

## 3 returns:

> - 1.) The **ui object** (table), containing the UI frame itself, all **awful ui methods**, tabs, elements, etc. Mainly it's used to **create new tabs**, which are your primary source of **creating elements**.
>
> ```lua
> ui:Tab("Tabname")
> ```
>
> - 2.) The **list of saved variables** (table), a.k.a **settings** saved by this GUI. You can access them like a normal table by the **var** option you set for each element. You can also store a reference to this table elsewhere, like in your [project namespace](namespaces#project).
>
> ```lua
> local ui, settings, cmd = awful.UI:New(...)
> if player.hp <= settings.defensiveHP then defensive() end
> ```
>
> - 3.) The **awful command object** for this ui, set up with the **name you passed as the first arg**.. By default, **this slash cmd will open/close your UI**. You can additionally use the cmd object from this return to register new slash command callbacks.
>
> Also, all 'un-registered' commands will redirect to `/awful`, so users can use your slash cmd to access things like `/awful cast` -> `/example cast polymorph focus`
>
> ```lua
> -- now `/example burst` will call our burst() function!
> cmd:New(function(msg)
>   if msg == "burst" then
>     burst()
>     -- returning true officially 'registers' it
>     return true
>   end
> end)
> ```

**`awful.UI:New(name, config) : gui, settings, cmd`**

### Since the **config** argument is what you'll use to customize the look and feel of your UI, let's go over what all you can configure there:

# config

## title

> So in the example UI config above, we pass title as a **string**, but you can also pass **an array of strings**, which will be placed next to each other at the top.
>
> ```lua
> title = {"example", "ui"},
> ```
>
> This is primarily so you can have a **multi-colored title** for your UI. To do that, you simply **pass a matching array of RGBa colors for each string**:
>
> ```lua
> local white, green = {r,g,b,a}, {r,g,b,a}
> ...
> colors = {
>   title = {white, green},
> ```

## colors

> awful UI uses red, green, blue, alpha color format. **r/g/b 0-255, alpha 0-1**.

```lua
-- more example color pseudo code
local green = {100, 255, 100, 1}
local white = {255, 255, 255, 1}
local black = {6, 4, 4, 0.8}
...UI:New('example', {
  title = {'example', 'ui'},
  colors = {
    title = {white, green},
    primary = white,
    accent = green,
    background = black,
  },
})
```

### colors > primary

> Primary is used for general purpose text in the ui, like element labels, headers and sections. It should contrast well with the background.
>
> ```lua
> colors = {
>   primary = {r,g,b,a},
> ```

### colors > title

> **To color a single title:**
>
> ```lua
> title = {r,g,b,a},
> ```
>
> **Multi-colored titles:**
>
> ```lua
> title = {{r,g,b,a}, {r,g,b,a}, ...},
> ```

### colors > accent

> The accent color is used to fill checkboxes, the slider thumb, scroll bars, etc.
>
> ```lua
> colors = {
>   accent = {r,g,b,a},
> ```

### colors > background

> The background color
>
> ```lua
> colors = {
>   background = {r,g,b,a},
> ```

### colors > tertiary

> The tertiary color is used in both the sidebar and other minor elements like the value labels on sliders.
>
> ```lua
> colors = {
>   tertiary = {r,g,b,a},
> ```

## width / height / scale

> You can set the width, height, and/or scale of the UI.
>
> ```lua
> UI:New('example', {
>   ...
>   width = 350,
>   height = 225,
>   scale = 1.2
> })
> ```

defaults are as follows, which makes for a rather compact ui:

```lua
width = 325
height = 195
scale = 1
```

## show

> If you set this to `true`, your UI will open on each load.

## defaultTab

> By default, when you open the UI it will select the first tab you created. You can choose a different one by passing this variable as the tab name.
>
> ```lua
> -- by default, it would select this tab when first opened
> ui:Tab("General")
> -- but config below will make it select this one instead
> ui:Tab("Information")
>
> UI:New('example', {
>   ...
>   defaultTab = "Information",
> ```

## sidebar

> You can pass this as false to disable the tertiary colored "sidebar" under the tabs on the left.
>
> ```lua
> UI:New('example', {
>   ...
>   sidebar = false,
> ```

## tabs_w

> By default, the "sidebar" aka "tabs section" will grow with the width of your title. If your title is super long and you need to limit the width of the tab section and let it overflow, or you just want a different sized tab section, you can pass a specific width with this.
>
> ```lua
> UI:New('example', {
>   ...
>   tabs_w = 95, -- default is 110
> ```

### That's pretty much all there is to config at the time of writing this.

> **Now, let's explore the [elements](awful-ui-elements) available to awful UI!**

# Tab

> `ui:Tab` creates a new tab in the tab section of the GUI, and returns a smart **Tab Object** which you can use to create elements.

```lua
ui:Tab(name)
```

## Examples

```lua
local ui, settings, cmd = awful.UI:New(...)

--! My tab !--
local myTab = ui:Tab("Mine!") -- this my tab :)
myTab:CheckBox(...) -- blabla, make element

--! Your tab !--
local yourTab = ui:Tab("Yours " .. awful.textureEscape(118, 12, "0:2")) -- >:(
yourTab:Slider(...) -- etc
```

# Group

> `ui:Group` creates a new expandable / collapsible **folder** in the tab section of the GUI, which can contain a **group of tabs**. it can even be configured to **apply new title(s) and color(s) to the UI** when a tab from the folder is selected!
>
> Returns a **group object**, which allows you to create tabs in the group via the same method as ui:Tab
>
> ```lua
> local tab = group:Tab(name) -- this tab is in the group
> ```

```lua
ui:Group(options)
```

## Options

### Required Options:

> **name**: `{string}`, **required** - the name of the group, which is what will be displayed for the folder (it looks like a tab, but when you click it, expands into a folder of tabs)

### Optional Options:

> The following options are optional. **They're configured the same as they are in [UI:New config](awful-ui?id=config)** They will update the title/color/etc. of the GUI when visintg a tab in this group.
>
> **colors** - [config](awful-ui?id=colors)
>
> **title** - [config](awful-ui?id=title)

## Examples

```lua
local ui, settings, cmd = awful.UI:New(...)

-- hunter group
local hunterColor = {170, 211, 114, 1}
local hunterGroup = ui:Group({
	name = "Hunter",
	-- UI takes on hunter theme when on hunter tabs 8=D
	title = {"awful", "hunter"},
	colors = {
		title = {awfulCream, hunterColor},
		accent = hunterColor,
	}
})

--! CONTROL TAB !--
local control = hunterGroup:Tab("Control " .. awful.textureEscape(187650, 12))

control:Text({text = awful.textureEscape(187650, 14) .. " Freezing Trap", header = true})

--! DEFENSE TAB !--
local defense = hunterGroup:Tab("Defense " .. awful.textureEscape(53480, 12))

defense:Checkbox({name = "Auto RoS", default = true, var = "autoRoS", tooltip = "Automatically use RoS"})

```

----
pages/awful_ui/ui-elements.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Awful UI makes it easy to generate powerful elements within your new UI.

## Let's go over some of them.

> Elements are created using the methods found within **tab objects**. You create tab objects using `ui:Tab(name)`, which returns the tab object for that tab. You can then declare them locally or retrieve them from `ui.tabs["tab name"]` and start populating them with elements using the methods we're about to go over.
>
> ```lua
> tab:Checkbox({text="hi", var="hi"})
> ```
>
> Now there's a checkbox on that tab!

# Getting Started

## Initial Setup

> I'm starting under the assumption you've read everything from the [introduction](awful-ui) section, and have an understanding of how [namespaces](namespaces) work. Most of this will be [pseudocode](https://www.educative.io/edpresso/what-is-pseudocode).

### gui.lua

```lua
local Unlocker, awful, project = ...

-- this is pseudo code, be sure to read "introduction" and configure your gui properly.
local ui, settings, cmd = awful.UI:New('example', {
  ...
})

-- making the settings available to the rest of our project
project.settings = settings

-- and this is the tab I'll be adding elements to
local General = ui:Tab("General")
```

### example.lua

```lua
local Unlocker, awful, project = ...
-- now in any other file (spellbook, actor, etc.), we can pull in our settings like so:
local settings = project.settings


-- somewhere in our code:
if player.hp <= settings.healthstoneHP then
  healthstone:Use() -- example blabla
end
```

# Elements

## Checkbox

> Checkboxes are the simplest form of control, allowing the user to toggle certain things off/on.
>
> value type: `boolean`

### Options:

> **text**: `{string}`, **required** - the label next to the checkbox
>
> **var**: `{string}`, **required** - the variable the value of this checkbox will be stored at within your UI's saved variables list.
>
> **default**: `{boolean}`, optional - default checked value of the box
>
> **tooltip**: `{string}`, optional - displays a tooltip when hovering the element to better explain its purpose to the user.

### Examples:

```lua
General:Checkbox({
  text = "Fuhr Mode",
  var = "fuhrMode",
  tooltip = "Enables Fuhr Mode\n\n-20% damage"
})

-- example usage in our rotation
if settings.fuhrMode then
  return StopDoingDamage()
end

-- more examples
General:Checkbox({
  text = "Auto Blink",
  var = "autoBlink",
  default = true,
  tooltip = "Toggles auto blink usage in the routine"
})

if settings.autoBlink then
  blink()
end
```

## Text

> Text can be used for a variety of things, from headers that section off groups of elements to create a more organized feel, to creating an entire tab dedicated to explaining macros or other aspects of your project.

### Options:

> **text**: `{string}`, **required** - the text to be displayed (hint: you can use texture escapes `|cFF<HEXCOLOR>` in this strinng to color the text, or `awful.textureEscape(spellID[,size, "x:y"])` to draw spell textures in your UI.. examples below)
>
> **header**: `{boolean}`, optional - sets font to header-appropriate size relative to other elements, and adds a bit more padding on top and bottom to better separate it from other elements.
>
> **size**: `{number}`, optional - font size of the text
>
> **paddingLeft, paddingRight, paddingTop, paddingBottom**: `{number}`, optional - adds padding to any side of the text
>
> **OnClick**: `{function}`, optional - you can set up a function to be called when your text element is clicked.

### Examples:

```lua
General:Text({
  text = "General Settings",
  header = true
})

-- lil combustion icon, fires an alert when clicked!
General:Text({
	text = awful.textureEscape(190319, 16, "0:2") .. " Fire Mage:",
	size = 14,
	paddingBottom = 10,
	OnClick = function(self, event)
		awful.alert("You're Fired ;)", 190319)
	end
})

-- padding on the left places element further to the right
General:Text({
	text = "I'm way out on the right!",
	size = 12,
	paddingLeft = 10
})

-- large green colored text, nice!
General:Text({
	text = "|cFF6eff86Big green text, wow!",
	size = 16
})

-- multiple colors
General:Text({
	text = "|cFF6eff86Green text|r, wait.. |cFF43caf7blue text!",
})
```

> ![text](/gifs/text.gif)
>
> Some Text from example above

## Slider

> Sliders allow the user to select a number from the range you set. This can apply to any number, from health percentages, measurements of time, diminishing return modifiers, etc. That's why you're able to set the `valueType` in the options, to better display what type of value the user is controlling.
>
> value type: `number`

### Options:

> **text**: `{string}`, **required** - the label on top of the slider
>
> **var**: `{string}`, **required** - the variable the value of this slider will be stored at within your UI's saved variables list.
>
> **min**: `{number}`, **recommended** - minimum value of the slider, the "low end" - defaults to 0 if not specified
>
> **max**: `{number}`, **recommended** - maximum value of the slider, the "high end" - defaults to 100 if not specified
>
> **default**: `{number}`, **recommended** - default value of the slider. defaults to `min` if not specified.
>
> **valueType**: `{string}`, optional - adds this string to the end of the slider's value(s) displayed in the GUI, making it clearer to the user what type of value they're controlling. e.g, `ms`, `%`, `sec.`
>
> **step**: `{number}`, optional - allows you to control the value step for the slider - defaults to 1
>
> **tooltip**: `{string}`, optional - displays a tooltip when hovering the element to better explain its purpose to the user.

### Examples:

```lua
General:Slider({
	text = "Healthstone HP",
	var = "healthstoneHP",
	min = 0,
	max = 100,
	step = 1,
	default = 25,
	valueType = "%",
	tooltip = "HP the routine will use healthstone at"
})

-- in the routine
if player.hp <= settings.healthstoneHP then
  healthstone:Use()
end

-- more examples:
General:Slider({
	text = "Kick Delay",
	var = "kickDelay",
	min = 0,
	max = 450,
	step = 5,
	default = 250,
	valueType = "ms",
	tooltip = "Delay on interrupts from the routine (in milliseconds)\n\nAverage human reaction time is ~250ms. Blablabla."
})

if target.castTimeComplete > settings.kickDelay then
  kick()
end
```

> ![sliders](/gifs/sliders.gif)
>
> Sliders from example above
>
> note: values are only available in chat like this because i declared my ui object as `_G.gui`

## Dropdown

> Dropdowns allow the user to select an option from a list of options. Multi-selection dropdowns allow the user to select multiple options from the list. You can easily choose one or the other dropdown types for your application, but the value types are very different between the two.
>
> **Dropdown** value type: `string`
>
> Example: **settings.mode == "fuhr mode"**
>
> ```lua
> if settings.mode == "fuhr mode" then doNoDamage() end
> ```
>
> **Multi-selection Dropdown** value type: `associative array`
>
> Example: `**settings.modes == { ["fuhr mode"] = true, ["example mode"] = true }**`
>
> ```lua
> if settings.modes["fuhr mode"] then doNoDamage() end
> ```

### Options:

> **options**: `{table}`, **required** - an array of dropdown options, each containing a `label`, `value`, and optionally a `tooltip`
>
> **var**: `{string}`, **required** - the variable the value of this dropdown will be stored at within your UI's saved variables list.
>
> **header**: `{string}`, **recommended** - the header on top of the dropdown
>
> **multi**: `{boolean}`, optional - whether or not this dropdown is a multi-selection dropdown
>
> **placeholder**: `{string}`, **recommended** - placeholder text that will be displayed on the dropdown when nothing is selected
>
> **default**: `{multi: array, single: string}`, optional - default selection(s) of the dropdown. an array of **values** from given options as strings for multi dropdowns, or the one value string for single selection dropdowns
>
> **tooltip**: `{string}`, optional - displays a tooltip when hovering the element to better explain its purpose to the user.

- **Tip:** For **single selection dropdowns**, you don't need both a placeholder **and** a default. It's one or the other. Right clicking the dropdown will reset it to default, which is **no selection** if you didn't provide a default. If you did provide a default, it will always have some value, so the placeholder will never be shown.

### Examples:

```lua
--! SINGLE DROPDOWN !--
General:Dropdown({
	var = "mode",
	tooltip = "Choose your mode. Can be a lot of test or a little, or anywhere inbetween on the test spectrum.\n\nIf you need any more test, don't hesitate to contact us:\n\n1-800-TEST-123",
	options = {
		{ label = "Big Mode", value = "Big", tooltip = "Allows you to test big mode without small mode interfering" },
		{ label = "Small Mode", value = "Small", tooltip = "Reduces the size of all enabled test modes" },
		{ label = "Mode of Tests", value = "Tests", tooltip = "Increase the rate at which you can do tests"  },
		{ label = "No Test Zone", value = "NoTesting", tooltip = "Stops all of the testing" },
		{ label = "Fuhr Mode", value = "Fuhr" },
	},
	placeholder = "Select your mode",
	header = "Testing Mode:",
})

-- example usage
if settings.mode == "Fuhr" then
  doNoDamage()
elseif settings.mode == "Big" then
  doBigDamage()
end

--! MULTI-DROPDOWN !--
General:Dropdown({
	var = "totems",
	multi = true,
	tooltip = "Choose the totems you want to stomp.",
	options = {
		{ label = "Grounding", value = 5925 },
		{ label = "Spirit Link", value = 53006 },
    { label = "Psyfiend", value = 101398 },
    { label = "War Banner", value = 119052, tooltip = "Stomping banner is useful for getting cc and blabla" },
    { label = "Capacitor", value = 61245, tooltip = "Stomping cap is good, no cap" },
	},
	placeholder = "Select totems",
	header = "Totems to stomp:",
  default = {5925, 53006, 101398} -- optional default selections
})
-- example usage
awful.totems.stomp(function(totem, uptime)
  -- only stomp from selected totems
  if not totem.id or not settings.totems[totem.id] then return end
  if fireBlast:Cast(totem) then
    awful.alert("Stomp " .. totem.name, fireBlast.id)
  end
end)
```

> ![mdd](/gifs/mdd.gif)
>
> Multi Dropdown from example above

Sometimes a Spell can have multiple IDs, such as Polymorph due to various Glyphs that can be applied to it. If you don't want to have one entry per Polymorph in e.g. your Interrupt dropdown, then you can do as the below example:
```lua
--! MULTI-DROPDOWN WITH MULTIPLE SPELL IDS FOR SAME ENTRY !--
General:Dropdown({
	var = "interrupts",
	multi = true,
	tooltip = "Choose the Spell you want to Interrupt",
	options = {
		{ label = "Repentance", value = 20066 },
		{ label = "Polymorph", value = "Polymorph", tvalue = {118,161355,161354,161353,126819,61780,161372,61721,61305,28272,28271,277792,277787} },
	},
	placeholder = "Select Interrupts",
	header = "Casts to Interrupt:",
  	default = {20066, "Polymorph"} -- optional default selections
})
-- example usage
local function SettingsCheck(settingsVar, castId)
    for k, v in pairs(settingsVar) do
        if k == castId and v == true then return true end
        if type(v) == "table" then
            for _, id in ipairs(v) do
                if castId == id then return true end
            end
        end
    end
end

awful.enemies.loop(function(unit, i, uptime)
    if not unit.casting then return end
    if unit.casting and unit.castint then return end
    if unit.castTimeComplete > 0.3 and unit.castRemains > awful.buffer then
        if SettingsCheck(settings.interrupts, unit.castingid) then
            if counterspell:Cast(unit) then
                return true
            end
        end
    end
end)
```

----
pages/awful_ui/meta.en-US.json
{
    "ui": "Introduction",
    "ui-elements": "Elements",
    "ui-statusframe": "Status Frame"
}
----
pages/awful_ui/ui-statusframe.en-US.mdx
# StatusFrames allow you to display interactive heads-up status information

![statusframe](/gifs/statusframe.gif)

> Calling `StatusFrame` will create a status frame for your UI. It saves its own position separately from your main UI, and has a lock/unlock button for the user to position and lock it where they like.
>
> StatusFrame has a growing list of its own elements to display interactive heads-up status information based on your settings in real time.
>
> ```lua
> local ui, ... = awful.UI:New(...)
>
> local statusFrame = ui:StatusFrame(options)
> ```

# Getting Started

## Initial Setup

> Create a StatusFrame

- `ui:StatusFrame` has 1 param, `options`

### Optional Options:

> The following options are optional, but allow for customization of the StatusFrame.
>
> **colors** - [config](awful-ui?id=colors) :
>
> - there are a few minor differences with colors here:
>   - 1. colors.**enabled** is used for Enabled text in Toggles.
>   - 2. colors.**disabled** is used for Disabled text in Toggles.
>   - 3. colors.**value** is used for value text in some elements
>   - 4. colors.**primary** used everywhere else
>
> **fontSize** `{number}`, normal font size for text in the status frame. other font sizes used will scale relative to this one.
>
> **maxWidth** `{number}`, max width of the StatusFrame before elements begin wrapping to create a new row.
>
> **column** `{boolean}`, pass this if you want all elements to be stacked on top of each other

### Creation Example

```lua
local statusFrame = ui:StatusFrame({
	fontSize = 12,
	colors = {
    -- transparent bg
		background = {0, 0, 0, 0},
    -- cool blue value text
		value = {30, 240, 255, 1},
	},
	maxWidth = 450,
	-- column = true
})
```

----
pages/spell_objects/spell-objects.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they?

> They are another special type of **awful object**, which provide a powerful, customized, object-oriented set of tools **for each spell** your routine will be casting.
>
> You create them by providing a spell ID and traits (options) that apply to your spell. Then for the life of the spell object, all methods, attributes, etc. will act according to these traits.

## Making them

### awful.Spell

```lua
awful.Spell(spellID, traits) -> Spell Object
```

### Example

```lua
local mortalStrike = awful.Spell(12294, {damage = "physical"})
```

Now `mortalStrike` contains all attributes, methods, and functions of a **spell object**. We can use it to gather all kinds of information about Mortal Strike, as well as actually cast it and set up various conditions for casting it. Most relevant information about the spell is gathered from WoW's API by the spell object, and presented in the form of **attributes**. We also told it that it's a physical damage ability - so it will make no mistake about which immunities to avoid attacking into.

## More about them

### Traits

> There's a big ol' ever-growing list of spell object **traits** which tell the framework more about the spell behind the object, and how to use it. Many traits sole purpose is to avoid casting into immunities, and many define different rules for when it's possible to cast the spell.
>
> By default, `:Cast` won't even try to cast while you're in CC, but some abilities can be cast while in CC, like Divine Shield or Ice Block. So you pass `ignoreControl = true` to the options, and bam - it will cast it while in CC.
>
> ```lua
> -- may as well have it cancel channels too, if we're trying to cast it
> iceBlock = awful.Spell(123, {ignoreControl = true, ignoreChanneling = true})
> ```
>
> Same for moving and casting, while it does understand you can cast with things like Spiritwalker's Grace or Ice Floes - some spells can always be cast while moving, like scorch or steady shot.
>
> ```lua
> scorch = awful.Spell(123, {ignoreMoving = true})
> ```

### Benefits of Spell Objects

> Many complex calculations and actions are handled effortlessly by **Spell Objects**. Mortal Strike is a super basic example, but the `:Cast` method of `mortalStrike` will already make sure the spell is off cooldown, we have enough rage, we're in melee range of the target and facing them, they aren't immune to physical damage (even from something like evasion or die by the sword while the target is facing us,) and more before casting it.
>
> Spell objects allow you to modularize code related to the spell into neat little packages within itself. It makes for a fantastic organization method (code related to each spell is within its own spell object - your actor becomes an easy to digest spell-related stack of priorities, as routines should be!), and a major performance benefit (code related to spells only runs when the underlying spell is ready to be cast)!
>
> Spells with cast times already know to start `:Cast` perfectly timed as an immunity to it will fall, complex AoE positioning around corners and out of range is all handled automatically by `CastEdge`, and soooo much more.

## Populating the Actor

> You can use **[awful.Populate](../awful_toolbox/toolbox_tools?#Populate)** to make a list of spell objects available to your routine actor and the scope of your SpellBook file.
>
> ```lua
> local actor = project.hunter.survival
> local spells = {
>   exhilaration = awful.Spell(1234, {heal = true}),
>   intimidation = awful.Spell(12345, {effect = "physical", stun = true}),
>   trap = project.hunter.trap, -- grabbing this from hunter file!
> }
> -- populate actor and scope of this file
> awful.Populate(spells, actor, getfenv(1))
> ```
>
> It's important to use Populate if you want to access your spell objects without creating additional local references to them or storing and grabbing them from tables.
>
> [Read more about populate here](../awful_toolbox/toolbox_tools?#Populate)

## Raw Spell List Example

```lua
local Spell = awful.Spell
awful.Populate({

  -- static objects [not req. but tiny perf. increase and takes care of declaration where i use them]
  target = awful.target,
  focus = awful.focus,
  player = awful.player,
  healer = awful.healer,
  pet = awful.pet,
  enemyHealer = awful.enemyHealer,

  -- dmg
  kill = Spell(53351, { damage = "physical", ranged = true, targeted = true }),
  barbed = Spell(217200, { damage = "physical", ranged = true, targeted = true }),
  cobra = Spell(193455, { damage = "physical", ranged = true, targeted = true }),
  flayed = Spell(324149, { damage = "physical", ranged = true, targeted = true, bleed = true }),
  killCommand = Spell(34026, { damage = "physical", targeted = true }),
  conc = Spell(5116, { effect = "physical", ranged = true, targeted = true, slow = true }),

  -- cc
  trap = hunter.trap,
  tar = Spell(187698, { effect = "magic", slow = true }),
  cs = Spell(147362, { effect = "physical" }),

  -- offensive
  wrath = Spell(19574),
  wild = Spell(193530),
  tranq = Spell(19801),
  bassy = Spell(205691, { damage = "physical", targeted = true }),

  -- defensive
  feign = hunter.feign,
  freedom = Spell(53271, { ignoreFacing = true, ignoreLoS = true, beneficial = true }),
  turtle = Spell(186265),

  -- misc
  flare = Spell(1543),
  mendPet = Spell(136, { heal = true }),
  camo = hunter.camo,
  ros = hunter.ros,
  disengage = hunter.disengage,
  racials = {
    -- blood fury
    Orc = Spell(20572),
    -- berserking
    Troll = Spell(26297),
  },

}, bm, getfenv(1))
```

### [Spell Object Traits](spell-object-traits)

----
pages/spell_objects/spell-object-attributes.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they?

> They function the same as [awful object attributes](/awful_objects/object-attributes), so it's worth reading up on those if you haven't.
>
> Spell Objects have many attributes of their own though, usually relating to some **information about the spell**, most of which is derived from the **Spell ID** using WoW API, which is why you must provide it instead of a spell name. Here are some examples of spell attributes:
>
> **spell.cd** - Current cooldown of the spell
>
> **spell.name** - Name of the spell
>
> **spell.range** - Max range of the spell
>
> Simple and effective :)

```lua
-- creating new spell object
local mortalStrike = awful.Spell(1234, { damage = "physical" })

-- can use methods on it, like :Callback
mortalStrike:Callback(function(spell)
  return spell:Cast(target)
end)

-- casting methods outside of :Callback are also valid ofc
mortalStrike:Cast(target)
heroicLeap:SmartAoE(target)

-- all of the attributes are stored right inside the same object
if mortalStrike.cd < 2 then
  print(mortalStrike.name .. " coming off cd!")
end
```

# Spell Attributes

## baseCD

> The base cooldown of the spell, according to `GetSpellBaseCooldown`, but with fixes applies for spells that have multiple charges

```lua
if target.cooldown("survival instincts") > spell.baseCD then
  spell:Cast(target)
end
```

## castTime

> The cast time of the spell (0 for instant cast spells)

```lua
-- this calculation is already handled inside `:Cast`!
if target.magicImmunityRemains < spell.castTime then
  spell:Cast(target)
end
```

## cd

> Current cooldown of the spell

- `cooldown` is another valid key for the same attribute

```lua
if spell.cd > 10 then
  print("dang it, gonna be a while before i can use this one")
end
```

## charges

> Number of charges available for the spell.

- Round integer, does not include fractional charges.

```lua
if spell.charges >= 2 then
  spell:Cast(target)
end
```

## chargesFrac

> Number of charges available for the spell, including fractional charges.

- Float, includes frac charges.
- `frac` is another valid key for the same attribute

```lua
if fireBlast.frac >= 1.5 then
  fireBlast:Cast()
end
```

## chargesMax

> Number of maximum charges for the spell

```lua
if spell.frac >= spell.chargesMax - 0.5 then
  spell:Cast()
end
```

## nextChargeCD

> Remaining cooldown on the next charge coming back

```lua
if fireBlast.nextChargeCD <= gcd then
  -- blablablabla
end
```

## cost

> The resource cost(s) of the spell, returned as a meta object including all resource costs.

- You can check costs by [powerType](toolbox-general?id=powertypes) `spell.cost[powerType]`
  - though actual stored keys are identical to `GetSpellPowerCost` returns
- Values are stored as numbers
- If the indexed powertype is unknown, it will return 0.

```lua
if spell.cost.energy < 25 then
  spell:Cast()
end

-- combo points
if spell.cost.cp > 3 then
  -- blbllalb
end

if spell.cost.astralPower > 50 then
  --bla
end
```

## damage / effect

> Attempts to scrape damage / healing effect of the spell from its tooltip

- `damage` and `effect` are different keys for the same attribute.
- For combo-point spending abilities, grabs the 1 combo point damage, and multiplies that by current combo points.
- For other spells, it essentially just grabs the biggest number in the tooltip and returns that.
- **Does not account for versatility, armor, defensive buffs, etc** - although buffs that alter damage or stats do seem to affect tooltip damage values.. it's very tough to accurately estimate damage of a spell, but can be done.

```lua
-- executie
if target.health < spell.damage then
  if spell:Cast(target) then
    awful.alert("EXECUTE!!!!", spell.id)
  end
end

-- healing stuff??
if spell.effect < friend.missingHealth then
  spell:Cast(friend)
end
```

## flying

> Checks missiles OM and combat log event history to determine whether or not a spell is still in the air.

- Currently experimental, big changes to come soon.

```lua
if not pyroblast.flying then
  fireBlast:Cast()
end
```

## gcd

> The global cooldown duration the spell will incur, if any

- Most GCD incurring spells incur a base GCD of 1.5s, but some are different. For example:
  - **Mighty Bash**: 1500ms Base GCD / Haste Modifier
  - **Rake**: 1000ms Base GCD / Haste Modifier

```lua
if moreImportantSpell.cooldown > lessImportantSpell.gcd then
  lessImportantSpell:Cast()
end
```

## id

> The SpellID of the spell. You provided it we're just giving it back.

## known

> Whether or not we know the spell, which can be surprisingly annoying to find out.

- `known` is an exhaustive method, requiring one of the following to have a positive return:
  - `IsSpellKnown`
  - `IsPlayerSpell`
  - `GetSpellInfo(spell.name)` - must return the spell name

```lua
if not spell.known then
  print("we know not what we think we not know, we know only the now know that we know")
end
```

## name

> The proper name of the spell

```lua
awful.alert(spell.name, spell.id)
```

## queued

> Whether or not the spell is current & queued for next GCD

```lua
if spell.queued then
  print("It's really happening!")
end
```

## range

> The max range the spell can be cast at

```lua
if target.dist < spell.range then
  awful.alert('hallelujah', spell.id)
end
```

## usable

> Whether or not the spell is currently "usable"

- Based on `IsUsableSpell` api

```lua
if spell.usable then
  spell:Cast()
end
```

## used

> Checks if the spell has been recently used by the player

- these last couple are functions btw, just too lazy to make a whole section til more spell object functions exist

```lua
if spell.used(5) then
  print("we have cast the spell in the last 5 seconds, wow!")
end
```

## inRange

> Whether or not the spell is in range of a unit

```lua
if spell.inRange(unit) then
  print("spel in range of unit, nioce")
end
```

----
pages/spell_objects/spell-object-traits.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# Setting Traits

Traits, the 2nd parameter of `awful.Spell` allows you to set a list of traits about the spell which affect how it is used.. It expects an associative array in which you can set multiple traits, just make sure they correctly apply to your spell.

```lua
-- @param {number} spellID, spell ID of the spell to create the object for
-- @param {associative array} traits, list of traits for the spell object
awful.Spell(spellID, traits)
```

## Some examples:

```lua
-- magic targeted spell, knows not to use into grounding or spell reflect among other magic immunities
awful.Spell(1234, { damage = "magic", targeted = true })

-- physical slow spell, windwalker monk "Disable" for example.. knows it can use it as long as they're not immune to physical or slow effects.. for example - physical damage can't be used into Nether Walk, but a physical effect can, so they can be disabled - but if they get freedom or blessing of protection, nope.
awful.Spell(123, { effect = "physical", slow = true })

-- magic effect, like "Spellsteal" - again can be used into something like netherwalk, but it's bad to send pyroblasts into it! that's the diff between effect and damage.
awful.Spell(13131, { effect = "magic", targeted = true })
```

# List of Traits

## diameter / radius

> **For AoE Spells** - The diameter **or** radius of the AoE reticle

- Only one needs to be defined, do whichever you prefer.
- Accepts a **number** or a **function**, in case the circumference changes based on talents or something, and may need to be updated dynamically.
- Used by [**SmartAoE**](spell-object-methods?id=smartaoe) to know its boundaries when edging or beyond-max-ranging the AoE cast.

```lua
-- 8yd diameter = 4yd radius
{ diameter = 8 }

-- 10yd radius = 20yd diameter
{ radius = 10 }

-- dynamic circumference, we expect some buff / talent will affect it
local bin = awful.bin
{
  diameter = function()
    local base, diameter = 8, 8
    -- + 25% for "increased area" buff
    diameter = diameter + bin(player.buff(23549)) * (base * 0.25)
    -- + 20% for "fake example" talent
    diameter = diameter + bin(player.hasTalent("fake example")) * (base * 0.2)
    return diameter
  end
}

```

## beneficial

> Whether or not the spell has a beneficial effect

- Causes `:Cast` to avoid facing requirement .. Most beneficial spells do not require facing.
- `:Cast` will avoid buffs/debuffs that absorb beneficial effects (e.g, Cyclone / Banish)
- This is an alternative from `heal` trait - which also checks for healing immunities specifically

```lua
{ beneficial = true }
```

## damage

> The type of damage the spell does (if any)

- Causes `:Cast` to avoid attacking into immunities against the given damage type.

```lua
-- physical damage
{ damage = "physical" }
-- magic damage
{ damage = "magic" }
```

## heal

> Whether or not the spell is a healing effect

- Causes `:Cast` to avoid facing requirement .. Most healing spells do not require facing.
- `:Cast` will avoid buffs/debuffs that absorb all healing effects (e.g, Cyclone / Banish)

```lua
{ heal = true }
```

## effect

> The effect type of the spell (if any)

- Causes `:Cast` to avoid immunities to the given effect type

```lua
-- physical effect
{ effect = "physical" }
-- magic effect
{ effect = "magic" }
```

## cc

> Whether or not the spell applies a crowd control effect

- **true**: Causes `:Cast` to avoid immunities to general CC effects (e.g, Bladestorm)
- providing specific effect type also avoids immunities to that cc type:
  - **"stun"**: avoids Icebound Fortitude, Ice Form, etc.
  - **"charm", "fear", "sleep"**: avoids Lichborne
  - **"polymorph"**: avoids Lichborne, druid forms, etc.

```lua
-- general cc
{ cc = true }

-- specifying type avoids general cc immunities, AND specific immunities
-- stun
{ cc = "stun" }
-- charm
{ cc = "charm" }
-- poly
{ cc = "polymorph" }
```

## bleed

> The spell applies a bleed effect

- Causes `:Cast` to avoid immunities to bleeds (e.g, empowered kyrian potion)

```lua
{ bleed = true }
```

## targeted

> Whether or not the spell specifically hits one unit

- Default is `true` for most spells - even if you don't set it
- Causes `:Cast` to avoid immunities to targeted damage/effects.
  - e.g, Grounding / Reflection
    - **immune** to **Pyroblast**
    - **NOT immune** to non-targeted damage/effects like **Meteor**
  - e.g, Evasion / Blur
    - **immune** to **Slam** while facing you
    - **NOT immune** to non-targeted effects like **Intimidating Shout**

```lua
-- set it to false for non-targeted spells
{ targeted = false }
-- set it to true, though this is TRUE by default!
{ targeted = true }
```

## ranged

> Whether or not the spell is cast from range

- This is currently only used for ranged physical spells
- Parry does not apply to ranged physical attacks, so declaring this lets `:Cast` know it's okay to cast the spell into effects like Die by the Sword, Riposte, or Turbo Fists while they are facing you

```lua
{ ranged = true }
```

## alwaysFace

> If `:Cast` should always force face the unit to cast, if not already facing

- Not really recommended, since you can add `face = true` to `:Cast` options situationally when needed.

```lua
{ alwaysFace = true }
```

## ignoreFacing

> Ignore facing requirement, this spell does not require facing when casting on a unit.

```lua
{ ignoreFacing = true }
```

## ignoreLoS

> Ignore LoS requirement, this spell does not require LoS when casting on a unit.

```lua
{ ignoreLoS = true }
```

## ignoreControl

> Ignore control requirement, this spell can be cast while I am in CC

```lua
{ ignoreControl = true }
```

## ignoreMoving

> This spell can be cast while moving

```lua
{ ignoreMoving = true }
```

## ignoreCasting

> This spell can be cast while casting other spells

```lua
{ ignoreCasting = true }
```

## ignoreChanneling

> This spell can be cast while channeling other spells

```lua
{ ignoreChanneling = true }
```

## stupidChannel

> This channel is worthless and my other spells can go ahead and interrupt it

```lua
{ stupidChannel = true }
```

## castByID

> This spell should specifically be cast using its ID and not its resolved named
Awful will always try to cast the spell by name, which can be an issue in Classic where downranking can be beneficial.
By adding this trait you force it to cast by ID, which will allow you to downrank a spell to a specific Rank using that ranks SpellID.

```lua
{ castByID = true }
```

----
pages/spell_objects/meta.en-US.json
{
    "spell-objects": "Creation",
    "spell-object-traits": "Traits",
    "spell-object-methods": "Methods",
    "spell-object-attributes": "Attributes"
}
----
pages/spell_objects/spell-object-methods.en-US.mdx
import Callout from 'nextra-theme-docs/callout'
import Link from 'next/link'

# What are they

Spell Objects contain some very useful methods (object-oriented functions) that allow you to cast the underlying spell in many different ways, simple and complex. They also contain methods that help organize and structure your code by spell in some super neat ways.

## Quick example

- `:Cast`, `:Callback`, and `:Castable` are all methods used below

```lua
local mortalStrike = awful.Spell(12294, { damage = "physical" })
local sharpen = awful.Spell(198817)

-- cast method
if mortalStrike:Cast(target) then awful.alert("Mortal strike!", mortalStrike.id) end

-- some callback methods
sharpen:Callback("pre-ms", function(spell)
  -- fires an alert about the pre-ms sharpen
  return spell:Cast() and alert("Sharpen Blade (MS)", spell.id)
end)

mortalStrike:Callback("prio", function(spell)
  if not spell:Castable(target) then return end
  -- pre-sharpen when MS is castable
  sharpen("pre-ms")
  return spell:Cast(target)
end)

mortalStrike("prio") -- invoking that callback function, will execute clean sharpen+ms
```

# Methods

## Callback

> Powerful organization & performance tool. Allows us to set up concise, modular sets of conditions & actions regarding the spell to be called upon elsewhere.

```lua
spell:Callback([key,]callback)
```

- When at least one `:Callback` is configured, **calling** the spell object like so `spell(key)` will **invoke the callback function behind that key** (if any)
  - You can have one Callback per spell with **no key**, to be invoked when calling the spell with no key
  ```lua
    spell:Callback(function(spell) return spell:Cast() end)
    spell() -- invoking the keyless callback
  ```
  - Other Callbacks **must have a key** to be invoked
  ```lua
    spell:Callback("test", function(spell) return spell:Cast() end)
    spell("test") -- invoking spell's "test" callback
  ```
- When **calling** your spell object, the callback function **will not be invoked if the spell is on cooldown, or you don't have the resources to use it.**
- It's possible to perform literally **any** actions within, including casting other spells, but keeping it related to the spell and the true purpose of the callback is ideal.
- The callback function is passed the spell object itself as first argument

  - It can also receive more arguments if you set up more parameters. It's a normal function!

    ```lua
    -- setting up another param "obj" in our callback function
    spell:Callback("low hp", function(spell, obj)
      -- only cast when given object exists and is below 35% hp!
      if not obj.exists or obj.hp > 35 then return end
      return spell:Cast(obj)
    end)

    -- passing 2nd arg when invoking
    spell("low hp", focus) -- cast on focus target
    spell("low hp", target) -- cast on target
    ```

- Using `:Callback` enables powerful debugging and performance tools like `hookSpellCallbacks` and `hookSpellCasts`

> See also: `Callbacks` (Shown in example below)

### Examples

```lua
-- flayed shot on best target - don't do it to hunters :P
flayed:Callback("best target", function(spell)
  if barbed_next_gcd then barbedAlert() return end
  if target.class2 ~= "HUNTER" then
    -- no blur
    if target.buff(198589) then return end
    return spell:Cast(target, {face = true}) and alert("Flayer's Mark", spell.id)
  else
    pets.loop(function(pet)
      if not pet.enemy then return end
      return spell:Cast(pet, {face = true}) and alert("Flayer's Mark (Pet - " .. pet.name .. ")", spell.id)
    end)
    enemies.loop(function(enemy)
      if enemy.bcc or enemy.isUnit(target) or enemy.isUnit(trapTarget) then return end
      -- no blur
      if enemy.buff(198589) then return end
      return spell:Cast(enemy, {face = true}) and alert("Flayer's Mark (" .. enemy.class .. ")", spell.id)
    end)
  end
end)

-- calling it in the actor
actor:Init(function()
  -- now it will find the best target and cast flayed on them!
  flayed("best target")
end)
```

```lua
-- concussive shot
local function dontConc(unit, overlap)
  overlap = overlap or 0
  return player.buff(camo.id)
  or not unit.enemy
  or unit.immuneSlow
  -- unit already in cc
  or unit.ccr > overlap
end

conc:Callback(function(spell, unit)
  if dontConc(unit) then return end
  return spell:Cast(unit) and alert("Conc " .. unit.classString .. " (Trap Pursuit)", spell.id)
end)

conc:Callback("bad position", function(spell, unit)
  if dontConc(unit, buffer) then return end

  local bpUnit = unit.isUnit(target) and enemyHealer or unit.isUnit(enemyHealer) and target or {}
  local badPosition = bpUnit.exists and (not unit.losOf(bpUnit) or unit.distanceTo(bpUnit) > 40)

  if not badPosition or unit.isUnit(bpUnit) then return end

  return spell:Cast(unit) and alert("Conc " .. unnit.classString .. " (Bad Position)", spell.id)
end)

-- now we can call these!
conc("bad position", focus) -- will use our bad positioning logic
conc(focus) -- will use our basic logic
```

### Callbacks Example

```lua
kill:Callbacks({
  anyone = function(spell)
    enemies.loop(function(enemy)
      if enemy.hp > 20 or enemy.buff(198589) then return end
      return spell:Cast(enemy, {face = true}) and alert("Kill Shot (Execute)", spell.id)
    end)
  end,
  execute = function(spell)
    if target.hp > 20 or target.buff(198589) then return end
    return spell:Cast(target, {face = true}) and alert("Kill Shot (Execute)", spell.id)
  end,
  proc = function(spell)
    if not player.buff(flayers_mark) or target.buff(198589) then return end
    return spell:Cast(target, {face = true}) and alert("Kill Shot (Proc)", spell.id)
  end
})

actor:Init(function()
  local function ks()
    return kill("anyone")
    or kill("execute")
    or kill("proc")
  end
  ks()
end)
```

## Update (old)

> Legacy version of :Callback

- less features, less performant
- kept for legacy routine compatibility

```lua
spell:Update(function(spell, key)
  if key == "test" then
    return spell:Cast(target)
  end
end)

spell("test")
```

# Casting Methods

## Cast

> Checks if the spell is castable, then attempts to cast it. Returns true if cast attempted.

```lua
spell:Cast(unit, options) -- both params are optional
```

- Accepts **[spell object traits](spell-object-traits)** as 2nd arg (1st if no unit) if you need specific instances of Cast to behave differently than others, such as force-facing the unit in important conditions.
  - There are some **alternate traits** `:Cast` accepts which spell objects do not:
  ```lua
  { stopMoving = true } -- stops moving to begin the cast
  { face = true } -- force face the object if ready to cast but not facing
  ```
- Checks that the spell is [`:Castable`](spell-object-methods?id=castable) before casting.
- Causes `:Cast` to avoid attacking into immunities against the given damage type.

```lua
-- physical damage
{ damage = "physical" }
-- magic damage
{ damage = "magic" }
```

## Castable

> Returns true if the spell is castable on given unit with given options.

```lua
spell:Castable(unit, options) -- both params are optional
```

- The following is checked (in order) to determine castability of spell:
  - **player is not falling** (only with **mustBeGrounded** trait)
  - **player has required buff(s)** (only with **requiresBuff** trait)
  - **if unit is given:**
    - **unit must exist**
    - **unit must not be dead**
    - **unit does not have roar of sac** (only with fire blast)
    - **unit is not immune** (based on **[immunity traits](spell-object-traits)**)
  - **spell cooldown must be {`<=`} [`spellCastBuffer`](toolbox-general?id=spellcastbuffer)**
  - without **pet** trait:
    - **must not be casting or channeling** (unless **ignoreCasting** / **ignoreChanneling** trait set)
  - with **pet** trait:
    - **pet must not be in cc**
  - without **pet** trait:
    - **player must not be in cc** (only without _ignoreControl_ trait)
    - **player must not be in stun** (only without _ignoreStuns_ trait)
  - **if unit is given:**
    - spell must be **known** and **usable** (enough resources, learned, etc.)
    - **if cast time of the spell is > 0:**
      - if **player is moving**, must have **one** of the following:
        - **ignoreMoving** trait
        - movable while casting buff
    - if **out of range**, must have **one** of the following:
      - **ignoreRange** trait
    - if **out of LoS**, must have **one** of the following:
      - **ignoreLoS** trait
    - if **not facing** the unit, must have **one** of the following:
      - **face** trait
      - **heal** trait
      - **ignoreFacing** trait
      - **pet** trait

```lua
if spell:Castable(target) then
  alert("accordin' to my calcurlationz, we can cast it!")
end
```

## AoECast

> Casts an AoE spell at given object or coords

```lua
-- accepts 3d coords
spell:AoECast(x,y,z)

-- or awful object
spell:AoECast(unit)
```

- Casts directly at the coords [of the unit] given
- Generally makes no modifications to the coords or anything, just does a direct cast -> click operation as smoothly as possible.
  - Some exceptions rarely apply when passing awful object, like minor bug fixes baked in for some weirdly positioned PvE bosses
- Also checks for trait-assigned immunities when given an awful object

```lua
if heroicLeap:AoECast(target) then
  alert("nice!")
end
```

## SmartAoE

> Finds ideal position to cast the spell based on current conditions and given options, then casts it.

```lua
-- passing awful object
spell:SmartAoE(unit, options)

-- passing position
spell:SmartAoE({x, y, z}, options)
```

- Returns **`{boolean}`** "casted", whether or not the AoE cast was attempted
- Checks `:ShouldCast` (basically `:Castable` without any range, LoS, or facing restrictions) before committing to the cast
- Requires [diameter or radius](spell-object-traits?id=diameter-radius) trait to function as intended
- Default Behavior:

  - Casts at the position given when possible. If out of LoS or range, will offset the cast as much as necessary to reach castability, so long as the unit or coords are still within the bounds of the AoE.

- Parameters:

  - 1.) Awful Object or a table containing `{x, y, z}` coordinates
  - 2.) Options **`{table}`**, optional - List of options that vastly alter the positioning decision of SmartAoE

    - viable options, all of which are **completely optional**. these can also be stored in your [spell object traits](spell-object-traits) as defaults for SmartAoE:

      - **offsetMin** `{number}`, minimum offset from given pos (default: **0**)
      - **offsetMax** `{number}`, maximum offset from given pos (default: **radius**)
      - **distanceSteps** `{number}`, number of steps between min and max dist (default: **24**) ...btw, the smallest allowable distance step is 0.5yd - short min/max offset deficits with lots of unnecessary distance steps will be ignored for obvious performance reasons
      - **circleSteps** `{number}`, number of positions to examine in a circle around each distance step (default: **48**) ...higher = more performance hungry but higher accuracy and precision
      - **movePredTime** `{number}`, if passed an object (or filter), will perform calculations using predicted position of object(s) based on linear movement over this duration instead of current position
      - **sort** `{function}`, when sort is configured, you can control the final sorting of valid cast positions, of which it will choose the top in the list.

        ```lua
        -- we use this later
        local dist = awful.Distance

        -- ursol spell object
        ursol = Spell(102793, {
          effect = "magic",
          slow = true,
          diameter = 8
        })

        -- ursol kill target *away* from nearby aoe defensive
        -- e.g, darkness, AMZ, earthen wall, barrier, link ..
        -- (AoEDefensive is from my routine, finding the right area trigger)
        local x, y, z = AoEDefensive.position()
        if feral.ursol:SmartAoE(target, {
          movePredTime = awful.buffer,
          sort = function(t, b)
            -- sort valid positions by furthest away from the defensive
            return dist(t.x, t.y, t.z, x, y, z) > dist(b.x, b.y, b.z, x, y, z)
          end
        })
        ```

      - **filter** `{function}`, calls this function for all OM units at each simulated cast position that still hits the primary unit/position to keep a count of **filter hits**. searches for a cast position that is below your **maxHit** threshold

        - it will call the function **several times for each enemy & friendly unit / player** in awful OM
        - you must be extremely careful what you do inside of it to avoid performance issues
        - performance is `O(n * (#units * ( distanceSteps * circleSteps )))` - **careful what you do in there**!
        - the following args are passed for each object, repeated until it finds the optimal position:
          - **argument 1**: the awful object, each is passed to your function one by one
          - **argument 2**: the object's est. distance to the cast position planned at time of query
          - **argument 3**: the planned AoE cast position itself `{x, y, z}` at time of query
        - each `true` return will add `1` to the **hit count**
        - each **non-boolean return of value** (string, number, etc,) will mark the unit to have the AoE positioned as far as possible from them, without affecting the actual hit count
        - filter function example:

        ```lua
        {
          filter = function(obj, estDist, castPosition)
            -- filter out friends who have UA for mass dispel
            if obj.friendly and obj.debuff('unstable affliction') then
              -- already in radius, we are going to hit them. return true to add 1 to hit count
              if estDist <= radius then return true end
              -- close enough to potentially hop in, should angle it away from them. returning non-bool value to tell it to avoid this unit.
              if estDist <= radius * 2 then return "avoid" end
            end
          end
        }
        ```

      - filter function specific traits:
      - **maxHit** `{number}`, the maximum acceptable filter hit count to still cast the spell (default: **0**)
      - **ignoreEnemies** `{boolean}`, do not add enemies to `units` table for filter function - will only check friends. very important to add this if enemies are not relevant to your filter function.
      - **ignoreFriends** `{boolean}`, do not add friends to `units` table for filter function - will only check enemies. very important to add this if friends are not relevant to your filter function.
      - **units** `{table}`, iterative array of awful objects, if you want to explicitly pass a list of objects to filter through (keep in mind, **you should only pass this trait at cast time**, not when initializing new spell object, otherwise the list will become stale)

### Examples:

```lua
-- ring of frost, perfectly edged on the target, and automagically casted around pillars!
local rof = awful.Spell(113724, {
  effect = 'magic',
  cc = true,
  diameter = 12,
  offsetMin = 4.5,
  offsetMax = 5.5
})
ringOfFrost:SmartAoE(focus)

-- mass dispel sheep around a corner!
local massDispel = awful.Spell(327830, {
  diameter = 30
})
massDispel:Callback('sheepy', function(spell)
  group.loop(function(friend)
    if not friend.debuff('polymorph') then return end
    if not spell:SmartAoE(friend) then return end
    return awful.alert(spell.name .. ' (Sheepy Sheep)', spell.id)
  end)
end)
massDispel('sheepy')
```

## SmartAoEPosition

> Returns the position at which **SmartAoE** has decided to cast, if any.

- **SmartAoE** is essentially just this 3 step process:
  - 1. checks `ShouldCast`
  - 2. grabs coords from `SmartAoEPosition`
  - 3. casts at the position with `AoECast(x, y, z)`

```lua
local x, y, z = spell:SmartAoEPosition(unit, options)

local x, y, z = spell:SmartAoEPosition({x,y,z}, options)
```

--END--
