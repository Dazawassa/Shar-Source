# Disclaimer
This page is referring to known bugs with the video game itself. This page isn't an FAQ for any problems you are encountering with the source code itself.

_The following list was written by aldelaro5. I have made minor changes to the text but it is largely still their writing. The original can be seen [here](https://pastebin.com/Sx6SAjEf)._

# Fail Glitch

In 4 - 5 it is possible to fail the mission without then failing the following condition. This has been referred to as the "Fail Glitch". This is the result of an oversight in the code. In this missions MFK file the follow condition is set to allow the player to be up to 275 units away from the Sedan before it meets a fail condition.

However the mission objective _CollectDumpedObjective_ has a hardcoded condition called _CollectionCondition_. This condition is met once the player is 200 units away from a dropped collectable. This condition exists in all missions involving item drops. This condition will be met before the player gets 275 units away and thus the level fails before the bar hits 0. 
 
# 2 Cycles and 3-5 Manipulation

In 4-5 and 5-1 it is possible to have the car not only skip a drop, but then require an extra cycle to be performed until it will drop the object. The car is given a set of waypoints. With each waypoint marking a spot where the car should drop its item. When the car reaches the waypoint, instead of the car simply dropping the collectable. The game instead generates a value between 0 and 5000. This being the number of milliseconds before the item is then dropped.

The issue present is that it is possible for the computer to generate a time that is longer than the time it takes for the car to reach the next waypoint. If the time before dropping is longer than the time to hit the next waypoint then the game will ignore the next drop. Because this drop has been ignored the car then needs to continue on its path until it hits the waypoint a second time. Giving it a second chance for the drop.
 
This can be exploited in speedrunning. Notably in 3 - 5 where crashing in front of snakes car before he hits the ramp can avoid the risk of the item spawning in mid air and stopping the player from needing to drive around the damn after hitting the ramp themselves.
 
# WWII Car Dupe

The World War II car can be duplicated in missions that require it to be used. This introduces a number of oddities. With the most notable being in 7-7 where the car speed is increased. Reason for this is that the player is put into the prop car seen before the mission rather than the car spawned through the phone booth. AI cars constantly use the standard gas scale and not use the high speed gas scale when it is at high speed. Since the World War II car has a slower high speed scale it makes the car faster than usual.
 
# Baby Van

In 5-3 there is a 50% chance that the car will drive in a strange way that isn't seen in most other vehicles. This is caused by a glitch in the P3D file where the game will load garbage data along side the model. This garbage data. This garbage data is treated like a value for the mass of the car centre. Which then results into the strange driving.
 
# Iris Wipe

The Iris wipe is a transition used to transition between states of the game. Example being going from driving a car to walking. The game does not pause itself when this transition is played. Which is then problematic as the game will often load and deload assets during this transition. You can abuse this to walk through walls by entering a car as it teleports. By doing this in 2-4 you can trick the game into loading the Collison used when the player is in a car when the player is not in one. Which then allows the players to move through walls. Spawning some cars from the phone booth can cause a crash in this state with a stack overflow.
 
# Early Fails on Race Missions

The game will end a race if the AI hasn't moved for longer than 20 seconds. If the player is 300 units or more behind the AI they are not able to move. Which then causes that level fail trigger.

# Enter Skip Storage Continue writing from here.
 
It is possible to "store" a "[ENTER] Skip" function to the next pan in animation that occurs when a mission warp is performed which saves a considerable amount of time. To do it, one needs to call any car from a phone booth and during the pan in animation, press the change camera button which will change the current camera to the mouselook mode. The cause is very simple: the game has a function to allow or disallow camera toggles depending on the current camera mode, Although they correctly disallowed the ANIMATEC_CAM, they forgot to disallow RELATIVE_ANIMATED_CAM which is the one used for the phone booth animation thus allowing to toggle to the next camera which happens to be the mouselook mode. The reason for the enter skip is because that same animation, the game allows you to skip it by pressing enter. The game accomplishes this by turning a global flag to on, then off when it's done, but the camera toggle prevents this so the flag is still on and it can be used for the next camera pan in.
 
Camera pan-in skip (not to be confused with enter skip storage)
 
If you start a countdown mission, either cancel or finish it, then call a car from a phone booth, the next pan-in animation on mission warp will have no black bars and it's possible to completely skip it by pausing and unpausing, an action not normally possible. The reason this happens is...honestly idk what Radical was thinking for this one because I get what is happening, but I don't understand WHY it was done this way. So, when you start a countdown mission, it sets a flag to skip the black bars on the next animation....no one knows why and this is strange because they don't seem to use it AND they never really put it back to false afterwards....questionable decisions. This is normally put back to false when you mission warp as it would switch your camera to ANIMATED_CAM, then destroy it which implies to put back the flag to false...but for some reason, it won't switch your camera with a RELATIVE_ANIMATED_CAM leaving the flag on. To do this, you simply have to set your camera to that one by just using the phone booth...seriously when I researched this, it broke me because of how dumb this was.
 
Bust glitch
 
When you get busted on foot (NOT in car), the game does a fade to black before fading back in. You can pause the entire time which can mess this because when it fades to black, it suspends the current context and it's only when fading back in that it resumes it. So you can pause during the fade to black and main menu warp to STILL have the context suspended. Suspending the context will block all update to the current gameplay manager which in normal gameplay is the mission manager. It's only useful if combined with the camera pan-in skip because if you would warp to a mission from the main menu with the context suspended, it would resume it as part of the pan-in animation, but it won't do that if you had a camera pan-in skip active. This allows to keep the context suspended in regular gameplay and a lot of interesting things happens.
 
One of them is actually the case in the bounty video: the coin grinding walking through walls. If you talk to a race mission NPC with the context suspended (NOT a wager race one), it will handle the event by setting a flag in the mission manager so it can itself handle it on the next update, which won't happen right away because the context is suspended. From there, if you get into a car, the game tries to put you in the car, but that also resumes the context so now the dialogue begin which forces you out of the car...except you can still control the car. With this, if you mission warp, you don't have collision and I still don't know WHY it disables collisions, but one thing I know is that it's NOT the same reason as the walking through walls you can get in 2-4 and it seems to more relate to the state of the player, not the flag that tells if you are in a car or not. This was seemingly complex to find so I stopped there, but the fact you can get a context suspended and queue up stuff like that might be usefull.
 
This is currently used in the current coin grinding setup.
 
Cutscene skip
 
You can skip the FMV after 2-7, 5-7 and 7-7 (that last one is done in 100%). To do so, simply pause with rather precise timing (depends on the FPS cap) after the game moves on to the next mission stage, but before the FMV starts. From there, you can warp to the next level since the game already marked the level as completed in the CSM. For 7-7, you could warp somewhere to not watch the cutscene. This has been known for a long time.
 
 
 
Following this, I will now list glitches and stuff that aren't known much and I haven't investigated yet; either because I wanted to do coins before or that it would be too weird to deal with.
 
Cutscene glitch
 
I know very little about this one so I will quote Liquid's word from the bounty video's description:
 
"Cutscene glitch is performed by skipping a mission that has a cutscene at the end of it (bart 1, bart 2 or Apu) by entering an interior. After you enter the interior, select skip, then walk out of the interior, you will have no control. If you pause you cannot unpause. If you change camera to "mouselook mode" then you regain control if you warp to a different mission in the same level. If you warp away to another level, it will have a similar effect as to what happens with busted glitch. This seems to be a bit more delayed and is not entirely understood yet. This glitch is currently useless"
Could be worth investigating.
 
Control multiple car glitch
 
In a countdown mission, say 3-3, if you get out of the car (pause unpause during the countdown will let you get out) then get into a traffic car, you will fail the mission, but if you retry, you are now able to control BOTH your car AND the traffic car you entered in, even if that car likely has changed places. You may repeat this however you want and there's a video of it in action here: https://www.youtube.com/watch?v=BW-4Z0fZ92k .  Nothing is known of why this happens yet, but I plan to research that.
 
Timer freeze glitch
 
In 6-6, if you get into an interior after the mission starts, it will fail, but then if you retry, you have the time frozen. In fact, this seemed to affect more as it was reported that other stuff was frozen. I am planning to research this as it sounds very intriguing.
 
Wonky driving
 
ANY car in the game can have this issue, including AI or player. The driving from people who got into a car with "wonky" driving is reported to have "sensitive steering", but it happens completely randomly. It was thought that the baby van problem was related, but unfortunately, no connection could be made. Since it's so difficult to replicate, no serious research has been done on this.