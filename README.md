<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [The Kmax Tool Suite](#the-kmax-tool-suite)
  - [Getting Started](#getting-started)
  - [Upgrading `kmaxtools`](#upgrading-kmaxtools)
  - [Additional Documentation](#additional-documentation)
  - [Tool Overview](#tool-overview)
    - [Contributors](#contributors)
  - [Install from Repository](#install-from-repository)
  - [Quick Start](#quick-start)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# The Kmax Tool Suite

## Getting Started

Install via `pip3` for python 3.X or `pip` for python 2.X:

    sudo pip3 install kmaxtools

Download the Linux source:

    wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.tar.xz
    tar -xvf linux-5.4.tar.xz

Run `klocalizer`

    cd linux-5.4/
    klocalizer drivers/usb/storage/alauda.o

Build the resulting `.config` file with [make.cross](https://github.com/fengguang/lkp-tests/blob/master/sbin/make.cross):

    make.cross ARCH=x86_64 olddefconfig; make.cross ARCH=x86_64 clean drivers/usb/storage/alauda.o

## Upgrading `kmaxtools`

Use the `-U` flag to `pip3`/`pip`:

    sudo pip install -U kmaxtools

## Additional Documentation

[Advanced Usage](docs/advanced.md)

[Bugs Found by `kmaxtools`](docs/bugs_found.md)

## Tool Overview

The Kmax Tool Suite (kmaxtools) contains a set of tools for performing
automated reasoning on Kconfig and Kbuild constraints.  It consists of
the following tools:

- `klocalizer` takes the name of a compilation unit and automatically
  generates a `.config` file that, when compiled, will include the
  given compilation unit.  It uses the logical models from `kclause` and `kmax`
- `kclause` "compiles"
  [Kconfig](https://www.kernel.org/doc/html/latest/kbuild/kconfig-language.html)
  constraints into logical formulas.  `kextractor` uses Linux's
  own Kconfig parser to perform an extraction of the syntax of
  configuration option names, types, dependencies, etc.
- `kmax` collects configuration information from [Kbuild
  Makefiles](https://www.kernel.org/doc/html/latest/kbuild/makefiles.html).
  It determines, for each compilation unit, a symbolic Boolean
  expression that represents the conditions under which the file gets
  compiled and linked into the final program.  Its algorithm is
  described [here](https://paulgazzillo.com/papers/esecfse17.pdf) and
  the original implementation can be found
  [here](https://github.com/paulgazz/kmax/releases/tag/v1.0).

### Contributors

- [Paul Gazzillo](https://paulgazzillo.com) -- creator and developer
- [ThanhVu Nguyen](https://cse.unl.edu/~tnguyen/) -- developer and z3 integration into `kmax`
- Jeho Oh -- developer and kclause's Kconfig logical formulas
- [Julia Lawall](https://pages.lip6.fr/Julia.Lawall/) -- ideation, evaluation

## Install from Repository

    git clone https://github.com/paulgazz/kmax.git
    cd kmax
    sudo python3 setup.py install

## Quick Start

The fastest way to get started is to use formulas already extracted for your version of Linux, which you can download here: <https://kmaxtools.opentheblackbox.net/formulas>

    wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.tar.xz
    tar -xvf linux-5.4.tar.xz
    cd linux-5.4/
    wget https://kmaxtools.opentheblackbox.net/formulas/kmax-formulas_linux-v5.4.tar.bz2
    tar -xvf kmax-formulas_linux-v5.4.tar.bz2

This contains a `.kmax` directory containing the Kconfig and Kbuild
formulas for each architecture.  If a version is not available
[here](https://kmaxtools.opentheblackbox.net/formulas) submit an issue to request
the formulas be generated and uplodated or see below for directions on
generating these formulas.

Run klocalizer for a given compilation unit, e.g.,

    klocalizer drivers/usb/storage/alauda.o

This will search each architecture for constraint satisfiability,
stopping once one is found (or no architecture's constraints are
satisfiable).  `klocalizer` writes this configuration to `.config` and
prints the architectures, e.g., `x86_64`, to standard out.

To build the compilation unit using the generated `.config`, use
[make.cross](https://github.com/fengguang/lkp-tests/blob/master/sbin/make.cross).
First set any defaults for the `.config` file:

    make.cross ARCH=x86_64 olddefconfig

Then build the compilation unit:

    make.cross ARCH=x86_64 drivers/usb/storage/alauda.o

If you cannot get a configuration or it is still not buildable, see the [Troubleshooting](#troubleshooting) section.
