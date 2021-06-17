Error and problem list
==============================================================================================
This is a documentation about the integration of model predictive control framework in Panda

.. toctree::
   :maxdepth: 2
   :glob:

Useful links
-------------
* Sphinx Style guide link_.

   .. _link:

   https://alyvix.com/learn/stylesheet.html
* `MarkDown Syntax
  <https://about.gitlab.com/handbook/markdown-guide/>`_.
* `Sphinx Syntax
  <https://sublime-and-sphinx-guide.readthedocs.io/en/latest/lists.html>`_.

Github Related issus
---------------------
.. code-block:: shell

   [rejected] master -> master (fetch first) error: failed to push some refs to

The problem is due to a modification in Github remote, but we didn't fetch it in the local file.
<https://www.cnblogs.com/bigtreei/p/10180383.html>

<https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners>

libfranka error:
---------------------
* Move command rejected: Or verify is the ethernet cable is fast enough

  .. code-block:: shell

      rostopic pub -1 /franka_control/error_recovery/goal franka_msgsErrorRecoveryActionGoal {}

* Unable to schedule real-time task:

  .. code-block:: shell

     sudo usermod -a -G realtime $(whoami)
