## 112.24.00

- Reduce code generated by `pa_test` in favor of more code in `pa_test_lib`.
  + Less generated code means less time spent compiling it.
  + Reducing code in `pa_test.ml` is good, because:
      a) Staging semantics make this code hard to understand.
      b) This code is written using the less familiar revised OCaml syntax.
      b) We get less code to migrate to syntax extensions.

- Re-used location code in `pa_here`; side benefit that we get full path names
  in (Loc...) instead of just the file's basename.

## 111.08.00

- Made it possible to use `<:test_eq< >>` and friends in `Core_kernel`.

    Removed the dependency of the code generated by `pa_test` on
    `Core_kernel`, so `pa_test` can be used there.

## 110.01.00

- For `<:test_result< >>`, renamed `~expected` to `~expect`

## 109.53.00

- Bump version number

## 109.52.00

- Added an optional `?message` argument to `<:test_eq<>>`,
  `<:test_result<>>` and `<:test_pred<>>`.

## 109.47.00

- Added new syntax `<:test_result<>>`, which is similar to `<:test_eq<>>`.

    `test_result` is meant for the common case of comparing some hardcoded
    expected value against one returned by some code you're testing.  In
    this case you can now write, for example:

    ```ocaml
    <:test_result<int>> 3 ~expected:4
    ```

    which behaves exactly the same way as:

    ```ocaml
    <:test_eq<int>> 3 4
    ```

    except that the error message is something like:

    ```
    ("got unexpected result"
      ((got 3) (expected 4)
        (Loc "File \"z.ml\", line 44, characters 25-25")))
    ```

    rather than:

    ```
    ("comparison failed"
      (3 vs 4
        (Loc "File \"z.ml\", line 44, characters 25-25")))
    ```

    The additional labels on the values help identify the problem earlier.

## 109.34.00

- Added new quotation expanders `<:test_eq< >>` and `<:test_pred< >>`.

    These can be used to reduce boilerplate in testing code.

    - `<:test_eq< type >>` expands to a function of type
      ```ocaml
        ?(f = (fun x y -> <:compare< type >> x y = 0))
      -> ?here:Lexing.position list
      -> type
      -> type
      -> unit
      ```
      that throws a nice exception if the values are not equal according to `f`.

    - `<:test_pred< type >>` expands to a function of type
      ```ocaml
      ?here:Lexing.position list -> (type -> bool) -> type -> unit
      ```
      that throws a nice exception if the predicate fails on the value.

