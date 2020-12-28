.. _volto_talk_listview-label:

Volto View Components: A Listing View for Talks
===============================================

.. sidebar:: Volto chapter

  .. figure:: _static/volto.svg
     :alt: Volto Logo

  This chapter is about the React frontend Volto.

  Solve the same tasks in Plone Classic in chapter :doc:`views_3`

  .. topic:: Description

      Create a view that shows a list of content

.. sidebar:: Get the code! (:doc:`More info <code>`)

   Code for the beginning of this chapter::

       git checkout theming

   Code for the end of this chapter::

        git checkout talkview


To be solved task in this part:

* Create a view that shows a list of talks

In this part you will:

* Create the view component
* Register a react view component for the Folder content type
* Use an endpoint of Plone REST API to fetch content
* Display the fetched data

.. only:: not presentation

    Volto has multiple views for listing objects. Most of them list all objects in a folder or folderish type like the ``listing_view`` with title and description.
    The talk list should also show information about the dates, the locations and the speakers. We will create an additonal view for the folder content type.


Register the view in Volto and Plone
------------------------------------


Create a new file :file:`src/components/Views/TalkList.jsx`.

As a first step the file will hold a simple view component:

..  code-block:: jsx

    import React from 'react';

    const TalkListView = props => {
      return <div>I'm the TalkList component!</div>;
    };
    export default TalkListView;

As a convention we provide the view from :file:`src/components/index.js`.

..  code-block:: jsx
    :emphasize-lines: 2,4

    import TalkView from './Views/Talk';
    import TalkListView from './Views/TalkList';

    export { TalkView, TalkListView };

Now register the new component as a view for type Folder in ``src/config.js``.

..  code-block:: jsx
    :linenos:
    :emphasize-lines: 1,7-10

    import { TalkListView, TalkView } from './components';

    [...]

    export const views = {
      ...defaultViews,
      layoutViews: {
          ...defaultViews.layoutViews,
          talklist_view: TalkListView,
      },
      contentTypesViews: {
        ...defaultViews.contentTypesViews,
        talk: TalkView,
      },
    };

This extends the list of folder views ``defaultViews.layoutViews`` with the key/value pair ``talklist_view: TalkList`` .

To add a layout view you also have to add this new view in the ``ZMI`` of your ``Plone``. Login to your Plone instance. Go to ``portal_types`` and select the ``Folder``-Type to add your new ``talklist_view`` to the ``Available view methods``.

.. figure:: _static/add_talklistview_in_zmi.png
    :alt: Add new View to content type Folder in the ZMI.

    Add new View to content type Folder in the ZMI.

.. only:: not presentation

    .. warning::

        This step is not in the final code for this chapter since it only changes the frontend, you need to do it manually for now.
        It will be added in the next chapter where you change the backend-code.

        The change would be in :file:`profiles/default/types/Document.xml`:

        .. code-block:: xml
            :linenos:
            :emphasize-lines: 5-7

            <?xml version="1.0"?>
            <object name="Document" meta_type="Dexterity FTI" i18n:domain="plone"
                xmlns:i18n="http://xml.zope.org/namespaces/i18n">
              <property name="filter_content_types" purge="false">False</property>
              <property name="view_methods" purge="false">
                <element value="talklist_view"/>
              </property>
              <property name="behaviors" purge="false">
                <element value="plone.constraintypes"/>
              </property>
            </object>

From now on you can select the new view for folder:

.. figure:: _static/talklistview_select.png

Now we will improve this view step by step. We start working directly with the context of our talks folder. The context is part of the props of the view. To have a convenient access to the context we assign a variable ``content`` the value of ``props.content``.

Via ``content`` we have access to title, description and other attributes

