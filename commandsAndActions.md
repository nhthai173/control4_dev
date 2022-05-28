# Commands and Actions

## XML

#### Commands
|       |                           |
|-------|---------------------------|
| NAME  | the room name             |
| PARAM1| the first parameter value |
```
<commands>
    <command>
        <name>SetState</name>
        <description>Set State to PARAM1 in NAME</description>
        <params>
            <param>
                <name>State</name>
                <type>LIST</type>
                <items>
                    <item>On</item>
                    <item>Off</item>
                </items>
            </param>
        </params>
    </command>
</commands>
```

#### Actions
```
<action>
    <name>Test Action with Parameters</name>
    <command>TestActionWithParameters</command>
    <params>
        <param>
            <name>Test STRING password</name>
            <type>STRING</type>
            <password>true</password>
        </param>
        <param>
            <name>Test STRING</name>
            <type>STRING</type>
        </param>
        <param>
            <name>Test LIST</name>
            <type>LIST</type>
            <items>
                <item>On</item>
                <item>Off</item>
            </items>
        </param>
        <param>
            <name>Test RANGED_INTEGER</name>
            <type>RANGED_INTEGER</type>
            <minimum>79</minimum>
            <maximum>120</maximum>
        </param>
        <param>
            <name>Test RANGED_FLOAT</name>
            <type>RANGED_FLOAT</type>
            <minimum>50.5</minimum>
            <maximum>60.3</maximum>
        </param>
        <param>
            <name>Test COLOR_SELECTOR</name>
            <type>COLOR_SELECTOR</type>
        </param>
    </params>
</action>
```
![](https://i.imgur.com/clJp6UT.png)
![](https://i.imgur.com/VXZcoCY.png)


## Lua Callback

Driver callback function: **ExecuteCommand**
```
ExecuteCommand (strCommand, tParams)
```

#### Commands
| Name          | Type      | Description                   |
|-              |-          | -                             |
| strCommand    | string    | Value of `<name></name>`      |
| tParams       | table     | Table of `<params></params>`  |

##### Example tPatams table
```
{
    "State" = "On"
}
```

#### Actions
strCommand = `LUA_ACTION`

##### Example tPatams table
```
tParams = {
    "ACTION" = "TestActionWithParameters",
    "Test STRING password" = "password",
    "Test STRING" = "string",
    "Test LIST" = "On",
    "Test RANGED_INTEGER" = 79,
    "Test RANGED_FLOAT" = 50.5,
    "Test COLOR_SELECTOR" = "255,0,0"
}
```

---

## Example callback function
```
EX_CMD = {} -- contains all commands


function ExecuteCommand(sCommand, tParams)
	-- Remove any spaces (trim the command)
	local trimmedCommand = string.gsub(sCommand, " ", "")
	local status, ret

	-- if function exists then execute (non-stripped)
	if (EX_CMD[sCommand] ~= nil and type(EX_CMD[sCommand]) == "function") then
		status, ret = pcall(EX_CMD[sCommand], tParams)
	-- elseif trimmed function exists then execute
	elseif (EX_CMD[trimmedCommand] ~= nil and type(EX_CMD[trimmedCommand]) == "function") then
		status, ret = pcall(EX_CMD[trimmedCommand], tParams)
	else
		-- Unhandled command
		status = true
	end
	
	if (not status) then
		-- Error: ret
	end
	return ret -- Return whatever the function returns because it might be xml, a return code, and so on
end


-- Function called for any actions executed by the user from the Actions Tab in Composer.
function EX_CMD.LUA_ACTION(tParams)
	if (tParams ~= nil) then
		for cmd, cmdv in pairs(tParams) do
			if (cmd == "ACTION" and cmdv ~= nil) then
				local status, err = pcall(LUA_ACTION[cmdv])
				if (not status) then
					-- Error: err
				end
				break
			end
		end
	end
end
```