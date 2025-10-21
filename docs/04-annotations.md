# Annotations

Weft makes heavy use of annotations to capture intent, context, and metadata about code entities. Annotations are prefixed with `@` and can be applied to types, properties, functions, and more.

## Core Annotations

### @Instruction

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

Use `@Instruction` when you need to provide context or clarification that isn't obvious from the code structure itself.

### @SumFunc

Summarize desired functionality with N lines of following statements beginning with `=>`. This annotation helps communicate the logical flow of a function without getting bogged down in implementation details:

```weft
func fetchPosts() => [Post] {
  @SumFunc
  => use the self.filter property to call the posts api endpoint
  => organize results into a function scoped variable
  => return the variable as an array of type Post
}
```

### @Index

Create an `index.weft` file in a directory and put `@Index('directory_name')` at the top. This file will be referenced for general context and insights about the directory contents. Write content in markdown or plain text and use XML tags as desired.

```weft
@Index('models')

This directory contains all data models for the application,
organized into subdirectories for DTOs and database schemas.
```

### @JSON

Marks a type or property as being serialized to/from JSON. This is useful for API models and data transfer objects.

```weft
@JSON
type Article {
    var id: string
    var title: string
    var content: string
}
```

## Database Annotations

Weft supports the following annotations for marking database schema definitions: `@Schema`, `@Entity`, `@DatabaseModel`. They all accomplish the same task: tell the system that this object is a schema definition for your database.

> By default, all properties on any database schema are treated as fields to be stored in the table.

### Schema Definition

```weft
@Schema
struct User {
    @Id(generated)
    var id: string
    var username: string
    var email: string
}
```

### Available Database Annotations

**`@Id`**: Marks a field as the primary key. Use `@Id(generated)` to indicate the ID should be auto-generated. Note: a property simply named `id` will be automatically assigned as the primary key.

**`@Field`**: Marks a property to be stored as a column in the database schema. By default, all properties on any object marked as a schema will be treated as a field. You can add the annotation for clarity if desired.

**`@Transient`, `@Exclude`, `@Ignore`, `@NotField`**: Marks a field to be ignored by the database.

**`@ForeignKey('table_name')`, `@Reference('table_name')`, `@Relation('table_name')`**: Marks a field as a foreign key referencing another table.

**`@Index`**: Marks a field to be indexed for faster queries.

**`@Nullable`, `@Optional`**: Marks a field as allowing null values. Alternatively, you can do this in the variable declaration by using a question mark on the type, like `var someField: string?`.

**`@Required`**: Marks a field as required.

**`@Unique`**: Marks a field as unique across all records.

### Complete Database Example

```weft
@Schema
struct AuditLog {
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

## State Management Annotations

See the [User Interface](06-user-interface.md) section for details on state management annotations like `@State`, `@Binding`, `@Observed`, and `@Environment`.