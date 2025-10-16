# Weft Notes

I'm using this file to keep track of Weft ideas and features I will need to implement. This will be expanded into proper documentation down the line.

## Annotations

@Instruction
Specific instruction comments to human or LLM translators to reduce ambiguity during target implementation. Pass in a multiline string, like so: 
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
Summarize desired functionality with N lines of following statements starting with `=>`, like so:
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


### Databases

Weft supports the following annotations for marking database schema definitions: `@Schema, @Entity, @DatabaseModel`. They all accomplish the same task: tell the system that this object is a schema definition for your database.

By default, all properties on any database schema are treated as fields to be stored. Other annotations are available to set properties on schemas:

`@Id`: Marks a field as the primary key. Use `@Id(generated)` to indicate the ID should be auto-generated. 

`@Field`: Marks a property to be stored as a column in the database schema. **By default, all properties on any object marked as a schema will be treated as a field.** You can add the annotation for clarity if desired.

`@Transient, @Exclude, @Ignore, @NotField`: Marks a field to not be stored in the database.

`@ForeignKey("table_name"), @Reference("table_name")`: Marks a field as a foreign key referencing another table.

`@Index`: Marks a field to be indexed for faster queries.

`@Nullable, @Optional`: Marks a field as allowing null values. Alternatively, you can do this in the variable declaration by using a question mark, like `var someField?: string` or `var someField: string?`.

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

These mean the value can be rewritten later (mutable data):
```weft
var someVar
mut someMut
mutable someMutable
```

These mean the value cannot be rewritten later (immutable data):
```weft
const someConst
let someLet
val someVal
final someFinal
```


### Enums

Enums should use the `enum` keyword and comma separate all-caps case values:
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