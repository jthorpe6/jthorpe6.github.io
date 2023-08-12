+++
title = "Greping in Emacs"
author = ["Joe Thorpe"]
date = 2023-08-12
draft = false
+++

Recently I've discovered the wonderful life of code review, and although I tried VS code, it felt clunky, and hard to navigate. Don't get me wrong VS code has its place, and its a decent editor. But I felt more ate home with emacs.

Anyone doing code review will most likely tell you to up your `grep` game. Or maybe if you spoke to me about code review, I'd tell you that.

Grepping a code base in emacs is actually a rather pleasant experience. Let me show you.

![](/images/20230812-213538_screenshot.png)

Yeah sure, its not the best example, but it hopefully shows that a new interactive buffer opens with the results.

The code for this is

```emacs-lisp
(setq grep-command "rg --color=always --no-heading --line-number --smart-case --follow ")
(setq grep-find-command "rg --color=always --no-heading --line-number --smart-case --follow --context=5 ")
(setq grep-find-template
      "find <D> <X> -type f <F> -exec rg <C> --color=always --no-heading --line-number --smart-case --follow --context=5 <R> /dev/null \\{\\} +")
(global-set-key (kbd "C-c r") 'grep-find)
```

This is where I change the `grep-command` to use [rg](https://github.com/BurntSushi/ripgrep) with a few options to give context around the matched word. I then map a keyboard shortcut to `C-c r`. I've also installed the emacs `rg` plugin, but I don't find myself using it much. However, I have found myself using `counsel-rg` for quick searches.

![](/images/20230812-214644_screenshot.png)

It does not open up in a new buffer, it shows the results in the mini buffer, which means that I cant go back to the results. So I find myself using this for smaller searches.

Until next time.
