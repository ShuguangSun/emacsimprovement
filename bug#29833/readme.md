What is the issue?
1. The function completion--flush-all-sorted-completions defined in minibuffer.el is always called without parameters which make it flush the cached table every time.
2. The function completion--flush-all-sorted-completions is added to the hook of after-change-functions, however, (jit-lock-after-change t) in the hook will trigger flush every time
3. The local cached table completion-all-sorted-completions is not used actually. The minibuffer-completion-table is called in a lot of function which will rebuild the table instead of the chached table.

It has small impact on the performance of
1. operation in memory, e.g., describe-variable, describe-function
2. filename in local driver/PC
3. filename in tramp where it works on the ls output instead of trying to re-do the ls command on the server
Those operations are fast and time for fuctions symbolp and functionp is ignorable.

However, if it works under some slow instance, for example, a mapped driver in Window with slow net work, the performance is bad as the completion tries to list the directory in each call of completion--do-completion and icomplete-completions, and some other completion functions.


The patches try to use the cached table completion-all-sorted-completions-table as much as possible, and for filename completion, it caches the list of files in the base directory (file-name-directory).
