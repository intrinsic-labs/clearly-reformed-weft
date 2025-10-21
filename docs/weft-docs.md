# Weft Documentation

## What is Weft?

Weft is an interpretable, non-rigid, cooperative pseudocode language designed for cross-platform development that allows generation of fully native implementations for iOS, Android, web, and desktop. Unlike traditional frameworks that use a runtime layer or bridge, Weft is **structured, actionable documentation** that translates into idiomatic native code for each target platform.

Think of Weft as a metalanguage that sits between natural language specifications and executable code. You write your application once in clear, flexible pseudocode, and then generate pure idiomatic Swift, Kotlin, TypeScript, or other target languages. The result: native performance everywhere, with no runtime dependencies.

Weft captures semantic concept and intent in source code form. It reads like a developer’s thoughts, formalized enough that an implementer can act on them.

**Key Benefits:**
- **Native Performance**: No bridge, no runtime overhead — just pure native code
- **Flexible Syntax**: Use syntax from whatever language you're most comfortable with
- **Clear Intent**: Annotations and structured flow communicate your reasoning, not just actions
- **Human-in-the-Loop**: You maintain control and understanding of your codebase
- **No Lock-in**: Generated code is yours — remove the Weft layer anytime and maintain native codebases independently

Weft is perfect for solo developers and small teams who need native performance across multiple platforms but can't afford to maintain separate codebases from scratch.

