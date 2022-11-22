# protobuf

### Advantages
* Strictly Typed
  * Defined data type of all fields 
* Generate code for serialization & deserialization 
  * This will let us focus on the data schema and not on how data is serialized
  * In case of JSON, we need to optimize schema, think about ways to make keys and values smaller
* Schema Evolution
  * Forward & Backward compatable
* Comments
  * unlike json, it supports comments which make the documentation inline with the code itself
* Binary Serialization
  * It serializes data in binary format unlike json serilizing in string format
  * It makes data much smaller and cheaper to send
  
### Disadvantanges:
 * Binary Serialization
   * Binary data cannot be read directly, difficult to review and edit

---
### How to create protobuf message ?

First we need to create a .proto file

![Screen Shot 2022-11-22 at 12 08 35 PM](https://user-images.githubusercontent.com/108142931/203259849-587f9c1b-6b6d-46ba-8379-853518033646.png)

 1. proto syntax - Like version
 2. "message" keyword to define message
 3. "Message name"
 4. "data type" "field name"
 5. "field position/tags"
 
> Note: When you dont set a value to a field, it wont be serialized. But while deserializing , field will get default values

**Default values for scalar types:**
* int - 0
* bool - false
* string - empty string
* bytes - empty bytes

**Special field - repeated:**
* Its like collections of any type
``` 
repeated <datatype> <filedname> = <tag>;
ex: repeated string phones = 6;
```

### Importance of field TAGS

Field tags (Position) in the .proto file is key for serialization. Because serialization happens using the tags not the field names.
Some rules/tips for defining tags:
* TAGS - smallest value 1 - biggest value 536 million
* Google reserves set of tags for libraries - from 19000 to 19999
* Payload is affected by tag size, so choose ur tag wisely for your schema
  * Tag 1 to 15 - 1 Byte
  * Tag 16 to 2047 - 2 Byte
  * and so on

> Tips: Keep smallest tag numbers for most populated fields in your schema

**enum type:**
Protobuf also supports enum. Keyword is "enum"
* First tag of enum should be zero because enum tag starts with zero but as per tag rules TAG number should start with 1. Hence mark 1st tag as zero.
```
syntax = "proto3";

enum EyeColor {
    EYE_COLOR_UNSPECIFIED = 0;
    EYE_COLOR_BLACK = 1;
    EYE_COLOR_BLUE = 2;
}

message Account {
    uint32 id = 1;
    string name = 2;
    bytes thumbnail = 3;
    bool is_verified = 4;
    float height = 5;

    repeated string phones = 6;
    EyeColor eye_color = 7;
}
```
**Comments:**
* Multiline comment - /* -- */
* Single line - //
