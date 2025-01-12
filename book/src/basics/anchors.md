# Anchors
You can refer to a position relative to an element by using its anchors. Anchors come in three different variations but can all be used in two ways.

The first is by using the `anchor` argument on an element. When given, the element will be translated such that the given anchor will be wher the given position is. This is supported by all elements that have the `anchor` argument.

```typc,example
// Draw a circle and place its "west" anchor at the origin.
circle((0,0), anchor: "west")

// Draw a smaller red circle at the origin.
fill(red)
stroke(none)
circle((0,0), radius: 0.3)
```

The second is by using [anchor coordinates](coordinate-systems.md#anchor). You must first give the element a name by passing a string to its `name` argument, you can then use its anchors to place other elements. Note that this is only available for elements that have a `name` argument.

```typc,example
// Name the circle
circle((0,0), name: "circle")

// Draw a smaller red circle at "circle"'s east anchor
fill(red)
stroke(none)
circle("circle.east", radius: 0.3)
```

## Named
Named anchors are normally unique to the type of element, such as a bezier curve's control points. Other border and path anchors specify their own named anchors that are available to all elements that support border or path anchors.

Elements that have an `anchor` argument also have a "default" named anchor. You can use it by just giving the element's name without an anchor.

## Border
A border anchor refers to a point on the element's border where a ray is cast from the element's center at a given angle and hits the border.

They are given as angles where `0deg` is towards the right and `90deg` is up.

Border anchors also specify named compass directions such as "north", "north-east", etc. Border anchors aslo specify a "center" named anchor which is where the ray cast originates from.

```typc,example
circle((0, 0), name: "circle", radius: 1)

set-style(content: (frame: "rect", stroke: none, fill: white, padding: .1))
content((name: "circle", anchor: 0deg), [0deg], anchor: "west")
content((name: "circle", anchor: 160deg), [160deg], anchor: "south-east")
content("circle.north", [North], anchor: "south")
content("circle.south-east", [South East], anchor: "north-west")
content("circle.south-west", [South West], anchor: "north-east")
```

## Path
A path anchor refers to a point along the path of an element. They can be given as either a {{#type number}} for an absolute distance along the path, or a {{#type ratio}} for a relative distance along the path.

Path anchors also specify three anchors "start", "mid" and "end".

```typc
line((0,0), (10, 1), name: "line")

set-style(content: (frame: "rect", stroke: none, fill: white, padding: .1))
content("line.start", [0%, 0, "start"], anchor: "east")
content("line.mid", [50%, "mid"])
content("line.end", [100%, "end"], anchor: "west")

content((name: "line", anchor: 75%), [75%])
content((name: "line", anchor: 50pt), [50pt])
```