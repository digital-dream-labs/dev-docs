# General formatting

## Comments
* Standard comment block at the top of each file with the format:
* Note: Header and cpp can share the same description

```c++
/**
 * File: vehicleAIComponent
 *
 * Author: raul
 * Created: 02/10/14
 *
 * Description: Vehicle component for AI that makes high level decisions, such as
 *              item or target selection, who to evade, etc.
 *
 * Copyright: Digital Dream Labs, Inc. 2017
 *
 **/
```

* Use `//` commenting everywhere else (including above functions)

* Descriptive comments are mandatory for types defined in a header
   * Not mandatory for the main class of the file (since it’s in the file block)

* Add descriptive comments for all class members or at least for groups of members in header
   * Not mandatory for constructor/destructor/operators


## Spaces / tabs

* No tabs at all. Insert spaces instead: 2 spaces per indent
* Max Column width: 108 characters ( so we don't have to side scroll when reviewing changes in GitHub )
* Return type on same line as function
* Space after comma in arguments
* No space between function name and open parenthesis
* Namespaces don't add tab/space indentation
   * Exception for pre-declaration sections in headers, where compact and indented declarations are more readable.
```c++
namespace Anki {
  namespace Cozmo {
    namespace MicData {
      class MicDataInfo;
    }
  }
  namespace Util {
    namespace Data {
      class DataPlatform;
    }
  }
}


namespace Anki {
namespace Util {

class MyClass {
  void MyMethod();
}

} // namespace
} // namespace
```

## Brackets

* Brackets are required for one-line blocks (prevents nasty bugs from omission mistakes)

```c++
if (myBool) {
  myDo();
}
```

* For functions, brackets “{“ on separate line after closing declaration, with the exception of empty bodies and one-line accessors (get/set) in header files.

```c++
void myFunction()
{
}
```

* Loops, if/else blocks can have brackets in the same line or in new line, whatever makes the code easier to read based on your judgement.

* Always group logic within parentheses

```c++
if ( myvar == yourvar && hisvar == hervar )     // BAD
if ((myvar == yourvar) && (hisvar == hervar)) // GOOD

// even when operator precedence does not require it
int number = a - b * c;       // BAD
int number = a - (b * c);   // GOOD
```

## Preprocessor

### `#ifdef`
* All header files must be wrapped with `#ifndef` / `#endif` with the format:

`__Folder1_FolderN_ClassOrFilename_H__`

eg: /Basestation/metagame/achievements/achievementTypes.h:

```c++
  #ifndef __Metagame_Achievements_AchievementTypes_H__
  #define __Metagame_Achievements_AchievementTypes_H__
    ...
  #endif // __Metagame_Achievements_AchievementTypes_H__
```

* Use `UNIT_TEST` for unit test specific code in basestation/drive. Should be little to nothing.


### `#if`

* Do not use #ifdef, but `#if`, for conditional code (prevents omission/inclusion mistakes)

* Define macro value for `#if` in the most confined scope possible. For example, if only 1 function uses it, define right before its usage. Generally this means a cpp file rather than a header file.

* Use brackets to delimit the scope of variables inside the clause

```c++
          void myFun()
          {
            #define MY_DEBUG 0
            #if MY_DEBUG
            {
              int thisVarIsOnlyHere = 0;
              printf(“\n”);
            }
            #endif
          }
```

* Indent the `#if` clause to the level it belongs to in the scope, not to the left
* Use `ANKI_DEVELOPER_CODE` to strip out code that should not be present in shipping product.


## Macros

* Minimize the use of macros, and replace with inlined functions whenever possible
* For macros that use arguments, make sure function calls or modifications of variables aren’t passed in, since they may be executed multiple times. This would be avoided with proper inlined functions.


## `#include`

* Minimize the number of includes to only what’s necessary, using forward declaration in header files whenever possible
* In some cases, consider pointers instead of objects for members, when big headers would be required to define the type. This allows forward declaration instead.
* Make sure there are no cyclic dependencies when designing systems (for example, in our current codebase: basestation includes metagame includes ankiutils, but not the other way around.
* Use complete path for local / project includes
* Prefer deeper folder structure when designing your component. Don't add a top-level folder unless you are adding a top-level component.
* Format:
   * Corresponding header must be the first included. 
   * Then from local to global namespace

```c++
#include “myFile.h”
#include “userPathY/userFileA.h” // from local to global, then in alphabetic order
#include “userPathY/userFileB.h” // from local to global, then in alphabetic order
#include “userPathX[a][b][c][d]/userFileA.h” // from local to global, then in alphabetic order
#include “userPathX/userFileB.h” // from local to global, then in alphabetic order
#include <systemFile>


//////////////////////////////////////////////////////////////////////////////////
// example of VehicleAIComponent.cpp
// note how basestation, metagame, util are packed together from local to global
// note how files within namespaces are alphabetically ordered 
// note how system headers use <>
#include "vehicleAIComponent.h" // your header in local directory
#include "basestation/vehicle/vehicleConditions/vehicleCondition.h"
#include "basestation/vehicle/vehicleConditions/vehicleConditionFactory.h"
#include "basestation/vehicle/vehicleConfig.h"
#include "basestation/vehicle/vehicleManager.h"
#include "metagame/stringMap/stringMap.h"
#include "util/logging/logging.h"
#include "util/parsingConstants/parsingConstants.h"
#include <vector>
//////////////////////////////////////////////////////////////////////////////////
```

## Code

### Naming convention

* Folders and files in lowerCamelCase (no spaces)        : `/highLevelAI/vehicleAIComponent.h`
* Namespaces in UpperCamelCase                        : `namespace BaseStation { …  }`
* Class/struct/typedef names in UpperCamelCase        : `class VehicleAIComponent { …  };`
* Interfaces in UpperCamelCase, starting “I”                : `class IBountyProvider { … };`
* Methods/functions in UpperCamelCase                : `void ClearAll();`
* Variables in lowerCamelCase                                : `int thisIsALocalVar;`
* Member variables in lowerCamelCase, starting “_”        : `int _thisIsAMemberVar;`
* Constants lowerCamelCase, starting “k”                : `const int kIterations = 3;`
* Non-constant static members, starting with “s”        : `static MapType sSharedTable;`
* Enumerations : see section Enumerations below 

#### Physical Units

* Including physical units in a name is often very helpful, but not required.
* Add an underscore at the end of the variable name, followed by the unit.
* Use the unit's official SI abbreviation.
* "Sec" can be used instead of "s", when it is more clear.
```c++
float offsetFromDSCenter_m;           // meters
float offsetFromDSCenter_mm;          // millimeters
uint  clockFreq_MHz;                  // Megahertz
float timeout_s;                      // seconds
uint  timeStamp_us;                   // micro seconds
```

If the unit has an exponent, include the exponent value directly:
```c++
float ComputeArea_m2();               // meters squared
```

For units that have a denominator, separate the numerator and denominator with "Per".
```c++
float speed_mmPers;                   // millimeters / second
float horzAccel_mPerSec2;             // meters / (second^2)
```

### Namespaces

* Do not ever import namespaces in header files, use qualified-id instead (eg: `std::vector`)
  * If a qualified id is long and/or is going to be used several times, import with a type alias, but within the scope of the class:

```c++
class MyClass
{
  public:
    using ptree = ::boost::property_tree::ptree;
};
```

* Do not import namespaces in cpp files in general, use qualified-id instead (eg: std::vector)
   * See above for `using/typedef`

* Make proper use of namespaces for our components (`basestation`, `metagame`, `ankiutil`, etc)
   * Nothing in our codebase should be outside of a namespace


### Typedefs

* Prefer new c++11 type alias syntax instead of typedef
* Use most confined scope/visibility possible for the type usage (class private, namespace public, …) For example, an internal type in a class should be declared in the private section of such class.

### Constants

* Consider using enums for exported or global integer constants that behave as ids, error codes, etc.
```c++
enum { ACHIEVEMENT_DEFINITION_INVALID_ID = 0 };
// instead of
#define ACHIEVEMENT_DEFINITION_INVALID_ID ((AchievementDefinitionIdType)0)
```

* Consider using static const variables for constants of integral types (not objects)
* Limit scope of constants as much as possible
* Define class constants as static const class members


### Classes

* All member variables must be private, and provide getters/setters whenever needed
* All primitive member variables must be given a value in the constructor initialization list or in the class definition
   * Note this does not mean they have to be constructed (for example, pointers can delay construction of their types to a later initialization)
   * Respect the format:

```c++
    class Foo {
      ...
    private:
      int member_1 = val1;
    };

    Bar::Bar(...)
    : member_2( val2 )
    , member_3( val3 )
    {
      // ...
    }
```

* Const getters must return copy of the value or const reference/pointer to the member
* Only one class per file. Small helper structs are allowed (see below), but not encouraged
* Order of declaration is: public, then protected, then private.
* Order of declaration within visibility: types, then methods, then variables.


### Structs

* Use structs only as data containers. If functionality is provided, it should be a class, excluding convenience constructors.
* Member variables can be public if the scope of the struct is limited (local to class for example), omit underscore in this case.

### Methods
* Functions which contain output parameters should prefer to order the arguments so that all input arguments are supplied before the ouput arguments, as well as prepending ‘out’ to the name
         `void MyFunction( int myInput, int& outMyOutput1, int& outMyOutput2 );`

* Use override keyword whenever overriding a virtual method. This allows catching unintended spelling mistakes or changes in base API. Do not omit the virtual keyword when using override.


### Types

* Of the built-in C++ integer types, the only one used is int. If a program needs a variable of a different size, use a precise-width integer type from `<cstdint>`, such as `int16_t`.

* Use `size_t` for sizes

* Respect types from system libraries. If your variable stores a value returned by a system function or method, your variable should match the return type of such function.

* Pointers and references must be adjacent to the type rather than with the variable
   * For this reason, no multiple pointers/references shall be declared in the same line

```c++
const MyClass* myClassVar;         // preferred
const MyClass *myClassVar;         // not preferred
const MyClass *a, *b;         // not allowed
```

* Use references instead of pointers whenever the value can not be null

### Enumerations

* Use strongly typed enums (enum classes) for variables with mutually-exclusive values
   * Type name in UpperCamelCase starting with E
   * Entry names in UpperCamelCase

```c++
// Mission group state
enum class EMissionGroupState
{
    Locked
  , Available
  , Completed
};
```

* Use old-style enums when arithmetic is expected from the type or for constants
   * Use typedef if C-code uses the enumeration
   * Type name in UpperCamelCase
   * Entry names in all caps

```c++
// Mask
typedef enum
{
    MM_SELF        = 0x01,
  , MM_TARGET      = 0x02
  , MM_HOST        = 0x04
  , MM_ALL         = SM_SELF | SM_TARGET | SM_HOST
} MessageMask;

enum { ACHIEVEMENT_DEFINITION_INVALID_ID = 0 };
```

### Switch statements

* Indent ‘case’ and indent again everything inside the case
* Must have a default block with error message if not expected
* Use brackets per case (allows defining variables in scope)

```c++
switch( var ) {
  case 1:
  {
    break;
  }
  case 2:
  {
    break;
  }
  default:
  {
    handleOrError();
  }
}
```

* Do not cascade (fall through) cases unless they have the exact same code

```c++
switch( var ) {
  case 1:
  case 2:           // case 1 and case 2 have the same code    [ OK ]
  {
    do();
    break;
  }
  case 3:
  {
    do3();
  }
  case 4:           // case 3 cascades into case 4 sneakily    [ NOT ALLOWED ]
  {
    do4();
    break;
  }
  default:
    handleOrError();
}
```

### Const-correctness

* Make extensive use of const. In particular:
   * Use const wherever possible, especially references and pointers passed as arguments that are not expected to be modified, getters, and local/temp variables inherently const.
   * provide const and non-const version of methods whenever necessary
* const methods must return const references/pointers (does not apply to return by value)
* Function arguments that are not meant to be modified must be declared const, especially pointers and references
* Do not use const_cast
* Use of mutable is discouraged except for rare debugging purposes (eg: counter member in getter)
* Convention: const before the type

```c++
const MyClass* cp1 = &myObj;           // non-const pointer to const type
const MyClass* const cp2 = &myObj; // const pointer to const type
MyClass* const cp3 = &myObj;         // const pointer to non-const type
```

### Language enforced practices

* Respect the rule of three (Wikipedia): “if a class defines one of the following, it should probably explicitly define all three: destructor, copy constructor, copy assignment operator.” C++11 extends this to the rule of five with the addition of move constructors and move assignment operators. If your class implements any of these, provide proper implementations for all of them, or use non-copyable:
   * Use `AnkiUtil::noncopyable` whenever your class would not work with default implementations of copy constructor and assignment operator, but is not expected to be copied, for which you don't want to provide custom implementations.
   
* As a corollary to the above, avoid declaring members of the rule of five if you don't need them. Using smart containers (e.g. `std::unique_ptr<MyClass>` instead of `MyClass*` can help remove the need for custom destructors, and will allow the compiler to generate copy and move operations that do the right thing by default.

* When the default implementations of move/copy operations will suffice, use them instead of writing your own:

```c++
class MyClass {
  ~MyClass(); // this class needs a custom destructor for some reason
  MyClass(const MyClass& other) = default; // default copy constructor
  MyClass& operator=(const MyClass& other) = default; // default copy assignment
  MyClass(MyClass&& other) = default; // default move constructor
  MyClass& operator=(MyClass&& other) = default; // default move assignment
};
```

* Add equality assertion at the end of custom assignment operators and copy constructors:
   * `assert( *this == other ); // this should be true if the copy was correct`

* Use `nullptr` keyword instead of implementation defined NULL for null pointers

* Use constant values to the left of equality comparisons. This prevents accidental bugs due to unintended assignments:

```c++
  Output* bestSelection = do();
  if ( bestSelection = nullptr ) // compiles and introduces a bug
  if ( nullptr = bestSelection ) // doesn't compile
  if ( nullptr == bestSelection ) // preferred over if ( bestselection == nullptr )
```

* Do not compare against true/false keywords and prefer positive names for boolean variables:
        (A negative name is something that reflects the opposite of the boolean value. Eg: ignore, skip,         prevent, ...)

```c++
if ( ignoreFailure == false )         // most confusing statement ever
if ( handleFailure )                  // easier to understand
```

* Return access to member containers by const reference. When a function returns a newly-constructed container of data, return it by value so the compiler can make use of RVO or cheap move semantics.
   * Accessor: return const_reference to type alias or type

```c++
using TargetScoreList = std::vector<AITargetScore>;

    // returning access to a class member
    const TargetScoreList& GetCurrentTargetScores() const {
      return _allTargetScores;
    }
    
    // returning a newly-constructed container
    TargetScoreList GetNewTargetScores() const {
      TargetScoreList scoreList;
      // ... fill scoreList
      return scoreList;
    }
```

   * Output: When returning objects that cannot be cheaply moved, receive the object by reference as a parameter and modify it within the function.

* Consider twice before passing objects and containers by value as arguments (as opposed to pointers and references)
  * Exception: when a parameter is always copied and can be moved cheaply, passing by value is usually correct.
  
  ```c++
  // bad, parameter is always copied no matter what
  void SetName(const std::string& name) {
    _name = name;
  }
  
  // good; if SetName is called with a std::string&&, the full string
  // is not copied
  void SetName(std::string name) {
    _name = std::move(name);
  }
  ```

* Never call virtual methods from constructors or destructors (including copy constructors)

* Use prefix form (++i) of the increment and decrement operators with iterators and other template objects. In general, prefer such form to post-prefix for integers too, for consistency.
  `for( size_t index=0; index<totalSize(); ++index ) {...}`

* Make use of const variables for return values that need to be calculated, even for one-line calculations. This is important because it makes debugging easier, and allows conditional breakpoints without any overhead (compile will optimize the const var):
```c++
  bool IsSpeedChangeAllowed() const
  {
    const bool ret = ( isLocalized && speedController->IsSpeedChangedAllowed() );
    return ret;
  }
  // better than
  bool IsSpeedChangeAllowed() const
  {
    return isLocalized && speedController->IsSpeedChangedAllowed();
  }
```

* Same statement above applies to if blocks
```c++
  void DoFunStuff() const
  {
    const bool weAreFun = ( isLocalized && speedController->IsSpeedChangedAllowed() );
    if ( weAreFun )
    {
      Fun();
    }    
  }
  // better than
  void DoFunStuff() const
  {
    if ( isLocalized && speedController->IsSpeedChangedAllowed() )
    {
      Fun();
    }    
  }
```

* All variables must be given a value when declared
* No code allowed in header files, except for inline functions, getters/setters, and templates, and only in the case that they are small.
   * If you implement a function bigger than 2 lines, implement it outside the class declaration either in the header file, but outside the class declaration, or in a new file that is included from the main one:
```c++
  namespace MyNamespace {
  class MyClass
  {
    inline void DoStuffInlined();
  };


  ///// inline methods:


  void MyClass::DoStuffInlined() 
  {
    // blah
  }
  } // namespace


   -------- plannerTable.h --------
 namespace MyNamespace {
  class Table {
 public:
   // declarations go here
   inline int foo();
   inline int bar();
 };
  } // namespace

 #include "plannerTable.inl"


 -------- plannerTable.inl -------
  #ifdef INCLUDE_ONLY_ONCE_PLANNER_TABLE_INL
  #error "plannerTable.inl should only be included once. Please fix."
  #endif
  #define INCLUDE_ONLY_ONCE_PLANNER_TABLE_INL


  namespace MyNamespace 
  {
 // IN this file, no other header guard or # includes
  int Table::foo() { // ... }
 int Table::bar() { // ... }
  } // namespace
```

* Prefer `const auto&` over `auto` where possible, to avoid accidental copies. Const references will persist the lifetime of a temporary value, and are safe to use to capture objects returned by value. `auto` without a reference should not be used in ranged for loops.

```c++
  // good; no copies
  for (const auto& achievementIdIt : achievementList)
  {
    achievements_.insert(achievementIdIt);
  }
  
  // bad; unclear without looking up dataList's type whether or not this code
  // is copying objects
  std::vector<int> dataList;
  ...
  for (auto data : dataList)
  {
    ...
  }
  
  // really bad; copying every string!
  for (auto hugeString : hugeStrings)
  {
    ...
  }
```

* Use `auto*` when the type being assigned is known to be a pointer; it clarifies that objects are not being copied and the meaning of the `->` operator.

```c++
  // good; usage of auto makes it clear it's operating on pointers
  std::vector<uint8_t*> bufferList;
  ...
  for (auto* buffer : bufferList) {
    ...
  }
```

### Consistency

* If you are editing someone else's code, take a few moments to look at the code around you and determine its style. If they use spaces around their if statements, you should, too. If their comments have little boxes of stars around them, make your comments have little boxes of stars around them too, etc.
* If the code you are editing is old or is badly against the coding standards, fix it without delay. Discuss with team members large refactors.
   * If you are touching a lot of old code, try to separate that out as a separate commit that doesn’t change any behavior, for ease of code review


## Code Review

* Any commit in Master has to build if synced to that commit.
