Run Coq in your browser!
------------------------

A [Coq](https://coq.inria.fr) Online Integrated Development Environment
running in the browser! Try it:

<https://x80.org/rhino-coq/>

The goal of this project is to open new UI/interaction possibilites,
and to improve the acessibility of the platform itself.

Coq 8.5 is compiled to javascript using `js_of_ocaml` compiler, no
servers or external programs are needed. JsCoq runs fine in Chrome (>=
49) or Mozilla Firefox. You need a recent browser. It also runs in my old Galaxy Nexus.

* **Important:** Libraries are fully qualified, so you need to do `Require Import Coq.List.Lists", etc...
* **Important:** The project is pretty much a pre-alpha technology demo, you are welcome to use it but expect trouble.

## API / How to use

The current release provides a `coqManager` javascript object, so you can
embed Coq in your particular javascript application. The basic use is:

````
  <script type="text/javascript">
   var coq = new CoqManager (['addnC', 'prime_above']);
  </script>
```

But it requires some particular UI classes present in the document for
now. Look at `newide.html` to get more an idea on how to use jsCoq in
your own html pages.

### Options

JsCoq accepts the following options as an optional second parameter to
the constructor; current options are:

* `prelude: bool`: Whether to load Coq's prelude or not.
* `all_pkgs`, `init_pkgs`: List of Coq's packages to show/preload.
* `mock: bool`: Use a mock jsCoq object, useful for prototyping.

### Serialization

JsCoq supports serialization to Json or Sexps for Coq's internal data
structures. This is still not implemented in a systematic way, for now
you can get the current goal using the `goal_sexp` and `goal_json`
methods. We are working to add a more systematic implementation, as
well as to provide access to Coq printing facilities from JS.

### CoqDoc

An experimental version of coqdoc outputting jsCoq can be found at
https://github.com/ejgallego/coq/tree/coqdoc

Just build coqdoc normally and use the option `--backend=jscoq`.

This is good for converting old coqdoc files, but it may produce some artifacts. A
coqdoc replacement is being developed at
https://github.com/ejgallego/udoc

## Troubleshooting ##

Clearing the browser cache may solve lots of issues.

## Reporting Bugs ##

Feel free to use the issue tracker. Please include your
browser/OS/user-agent and command line options.

We don't consider the code/architecture stable yet, but any
contribution or comment is really welcome!

## What is broken ##

* Loading ML modules may be slow if not using the bytecode cache.
* `vm_compute` and `native_compute` are not supported.
* There surely are threading and performance problems.

## Contact & Sponsorship ##
![FEEVER Logo](/images/feever-logo.png?raw=true "Feever Logo")

jsCoq has been make possible thanks to funding by the [FEEVER](http://feever.fr) project.

Contact: Emilio J. Gallego Arias `e+jscoq at x80.org`.

## jsCoq Users:

Incomplete list of places where jsCoq has been used:

* https://team.inria.fr/marelle/en/advanced-coq-winter-school-2016/

## How to Install/Build ##

You can download ready-to-use builds from
https://github.com/ejgallego/jscoq-builds/ ; find below the
instructions to build JsCoq yourself.

* _warning_: The build process takes *more* than 8GiB of RAM.
* First, you need a dual 32/64 bits Ocaml toolchain. Get a
  recent opam and a multiarch gcc (`gcc-multilib` package in
  Debian/Ubuntu), then run:

  ````
$ cp -a opam/4.02.3+32bit ~/.opam/compilers/4.02.3/
$ ./toolchain-setup.sh
  ````

  and it should do the trick.

  You can tweak some variables in the `build-common.sh` file.

* Second, you need to build Coq v8.5:

  ````
$ git clone https://github.com/coq/coq.git ~/external/coq-git
$ cd ~/external/coq-git
$ git checkout v8.5
$ opam switch 4.02.3+32bit
$ eval `opam config env`
$ ./configure -local -coqide no -native-compiler no
$ make               # use -j N as desired
  ````

  jsCoq is compatible with vanilla Coq v8.5. However, we maintain a
  tree with some specific patches at
  https://github.com/ejgallego/coq/tree/jscoq-patchqueue

  If you want to use a different location for the Coq sources, edit
  the `COQDIR` variable in JsCoq's `Makefile`.

* You may want to select what libraries get
  included. `coq-tools/dftlibs.ml` allows you to do that.

* Finally:

  ````
$ ./build.sh
  ````

  should build jscoq. The script tries to manage the pain of the 32/64
  bit switch, you can also use make if you want finer control.

* To run jscoq in locally you may need to start your browser as:

  ````
$ google-chrome-beta --allow-file-access-from-files --js-flags="--stack-size=65536" index.html
  ````

* _Bytecode cache_:

  By default JsCoq must compile ocaml's bytecode to javascript on the
  fly to load cma plugins.  However, we can cache the process of
  compilation by creating a bcache. The process is a bit involved:

  1) Load all the .cma files you want to be cached from a regular web
     browser, they will get compiled and cached.

  2) Call the `dumpCache()` function from the JS console; this should
     download the cache.

  3) Place the files in the `jscoq` directory, and do `make
     bcache`. The cache will be then processed using the
     `coq-tools/byte_cache.js` program to generate a `/bcache.list`
     file and a `/bcache` directory.

     This requires node.

* Profit!
* Packages: ssreflect/mathcomp.

  JsCoq supports extra addons, including ssreflect. In order to build
  it with JsCoq, download the ssreflect distribution from
  `https://github.com/math-comp/math-comp`

  Go to the main directory and then build with:

  ```
$ opam switch 4.02.3+32bit
$ eval `opam config env`
$ export PATH=~/external/coq-git/bin:$PATH
$ make
  ```

  You need to specify in the jsCoq makefile the place where math-comp
  has been downloaded, `~/external/coq/math-comp` is the default.

  A patch optimizing mathcomp loading times can be found in the patch
  folder, highly recommended.

## Commit tag conventions [work in progress]:

- [jscoq]: ML/Coq interface.
- [ui]: Html/Css commit
- [cm]: CodeMirror provider.
- [libs]: Coq Library support and format.
- [doc]: Documentation.
- [addons]: Addons support.
- [makefile]: Build system.

## Documents

See the notes directory.
