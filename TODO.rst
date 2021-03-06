=======================
The Skyfield To-Do List
=======================

This list includes both a section on immediately-reachable goals that
are appropriate for sprints and collaboration, and longer-term goals
that the code base is not quite ready for yet but that we do not want to
forget.

Sprint Possibilities
====================

* Trying to index a unit class should print help suggesting a unit be
  specified, similar to trying to iterate across one.

* Several interesting API questions arise because of
  `this Stack Overflow question <https://stackoverflow.com/questions/62654081/path-between-two-topos-locations-determine-latitude-and-longitude-where-a-giv>`_.
  Should I finally go through with renaming ``.position`` to ``.xyz``?
  Should ``.from_altaz()`` retain observer data
  so that a follow-up ``.altaz()`` returns the same coordinates?
  Should positions fully support math,
  so the computation of the Topos position can only be computed once?
  Should the result of ``.from_altaz()`` be somehow possible to connect
  with ``subpoint()``,
  which is currently stranded over on the ``Geocentric`` class —
  maybe ``subpoint()`` really should be a constructor on the Topos class
  that can take any kind of position that’s geocentered?

* For #145, skip deflections of planets that can’t affect an observation.

* For #145, create a good syntax for combining two ephemerides.

* Expand a bit on the documentation for stars, now that they can have an
  epoch for their position.

* Load and use the various offsets between UTC and TAI that were in
  effect before 1972.

* When I wrote `add_deflection()` and needed to know whether Jupiter
  itself is available in an ephemeris, or whether the Jupiter Barycenter
  should be used in its place, I tried writing the test `if name not in
  ephemeris:`.  But this sent the code into an infinite loop!  Why does
  `in` not work on an ephemeris object?  This should be fixed, and a
  test written to keep it fixed.

* The deflection code should really use integer identifiers instead of
  using names, which are slower because they need decoding.

* The deflection code should have a quick way to reach in and ask an
  ephemeris for a raw position in au, without having to spin up a body
  object and have it spin up a `Distance` object.

* We currently download most SPICE kernels from NAIF, but have to use
  FTP for fetching DE422.  Are the files from the two sites equivalent
  and do they have the same data?  Should we prefer one or the other?

* We should have an illustration of Earth satellite heights above the
  surface, plotted against a blue atmosphere fading out into the black
  of space as the plot goes upwards towards the top.

* How can users find out what’s in an ephemeris?

* The ``Timescale`` object does not currently know where its data comes
  from, so its ``repr()`` is pretty uninformative.  Should it someday be
  put in charge of loading its data from the data files specified, so
  that its ``repr()`` can print out which leap second file and delta T
  file it is using?  Should it also display how up to date the files
  are, and what leap seconds it knows about?  Also: it should be told
  the expiration date of all of its data, so that it can print it out as
  part of its ``repr()``.

* In `stars.rst`, document the other alternatives for how to set the RA
  and dec of a new Star object.

* Solar and lunar eclipses.

* We should implement comets and asteroids using the standard formulae
  (can we find a good vector version, that will match the rest of our
  approach?) for a Keplerian orbit.

Adding more smarts to ephemeris handling
========================================

* When we add or subtract vectors, the new `VectorSum` needs to inherit
  an `ephemeris` from one of the segments being combined.  Right now it
  just grabs the first one.  What if, of the choices of ephemeris among
  the segments, grabbing the first one gives us an ephemeris that is
  missing several key large bodies, and so we run into an exception when
  `.apparent()` tries to compute gravitational deflection?  Should we be
  more intelligent in our choice?  Or should we even combine the various
  ephemerides our segments might offer?  Or should we specifically go
  ahead and look for the deflectors we need and try to find a segment
  for them each?

* And additionally: the error when not enough bodies are available for
  deflection maybe someday needs to be more helpful.

* Bring support for Type 1 and Type 21 JPL ephemerides into Skyfield
  from the third-party libraries where it now lives.  The routines will
  need to be vectorized and updated to use a Skyfield approach to vector
  and matrix operations.  When complete and documented, make a comment
  at: https://github.com/skyfielders/python-skyfield/issues/350

For 2.0
=======

* Remove old deprecation warnings for pre-1.0 behaviors.

* Remove support and tests for old ephemeris Python packages.

Longer-term goals
=================

* Make all objects that are `.observe()`’d from Earth include a
  sublatitude and sublongitude coordinate stating the position on Earth
  from which they appear directly overhead.  When complete, make a note
  at the PyEphem GitHub issue:

  https://github.com/brandon-rhodes/pyephem/issues/16

* When Earth Satellites are implemented, include the orbit number of a
  satellite's current position in the public attributes that are set on
  the resulting position object, as promised in PyEphem GitHub issue:

  https://github.com/brandon-rhodes/pyephem/issues/15
