# Engineering Philosophy

## Architecture

> The battle against complexity in web development is a constant tug of war. We give a little to get something new, we take it back to make it simpler.
> Progress is good. Complexity is a bridge. Simplicity is the destination.

I love this [quote from DHH](https://world.hey.com/dhh/introducing-propshaft-ee60f4f6). For those unfamiliar, DHH is the founder of Ruby's Rails framework, and is a huge proponent of the "mighty monolith." While I disagree with his ideal architecture, I very much subscribe to keeping things simple. In a similar vein, knowing when to buy something vs build it ourselves is an important part of our jobs. There's no need to be continually reinventing the wheel.

- [Kubernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/) aids in automating deployment, scaling, and managing containerized [12-factor applications](https://12factor.net/). Deployed in the cloud, scaling with the ebb and flow of traffic is possible.
- [Service meshes](https://buoyant.io/service-mesh-manifesto) allow common application code for service-to-service communication to be shifted left and deployed independently from applications - traffic management, observability, and security.
- [Schemas](https://protobuf.dev/) allow APIs to be built before a single line of code is written, often allowing client and server code to be programmatically generated.
- [Federated GraphQL](https://www.apollographql.com/docs/federation/) helps in consolidating multiple GraphQL schemas into a single schema, facilitating better API organization and microservice communication. This is great for keeping an API unidirectional.

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

Let's not argue about style... there are linters provided for every language that cover anything from syntax to formatting to deprecations to complexity. Make sure our editor is setup to use those linters, or get yelled at by the hooks before we can commit our changes (or worse, CI fails).

## Continuous Integration

Let's protect ourselves from... ourselves. CI enables us to do things like:

- verify our tests are passing
- check our code against linters
- static code analysis
- build various artifacts
- keep our dependencies current

...all without really having to know much about any given repo. Integration failures can be configured to be quite verbose, with clear steps to rectify any errors.

GitHub Actions is a wonderful solution. Here are some reusable [workflows](https://github.com/dudo/dudo/tree/main/.github/workflows), and some [templates](https://github.com/dudo/.github/tree/main/workflow-templates) for adding Actions to a repo.

## Continuous Deployment

Embracing GitOps, tools like [Flux](https://www.weave.works/oss/flux/) and [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) enable pull-based deployments straight to Kubernetes. This keeps git as our single source of truth, and operators within the K8s cluster tirelessly ensure that their state is mirrored with git state, automating the hop from merged code to deployed software... continuously. These tools simplify the journey from code to cloud, embodying the KISS mantra while efficiently navigating the complexities of CD.

## Provisioning Resources

GitOps extends its "single source of truth" philosophy into our infrastructure, ensuring that our declarations in git are faithfully manifested in reality.

- [Terraform](https://www.terraform.io/) has been the defacto infrastructure as code (IaC) tool (though that'll probably change with the recent BSL). Most cloud providers have their own tooling, which, let's be honest - are we really going multi-cloud?
- Secrets are tucked away in [Vault](https://www.vaultproject.io/) or the cloud provider's secret manager, then pulled back into the cluster via [external-secrets-operator](https://external-secrets.io/latest/).

## Security

Security is like an onion...

- Starting with code, employ static analysis tools to catch vulnerabilities before they reach production. CI should be running a suite of security checks with every commit.
- Prefer hardened, minimal base images and employ network policies to create a fortress around services. Terraform isn’t just for provisioning; it’s for crafting a secure foundation.
- At runtime, embrace a defense-in-depth strategy. [Monitoring, logging, and alerting systems](https://panther.com/) keep us informed, while an incident response plan ensures we're ready to act when necessary.
- Continuous education and adherence to best practices like regular patching and updates, ensure we're always a step ahead.

## Observability

So... your PR was merged. Congratulations! An image was built, tagged, and pushed up to the container registry. An operator within K8s has presumably detected the new tag, pulled the image, and started a canary rollout. Incoming requests are gradually diverted to new pods. Your changes are taking traffic. Now what?

If you can afford [Datadog](https://docs.datadoghq.com/tracing/), go with them - they're the best (and they know it $$$). [Grafana's LGTM stack](https://grafana.com/) is pretty great for self-managed setups. Regardless of the tool, microservices necessitate advanced observability, and distributed tracing is a must have. Logs and metrics still serve their purpose, even though there are companies like [Observe](https://www.observeinc.com/) and [Honeycomb](https://docs.honeycomb.io/concepts/events-metrics-logs/) are embracing events.

Always remember to go [SLO](https://cloud.google.com/blog/products/devops-sre/sre-fundamentals-slis-slas-and-slos) to go fast!
