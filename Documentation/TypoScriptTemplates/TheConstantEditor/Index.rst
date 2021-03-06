
.. include:: ../../Includes.txt


.. _constant-editor:

Declaring constants for the Constant Editor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can put comments anywhere in your TypoScript. Comments are always
ignored by the parser when the template is processed. But the backend
module **WEB > Template** has the ability to use comments in the
constant editor to make simple configuration of a template even
easier than constants already make it themselves.

.. figure:: ../../Images/TemplatesConstantEditor.png
   :alt: The Constant Editor showing some categories with constants.

When the "Constant Editor" parses the template, *all*
comments before every constant-definition are registered. You can
follow a certain syntax to define what category the constant should be
in, which type it has and provide a description for the
constant.

.. code-block:: typoscript

   styles.content.textStyle {
      # cat=content/cText/1; type=; label= Bodytext font: This is the font face used for text!
      face =
      # cat=content/cText/2; type=int[1-5]; label= Bodytext size
      size =
      # cat=content/cText/3; type=color; label= Bodytext color
      color =
      color1 =
      color2 = blue
      properties =
   }

In the above example, three constants have syntactically correct comments
and will appear in the "Constant Editor". The other three will not. The
syntax is described in the rest of this chapter.

Making your most important constants available for the "Constant Editor"
is a real usability gain.


.. _constant-editor-default-values:

Default values:
"""""""""""""""

A constant may be given a default value when it is defined,
as is the case for the :code:`color2` constant in the above
example.

More generally, the default value of a constant is determined
by the value the constant has **before** the last template
(i.e. the one you're manipulating with the *Template* module)
is parsed (previous templates are typically included template records!).

However a template may contain a :code:`###MOD_TS:EDITABLE_CONSTANTS###`.
In this case, the constant definitions found before this mark are also regarded
as default values.

.. note::

   This is a rather obscure and little-used feature.


.. _constant-editor-comments:

Comments:
"""""""""

How the comments are perceived by the module:

- The comment line before the constant is considered to contain
  its definition.

- Each line is split at the :code:`;` (semicolon) character, that separates
  the various parameters

- Each parameter is split at the :code:`=` (equal) sign to separate the
  parameter's key and value.

The possible keys are described below.

.. _constant-editor-keys:

