## This is a demo of TRIVIAL-SIGNAL library usage

You'll need a [Roswell](https://github.com/roswell/roswell), to run these examples as is or you can easily adapt them for running under plain SBCL
by replacing shebang with `#!/usr/bin/env sbcl --script` and ensuring that [trivial-signal](https://github.com/guicho271828/trivial-signal) library is loaded.

The problem with the library is that it shows in the readme, that a signal handler can
be set with a top-level form like `(setf (signal-handler :term) #'exit-on-signal)`, but some
users on the internet [complain](https://www.reddit.com/r/lisp/comments/x17jmr/what_am_i_doing_wrong_with_this_signal_handler/)
that this approach does not work for them. And it is correct, because trivial-signal makes
these handlers enabled only inside `TRIVIAL-SIGNAL:WITH-SIGNAL-HANDLER` or `TRIVIAL-SIGNAL:SIGNAL-HANDLER-BIND` macros
or during the `TRIVIAL-SIGNAL:CALL-SIGNAL-HANDLER-BIND` function call.

So, I've created a two simple demos to show how to use the library correctly.
Probably, I'll made a post for my [Lisp Project of the Day](https://40ants.com/lisp-project-of-the-day/)
blog out of this demo. Also, I'll contribute these examples to improve the library's documentation.


### Simple Handler

The first example is trivial and shows how to exit from a Common Lisp program when user hits `Ctrl-C` without
scaring him with a long backtrace:

```
% ./single-handler.ros
Now I'm going to sleep 15 seconds.
^C
Catched INT signal. Gracefully exiting from the program.
```

### Multiple Handlers

The second example shows:

* How to set handlers for multiple signals.
* How to suppress some signals handling while handling them.
  This could be useful to do hot reload or reload config files on `SIGUSR1` signal.

```
% time ./multiple-handlers.ros
Now I'm going to sleep 15 seconds.
Try to execute "kill -USR1 17433" or "kill -TERM 17433" in another console.

Catched TERM signal. Gracefully exiting from the program.
./multiple-handlers.ros  0.39s user 0.18s system 8% cpu 6.808 total
```

Now we'll check how our program processes `SIGUSR1` signal:

```
% time ./multiple-handlers.ros
Now I'm going to sleep 15 seconds.
Try to execute "kill -USR1 17522" or "kill -TERM 17522" in another console.

Catched USR1 signal. Doing hot code reload and returning back to work.

Catched USR1 signal. Doing hot code reload and returning back to work.
Done.
./multiple-handlers.ros  0.39s user 0.17s system 3% cpu 15.675 total
```
