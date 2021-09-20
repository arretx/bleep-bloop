This guide is specific to my configuration, but feel free to use any of these ideas for your projects.

## Flow #1: Set Variables

### Function Node 1: Global States / Services

The following function creates a Global context variable containing an object that has all of the home assistant entity states and services and can be used anywhere in NodeRED.

#### Node Exported 

```
[{"id":"952152fe.317","type":"function","z":"4dc1f2f2.a22a4c","name":"Global States / Services","func":"// The following two lines create Global context variables which contain all of the home assistant entity states and services\n//and can be used anywhere in NodeRED.\n\nglobal.set(\"states\", global.get('homeassistant').homeAssistant.states);\nglobal.set(\"services\", global.get('homeassistant').homeAssistant.services);","outputs":1,"noerr":0,"x":430,"y":40,"wires":[[]]}]
```

#### Function Script

```
// The following two lines create Global context variables which contain all of the home assistant entity states and services
//and can be used anywhere in NodeRED.

global.set("states", global.get('homeassistant').homeAssistant.states);
global.set("services", global.get('homeassistant').homeAssistant.services);
```
### Function Node 2: Global Variables: Sound FX

In the HASS.io `/config/www` folder, I've created a folder called `/sounds` which contains many sub-folders and audio files used througout home assistant just to make things more interesting.

#### Exported Node

```
[{"id":"c89aa49c.b07428","type":"function","z":"4dc1f2f2.a22a4c","name":"Global Variables: Sound FX","func":"var soundFxPath = \"/config/www/sounds/\";\nvar soundFx = {\n    \"soundFxPath\" : soundFxPath,\n    \"soundFxCoffeePath\" : \"coffee/\"\n    };\n    \nvar soundFxMonth = global.get('calendarData').monthName;\nvar soundFxThemes = {\n    \"january\" : \"\",\n    \"february\" : \"\",\n    \"march\" : \"\",\n    \"april\" : \"\",\n    \"may\" : \"\",\n    \"june\" : \"\",\n    \"july\" : \"\",\n    \"august\" : \"\",\n    \"september\": \"\",\n    \"october\" : \"halloween/\",\n    \"november\" : \"thanksgiving/\",\n    \"december\" : \"christmas/\"\n    };\n\nvar soundFxTheme = soundFxThemes[soundFxMonth];\n\nvar soundFx = {\n    \"soundFxPath\" : soundFxPath,\n    \"soundFxCoffeePath\" : soundFxPath + \"coffee/\",\n    \"soundFxTheme\" : soundFxTheme\n    };\n\nglobal.set(\"soundFx\", soundFx);\n\nreturn msg;","outputs":1,"noerr":0,"x":440,"y":80,"wires":[[]]}]
```
#### Variables and Their Purpose

