Let’s attempt to use SSH port forwarding to tunnel a remote Docker host to out local machine, granting us access to resources as if they are running locally.

First tunnel remote socket to a port on local machine.
```sh
ssh -nNT -L localhost:2345:/var/run/docker.sock user@server
```
Flag **n** prevents reading from stdin, **N** does not execute any command and **T** will not allocate a PTY.

**Next open a new terminal and export DOCKER_HOST.**

```sh
export DOCKER_HOST="localhost:2345"
```
While using this terminal, local docker commands are executed on remote server.

## Removing the Socket Forwarding
You can see the current processes related to ssh by e.g.:
```sh
ps aux | grep ssh
```
To stop the process related to your socket forwarding you can e.g. grep for its path (and ignore the grep itself: grep -v grep), filter its id and then kill it:
```sh
kill $(ps aux | grep /path/to/local/<socket>:/var/run/docker.sock:/var/run/docker.sock | grep -v grep | awk '{print $2}')
```
After removing the the socket you need to clear, "remove" the environment variable DOCKER_HOST:
```sh
unset DOCKER_HOST
```
In contrast to the automatic file creation on setup you now need to manually remove the local socket file:
```sh
unlink /path/to/local/<socket>
```
And again depending on your environment remove the <user> from group docker, check the result and exit:
```sh
ssh -i /path/to/<key_file> -t <user>@<host> "sudo deluser <user> docker; groups <user>;exit;
```

#####_alternative example link_:
- [https://medium.com/@dperny/forwarding-the-docker-socket-over-ssh-e6567cfab160]

