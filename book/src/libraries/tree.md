# Tree
The tree library allows the drawing of diagrams with simple tree layout algorithms.

## Nodes
A tree node is an array consisting of the node's value at index 0 followed by its child nodes. For the default `draw-node` function, the value (the first item) of a node must be of type {{#type content}}.

Example of a list of nodes:
```typc,example
cetz.tree.tree(
  (
    [A],
    (
      [B],
      (
        [C],
        ([D],)
      )
    )
  ),
  direction: "right"
)
```

Example of a tree of nodes:
```typc,example
cetz.tree.tree(
  (
    [A],
    (
      [B],
      [C]
    ),
    (
      [D],
      [E]
    )
  ),
  direction: "right"
)
```

## Drawing and Styling Tree Nodes
To control how the nodes and edges are drawn, you can provide function callbacks to the `draw-node` and `draw-edge` parameters. 

...

