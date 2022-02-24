# LFCE User and Group Management

1. [Connect to an external authentication source](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_UserandGroupManagement.md#connect-to-an-external-authentication-source)

2. [Configure advanced PAM](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_UserandGroupManagement.md#configure-advanced-PAM)


## Connect to an external authentication source

## Configure advanced PAM

- Check PAM info in LFCS repo: [here](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/UserandGroupManagement.md#configure-pam)

- **Example -> How to Restrict root Access to SSH Service Via PAM:**

    ```bash
    $ sudo vim /etc/pam.d/sshd
    ```
    Add this rule:.
    ```bash
    auth required pam_listfile.so onerr=succeed item=user sense=deny file=/etc/ssh/deniedusers
    ```
    - **auth** -> is the module type (or context).
    - **required** -> is a control-flag that means if the module is used, it must pass or the overall result will be fail, regardless of the status of  other modules.
    - **pam_listfile.so** -> is a module which provides a way to deny or allow services based on an arbitrary file.
    - **onerr**=succeed -> module argument.
    - **item**=user -> module argument which specifies what is listed in the file and should be checked for.
    - **sense**=deny -> module argument which specifies action to take if found in file, if the item is NOT found in the file, then the opposite action is requested.
    -  **file**=/etc/ssh/deniedusers -> module argument which specifies file containing one item per line.

    Next, we need to create the file **/etc/ssh/deniedusers** and add the name **root** in it:

    ```bash
    $ sudo vim /etc/ssh/deniedusers
    ```

    Save the changes and close the file, then set the required permissions on it:

    ```bash
    $ sudo chmod 600 /etc/ssh/deniedusers
    ```

    - Do the oposite allow users to connect:
        ```bash
        auth required pam_listfile.so item=user sense=allow file=/etc/sshd/sshd.allow onerr=fail
        ```

- **Example -> Configure PAM SSH so that if user tries to login 5 time, it gets refused!**

    - Something similar can be done using `/etc/security/limits.conf` configuring *maxlogin* for user.
    - Add the following to */etc/pam.d/password-auth*:
        ```bash
        # This will lock every account after 5 attempt.
        # Add following line at beginning of the ‘auth‘ section.
        auth     required       pam_tally2.so deny=5 even_deny_root unlock_time=120

        # add the following line to ‘account‘ section.
        account     required      pam_tally2.so
        ```
        - `pam_tally2 -u sten` -> To check and reset the counter.

        TBD

- **How to Configuring Advanced PAM in Linux:**
    - To write more complex PAM rules, you can use valid control-flags in the following form:

        ```bash
        type [value1=action1 value2=action2 …] module module-arguments
        ```

        The actionN can take one of the following forms:
        - **ignore** -> if this action is used with a stack of modules, the module’s return status will not contribute to the return code the application obtains.
        - **bad** -> indicates that the return code should be thought of as indicative of the module failing. If this module is the first in the stack to fail, its status value will be used for that of the whole stack.
        - **die** -> equivalent to bad but may terminate the module stack and PAM immediately returning to the application.
        - **ok** -> this instructs PAM that the system administrator thinks this return code should contribute directly to the return code of the full stack of modules.
        - **done** -> equivalent to ok but may terminate the module stack and PAM immediately returning to the application.
        - **N** (an unsigned integer) -> equivalent to ok but may jump over the next N modules in the stack.
        - **Reset** -> this action clears all memory of the state of the module stack and restart with the next stacked module.

        Each of the four keywords: required; requisite; sufficient; and optional, have an equivalent expression in terms of the [...] syntax, which allow you to write more complicated rules and they are:

        - required: [success=ok new_authtok_reqd=ok ignore=ignore default=bad]
        - requisite: [success=ok new_authtok_reqd=ok ignore=ignore default=die]
        - sufficient: [success=done new_authtok_reqd=done default=ignore]
        - optional: [success=ok new_authtok_reqd=ok default=ignore]

        One example:
        ```bash
        #%PAM-1.0
        # This file is auto-generated.
        # User changes will be destroyed the next time authconfig is run.
        session [success=1 default=ignore] pam_succeed_if.so service !~ gdm* service !~ su* quiet
        session [default=1] pam_lastlog.so nowtmp showfailed
        session optional pam_lastlog.so silent noupdate showfailed
        ```

    - **A lot of above info can be seen with:** `man pam.d`

- More info on PAM: https://documentation.suse.com/zh-cn/sles/12-SP5/html/SLES-all/cha-pam.html

[Check everything from LFCS essentials KB](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/UserandGroupManagement.md)

[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_UserandGroupManagement.md#connect-to-an-external-authentication-source)