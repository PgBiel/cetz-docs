# Coordinate Systems
A {{#type coordinate}} is a position on the canvas on which the picture is drawn. They take the form of dictionaries and the following sub-sections define the key value pairs for each system. Some systems have a more implicit form as an array of values and CeTZ attempts to infer the system based on the element types.

## XYZ
Defines a point `x` units right, `y` units upward and `z` units away.

<parameter-definition name="x" types="number" default="0">
The number of units in the `x` direction.
</parameter-definition>

<parameter-definition name="y" types="number" default="0">
The number of units in the `y` direction.
</parameter-definition>

<parameter-definition name="z" types="number" default="0">
The number of units in the `z` direction.
</parameter-definition>

The implicit form can be given as an array of two or three {{#type number}}s, as in `(x, y)` or `(x, y, z)`

```typc,example
line((0,0), (x: 1))
line((0,0), (y: 1))
line((0,0), (z: 1))

// Implicit form
line((2, 0), (3, 0))
line((2, 0), (2, 1, 0))
line((2, 0), (2, 0, 1))
```

## Previous
Use this to reference the position of the previous coordinate passed to a draw function. It takes the form of an empty array `()` and the previous position initially will be `(0, 0, 0)`. This will never reference the position of a coordinate used to define another coordinate.

```typc,example
line((0,0), (1, 1))

// Draws a circle at (1,1)
circle(())
```


## Relative
Places the given coordinate relative to the previous coordinate. Or in other words, for the given coordinate, the previous coordinate will be used as the origin. Another coordinate can be given to act as the previous coordinate instead.

<parameter-definition name="rel" types="coordinate">
The coordinate to place relative to the previous coordinate.
</parameter-definition>

<parameter-definition name="update" types="bool" default="true">
When false the previous the previous position will not be updated.
</parameter-definition>

<parameter-definition name="to" types="coordinate" default="()">
The coordinate to treat as the previous coodinate.
</parameter-definition>

In the example below, the red circle is placed one unit to the right of the blue circle. If the blue circle was to be moved a different position, the red circle will move with the blue circle to stay one unit to the right.

```typc,example
circle((0, 0), stroke: blue)
circle((rel: (1, 0)), stroke: red)
```

## Polar
Defines a point that is `radius` distance away from the origin at the given angle.

<parameter-definition name="angle" types="angle">
The angle of the coordinate. An angle of `0deg` is to the right, a degree of `90deg` is upward.
</parameter-definition>

<parameter-definition name="radius" types="number,array">
The distance from the origin. An array of {{#type number}} can be given, in the form `(x, y)`, to define the `x` and `y` radii of an ellipse instead of a circle.
</parameter-definition>

```typc,example
line((0, 0), (angle: 30deg, radius: 1))
```

The implicit form is an array of the angle then the radius `(angle, radius)` or `(angle, (x, y))`.

```typc,example
line(
  (0, 0),
  (30deg, 1),
  (60deg, 1),
  (90deg, 1),
  (120deg, 1),
  (150deg, 1),
  (180deg, 1)
)
```


## Barycentric
In the barycentric coordiante system a point is expressed as the linear combination of multiple vectors. The idea is that you specify vectors \\(v_1, v_2, ..., v_n\\) and numbers \\(\alpha_1, \alpha_2, ..., \alpha_n\\). Then the barycentric coordinate specified by these vectors and numbers is \\[\frac{\alpha_1 v_1 + \alpha_2 v_2 + \cdots + \alpha_n v_n}{\alpha_1 + \alpha_2 + \cdots + \alpha_n}\\]

<parameter-definition name="bary" types="dictionary">
A dictionary where the key is a named element and the value is a {{#type float}}. The `center` anchor of the named element is used as \\(v\\) and the value is used as \\(\alpha\\)
</parameter-definition>

```typc,example
circle((90deg, 3), radius: 0, name: "content")
circle((210deg, 3), radius: 0, name: "structure")
circle((-30deg, 3), radius: 0, name: "form")

for (c, a) in (
  ("content", "south"),
  ("structure", "north"),
  ("form", "north")
) {
  content(c, align(center, c + [\ oriented]), padding: .1, anchor: a)
}

stroke(gray + 1.2pt)
line("content", "structure", "form", close: true)

for (c, s, f, cont) in (
  (0.5, 0.1, 1, "PostScript"),
  (1, 0, 0.4, "DVI"),
  (0.5, 0.5, 1, "PDF"),
  (0, 0.25, 1, "CSS"),
  (0.5, 1, 0, "XML"),
  (0.5, 1, 0.4, "HTML"),
  (1, 0.2, 0.8, "LaTeX"),
  (1, 0.6, 0.8, "TeX"),
  (0.8, 0.8, 1, "Word"),
  (1, 0.05, 0.05, "ASCII")
) {
  content(
    (bary: (
      content: c, 
      structure: s, 
      form: f
    )),
    cont,
    fill: rgb(50, 50, 255, 100),
    stroke: none,
    frame: "circle"
  )
}
```

## Anchor
Defines a piont relative to a named element using anchors, see [Anchors](anchors.md).

<parameter-definition name="name" types="str">
The name of the element that you wish to use to specify a coordinate.
</parameter-definition>

<parameter-definition name="anchor" types="str,angle,number,ratio,none" default="none">
The anchor of the element. Strings are named anchors, angles are border anchors and numbers and ratios are path anchors. If not given, the default anchor will be used, on most elements this is `center` but it can be different or not exsist at all!
</parameter-definition>

```typc,example
circle((0,0), name: "circle")
// Anchor at 30 degree
content((name: "circle", anchor: 30deg), box(fill: white, $ 30 degree $))
// Anchor at 30% of the path length
content((name: "circle", anchor: 30%), box(fill: white, $ 30 % $))
// Anchor at 3.14 of the path
content((name: "circle", anchor: 3.14), box(fill: white, $ p = 3.14 $))
```

You can also use implicit syntax of a dot separated string in the form `"name.anchor"` for all anchors.

```typc,example
line((0, 0), (4, 3), name: "line")
circle("line.75%", name: "circle")
rect("line.start", "circle.east")
```

## Tangent
This system allows you to compute the point that lies tangent ot a shape. In detail, consider an element and a point. Now draw a straight line from the point so that it "touches" the element (more formally, so that it is _tangent_ to this element). The point where the line touches the shape is the point referred to by this coordinate system.

<parameter-definition name="element" types="str">
The name of the element on whose border the tangent should lie.
</parameter-definition>

<parameter-definition name="point" types="coordinate">
The point through which the tangent should go.
</parameter-definition>

<parameter-definition name="solution" types="int">
Which solution should be used if there are more than one.
</parameter-definition>

A special algorithm is needed in order to compute the tangent for a given shape. Currently it does this by assuming the distance between the center and top anchor (See [Anchors](anchors.md)) is the radius of a circle.

```typc,example
grid((0,0), (3,2), help-lines: true)

circle((3,2), name: "a", radius: 2pt)
circle((1,1), name: "c", radius: 0.75)
content("c", $ c $, anchor: "north-east", padding: .1)

stroke(red)
line(
  // The starting point
  "a",
  // The tangent coordinate
  (element: "c", point: "a", solution: 1),
  // The center of the circle
  "c",
  // The other tangent coordinate
  (element: "c", point: "a", solution: 2),
  close: true
)
```

## Perpendicular
Can be used to find the intersection of a vertical line going through a point \\(p\\) and a horizontal line going through some other point \\(q\\).

<parameter-definition name="horizontal" types="coordinate">
The coordinate through which the horizontal line passes.
</parameter-definition>

<parameter-definition name="vertical" types="coordinate">
The coordinate through which the vertical line passes.
</parameter-definition>

You can use the implicit syntax of `(horizontal, "-|", vertical)` or `(vertical, "|-", horizontal)`.

```typc,example
set-style(content: (padding: .05))
content((30deg, 1), $ p_1 $, name: "p1")
content((75deg, 1), $ p_2 $, name: "p2")

line((-0.2, 0), (1.2, 0), name: "xline")
content("xline.end", $ q_1 $, anchor: "west")

line((2, -0.2), (2, 1.2), name: "yline")
content("yline.end", $ q_2 $, anchor: "south")

line("p1.south-east", (horizontal: (), vertical: "xline.end"))
line("p2.south-east", ((), "|-", "xline.end")) // Short form
line("p1.south-east", (vertical: (), horizontal: "yline.end"))
line("p2.south-east", ((), "-|", "yline.end")) // Short form
```

## Interpolation
Use this to linearly interpolate between two coordinates `a` and `b` with a given distance `number`. If `number` is a {{#type number}} the position will be at the absolute distance away from `a` towards `b`, a {{#type ratio}} can be given instead to be the relative distance between `a` and `b`. An {{#type angle}} can also be given for the general meaning: "First consider the line from `a` to `b`. Then rotate this line by `angle` around point `a`. Then the two endpoints of this lien will be `a` and some point `c`. Use the point `c` for the subsequent computation."

<parameter-definition name="a" types="coordinate">
The coordinate to interpolate from.
</parameter-definition>

<parameter-definition name="b" types="coordinate">
The coordinate to interpolate to.
</parameter-definition>

<parameter-definition name="number" types="ratio,number">
The distance between `a` and `b`. A {{#type ratio}} will be the relative distance between the two points, a {{#type number}} will be the absolute distance between the two points.
</parameter-definition>

<parameter-definition name="angle" types="angle" default="0deg">
Angle between \\(\vec{AB}\\) and \\(\vec{AP}\\), where \\(P\\) is the resulting coordinate. This can be used to get the _normal_ for a tangent between two points.
</parameter-definition>

Can be used implicitly as an array in the form `(a, number, b)` or `(a, number, angle, b)`.

```typc,example
grid((0,0), (3,3), help-lines: true)

line((0,0), (2,2), name: "a")
for i in (0%, 20%, 50%, 80%, 100%, 125%) { // Relative distance
  content(("a.start", i, "a.end"),
  box(fill: white, inset: 1pt, [#i]))
}

line((1,0), (3,2), name: "b")
for i in (0, 0.5, 1, 2) { // Absolute distance
  content(("b.start", i, "b.end"),
  box(fill: white, inset: 1pt, text(red, [#i])))
}
```

---

```typc,example
grid((0,0), (3,3), help-lines: true)
line((1,0), (3,2))
line((1,0), ((1, 0), 1, 10deg, (3,2)))
fill(red)
stroke(none)
circle(((1, 0), 50%, 10deg, (3, 2)), radius: 2pt)
```

---

```typc,example
grid((0,0), (4,4), help-lines: true)

fill(black)
stroke(none)
let n = 16
for i in range(0, n+1) {
  circle(((2,2), i / 8, i * 22.5deg, (3,2)), radius: 2pt)
}
```

You can even chain them together!

```typc,example
grid((0,0), (3, 2), help-lines: true)
line((0,0), (3,2))
stroke(red)
line(((0,0), 0.3, (3,2)), (3,0))
fill(red)
stroke(none)
circle(
  ( // a
    (((0, 0), .3, (3, 2))),
    0.7,
    (3,0)
  ),
  radius: 2pt
)
```

---

```typc,example
grid((0,0), (3, 2), help-lines: true)
line((1,0), (3,2))
for (l, c) in ((0cm, "0cm"), (1cm, "1cm"), (15mm, "15mm")) {
  content(((1,0), l, (3,2)), box(fill: white, $ #c $))
}
```

Interpolation coordinates can be used to get the _normal_ on a tangent:

```typc,example
let (a, b) = ((0,0), (3,2))
line(a, b)
// Get normal for tangent from a to () with distance .5, at a
circle(a, radius: .1, fill: black)
line((a, .7, b), (a: (), b: a, number: .5, angle: 90deg), stroke: red)
```


## Function
An array where the first element is a function and the rest are coordinates will cause the function to be called with the resolved coordinates. The resolved coordinates will be given as a {{#type vector}} that represents an xyz point in space.

The example below shows how to use this system to create an offset from an anchor, however this could easily be replaced with a [relative coordinate](#relative) with the `to` argument set. 

```typc,example
circle((0, 0), name: "c")
fill(red)
circle((v => cetz.vector.add(v, (0, -1)), "c.west"), radius: 0.3)
```