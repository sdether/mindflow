Sample
------

```
^title -> fetch node
->
node is page
$resolved
node is page ? resolved : node is redirect
node is redirect ? fetch target : node is virtual
@node is virtual -> node has a redirect parent
node has a redirect parent ? convert to virtual redirect : resolved
convert to virtual redirect
-> fetch target
-> target is a page
  ? attach as page redirect
  : target is a redirect
attach as page redirect -> resolved
target is a redirect ? seen the target before : target is a virtual
@target is a virtual -> target has a redirect parent
seen the target before ? attach as bad redirect : fetch target
attach as bad redirect -> resolved
target has a redirect parent ? seen the target parent before : attach as virtual target
attach as virtual target -> resolved
seen the target parent before ? attach as bad redirect : convert to virtual redirect
```

```
^title
-> fetch node
-> node is page
  ? $resolved
  : node is redirect
    ? fetch target
    : @node is virtual
    -> node has a redirect parent
      ? convert to virtual redirect
      : $resolved
convert to virtual redirect
-> fetch target
-> target is a page
  ? attach as page redirect -> $resolved
  : target is a redirect
    ? seen the target before
    : @target is a virtual -> target has a redirect parent
seen the target before
  ? attach as bad redirect -> resolved
  : fetch target
target has a redirect parent
  ? seen the target parent before
    ? attach as bad redirect
    : convert to virtual redirect
  : attach as virtual target -> resolved
```

Syntax
------
* A node is any string between separators: ? : -> and newline
** newlines around a separator are ignored
** the newline separator is the statement terminator
** nodes can be repeated (i.e. for multiple nodes targeting the same with ->)
** for multiple, conflicting definitions of a node, the first is used
** conditionals are the exception, i.e. a node can be mentioned before it is used as a conditional
* The type of a node can be set by a designator: ^ $ @
** The default type of a node is `action`
** ^ - `start` (top rank)
** $ - `end` (bottom rank)
** @ - input/output
** a node followed by the ? separator is always a `conditional`
* Nodes can be grouped on the same level via a leading {group}
** {group} can be any contiguous string
** {group} immediately follows a designator if present

AST
---
```
{
  title: {
    title: 'title',
    type: 'start',
    to: 'fetch_node'
  },
  fetch_node: {
    title: 'fetch node',
    type: 'action',
    to: 'node_is_page'
  },
  node_is_page: {
    title: 'node is page',
    type: 'conditional',
    true: 'resolved',
    false: 'node_is_redirect'
  },
  resolved: {
    title: 'resolved',
    type: 'end',
  },
  node_is_redirect: {
    title: 'node is redirect',
    type: 'conditional',
    true: 'fetch_target',
    false: 'node_is_virtual
  },
  fetch_target: {
    title: 'fetch target',
    type: 'io',
    to: 'node_has_a_redirect_parent
  },
  ...
}
```