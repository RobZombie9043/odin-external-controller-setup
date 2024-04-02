# odin-external-controller-setup

---

Work arounds to make the docking experience with the Odin2 more seamless (switch-like).

Some emulators can auto-detect controllers and configure inputs automatically (e.g. Retroarch, PPSSPP) but others require some more work to setup.

---

## AetherSX2/NetherSX2
AetherSX2 allows you to bind multiple controllers inputs to the same profile simultaneously so no profile swapping is necessary once set up 
- go to Controller Settings
- select Controller Port 1
- set up the normal Odin controller bindings if not already done
- connect external controller 
- long press on a binding
- click add new
- add your extra controller binding
- repeat for all other bindings

Now controls should be working for Odin handheld play and for external controller.

![AetherSX2](AetherSX2/AetherSX2-add-new.png)
---

## Dolphin
Dolphin allows you to bind multiple controllers inputs to the same profile simultaneously so no profile swapping is necessary once set up, however this takes a little more work than for AetherSX2
- tap the settings cog
- select GameCube Input
- tap the setting cog next to GameCube Controller 1
- change device to Android/0/Virtual
- toggle on 'Create Mappings for Other Devices'
- select the 3 dots next to button A
- select your first device in dropdown
- scroll down and select Button A
- tap in the expression box to open keyboard and add a | (the vertical bar symbol acts as an OR operator in Dolphin)
- select device again and choose the external controller
- scroll down and select Button A again
- rinse and repeat for all control bindings and a similar process can be followed for Wii input

The expression box should then look something like this (dependent on your controller device names) when set up:
'Android/1/Xbox Wireless Controller:Button A' | 'Android/3/DualSense Wireless Controller:Button A'

Example config files for [GameCube Controller](https://github.com/RobZombie9043/odin-external-controller-setup/blob/main/Dolphin/GameCube%20-%20Odin%20(xbox%20profile)%20and%20DualSense%20(Odin%20profile).ini) and [Wii Classic Controller](https://github.com/RobZombie9043/odin-external-controller-setup/blob/main/Dolphin/Wii%20Classic%20Controller%20(Odin%20(xbox%20profile)%20and%20DualSense%20(Odin%20profile)).ini)
Note these have been setup with the Odin2 using the xbox controller profile (from Odin settings menu) and a DualSense controller using the odin controller profile (from Odin settings menu). Shared ini files as an example but these would need to be changed to suit other controllers and setup configurations. 

![Dolphin-Controller-1](Dolphin/Dolphin-GameCube-Controller-1.png)
![Dolphin-Configure-Input](Dolphin/Dolphin-GameCube-Configure-Input.png)
![Dolphin-Controller-Setup-Complete](Dolphin/Dolphin-GameCube-Controller-complete.png)
---

## Yuzu
Yuzu does not allow binding of multiple controller inputs but does include a controller auto-mapping function but this requires you to go into the controls menu and auto-map the controller each time this is changed.
An alternative work around is to set up Tasker to trigger on a controller connection event to run a task that edits the controller details in the config.ini file that Yuzu reads.
Note this only works if Yuzu is not running as the config file is loaded on app start up.
In Tasker:
- In the Profiles screen -> Click the + button to add a profile
- To set up the trigger for connection of bluetooth controller:
  - Select State -> Select Net -> Select BT connected
    - Click the magnifying glass next to Name -> Select BT Device from pop up menu
    - Click the magnifying glass next to Address -> Select BT Device from pop up menu
    - Go back
- To set up the task to change the config.ini file to use controller settings:
  - Select New Task -> Give the task a name
  - Click the + button to add an action
    - Select File -> select Read file
      - Click the magnifying glass and select the config.ini file in the following location: Android/data/org.yuzu.yuzu_emu/files/config/config.ini
      - In the To Var box add %text
      - Go back
    - Add new task -> select Variable -> select Variable Search Replace
      - Add text strings to the 'Search' and 'Replace With' boxes for what needs to be changed
      - What needs to be changed will need to be identified from the existing config.ini file - check this when Odin controller is setup and when external controller is connected to see what changes
      - e.g. for my setup the line that shows player_0_button_a="button:96,guid:00000000000001110000000000002020,port:0,display:Odin Controller 0,engine:android" changes to player_0_button_a="button:96,guid:00000000000001110000000000002020,port:0,display:DualSense Wireless Controller 0,engine:android" and similiar for other button configs
      - For the example above I needed to set the task Search to 'display:Odin Controller 0' and Replace With: 'display:DualSense Wireless Controller 0'
      - For other setups the guid number and/or port number used may also need to be changed in which case you would need to set up additional task steps for this following the same Variable Search Replace structure
    - Add new task -> select File -> select Delete File -> click magnifying glass and select the config.ini file in the following location: Android/data/org.yuzu.yuzu_emu/files/config/config.ini (this step is needed as Tasker could not overwrite the existing file without first deleting it)
    - Add new task -> select File -> select Write File -> click magnifying glass and select the config.ini file in the following location: Android/data/org.yuzu.yuzu_emu/files/config/config.ini (this step is needed as Tasker could not overwrite the existing file without first deleting it) -> in Text box add %text
  - Go back to Tasks and add a new task
- We then need to set up anotehr task to reverse the changes made to the config.ini file on controller disconnect:
  - Follow all the steps above to set up the Task but reverese the Search and Repalce With strings to return the controls to normal for Odin handheld play 
   
Example Tasker Project file for compeleting the [Yuzu config ini changes](https://github.com/RobZombie9043/odin-external-controller-setup/blob/main/Yuzu/Yuzu_ini.prj.xml)
The BT address and Search and Replace With commands would need to be modified to suit other controllers and setup configurations. 
