# Dudo's Engineering Philosophy

## Architecture

> The battle against complexity in web development is a constant tug of war. We give a little to get something new, we take it back to make it simpler.
> Progress is good. Complexity is a bridge. Simplicity is the destination.

I love this [quote from DHH](https://world.hey.com/dhh/introducing-propshaft-ee60f4f6). For those unfamiliar, DHH is the founder of Ruby's Rails framework, and is a huge proponent of the "mighty monolith." While I disagree with his ideal architecture, I very much subscribe to keeping things simple. In a similar vein, knowing when to buy something vs build it ourselves is an important part of our jobs. There's no need to be continually reinventing the wheel.

- [Kubernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/) aids in automating deployment, scaling, and managing containerized [12-factor applications](https://12factor.net/).
- [Federated GraphQL](https://www.apollographql.com/docs/federation/) helps in consolidating multiple GraphQL schemas into a single schema, facilitating better API organization and microservice communication.
- [Service meshes](https://buoyant.io/service-mesh-manifesto) allow common application code for service-to-service communication to be shifted left and deployed independently from applications - traffic management, observability, and security.
- [Schemas](https://protobuf.dev/) allow APIs to be built before a single line of code is written, often allowing client and server code to be programatically generated.

## Development Environment

Containerization changed everything. Long ago are the days of needing a version manager for every language you’re tinkering with, and trying to remember the syntax of each one. [asdf](https://asdf-vm.com) solves part of the problem, but [docker FROM](https://docs.docker.com/engine/reference/builder/#from) renders most version tooling obsolete. Pick a language and version, build up a Dockerfile, and now an image is checked into source code, and used in every environment! [Compose](https://docs.docker.com/compose/compose-file/03-compose-file/) makes stitching apps, data stores, and tooling together painless.

All that is to say that setting up our local environment is as easy as setting up [docker](https://docs.docker.com/desktop/)...

[Compose files](https://gist.github.com/dudo/96cd32821e78385c88560b50b7a12a4d) can be configured with tooling to run any language/framework commands. I prefer them to Makefiles.

```sh
# pro-tip
alias dr='docker compose run --rm '

dr npm install left-pad
dr npm update

dr go get -d golang.org/x/net
dr go mod tidy

dr bundle exec rails specs
dr bundle exec rails g model thing
dr bundle exec rails db:setup
```

Spinning up a local server typically consists of:

```sh
cp .env.sample .env
docker compose up -d
```

## Pre-commit Hooks

Let's not argue about style... there are linters provided for most languages that cover everything from syntax to formatting to deprecations to complexity. Make sure our editor is setup to use those linters, or get yelled at by the hooks before we can commit our changes (or worse, CI fails).

## Continuous Integration

Let's protect ourselves from... ourselves. CI enables us to do things like:

- verify our tests are passing
- check our code against linters
- static code analysis
- build various artifacts
- keep our dependencies current

...all without really having to know much about any given repo. Integration failures can be configured to be quite verbose, with clear steps to rectify any errors.

GitHub Actions is a wonderful solution. Some reusable workflows are kept in <https://github.com/dudo/dudo/tree/main/.github/workflows>, and some templates for adding Actions to a repo are available in <https://github.com/dudo/.github/tree/main/workflow-templates>.

## Continuous Deployment

GitOps. Flux pull-based. Kubernetes.

## Provisioning Resources

GitOps. Terraform. Databases. K8s cluster. Auto scaling k8s.

## Observability

So... your PR was merged. Congratulations! An image was built, tagged, and pushed up to the container registry. Kubernetes has presumably detected the new tag, pulled the image, and started scaling up replica sets. Incoming requests are gradually diverted to the new pods. Your changes are taking traffic. Now what?

Go SLO to go fast. Microservices necessitate advanced observability. Distributed tracing.
