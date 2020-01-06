---
layout: single
title:  "Fix history: Edit the last bash command"
date:   2020-01-05
header:
  image: "/assets/images/fix-history.jpg"
  image_description: "fix history: edit the last bash command"
classes: wide
category: Bash
excerpt: TL;DR Edit previous bash commands with the "fc" and "^str1^str2" builtins.
tags: 
- productivity
- vim
- bash
---

How many times have you typed a long command and quickly pressed enter, only to find out that you misspelled something or missed an argument? 

As a CLI user, you probably look to minimize the number of actions it takes to get a task done - so instead of rewriting the entire command (or arrowing up and changing the string), you can easily replace a string in your command with builtin bash tools.

## Event Designators: Quick Substitutions
> An event designator is a reference to a command line entry in the history list. 

The quick substitution is performed by using the following syntax: `^str1^str2^`, which means that the last command will be run again with `str1` replaced with `str2`. Let's see an example - say I want to copy a file named `file1.py` to the current directory:

```bash
$ cp ~/foo/bar/fle1.py .
```
Oops, I misspelled `file1.py` and wrote `fle1.py`. I can easily rerun the command with the correct file name using quick substitution:

```bash
$ ^fle^file^
```

Pressing enter prints the corrected command to the standard output and runs it as intended! You should note that the only the first matching string is replaced. There are ways to perform global replace, but the syntax is quite arcane and easy to forget. However, there is an alternative, which brings us to the next builtin.

*By the way, the quote above is taken from the [`man` pages][man-event-designators]. You should look it up (it is generally a good idea to read `man` once in while).*

## fc: memorable substitution and much more
### Substitution
Another builtin way to perform quick substitution is with the [`fc` command][fc-command] (the name can be thought of as a mnemonic for 'fix command').

To perform the same substitution from the previous example, run:

```bash
$ fc -s fle=file
```

This can be easily aliased to become even more comfortable:

```bash
$ alias r="fc -s"
$ r fle=file
```

I find this much more memorable than the `^str1^str2^` syntax.
Do note that this command performs a **global** substitution, so you have to be more careful about it.

### Editing previous command
Actually `fc` can do much more. Sometimes a simple substitution is not enough to fix the command. For example, when running a long `curl` command with payload, and you need to change some of the payload's values. 

Arrowing up and editing in the command line feels really inefficient, especially if you are a vim user. Yeah, I could copy the command to vim, make the changes, copy it again to the command line and run it. But that's an awful lot of steps for such a simple task. Well, this paragraph does not reside under the `fc` title for nothing! Try running the following:
```bash
$ fc
```

Yes, it does exactly what we needed it to (or maybe it opened nano, but we can fix that if needed)! Running `fc` with no flags or arguments, opens up the previous command to edit in a predefined editor. Edit, save and exit and the fixed command will be run!

### Changing the editor
For one time (or always by aliasing and saving in `.bashrc`):
```bash
$ fc -e ename
```
Where `ename` is the name of the desired editor.
Another option is to define the environment variable FCEDIT with the desired editor. For vim that would be:
```bash
$ export FCEDIT=vim
```

### Aborting the fix and avoiding unwanted re-reruns
You will notice that even if you don't save the changes and quit the editor (`:q!` in vim), the unedited command will be executed, which is probably not what you want.
To avoid that, the editor must return a non-zero exit code. You can do this in vim by exiting with `:cq` (or `:cq!` if you made changes).

## Conclusion
These are just very few ways to fix previously executed commands in bash. There are of course others (such as the more known event designator `!`).

What are your favorites? Please write them in the comments!

[man-event-designators]: https://www.gnu.org/software/bash/manual/html_node/Event-Designators.html
[fc-command]: [https://www.gnu.org/software/bash/manual/html_node/Bash-History-Builtins.html]