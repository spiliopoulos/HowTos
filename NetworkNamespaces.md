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


##Virtualenv

It seems that namespaces don't play well with python's virtualenv/path.
In order to use a virtualenv inside a namespace we have to activate it
in the namespace before executing anything in python.

```
ip netns exec {NAMESPACE} source venv/bin/activate; which ipython
```

It might produce an error like

```
exec of "source" failed: No such file or directory
```

but it should be working
