require.vim
===========


require vim easy

Note
----

now unstable, use at your own risk

Install
-------



``Plug 'gu-fan/require.vim'``



Useage
------



``export.at`` to export private value

``require.at`` to retrieve exported value


.. code:: vim   
     
     " ./private.vim
    let s:b = {"a":1,"b":2}
    fun! s:b.fun() dict
        return 3
    endfun
    let s:k = [1,2,3,4, s:b]

    " exported value will always be updated when source this file
    call export.at(s:k, expand("<sfile>:p"))

    " ./test.vim
    " require private vmodule
    let private = require.at("private", expand("<sfile>:p"))
    echom private[1] == 2
    echom type(private[4].fun) == v:t_func

``Require`` a module to ensure loaded

.. code:: vim   
     
     " ./global.vim
     func GlobalFunc()
         return "global"
     endfun

     
     " ./test.vim
     Require 'global'

     echom GlobalFunc() == "global"


- Your can require multiple modules


.. code:: vim   
     
     " ./lib/a.vim
     func GlobalFuncA()
         return "global a"
     endfun
     " ./lib/b.vim
     func GlobalFuncB()
         return "global b"
     endfun
     " ../c.vim
     func GlobalFuncC()
         return "global c"
     endfun
     " ~/.plugged/simpleterm.vim/test/require.vim
     func GlobalFuncTerm()
         return "simple terminal"
     endfun

     
     " ./test.vim
     " require multiple modules
     Require ['lib/a', 'lib/b', '../c', 'simpleterm.vim/test/require']
     echom GlobalFuncA() == "global a"
     echom GlobalFuncB() == "global b"
     echom GlobalFuncC() == "global c"
     echom GlobalFuncTerm() == "simple terminal"


- ``Export`` a plain value (no script/local value included)


.. code:: vim   

     " ./plain.vim
    let dic = {}
    func dic.fun() dict
        return "function"
    endfun

    Export {"a": 1 , "b": 2, "test": [1,2,3], "dic": dic}

    " ./test.vim
    let plain = require.at("plain", expand("<sfile>:p"))
    echom plain.a == "1"
    echom type(plain.dic.fun) == v:t_func
    



``ClearRequireCache`` to clear require cache without restart vim

``UnletExport``  clear require cache of current file, easy for deveolping


``TEST`` all test are located in test folder


Resolve
--------


when requiring a 'MODULE', it will


1. first search module in relative paths
2. then search ``g:require.user_path``
3. then search ``$VIMRUNTIME`` path


the file pattern used


- 'MODULE.vim' 
- 'MODULE/MODULE_NAME.vim'
- 'MODULE/index.vim' 
- 'plugin/MODULE.vim'


Further
-------


``g:require`` && ``g:export`` are the main objects used by plugin::


    g:require.user_path             a user_path array
                                    default ['~/.vim/plugged/']

    ---------------------------------------------------------
    core functions and values

    g:require.resolve               resolver
    g:require.source                sourcer

    g:require.modules               required modules
    g:export.values                 exported values

    g:require.at                    require function
                                    return value
                                    -1  : no value
                                    -2  : no such module

    g:export.at                     export function


|

::

    ----------------------------------------
    " use snippet if needed 

    snippet     export
    options     head
    
        call export.at(${1:value}, expand('<sfile>:p'))


    snippet     require 
    options     head
    
        let ${1:value} = require.at('${2:module}', expand('<sfile>:p'))

Thought
-------

WHY?

    1. simplify module structure and module reuse
    2. require needed value directly
    3. avoid using of &rtp, and increase speed

Author & License
----------------


Author
    gu.fan at https://github.com/gu-fan


License
    MIT License
