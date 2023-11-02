# Engineering Philosophy

## Architecture

> The battle against complexity in web development is a constant tug of war. We give a little to get something new, we take it back to make it simpler.
> Progress is good. Complexity is a bridge. Simplicity is the destination.

I love this [quote from DHH](https://world.hey.com/dhh/introducing-propshaft-ee60f4f6). For those unfamiliar, DHH is the founder of Ruby's Rails framework, and is a huge proponent of the "mighty monolith." While I admire DHH's stance on simplicity, we diverge on architectural ideals. Modern tooling streamlines microservices management:

- [Container Orchestration](https://kubernetes.io/docs/tutorials/kubernetes-basics/) in the cloud automates deployment and scaling of [12-factor apps](https://12factor.net/).
- [Service meshes](https://buoyant.io/service-mesh-manifesto) externalize common code for inter-service communication. This enhances traffic management and security.
- [Schemas](https://protobuf.dev/) predefine APIs, often automating client and server code generation.
- [Federated GraphQL](https://www.apollographql.com/docs/federation/) consolidates GraphQL schemas, optimizing API organization and microservice communication.
- [Events](https://en.wikipedia.org/wiki/Event-driven_architecture) foster service independence, boosting scalability and resiliency via asynchronous communication.

Tangentially, an essential part of our job is knowing when to buy versus build. When services are small and have a singular purpose, there's often a SAAS solution. There's no sense in reinventing the wheel.

## Development Environment

Containerization changed everything. Long ago are the days of needing a version manager for every language you’re working with. [asdf](https://asdf-vm.com) solves part of the problem, but [docker](https://docs.docker.com/engine/reference/builder/#from) renders most version tooling obsolete. [Docker compose](https://docs.docker.com/compose/compose-file/03-compose-file/) makes stitching apps, data stores, and tooling together painless. In other words, setting up our local environment is as easy as setting up [docker](https://docs.docker.com/desktop/)...

[Configure docker compose files](https://gist.github.com/dudo/96cd32821e78385c88560b50b7a12a4d) with tooling to run any language or framework commands. I prefer them to Makefiles.

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

## Style

Let’s keep code styling consistent with linters. There is community tooling for all languages, so be sure to configure your editor. 

## Continuous Integration

Let's protect ourselves from... ourselves. CI enables us to do things like:

- verify our tests are passing
- check our code against linters
- static code analysis
- build various artifacts
- keep our dependencies current

...all without having to know much about any given repo. Configure integration failures to be verbose, with clear steps to rectify any errors.

GitHub Actions is a wonderful solution. Use [reusable workflows](https://github.com/dudo/dudo/tree/main/.github/workflows) and [templates](https://github.com/dudo/.github/tree/main/workflow-templates) to add Actions to a repo.

## Continuous Deployment

Embracing GitOps, tools like [Flux](https://www.weave.works/oss/flux/) and [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) enable pull-based deployments straight to Kubernetes. This keeps git as our single source of truth! Operators within the Kubernetes cluster align their state with the source code, minimizing the system's attack surface and guaranteeing the capture of all changes.

## Resource Provisioning 

GitOps also centralizes infrastructure declarations. While [Terraform](https://www.terraform.io/) is a go-to, cloud providers offer their alternatives.

## Security

Security is like an onion...

- Starting with code, employ static analysis tools to catch vulnerabilities before they reach production. CI should be running a suite of security checks with every commit.
- Prefer hardened, minimal base images and employ network policies to create a fortress around services. Terraform isn’t just for provisioning; it’s for crafting a secure foundation.
- At runtime, utilize a defense-in-depth strategy. [Monitoring, logging, and alerting systems](https://panther.com/) keep us informed, while an incident response plan ensures we're ready to act when necessary.
- Ensure we're always a step ahead. Embrace continuous education and adherence to best practices like regular patching and updates.

## Observability

So... your PR was merged. Congratulations! An image was built, tagged, and pushed up to the container registry. An operator within Kubernetes has presumably detected the new tag, pulled the image, and started a canary rollout. Incoming requests are gradually diverted to new pods. Your changes are taking traffic. Now what?

If you can afford [Datadog](https://docs.datadoghq.com/tracing/), go with them as they're best-in-class. [Grafana's LGTM stack](https://grafana.com/) is excellent for self-managed setups. Regardless of the tool, microservices necessitate advanced observability, and distributed tracing is necessary. Logs and metrics still have their utility, but some companies have transitioned to event-based systems.

Last, but not least, always remember to go [SLO](https://cloud.google.com/blog/products/devops-sre/sre-fundamentals-slis-slas-and-slos) to go fast!

---

This README has been optimized for accessibility based on GitHub's blogpost "[Tips for Making your GitHub Profile Page Accessible](https://github.blog/2023-10-26-5-tips-for-making-your-github-profile-page-accessible)".
