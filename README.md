# couchbase-lite-C

This is a generic cross-platform version of Couchbase Lite.  
Or, it will be when it's done.  
**It's in the very early stages of development.**

## Goals

(Unofficial; we have no PRD or design docs yet)

* C API
  - C++ is a stretch goal but easily achieved
  - Can be bound to other languages like JavaScript or Python
* API similar to other Couchbase Lite platforms
* Same feature set as other Couchbase Lite platforms
* Support common Linux distros, esp. Ubuntu and Raspbian
  - Will probably support macOS too, just for ease of development
  - Windows?
* Minimal platform dependencies other than filesystem and TCP/IP
* Run on Raspberry-Pi-level hardware
  - 32-bit and 64-bit
  - x86 and ARM
  - Hundreds of MB RAM, hundreds of MHz CPU, hundreds of MB storage

## Example

```c
// Create a document:
CBLError error;
CBLDatabaseConfiguration config = {"/tmp"};
CBLDatabase* db = cbl_db_open("my_db", &config, &error);
CBLDocument* doc = cbl_doc_new("foo");
FLMutableDict props = cbl_doc_mutableProperties(doc);
FLMutableDict_SetString(props, "greeting"_sl, "Howdy!"_sl);

// Save the document:
const CBLDocument *saved = cbl_db_saveDocument(db, 
                                               doc, 
                                               kCBLConcurrencyControlFailOnConflict, 
                                               &error);
cbl_doc_release(saved);
cbl_doc_release(doc);

// Read it back:
const CBLDocument *readDoc = cbl_db_getDocument(db, "foo");
FLDict readProps = cbl_doc_properties(readDoc);
FLSlice greeting = FLValue_AsString( FLDict_Get(readProps, "greeting"_sl) );
cbl_doc_release(readDoc);
```

## Documentation

These APIs are very preliminary, unreviewed, and probably incomplete!

* [C API documentation](http://labs.couchbase.com/couchbase-lite-C/C/html/modules.html) (generated by Doxygen from the header files)

## Building It

### With CMake

1. Clone the repo
2. Check out submodules (recursively)
3. `mkdir build_cmake`
4. `cd build_cmake`
5. `cmake .. && make -j5`
6. `./test/CBL_C_Tests -r list`

The library is at `build_cmake/libCouchbaseLiteC.dylib`. (Or `.DLL` or `.so`)

### With Xcode on macOS

1. Clone the repo
2. Check out submodules (recursively)
3. Open the Xcode project
4. Select scheme `CBL_Tests`
5. Run

The library is `libcouchbase_lite.dylib` in your `Xcode Build Products/Debug` directory (the path depends on your Xcode settings.)

## Using It

* In your build settings, add the paths `include` and `vendor/couchbase-lite-core/vendor/fleece/API` (relative to this repo) to your header search paths.
* In your linker settings, add the dynamic library.
* In source files, add `#include "cbl/CouchbaseLite.h"`.
