# Montage v6.1 Debug Setup for Python 3.8

This guide will walk you through setting up and debugging Montage v6.1 specifically for Python 3.8 using Docker.

## Prerequisites

- Docker installed on your system.
- Basic knowledge of git and Python package management.

## Step 1: Clone the Montage Repository

Begin by cloning the Montage repository from GitHub:

```bash
git clone https://github.com/guswns531/Montage.git
cd Montage/
```

## Step 2: Checkout the Specific Version (v6.1)

Checkout the version `v6.1` and create a new branch for debugging:

```bash
git checkout tags/v6.1
git branch debug-v6.1
git checkout debug-v6.1
```

## Step 3: Start the Debugging Process

Add all changes and commit them to the new branch:

```bash
git add .
git commit -m "Start Debug Montage-v6.1"
git push --set-upstream origin debug-v6.1
```

## Step 4: Start the Docker Environment

Run the following script to start the Docker environment:

```bash
sh Python_ManyLinux/startDocker.sh
```

## Step 5: Setting Up in Docker for Python 3.8

Once inside Docker, navigate to the Montage build directory:

```bash
cd /build/Montage/
```

### Step 5.1: Install Required Python Packages

Install the necessary Python packages:

```bash
/opt/python/cp38-cp38/bin/pip install Cython==0.29.24
/opt/python/cp38-cp38/bin/pip install jinja2
```

### Step 5.2: Build the Project

Run the `make` command to build the project:

```bash
make
```

### Step 5.3: Clean Previous Builds (Optional)

Remove any previous build artifacts:

```bash
rm -rf wheelhouse dist
rm -rf MontagePy.egg-info build dist MontagePy/__pycache__
```

### Step 5.4: Parse the Python Script

Run the following command to parse the `parse.py` script:

```bash
/opt/python/cp38-cp38/bin/python parse.py
```

### Step 5.5: Modify the `_wrappers.pyx` File

Use the following `sed` command to modify the `_wrappers.pyx` file:

```bash
sed '/^def mViewer/a \ \ \ \ # Next four lines added by sed script\n    import pkg_resources\n\n    if fontFile == "":\n        fontFile = pkg_resources.resource_filename("MontagePy", "FreeSans.ttf")' MontagePy/_wrappers.pyx > MontagePy/tmpfile

mv MontagePy/tmpfile MontagePy/_wrappers.pyx
```

### Step 5.6: Build and Package the Python Wheel

Build the project and create a Python wheel:

```bash
/opt/python/cp38-cp38/bin/python setup_manylinux.py build bdist_wheel
```

### Step 5.7: Repair and Move the Wheel

Use `auditwheel` to repair the wheel and then move it to the distribution directory:

```bash
auditwheel repair dist/*.whl
rm dist/*
mv wheelhouse/* dist
```

### Step 5.8: Install the Final Package

Finally, install the built package:

```bash
/opt/python/cp38-cp38/bin/pip install wheelhouse/MontagePy-1.2.3-cp38-cp38-manylinux_2_5_x86_64.manylinux1_x86_64.whl
```

## Conclusion

Following these steps will set up and debug Montage v6.1 for Python 3.8 within a Docker environment. Ensure you replace paths and version numbers according to your specific setup if they differ.

---

Montage: Astronomical Image Mosaics, Examination, and Visualization
===================================================================

Montage (http://montage.ipac.caltech.edu) is an Open Source toolkit,
distributed with a BSD 3-clause license, for assembling Flexible 
Image Transport System (FITS) images into mosaics according to 
the user's custom specifications of coordinates, projection,
spatial sampling, rotation and background matching.

The toolkit contains utilities for reprojecting and background 
matching images, assembling them into mosaics, visualizing the
results, and discovering, analyzing and understanding image metadata
from archives or the user's images.

Montage is written in ANSI-C and is portable across all common
Unix-like platforms, including Linux, Solaris, Mac OSX and Cygwin on
Windows.  The package provides both stand-alone executables and
the same functionality in library form.  It has been cross-compiled
to provide native Windows executables and packaged as a binary Python
extension (available via "pip install MontagePy").

The distribution contains all libraries needed to build the toolkit 
from a single simple "make" command, including CFITSIO and the WCS
library (which has been extended to support HEALPix and World-Wide
Telescope TOAST projections. The toolkit is in wide use in astronomy
to support research projects, and to support pipeline development,
product generation and image visualization for major projects and
missions; e.g. Spitzer Space Telescope, Herschel, Kepler, AKARI and
others. Montage is used as an exemplar application by the computer
science community in developing next-generation cyberinfrastructure,
especially workflow frameworks on distributed platforms, including
multiple clouds.

Montage provides multiple reprojection algorithms optimized for 
different needs, maximizing alternately flux conservation, range of
projections, and speed.

The visualization module supports full (three-color) display of FITS
images and publication quality overlays of catalogs (scaled symbols),
image metadata, and coordinate grids.  It fits in equally well in
pipelines or as the basis for interactive image exploration and there
is Python support for the latter (It has also been used in web/Javascript
applications).

Montage was funded from 2002 to 2005 by the National Aeronautics and
Space Administration's Earth Science Technology Office, Computation
Technologies Project, under Cooperative Agreement Number NCC5-626
between NASA and the California Institute of Technology. The Montage
distribution includes an adaptation of the MOPEX algorithm developed
at the Spitzer Science Center. Montage is now funded by the National
Science Foundation under Award Number NSF ACI-1440620.
