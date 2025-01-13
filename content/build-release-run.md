## V. Build, release, run
### Strictly separate build and run stages

A [codebase](./codebase.md) is transformed into a (non-development) deploy through three stages:

* The *build stage* is a transform which converts a code repo into an executable bundle known as a *build*.  Using a version of the code at a commit specified by the deployment process, the build stage fetches vendors [dependencies](./dependencies.md) and compiles binaries and assets.
* The *release stage* takes the build produced by the build stage and combines it with the [config](./config.md) for the deployment target to produce an installation package.  The resulting *release* is installable into the execution environment. This process prompts or references sources to establish the environment variables to configure the execution environment.
* The *run stage* (also known as "runtime") runs the app in the execution environment, by launching some set of the app's [processes](./processes.md) against a selected release.

![Code becomes a build, which is combined with config to create a release.](/images/release.png)

**The twelve-factor app uses strict separation between the build, release, and run stages.**  For example, it is impossible to make changes to the code at runtime, since there is no way to propagate those changes back to the build stage.

Deployment tools typically offer release management tools, most notably the ability to roll back to a previous release.  For example, the [Capistrano](https://github.com/capistrano/capistrano/wiki) deployment tool stores releases in a subdirectory named `releases`, where the current release is a symlink to the current release directory.  Its `rollback` command makes it easy to quickly roll back to a previous release.

Every release should always have a unique release ID, such as a timestamp of the release (such as `2011-04-06-20:32:17`) or an incrementing number (such as `v100` or semver system).  Releases are an append-only ledger and a release cannot be mutated once it is created.  Any change must create a new release.

Builds are initiated by the app's developers whenever new code is deployed.  Runtime execution, by contrast, can happen automatically in cases such as a server reboot, a crashed process being restarted by the process manager, or an update to runtime configuration.  Therefore, the `run stage` should be kept to as few options possible since problems what prevents an app from running can occur in the middle of the night when no developers are on hand.  Correspondingly, the build stage can be more complex since errors are always in the foreground for a developer who is driving the deploy.