| Variable | Purpose |
| - | - |
| soundFxPath | Defines the root path to all of the sounds stored in HASS.io (`/config/www/sounds/`) |
| soundFxCoffeePath | `soundFxPath + coffee/`, where all coffee commercials are stored. (`/config/www/sounds/coffee/)` |
| soundFxMonth | Set to the name of the current month based on `calendarData` |
| soundFxThemes | an object containing the names of all of the months and their corresponding holiday or special occasion expressed as a path for seasonal sounds.  For example, `{"december":"christmas/"}` |
| soundFx | Combines the other soundFx variables into a single object.
| soundFxTheme | soundFxThemes[soundFXMonth] Returns the path associated with the current month. |
| soundFX (Global.set) | Sets a global context variable containing the contents of the `soundFx` object. |

#### Function Script

```
var soundFxPath = "/config/www/sounds/";
    
var soundFxMonth = global.get('calendarData').monthName;
var soundFxThemes = {
    "january" : "",
    "february" : "",
    "march" : "",
    "april" : "",
    "may" : "",
    "june" : "",
    "july" : "",
    "august" : "",
    "september": "",
    "october" : "halloween/",
    "november" : "thanksgiving/",
    "december" : "christmas/"
    };

var soundFxTheme = soundFxThemes[soundFxMonth];

var soundFx = {
    "soundFxPath" : soundFxPath,
    "soundFxCoffeePath" : soundFxPath + "coffee/",
    "soundFxTheme" : soundFxTheme
    };

global.set("soundFx", soundFx);

return msg;
```

### Function Node 3: Calendar Variables

I know, there are probably a thousand ways to do this but this is what I landed on.

#### Exported Node

```
[{"id":"4df65483.b36aec","type":"function","z":"4dc1f2f2.a22a4c","name":"Calendar Variables","func":"const gHomeAssistant = global.get('homeassistant').homeAssistant;\nconst monthNames = [\"january\", \"february\", \"march\", \"april\", \"may\", \"june\",\n  \"july\", \"august\", \"september\", \"october\", \"november\", \"december\"\n];\nconst dayNames = [\"sunday\", \"monday\", \"tuesday\", \"wednesday\", \"thursday\", \"friday\", \"saturday\"];\nconst d = new Date();\n\nvar monthName = monthNames[d.getMonth()];\nvar dayName = dayNames[d.getDay()];\nvar hourNumber = d.getHours();\nvar monthDay = d.getDate();\n\nif (gHomeAssistant.states['calendar.holidays_in_united_states'].state == \"off\") {\n    var holiday = false;\n} else {\n    var holiday = true;\n}\nglobal.set('monthName', undefined);\n\nvar timeData = {\n    \"monthName\" : monthName,\n    \"dayName\" : dayName,\n    \"hourNumber\" : hourNumber,\n    \"monthDay\" : monthDay,\n    \"holidayToday\" : holiday,\n    \"nextHoliday\" : gHomeAssistant.states['calendar.holidays_in_united_states'].attributes.message\n    };\n\nglobal.set(\"calendarData\", timeData);\nglobal.set(\"test1\", undefined);\nreturn msg;","outputs":1,"noerr":0,"x":410,"y":120,"wires":[[]]}]
```

#### Function Script

```
const gHomeAssistant = global.get('homeassistant').homeAssistant;
const monthNames = ["january", "february", "march", "april", "may", "june",
  "july", "august", "september", "october", "november", "december"
];
const dayNames = ["sunday", "monday", "tuesday", "wednesday", "thursday", "friday", "saturday"];
const d = new Date();

var monthName = monthNames[d.getMonth()];
var dayName = dayNames[d.getDay()];
var hourNumber = d.getHours();
var monthDay = d.getDate();

if (gHomeAssistant.states['calendar.holidays_in_united_states'].state == "off") {
    var holiday = false;
} else {
    var holiday = true;
}
global.set('monthName', undefined);

var timeData = {
    "monthName" : monthName,
    "dayName" : dayName,
    "hourNumber" : hourNumber,
    "monthDay" : monthDay,
    "holidayToday" : holiday,
    "nextHoliday" : gHomeAssistant.states['calendar.holidays_in_united_states'].attributes.message
    };

global.set("calendarData", timeData);
global.set("test1", undefined);
return msg;
```

#### Variables and Their Purpose

| Variable | Purpose |
| - | - |
| gHomeAssistant | Stores the global homeassistant object in gHomeAssistant.  I honestly am not sure this is necessary, but there it is. |
| monthNames | Creates an array of month names to be referenced wherever. |
| dayNames | Creates an array of all of the names of the days in the week. |
| monthName | Looks up the month name in the `monthNames` array by index based upon the actual month number. (0 - 11) |
| dayName | Looks up the day name in `dayNames` based upon the number of the day of the week. (0 - 6) |
| hourNumber | Stores the hour by number (0 - 23)|
| monthDay | Stores the day of the month expressed as an integer (0 - 30/31). |
| holiday | A boolean set by a google calendar using the HA calendar integration.  If it's a holiday, the state of the calendar `calendar.holidays_in_united_states` will be "on", thus, the variable `holiday` will be `true`.|
| timeData | An object that combines all of these variables to be stored as a global context variable. |
| calendarData | A globally set variable that contains all of the contents of `timeData` |


