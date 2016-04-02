dopam: Run OPAM under Docker
============================

[OPAM](https://opam.ocaml.org) is a source-level package manager that
supports a Git-based workflow and version constraint solving.  The `dopam`
command supports running it on a MacOS X computer that has no development
tools installed.  It does this by running all of the development tools
within a [Docker](http://docker.com) container.

`dopam` supports simultaneously running a variety of OCaml versions (4.01,4.02,4.03,4.03-flambda),
many distributions (Debian, Ubuntu, CentOS, Fedora, Alpine) and multiple CPU
architectures (x86_64, ARM) and so on.

## Installation

If you do not have Homebew installed, first do this from a Terminal:

    sudo mkdir /usr/local/bin
    sudo chown $USER /usr/local/bin

Then in your home directory:

    git clone https://github.com/avsm/dopam
    cp dopam/dopam /usr/local/bin/dopam
    chmod 755 /usr/local/bin/dopam

## Usage

To get started quickly:

- change to a source directory of an OPAM project
  file) and run `dopam init`.  This will create a `default` container that
  mounts your project into a Debian image and pins the local
  source code.
- `dopam init` drops you into a shell within the container.
  You can get back with `dopam shell` or open concurrent shells.
- Choose another compiler version or distro with command line
  flags to `dopam init`.  For example:
    - `dopam init -d ubuntu cohttp ~/src/ocaml-cohttp`
    - `dopam init -a arm uri ~/src/ocaml-uri`
    - `dopam init -d centos -v 4.03`
    - `dopam init -d alpine -v 4.03-flambda mirage ~/src/mirage`
- If you have a custom OPAM repository that you wish to use,
  just specify it via `-r`:
    - `dopam init -r ~/myopamrepo -d fedora -v 4.02 proj ~/myproj`
- `dopam list` shows all known projects.
- `dopam save` freezes all local changes and commits them into 
  new images with those changes.  Try not to overly use this though,
  as containers are meant to be stateless.
- `dopam reset` will remove containers so that they can be rebuilt.

### Man page

```
$ dopam help
Usage:
  dopam init [<options>] [<name>] [<srcdir>]
  dopam list
  dopam shell [<options>] <name>
  dopam reset [<name>]
  dopam save [<name>]
  dopam selfupdate

General options:
 -n: dry run, just echo commands

Init options:
 -j: number of concurrent OPAM jobs
 -d: Distro (debian,ubuntu,centos,fedora,alpine)
 -a: CPU arch (x86_64,arm)
 -v: OCaml compiler version (4.01,4.02,4.03,4.03-flambda)
 -r: Custom local OPAM repository directory
 <name>: OPAM project name (default: default)
 <srcdir>: Local source directory (default: .)

dopam reset: rebuilds all the local containers from scratch
dopam save: commits changes to a local image
dopam selfupdate: updates the dopam script with latest from GitHub
```

- *Status*: experimental, unreleased
- *Contact*: Anil Madhavapeddy <anil@recoil.org>
