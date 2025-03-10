# SchOJ Judge [![Linux Build Status](https://img.shields.io/travis/SchOJ/judge.svg?logo=linux)](https://travis-ci.org/SchOJ/judge)

**A fork of [DMOJ/judge](https://github.com/DMOJ/judge)**

Python [AGPLv3](https://github.com/SchOJ/judge/blob/master/LICENSE) contest judge backend for the [SchOJ](http://github.com/SchOJ/site) / [DMOJ](http://github.com/DMOJ/site) site interface.

## What is added?

* New language support (like Julia)
* `unittest` capability

## Supported Platforms and Runtimes

Our instance runs on Linux x64. DMOJ/judge supports more architectures. Since we also update from upstream, SchOJ/judge may also work on these architectures.

The judge does **not** need a root user to run on Linux machines: it will run just fine under a normal user. However, to run it in Docker, a [privileged](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) permission is needed.

Supported languages include:
* C++ 11/14/17/20 (GCC and Clang)
* C 99/11
* Java 8/9/10/11/15/17
* Python 2/3
* PyPy 2/3
* Pascal
* Perl
* Mono C#/F#/VB
* Julia

The judge can also grade in the languages listed below. These languages are less tested and more likely to be buggy.
* Ada
* AWK
* COBOL
* D
* Dart
* Fortran
* Forth
* Go
* Groovy
* Haskell
* INTERCAL
* Kotlin
* Lean 4
* Lua
* NASM
* Objective-C
* OCaml
* PHP
* Pike
* Prolog
* Racket
* Ruby
* Rust
* Scala
* Chicken Scheme
* sed
* Steel Bank Common Lisp
* Swift
* Tcl
* Turing
* V8 JavaScript
* Brain\*\*\*\*
* Zig

## Installation
<<<<<<< HEAD
Installing the DMOJ judge creates two executables in your Python's script directory: `dmoj` and `dmoj-cli`.
`dmoj` is used to connect a judge to a DMOJ site instance, while `dmoj-cli` provides a command-line interface to a
local judge, useful for testing problems.

For more detailed steps, read the [installation instructions](https://docs.dmoj.ca/#/judge/setting_up_a_judge).

Note that **the only officially-supported Linux distribution is the latest Debian**, with the default `apt` versions of all runtimes. This is [what we run on dmoj.ca](https://dmoj.ca/runtimes/matrix/), and it should "just work". While the judge will likely still work with other distributions and runtime versions, some runtimes might fail to initialize. In these cases, please [file an issue](https://github.com/DMOJ/judge-server/issues).

### Stable Build
[![PyPI version](https://badge.fury.io/py/dmoj.svg)](https://pypi.python.org/pypi/dmoj)
[![PyPI](https://img.shields.io/pypi/pyversions/dmoj.svg)](https://pypi.python.org/pypi/dmoj)
=======
>>>>>>> 6227be6a (Update README to make it SchOJ-style)

We don't publish the package to Pypi while DMOJ/judge [does](https://pypi.python.org/pypi/dmoj).

<<<<<<< HEAD
```
$ pip install dmoj
```

### Bleeding-Edge Build
This is the version of the codebase we run live on [dmoj.ca](https://dmoj.ca).

```
$ git clone --recursive https://github.com/DMOJ/judge-server.git
$ cd judge-server
$ pip install -e .
```
=======
The [Linux Installation](https://docs.dmoj.ca/en/latest/judge/linux_installation/) instructions of DMOJ/judge also works on SchOJ/judge.
>>>>>>> 6227be6a (Update README to make it SchOJ-style)

Several environment variables can be specified to control the compilation of the sandbox:

* `DMOJ_TARGET_ARCH`; use it to override the default architecture specified for compiling the sandbox (via `-march`).
   Usually this is `native`, but will not be specified on ARM unless `DMOJ_TARGET_ARCH` is set (a generic, slow build will be compiled instead).

### With Docker
We maintain Docker images with all runtimes we support in the [runtimes-docker](https://github.com/DMOJ/runtimes-docker) project.

Runtimes are split into three tiers of decreasing support. Tier 1 includes
Python 2/3, C/C++ (GCC only), Java 8, and Pascal. Tier 3 contains all the
runtimes we run on [dmoj.ca](https://dmoj.ca). Tier 2 contains some in-between
mix; read the `Dockerfile` for each tier for details. These images are rebuilt
and tested every week to contain the latest runtime versions.

The script below spawns a tier 1 judge image. It expects the relevant
environment variables to be set, the network device to be `enp1s0`, problems
to be placed under `/mnt/problems`, and judge-specific configuration to be in
`/mnt/problems/judge.yml`. Note that runtime configuration is already done for you,
and will be merged automatically into the `judge.yml` provided.

```
$ git clone --recursive https://github.com/DMOJ/judge-server.git
$ cd judge-server/.docker
$ make judge-tier1
$ exec docker run \
    --name judge \
    -p "$(ip addr show dev enp1s0 | perl -ne 'm@inet (.*)/.*@ and print$1 and exit')":9998:9998 \
    -v /mnt/problems:/problems \
    --cap-add=SYS_PTRACE \
    -d \
    --restart=always \
    dmoj/judge-tier1:latest \
    run -p15001 -s -c /problems/judge.yml \
    "$BRIDGE_ADDRESS" "$JUDGE_NAME" "$JUDGE_KEY"
```

## Usage
### Running a Judge Server
```
$ dmoj --help
usage: dmoj [-h] [-p SERVER_PORT] -c CONFIG [-l LOG_FILE] [--no-watchdog]
            [-a API_PORT] [-A API_HOST] [-s] [-k] [-T TRUSTED_CERTIFICATES]
            [-e ONLY_EXECUTORS | -x EXCLUDE_EXECUTORS] [--no-ansi]
            server_host [judge_name] [judge_key]

Spawns a judge for a submission server.

positional arguments:
  server_host           host to connect for the server
  judge_name            judge name (overrides configuration)
  judge_key             judge key (overrides configuration)

optional arguments:
  -h, --help            show this help message and exit
  -p SERVER_PORT, --server-port SERVER_PORT
                        port to connect for the server
  -c CONFIG, --config CONFIG
                        file to load judge configurations from
  -l LOG_FILE, --log-file LOG_FILE
                        log file to use
  --no-watchdog         disable use of watchdog on problem directories
  -a API_PORT, --api-port API_PORT
                        port to listen for the judge API (do not expose to
                        public, security is left as an exercise for the
                        reverse proxy)
  -A API_HOST, --api-host API_HOST
                        IPv4 address to listen for judge API
  -s, --secure          connect to server via TLS
  -k, --no-certificate-check
                        do not check TLS certificate
  -T TRUSTED_CERTIFICATES, --trusted-certificates TRUSTED_CERTIFICATES
                        use trusted certificate file instead of system
  -e ONLY_EXECUTORS, --only-executors ONLY_EXECUTORS
                        only listed executors will be loaded (comma-separated)
  -x EXCLUDE_EXECUTORS, --exclude-executors EXCLUDE_EXECUTORS
                        prevent listed executors from loading (comma-
                        separated)
  --no-ansi             disable ANSI output
  --skip-self-test      skip executor self-tests
```

### Running a CLI Judge
```
$ dmoj-cli --help
usage: dmoj-cli [-h] -c CONFIG
                [-e ONLY_EXECUTORS | -x EXCLUDE_EXECUTORS]
                [--no-ansi]

Spawns a judge for a submission server.

optional arguments:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        file to load judge configurations from
  -e ONLY_EXECUTORS, --only-executors ONLY_EXECUTORS
                        only listed executors will be loaded (comma-separated)
  -x EXCLUDE_EXECUTORS, --exclude-executors EXCLUDE_EXECUTORS
                        prevent listed executors from loading (comma-
                        separated)
  --no-ansi             disable ANSI output
  --skip-self-test      skip executor self-tests
```

## Documentation
For info on the problem file format and more, [read the documentation.](https://docs.dmoj.ca)
