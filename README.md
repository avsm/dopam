dopam: Run OPAM under Docker
============================

[OPAM](https://opam.ocaml.org) is a source-level package manager that
supports a Git-based workflow and version constraint solving.  The `dopam`
command supports running it on a MacOS X computer that has no development
tools installed.  It does this by running all of the development tools
within a [Docker](http://docker.com) container.

`dopam` supports simultaneously running a variety of OCaml versions (4.01,4.02,4.03,4.03-flambda),
many distributions (Debian, Ubuntu, CentOS, Fedora, Alpine) and multiple CPU
architectures (x86/64, ARM) and so on.

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
  source code.  You can edit the source code on your Mac as normal, and
  run `make` from within the container.
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
- `dopam bulk-compilers` will compile the source code with a variety
  of OCaml compiler versions so you can test compatibility.
- `dopam bulk-distros` will build your code on a variety of Linux
  distributions (Debian, Ubuntu, Fedora, CentOS, Alpine, etc).  You
  can use this to refine the `depext` tags for any system packages
  required for your source code.
- Your `~/.gitconfig` and `~/.ssh` will be mapped into the container.
  If you are running [docker-ssh-agent-forward](https://github.com/avsm/docker-ssh-agent-forward)
  then the agent socket will also be forwarded inside the container so you can
  pull/push repos that need authentication.

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

## Customising the package

Your repository can contain OPAM extension points so that you
can customise the packages that are installed.

### OPAM file

Most repositories will have a single `opam` file that describes
the project dependencies.  If this file is present, then `dopam`
will execute the following commands within the container:

    opam pin --yes -k auto -n add <project> /home/opam/src
    opam-depext -u <project>
    opam install -y -v -j <njobs> --deps-only <project>

This will cause all the dependencies (including system packages)
to be installed, and the source repository should be ready to be
built.

### OPAM remote

If you have more complex dependency requirements, you can also use a local OPAM
repository to name a collection of dependencies.  These dependencies can
include remote `git` or `hg` version-controlled trees, as well as packages that
do not yet exist in the central OPAM repository.

To do this, create a directory called `repo/` under which you place a
standard OPAM repository.  This will most commonly look like:

    repo/
      packages/
        foo.dev/
          opam
          url
        bar.dev/
          opam
          url

The `url` file can point to a `git` remote by placing this inside it:

    git: "git://github.com/avsm/foo#my-branch

The branch can also point to a specific revision or tag.  See the
[OPAM documentation](https://github.com/ocaml/opam/wiki/Packaging) for
more information on creating custom remote repository.

## Contact

- *Status*: experimental, unreleased
- *Contact*: Anil Madhavapeddy <anil@recoil.org>
