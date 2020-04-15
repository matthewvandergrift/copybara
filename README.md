# Copybara

*A tool for transforming and moving code between repositories.*

Copybara is a tool used internally at Google. It transforms and moves code between repositories.

Often, source code needs to exist in multiple repositories, and Copybara allows you to transform
and move source code between these repositories. A common case is a project that involves
maintaining a confidential repository and a public repository in sync.

Copybara requires you to choose one of the repositories to be the authoritative repository, so that
there is always one source of truth. However, the tool allows contributions to any repository, and
any repository can be used to cut a release.

The most common use case involves repetitive movement of code from one repository to another.
Copybara can also be used for moving code once to a new repository.

Examples uses of Copybara include:

  - Importing sections of code from a confidential repository to a public repository.

  - Importing code from a public repository to a confidential repository.

  - Importing a change from a non-authoritative repository into the authoritative repository. When
    a change is made in the non-authoritative repository (for example, a contributor in the public
    repository), Copybara transforms and moves that change into the appropriate place in the
    authoritative repository. Any merge conflicts are dealt with in the same way as an out-of-date
    change within the authoritative repository.

Currently, the only supported type of repository is Git. Copybara also supports reading from Mercurial repositories, but the feature is still experimental.
Support for other repositories types will be added in the future.

## Example

```python
source = "https://github.com/matthewvandergrift/CopySource.git"
destination = "https://github.com/matthewvandergrift/CopyDest.git"

core.workflow(
    mode = "ITERATIVE",
    name = "default",
    origin = git.origin(
        url = source,
        ref = "master",
    ),
    destination = git.destination(
        url = destination,
        fetch = "master",
        push = "master",
    ),
    destination_files = glob(["**"]),
    authoring = authoring.pass_thru("<matthew.vandergrift@live.com>"),
)
```

## Getting Started using Copybara

Copybara doesn't have a release process yet, so you need to compile from HEAD. In order to do that
you need:

  * [Install JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
  * [Install Bazel](https://docs.bazel.build/versions/master/install.html).
  * Clone the copybara source locally:
      ```
      $ git clone https://github.com/google/copybara.git
      ```
  * Go to that cloned directory:
      ```
      $ cd <clone dir>
      ```
  * Build:
      ```
      $ bazel build //java/com/google/copybara
      $ bazel build //java/com/google/copybara:copybara_deploy.jar
      ```
  * Tests: if you want to ensure you are not using a broken version!!
      ```
      $ bazel test //...
      ```

## Run it manually

```shell
$ cd bazel-bin/java/com/google/copybara

$ java -jar copybara_deploy.jar /Users/matthew.vandergrift.ibm.com/source/open/copybara/copy.bara.sky --force --init-history

$ java -jar copybara_deploy.jar /Users/matthew.vandergrift.ibm.com/source/open/copybara/copy.bara.sky --force --init-history --nosmart-prune
```

## Using Docker to build and run Copybara

```
$ docker build --rm -t copybara .
```

### Once that has finished building you can run:
```
$ docker run -it -v "$(pwd)":/usr/src/app copybara copybara
```

#### A few environment variables exist to allow you to change how you run copybara:
* `COPYBARA_CONFIG=copy.bara.sky`
  * allows you to specify a path to a config file, defaults to root `copy.bara.sky`
* `COPYBARA_SUBCOMMAND=migrate`
  * allows you to change the command run, defaults to `migrate`
* `COPYBARA_OPTIONS='--force --init-history --nosmart-prune'`
  * allows you to specify options for copybara, defaults to none
* `COPYBARA_WORKFLOW=default`
  * allows you to specify the workflow to run, defaults to `default`
* `COPYBARA_SOURCEREF=''`
  * allows you to specify the sourceref, defaults to none

```
docker run
       -e COPYBARA_CONFIG='other.config.sky'
       -e COPYBARA_SUBCOMMAND='validate'
       -v "$(pwd)":/usr/src/app
       -it copybara copybara
```

## Git Config and Credentials

There are a number of ways by which to share your git config and ssh credentials with the docker container, an example with OS X is below:

```
docker run
       -v ~/.ssh:/root/.ssh
       -v ~/.gitconfig:/root/.gitconfig
       -v "$(pwd)":/usr/src/app
       -it copybara copybara
```

## Optional tips

If you want to see the test errors in Bazel, instead of having to cat the logs, add this line to your 

  ```
  $ ~/.bazelrc: *test --test_output=streamed*
  ```

## Documentation

  * [Reference documentation](docs/reference.md)
  * [Examples](docs/examples.md)
  