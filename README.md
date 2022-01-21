# vim-surround-funk

This was inspired by tpope's [vim-surround
plugin](https://github.com/tpope/vim-surround) and allows you to delete, change
and yank a surrounding function call along with its additional arguments. With
the surrounding function call in the unnamed register, you can 'grip' any text
object with it (including a different function call, see below). 'Gripping'
will wrap/encompass a word or function call with the one you have in the
unnamed register (see below).

*N.B. This plugin was formerly called surround-funk.vim, but was renamed to be
more consistent with tpope's vim-surround.*

## Table of contents
* [Feature Demos](#feature-demos)
    * [Commands for stripping function calls and gripping other objects](#commands-for-stripping-function-calls-and-gripping-other-objects)
    * [Text objects for function body and name](#text-objects-for-function-body-and-name)
* [Usage](#usage)
    * [What is a surrounding function call?](#what-is-a-surrounding-function-call)
    * [Deleting, changing and yanking a surrounding function call](#deleting-changing-and-yanking-a-surrounding-function-call)
    * [Text objects](#text-objects)
    * [Gripping a text object or motion with a function call](#gripping-a-text-object-or-motion-with-a-function-call)
    * [Settings](#settings)
        * [Turn off automatic creation of mappings](#turn-off-automatic-creation-of-mappings)
        * [Specify what characters are allowed in a function name](#specify-what-characters-are-allowed-in-a-function-name)
* [Contribution guidelines](#contribution-guidelines)
    * [Report a bug](#report-a-bug)
    * [Request a feature](#request-a-feature)
* [Installation](#installation)
* [Related plugins](#related-plugins)
* [License](#license)

## Feature demos
### Commands for stripping function calls and gripping other objects
![demo](https://github.com/Matt-A-Bennett/vim_plugin_external_docs/blob/master/vim-surround-funk/operator_1100_775_annotated.gif)
         
### Text objects for function body and name
![demo](https://github.com/Matt-A-Bennett/vim_plugin_external_docs/blob/master/vim-surround-funk/textobjects_1100_775_annotated.gif)

## Usage

(Everything in this section can also be found in Vim's help docs with `:help
surround-funk`, or just `:help funk`)

### What is a surrounding function call?

Below, the `*` symbols show what would be deleted (or yanked) with the `dsf`
(or `ysf`) command. The `^` symbols show where the cursor can be when issuing
the command:

```
sf: Where the name of the function (e.g. outerfunc) is a standard Vim word.

   **********               *
np.outerfunc(innerfunc(arg1))
   ^^^^^^^^^^               ^

   **********               *************
np.outerfunc(innerfunc(arg1), arg2, arg3)
   ^^^^^^^^^^               ^^^^^^^^^^^^^

   **********    *
np.outerfunc(arg1)
   ^^^^^^^^^^^^^^^

   **********                *
np.outerfunc(arg1, arg2, arg3)
   ^^^^^^^^^^^^^^^^^^^^^^^^^^^

# multi-line function calls also work:

   **********               ***************
np.outerfunc(innerfunc(arg1),   
                                arg2, arg3) < anywhere on this line is fine too
   ^^^^^^^^^^               ^^^^^^^^^^^^^^^
```

```
sF: Where the name of the function (e.g. np.outerfunc) is similar to a Vim
    WORD, but is additionally delimited by any character not in
    'g:surround_funk_legal_func_name_chars' (see below)

*************               *
np.outerfunc(innerfunc(arg1))
^^^^^^^^^^^^^               ^

*************               *************
np.outerfunc(innerfunc(arg1), arg2, arg3)
^^^^^^^^^^^^^               ^^^^^^^^^^^^^

*************    *
np.outerfunc(arg1)
^^^^^^^^^^^^^^^^^^

*************                *
np.outerfunc(arg1, arg2, arg3)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

# multi-line function calls also work:

*************               ***************
np.outerfunc(innerfunc(arg1),   
                                arg2, arg3) < anywhere on this line is fine too
^^^^^^^^^^^^^               ^^^^^^^^^^^^^^^
```                                       

### Deleting, changing and yanking a surrounding function call

If you have tpope's excellent [repeat.vim
plugin](https://github.com/tpope/vim-repeat), then the `dsf` and `dsF` commands
are repeatable with the dot command.

To prevent these mappings from being generated, and define your own custom
ones, see `g:surround_funk_create_mappings`
[below](#turn-off-automatic-creation-of-normal-mode-mappings).

```
dsf: Delete surrounding function call

dsF: Like 'dsf', but the function name is delimited by any character not in 
     'g:surround_funk_legal_func_name_chars' (see below)

csf: Like 'dsf' but start instert mode where the opening parenthesis of the
     changed function used to be

csF: Like 'csf', but the function name is delimited by any character not in 
     'g:surround_funk_legal_func_name_chars' (see below)

ysf: Yank surrounding function call 

ysF: Like 'ysf', but the function name is delimited by any character not in
     'g:surround_funk_legal_func_name_chars' (see below)
```

### Text objects

The following text objects are made available by surround-funk:

To prevent these mappings from being generated, and define your own, see
`g:surround_funk_create_mappings`
[below](#turn-off-automatic-creation-of-normal-mode-mappings).

```
af: From the first letter of the function's name to the closing parenthesis of
    that function call

aF: Like 'af', but the function name is delimited by any character not in 
    'g:surround_funk_legal_func_name_chars' (see below)

if: Alias of 'af'

iF: Alias of 'aF'

an: The function's name

aN: Like 'an', but the function name is delimited by any character not in 
    'g:surround_funk_legal_func_name_chars' (see below)

in: Alias of 'an'

iN: Alias of 'aN'
```

For example, with the cursor anywhere indicated by the `^` symbols, doing `vif`
will visually select the entire function call, indicated by the `*` symbols (to
include the `np.` part of the function name, use `viF`):

```
   **************************************
np.outerfunc(innerfunc(arg1), arg2, arg3)
   ^^^^^^^^^^               ^^^^^^^^^^^^^
```

To select just the function's name, use `vin` (again, use `viN` to include the
`np.` part):

```
   *********
np.outerfunc(innerfunc(arg1), arg2, arg3)
   ^^^^^^^^^^               ^^^^^^^^^^^^^
```

### Gripping a text object or motion with a function call

If you have tpope's excellent [repeat.vim
plugin](https://github.com/tpope/vim-repeat), then the following command is
repeatable with the dot command.

To prevent these mappings from being generated, and define your own see
`g:surround_funk_create_mappings`
[below](#turn-off-automatic-creation-of-normal-mode-mappings).

```
gs: Grip (i.e wrap/encompass) any text object or motion with the function call
    in the unnamed register.
```

In the example below, with the cursor anywhere with a `^` symbol, you can do
`ysF` to 'yank the surrounding function call' (which is all the stuff with `*`
above it):

```
*************               *************
np.outerfunc(innerfunc(arg1), arg2, arg3)
^^^^^^^^^^^^^               ^^^^^^^^^^^^^
```

Then go to some other function call (or just a word) (the cursor can be
anywhere in this case)

```
os.lonelyfunc(argA, argB)
^^^^^^^^^^^^^^^^^^^^^^^^^
```

And do `gsiF` or `gsaF` to 'grip/surround' the lonely function call with the
yanked one:

```
*************                         *************
np.outerfunc(os.lonelyfunc(argA, argB), arg2, arg3)
^
```

You could then move to a word:

```
MeNext
^^^^^^
```

and grip/surround it with `gsiw`

```
*************      *************
np.outerfunc(MeNext, arg2, arg3)
^
```

You could also grip a multi-line function call (again using `gsiF` or `gsaF`):

```
**************      *******
os.multi_line(argA(),
                argB, argC) < anywhere on this line is fine too
^^^^^^^^^^^^^^  ^^^^^^^^^^^
```

To get:

```
*************                    *
np.outerfunc(os.multi_line(argA(),
                argB, argC), arg2, arg3)
                           *************
```

### Settings
#### Turn off automatic creation of mappings

By default surround-funk creates the above mappings. If you would
rather it didn't do this (for instance if you already have those key
combinations mapped to something else) you can turn them off with:

```vim
let g:surround_funk_create_mappings = 0
```

And map them to something different with:

```vim
nmap <your-map-here> <Plug><OperationToMap>
```

For reference, the default mappings are as follows:

```vim
" normal mode
nmap <silent> dsf <Plug>DeleteSurroundingFunction
nmap <silent> dsF <Plug>DeleteSurroundingFUNCTION
nmap <silent> csf <Plug>ChangeSurroundingFunction
nmap <silent> csF <Plug>ChangeSurroundingFUNCTION
nmap <silent> ysf <Plug>YankSurroundingFunction
nmap <silent> ysF <Plug>YankSurroundingFUNCTION

" visual mode
xmap <silent> af <Plug>SelectWholeFunction
omap <silent> af <Plug>SelectWholeFunction
xmap <silent> aF <Plug>SelectWholeFUNCTION
omap <silent> aF <Plug>SelectWholeFUNCTION
xmap <silent> if <Plug>SelectWholeFunction
omap <silent> if <Plug>SelectWholeFunction
xmap <silent> iF <Plug>SelectWholeFUNCTION
omap <silent> iF <Plug>SelectWholeFUNCTION
xmap <silent> an <Plug>SelectFunctionName
omap <silent> an <Plug>SelectFunctionName
xmap <silent> aN <Plug>SelectFunctionNAME
omap <silent> aN <Plug>SelectFunctionNAME
xmap <silent> in <Plug>SelectFunctionName
omap <silent> in <Plug>SelectFunctionName
xmap <silent> iN <Plug>SelectFunctionNAME
omap <silent> iN <Plug>SelectFunctionNAME

" operator pending mode
nmap <silent> gs <Plug>GripSurroundObject
vmap <silent> gs <Plug>GripSurroundObject
```

#### Specify what characters are allowed in a function name

By default the 'surround-funk' plugin defines any vim word character
(`[0-9A-Za-z_]`) and any period symbols as valid parts of a function's name.
These characters are used to find the function name when using the capitalised
(e.g. `dsF`, but not `dsf`) versions of the above commands. You can add to, or
remove from, these groups.

The default:

```vim
g:surround_funk_legal_func_name_chars = ['[0-9]', '[A-Z]', '[a-z]', '_', '\.']
```

will match function names like:

```
      *********  *************  **************
...), pd.mean(), np2.my_func(), 8np.my._func(), ...
      ^^^^^^^^^  ^^^^^^^^^^^^^  ^^^^^^^^^^^^^^
```

but would stop at characters not in the legal name set (e.g. `@`, `#`, `/`):

```
         ******         ******      **********
...), pd@mean(), np2.my#func(), 8np/my._func(), ...
         ^^^^^^         ^^^^^^      ^^^^^^^^^^
```

To make numbers illegal, and to introduce `#` and `@` as legal characters, use:

```vim
let g:surround_funk_legal_func_name_chars = ['[A-Z]', '[a-z]', '_', '\.', '#', '@']
```

will match function names like:

```
      *********     **********   *************
...), pd.mean(), np2.my_func(), 8np.my._func(), ...
      ^^^^^^^^^     ^^^^^^^^^^   ^^^^^^^^^^^^^

      *********     **********      **********
...), pd@mean(), np2.my#func(), 8np/my._func(), ...
      ^^^^^^^^^     ^^^^^^^^^^      ^^^^^^^^^^
```

## Installation

Use your favorite plugin manager.

- [Vim-plug][vim-plug]

    ```vim
    Plug 'Matt-A-Bennett/vim-surround-funk'
    ```

- [NeoBundle][neobundle]

    ```vim
    NeoBundle 'Matt-A-Bennett/vim-surround-funk'
    ```

- [Vundle][vundle]

    ```vim
    Plugin 'Matt-A-Bennett/vim-surround-funk'
    ```

- [Pathogen][pathogen]

    ```sh
    git clone git://github.com/Matt-A-Bennett/vim-surround-funk.git ~/.vim/bundle/vim-surround-funk
    ```

[neobundle]: https://github.com/Shougo/neobundle.vim
[vundle]: https://github.com/gmarik/vundle
[vim-plug]: https://github.com/junegunn/vim-plug
[pathogen]: https://github.com/tpope/vim-pathogen

## Contribution guidelines

### Report a bug
First, check if the bug is already known by seeing whether it's listed on the
[surround-funk todo list](https://github.com/Matt-A-Bennett/vim_plugin_external_docs/blob/master/vim-surround-funk/todo.md).

If it's not there, then please raise a [new
issue](https://github.com/Matt-A-Bennett/vim-surround-funk/issues) (or submit a
pull request) so I can fix it. To make it easier, you can use the following
template (If multi-line was broken, you could show me something like this):

---

Command used: `dsF`

Reproducible example (with arrows showing where the cursor was, in this case,
the 'e' in 'mean'):

```
np.mean(st.std(arg1),     <---
            arg2, arg3)

    ^
    |
```

Result:

```
np.mean(st.std(arg1),     <---
    arg2, ar

^
|
```

Expected:

```
st.std(arg1)  <---
            
^
|
```

---

### Request a feature
First, check if the feature is already planned by looking at the 
[surround-funk todo list](https://github.com/Matt-A-Bennett/vim_plugin_external_docs/blob/master/vim-surround-funk/todo.md).

If it's not there, then please raise a [new
issue](https://github.com/Matt-A-Bennett/vim-surround-funk/issues) describing what
you would like and I'll see what I can do! If you would like to submit a pull
request, then do so (please let me know this is your plan first in a [new issue](https://github.com/Matt-A-Bennett/vim-surround-funk/issues)).

## Related plugins
 - [vim-surround](https://github.com/tpope/vim-surround)
 - [vim-sandwich](https://github.com/machakann/vim-sandwich)
## License
 Copyright (c) Matthew Bennett. Distributed under the same terms as Vim itself.
 See `:help license`.

