dopam: Run OPAM under Docker
============================

[OPAM](https://opam.ocaml.org) is a source-level package manager that
supports a Git-based workflow and version constraint solving.  The `dopam`
command supports running it on a MacOS X computer that has no development
tools installed.  It does this by running all of the development tools
within a [Docker](http://docker.com) container.

## Installation

If you do not have Homebew installed, first do this from a Terminal:

    sudo mkdir /usr/local/bin
    sudo chown $USER /usr/local/bin

Then in your home directory:

    git clone https://github.com/avsm/dopam
    cp dopam/dopam /usr/local/bin/dopam
    chmod 755 /usr/local/bin/dopam
    
- *Status*: experimental, unreleased
- *Contact*: Anil Madhavapeddy <anil@recoil.org>
