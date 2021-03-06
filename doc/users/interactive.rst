.. currentmodule:: matplotlib

.. _mpl-shell:

=====================
 Interactive Figures
=====================

.. toctree::


When working with data it is often invaluable to be able to interact
with your plots. In many cases the built in pan/zoom and mouse-location
tools are sufficient, but you can also use the Matplotlib event system
to build customized data exploration tools.

Matplotlib ships with :ref:`backends <what-is-a-backend>` binding to
several GUI toolkits (Qt, Tk, Wx, GTK, macOS, JavaScript) and third party
packages provide bindings to `kivy
<https://github.com/kivy-garden/garden.matplotlib>`__ and `Jupyter Lab
<https://github.com/matplotlib/ipympl>`__.  For the figures to be
"live" the GUI event loop will need to be integrated with your prompt. The
simplest way is to use IPython (see :ref:`below <ipython-pylab>`).

The `.pyplot` module provides functions for explicitly creating
Figures that include interactive tools, a toolbar, a tool-tip, and
:ref:`key bindings <key-event-handling>` ready to go:

`.pyplot.figure`
    Creates a new empty `.figure.Figure` or selects an existing figure

`.pyplot.subplots`
    Creates a new `.figure.Figure` and fills it with a grid of `.axes.Axes`

`.pyplot` has a notion of "The Current Figure" which can be accessed
through `.pyplot.gcf` and a notion of "The Current Axes" accessed
through `.pyplot.gca`.  Almost all of the functions in `.pyplot` pass
through the current `.Figure` / `.axes.Axes` (or create one) as
appropriate.  Matplotlib keeps a reference to all of the open figures
created this way so they will not be garbage collected.  You can close
and deregister `.Figure`\s from `.pyplot` individually via
`.pyplot.close` or close all open figures via ``plt.close('all')``.

For discussion of how the integration of the event loops and Matplotlib's event
system work under the hood see:

.. toctree::
    :maxdepth: 1

    interactive_guide.rst
    event_handling.rst


.. _ipython-pylab:

IPython integration
===================

We recommend using IPython for an interactive shell.  In addition to
all of its features (improved tab-completion, magics,
multiline editing, etc), it also ensures that the GUI toolkit event
loop is properly integrated with the command line (see
:ref:`cp_integration`).  To configure the integration and enable
:ref:`interactive mode <controlling-interactive>` use the
``%matplotlib`` magic

.. highlight:: ipython

::

   user@machine:~ $ ipython
   Python 3.8.2 (default, Apr  8 2020, 14:31:25)
   Type 'copyright', 'credits' or 'license' for more information
   IPython 7.13.0 -- An enhanced Interactive Python. Type '?' for help.

   In [1]: %matplotlib
   Using matplotlib backend: Qt5Agg

   In [2]: import matplotlib.pyplot as plt

Calling

::

   In [3]: fig, ax = plt.subplots()

will pop open a window for you and

::

   In [4]: ln, = ax.plot(range(5))

will show your data in the window.  If you change something about the
line, for example the color

::

   In [5]: ln.set_color('orange')

it will be reflected immediately. If you wish to disable this behavior
use

::

   In [6]: plt.ioff()

and

::

   In [7]: plt.ion()

re-enable it.

With recent versions of ``Matplotlib`` and ``IPython`` it is
sufficient to import `matplotlib.pyplot` and call `.pyplot.ion`, but
using the magic is guaranteed to work regardless of versions.


.. highlight:: python

.. _controlling-interactive:

Interactive mode
================


.. autosummary::
   :template: autosummary.rst
   :nosignatures:

   pyplot.ion
   pyplot.ioff
   pyplot.isinteractive


.. autosummary::
   :template: autosummary.rst
   :nosignatures:

   pyplot.show
   pyplot.pause


Interactive mode controls:

- whether created figures are automatically shown
- whether changes to artists automatically trigger re-drawing existing figures
- whether `.pyplot.show` returns immediately or after all of the
  figures have been closed when given no arguments


If in interactive mode, then:

- newly created figures will be shown immediately
- figures will automatically redraw on change
- pyplot.show will return immediately by default

If not in interactive mode then:

- newly created figures and changes to figures will
  not be reflected until explicitly asked to be
- pyplot.show runs the GUI event loop and does not return until all of
  the plot windows are closed


