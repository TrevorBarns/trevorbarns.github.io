---
title: Trevor Barns' Cookbook
description: FiveM resource development tips, methods, and tools. Blog on things I find interestings.
layout: default
---

## Writing File to Server in FiveM
Saving data to a file can be useful if the data is relatively static and requires some overhead to generate. Instead of running a loop to rebuild data evertime a client loads in, it can be more efficient to read it from a file. Some items that may be useful to read from file: table of prices for a merchant script, postal numbers, table of controls, etc.

### Via Serverside Function
If you wanted to encode a lua table to JSON and store the data for later retrieval:
```lua
--server.lua
function WriteTableToFile(filename, table)
	file = io.open("resources/[scripts]/"..GetCurrentResourceName().."/"..filename, "w") --"w" for write (overrides existing data)
	if file then
		file:write(json.encode(table))
		print("Writing to: ..resources/[scripts]/"..GetCurrentResourceName().."/"..filename)
	else
		print("Log file couldn't be created")
	end
	file:close()
end
```

Append text to an existing (or create a new) file:
```lua
--server.lua
function AppendTextToFile(filename, text)
	file = io.open("resources/[scripts]/"..GetCurrentResourceName().."/"..filename, "a")    --"a" for append
	if file then
		file:write(text)
		print("Writing to: ..resources/[scripts]/"..GetCurrentResourceName().."/"..filename)
	else
		print("Log file couldn't be created")
	end
	file:close()
end
```

To read data from file on either client or server side:
```lua
local file_contents = LoadResourceFile(GetCurrentResourceName(), 'PLUGINS/extra_controls/controls.json') --where 'PLUGINS/extra_controls/controls.json' is listed in fxmanifest under files. In this case it's a JSON file, we can load that into a lua table using json.decode(string).
local table_from_file = json.decode(file_contents)
```

If it were a text document, you might want to parse the data using newline characters (`/n`):
```lua
local file_contents = LoadResourceFile(GetCurrentResourceName(), 'foo.txt') 
for line, newline in file_contents:gmatch"([^\r\n]*)([\r\n]*)" do
   print(line)
end
```

### Via Event (Development Purposes Only)
> ⚠️ The below code is not safe for production use. This should only be used for development pursposes. The event allows malicious players to write potentially harmful data to the server.  

The event below is stored on a file that I deploy when needed for quickly saving tables to JSON files for later loading, an event is used since clients cannot directly write to the server, for obvious reasons. 
```lua
RegisterServerEvent('sv:WriteToFile') 
AddEventHandler('sv:WriteToFile', function(filename, table)
	file = io.open("resources/[scripts]/"..GetCurrentResourceName().."/"..filename, "w")
	if file then
		file:write(json.encode(table))
		print("Writing to: ..resources/[scripts]/"..GetCurrentResourceName().."/"..filename)
	else
		print("Log file couldn't be created")
	end
	file:close()
end)
```
* * *

## Detecting Keyboard vs. Controller Input
The native has been renamed a number of times but can be found [here](https://docs.fivem.net/natives/?_0xA571D46727E2B718), depending on your enviroment `IsInputDisabled`, `GetLastInputMethod`, and `IsUsingKeyboard` are equvilant.

In the example below, control index 10 refers to _pageup_ or _left-trigger_ on controller. By using `IsInputDisabled` we limit input to keyboard only preventing the left-trigger activation on controller.
```lua
Citizen.CreateThread(function()
  if IsInputDisabled(0) and IsControlJustPressed(0,10) then
    print("do something here")
  end
  Citizen.Wait(0)
end)
```
* * *

##  Lua Demo Online 
I highly recommend checking out [Lua Demo](https://www.lua.org/cgi-bin/demo), it allows you to run lua code online quickly. It does not have access to other libraries like JSON or CFX natives. But, can debug and experiment quickly with base lua functionality think tables, string manipulation, variable scoping. This is much faster than modifiying resource code, ensuring, alt tabbing every time you are trying to debug some functionality. I often replace data I would get from CFX natives with placeholder data.
![LuaDemo](https://i.gyazo.com/ea9463d125fdd7f0163d3ee3bfe93175.png)

* * *


Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.


```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```