Keys:
"""""


.. _constant-editor-keys-cat:

cat=
~~~~

- Comma-separated list of the categories (case-insensitive) that the
  constant is a member of. You should really *list only one category*,
  because it usually turns out to be confusing for users, if the
  same constant appears in multiple categories!

- If the chosen category is *not* found among the default categories
  listed below, and is not a custom category either, it's regarded a new category.

- If the category is empty (""), the constant is excluded from the
  editor!

.. _constant-editor-keys-cat-predefined-categories:

Predefined categories
*********************

=========  ======================================================================
Category   Description
=========  ======================================================================
basic      Constants of superior importance for the template. This is typically
           dimensions, image files and enabling of various features. The most
           basic constants, which you would almost always want to configure.
menu       Menu setup. This includes font files, sizes, background images.
           Depending on the menu type.
content    All constants related to the display of page content elements.
page       General configuration like meta tags, link targets.
advanced   Advanced functions, which are seldom used.
=========  ======================================================================

.. _constant-editor-keys-cat-custom-categories:

Custom categories
*****************

To define your own category put a comment including the parameter
:code:`customcategory`. Example:

.. code-block:: typoscript

   # customcategory=mysite=LLL:EXT:myext/locallang.xlf:mysite

This line defines the new category "mysite" which will be available
for any constant defined **after** this line. The :code:`LLL:` reference
points to the localized string used to "name" the custom category
in the Constant Editor. Usage example:

.. code-block:: typoscript

   #cat=mysite//a; type=boolean; label=Global no_cache
   config.no_cache = 0


.. _constant-editor-keys-cat-subcategories:

Subcategories
*************

There are a number of subcategories one can use. Subcategories are entered
after the category separated by a slash :code:`/`. Example:

.. code-block:: typoscript

   "basic/color/a"

This will make the constant go into the "BASIC" category and be listed
under the "COLOR" section.

You can use one of the predefined subcategories or define your own. If
you use a non-existing subcategory, your constant will just go into
the subcategory "Other".

.. _constant-editor-keys-cat-predefined-subcategories:

Predefined subcategories
************************

Standard subcategories (in the order they get listed in the Constant
Editor):

===========  ========================================================================
Subcategory  Description
===========  ========================================================================
enable       Used for options that enable or disable primary functions of a
             template.
dims         Dimensions of all kinds; pixels, widths, heights of images, frames,
             cells and so on.
file         Files like background images, fonts and so on. Other options related
             to the file may also enter.
typo         Typography and related constants.
color        Color setup. Many colors will be found with related options in other
             categories though.
links        Links: Targets typically.
language     Language specific options.
===========  ========================================================================

There also exists a list of subcategories based on the default content elements:

cheader,cheader\_g,ctext,ctextpic,cimage,cbullets,ctable,cuploads,
cmultimedia,cmailform,csearch,clogin,csplash,cmenu,cshortcut,clist,cscript,chtml

These are all categories reserved for options that relate to content
rendering for each type of "tt\_content" element. See the static_template
of extension "css\_styled\_content" for examples.

.. _constant-editor-keys-cat-custom-subcategories:

Custom subcategories
********************

Defining a custom subcategory is similar to defining a custom category,
using the :code:`customsubcategory` parameter. Example:

.. code-block:: typoscript

   # customsubcategory=cache=LLL:EXT:myext/locallang.xlf:cache

Usage example:

.. code-block:: typoscript

   #cat=mysite/cache/a; type=boolean; label=Global no_cache
   config.no_cache = 0

Will look in the Constant Editor like this:

.. figure:: ../../Images/TemplatesCustomSubcategory.png
   :alt: The Constant Editor showing a custom category.


.. _constant-editor-keys-cat-constants-ordering:

Constants ordering
******************

The third part of the category definition is optional and represents
the order in which the constants are displayed in the Constant Editor.
The values are sorted alphabetically, so it is traditional to use letters.
Example:

.. code-block:: typoscript

   #cat=mysite/cache/b; type=boolean; label=Special cache
   config.no_cache = 0
   #cat=mysite/cache/a; type=boolean; label=Global no_cache
   config.no_cache = 0

The "Special cache" constant will be displayed after the "Global no_cache"
constant, because it is ranked with letter "b" and the other constant
has letter "a". Constants without any ordering information will come last.


.. _constant-editor-keys-type:

type=
~~~~~

There exists a number of predefined type, which define what kind
of field is rendered for inputting the constant.

===========================  ============================================================================
Type                         Description
===========================  ============================================================================
int [low-high]               Integer, opt. in range "low" to "high"

int+                         Positive integer

offset [L1,L2,...L6]         Comma-separated list of integers. Default is "x,y", but as comma separated
                             parameters in brackets you can specify up to 6 labels being comma
                             separated! If you wish to omit one of the last 4 fields, just don't
                             enter a label for that element.

color                        HTML color

wrap                         HTML code that is wrapped around some content.

options [item1,item2,...]    Selectbox with values/labels item1, item2 etc. Comma-separated. Split
                             by "=" also and in that case, first part is label, second is value

boolean [truevalue]          Boolean, opt. you can define the value of "true", def.=1

comment                      Boolean, checked= "", not-checked = "#".

file [ext-list/IMAGE\_EXT]   Selectorbox with resources. Opt. list allowed extensions (no space in
                             list!), e.g. "[ttf]" or "[txt,html,htm]". You can also enter
                             "[IMAGE\_EXT]" in which case the default image-extensions are listed.
                             (used for data type "imgResource")

string (the default)         Just a string value

user                         Path to the file and method which renders the option HTML,
                             for example  `type=user[Vendor\Extension\Namespace\ClassName->myCustomField]`.
                             The method should have following signature:
                             :php:`public function myCustomField(array $params, $configurationForm)`
                             where :php:`$configurationForm` is an instance of :php:`\TYPO3\CMS\Core\TypoScript\ConfigurationForm`.
                             See :php:`TYPO3\CMS\Extensionmanager\ViewHelpers\Form\TypoScriptConstantsViewHelper->renderUserFunction` for reference.
===========================  ============================================================================


.. _constant-editor-keys-label:

label=
~~~~~~

Text string, trimmed.

It gets split on the first :code:`:` (colon) to separate header and body of the comment.
The header is displayed on its own line in bold.

The string be localized by using the traditional "LLL" syntax. Example:

.. code-block:: typoscript

   #cat=Site conf/cache/a; type=boolean; label=LLL:EXT:examples/locallang.xlf:config.no_cache
   config.no_cache = 0

Note that a single string is referenced (not one for the header and
one for the description). This means that the localized string must
contain the colon separator (:code:`:`). Example:

.. code-block:: xml

   <trans-unit id="config.no_cache" xml:space="preserve">
     <source>Global no_cache: Check the box to turn off all caches.</source>
   </trans-unit>


.. _constant-editor-categories:

TSConstantEditor.[category]
"""""""""""""""""""""""""""

