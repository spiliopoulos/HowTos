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


##User that executes command

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
