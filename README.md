# protobuf

## Advantages
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
  
## Disadvantanges:
 * Binary Serialization
  * Binary data cannot be read directly, difficult to review and edit
