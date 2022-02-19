# LFCE Essential Commands - 5%

<img src="https://www.svgrepo.com/show/107972/lazy.svg" width="250" align="right"/></a>

1. [Use version control tools](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_EssentialCommands.md#use-version-control-tools)
2. [Manipulate file content programmatically](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_EssentialCommands.md#manipulate-file-content-programmatically)
3. [Run commands on many systems simultaneously](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_EssentialCommands.md#run-commands-on-many-systems-simultaneously)
4. [Install Linux Distribution](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_EssentialCommands.md#install-linux-distribution)


## Use version control tools

- Following some common git commands:
    ```bash
    git config --global user.name Ivan Grozni
    git config --global user.email something.gmail.com
    git config --list
    git init --bare /dir/bashscripts
    git status
    git add -A
    git commit -a -m "Initial Commit"
    git push origin master
    git status
    git checkout -b latest
    git branch
    git add delme
    git commit -a -m 'add new file'
    git checkout master
    git merge test
    git branch -d test
    git pull origin
    git log
    git show
    git diff master latest
    ```

    `git clone ssh://osboxes@192.168.0.108/tmp/test ./opala/` -> Clone repo from remote machine using ssh.
    - Had to set `git config receive.denyCurrentBranch ignore` from the repo side and change group and user owner != root.

## Manipulate file content programmatically

- Use the following command to do it: `sed, uniq, grep, tr, cut`

    `cut --complement -d " " -f2,3 original > new` -> Delete 2nd and 3 column from file.

    `cat file | sort | uniq -d` -> Show only duplicate lines.

    `uniq -u` -> Shows only unique lines.

    Check them into the [LFCS repo.](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/EssentialCommands.md)

## Run commands on many systems simultaneously

- If you are managing multiple Linux servers, and you want to run multiple commands on all the
Linux servers in parallel fear not. To achieve, this you can use the ***pssh*** (parallel ssh) program, a command line utility for executing
ssh in parallel on a number of hosts.

- How to install it:  `yum install pssh`

- How to use it:
    - Create a file with random name *pssh-hosts* containig the IPs of the host you want to connect to with the following syntax:
        ```bash
        # cat pssh-hosts
        192.168.0.101:22
        ```
    - Start using it:
        ```bash
        pssh -h pssh-hosts -i -o dir_name -l root -A df -h
        ```
        -h: used to specify host file

        -i: used to display standard output and standard error

        -o: to save standard output to a given directory

        -l: to include a default username on all hosts that do not define a specific user 

        -A: to ask for a password and send to ssh

## Install Linux Distribution

- If you are studying for **LFCE** you need to know this already!

[Check everything from LFCS essentials](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/EssentialCommands.md)

[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_EssentialCommands.md#lfce-essential-commands---5)
