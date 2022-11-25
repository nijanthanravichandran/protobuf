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

### Multiple message in same proto file
```
syntax = "proto3";

message message1 {
    uint32 id =1;
}

enum Enum1 {
    DUMMY_UNDEFINED = 1;
    /*
    other definitions
    */
}

message message2 {
    message1 msg = 1;
    Enum1 enum = 2;
    uint32 phone =3;
}
```

### Nested Messages
```
message message2 {
    message message1 {
        uint32 id =1;
    }
    
    enum Enum1 {
        DUMMY_UNDEFINED = 1;
        /*
        other definitions
        */
    }
    message1 msg = 1;
    Enum1 enum = 2;
    uint32 phone =3;
}
```

### Imports & Package
```
syntax="proto3";

import "message3.proto";

message message4 {
    message3 mes =1;
}
```
> message3.proto file is in same location as of message4 

Packaged proto files
```
syntax = "proto3";

package my.package;

message Message4 {
    uint32 id;
}
```
Accessing message from a package
```
syntax="proto3";

import "package.proto";

message Message5 {
    my.package.Message4 mes =1;
}
```

### Protobuf compiler installation

```
brew install protobuf
```

### Compile protos

Proto complier supports compiling files in different languages. Supported languages:

```
  --cpp_out=OUT_DIR           Generate C++ header and source.
  --csharp_out=OUT_DIR        Generate C# source file.
  --java_out=OUT_DIR          Generate Java source file.
  --kotlin_out=OUT_DIR        Generate Kotlin file.
  --objc_out=OUT_DIR          Generate Objective-C header and source.
  --php_out=OUT_DIR           Generate PHP source file.
  --pyi_out=OUT_DIR           Generate python pyi stub.
  --python_out=OUT_DIR        Generate Python source file.
  --ruby_out=OUT_DIR          Generate Ruby source file.
```
You can generate different language source file in single command.

protoc --java_out=<output dir> --python_out=<output dir> <path to proto file>

Ex: ``` protoc --java_out=java --python_out=python simple.proto ```

Directory Structure:
![Screen Shot 2022-11-23 at 9 28 19 AM](https://user-images.githubusercontent.com/108142931/203474686-ca39533f-6755-4e9a-9d1e-10bbb66e311c.png)



### Simple Proto message creation

Step 1: Create simple.proto file
```
syntax = "proto3";

message Simple {
  uint32 id = 1;
  bool is_simple = 2;
  string name = 3;
  repeated uint32 sample_list = 4;
}
```

Step 2: Complie the proto file
```
protoc -I${PROTO_DIR} --python_out=${PROTO_DIR} ${PROTO_DIR}/*.proto
```
![Screen Shot 2022-11-24 at 3 36 42 PM](https://user-images.githubusercontent.com/108142931/203775256-b47aa593-892d-4510-a04f-66dddf3c94f9.png)

Step 3: Generate message using compiled file
```
import proto.simple_pb2 as simple_pb2

def simple():
    return simple_pb2.Simple (
        id=10,
        is_simple=True,
        name="Nijanthan",
        sample_list=[1, 2, 3]
    )

if __name__ == '__main__':
    print(simple())
```
Output of above code:
```
/Volumes/Pandora/Neom/Programming/3_Python/protobuf/pycharm/venv/bin/python /Volumes/Pandora/Neom/Programming/3_Python/protobuf/pycharm/main.py 
id: 10
is_simple: true
name: "Nijanthan"
sample_list: 1
sample_list: 2
sample_list: 3
```

### Complex proto message

complex.proto
 ```
 syntax="proto3";
package example.complex;

message Dummy {
  uint32 id =1;
  string name =2;
}

message Complex {
  Dummy one_dummy = 1;
  repeated Dummy multi_dummy = 2;
}
 ```
 
 compile and generate : complex_pb2.py
 
 main.py
 ```
 import proto.simple_pb2 as simple_pb2
import proto.complex_pb2 as complex_pb2

def simple():
    return simple_pb2.Simple (
        id=10,
        is_simple=True,
        name="Nijanthan",
        sample_list=[1, 2, 3]
    )

def complex():
    message = complex_pb2.Complex()
    message.one_dummy.id = 12
    message.one_dummy.name = "nijanthan"
    message.multi_dummy.add(id=13, name="myname")
    message.multi_dummy.add(id=14, name="myname")
    message.multi_dummy.add(id=15, name="myname")
    return message

if __name__ == '__main__':
    print(simple())
    print(complex())
```
 
 output:
 ```
 /Volumes/Pandora/Neom/Programming/3_Python/protobuf/pycharm/venv/bin/python /Volumes/Pandora/Neom/Programming/3_Python/protobuf/pycharm/main.py 
id: 10
is_simple: true
name: "Nijanthan"
sample_list: 1
sample_list: 2
sample_list: 3

one_dummy {
  id: 12
  name: "nijanthan"
}
multi_dummy {
  id: 13
  name: "myname"
}
multi_dummy {
  id: 14
  name: "myname"
}
multi_dummy {
  id: 15
  name: "myname"
}
 ```