[Read the original idea for Weft here.](https://rocketbro.vercel.app/blog/weft)

---

## Control Flow & Operators

Weft supports common control flow patterns and operators with familiar variants from popular languages. Write using the syntax you're most comfortable with — all variants are valid and will be properly translated.

### Conditionals

Weft accepts multiple conditional syntax styles:

```weft
// C-style braces (Swift, Kotlin, JS)
if condition {
    // do something
} else if otherCondition {
    // do something else
} else {
    // fallback
}

// Python-style keywords (also works: elsif)
if condition {
    // do something
} elif otherCondition {
    // do something else
} else {
    // fallback
}

// With or without parentheses
if (user.isActive && user.age > 18) {
    // both work
}

if user.isActive && user.age > 18 {
    // both work
}
```

### Loops

```weft
// For-in loops (collection iteration)
for item in collection {
    // process item
}

// For-in with range
for i in 0..10 {
    // i goes from 0 to 9 (inclusive range)
}

// While loops
while condition {
    // keep going
}

// Loop control
break      // exit loop
continue   // skip to next iteration
return     // exit function
```

### Switch/Match

```weft
// Switch statements
switch value {
    case option1:
        // handle option1
    case option2:
        // handle option2
    default:
        // fallback
}

// Pattern matching style (Rust/Kotlin)
match value {
    option1 => result1
    option2 => result2
    _ => defaultResult
}
```

### Functions

Multiple function declaration styles are supported:

```weft
// Various function keywords (all work)
func fetchData() => Result { }
function fetchData() => Result { }
fn fetchData() => Result { }
def fetchData() => Result { }

// With parameters and return types
func calculateTotal(items: [Item], tax: float) => float {
    var total = 0.0
    for item in items {
        total += item.price
    }
    return total * (1 + tax)
}

// Async functions
async func fetchArticles() => [Article] {
    var articles = await apiClient.get("/articles")
    return articles
}
```

### Operators

Weft supports both symbol-based and word-based operators:

#### Boolean Logic
```weft
// Use symbols or words (both valid)
if isActive && hasPermission { }
if isActive and hasPermission { }

if isAdmin || isModerator { }
if isAdmin or isModerator { }

if !isDeleted { }
if not isDeleted { }
```

#### Comparison
```weft
// Standard comparison operators
==  // equal
!=  // not equal
<   // less than
>   // greater than
<=  // less than or equal
>=  // greater than or equal

// Membership
if item in collection { }
if item not in collection { }
```

#### Null Safety
```weft
// Null/nil/none (all mean the same)
var value = null
var other = nil
var another = none

// Optional chaining
let title = article?.metadata?.title

// Null coalescing
let displayName = user.nickname ?? user.username ?? "Anonymous"

// Optional types
var maybeValue: string?
var definiteValue: string
```

### Error Handling

```weft
// Try-catch blocks
try {
    var data = await fetchData()
    processData(data)
} catch error {
    logError(error)
} finally {
    cleanup()
}

// Throw errors
if !isValid {
    throw ValidationError("Invalid input")
}
```

### Access Modifiers & Static Members

Control visibility of types, properties, and methods:

```weft
public var publicProperty       // accessible everywhere
private var privateProperty     // accessible only within this type
private(set) var privateSetProp // readable everywhere, writable only within this type
protected var protectedProp     // accessible in type and subclasses
internal var internalProp       // accessible within same module
```

Use `static` to define class-level (rather than instance-level) properties and methods:

```weft
class MathUtils {
    static let PI = 3.14159
    static let E = 2.71828

    static func max(a: int, b: int) => int {
        return a > b ? a : b
    }
}

// Usage: access without creating an instance
let pi = MathUtils.PI
let result = MathUtils.max(10, 20)

class User {
    static var userCount: int = 0    // shared across all instances
    var id: string                   // unique to each instance
    var name: string

    func init(name: string) {
        self.name = name
        User.userCount += 1          // increment shared counter
    }
}
```

---

## Primitive Types

**Weft is a strictly-typed language.** Every variable, parameter, and return value must have a defined type (either explicit or inferred). This ensures clear communication of intent to translators and results in higher-quality native code generation.

Types can be explicit or inferred from context:
```weft
var name: string = "John"    // explicit
var name = "John"            // inferred as string (also valid)
var name: string             // declaration without assignment
```


Weft supports the following primitive types. The canonical style is **lowercase**, but **PascalCase** is also supported for developers coming from Swift, Kotlin, or other statically-typed languages.

### Core Types

```weft
// Text
string / String
"" / ''                             // string literals

// Numbers
int / Int / integer / Integer       // whole numbers
float / Float                       // decimal numbers (32-bit)
double / Double                     // decimal numbers (64-bit, higher precision)

// Boolean
bool / Bool / boolean / Boolean

// Temporal
date / Date                         // calendar date
datetime / DateTime                 // date with time

// Special Types
url / URL / Url                     // web addresses, URIs
image / Image                       // image data/references
void / Void                         // no return value (for functions)
```

Weft does not support `any` as a type, as it goes against the goal of clear intent and structure. For dynamic data, define a proper data transfer object (DTO), enums with associated values, or interfaces/protocols to capture expected structure.

### Collections

Weft supports three core collection types: **arrays**, **dictionaries/maps**, and **sets**. Multiple syntax variants are supported to accommodate developers from different language backgrounds.

```weft
// Arrays/Lists
[Type]                              // canonical - array of Type
Array<Type>                         // also supported (generic style)
List<Type>                          // also supported (Kotlin style)

[string]                            // array of strings
[Article]                           // array of Articles

// Dictionaries/Maps
[Key: Value]                        // canonical - Swift-style
Map<Key, Value>                     // also supported (Kotlin/Java style)
Dict<Key, Value>                    // also supported (Python style)

[string: Article]                   // dictionary mapping strings to Articles
[int: User]                         // dictionary mapping ints to Users

// Sets
Set<Type>                           // unique, unordered collection

Set<string>                         // set of unique strings
Set<int>                            // set of unique integers
```

### Optionals

```weft
// Optional types
Type?                               // optional Type (may be null/nil)
string?                             // optional string
User?                               // optional User

// Optional collections
[Type]?                             // optional array
[string]?                           // may be null or array of strings
[string: int]?                      // optional dictionary
Set<string>?                        // optional set
```

### Mutability

Use `var, mut, mutable` vs `let, const, val, final` to control mutability of collections, not the type itself:

```weft
let readOnly: [Article]             // immutable binding - cannot reassign
var mutable: [Article]              // mutable binding - can modify or reassign

let constMap: [string: int]         // cannot reassign this dictionary
var mutMap: [string: int]           // can modify or reassign this dictionary
```

### Examples in Context

```weft
type User {
    var id: string
    var username: string
    var age: int
    var isActive: bool
    var profileImage: image?        // optional image
    var lastLogin: datetime?        // optional datetime
}

type Cache {
    var articles: [string: Article]     // dictionary mapping IDs to Articles
    var tags: Set<string>               // set of unique tags
    var favorites: [Article]            // array of favorite articles
    var metadata: Map<string, string>   // alternative Map syntax
}

func calculateAverage(numbers: [float]) => float {
    var sum: float = 0.0
    for num in numbers {
        sum += num
    }
    return sum / numbers.count
}

func groupByCategory(articles: [Article]) => [string: [Article]] {
    var groups: [string: [Article]] = [:]
    @SumFunc
    => iterate through articles
    => group by category name
    => return dictionary of category to array of articles
}

async func fetchUser(id: string) => User? {
    // may return User or null
}
```

---

## Annotations

Weft makes heavy use of annotations to capture intent, context, and metadata about code entities. Annotations are prefixed with `@` and can be applied to types, properties, functions, and more.

@Instruction
Specific arbitrary instructions to human or LLM translators to reduce ambiguity during target implementation. Pass in a single/multiline string, like so:
```weft
@Instruction('''
API return clarification -
The API call will return both a featured_image and
a featured_image_full value; please map the plain
featured_image during target implementation and
ignore the full size
''')
```

@SumFunc
Summarize desired functionality with N lines of following statements beginning with `=>`, like so:
```weft
func fetchPosts() => [Post] {
  @SumFunc
  => use the self.filter property to call the posts api endpoint
  => organize results into a function scoped variable
  => return the variable as an array of type Post
}
```

@Index
Create an `index.weft` file in a directory and put `@Index('directory_name')` at the top. This file will be referenced for general context and insights about the directory contents. Write content in markdown or plain text and use XML tags as desired.

@JSON
Marks a type or property as being serialized to/from JSON. This is useful for API models and data transfer objects.


### Databases

Weft supports the following annotations for marking database schema definitions: `@Schema, @Entity, @DatabaseModel`. They all accomplish the same task: tell the system that this object is a schema definition for your database.

> By default, all properties on any database schema are treated as fields to be stored in the table.

Other annotations are available to set properties on schemas:

`@Id`: Marks a field as the primary key. Use `@Id(generated)` to indicate the ID should be auto-generated. Note: a property simply named `id` will be automatically assigned as the primary key.

`@Field`: Marks a property to be stored as a column in the database schema. **By default, all properties on any object marked as a schema will be treated as a field.** You can add the annotation for clarity if desired.

`@Transient, @Exclude, @Ignore, @NotField`: Marks a field to be ignored by the database.

`@ForeignKey('table_name'), @Reference('table_name'), @Relation('table_name')`: Marks a field as a foreign key referencing another table.

`@Index`: Marks a field to be indexed for faster queries.

`@Nullable, @Optional`: Marks a field as allowing null values. Alternatively, you can do this in the variable declaration by using a question mark on the type, like `var someField: string?`.

`@Required`: Marks a field as required.

`@Unique`: Marks a field as unique across all records.

```weft
@Schema
class AuditLog {
    @Id(generated)
    var logId: int

    @ForeignKey("users")
    var userId: string

    var action: string

    @Index
    var timestamp: datetime

    @Nullable
    var details: object

    @Ignore
    var notes: string
}
```


## Syntax

### Value Assignment

These mean the value can be rewritten later (mutable data): `var, mut, mutable`
```weft
var someVar
mut someMut
mutable someMutable
```

These mean the value cannot be rewritten later (immutable data): `const, let, val, final`
```weft
const someConst
let someLet
val someVal
final someFinal
```


### Enums

Enums should use the `enum` keyword and comma separate case values. Typically, case values would be all-caps, but lowercase or sentence case are also acceptable.
```weft
enum SomeEnum {
  RED,
  GREEN,
  BLUE
}
```

Enums may also define associated values:
```weft
enum SomeEnum {
  SUCCESS(message),
  ERROR(code, message),
  TIMEOUT
}
```

And raw values with primitive types (like string or int):
```weft
enum SomeEnum: string {
  RED = "red",
  GREEN = "green",
  BLUE = "blue"
}
```

### Scope & Definition

Weft provides multiple keywords to define scope and structure. Use whichever feels most natural for your context — Weft's flexible syntax accepts various approaches, and the translator (whether human or model) will use context and annotations to implement correctly for the target platform.

#### Primary Keywords

1. **`type`** - General-purpose keyword for quickly defining custom types/objects. The translator uses context and annotations to determine the best implementation (class, struct, interface, etc.) for the target platform. Use this when you want flexibility and don't need to specify exact implementation details.

2. **`class`** - Inheritance, complex functionality, state management. Use when you need traditional OOP features.

3. **`struct`** - DTO/object definition with some convenience methods. Use for data-centric types that may include helper methods.

4. **`data`** - Pure data with no custom methods (auto-generates boilerplate like equals, hash, toString, copy). Use for simple data containers.

5. **`object`** - Singleton objects or constant containers. A singleton is a design pattern where only one instance of the type exists throughout your application — useful for global constants, configuration, or shared resources.

We can break each of these down into examples:

Case 1: Quick type definition (flexible implementation)
```weft
type Article {
    var id: string
    var title: string
    var content: string
}
// Translator decides best implementation based on target:
// Swift: struct, Kotlin: data class, TypeScript: interface
```

Case 2: Singleton/constants container
```weft
object ENDPOINTS {
    let BASE_URL = "https://api.example.com"
    let ARTICLES = self.BASE_URL + "/articles"
    let VIDEOS = self.BASE_URL + "/videos"
}
// Only one instance exists globally
```

Case 3: Complex functionality, state management
```weft
class ArticleManager {
    private var cache: [Article]
    private var apiClient: APIClient

    func fetchArticles() => [Article] {
      @SumFunc
      => check if articles are in cache
      => if not, use apiClient to fetch from server
      => store fetched articles in cache (modify state)
    }
}
```

Case 4: Data with convenience methods
```weft
struct Article {
    var id: string
    var title: string
    var content: string

    func toDisplayFormat() => DisplayArticle {
        // Custom transformation
    }

    func validate() => bool {
        // Custom validation logic
    }
}
```

Case 5: Pure data
```weft
data ArticleMetadata {
    var id: string
    var title: string
    var author: string
    var publishedDate: Date
}
// Auto-generates: equals, hash, toString, copy
// LLM knows: use data class (Kotlin), struct
// with Equatable/Hashable (Swift),
// interface (TypeScript), dataclass (Python)
```
