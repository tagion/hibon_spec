# Converting between HiBON and JSON

To secure than HiBON is hash invariant when HiBON is converted back and forth between HiBON and JSON. The JSON must flow the format described below. 

A HiBON object must be generated as a JSON object and a HiBON array must be generated as a JSON object. HiBON data types must be generated as a JSON array with two element where the element index 0  is HiBON type as a string and element index 1 is the contains the value.

Except for HIBON type STRING and BOOLEAN which uses the JSON type directly and the JSON null type is converted to a empty HiBON object.

An extra array element is added for HiBON type HASHDOC, CRYPTDOC and CREDENTIAL which is the sets the data block type. 



| Type name | HiBON Type | Value format     | JSON Type      |
| --------- | ---------- | ---------------- | -------------- |
| "f32"     | FLOAT32    | hex_float        | string         |
| "f64"     | FLOAT64    | hex_float        | string         |
| "i64"     | INT64      | signed           | string         |
| "u32"     | UINT32     | number\|unsigend | number\|string |
| "u64"     | UINT64     | unsigend         | string         |
| "bool"    | BOOLEAN    | bool             | bool           |
| "utc"     | UTC        | unsigend         | string         |
| "ibig"    | INTBIG     | signed \| base64 | string         |
| "ubig"    | UINTBIG    | unsigned\|base64 | string         |
| "*"       | BINARY     | base64 \|  hex   | string         |
| "#"       | HASHDOC    | base64 \| hex    | string         |



## Value format

This table shows the valid formats describe as regular expression.

| Value format | Regular expression (in D std.regex syntax)                   |
| ------------ | ------------------------------------------------------------ |
| hex_float    | `[-]?0[xX][0-9a-fA-F]+(\.[0-9a-fA-F]+)[pP][+-]?[0-9a-fA-F]+` |
| signed       | `-?(0[xX][0-9a-fA-F]+|[0-9]+)`                               |
| unsigend     | `(0[xX][0-9a-fA-F]+|[0-9]+)`                                 |
| hex          | `0[xX][0-9a-fA-F]+`                                          |
| base64       | `@[A-Za-z0-9\+\/]+[=]*`                                      |

 

## JSON Compliant rules

