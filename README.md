# Universal Binary Notation (UBN)

Universal Binary Notation file format specification (beta)

Introduction
------------
Universal Binary Notation (UBN) is a general purpose self-explained binary file format for hierarchically structured data. The basic syntax is very simple and easy to implement but it tries to satisfy the needs of all imaginable applications for binary data storage and exchange. The simplicity allows to generate typical data files by a few lines of code without any library. Simultaneously, with the use of meta information big data files can be generated that are manageable efficiently with random access.

The Vision
----------

The success of the Digital Revolution is based on the common notation of all kind of information by binary series of zeros and ones. It is therefore all the more astonishing that the idea of unification did not find its way to the syntax of binary data structures. Many thousand binary file formats exist in the world that need custom-built programs or libraries to decode the data. The leak of a unified data structure format counteracts the full advantage of digitalization.

For text files universal formats exist, like [XML](https://www.w3.org/XML/), [JSON](http://www.json.org/), [CSV](https://en.wikipedia.org/wiki/Comma-separated_values). As a drawback text formats have limited speed and storage efficiency since numerical values have to be translated into their decimal text representations and included elements cannot be read without parsing the whole text file. 

Here comes the vision of a Universal Binary Notation (UBN) that is easy to use and provides all desirable properties of binary formats. One single editor should be able to view and edit the content of any binary file format that is based on UBN. This would allow binary files to gain the popularity of text files, which can all be opened by one text-editor. Due to it's binary structure certain sub-elements can be accessed very efficiently. This would make binary files even more flexible than text-files and enable users to handle elements as intuitively as files in a directory tree of a file system.

But why again a new format? Does no common binary format exist for general purposes? There are some examples, however they suffer from too high complexity or limited versatility. Examples are [HDF5](https://www.hdfgroup.org/HDF5/) (Hierarchical Data Format) and [UBJSON](https://github.com/ubjson/universal-binary-json/) (Universal Binary Java Script Object Notation). The former is feature-rich and suitable for huge scientific data sets but has a quite complicated grammar while the latter is very simple, but is not optimized for big databases. UBN is supposed to bridge the gap.

Core Idea
---------

To unify the contradicting requirements of simplicity and advanced features, the format specification is divided into two meta levels. The core grammar describes a very simple but hierarchical data structure. Advanced features such as random access are hidden in a special grammar rule for meta information. When ignoring the meta information, the file still can be parsed with the core grammar and most of the binary data types can be understood. It is assumed that most UBN files of typical usage will not contain any meta information.

### Features of the grammar

1. Basic boolean, integer, floating point data types and strings
2. Arrays, multi-dimensional arrays
3. Lists of mixed arbitrary elements
4. Objects or dictionaries with key/value pairs for arbitrary elements
5. Arbitrary hierarchy levels
6. Unfinished files are syntactically valid as long as the last element is complete
7. Self-similarity: Inner elements can be extracted as complete and valid files

### Possible features by making use of the optional meta information

1. Table of contents
2. Fast random access to certain sub-elements in big files
3. Structured types
4. Fast deletion and addition of elements in big files
5. Chunk data mode for efficient writing and reading of big files
6. Compression of data
7. Included checksum
8. Datetime definition

Status
------

UBN is under development. The grammar will be finalized soon at some point when it is consolidated that nothing important is missing. There will be no different versions for the core grammar. At the moment a flag for a beta status is set. The meta language, in contrast, will grow from time to time and new features will be added. A version number will indicate the compatibility of the releases.

Grammar (beta4)
--------------

The graphical representation of the grammar rules below should contain all information to enable a programmer writing valid UBN files. The red round boxes represent data to be written. Single black characters inside are stored directly as ASCII characters. Green boxes require nested grammer rules.

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_file.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_element.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_list.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_dict.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_value.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_type.png"></p>

<p align="center"><img src="https://raw.githubusercontent.com/bitagoras/Universal-Binary-Notation/master/figures/UBN_count.png"></p>

Other than data in text formats no stop symbols can be defined for binary elements since the whole value range is reserved for the binary data. Therefore the size of the data must be determined by the parser from information stored in front of the data. The size of basic data types are given in the type table. In case of arrays the number of bytes have to be multiplied accordingly.

## Types

| Type| Name      | Bytes | Description                    | Comment                                       |
|:---:|-----------|:-----:|--------------------------------|-----------------------------------------------|
| `i` | uint8     | 1     | unsigned integer 8-bit         | C-type: unsigned char                         |
| `j` | uint16    | 2     | unsigned integer 16-bit        | C-type: unsigned short int                    |
| `k` | uint32    | 4     | unsigned integer 32-bit        | C-type: unsigned int                          |
| `l` | uint64    | 8     | unsigned integer 64-bit        | C-type: unsigned long int                     |
| `I` | int8      | 1     | signed integer 8-bit           | C-type: char                                  |
| `J` | int16     | 2     | signed integer 16-bit          | C-type: short int                             |
| `K` | int32     | 4     | signed integer 32-bit          | C-type: int                                   |
| `L` | int64     | 8     | signed integer 64-bit          | C-type: long int                              |
| `b` | boolean   | 1     | boolean type                   | values: 0x00 = false or 0xFF = true           |
| `h` | float16   | 2     | half precision float 16-bit    | IEEE 754-2008 half precission                 |
| `f` | float32   | 4     | float 32-bit                   | IEEE 754 single precision, C-type: float      |
| `d` | float64   | 8     | double precision float 64-bit  | IEEE 754 double precision, C-type: double     |
| `s` | str/utf-8 | 1     | ascii / utf-8 string           | no other coding than utf-8 is specified       |
| `u` | uft-16    | 2     | unicode string in utf-16       |                                               |
| `x` | byte      | 1     | user defined data byte         | special structs, compressed data etc.         |
| `X` | byte      | 1     | user defined data byte         | special structs, compressed data etc.         |

Examples
--------

In the examples below, characters in brackets `[ ]` symbolize bytes stored directly as ASCII values. Parentheses `( )` show readable representations of the corresponding binary data. If no type is noted for integers in parentheses the type is uint8. All examples are valid and complete UBN files. No additional header is required. That's simple, isn't it?

* **String**:

```
"hello world"

UBN:
     [m] (uint8: 11) [s] [h] [e] [l] [l] [o] [ ] [w] [o] [r] [l] [d]

hex: 6D          0B  73  68  65  6C  6C  6F  20  77  6F  72  6C  64
```

* **Integer:**

```
1025

UBN:
[j] (uint16: 1025)

hex: 6A 01 04
```

* **3d vector of type uint8:**

```
[10, 200, 255]

UBN:
       [3] [i] (uint8: 10) (uint8: 200) (uint8: 255)

hex:   33  69          0A           C8           FF   
```

* **List with integer, string and float:**

```
[7, "seven", 7.77]

UBN:
[[] [i] (uint8: 7) (uint8: 5) [s] [seven] [f] (float32: 7.77) []]
```

* **3 x 3 matrix of double:**

```
[ [1.1, 3.3, 5.5],
  [2.2, 4.4, 6.6],
  [3.3, 5.5, 7.7] ]
  
UBN:  
[3]
    [3] [d]
        (float64: 1.1) (3.3) (5.5)
        (2.2) (4.4) (6.6)
        (3.3) (5.5) (7.7)
```

* **800 x 600 x 3 RGB Image:**

```
UBN:
[n] (uint16: 800)
    [n] (uint16: 600)
        [3] [i]
        (... 800*600*3 bytes of data ...)
```

* **Object:**

```
{
  "planet": "Proxima b",
  "mass": 1.27,
  "habitable": True
}

UBN:
[{]
    [6] [s] [planet] [9] [s] [Proxima b]
    [4] [s] [mass] [d] (float64: 1.27)
    [9] [s] [habitable] [T]
[}]
```

* **3 x 3 table of doubles with named colums "lon", "lat", "h":**

```
[ ["lon", "lat",   "h"],
  [1.1,    3.3,    5.5],
  [2.2,    4.4,    6.6],
  [3.3,    5.5,    7.7],
  [4.4,    6.6,    8.8] ]

UBN:
[[]
    [[]
        [3] [s] [lon]
        [3] [s] [lat]
        [s] [h]
    []]
    [3] [d] (1.1) (3.3) (5.5)
    [3] [d] (2.2) (4.4) (6.6)
    [3] [d] (3.3) (5.5) (7.7)
    [3] [d] (4.4) (6.6) (8.8)
[]]
```

# Meta Language

Version: 0.1

The content of the `meta info` element gives information and hints about how to read or pre-process the data, before it is passed to the application. A parser that do not support this meta information has to parse the element after `[*]` but hat ignore its content. The content is mostly used for optimizing the efficiency or speed for writing and reading large files with random access. It can also contain application-specific information of how to apply the data. The meta info consists normally of dict objects in front of elements and contains e.g. pointers to sub-elements, definitions of user-defined types for `x`, or instructions to transpose or concatenate matrices or vectors when loaded into memory. When the meta information is a dict object, it contains pairs of keywords and values. Each pair represents a feature to extent the UBN core grammar. Some examples of meta features are listed below.

All information about sizes or relative jump positions are related to the whole element including the metainfo itself. So the parser has to remeber the position of the `*` character of the metainfo as the referred absolute position. Also some whitespaces belong to the element as defined in the grammar.

### Size of element

**Purpose:** Gives a size information about this element. As a convention the size information can also be noted as a simplified meta information that consists only of a number `N` instead of an dict object.

**Keyword:** `size`

**Value:** `N`

```
   N: 
      type:    unsigned integer (i,j,k,l)
      content: number of bytes of the element
```

**Explanation:**

This meta feature tells the number of bytes of an element. The size also includes the metainfo itself, as well as white-spaces after the metainfo. The size information helps to browse more quickly through the file structure in order to access a certain sub-element in large files, without parsing all previous elements.

**Example:**

Let's assume the element, without the size of the meta information, is 1200 byte. The meta information (with size 11 byte) would be:

```
[*] [{] [4] [size]
    [j] (unit16: 1211)
[}]
```
or in the simplified convention:

```
[*] [j] (unit16: 1204)
```

### Deleted element

**Purpose:** Flags this element as deleted. For this flag also exist a simplified convention where the meta information consist only of the value true or false.

**Keyword:** `deleted`

**Value:** `T` (true) or `F` (false)

**Explanation:**

This meta feature tags an element as deleted, when the value is set to true. This is useful for big files when an element in the middle should be able to be deleted without rewriting the whole file. Small elements can be deleted by overwriting them with spaces. For larger elements a metainfo like this can be added, followed by an `x` array that covers the element until the end. By this a very large element can be deleted by writing only a few bytes at the beginning. The next time the entire file is rebuilt, the unused space can be discarded. This feature also can be used to reserve some space for e.g. a table of content that will be included later.

**Example:**

In the following example an element with 10000 bytes is tagged as deleted. The included metainfo and the `x` byte-array type definition together are 15 bytes long. The remaining bytes of the 10000 bytes are covered by the 9985 long `x` array. So, only 15 bytes had to be written to remove the element, instead of writing 10000 spaces or rebuilding the whole file.

```
[*] [{] (7) [deleted]
    [T]
[}]
[n] (unit16: 9985) [x]
```
or in the simplified convention:

```
[*] [T]
[n] (unit16: 9985) [x]
```


## Table of content

**Purpose:** Table of content: Lists the starting positions of all elements in a list

**Keyword:** `TOC`

**Value:**

```
    type:    array of unsigned integer (i,j,k,l)
    content: relative byte offset of elements from the beginning of the metainfo
```

**Explanation:**

This meta feature allows to access an element of large data files. The relativ offsets are stored in an integer array with the same length as the list object. The offset points to the beginning of each element, which can include another meta information for this element (e.g. for integritiy check or TOCs for subelements). For this meta information also a simplified convention exists whith the meta information that consist only of the value, an integer array.

**Example:**

This example shows a table of a short list with mixed types

```
[7, "seven", 7.77]

UBN:

[*] [{] [3] [s] [TOC]
    [3] [i]                 # uint8 array of length 3
        (15) (17) (24)      # offsets to the elements
[}]
[[] [i] (uint8: 7) (uint8: 5) [s] [seven] [f] (float32: 7.77) []]
     ^              ^                      ^   # target positions 
```
or in the simplified notation

```
[*] [3] [i]                 # uint8 array of length 3
        (9) (11) (18)       # offsets to the elements
[}]
[[] [i] (uint8: 7) (uint8: 5) [s] [seven] [f] (float32: 7.77) []]
     ^              ^                      ^   # target positions 
```
