# Structured Bindings Pack
Header only library for serializing C++ structs into [MessagePack](https://github.com/msgpack/msgpack/blob/master/spec.md) binary form with minimal boilerplate code. Requires C++17 and MSVC compiler (but should probably work with other compilers as well). Obviously, this is just a fun project, highly experimental, avoid using it during _rocket surgeries_.

## What is this good for?
If you need to serialize small non-POD structures (i.e., you can't just use plain `memcpy`), this library might provide good quick solution. There is no need to manually write serialization and deserialization code for each and every struct you have. There aren't even any message definition files (like `.proto` files in Google's [Protocol Buffers](https://developers.google.com/protocol-buffers)). Everything is resolved during compilation.

## Quick example
```cpp
struct UserData final
{
	int age;
	float height;
	std::string name;
	uint64_t password_hash;
	std::vector<int> lucky_numbers;
};

// Fill UserData struct with some random data
UserData ud;
ud.age = 32;
ud.height = 1.75f;
ud.name = "Jeff";
ud.password_hash = 0xDEADBEEFDEADC0DEull;
ud.lucky_numbers = { 69, 420, 1984 };

// Serialize it into a buffer
sbp::buffer buff;
sbp::write(buff, ud);
```
That's it! As long as your structs are simple enough (see limitations below), there is no need to write any extra serialization or deserialization code.

How does serialized `UserData` instance look like in memory:
```
'20 ca 00 00 e0 3f a4 4a 65 66 66 cf de c0 ad de ef be ad de 93 45 d1 a4 01 d1 c0 07'

20                      : 7-bit positive integer (32)
ca                      : 32-bit floating point number
00 00 e0 3f             : 1.75f
a4                      : 4 bytes string
4a 65 66 66             : "Jeff"
cf                      : 64-bit unsigned integer
de c0 ad de ef be ad de : 0xDEADBEEFDEADC0DE
93                      : array of 3 elements
45                      : 7-bit positive integer (69)
d1                      : 16-bit signed integer
a4 01                   : 420
d1                      : 16-bit signed integer
c0 07                   : 1984
```

## Basic supported types
- `bool`
- `int8_t`, `int16_t`, `int32_t`, `int64_t`
- `uint8_t`, `uint16_t`, `uint32_t`, `uint64_t`
- `float`, `double`
- `std::string`
- `const char *`
- `std::vector`
- `std::map`
- `std::unordered_map`
- `std::array`

## Extended types
- `sbp::ext`
- `sbp::bin`

## Adding custom types
_... TODO_

## Using `sbp::buffer`
_... TODO_

## Limitations
- library does not care about endianness
- no STL streams or allocators support (but feel free to roll your own `sbp::buffer` implementation)
- error reporting is very primitive, no exceptions used
- inheritance does not work, you must use composition instead:
- C-style arrays do not work, you must use `std::array` instead:
- if you really want to use inheritance (or even C-style arrays), you have to provide template specializations for `std::tuple_size`, `std::tuple_element` and `std::get`

## Credits
- structured binding code originally taken from [avakar/destructure](https://github.com/avakar/destructure)
- binary serialization follows [MessagePack](https://github.com/msgpack/msgpack/blob/master/spec.md) format (to some extent)
