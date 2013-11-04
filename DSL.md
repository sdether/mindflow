Sample
------

```
^title -> fetch node -> node is page
$resolved
node is page +> resolved !> node is redirect
node is redirect +> fetch target !> node is virtual
@node is virtual -> node has a redirect parent
?node has a redirect parent +> convert to virtual redirect !> resolved
convert to virtual redirect -> fetch target -> target is a page
?target is a page +> attach as page redirect !> target is a redirect
attach as page redirect -> resolved
?target is a redirect +> seen the target before !> target is a virtual
@target is a virtual -> target has a redirect parent
?seen the target before +> attach as bad redirect !> fetch target
attach as bad redirect -> resolved
?target has a redirect parent +> seen the target parent before !> attach as virtual target
attach as virtual target -> resolved
?seen the target parent before +> attach as bad redirect !> convert to virtual redirect
```

Syntax
------
* A node is any string between line start, markers and line end
* A node can only appear once at the beginning of a line
* {rank} is always optional and only used to force multiple nodes to be rendered on the same level
* Markers for determining the type of node (must precede node at beginning of line or after flow `follows`)
** {rank} - action
** ^ - start symbol (top rank)
** $ - end symbol (bottom rank)
** ?{rank} - conditional. must be followed by +> and !>
** @{rank} - input/output
* Flow control (must follow a node name)
** -> - `follows` creates unmarked arrow from LHS to RHS. May be chained on line
** +> - `yes` conditional for ? marker
** !> - `no` conditional for ? marker