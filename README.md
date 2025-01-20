# common-targets

Common set of commands to be used among projects in different languages/structures for the sake of sanity of the developer

If a project is using the practices defined in **common-targets**, you can check it out, open a shell terminal and run

```
# install the required development tools for the project
make prepare

# build the software
make build

# run all tests (unit and e2e/integration tests)
make test

# check code style and security findings
make lint

# deploy the software to dev environment
STAGE=dev make deploy

# delete any temporary files created during the build/test/lint/deploy phases
make clean
```

# Spec

## Problem

- Software projects have different folder and management structures
- This is even more complicated when you are dealing with different language or tooling ecossystems, making it difficult for new developers of a project to onboard, because they have to learn again and again how to setup their machine and run the software as they move from one project to the other.
- In best case scenario, those projects have good README or CONTRIBUTING files with good setup instructions, but still the diversity of commands to perform similar tasks among projects is still high.
- As each project/setup is different, the CI pipelines used on each software development is also very different

## Context

- On the other hand, we can see a lot of similarities in the basics of software development, regardless of language/tooling/ecossystem/complexity/platform
- Almost every software needs to be **built**, **tested**, **linted**, **deployed** in different environments and the developer sometimes needs extra setups on their laptops to start working on it
 
## Solution

- Standarlise a common set of commands that can be used by the developers in any project and by CI pipelines

## Proposal

### Command list

- "prepare"
  - installs (or checks) any tools required on the developer machine to build this software (such as nvm, brew, python, golang etc)
  - normally this operation is done once by the developer for an specific project

- "build"
  - install any dependencies of the modules
  - make any post setups required for the software to work
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence
 
- "lint"
  - performs code style checks, code/dependency security checks, project structure checks etc
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence
 
- "lint-fix"
  - performs automatic fixes according to formatting or linting rules, if possible
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence
 
- "test"
  - runs all required tests
  - internally might invoke test-unit and test-integration, for example.
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence

- "test-integration"
  - runs all required integration tests
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence

- "test-unit"
  - runs all required unit tests
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence

- "deploy"
  - publishes this software to an environment
  - if this is a reusable library, publishes it to the registry (such as pypi, npm, golang etc)
  - if this is a business software, provision it on the running environment (such as a cloud provider, target on-premisse server, desktop machine)
  - use ENV variable "STAGE" to define which stage this should be deployed to
    - For example:
      - for a library, you can use the hint from STAGE=dev to auto version and publish the library as "1.0.0-alpha", if it's "acc" to "1.0.0-rc" and "prd" to "1.0.0"
      - for a business softwares, you can use STAGE=tst to deploy to your test environment on the cloud, "acc" to acceptance and "prd" to production
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence

- "undeploy"
  - deactivates/uninstall/removes the software from an environment
  - useful for deleting temporary deployments such as PR environments
  - use ENV variable "STAGE" in the same manner as in "deploy" command

- "start"
  - runs the software locally
  - examples: runs the jupyter notebook used by data scientists, runs a NodeJS service exposing APIs locally during development, runs React server and opens the page on the browser in developer mode

- "clean"
  - cleans up any temporary or transient files created during build/lint/test so we can perform clean operations if needed
  - example: remove node_modules dirs in JS, delete virtual environments in Python, delete generated binaries in Golang, delete generated files in NextJS
  - used both by the developer on his/her machine during development and by automated CI pipelines to enhance consintence

### ENV variables

- "STAGE"
  - should have the format "[prefix][-variant?]"
  - commonly used prefixes are: "dev", "tst", "acc", "prd", but you can extend this
  - examples: "dev", "dev-pr123", "tst", "prd-blue", "prd-green"
  - this env var can be required to be set in the context of any command (for example, if you need the "STAGE" name when building a package, or wants to apply a different set of linting rules depending on the ENV that the package is being prepared)

#### Extensions

- You can add custom extra commands prefixed by the default names in order to achieve specific requirements, still making it intuitive for the developer
- examples:
  - "build-dev" - prepares a build for STAGE=dev
  - "test-smoke" - performs unit tests only on a few important files
  - "start-debugger" - opens a visual debugger on the browser

### Use of Makefiles

In order to have a common entry point for commands regardless of the developer background, it's adviseable to use Makefiles. It means that:

- every project should have a Makefile in its root folder with the standard targets (build/lint/test/deploy etc)
- automated CI pipelines should invoke the makefile scripts in order to perform those tasks
- when a new developer checkouts the project, he/she can directly run "make build" in order to build the project, or "make test" to run unit tests

### Monorepos

When using common-targets in a monorepo you should scope the commands to different branches of folders. 
- Inside each module of the monorepo, you will have a Makefile with build/test/lint targets specific for the module itself, but you can have other Makefiles that groups modules on the monorepo if they are in a higher hirarchy folder.
- For example, if placing it on the root of the monorepo, "build" should build all modules on the monorepo (by invoking "make build" on each module). Check the /examples folder for more details.

## Examples

TODO

Check [/examples](/examples) folder for more
