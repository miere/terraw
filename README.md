# Terraform Wrapper
A tool designed to make your life easier when using terraform to maintain your infrastructure.

## Basic Usage
`terraw` was designed to provide a simplified syntax by infeering most common parameters from your
project, helping you to avoid mistakes when you are moving commands from your development environment to
your CI/CD tool.

```shell
# If you want to initialize a Terraform working directory
$ ./terraw init
# Or if you want to update local state file against real resources
$ ./terraw refresh
# When you want to generate, show and automatically apply an execution plan
$ ./terraw apply
# And, when you just manually fixed something in the UI and want it reflected in you current state
$ ./terraw import aws_iam_policy.autoscaling arn:aws:iam::123456789012:policy/AutoScaling
```
Usually, you don't have to pass parameters. Under the hood, `terraw` will follow simple conventions
to infeer where your source code is placed, which externally defined values should be used as input
for your variables, and which backend you intent to use to store your `terraform.state` file.

The basic syntax is: `./terraw <command> <options>`, where:
- `command` is one of following known commands that you can also find in `terraform`: apply, import,
init, refresh and destroy.
- for each `command` you can use any `terraform` valid option as `options`, except by `-auto-approve`,
`-backend-config`, `-var-file` and the _source directory_ once they are automatically computed by
`terraw` and passed to `terraform`.  

## Installation
`TODO`

## Using terraw
`terraw` was designed to enforce most of terraform best practices on your projects. When you adopt then
you'll notice significat increase on your DevOps team's performance. To start, let's take a look into
the project structure that `terraw` expects in order to work properly.

```
- terraw
- terraw.conf
- source/
   - main.tf
   - variables.tf
   - output.tf
   ...
- env-staging.tfvars
- env-staging-backend.tfvars
- env-production.tfvars
- env-production-backend.tfvars
```

### The terraw.conf file
The `terraw.conf` file is an optional file the you can place along with `terraw` in order to customize
the default behavior, specially default variables and conventionts. Once `terraw` is a simple bash script,
`terraw.conf` should be a valid bash script as well. Bellow a simple `terraw.conf` that overwrites the
default `tfvars` file expected by `terraw`.

```shell
TF_VARS="custom.tfvars"
```

### The source folder
As we can see it expects a root folder called `source` which will contain you `tf` files. Indeed, that's
the only required item from the above structure, as long as you put at least one `tf` file into it. When
you have a big structure to define, or your build plan require a long set of input or output variables,
it is recommended that you create a separated file for that propose (e.g. variables.tf and output.tf).

You can use a different source directory by defining the variable `TF_SRCDIR` in the `terraw.conf` file.

### Working with multiple deployment environments
We can also notice that there a few `tfvars` in the root folder of the above structure. Terraform created
`tfvars` files in order to provide an easy way to define variables, making it easy to provide different
values for different deployment environments.

Whenever you execute the goals `destroy`, `apply`, `refresh` or `import`, `terraw` will pick the file which respects
the convention `env-${ENV}.tfvars`. By default, `ENV` is set to `staging`. You can overwrite this value
by setting a new one while executing `terraw`:

```shell
$ ENV=production ./terraw init
$ ENV=production ./terraw apply
```

You can also define your own logic to choose the `tfvars` file by creating a custom `terraw.conf` file.

```shell
# terraw.conf

case "$ENV" in
  dev|DEV|staging|test) TF_VARS="staging.tfvars"
  *) TF_VARS="production.tfvars"
esac
```

### Defining an external backend configuration
`tfvars` files can be used to define external backend configuration variables. By default, whenever you
execute the goal `init`, `terraw` will pick the file which respects the convention `env-${ENV}-backend.tfvars`.

## Reporting Bugs/Feature Requests

We welcome you to use the GitHub issue tracker to report bugs or suggest features.

When filing an issue, please check existing open, or recently closed, issues to make sure somebody else hasn't already 
reported the issue. Please try to include as much information as you can. Details like these are incredibly useful:

* A reproducible test case or series of steps
* The version of our code being used
* Any modifications you've made relevant to the bug
* Anything unusual about your environment or deployment


## Contributing via Pull Requests
Contributions via pull requests are much appreciated. Before sending us a pull request, please ensure that:

1. You are working against the latest source on the *master* branch.
2. You check existing open, and recently merged, pull requests to make sure someone else hasn't addressed the problem already.
3. You open an issue to discuss any significant work - we would hate for your time to be wasted.

To send us a pull request, please:

1. Fork the repository.
2. Modify the source; please focus on the specific change you are contributing. If you also reformat all the code, it will be hard for us to focus on your change.
3. Ensure local tests pass.
4. Commit to your fork using clear commit messages.
5. Send us a pull request, answering any default questions in the pull request interface.
6. Pay attention to any automated CI failures reported in the pull request, and stay involved in the conversation.

GitHub provides additional document on [forking a repository](https://help.github.com/articles/fork-a-repo/) and 
[creating a pull request](https://help.github.com/articles/creating-a-pull-request/).


## Finding contributions to work on
Looking at the existing issues is a great way to find something to contribute on. As our projects, by default, use the default GitHub issue labels ((enhancement/bug/duplicate/help wanted/invalid/question/wontfix), looking at any 'help wanted' issues is a great place to start. 

## License
`terraw` is just a wrapper and is licenced under the Apache License 2 terms.

