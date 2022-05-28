# Change LED Color


## XML

```
<properties>
    <property>
        <name>On Name</name>
        <default></default>
        <type>STRING</type>
        <readonly>false</readonly>
    </property>
    <property>
        <name>Off Name</name>
        <default></default>
        <type>STRING</type>
        <readonly>false</readonly>
    </property>
</properties>

<connections>
    <connection>
        <id>500</id>
        <facing>6</facing>
        <connectionname>Toggle</connectionname>
        <type>1</type>
        <consumer>False</consumer>
        <audiosource>False</audiosource>
        <videosource>False</videosource>
        <linelevel>False</linelevel>
        <classes>
            <class>
                <classname>BUTTON_LINK</classname>
                <autobind>False</autobind>
            </class>
        </classes>
        <hidden>False</hidden>
    </connection>
    <connection>
        <id>501</id>
        <facing>6</facing>
        <connectionname>On</connectionname>
        <type>1</type>
        <consumer>False</consumer>
        <audiosource>False</audiosource>
        <videosource>False</videosource>
        <linelevel>False</linelevel>
        <classes>
            <class>
                <classname>BUTTON_LINK</classname>
                <autobind>False</autobind>
            </class>
        </classes>
        <hidden>False</hidden>
    </connection>
    <connection>
        <id>502</id>
        <facing>6</facing>
        <connectionname>Off</connectionname>
        <type>1</type>
        <consumer>False</consumer>
        <audiosource>False</audiosource>
        <videosource>False</videosource>
        <linelevel>False</linelevel>
        <classes>
            <class>
                <classname>BUTTON_LINK</classname>
                <autobind>False</autobind>
            </class>
        </classes>
        <hidden>False</hidden>
    </connection>
</connections>
```


## Lua

#### RGB to Hex
```
function RGB2HEX (rgb)
	local hex = ''
	for color in string.gmatch(rgb, "%d+") do
		hex = hex .. string.format ('%02x', color)
	end
	return hex
end
```

#### Color config (property changed)
*See more: [OnPropertyChanged](https://control4.github.io/docs-driverworks-api/#onpropertychanged)*
```
LED ['Off'] = '000000'

function ON_PROPERTY_CHANGED.OnLEDColor (value)
	LED ['On'] = RGB2HEX (value)
	C4:SendToProxy(500, "BUTTON_COLORS", {ON_COLOR = {COLOR_STR = LED ['On']}, OFF_COLOR = {COLOR_STR = LED ['Off']}}, "NOTIFY")
	C4:SendToProxy(501, "BUTTON_COLORS", {ON_COLOR = {COLOR_STR = LED ['On']}, OFF_COLOR = {COLOR_STR = '000000'}}, "NOTIFY")
end

function ON_PROPERTY_CHANGED.OffLEDColor (value)
	LED ['Off'] = RGB2HEX (value)
	C4:SendToProxy(500, "BUTTON_COLORS", {ON_COLOR = {COLOR_STR = LED ['On']}, OFF_COLOR = {COLOR_STR = LED ['Off']}}, "NOTIFY")
	C4:SendToProxy(502, "BUTTON_COLORS", {ON_COLOR = {COLOR_STR = LED ['Off']}, OFF_COLOR = {COLOR_STR = '000000'}}, "NOTIFY")
end
```

#### Trigger
STATE: Color index in Proxy Params
```
C4:SendToProxy (500, 'MATCH_LED_STATE', {STATE = '1'})
C4:SendToProxy (501, 'MATCH_LED_STATE', {STATE = '1'})
C4:SendToProxy (502, 'MATCH_LED_STATE', {STATE = '0'})
```