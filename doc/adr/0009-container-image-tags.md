# 9. Convention for tagging of container images

Date: 2020-03-13

## Status

Proposed

## Context

Libero's products are assembled from product specific, general libero and external container images.

Integration testing and reproducible deploys require uniquely identifiable image tags.

As a starting point for users earch product has an umbrella repo. This is not only used to collect product issues and documents. I also contains e.g. docker-compose files, helm charts that combine various components to a useable whole.


## Decision

### Tagging flow

- each container image of a Libero container shall be tagged with the git-sha of the build
- compose files/helm charts used for integration test shall use immutable image tags
- the compose files will track the component images and external images with the help of PR generating bots (e.g. dependabot, renovate)
- upon passed integration tests the used libero component images get tagged with the git-sha of the umbrella repo
- semver tagging of Libero component images is triggered by tagging of the umbrelle repo

### Tag Structure

Component build passes tests and gets pushed to registry:

- liberoadmin/reviewer-client:full_git_hash

Integration tests in umbrella repo succeed:

- liberoadmin/reviewer-client:reviewer-full_git_hash

Umbrella repo gets version tagged:

- liberoadmin/reviewer-client:reviewer-1.2.43
- liberoadmin/reviewer-client:reviewer-1.2
- liberoadmin/reviewer-client:reviewer-1

The {umbrella-product-name}-{sha|semver} scheme allows components to be reused by other Libero projects e.g. audit:reviewer-1.2.43 and audit:publisher-2.3.54 could refer to same image.

For external dependencies the tags should reflect their project's tagging scheme.
The bot should if possible support updating the pinned digest.

- node:8@sha256:552348163f074034ae75643c01e0ba301af936a898d778bb4fc16062917d0430

Likewise deployments of our images can use our semver plus digest pinning of reproducable deploys.

## Consequences

This scheme will be rolled out to all Libero products as capacity and roadmap allow.

Reviewer will be the first product to implement this and used to validate this proposal.