..  code-block:: jsx
    :linenos:
    :emphasize-lines: 5

    import React from 'react';
    import { Container } from 'semantic-ui-react';
    import { Helmet } from '@plone/volto/helpers';

    const TalkListView = ({content}) => {
      return (
        <Container className="view-wrapper">
          <Helmet title={content.title} />
          <article id="content">
            <header>
            <h1 className="documentFirstHeading">{content.title}</h1>
            {content.description && (
              <p className="documentDescription">{content.description}</p>
            )}
            </header>
          </article>
        </Container>
      )
    };
    export default TalkListView;


.. only:: not presentation

    Display the content of a folder
    -------------------------------

    .. note::

        For the next part you should have some talks and no other content in one folder to work on the progressing view.

    .. warning::

        Due to a breaking change in Volto 10 the following code does not work anymore. ``content``  no longer holds the full content objects but a simplified representation of them. See https://docs.voltocms.com/upgrade-guide/#getcontent-changes

        Skip ahead to :ref:`talklistview_search_endpoint-label` until we fix this :)


    You can iterate over all items in our talks folder by using the map ``content.items``. To build a view with some elements we used in the ``TalkView`` before, we can reuse some components and definitions like the ``color_mapping`` for the ``audience``.

    ..  code-block:: jsx
          :emphasize-lines: 2-5,9-61

          import React from 'react';
          import { Container, Segment, Label, Image } from 'semantic-ui-react';
          import { Helmet } from '@plone/volto/helpers';
          import { Link } from 'react-router-dom';
          import { flattenToAppURL } from '@plone/volto/helpers';

          const TalkListView = props => {
            const { content } = props;
            const results = content.items;
            const color_mapping = {
              Beginner: 'green',
              Advanced: 'yellow',
              Professional: 'purple',
            };
            return (
              <Container className="view-wrapper">
                <Helmet title={content.title} />
                <article id="content">
                  <header>
                    <h1 className="documentFirstHeading">{content.title}</h1>
                    {content.description && (
                      <p className="documentDescription">{content.description}</p>
                    )}
                  </header>
                  <section id="content-core">
                    {results &&
                      results.map(item => (
                        <Segment padded>
                          <h2>
                            <Link to={item['@id']} title={item['@type']}>
                              {item.type_of_talk.title}: {item.title}
                            </Link>
                          </h2>
                          {item.audience?.map(item => {
                            let audience = item.title;
                            let color = color_mapping[audience] || 'green';
                            return (
                              <Label key={audience} color={color}>
                                {audience}
                              </Label>
                            );
                          })}
                          {item.image && (
                            <Image
                              src={flattenToAppURL(item.image.scales.preview.download)}
                              size="small"
                              floated="right"
                              alt={content.image_caption}
                              avatar
                            />
                          )}
                          {item.description && <div>{item.description}</div>}
                          <Link to={item['@id']} title={item['@type']}>
                            read more ...
                          </Link>
                        </Segment>
                      ))}
                  </section>
                </article>
              </Container>
            );
          };
          export default TalkListView;

    * With {content.items} we iterate over the contents of the folder and assign the received map to the constant ``results`` for further use.
    * With ``{results && results.map(item => ()}`` we test if there is any item in the map and then iterate over this items.
    * To use the existing Link-Component we'll have to use ``import { Link } from 'react-router-dom';`` and configure the component:

        * ``to={item['@id']}`` will make the link point to the URL of the item and assign it to the Link as destination
        * ``{item['@type']}`` will give you the contenttype name of the item, which could help you to change layouts for the listed items if you have different content in your folder
    * You can get all other information like title and description with the dotted notation like ``{item.title}`` and ``{item.description}``. We use that to display ``audience``, ``image`` and ``description`` like we already did in the talkview.

    The iteration over ``content.items`` to build a listing can be problematic though, because this approach has some limitations you may have to deal with:

    * listed content can include different types and could have different fields or use cases (long, difficult-to-read code if every addable type/use case has to be covered) or
    * not all content for the listing exists in one folder but may arranged in a wide structure (for example in topics or by day)


.. _talklistview_search_endpoint-label:

Using the search endpoint
-------------------------

To get a list of all talks - no matter where they are in our site - we will use the ``search endpoint`` of the Plone REST API.
That is the equivalent of using a catalog search in classic Plone (see :ref:`views3-catalog-label`).

..  code-block:: jsx
    :emphasize-lines: 6-7,11-13,21-28

    import React from 'react';
    import { Container, Segment, Label, Image } from 'semantic-ui-react';
    import { Helmet } from '@plone/volto/helpers';
    import { Link } from 'react-router-dom';
    import { flattenToAppURL } from '@plone/volto/helpers';
    import { searchContent } from '@plone/volto/actions';
    import { useDispatch, useSelector } from 'react-redux';

    const TalkListView = ({content}) => {
      const dispatch = useDispatch();
      const searchRequests = useSelector(state => state.search);
      const results = searchRequests.items;

      const color_mapping = {
        Beginner: 'green',
        Advanced: 'yellow',
        Professional: 'purple',
      };

      React.useEffect(() => {
        dispatch(
          searchContent('/', {
            portal_type: ['talk'],
            fullobjects: true,
          }),
        );
      }, [dispatch]);

      return (
        <Container className="view-wrapper">
          <Helmet title={content.title} />
          <article id="content">
            <header>
              <h1 className="documentFirstHeading">{content.title}</h1>
              {content.description && (
                <p className="documentDescription">{content.description}</p>
              )}
            </header>
            <section id="content-core">
              {results &&
                results.map(item => (
                  <Segment padded>
                    <h2>
                      <Link to={item['@id']} title={item['@type']}>
                        {item.type_of_talk.title || item.type_of_talk.token}:{' '}
                        {item.title}
                      </Link>
                    </h2>
                    {item.audience?.map(item => {
                      let audience = item.title || item.token;
                      let color = color_mapping[audience] || 'green';
                      return (
                        <Label key={audience} color={color}>
                          {audience}
                        </Label>
                      );
                    })}
                    {item.image && (
                      <Image
                        src={flattenToAppURL(item.image.scales.preview.download)}
                        size="small"
                        floated="right"
                        alt={content.image_caption}
                        avatar
                      />
                    )}
                    {item.description && <div>{item.description}</div>}
                    <Link to={item['@id']} title={item['@type']}>
                      read more ...
                    </Link>
                  </Segment>
                ))}
            </section>
          </article>
        </Container>
      );
    };

    export default TalkListView;


.. only:: not presentation

    We make use of the ``useSelector`` and ``useDispatch`` hooks from the react-redux library. They are used to subscribe our component to the store changes (``useSelector``) and for issuing Redux actions (``useDispatch``) from our components.

    Afterwards we can define the new results with ``const results = searchRequests.items;``, which will use the hooks and actions to receive a map of items.

    The search itself will be defined in the ``React.useEffect(() => {})``- section of the code and will contain all parameters for the search. In case of the talks listing view we search for all objects of type talk with ``portal_type:['talk']`` and force to fetch full objects with all information.

    The items themselves won't change though, so the rest of the code will stay untouched.

    Now you see all talks in the list no matter where they are located in the site.

    .. warning::

      If you change the view in Volto you’ll also change the view in the backend (Plone). As long as the same view isn’t available in the backend too, the site will show an error!

Search options
--------------

* The default representation for search results is a summary that contains only the most basic information like **title, review state, type, path and description**.
* With the option ``fullobjects`` all available field values are present in the fetched data.
* Another option is ``metadata_fields``, which allows to get more attributes (selection of Plone catalog metadata columns) than the default search without a performance expensive fetch via option fullobjects.

Possible **sort criteria** are indices of the Plone catalog.

.. seealso::

  * https://plonerestapi.readthedocs.io/en/latest/searching.html
  * http://docs.plone.org/develop/plone/searching_and_indexing/query.html

.. _volto_talk_listview-exercise-label:

Exercises
---------

Exercise 1
**********

Modify the criteria in the search to sort the talks in the order of their modification date.

..  admonition:: Solution
    :class: toggle

    .. code-block:: python
        :linenos:

        React.useEffect(() => {
          dispatch(
            searchContent('/', {
              portal_type: ['talk'],
              sort_on: 'modified',
              fullobjects: true,
            }),
          );
        }, [dispatch]);


Exercise 2
**********

Change ``TalkListView`` to show the keynote speakers (name, biography and foto) and with a link to their keynote. Remember that you cannot search for a specific value in ``type_of_talk`` yet so you'll have to filter the results.

For bonus points create and register it as a separate view ``Keynotes``

..  admonition:: Solution
    :class: toggle

    Write the view:

    ..  code-block:: jsx
        :linenos:
        :emphasize-lines: 34-36

        import React from 'react';
        import { Container, Segment, Image } from 'semantic-ui-react';
        import { Helmet } from '@plone/volto/helpers';
        import { Link } from 'react-router-dom';
        import { flattenToAppURL } from '@plone/volto/helpers';
        import { searchContent } from '@plone/volto/actions';
        import { useDispatch, useSelector } from 'react-redux';

        const TalkListView = props => {
          const { content } = props;
          const searchRequests = useSelector(state => state.search);
          const dispatch = useDispatch();
          const results = searchRequests.items;

          React.useEffect(() => {
            dispatch(
              searchContent('/', {
                portal_type: ['talk'],
                review_state: 'published',
                fullobjects: true,
              }),
            );
          }, [dispatch]);

          return (
            <Container className="view-wrapper">
              <Helmet title={content.title} />
              <article id="content">
                <header>
                  <h1 className="documentFirstHeading">Our Keynote Speakers</h1>
                </header>
                <section id="content-core">
                  {results &&
                    results.map(
                      item =>
                        item.type_of_talk.title === 'Keynote' && (
                          <Segment padded>
                            <h2>{item.speaker}</h2>
                            {item.image && (
                              <Image
                                src={flattenToAppURL(
                                  item.image.scales.preview.download,
                                )}
                                size="medium"
                                centered
                                alt={item.speaker}
                              />
                            )}
                            {item.speaker_biography && (
                              <div
                                dangerouslySetInnerHTML={{
                                  __html: item.speaker_biography.data,
                                }}
                              />
                            )}
                            <h3>
                              Keynote:{' '}
                              <Link to={item['@id']} title={item['@type']}>
                                {item.title}
                              </Link>
                            </h3>
                          </Segment>
                        ),
                    )}
                </section>
              </article>
            </Container>
          );
        };
        export default TalkListView;

    .. note::

        * The query uses ``review_state: 'published'``
        * Filtering is done using ``item.type_of_talk.title === 'Keynote' && (...`` during the iteration.

    To regster it move the code to new :file:`frontend/src/components/Views/Keynotes.jsx` and rename it to ``KeynotesView``:

    ..  code-block:: jsx

        const KeynotesView = props => {
          [...]
        }

        export default KeynotesView;

    Export it in :file:`frontend/src/components/index.js`:

    ..  code-block:: jsx
        :emphasize-lines: 3,5

        import TalkView from './Views/Talk';
        import TalkListView from './Views/TalkList';
        import KeynotesView from './Views/Keynotes';

        export { TalkView, TalkListView, KeynotesView };

    Register the component as layout view for folderish types in ``frontend/src/config.js``.

    ..  code-block:: jsx
        :emphasize-lines: 1,10

        import { TalkListView, TalkView, KeynotesView } from './components';

        [...]

        export const views = {
          ...defaultViews,
          layoutViews: {
            ...defaultViews.layoutViews,
            talklist_view: TalkListView,
            keynotes_view: KeynotesView,
          },
          contentTypesViews: {
            ...defaultViews.contentTypesViews,
            talk: TalkView,
          },
        };
