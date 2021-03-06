******
cygrid
******

- *Version:* 1.0.0
- *Authors:* Benjamin Winkel, Lars Flöer, Daniel Lenz
- *User manual:* `stable <https://bwinkel.github.io/cygrid/>`__ |
  `developer <https://bwinkel.github.io/cygrid/latest/>`__

.. image:: https://img.shields.io/pypi/v/cygrid.svg
    :target: https://pypi.python.org/pypi/cygrid
    :alt: PyPI tag

.. image:: https://img.shields.io/badge/license-GPL-blue.svg
    :target: https://www.github.com/bwinkel/cygrid/blob/master/COPYING
    :alt: License

.. image:: http://img.shields.io/badge/arXiv-1604.06667-blue.svg
    :target: https://arxiv.org/abs/1604.06667
    :alt: Publication

.. image:: https://img.shields.io/badge/ascl-1606.003-blue.svg?colorB=262255
   :target: http://ascl.net/1606.003

Project Status
==============

.. image:: https://travis-ci.org/bwinkel/cygrid.svg?branch=master
    :target: https://travis-ci.org/bwinkel/cygrid
    :alt: cygrid's Travis CI Status

.. image:: https://ci.appveyor.com/api/projects/status/1ydk0hjf04t90aw5?svg=true
    :target: https://ci.appveyor.com/project/bwinkel/cygrid
    :alt: cygrid's AppVeyor CI Status

.. image:: https://coveralls.io/repos/github/bwinkel/cygrid/badge.svg?branch=master
    :target: https://coveralls.io/github/bwinkel/cygrid?branch=master
    :alt: cygrid's Coveralls Status

`Cygrid` is already used in several "production" systems, for example it was
utilized for two major 21-cm HI surveys, EBHIS and HI4PI. Nevertheless,
we cannot guarantee that it's completely bug-free. We kindly invite you to
use the library and we are grateful for feedback. Note, that work on the documentation is still ongoing.

Purpose
=======

`cygrid` allows to resample a number of spectra (or data points) to a regular
grid - a data cube - using any valid astronomical FITS/WCS projection (see
http://docs.astropy.org/en/stable/wcs/).

The method is a based on serialized convolution with finite gridding kernels.
Currently, only Gaussian (radial-symmetric or elliptical) kernels are provided
(which has the drawback of slight degradation of the effective resolution).
The algorithm has very small memory footprint, allows easy parallelization,
and is very fast.

A detailed description of the algorithm is given in `Winkel, Lenz & Flöer
(2016) <http://adsabs.harvard.edu/abs/2016A%26A...591A..12W>`_, which we
kindly ask to be used as reference if you found `cygrid` useful for your
research.

Features
========

- Supports any WCS projection system as target.
- Conserves flux.
- Low memory footprint.
- Scales very well on multi-processor/core platforms.

Installation
============

The easiest way to install cygrid is via `pip`::

    pip install cygrid

The installation is also possible from source. Download the tar.gz-file, extract (or clone from GitHub) and simply execute::

    python setup.py install

Note, for Windows machines only Python 3.5+ is supported.

Dependencies
------------

We kept the dependencies as minimal as possible. The following packages are
required:

- `Python 3.5` or later (`cygrid` versions prior to v1.0 support `Python 2.7`)
- `numpy 1.10` or later
- `cython 0.23.4` or later
- `astropy 1.0` or later

(Older versions of these libraries may work, but we didn't test this!)

If you want to run the notebooks yourself, you will also need the Jupyter
server, matplotlib and wcsaxes packages. To run the tests, you'll need HealPy.

Note, for compiling the C-extension, openmp is used for parallelization and
some C++11 language features are necessary. If you use gcc, for example, you
need at least version 4.8 otherwise the setup-script will fail. (If you have
absolutely no possibility to upgrade gcc, older version may work if you
replace `-std=c++11` with `-std=c++0x` in `setup.py`. Thanks to bs538 for
pointing this out.)

For Mac OS, it is required to use gcc-6 in order to install cygrid. We
recommend to simply use the `homebrew <http://brew.sh>`_ package manager and then use `brew install gcc`.

Usage
=====

Minimal example
---------------

Using `cygrid` is extremely simple. Just define a FITS header (with valid
WCS), define gridding kernel and run the grid function:

.. code-block:: python

    from astropy.io import fits
    import cygrid

    # read-in data
    glon, glat, signal = get_data(...)

    # define target FITS/WCS header
    header = {
        'NAXIS': 3,
        'NAXIS1': 101,
        'NAXIS2': 101,
        'NAXIS3': 1024,
        'CTYPE1': 'GLON-SFL',
        'CTYPE2': 'GLAT-SFL',
        'CDELT1': -0.1,
        'CDELT2': 0.1,
        'CRPIX1': 51,
        'CRPIX2': 51,
        'CRVAL1': 12.345,
        'CRVAL2': 3.14,
        }

    # prepare gridder
    kernelsize_sigma = 0.2

    kernel_type = 'gauss1d'
    kernel_params = (kernelsize_sigma, )
    kernel_support = 3 * kernelsize_sigma
    hpx_maxres = kernelsize_sigma / 2

    mygridder = cygrid.WcsGrid(header)
    mygridder.set_kernel(
        kernel_type,
        kernel_params,
        kernel_support,
        hpx_maxres
        )

    # do the gridding
    mygridder.grid(glon, glat, signal)

    # query result and store to disk
    data_cube = mygridder.get_datacube()
    fits.writeto(
        'example.fits',
        header=header, data=data_cube
        )


More use-cases and tutorials
----------------------------

Check out the `Jupyter notebooks <http://nbviewer.jupyter.org/github/bwinkel/cygrid/blob/master/notebooks/index.ipynb>`_
in the repository for further examples of how to use `cygrid`. Note that you
only view them on the nbviewer service, and will have to clone the repository
or download the notebooks to run them on your machine.

Who do I talk to?
=================

If you encounter any problems or have questions, do not hesitate to raise an
issue or make a pull request. Moreover, you can contact the devs directly:

- <bwinkel@mpifr.de>
- <mail@daniellenz.org>


Preferred citation method
=========================

Please cite our `paper <http://adsabs.harvard.edu/abs/2016A%26A...591A..12W>`_ if you use `cygrid` for your projects.
