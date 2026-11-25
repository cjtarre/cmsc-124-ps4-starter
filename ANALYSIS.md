# The Search and the Stack

The P07 trace shows how Prolog answers `grandparent(tom,X)` through calls, failures, and backtracking. The rule finds a child of Tom, then checks that child's children. It first selects `liz`, but `parent(liz,X)` fails. Backtracking selects `bob`, whose children produce `X=ann` and `X=pat`. Prolog then exhausts its alternatives and ends the search.

```text
== trace P07 ==
Call: grandparent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: parent(liz,X)
  Fail: parent(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: parent(bob,X)
  Exit: parent(bob,ann)
Exit: grandparent(tom,ann)
answer 1: X=ann
Redo: grandparent(tom,ann)
  Redo: parent(bob,ann)
  Exit: parent(bob,pat)
Exit: grandparent(tom,pat)
answer 2: X=pat
Redo: grandparent(tom,pat)
  Redo: parent(bob,pat)
  Fail: parent(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: grandparent(tom,X)
== 2 answers ==
```

In Activity 15, a call pushes a frame containing parameters, locals, a return address, saved state, and links. `Call: grandparent(tom,X)` enters the outer call, while `Call: parent(liz,X)` enters a nested call. `Exit` corresponds to a successful return, while `Fail` unwinds an unsuccessful call. Thus, `Fail: parent(liz,X)` returns control to the grandparent rule, and `Exit: grandparent(tom,ann)` returns the first answer. These correspond to frame pops in the activity's model, although Prolog retains information for backtracking.

A `Redo` line resumes a previously successful goal to find another solution. A choice point records remaining alternatives and enough state to undo bindings made since that point. For example, `Redo: parent(tom,liz)` leads to `bob`, while `Redo: parent(bob,ann)` leads to `pat`. In Activity 15, returning from `walk(0)` uses its return address and removes its frame. Prolog preserves additional search information after success, allowing earlier decisions to be revisited. This information can occupy a separate record from the call frame.

Clause order affects `reach/2` because its recursive clause calls `reach(X,Z)` before checking an edge. Placing it first repeatedly starts another recursive call without reaching the base clause. Placing the edge-only base clause first lets `reach(a,d)` answer, although further searching can continue indefinitely. The recursive clause of `ancestor/2`, however, reads `parent(A,P)` before calling `ancestor(P,D)`. Each step follows a parent link through the supplied finite, acyclic family. Both clause orders therefore finish with the same six descendants. Their order differs because the base-first version returns direct children first, while the recursive-first version explores deeper descendants first.