#########################################
Draft IVOA SODA web service specification
#########################################

.. abstract::

   Proposes a new specification for the IVOA SODA service that supports JSON encoding.

.. warning::

   This document is **not an IVOA standard** and is **not complete**.
   It is an experiment intended as input to ongoing IVOA discussions about future revisions to IVOA standards.
   The document is written as if it were an IVOA standard to save effort if parts of it can be cut and pasted into future documents, but it is not endorsed by the IVOA and is not part of any IVOA protocol.

.. seealso::

   :sqr:`91`: Draft IVOA web service standards framework
       The draft protocol structure for IVOA web services.
       This specifies what should be included in a web service specification such as this one.

   :sqr:`92`: Draft IVOA JSON encoding
       A network protocol encoding based on HTTP and JSON that follows this framework.

Overview
========

Server-side Operations for Data Access is a protocol for performing server-side data processing on an object and returning the processed form.
One common use of this protocol is to return a subsection of an object (a "cutout") along some axes.

.. note::

   An actual specification should include the far more extensive discussion from the current SODA standard.

Date types
==========

The following data types are used in various places in the operation specification.

.. note::

   These should probably be lifted into :sqr:`091`, particularly since the encoding as query parameters probably doesn't match this layout.

interval (list of float)
    Defines a range of floating point numbers.
    The value must be a list of length two, which specify the inclusive endpoints of the range.
    The lower value must be given first.
    Positive and negative infinity are permissible values and represent leaving the range open on that end.

point (object)
    Defines a point in the sky in the ICRS coordinate system.
    This object has two labels:

    ra (float)
        The right ascension.

    dec (float)
        The declination.

Operations
==========

Perform a single cutout
-----------------------

Return a subsection of the object specified in the ``id`` parameter.
The dimensions of the subsection are defined by the ``stencil`` parameter.

The body of a successful response is the desired object subsection as a data response.
The MIME type of that response will match the MIME type of the object from which the cutout was taken.
For example, a cutout of a FITS data file will have a MIME type of ``application/fits``.

Operation type: query

``GET`` is not supported in the JSON network encoding.

Parameters
^^^^^^^^^^

id (string)
    The object from which to take a cutout.

stencil (object)
    The specification of the shape of the cutout.
    This is an object with the following labels:

    circle (object, optional)
        Include the points within a circle with the given center and radius.
        This is an object with the following labels:

        center (point)
            Center of the circle.

        radius (float)
            Radius of the circle.

    polygon (object, optional)
        Include the points within the polygon specified by the given points.
        This is an object with the following labels:

        vertex (list of point, plural: vertices)
            The vertices of the polygon.
            They must be given in the order that ensures the polygon winding direction is counter-clockwise when viewed from the origin towards the sky.
            At least three points must be given.
            The last edge of the polygon is the implicit edge from the last point to the first point.

    range (object, optional)
        Include the points within a range of right ascension and declination values in the ICRS coordinate system.
        This is an object with the following labels:

        ra (interval)
            Range of right ascension values.

        dec (interval)
            Range of declination values.

    band (interval, optional)
        Include the wavelengths within the given interval.

    time (interval, optional)
        Include data timestamped within the provided range, interpreted as Modified Julian Date values.
        To extract data from a specific instant, specify an interval with equal start and end values.

    pol (list of enum, optional, plural: pols)
        Include the given polarization states.
        Values must be chosen from ``I``, ``Q``, ``U``, and ``V``.

    At least one of the stencil labels must be given.
    Only one of ``circle``, ``polygon``, or ``range`` may be given.

To do
=====

The following things should be included in this specification but haven't been written yet:

.. rst-class:: compact

- OpenAPI 3.0 schema.
- UWS API that supports multiple ids and multiple stencils and returns all of the results as a list of UWS results.
