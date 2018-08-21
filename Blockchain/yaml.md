- a strict superset of JSON with the addition of syntactically significant newlines and indentation
- comment: `#`
- strings doesn't have to be quoted
- Multiple-line strings can be written either as a 'literal block' (using `|`),
or a 'folded block' (using `>`).
- nesting uses indentation
    - 2 space indent is preferred
- maps doesn't have to have string keys
- `<<:`
    - The `<<` merge key is used to indicate that all the keys of one or more specified maps should be inserted into the current map. If the value associated with the key is a single mapping node, each of its key/value pairs is inserted into the current mapping, unless the key already exists in it. If the value associated with the merge key is a sequence, then this sequence is expected to contain mapping nodes and each of these nodes is merged in turn according to its order in the sequence. Keys in mapping nodes earlier in the sequence override keys specified in later mapping nodes.
    - > http://yaml.org/type/merge.html
- `*`, `&`
    - Repeated nodes (objects) are first identified by an anchor (marked with the ampersand - `&`), and are then aliased (referenced with an asterisk - `*`) thereafter
        - http://yaml.org/spec/1.2/spec.html