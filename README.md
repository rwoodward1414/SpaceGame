# Space Delivery Game

Simple little game where you deliver items to space stations in your spaceship, while needing to manage your fuel and amount of storage space.

## Controls

Speed up - W
Slow down - S
Left - A
Right - D
Interact - E
Save - 1

Mouse moves around camera and points ship

When going slowly:
Up - C
Down - V

## Files and stuff

### Ship
BP_Ship is the blueprint file for the ship. It contains the controls and creates some of the UI.
It has variables for the amount of currency you have (units) and the storage space.

BP_Ship contains a component called MissionLog, this is a system that keeps track of current and completed missions.

### Space Stations
There are two types of space stations, one is a quest giver type and the other is a drop off type.
Station is the quest giver type, Station_DropOff is the drop off type. Station_DropOff_Planet is an instance of the drop off class and inherits all its functions.
Both impliment the I_Interaction interface.

Station contains a component called QuestGiver. This allows it to create a UI element that allows the user to view mission information and accept it.

### Mission Information
Mission information is stored in a data table called NewDataTable (because I forgot to rename it).

NewDataTable stores information of F_MissionDetials structure. F_MissionDetails has the name, information to put int he log, an array of F_StageDetails, number of stages, text for an NPC
(WB_Mission displays this text as the mission text as the idea is that you are talking to an NPC inside the station over a comm system), the current stage and the inital size of 
the delivery. 

Missions can have multiple stages or just one. F_StageDetails is the stucture for stage details, it has the name of the stage, the desciption, the payout recieved on completeing it and an 
F_Objective structure that details the objective of the stage. (It would probably make more sense for the objective information to just be part of the stage information but originally there was
to be mutliple objectives for a stage and this was an array.)

F_Objective is the stucture for objectives, this is the task that is carreid out by the player. It has the name, the ObjectiveID, the location, the size and an optional additonal size. Size is 
the amount of storage that is subtracted from the ship's used space when the objective is completed. Additional size is for if the Objective requires the player to pick up another delivery in the middle of a 
mission (which is also why mission details sets an inital size instead of just a size).

### Starting and Completeing Missions
When a player interacts with a quest giver station, the QuestGiver componet draws a WB_Mission UI to the screen. When the player clicks "accept", 
WB_Mission first checks if the ship has enough storage space and then sends the Misssion ID to BP_Ship's MissionLog component's Add Mission function.
Add Mission finds the mission information in the data table using the MissionID and adds the mission to its CurrentActive array, and then spawns a BP_MissionBase actor.
It then adds this actor to it's CurrentQuests array. After that, it adds the amount of space that the mission uses to BP_Ship's Used Space variable and then finally creates a WB_Mission_Log UI element.

BP_MissionBase is basically an instance of the mission itself. Here is where we keep track of progress and update things. It also is used to check when the mission is complete.

When the player interacts with a drop off station, the station returns it's ObjectiveID. The ship then calls an event dispatcher called OnObjectiveID. This event does nothing by itself, but it allows BP_Ship actor to share information to the BP_Mission actor, in this case, ObjectiveID. When this is called, BP_MissionBase runs a function called On Objective ID Heard. It takes the ObjectiveID and checks if it contains that 
objective. If so, it increments the objective's progress and then runs another function to check if the objective is complete. If it is, the size is subtracted from the ship's used space, a widget is drawn to the screen to tell the player it is complete and then a function to complete the stage is run. This increments the current stage, add the payout amount to the total units, checks if this was the last stage and either completes the mission or updates the mission progress for the next stage.

When the mission is completed, MissionLog runs a function and removes the mission from the current active and current quest arrays. It then re-draws the MissionLog UI.

### Saving
The game's progress can be saved by pressing '1'. This saves the CurrentActive and Completed mission arrays. 

