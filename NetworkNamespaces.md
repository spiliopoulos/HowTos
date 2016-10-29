#A collection of tips for network namespaces in linux

##Setup

```
# Create namespace
ip netns add {NAME_OF_NEW_NAMESPACE}
# Add a preexisting netowrk interface to the namespace
ip link set {NAME_OF_INTERFACE_TO_ADD_TO_NS} netns {NAMESPACE}
# Activate the network interface. You have to do this irregardless of if it was
# already activated before adding it to the namespace
ip netns exec {NAMESPACE_TO_EXECUTE_INTO} ip link set {INTERFACE} up
# Execute whatever you want to do in the namespace
ip netns exec {NAMESPACE_TO_EXECUTE_INTO} {WHATEVER; SEQUENCE; OF; COMMANDS; YOU; WANT}
```


##Executing commands not as root

ip-netns requires sudo to run. As a result all commands executed
with
```
ip netns exec
```
are executed under root.

To work around that we can do
```
ip netns exec {NAMESPACE} sudo -u {USER_TO_EXECUTE_UNDER} zsh

```
to get a shell executed in the namespace. Anything executed in that shell
executes in the namespace under the selected user's account

##Virtualenv

Python's virtualenv can't be sourced as root/sudo as a result we need to
follow the technique covered in the previous section


##Accessing X-apps that run in a network namespace from another namespace

If you need to remotely view the x-apps that are launched from a namespace
different than the one you ssh'ed into, ssh x-forwarding will not work.

You could try using *socat* like:
```
socat exec:'ssh {REMOTE_HOST} rm -rf /tmp/.X11-unix/X1; socat unix-l\:/tmp/.X11-unix/X1\,fork -' unix:/tmp/.X11-unix/X0
```

However it's not very consistent and for some reason it's not working with apps
that need to use dbus

You should use [Xpra](http://xpra.org)

In the remote server and outside of any namespace run:
```
xpra start :{DISPLAY_NUMBER}
```

In the local server attach to the remote xpra session by running:
```
xpra attach ssh:{REMOTE_HOST}:{DISPLAY_NUMBER}
```

Run apps in the namespace by:
```
DISPLAY=:{DISPLAY_NUMBER} app_command
```

or by first exporting the DISPLAY
```
export DISPLAY=:{DISPLAY_NUMBER}
```


