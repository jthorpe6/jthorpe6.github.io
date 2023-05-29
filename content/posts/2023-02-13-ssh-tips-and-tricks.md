---
layout: post
title:  "SSH Tips and Tricks"
date:   2023-02-13 22:00:00 +0000
---

Throughout my career I've always been the one that knew more that your typical `ssh` command. Colleges would often ping me if they ever needed to do anything "advanced" with `ssh`. Recently I found myself in the depths of `man ssh` and scripting my fair share of `ssh` shell scripts. So I thought I'd dump some of that knowledge in a post.

## The Config File

If you ever find yourself making an alias like `alias foo=ssh you@192.168.1.1 -o StrictHostKeyChecking=no` then your doing it wrong. `ssh` allows you to predefine settings on a per host basis in the `~/.ssh/config` file. For example, the alias before can be defined as the following:

```config
Host foo
    HostName 192.168.1.1
    User you
    StrictHostKeyChecking no
```

With that configured, you can now just type `ssh foo` and you should be `ssh`ing with the settings above.

## Running a Remote Command

What if you just want to run one command on the remote host? Well you can actually pass the remote command to the end of the `ssh` command, for example, the command `ssh you@192.168.1.1 id` will `ssh` to the host at `192.168.1.1` and after successful authentication, execute the `id` command. If the remote command requires arguments, I've found that the `-t` option works best for this.

This is also useful when you only want the output of say a remote log file, and you don't need a full shell on the remote host.

## Forking

Have you ever wanted to have all the benefits of `ssh` but without a shell ? Suppose you just want to access a remote service (more on that next). Or how about you want to tunnel a service through to another host and don't need the shell on the proxy. Well you can do this with the `-f` option. However, your'll likely need the `-N` option also. The `-f` option requests ssh to go to background just before command execution, and the `-N` option says to not execute a remote command.

For example, the following command can be used to tunnel port `2222` over localhost's port `22`, meaning after this command you can now run `ssh -p 2222 username@localhost` to `ssh` to a service behind a different host. You wont need the initial shell still open, and if you fork it, then its less prone to you accidentally closing it.

```bash
ssh -Nf -R2222:localhost:22 username@ip
```

The only caveat with forking a `ssh` session, is if you want to close it, you have to kill the process. Thats not overly annoying, but its worth mentioning.

## Port Forwarding

Sometimes access to a service on a remote system is only available to the localhost of the system. But this should not stop you accessing it from your host.

`ssh` supports a local and remote port forward, `ssh` actually allows you to proxy through systems to access remote services. In the following example the remote system only allows access to the database from localhost but you can connect via ssh and then connect to the database from your workstation.

```bash
ssh -L 3307:remotehost:3306 you@remotehost
```

The -L option tells ssh to forward the local port 3307 to the remote servers port 3307 allowing you to now connect from your local system.

```bash
mysql -u user -p -P 3307 -h 127.0.0.1
```

## Proxy Jump

What if your service is located behind a jump host? You could use the above trick, but that could become a bit hard to manage. Welcome the `-J` option. The `-J` option allows you to connect to services via a jump host. For example, the command `ssh you@192.168.1.1 -J you@jumphost:1022`, will first ssh to `you@jumphost` on port `1022` and then from that host it'll `ssh` to `you@192.168.1.1`.

You can also do this with `scp` too, with the `-o ProxyJump=you@jumphost:10222` option.

## SSH_ORIGINAL_COMMAND

The `SSH_ORIGINAL_COMMAND` is a variable that contains the command that is executed. Meaning, if the client ran `ssh you@host -t id` then the `SSH_ORIGINAL_COMMAND` variable on the remote host will contain `id` as well as a few other defaults. This variable is particularly useful to verify that the client isn't trying to do anything they should not be doing. For example, the following `bash` script, checks that the `SSH_ORIGINAL_COMMAND` matches against the `REGEX` variable, and if it does, its allowed to continue.

```bash
# regex to check the incoming SSH command
# assumes an ssh-ed25519 key
# checks for the 'echo' command
# checks for the authorized_keys file options 'command="exit",restrict,port-forwarding'
# checks for an ssh-ed25519 key with a comment format of $portnum_$hostname_$day-$monthabbrev-$year_$hour:$minute
REGEX="^echo command=\"exit\",restrict,port-forwarding ssh-ed25519 [a-zA-Z0-9\+\/]{68} [0-9]{5}_[0-9a-zA-Z\_\-]{1,15}_[0-9][0-9]-[ADFJMNOS][aceopu][bcglnprtvy]-[0-9][0-9][0-9][0-9]_[0-9][0-9]:[0-9][0-9]$"

# check the original command against the regex
if [[ $SSH_ORIGINAL_COMMAND =~ $REGEX ]]
then
    # the echo command matches the regex, add the key and options to the authorized_keys file
    $SSH_ORIGINAL_COMMAND | tee -a ~/.ssh/authorized_keys
fi
```

## The Client Menu

Ever ran `cat` on a binary file? Or your session has timed out and you did not use a `mosh` session, so the shell is just "hanging"? Or did you forget to setup that port forward? Well `ssh` has a client menu, whenever your in a `ssh` session hit return (to clear the buffer) and then `~?` to bring up the ssh client menu.

```
​ me@remotehost:~$
 me@remotehost:~$ ~?
Supported escape sequences:
 ~.   - terminate connection (and any multiplexed sessions)
 ~B   - send a BREAK to the remote system
 ~C   - open a command line
 ~R   - request rekey
 ~V/v - decrease/increase verbosity (LogLevel)
 ~^Z  - suspend ssh
 ~#   - list forwarded connections
 ~&   - background ssh (when waiting for connections to terminate)
 ~?   - this message
 ~~   - send the escape character by typing it twice
 (Note that escapes are only recognized immediately after newline).
```

Now when your session has timed out or you’ve accidentally ran cat on a binary, you can hit return then `~.` to terminate that ssh connection. Or if you’ve forgotten to open up a port forward you can hit return then `~C` and add the relevant options to setup your port forward.

## Some Examples

### Connect Script

Recently I needed to tunnel `ssh` from a VM back to myself, so I used the following `bash` script to first test that the VM could connect to me on port `22` and if it could, to then `ssh` to me, and tunnel port `2222` to its port `22`. Meaning that if this script was successful, I'd be able to run the command `ssh -p 2222 user@localhost` and be presented a shell on the VM.

```bash
#!/bin/bash

HOSTIP=$1

if [ -z $HOSTIP ]; then
    echo -e "\$1 not set"
    exit
fi

timeout 5 bash -c "</dev/tcp/$HOSTIP/22"

if [ $? -eq 0 ]; then
    ssh -fN -R 2222:localhost:22 me@$1
else
    echo -e "!!! cant connect to $HOSTIP on port 22 !!!"
fi
```

### Temporary Aliases

I work with VM's a lot, and often need to `ssh` to them. But I also delete them to save on my hard drive space. So sometimes VM's will be re-allocated the same IP from the DHCP server. This is fine from a networking point of view, but when I go to `ssh` to the VM, I'm greeted with an error stating that the host key conflicts. Or its already in the `~/.ssh/know_hosts` file. Its easy enough to fix, you remove the entry in the `know_hosts` file, and then attempt to `ssh` again. But but but, this could be so much nicer.

If I know that the VM is temporary, and wont be staying around, do I really need to save its host ksy in the `know_hosts` file ? Thats a question for the reader, but for me, I don't. So I have the following aliases set.

```bash
alias tssh="ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no"
alias tscp="scp -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no"
```