If you are in non-interactive mode (or created figures while in
non-interactive mode) you may need to explicitly call `.pyplot.show`
to bring the windows onto your screen.  If you only want to run the
GUI event loop for a fixed amount of time you can use `.pyplot.pause`.
This will both block the progress of your code (as if you had called
`time.sleep`), ensure the current window is shown and if needed
re-drawn, and run the GUI event loop (so the windows are "live" for
interaction) for the specified period of time.

Being in "interactive mode" is orthogonal to the GUI event loop being
integrated with your command prompt.  If you use `pyplot.ion`, but
have not arranged for the event loop integration, your figures will
appear but will not be "live" while the prompt is waiting for input.
You will not be able to pan/zoom and the figure may not even render
(the window might appear black, transparent, or as a snapshot of the
desktop under it).  Conversely, if you configure the event loop
integration, displayed figures will be "live" while waiting for input
at the prompt, regardless of pyplot's "interactive mode".  In either
case, the figures will be "live" if you use
``pyplot.show(block=True)``, `.pyplot.pause`, or run the the GUI main
loop in some other way.


.. warning::

   Using `.figure.Figure.show` it is possible to display a figure on
   the screen without starting the event loop and without being in
   interactive mode.  This may work (depending on the GUI toolkit) but
   will likely result in a non-responsive figure.

.. _navigation-toolbar:

Default UI
==========


The windows created by :mod:`~.pyplot` have an interactive toolbar with navigation
buttons and a readout of where the cursor is in dataspace.  A number of
helpful keybindings are registered by default.


.. _key-event-handling:

Navigation Keyboard Shortcuts
-----------------------------

The following table holds all the default keys, which can be
overwritten by use of your :ref:`matplotlibrc
<sphx_glr_tutorials_introductory_customizing.py>`.

================================== ===============================
Command                            Default key binding and rcParam
================================== ===============================
Home/Reset                         :rc:`keymap.home`
Back                               :rc:`keymap.back`
Forward                            :rc:`keymap.forward`
Pan/Zoom                           :rc:`keymap.pan`
Zoom-to-rect                       :rc:`keymap.zoom`
Save                               :rc:`keymap.save`
Toggle fullscreen                  :rc:`keymap.fullscreen`
Toggle major grids                 :rc:`keymap.grid`
Toggle minor grids                 :rc:`keymap.grid_minor`
Toggle x axis scale (log/linear)   :rc:`keymap.xscale`
Toggle y axis scale (log/linear)   :rc:`keymap.yscale`
Close Figure                       :rc:`keymap.quit`
Constrain pan/zoom to x axis       hold **x** when panning/zooming with mouse
Constrain pan/zoom to y axis       hold **y** when panning/zooming with mouse
Preserve aspect ratio              hold **CONTROL** when panning/zooming with mouse
================================== ===============================


.. _other-shells:

Other Python prompts
====================

If you can not or do not want to use IPython, interactive mode works
in the vanilla python prompt


.. sourcecode:: pycon

   >>> import matplotlib.pyplot as plt
   >>> plt.ion()
   >>>

however this does not ensure that the event hook is properly installed
and your figures may not be responsive.  Please consult the
documentation of your GUI toolkit for details.



Jupyter Notebooks / Lab
-----------------------

.. note::

   To get the interactive functionality described here, you must be
   using an interactive backend.  The default backend in notebooks,
   the inline backend, is not.  `~ipykernel.pylab.backend_inline`
   renders the figure once and inserts a static image into the
   notebook when the cell is executed.  The images are static and can
   not be panned / zoomed, take user input, or be updated from other
   cells.

To get interactive figures in the 'classic' notebook or jupyter lab
use the `ipympl <https://github.com/matplotlib/ipympl>`__ backend
(must be installed separately) which uses the **ipywidget** framework.
If ``ipympl`` is installed use the magic:

.. sourcecode:: ipython

  %matplotlib widget

to select and enable it.

If you only need to use the classic notebook you can use

.. sourcecode:: ipython

  %matplotlib notebook

which uses the `.backend_nbagg` backend which ships with Matplotlib.
However nbagg does not work in Jupyter Lab.

GUIs + jupyter
~~~~~~~~~~~~~~

If you are running your jupyter kernel locally you can use one of the
GUI backends.  The process running your kernel will show a GUI window
on your desktop adjacent to your web browser.  However if you move
that notebook to a remote server the kernel will try to open the GUI
window on *that* computer.  Unless you have arranged to forward the
xserver back to your desktop, you not be able to see or interact with
the figure (if it does not raise an exception outright).



PyCharm, Spyder, and VSCode
---------------------------

Many IDEs have built-in integration with Matplotlib, please consult their
documentation for configuration details.
