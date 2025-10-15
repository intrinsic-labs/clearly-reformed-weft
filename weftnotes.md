# Weft Notes

I'm using this file to keep track of Weft ideas and features I will need to implement.

### Annotations

@Instruction
Specific instruction comments to human or LLM translators to reduce ambiguity during target implementation. Use above a comment, like so: 
```weft
@Instruction
/** API return clarification
* the API call will return both a featured_image and
* a featured_image_full value; please map the plain
* featured_image during target implementation and
* ignore the full size
*/
```

@Summary
Summarize desired functionality with N lines of following statements starting with `=>`, like so:
```weft
func fetchPosts() => [Post] {
  @Summary
  => use the self.filter property to call the posts api endpoint
  => organize results into a function scoped variable
  => return the variable as an array of type Post
}
```