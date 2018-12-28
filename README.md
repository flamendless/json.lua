# ![json.lua](https://cloud.githubusercontent.com/assets/3920290/9281532/99e5e0cc-42bd-11e5-8fce-eaff2f7fc681.png)  
A lightweight JSON library for Lua

# This fork is to support [brinevector](https://github.com/novemberisms/brinevector)
* Encoding works, but decoding does not (I can't quite understand the decoding part) so here's what I do in my project to work around that:
```lua
local file = love.filesystem.read("entity_data.json") --read the file
local data = JSON.decode(file) --decode that file

--recursively/manually set the variables needed to be in brinevector Vector2 data
--this is the sample data I want to have after decoding:
local Data = {
	test = {
		UNITS = {
			grid_pos = Vector2(3, 3), --decoded is 'grid_pos = { 3, 3 }' which is a normal table
			offset = Vector2(32, 32),
		}
	}
}

for state, a in pairs(data) do
	for ent_type, b in pairs(a) do
		for id, c in pairs(b) do
			c.grid_pos = Vec2(c.grid_pos[1], c.grid_pos[2])
			c.offset = Vec2(c.offset[1], c.offset[2])
		end
	end
end

--Finally set your data to the modified data
self.data = data
```

## Features
* Implemented in pure Lua: works with 5.1, 5.2, 5.3 and JIT
* Fast: generally outperforms other pure Lua JSON implementations
  ([benchmark scripts](bench/))
* Tiny: around 290sloc, 9kb
* Proper error messages, *eg:* `expected '}' or ',' at line 203 col 30`


## Usage
The [json.lua](json.lua?raw=1) file should be dropped into an existing project
and required by it:
```lua
json = require "json"
```
The library provides the following functions:

#### json.encode(value)
Returns a string representing `value` encoded in JSON.
```lua
json.encode({ 1, 2, 3, { x = 10 } }) -- Returns '[1,2,3,{"x":10}]'
```

#### json.decode(str)
Returns a value representing the decoded JSON string.
```lua
json.decode('[1,2,3,{"x":10}]') -- Returns { 1, 2, 3, { x = 10 } }
```

## Notes
* Trying to encode values which are unrepresentable in JSON will never result
  in type conversion or other magic: sparse arrays, tables with mixed key types
  or invalid numbers (NaN, -inf, inf) will raise an error
* `null` values contained within an array or object are converted to `nil` and
  are therefore lost upon decoding
* *Pretty* encoding is not supported, `json.encode()` only encodes to a compact
  format


## License
This library is free software; you can redistribute it and/or modify it under
the terms of the MIT license. See [LICENSE](LICENSE) for details.

