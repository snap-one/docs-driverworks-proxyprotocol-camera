## Extras Library Use Case Examples

### Importing the library

To use this library, please move all `*.lua` files to the source directory of your driver, then import `Extras.lua` file in your `driver.lua` file. The `Extras.lua` file includes all other library files needed.

This library depends on a driver template. Please, make sure that the driver template files are included in the path.
**NOTE** Make sure paths are setup correctly

### How to use the library

#### Set Up

The code to the right is an example how to setup the extras library:

```lua
require "Extas"

function ON_DRIVER_INIT.SetupExtras()
	extras = Extras:new(DEFAULT_PROXY_BINDINGID)
	extras:addSection("ButtonLabel")
	extras:addSection("CheckboxLabel")
	local bt1Table = {
		id = "Button1ID",
		label = "Button 1 Label",
		command = "Button1Command",
		extraparams = {
			Checkbox4ID = "Extr1",
			Checkbox3ID = "Extr2",
			Button2ID = "Extr3"
			}
		}
	extras:addObjects({ExtrasButton:new(bt1Table, "ButtonText1"),
		   ExtrasButton:new({id = "Button3ID", label = "Button 3 Label", command = "Button1Command"}, "ButtonText3"),
		   ExtrasButton:new({id = "Button2ID", label = "Button 2 Label", command = "Button1Command"}, "ButtonText2"),
		   ExtrasButton:new({id = "Button4ID", label = "Button 4 Label", command = "Button1Command"}, "ButtonText4")},
		   "ButtonLabel")
	extras:addObjects({ExtrasCheckbox:new({id = "Checkbox1ID", label = "Checkbox 1 Label", command = "Checkbox1Command"}, "true"),
		   ExtrasCheckbox:new({id = "Checkbox2ID", label = "Checkbox 2 Label", command = "Checkbox1Command"}, "true"),
		   ExtrasCheckbox:new({id = "Checkbox3ID", label = "Checkbox 3 Label", command = "Checkbox1Command"}, "true"),
		   ExtrasCheckbox:new({id = "Checkbox4ID", label = "Checkbox 4 Label", command = "Checkbox1Command"}, "true")},
		   "CheckboxLabel")
	extras:sendSetup(DEFAULT_PROXY_BINDINGID)
end

```
- Section order in the CI will be in the same order section were added in the container class instance.
- Object order in the CI will be in the same order objects were added in the section.
- Remember that object could be added as separated elements or as an array of objects.
- This setup can be called in `DriverLateInit()`. Setting extras in the `DriverInit()` callback is possible but not recommended.


#### Handling Proxy Commands

The next code example shows how to write handlers for proxy commands received when extras change:

```lua
-- Proxy Command Handlers

PRX_CMD.Button1Command(proxyId, tParams)
	C4:SetVariable("Extras_Button1", os.date())
	LogDebug(os.date() .. " " .. extras:getObject(tParams.id).label .. " - Extras Button pressed")
end

function PRX_CMD.Checkbox1Command(proxyId, tParams)
	C4:SetVariable("Extras_Checkbox1", tParams.value)
	LogDebug(os.date() .. " " .. extras:getObject(tParams.id).label .. " - Extras Checkbox switched")
	extras:updateObject(proxyId, tParams.id, tParams.value)
end
```


To handle changes in extras parameters coming from the device side, the  `updateObject` method can be used. This method updates extras object instance and sends proxy notification that extras status has been changed. Also, each `ExtrasObjectBasedClass` class supports the  `ExtrasObjectBasedClass:update(value, hidden)` method that enables extras update from the class instance directly.

It is nice to create a variable for each extras object, to give the partner opportunity to monitor and take action based on extras states. Sample variable definitions are to the right.

```lua
--Variable Examples

C4:AddVariable("Extras_Button1", "", "STRING")
C4:AddVariable("Extras_Checkbox1", "", "BOOL")
```


#### Sqishy

If the Lua Squish is used, the Module code snippet below should be included in the squishy file:

```xml
-- Lua Squish Sample

Module "Extras" "extras/Extras.lua"
Module "ExtrasObject" "extras/ExtrasObject.lua"
Module "ExtrasButton" "extras/ExtrasButton.lua"
Module "ExtrasCheckbox" "extras/ExtrasCheckbox.lua"
Module "ExtrasIcon" "extras/ExtrasIcon.lua"
Module "ExtrasList" "extras/ExtrasList.lua"
Module "ExtrasSwitch" "extras/ExtrasSwitch.lua"
Module "ExtrasNumber" "extras/ExtrasNumber.lua"
Module "ExtrasSlider" "extras/ExtrasSlider.lua"
Module "ExtrasText" "extras/ExtrasText.lua"
Module "ExtrasTextField" "extras/ExtrasTextField.lua"
```

In this case, the library can be used as git submodule.


#### Removing

To remove the object or the section please use following methods:

- `Extras:removeObject(id)`
- `Extras:removeSection(sectionLabel)`

This way, you will avoid lost objects (objects that are in section array but will not be displayed because empty index before).
It is strongly recommended to hide rather than remove objects that will be temporary removed form the CI.

Before destroying an Extras class instance, please use the `Extras:cleanCapability()` method. Also, please pay attention to the Capability section of the documentation.

