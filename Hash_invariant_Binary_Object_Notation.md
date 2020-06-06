# Hash invariant Binary Object Notation

HiBON is a streamable data format which are able to contain common binary data types.

Pronounced Haibon

 ## Description of the binary package format

The HiBON  binary format describe here in pseudo-BNF format. 

### Basic Types

| Type name | Type description                                         |
| --------- | -------------------------------------------------------- |
| int       | signed 32 integer                                        |
| long      | signed 64 integer                                        |
| uint      | unsigned 32 integer                                      |
| ulong     | unsigned 64 integer                                      |
| float     | 32-bit IEEE 754-2008 binary floating point               |
| double    | 64-bit IEEE 754-2008 binary floating point               |
| utf8*     | is defined as a string of characters in UTF-8 format     |
| char*     | is defined as a string of characters in ASCII format     |
| byte*     | is defined as a string of bytes                          |
| i32       | signed 32 integer in leb128 format                       |
| i64       | signed 32 integer in leb128 format                       |
| u32       | unsigned 32 integer in leb128 format                     |
| u32       | unsigned 64 integer in leb128 format                     |
| f32       | float in little endian format                            |
| f64       | double in little endian format                           |
| len       | is a length file as u32 except the '\x00' is not allowed |
| null      | is defined as '\x00'                                     |



```
document   ::= null | len list        // len in bytes contained in the list
                                      // null value means the the document is empty
list       ::= element list
key        ::= key_index | key_string // Member key either as a u32 or text
element    ::=                        // TYPE key value
      FLOAT64 key f64  
    | FLOAT32 key f32 
 	| STRING key string
 	| DOCUMENT key document
 	| BINARY key binary
 	| BOOLEAN key ('\x00'|'\x01')
 	| UTC key u64
    | INT32 key i32
    | INT64 key i64
    | BIGINT key ibig
    | BIGUINT key ubig
    | UINT32 key u32
    | UINT64 key u64
    | CUSTOM key document // document is array where the first element is
                          // contains the name of the type and the the following contains
                          // value
                          // ex.
                          // ["u32[]", binary]
    | HASHDOC key hashdoc // Is the hash pointer to a HiBON
    | VER u32             // This field sets the version
    | RFU
    | ERROR
// Data types
string     ::= len utf8*       // Array of UTF-8 containg len elements
binary     ::= len byte*       // Array of byte containg len elements
// All number types is stored as little endian
u32        ::= leb128!uint     // leb128 decoded to a 32 bits unsigend integer
i32        ::= leb128!int      // leb128 decoded to a 32 bits sigend integer
u64        ::= leb128!ulong    // leb128 decoded to a 64 bits unsigend integer
i32        ::= leb128!long     // leb128 decoded to a 64 bits sigend integer
f32        ::= decode!float    // 32 bits floatingpoint
f64        ::= decode!double   // 64 bits floatingpoint
ibig       ::= len uint[] sign // Contains a big-integer value stored on multible of 4 bytes which represents
                               // unsigned integer in little endian format and the sign
                               // Only valid if ( len % 4 == 1 && len >= 4 )
sign       ::= '\x00' | '\x01' // Set the sign of the bigint (none two complement)
ibig       ::= len byte*       // Unsigne big-integer
                               // Only valid if ( len % 4 == 0 && len >= 4 )
binary     ::= len ubyte*      // Byte array of the length len
string     ::= len char*       // utf-8 array of the length len
hashdoc    ::= u32 binary      // The first filed set the hash function and binary is the hash-value
// Length fields
len        ::= leb128!uint     // Same a u32 except null value is accepted
null       ::= '\x00'
// key format
key_index  ::= '\x00' u32     // Defined the key as an unsigend 32 bits number used for document arrays
key_string ::= len key_text   // Is a key subset of the ascii see rule 1. 
// Type codes
FLOAT64    ::= '\x01'
FLOAT32    ::= '\x21'
STRING     ::= '\x02'
DOCUMENT   ::= '\x03'
BINARY     ::= '\x05'
BOOLEAN    ::= '\x08'
UTC        ::= '\x09'
INT32      ::= '\x10'
INT64      ::= '\x12'
UINT32     ::= '\x20'
UINT64     ::= '\x22'
HASHDOC    ::= '\x23'
IBIG       ::= '\x1B'
UBIG       ::= '\x2B'
CUSTOM     ::= '\x23'
VER        ::- '\x3F'
// Following types must result in an format error
RFC        ::= '\x40' | '\x7e' | '\x80' | '\xC3' | '\xFE' | '\xC2' | '\x13'
ERROR      ::= others 
```



## Compliment rules

#### Rules for key objects and array

1. A HiBON package is defined as complete Document including the first length 'len'.
2. An empty HiBON defined with a size of 1 byte and the value of '\x00'.
3. The member key can be either an index as a u32 number or as a ASCII text.
4.  If the len of the key has the value '\x00' then the key is u32 number.
5. if then len of the key has a value greater than zero then the key is represented as an ASCII string of the length len.
6. An HiBON is defined as an Array if all the keys is a number u32. 
7. If one or more keys is not a u32 number then the HiBON is defined as an Object.
8. If the HiBON is empty the it is defined as both an Object and Array.
9. All keys most be ordered according to the **key-ordering** algorithm
10. All keys most comply with **key-valid** algorithm
11. A keys is defined to be an index according to the **is-index** algorithm
12. The VER filed most be the first field in the recorder.
13. The VER of the value '\x00' is not allow. 
14. If the version filed is not available the HiBON version is the same as the parent HiBON.
15. If the VER field is not set the default version is zero.

#### Rules for types

1. BOOLEAN type must only contain '\x00' for false and '\x01' for true other values are not allowed

2. The size of a BOOLEAN is one byte

3. The size of a STRING can be zero or more

4. The size of a BINARY can be zero or more

5. UTC is in UNIX ...

6. The size of BIGUINT must be a multiple of 4 bytes

7. The size of BIGINT must be a multiple of 4 bytes plus one of the signed

8. The last byte in BIGINT format is the sign byte

9. The sign byte in BIGINT format must only contain '\x00' for positive value and '\x01' for negative value other values are not allowed

10. The HASHDOC contains the hash pointer to a HiBON the u32 value selects the hash function type (null is sha256)

11. The user time must alway contain a Document

    

