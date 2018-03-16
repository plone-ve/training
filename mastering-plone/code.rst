Using the code for the training
===============================

You can get the complete code for this training from `GitHub <https://github.com/collective/ploneconf.site>`_.

The code-package
----------------

The package

..  note::

    If you want to do it by hand do the following:

    .. code-block:: bash

        cd src
        git clone https://github.com/collective/ploneconf.site.git


Getting the code for a certain chapter
--------------------------------------

To use the code for a certain chapter you need to checkout the appropriate tag for the chapter.
The package will then contain the complete code for that chapter (excluding exercises).

If you want to add the code for the chapter yourself you have to checkout the tag for the previous chapter.


Here is a example:

..  code-block:: bash

    git checkout views_2

The names of the tags are the same as the URL of the chapter.
The tag for the chapter https://training.plone.org/5/mastering-plone/registry.html is ``registry``
and you can get it with :command:`git checkout registry`.


Moving from chapter to chapter
------------------------------

To change the code to the state of the next chapter checkout the tag for the next chapter:

..  code-block:: console

    git checkout views_3


If you made any changes to the code you have to get them out of the way first:

..  code-block:: console

    git stash

This will stash away your changes but not delete them. You can get them back later.
You should learn about the command :command:`git stash` before you try reapply stashed changes.

If you want to remove any changes you made locally you can delete them with this command:

..  code-block:: console

    git reset --hard HEAD





Telling Plone about ploneconf.site
----------------------------------

If you did not yet do this (it is covered in chapter :ref:`eggs1-label`) you will have to
modify :file:`buildout.cfg` to have Plone expect the egg :py:mod:`ploneconf.site` to be in :file:`src`.

.. code-block:: cfg
    :linenos:
    :emphasize-lines: 6, 12

    eggs =

    ...

    # our add-ons
        ploneconf.site
    #    starzel.votable_behavior

    ...

    [sources]
    ploneconf.site = git https://github.com/collective/ploneconf.site.git


Tags
----

These are the tags for which there is code:

==============================    ===============================
Chapter                           Tag-Name
==============================    ===============================
:doc:`about_mastering`
:doc:`intro`
:doc:`installation`
:doc:`case`
:doc:`features`
:doc:`anatomy`
:doc:`plone5`
:doc:`configuring_customizing`
:doc:`theming`
:doc:`extending`
:doc:`add-ons`
:doc:`dexterity`
:doc:`buildout_1`                 ``buildout_1``
:doc:`eggs1`                      ``eggs1``
:doc:`export_code`                ``export_code``
:doc:`views_1`                    ``views_1``
:doc:`zpt`                        ``zpt``
:doc:`zpt_2`                      ``zpt_2``
:doc:`views_2`                    ``views_2``
:doc:`views_3`                    ``views_3``
:doc:`testing`                    ``testing``
:doc:`behaviors_1`                ``behaviors_1``
:doc:`viewlets_1`                 ``viewlets_1``
:doc:`api`
:doc:`ide`
:doc:`dexterity_2`                ``dexterity_2``
:doc:`custom_search`
:doc:`events`                     ``events``
:doc:`user_generated_content`     ``user_generated_content``
:doc:`resources`                  ``resources``
:doc:`thirdparty_behaviors`       ``thirdparty_behaviors``
:doc:`dexterity_3`                ``dexterity_3``
:doc:`relations`                  ``relations``
:doc:`registry`                   ``registry``
:doc:`frontpage`                  ``frontpage``
:doc:`eggs2`
:doc:`behaviors_2`
:doc:`viewlets_2`
:doc:`reusable`
:doc:`embed`
:doc:`deployment_code`
:doc:`deployment_sites`

==============================    ===============================

Updating the code-package
-------------------------

This section if for training who want to update the code in :py:mod:`ploneconf.site` wfter changing something in the training documentation.

The current model uses only one branch of commits and maintains the integrity through rebases.

It goes like this:

* Only one one branch (master)
* Write the code for chapter 1 and commit
* Write the code for chapter 2 and commit
* Add the code for chapter 3 and commit
* You realize that something or wrong in chapter 1
* You branch off at the commit id for chapter 1
  `git checkout -b temp 123456`
* You cange the code and do a commit.
  `git commit -am 'Changed foo to also do bar'`
* Switch to master and rebase on the branch holding the fix which will inject your new commit into master at the right place:
  `git checkout master`
  `git rebase temp`
  That inserts the changes into master in the right place. You only maintain a master branch that is a sequence of commits.
* You then can update your chapter-docs to point to the corresponding commit ids:
  chapter one: `git checkout 121431243`
  chapter two: `git checkout 498102980`

Additionally you can

* set tags on the respective commits and move the tags. This way the docs do not need to change
* squash the commits between the chapters to every chapter is one commit.

To move tags after changes you do:

* Move a to another commit: `git tag -a <tagname> <commithash> -f`
* Move the tag on the server `git push --tags -f`

The final result should look like this:

.. figure:: ../_static/code_tree.png
   :align: center

I earlier versions we used a folder-based such as in https://github.com/collective/ploneconf.site_sneak. It proved to be a lot a lot of work to maintain that.
