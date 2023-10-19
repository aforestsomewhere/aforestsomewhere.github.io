## Mamma, my Bash is broken
This may be useful to HPC users troubleshooting a corrupted bash profile. Disclaimer that it will grossly oversimplify the computing concepts, and is collated from info from the holy trinity of StackExchange, ServerFault and the Raspberry Pi userbase. 
Depending on the specific local setup, you likely have ~/.bashrc and ~/.bash_profile files in your home folder. If you don't see them, it's probably because they are "hidden" files starting with a fullstop, but try to list it and it should be visible: 
```tsql
ls .bash_profile
 ```
When Bash is invoked as the login shell when you open an interactive remote session to connect to your institutes' HPC, it will look for the ~/.bash_profile firstly to confiugre your shell. Here's an example:
```tsql
# .bash_profile
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
# User specific environment and startup programs
PATH=$PATH:$HOME/.local/bin:$HOME/bin
 ```
As you can see, .bash_profile looks to another file, .bashrc, to find user aliases and functions, and also appends my local bin to PATH. This takes us to .bashrc:
```tsql
# .bashrc
# Source global definitions
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi
# User specific aliases and functions
export RUSTUP_HOME=/path/to/my/working/folder/Rust
 ```
"Source global definitions" basically configures my session to play nice within the wider HPC context.
The user specific aliases lets you define various functions and shortcuts automatically upon login.
.bash_profile seems preferable as a location for exporting environmental variables (e.g. making shortcuts instead of manually typing out long paths for your working directory) 
There's also scope for visualisation and function customisations here - if you have the time and will e.g. https://www.hpc.caltech.edu/documentation/faq/how-do-i-modify-my-shell-environment

But what happens when a good bash config setup, goes bad? Instead of seeing your username on the prompt line like this:
```tsql
[robert.smith@hpcure ~]$
 ```
you might see something like this
```tsql
-bash-4.1$
 ```

*Story time* I had something of a perfect storm last weekend, precipitated by and independent random HPC administrative event that required the permissions and profiles to be reset for all users, fine. But as my home folder was full, my profiles could not be reset (even the modification of existing files seems to be restricted if the disk usage is exceeded). 
So I saw the bash-versioning on my prompt, and also noticed all my self-care shortcuts were kaput (sob).

But, NBD, as they say, I sat down and deleted a lot of redundant PyPi wheels (note to self - setup a cleanup function to do this automatically in the near future).

I am not 100% certain on the order and signficance of what happened next, but basically after manually restoring my .bash_profile and .bashrc files to backups and restarting my session, the shell got stuck in a nice little login loop, where it was not even giving me the bash prompt. FUN.
Thankfully, someone else had been in this predicament and, much like the paintings in the Cave of Altamira, there was a record of their existence and action - only this time, through the medium of StackExchange intercourse: https://serverfault.com/questions/94503/login-without-running-bash-profile-or-bashrc

As a user without sudo privileges, and with access to user home folders restricted to the owners, many options were out.
In any case, the main point of this post is to preserve this background knowledge and ultimate fix, which was as simple as a good, old-fashioned, "Ctrl+C".
How, you might ask? *If*, after opening a new session, you can be quick enough to interrupt the process of finding and reading the faulty .bashrc, you will be logged into your cluster.
You may see strange behaviour e.g. nothing at all on the prompt line, but try to navigate to your home folder and you should be able to restore the .bashrc and .bash_profile files.
Another elegant option (not my invention, see the above-linked serverfault.com thread) is to pipe Ctrl+C directly before trying to open the ssh, e.g.
```tsql
{ echo ^C; cat /dev/tty; } | ssh -tt user@host
 ```
Bash environment corruption is up there with top unexpected-and-pointless frustrations, especially where you do not have admin privileges, so hopefully this will be useful to someone.

Extra resources from ze community:

https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/

https://unix.stackexchange.com/questions/88106/why-doesnt-my-bash-profile-work

http://research.libd.org/rstatsclub/post/edit-your-bashrc-file-for-a-nicer-terminal-experience/

