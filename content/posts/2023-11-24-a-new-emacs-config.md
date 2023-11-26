+++
title = "A New Emacs Config"
author = ["Joe Thorpe"]
date = 2023-11-24
draft = false
+++

Its been a while since I last posted here, I've like most people been busy with work and life in general, and that's let a few things slide. One of those was my `emacs` configuration. Being put on bed rest for a few weeks while I recover from an operation, I thought I'd go through my configuration and see if I can tidy it up a bit, and maybe try out a few different packages.


## Theme {#theme}

First of all I wanted to update my theme. It was time for a change. After a little while I settled on the [catppuccin theme](https://catppuccin-website.vercel.app/ports/emacs) specifically the [mocha](https://github.com/catppuccin/emacs/blob/main/assets/Mocha.webp) variation. This then lead me down a bit of a rabbit hole changing all the applications on my system to use this theme. I updated my [shellenv](https://github.com/jthorpe6/shellenv) repo with the updated setting.


### org-modern {#org-modern}

In addition to the main `emacs` theme, I remember some time a go coming across the [org-modern](https://github.com/minad/org-modern) package, so I enabled that. I had to turn of `org-indent-mode` to get pretty code blocks, but I was happy to remove that.


### doom-modeline {#doom-modeline}

Funny enough I never really bothered with a modeline, or if I did, I'd just slightly modify it. Recently I did try [powerline](https://github.com/milkypostman/powerline) but did not get along with it. Seeing as I was playing with a new configuration, I decided to install [doom-modeline](https://github.com/seagle0128/doom-modeline), and I love it. I did have to disable a few settings, like the clock and the encoding indicator.


### Fonts {#fonts}

Any good theme is not complete without a good font, and after watching the [Emacs From Scratch](https://www.youtube.com/watch?v=74zOY-vgkyw&list=PLEoMzSkcN8oPH1au7H6B7bBJ4ZO7BXjSZ) series, I wanted to try out setting a different font for `code` and writing. Emacs calls these `fixed-pitch` and `variable-pitch` fonts. However, due to the way I run `emacs` I had to set these in a `org-mode` hook like so.

```emacs-lisp
(defun jt/org-mode-fonts()
  (set-face-attribute 'org-document-title nil :font "Iosevka Curly" :weight 'bold :height 1.3)

  (dolist (face '((org-level-1 . 1.2)
                  (org-level-2 . 1.1)
                  (org-level-3 . 1.05)
                  (org-level-4 . 1.0)
                  (org-level-5 . 1.1)
                  (org-level-6 . 1.1)
                  (org-level-7 . 1.1)
                  (org-level-8 . 1.1)))
    (set-face-attribute (car face) nil :font "Iosevka Curly" :weight 'medium :height (cdr face)))

  ;; ;; Ensure that anything that should be fixed-pitch in Org files appears that way
  (set-face-attribute 'org-block nil :inherit 'fixed-pitch)
  (set-face-attribute 'org-formula nil  :inherit 'fixed-pitch)
  (set-face-attribute 'org-code nil   :inherit 'fixed-pitch))

(defun jt/org-mode-setup()
  (jt/org-mode-fonts)
  (variable-pitch-mode 1)
  (auto-fill-mode 0)
  (visual-line-mode 1)
  (diminish org-indent-mode))

(use-package org
  :pin gnu
  :hook (org-mode . jt/org-mode-setup)
;;SNIP;;
```

Now when I open an `org-mode` document, the body and titles font change, yet the `code` blocks font stays the same.


### Putting it all together {#putting-it-all-together}

Putting all the theme configuration together, I've got something like this.

{{< figure src="/images/20231124-theme.png" >}}


## Org-mode {#org-mode}

My `org-mode` configuration was very unhealthy, there was a lot of things in there that I just did not use anymore, my agenda was a mess, and if anything it made me not really use `org-mode` much anymore. I wanted this to change, `org-mode` is amazing, and will always be better than `markdown`.


### Denote {#denote}

I'd heard of [denote](https://protesilaos.com/emacs/denote) but had never really bought into a note taking management system, I would always just have a folder of folders and then `grep` if I needed to search them. I'd tried out obsidian but found that that was taking over my life, and I already have `emacs` doing that. Denote for me is the obsidian for `org-mode`, its just simple, and does not try to take over my workflow. I'm also going to try out journaling and see how that goes for "brain dumping" each day. For journals, I wanted it to be easy, I don't want to have to go through all the windows denote has by default. Denote does come with `org-capture` integration, and does allow for journals. However I had to _hack_ around with the journal capture template, like so.

```emacs-lisp
(defun jt/denote-journal ()
  "Create an entry tagged 'journal' with the date as its title."
  (interactive)
  (denote
   (format-time-string "%A %e %B %Y")
   '("journal")
   nil
   "~/org/journal/"))

(with-eval-after-load 'org-capture
  (add-to-list 'org-capture-templates
               '("j" "Journal entry" plain (nil)
                 #'jt/denote-journal
                 :immediate-finish t
                 :kill-buffer t
                 :jump-to-captured t)))

```

I now don't get prompted for the "title" or "tag" for journal entries, it just does it for me. I also have another capture template just for a new note with denote, but that example is in the documentation so I wont show it here.

Accompanied with the [consult-notes](https://github.com/mclear-tools/consult-notes) package, I can quickly search over my notes, by tags / titles.


### Org-apple-notes {#org-apple-notes}

As an Apple fanboy, I'm very much in the apple "walled garden", yet I'd prefer to be in the "`emacs` walled garden" more. So I went about seeing if there was a package to _sync_ or _send_ notes to Apple Notes. Unfortunately there's not a package on `melpa`, yet I did come across [org-apple-notes.el](https://github.com/gcv/org-apple-notes/tree/master) and for the most part does send `org-mode` notes over to Apple Notes. There's a few style changes I have to do by hand, but for the odd occasion I send notes to Apple Notes, I'm happy with that.


## Nicer code editing {#nicer-code-editing}

I really wanted to try out `tree-sitter` but I'm still too scared to give that a go, the same with `eglot`. So I decided to make my current code environment better. So i installed [color-identifiers-mode](https://github.com/ankurdave/color-identifiers-mode) to highlight identifiers, and I also installed [highlight-indent-guides](https://github.com/DarthFennec/highlight-indent-guides) to see where I am in indention.


## Nicer git {#nicer-git}

My `git` commits are very much a shot in the dark, and its something I do want to work on. I'd already installed [magit](https://magit.vc) but never took advantage of all of its features. I'm also one to commit `TODO`'s, `FIXME`'s and `HACK`'s into code (sorry, not sorry). But its something I should be aware of. Luckily I stumbled across the [magit-todos](https://github.com/alphapapa/magit-todos) package, and I can only think it was made for people like me. Now when I see the `magit` summary screen, I see all the `TODO`' I've left behind.

I also decided to learn more `magit` and what I think will save me the most is being able to commit _hunks_ of code, and treating it more like a code review than a mad dash to push it up to the remote.


## Other niceness {#other-niceness}

I decided to refactor my `init.el` so that the majority of my config is in the `(use-package emacs)` block. This allows me to keep the same flow with packages and with `emacs`, and while I was there I decided to change a few things.


### compile {#compile}

I'd often watch people in `emacs` and wonder "how on earth are they running that buffer in `emacs`". Turns out the `compile` command is not well, just used to compile the buffer. It can be used to run a command with a keybinding, namely the command to run the file your editing. So I set the to the `C-x C-r` command, and it will run the `compile` command. But what I did not understand was how to set that, because surely `bash ./foo.sh` is a different command to `python ./bar.py`. Turns out if you prefix that command with `C-u` it will prompt you for the command, and then when I press `C-x C-r` again it will take that.


### next-buffer previous-buffer {#next-buffer-previous-buffer}

The `emacs` tutorial says to try and avoid using the arrow keys, however the default for `next/previous-buffer` is to use the arrow keys. In an attempt to stop myself using the arrow keys I've defined the following keybindings.

```emacs-lisp
(global-set-key (kbd "C-x .") 'next-buffer)
(global-set-key (kbd "C-x ,") 'previous-buffer)
```

I'm not too sure if it will stick, but now I at least have the option.


### Make file executable after saving {#make-file-executable-after-saving}

I actually stumbled across this setting whilst trying to research a fix for a different `emacs` problem. What this hook does is to check if the buffer file has a shebang (e.g. `#!/usr/bin/env python`) in it and then it modifies its permissions, if necessary.

```emacs-lisp
(add-hook 'after-save-hook
        'executable-make-buffer-file-executable-if-script-p)
```


### eshell {#eshell}

When I first got into `emacs` I did dabble with the `eshell` but back then I was still learning `bash` and how to use the shell. Nowadays I know about the `async-shell-command` with `M-&` and I can get by with that mostly. So I decided to replace my `vterm` keybindings with `eshell`, and so far so good, I love the fact I can invoke `dired` right from the shell. The `tramp` support too is pretty nice. The [load-bash-alias](https://github.com/daviderestivo/load-bash-alias) package is also a must have, as I set a lot of aliases in my environment and its nice to carry those across with me into the `eshell`.


### pyenv {#pyenv}

I'm spending more and more time with `python` at work, so It was about time I learnt what [pyenv](https://github.com/pyenv/pyenv) was all about, and I love it. No more 9th level of python dependency hell, and I for sure wanted this in `emacs`, luckily there's a [pyenv `emacs` package](https://github.com/pythonic-emacs/pyenv-mode). So I enabled that, and am not looking back.


## Repo {#repo}

I'm certain that I've forgotten to mention something. So I'm going to link my configuration [here](https://github.com/jthorpe6/.emacs.d.git) as this post is already getting on the big side.
