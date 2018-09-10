[![GoDoc](https://godoc.org/github.com/neovim/go-client/nvim?status.svg)](https://godoc.org/github.com/neovim/go-client/nvim)
[![Build Status](https://travis-ci.org/neovim/go-client.svg?branch=master)](https://travis-ci.org/neovim/go-client)

Neovim/go-client is a [Neovim](https://neovim.io/) client and plugin host for [Go](https://golang.org/).

This example plugin adds the Hello command to Nvim.

```go
package main

import (
    "strings"
    "github.com/neovim/go-client/nvim/plugin"
)

func hello(args []string) (string, error) {
    return "Hello " + strings.Join(args, " "), nil
}

func main() {
    plugin.Main(func(p *plugin.Plugin) error {
        p.HandleFunction(&plugin.FunctionOptions{Name: "Hello"}, hello)
        return nil
    })
}
```

Build the program with the [go tool](https://golang.org/cmd/go/) to an
executable named `hello`. Ensure that the executable is on your path.

Add the following plugin to Nvim:

```vim
if exists('g:loaded_hello')
  finish
endif
let g:loaded_hello = 1

function! s:Requirehello(host) abort
  " 'hello' is the binary created by compiling the program above.
  return jobstart(['hello'], {'rpc': v:true})
endfunction

call remote#host#Register('hello', 'x', function('s:Requirehello'))
" The following lines are generated by running the program
" command line flag --manifest hello
call remote#host#RegisterPlugin('hello', '0', [
    \ {'type': 'function', 'name': 'Hello', 'sync': 1, 'opts': {}},
    \ ])

" vim:ts=4:sw=4:et
```

Start Nvim and run the following command:

```vim
:echo Hello('world')
```
