---
title: "Switching repositories with Magit"
author: ["Iqbal"]
date: 2014-02-22T21:49:00+05:30
categories: ["magit", "emacs"]
draft: false
url: "/blog/2014/02/22/switching-repositories-with-magit/"
---

[Magit](https://github.com/magit/magit) is an awesome git interface for emacs. The normal workflow with magit is
to do <kbd>M-x</kbd> `magit-status`, if the file you are editing is in a git
repository you will have magit UI opened allowing you interact with the git
repo. Otherwise it will ask you to enter the path to a git repository and
opens a magit UI for that repository.

It often happens that you are editing a file in a some git repository but want
to open the magit UI for some other git repository. You can of course open a
file from the other repository and do `magit-status`, but there should be a
better way to do so. And as with most things in Emacs, there **is** a better
way. If you call `magit-status` with a prefix argument, it will prompt you for
path to the git repository you want to switch to and open a magit UI associated
with that repository.

This is well and good but you still have to enter the path to repository, this
little improvement over opening a file and then calling `magit-status`. We can
improve the this further by using the variable `magit-repo-dirs`. Basically
you can set this variable to a list of directories that contain the git
directories and magit will search for git repositories in those directories
and offer them as choices when you do <kbd>C-u</kbd> <kbd>M-x</kbd>
`magit-status`.

Setting `magit-repo-dirs` definitely is huge improvement over the previous two
methods. As a bonus if you happen to use [projectile](http://github.com/bbatsov/projectile) like I do, you can also
use the fact that projectile remembers your past projects to set
`magit-repo-dirs` to all the git repositories that you have visited. I have
the following piece of elisp in my init file to do this.

```elisp
(eval-after-load "projectile"
  '(progn (setq magit-repo-dirs (mapcar (lambda (dir)
                                          (substring dir 0 -1))
                                        (remove-if-not (lambda (project)
                                                         (file-directory-p (concat project "/.git/")))
                                                       (projectile-relevant-known-projects)))

                magit-repo-dirs-depth 1)))
```

The code above filters non-git directories from the projects known to
`projectile` and sets them to magit-repo-dirs. It also sets magit-repo-dirs to 1
so that magit searches only the top level directories. Now whenever I do
<kbd>C-u</kbd> <kbd>M-x</kbd> `magit-status` it prompts me with the name of all
the git repositories on my PC, that I have visited atleast once. Finally if for
some reason you want to manually enter the path to git repository you can do
<kbd>C-u</kbd> <kbd>C-u</kbd> <kbd>M-x</kbd> `magit-status`.


## Update {#update}

As of Magit v2.1, the variable `magit-repo-dirs` has been renamed to
`magit-repo-directories` and `magit-repo-dirs-depth` has been renamed to
`magit-repository-directories-depth`, so the above would not work. Use the
following instead

```elisp
(eval-after-load "projectile"
  '(setq magit-repository-directories (mapcar #'directory-file-name
                                              (cl-remove-if-not (lambda (project)
                                                                  (file-directory-p (concat project "/.git/")))
                                                                (projectile-relevant-known-projects)))

         magit-repository-directories-depth 1))
```
