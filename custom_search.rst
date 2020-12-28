Custom search
=============

We can use the indexes we created in the last chapter to further improve the talk-list.

If the chapters about views seem complex, the now shown custom search addon might be a great alternative until you feel comfortable writing views and templates. There are several tools that allow you to add amazing custom searches and content-listings through the web in Plone.

eea.facetednavigation
---------------------

* Install `eea.facetednavigation <https://pypi.org/project/eea.facetednavigation/>`_
* Enable faceted navigation on a new folder "Discover talks" by clicking on *actions* > *Enable faceted navigation*
* Click on the tab *Faceted criteria* to configure it

    * Select 'Talk' for *Portal type*, hide *Results per page*
    * Add a checkboxes-widget to left and use the catalog index *Audience* for it.
    * Add a select-widget for speaker
    * Add a radio-widget for type_of_talk
    * Other noteable widgets are: tagcloud, a-z, search

Examples:

* https://www.dipf.de/en/research/current-projects
* https://www.mountaineers.org/courses/courses-clinics-seminars
* https://www.dyna-jet.com/hochdruckreiniger

.. TODO: add custom eea-view using dates

.. seealso::

    We use the new catalog indexes to provide the data for the widgets and search the results. For other use-cases we could also use either the built-in vocabularies (https://pypi.org/project/plone.app.vocabularies) or create custom vocabularies for this.

    * Custom vocabularies ttw using `Products.ATVocabularyManager <https://pypi.org/project/Products.ATVocabularyManager>`_
    * Programming using Vocabularies: https://docs.plone.org/4/en/external/plone.app.dexterity/docs/advanced/vocabularies.html
