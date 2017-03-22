# Overview

This interface layer handles the communication among Apache HBase peers.


# Usage

## Peers

This interface allows the peers of the HBase deployment to be aware of
each other. This interface layer will set the following states, as appropriate:

  * `{relation_name}.joined` A new peer in the HBase application has
  joined. The HBase charm should call `get_nodes()` to get
  a list of tuples with unit ids and IP addresses for quorum members.

    * When a unit joins the set of peers, the interface ensures there
    is no `{relation_name}.departed` state set in the conversation.

    * A call to `dismiss_joined()` will remove the `joined` state in the
    peer conversation so this charm can react to subsequent peers joining.


  * `{relation_name}.departed` A peer in the HBase application has
  departed. The HBase charm should call `get_nodes()` to get
  a list of tuples with unit ids and IP addresses for remaining quorum members.

    * When a unit leaves the set of peers, the interface ensures there
    is no `{relation_name}.joined` state set in the conversation.

    * A call to `dismiss_departed()` will remove the `departed` state in the
    peer conversation so this charm can react to subsequent peers departing.


For example, let's say that a peer is added to the HBase application
deployment. The HBase charm should handle the new peer like this:

```python
@when('hbase.installed', 'peer.joined')
def quorum_add(peer):
    nodes = peer.get_nodes()
    increase_quorum(nodes)
    peer.dismiss_joined()
```

Similarly, when a peer departs:

```python
@when('hbase.installed', 'peer.departed')
def quorum_remove(peer):
    nodes = peer.get_nodes()
    decrease_quorum(nodes)
    peer.dismiss_departed()
```


# Contact Information

- <bigdata@lists.ubuntu.com>
