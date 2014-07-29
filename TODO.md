- Improve js_of_ocaml support
- Add support for existing p4 extensions on 4.02
- Backport support for ocaml 4.00
- catch "different assumptions" exception
- there is a bug in cmi refreshing
- write syntax error message generating heuristic
- have a way to get the types of modules
  partially done by 6f7e89e6f6d0915f5b2f76d892c99434399e9a76
  now we need a clean/generic way
- Add support for ppx
- Replace Browse by a propper wrapping of Typedtree (~60%)
- Find proper API for incremental parser
  -> goto table should not be manipulated explicitly
  -> exceptions from semantic action should be caught and treated differently
  -> relying on special handling of stack bottom is not good either
     Bottom handling code improved, but we should make sure it s stable.
- Test:
  -> completion
  -> locate
  -> type enclosing / type expr
  -> occurrences
- Reimplement verbosity, expand module aliases
- When resuming after a (, lexer can get confused if it receives *:
  it will be lexed as STAR token and not beginning of comment
- Module constraint relaxation is wrong on functors argument. 
  Check how typer behaves with incorrect functors.

IDEA (later)
- perf: send batches of token rather than entering/exiting the whole
  parser barrier for each token

DONE
- recovery heuristic is really not sufficient, a few test cases easily trigger
  bad recursion:
  let f =
    let bad
    let x = () in
  fixed in b5cf1991705ea7f2138602875fe774e20b4a0a49

- with this input:
    let xxy =
      let xx = with
    let xxz : int = "hello"

    let _ = xx |;;
  completion at "|" is correct without the preceding "xx", wrong otherwise
  Also, xx generate one expected error and a Not_found exception.
  Both were due to a special case with Pexp_ident in typecore
  fixed in 8f0fd7cf0e5b7c19270c792c4bbda8f067936c5f