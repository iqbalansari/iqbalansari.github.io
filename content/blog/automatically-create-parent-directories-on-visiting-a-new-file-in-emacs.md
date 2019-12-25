---
title: "Automatically create parent directories on visiting a new file in Emacs"
author: ["Iqbal"]
date: 2014-12-07T12:59:00+05:30
categories: ["emacs"]
draft: false
url: "/blog//2014/12/07/automatically-create-parent-directories-on-visiting-a-new-file-in-emacs/"
---

In emacs we can open a file in a non-existent directory and then use the
command `make-directory` to create the parent directory (or directories) if
they do not exist. While this is really useful, I find it a bit cumbersome to
press <kbd>`M-x`</kbd> `make-directory` <kbd>`RET`</kbd> <kbd>`RET`</kbd> every time
I visit such a file. I was looking for ways to automate this when I came
across the variable `find-file-not-found-functions`. According to the
documentation (<kbd>`C-h`</kbd> <kbd>`v`</kbd> `find-file-not-found-functions`
<kbd>`RET`</kbd>)

> List of functions to be called for \`find-file' on nonexistent file.
> These functions are called as soon as the error is detected.
> Variable 'buffer-file-name' is already set up.
> The functions are called in the order given until one of them returns non-nil.

I wrote a simple function which checks whether the parent directories exist
for a given file and offers to create them if they do not exist.

```scheme
(defun my-create-non-existent-directory ()
  (let ((parent-directory (file-name-directory buffer-file-name)))
    (when (and (not (file-exists-p parent-directory))
               (y-or-n-p (format "Directory `%s' does not exist! Create it?" parent-directory)))
      (make-directory parent-directory t))))
```

I added this function to `find-file-not-found-functions`

```scheme
(add-to-list 'find-file-not-found-functions #'my-create-non-existent-directory)
```

So now whenever I visit a file a non existent directory emacs offers to create the parent directories.
