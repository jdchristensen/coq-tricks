# Tricks in Coq

Some tips, tricks, and features in Coq that are hard to discover.

If you have a trick you've found useful feel free to submit an issue or pull request!

## Ltac
* `pattern` tactic
* `lazymatch` for better error messages
* `deex` tactic
* `::=` to re-define Ltac
* `learn` approach - see [Clément's thesis](http://pit-claudel.fr/clement/MSc/#org036d20e)
* `unshelve` tactical, especially useful with an eapply - good example use case is constructing an object by refinement where the obligations end up being your proofs with the values as evars, when you wanted to construct the values by proof
* `unfold "+"` works
* `destruct matches` tactic
* using `instantiate` to modify evar environment (thanks to Jonathan Leivent on coq-club)
* `eexists ?[x]` lets one name an existential variable to be able to refer to it later
* strong induction is in the standard library: `Require Import Arith.` and use `induction n as [n IHn] using lt_wf_ind.`
* induction on the length of a list: `Require Import Coq.Arith.Wf_nat.` and `induction xs as [xs IHxs] using (induction_ltof1 _ (@length _)); unfold ltof in IHxs.`
* `debug auto`, `debug eauto`, and `debug trivial` give traces, including failed invocations. `info_auto`, `info_eauto`, and `info_trivial` are less verbose ways to debug which only report what the resulting proof includes
* `constructor` and `econstructor` backtrack over the constructors over an inductive, which lets you do fun things exploring the constructors of an inductive type. See [Constructors.v](Constructors.v) for some demonstrations.
* There's a way to destruct and maintain an equality: `destruct_with_eqn x`.
  You can also do `destruct x eqn:H` to explicitly name the equality
  hypothesis. This is similar to `case_eq x; intros`; I'm not sure what the
  practical differences are.
* `rew H in t` notation to use `eq_rect` for a (safe) "type cast". Need to
  import `EqNotations` - see [RewNotation.v](RewNotation.v) for a working
  example.
* `intro`-patterns can be combined in a non-trivial way: `intros [=->%lemma]` -- see [IntroPatterns.v](IntroPatterns.v).
* `change` tactic supports patterns (`?var`): e.g. `repeat change (fun x => ?f x) with f` would eta-reduce all the functions (of arbitrary arity) in the goal.

## Gallina
* tactics in terms, eg `ltac:(eauto)` can provide a proof argument
* maximally inserted implicit arguments are implicit even when for identifier alone (eg, `nil` is defined to include the implicit list element type)
* maximally inserted arguments can be defined differently for different numbers of arguments - undocumented but [`eq_refl` provides an example](https://github.com/coq/coq/blob/trunk/theories/Init/Logic.v#L297-298)
* `r.(Field)` syntax: same as `Field r`, but convenient when `Field` is a projection function for the (record) type of `r`.
* `Function` vernacular provides a more advanced way to define recursive functions, which removes the restriction of having a structurally decreasing argument; you just need to specify a well-founded relation or a decreasing measure maps to a nat, then prove all necessary obligations to show this function can terminate. See [manual](https://coq.inria.fr/refman/Reference-Manual004.html#sec78) and examples in `Function.v` for more details.

  Two alternatives are considerable as drop-in replacements for `Function`.
  * `Program Fixpoint` may be useful when defining a nested recursive function. See [manual](https://coq.inria.fr/refman/program.html#hevea_command290) and [this StackOverflow post](https://stackoverflow.com/questions/10292421/error-in-defining-ackermann-in-coq).
  * [CPDT's way](http://adam.chlipala.net/cpdt/html/Cpdt.GeneralRec.html) of defining general recursive functions with `Fix` combinator.
* One can pattern-match on tuples under lambdas: `Definition fst {A B} : (A * B) -> A := fun '(x,_) => x.` (works since Coq 8.6).

## Other Coq commands
* `Search` vernacular variants; see [Search.v](Search.v) for examples.
* `Search s -Learnt` for a search of local hypotheses excluding Learnt
* `Locate` can search for notation, including partial searches.
* `Optimize Heap` (undocumented) runs GC (specifically [`Gc.compact`](https://caml.inria.fr/pub/docs/manual-ocaml/libref/Gc.html))
* `Optimize Proof` (undocumented) runs several simplifications on the current proof term (see [`Proofview.compact`](https://github.com/coq/coq/blob/9a4ca53a3a021cb16de7706ec79a26e49f54de49/engine/proofview.ml#L40))
