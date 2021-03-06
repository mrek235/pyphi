Getting started
===============

This page provides a walkthrough of how to use PyPhi in an interactive Python
session.

.. tip::

    An `illustrated tutorial on how Φ is calculated
    <https://doi.org/10.1371/journal.pcbi.1006343.s001>`_ is available as a
    supplement to the `formal PyPhi paper
    <https://doi.org/10.1371/journal.pcbi.1006343>`_.

To explore the following examples, install `IPython
<https://ipython.org/install.html>`_ by running ``pip install ipython`` on the
command line. Then run it with the command ``ipython``.

Lines of code beginning with ``>>>`` and ``...`` can be pasted directly into
IPython.

----

Basic Usage
===========

Let's make a simple 3-node network and compute its |big_phi|.

To make a network, we need a TPM and (optionally) a connectivity matrix. The
TPM can be in more than one form; see the documentation for |Network|. Here
we'll use the 2-dimensional state-by-node form.

    >>> import pyphi
    >>> import numpy as np
    >>> tpm = np.array([
    ...     [0, 0, 0],
    ...     [0, 0, 1],
    ...     [1, 0, 1],
    ...     [1, 0, 0],
    ...     [1, 1, 0],
    ...     [1, 1, 1],
    ...     [1, 1, 1],
    ...     [1, 1, 0]
    ... ])

The connectivity matrix is a square matrix such that the |i,jth| entry is 1 if
there is a connection from node |i| to node |j|, and 0 otherwise.

    >>> cm = np.array([
    ...     [0, 0, 1],
    ...     [1, 0, 1],
    ...     [1, 1, 0]
    ... ])

We'll also make labels for the network nodes so that PyPhi's output is easier
to read.

    >>> labels = ('A', 'B', 'C')

Now we construct the network itself with the arguments we just created:

    >>> network = pyphi.Network(tpm, cm=cm, node_labels=labels)

The next step is to define a subsystem for which we want to evaluate |big_phi|.
To make a subsystem, we need the network that it belongs to, the state of that
network, and the indices of the subset of nodes which should be included.

The state should be an |n|-tuple, where |n| is the number of nodes in the
network, and where the |ith| element is the state of the |ith| node in the
network.

    >>> state = (1, 0, 0)

In this case, we want the |big_phi| of the entire network, so we simply include
every node in the network in our subsystem:

    >>> node_indices = (0, 1, 2)
    >>> subsystem = pyphi.Subsystem(network, state, node_indices)

.. tip::
   If you do not explicitly provide node indices to a |Subsystem| the system
   will, by default, cover the entire network. For example, the following is
   equivalent to the above definition of ``subsystem``:

    >>> subsystem = pyphi.Subsystem(network, state)

.. tip::
    Node labels can be used instead of indices when constructing a |Subsystem|:

        >>> pyphi.Subsystem(network, state, ('B', 'C'))
        Subsystem(B, C)

Now we use the |compute.phi()| function to compute the |big_phi| of our
subsystem:

    >>> pyphi.compute.phi(subsystem)
    2.3125

If we want to take a deeper look at the integrated-information-theoretic
properties of our network, we can access all the intermediate quantities and
structures that are calculated in the course of arriving at a final |big_phi|
value by using |compute.sia()|. This returns a nested object,
|SystemIrreducibilityAnalysis|, that contains data about the subsystem's
cause-effect structure, cause and effect repertoires, etc.

    >>> sia = pyphi.compute.sia(subsystem)

For instance, we can see that this network has 4 concepts:

    >>> len(sia.ces)
    4

See the documentation for |SystemIrreducibilityAnalysis| and |Concept| for more
information on these objects.

.. tip::
    The network and subsystem discussed here are returned by the
    :func:`pyphi.examples.basic_network` and
    :func:`pyphi.examples.basic_subsystem` functions.