There exist a special object called :code:`TSConstantEditor`, which
is not used as a constant, but as a container for some help text to
be displayed in the Constant Editor.

.. warning::

   Although this feature is not officially deprecated, it is very old
   and you should probably avoid relying on it.


.. container:: table-row

   Property
         header

   Data type
         string

   Description
         Help title.


.. container:: table-row

   Property
         description

   Data type
         string, break by //

   Description
         Description, enter "//" to create a line break.


.. container:: table-row

   Property
         bulletlist

   Data type
         string, break by //

   Description
         Lines for a bulletlist, enter "//" (double-slash) in order to break to
         next bullet.


.. container:: table-row

   Property
         image

   Data type
         image

   Description
         This is an optional image you can attach to the category.

         The image must be located inside some extension and be referred to
         using the :code:`EXT:` syntax.


.. container:: table-row

   Property
         Numbered array

   Data type
         list of constant names

   Description
         For each constant listed in this array, a numbered mark will be displayed
         next to its name in the Constant Editor. This numbered mark should also be
         present in the image, so that users can make a relation between the constant
         and its impact.


.. _constant-editor-categories-example:

Example
~~~~~~~

Assuming the following constant is defined:

.. code-block:: typoscript

   # customcategory=mysite=MySite
   #cat=mysite/cache/a; type=boolean; label=Global no_cache
   config.no_cache = 0

we could have a constant editor configuration like:

.. code-block:: typoscript

   TSConstantEditor.MySite {
      header = Clearing the cache
      description = This explains the constant, altough it is really just a silly example
      image = EXT:doc_tut_templating/Resources/Public/Images/Distribution.png
      bulletlist = Please note//That the numbered item//Is missing in the image
      1 = config.no_cache
   }

The above example, will render as:

.. figure:: ../../Images/TemplatesConstantEditorHelp.png
   :alt: Help displayed in the Constant Editor

The important things to note are:

- after :code:`TSConstantEditor` comes the category's name and not the category's code,
  i.e. in the above example, it is "MySite" that must be used and not "mysite".
  This means you cannot use white spaces in your category's name because the TypoScript
  parser does not accept blanks in object names (i.e. using "My Site" as a category name
  will prevent you from defining a corresponding :code:`TSConstantEditor` help for that
  category)

- the values used in the numbered array are the full name of the constants
  (e.g. :code:`config.no_cache`). The related number (e.g. "1") is the number
  that will appear in the little alert pill next the constant in the Constant Editor.
