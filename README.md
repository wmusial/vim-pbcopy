vim-pbcopy
==========
This is a Vim plugin that exposes a `cy` mapping in Visual mode and a
`cy{motion}` mapping in Normal mode which both attempt to yank the
selected/moved-over text and pipe it (via `ssh` if necessary) to the
`pbcopy` command on a Mac OS X client. What does this mean? It means you can
use `cy` wherever you'd normally use `y` and have that text available in
your Mac OS X system clipboard, whether you're working locally or remotely.
It also performs a simple `y` yank operation on your selection so you'll
have it in the default `"` yank register as well.

Installation
------------
If you don't have a preferred installation method, I recommend
installing [pathogen.vim](https://github.com/tpope/vim-pathogen), and
then simply copy and paste:

    cd ~/.vim/bundle
    git clone https://github.com/ahw/vim-pbcopy.git


Local Usage
-----------
Use `cy{motion}` to copy text to the Mac OSX system clipboard or hit `cy`
after selecting text in Visual mode. In the background it is simply running
running 

```sh
echo -n "whatever text you copied" | pbcopy
```

You can configure the local pbcopy command in your `~/.vimrc` file:

> **~/.vimrc**
>
> ```vim
> let g:vim_pbcopy_local_cmd = "pbcopy"
> ```


Remote Usage
------------
Nothing changes except you need to set the Vim global variable
`g:vim_pbcopy_remote_cmd` variable in your `~/.vimrc` file to your preferred
way of running pbcopy on your local machine.

> **~/.vimrc**
>
> ```vim
> let g:vim_pbcopy_remote_cmd = "ssh your-mac-laptop.example.com pbcopy"
> ```

This will pipe the copied text to your Mac client's `pbcopy` over SSH.

```sh
echo -n "whatever text you copied" | ssh your-mac-laptop.example.com pbcopy
```

This assumes that you have an SSH server running on your laptop, of course.
Note: I haven't tested this using password-based SSH logins (my
configuration uses SSH keys).

See section `Related Projects` below for other remote usage possiblities. 

Troubleshooting
---------------

### Problems with Newlines
There are some inconsistencies around the way backslashes are escaped on
different systems. Try copying some text with `\` and `\n` characters. If
you're getting incorrect output when you then try to paste that text somewhere,
see the table below for how to remedy.


If you copied this          | and pasted this               | add this to ~/.vimrc
---                         | ---                           | ---
console.log('some\nthing'); | console.log('some\nthing');   | Nothing! It Just Works&trade;
console.log('some\nthing'); | console.log('some<br>thing'); | `let g:vim_pbcopy_escape_backslashes = 1`
console.log('some\nthing'); | console.log('some\\\nthing'); | `let g:vim_pbcopy_escape_backslashes = 0`


Related Projects
================

### [clipper](https://github.com/wincent/clipper)

> Clipper is an OS X 'launch agent' that runs in the background providing a
> service that exposes the local clipboard to tmux sessions and other
> processes running both locally and remotely. ... We can use it from any
> process, including Vim
>
> Source: https://github.com/wincent/clipper/blob/master/README.md

`cipper` listens to a select localhost port, and put all input in the system
clipboard.
This is useful for both local and remote use: locally, using `clipper` rather 
than `pbcopy` circumvents known problems with `pbcopy` and `tmux`. 
Remotely, you can forward a remote port to the local clipper's port via an
ssh reverse tunnel. 

To this end, set up a reverse tunnel in your `~/.ssh/config`:

> **~/.ssh/config**
>
> ```
> Host my.remote.host
>   RemoteForward 8377 localhost:8377
> ```

Alternatively, ssh with `-R`:

```sh
$ ssh -R 8377 my.remote.host
```

Then pipe vim selection to localhost:8377. 
> **~/.vimrc**
>
> ```vim
> let g:vim_pbcopy_local_cmd = "cat > /dev/tcp/localhost/8377"
> let g:vim_pbcopy_remote_cmd = "cat > /dev/tcp/localhost/8377"
> ```






