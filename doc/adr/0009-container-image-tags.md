# 9. Convention for tagging of container images

Date: 2020-03-13

## Status

Proposed

## Context

Libero's products are assembled from product specific, general libero and external container images.

Integration testing and reproducible deploys require uniquely identifiable image tags.

As a starting point for users each product has an umbrella repo. This is not only used to collect product issues and documents. I also contains e.g. docker-compose files, helm charts that combine various components to a useable whole.

Container images always have a digest hash that uniquely identifies their build and is independent from git hashes, git tags and image tags.

The image associated with an image tag is allowed to change. This is useful for tracking semver image tags in deploys, but results in the image digest being required for pinning etc.

This ADR does not define how the image tags are used for integration tests or deploys. Nor does it define the consequences of their test results. But by defining a tagging convention is allows us to develop these strategies.

## Decision

### Tagging 

- libero container images shall __always__ be tagged with both:
  - `${branch}-${short_git_hash}-${timestamp}`
  - `${branch}-${short_git_hash}`
- `timestamp` format is `%Y%m%d.%H%M`
- images shall receive semver tags when the git repo receives them
- semver tagging edits/creates three tags i.e.: 
  - image gets tagged `1.2.43`,  `1.2` and `1`  
  - images previously associated with major/minor tag loose this tag
  - (see example below) 
- git tags shall consists of the version preceeded by a `v`  
  e.g. `refs/tags/v1.2.43`
- only images from `master` branch shall be pushed as `latest`

Examples:

```sh
liberoadmin/reviewer-client:master-${short_git_hash}-${timestamp}
liberoadmin/reviewer-client:master-${short_git_hash}
liberoadmin/reviewer-client:develop-${short_git_hash}-${timestamp}
liberoadmin/reviewer-client:develop-${short_git_hash}
liberoadmin/reviewer-client:1.2.43
```

Example Workflow:

1. head of `libero/reviewer-client` `master` moves  
   the hash is `2d57c085`
   1. new container image gets built
   2. image gets pushed to registry as  
      `liberoadmin/reviewer-client:master-2d57c085-20200512.0834` __and__  
      `liberoadmin/reviewer-client:master-2d57c085`  
      its short digest is: `dd75d2e1bbfc`
   3. same image gets tagged and pushed as `latest`
2. `master` gets tagged as `v1.2.43`
3. image `dd75d2e1bbfc` receives additional tags
   - `liberoadmin/reviewer-client:1.2.43`
   - `liberoadmin/reviewer-client:1.2`
   - `liberoadmin/reviewer-client:1`

## Consequences

This scheme will be rolled out to all Libero products as capacity and roadmap allow.

Reviewer will be the first product to implement this and used to validate this proposal.

Libero deploys and integration tests can track image changes per branch or semver, no longer relying on `latest` for continuous deploys.

Teams are encouraged to adopt automated [semver](https://semver.org/) tagging of their repo to ensure compliance with semver. Various tools that utilize [conventional commits](https://conventionalcommits.org/) for this exist.
