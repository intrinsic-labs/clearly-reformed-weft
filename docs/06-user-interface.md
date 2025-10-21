# User Interface

Weft provides first-class support for building user interfaces with the `view` keyword and state management annotations. Views are declarative UI components that translate to SwiftUI, Jetpack Compose, React, or other UI frameworks.

## The `view` Keyword

Use `view` to define UI components:

```weft
view ArticleListView {
    // View properties and body go here
}
```

The `view` keyword signals to the translator that this should become:
- **SwiftUI**: `struct ArticleListView: View`
- **Jetpack Compose**: `@Composable fun ArticleListView()`
- **React**: `function ArticleListView()`

## State Management

Weft uses annotations to express state management intent across different UI frameworks. Each annotation maps to the appropriate platform-specific mechanism.

### State Annotations

```weft
@State          // local mutable state owned by this view
@Binding        // two-way binding to parent state
@Observed       // observing external state object/store
@Environment    // injected from environment/context
```

### How They Work

```weft
view CounterView {
    @State var count: int = 0                    // local state
    @Binding var parentCount: int                // bound to parent
    @Observed var viewModel: CounterViewModel    // external state
    @Environment var theme: Theme                // environment injection
    
    // Computed properties (no annotation needed)
    var doubled: int {
        return count * 2
    }
    
    var isEven: bool {
        return count % 2 == 0
    }
}
```

### Translation Examples

**SwiftUI:**
```swift
struct CounterView: View {
    @State private var count: Int = 0
    @Binding var parentCount: Int
    @StateObject var viewModel: CounterViewModel
    @Environment(\.theme) var theme
    
    var doubled: Int { count * 2 }
    var isEven: Bool { count % 2 == 0 }
}
```

**Jetpack Compose:**
```kotlin
@Composable
fun CounterView(
    parentCount: MutableState<Int>,
    viewModel: CounterViewModel = viewModel(),
    theme: Theme = LocalTheme.current
) {
    var count by remember { mutableStateOf(0) }
    val doubled = count * 2
    val isEven = count % 2 == 0
}
```

**React:**
```typescript
function CounterView({ parentCount, setParentCount }) {
    const [count, setCount] = useState(0);
    const viewModel = useContext(ViewModelContext);
    const theme = useContext(ThemeContext);
    
    const doubled = useMemo(() => count * 2, [count]);
    const isEven = useMemo(() => count % 2 === 0, [count]);
}
```

## Lifecycle Hooks

Use lifecycle hooks for side effects and reactions to view lifecycle events:

```weft
view ArticleListView {
    @State var articles: [Article] = []
    @Observed var articleService: ArticleService
    
    onAppear {
        @SumFunc
        => fetch articles when view first appears
        articles = await articleService.fetchArticles()
    }
    
    onDisappear {
        @SumFunc
        => cleanup when view is removed
        articleService.cancelRequests()
    }
    
    onChange(searchText) {
        @SumFunc
        => refetch when search text changes
        articles = await articleService.search(searchText)
    }
}
```

**Lifecycle hooks map to:**
- **SwiftUI**: `.onAppear`, `.onDisappear`, `.onChange`
- **Jetpack Compose**: `LaunchedEffect`, `DisposableEffect`
- **React**: `useEffect` with appropriate dependencies

## Complete Example

```weft
view ArticleDetailView {
    @Binding var article: Article
    @State var isBookmarked: bool = false
    @Environment var bookmarkService: BookmarkService
    
    var formattedDate: string {
        return article.publishedDate.format("MMM d, yyyy")
    }
    
    onAppear {
        @SumFunc
        => check if article is bookmarked when view appears
        isBookmarked = await bookmarkService.isBookmarked(article.id)
    }
    
    func toggleBookmark() {
        @SumFunc
        => toggle bookmark status
        if isBookmarked {
            await bookmarkService.remove(article.id)
        } else {
            await bookmarkService.add(article)
        }
        isBookmarked = !isBookmarked
    }
    
    // View body would be defined here using declarative syntax
}
```

## Parent-Child State Flow

```weft
view ParentView {
    @State var selectedArticle: Article?
    @State var articles: [Article] = []
    
    // Pass state down to child
    view ArticleList(
        articles: articles,
        selectedArticle: $selectedArticle    // $ prefix for binding
    )
}

view ArticleList {
    var articles: [Article]                  // read-only prop
    @Binding var selectedArticle: Article?   // two-way binding
    
    func selectArticle(article: Article) {
        selectedArticle = article            // updates parent's state
    }
}
```

The `$` prefix signals that you're passing a binding (two-way connection) rather than just a value.

## Summary

Weft's state management system abstracts platform-specific implementations into clear, semantic annotations:

- **`@State`** - Local view state
- **`@Binding`** - Two-way parent-child communication
- **`@Observed`** - External state objects
- **`@Environment`** - Context injection

Combined with lifecycle hooks (`onAppear`, `onDisappear`, `onChange`), this provides everything needed to build reactive, stateful user interfaces that translate cleanly to any target platform.


## UI Building Blocks & Best Practices

Weft provides a set of built-in UI components and best practices to help you create consistent and maintainable user interfaces across platforms that will easily translate to target implementations. 

```weft
// layout
Column(parameter: value) { content }
Row(parameter: value) { content }
ZStack(parameter: value) { content }

// components
Button(action: func) { parameter: value }
Text(content: string) { parameter: value }
Image(source: image) { parameter: value }
AsyncImage(source: string) { parameter: value }
TextField(binding: @Binding var) { parameter: value }
```

For lists, simply render views inside a loop. 

You can invent paramenters and corresponding values as needed, as long as they are clear in their intended outcome. This takes the places of modifiers in Kotlin/Swift, like so:

```weft
Column(
    isScrollable: true
    scrollIndicator: hidden
    spacing: 8
    onRefresh: this.refresh()
) {
    for article in articles {
        ArticleCard(article: article)
    }
}
```

