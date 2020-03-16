# 9. Convention for tagging of container images

Date: 2020-03-13

## Status

Proposed

## Context

Libero's products are assembled from product specific, general libero and external container images.

Integration testing and reproducible deploys require uniquely identifiable image tags.

As a starting point for users each product has an umbrella repo. This is not only used to collect product issues and documents. I also contains e.g. docker-compose files, helm charts that combine various components to a useable whole.


## Decision

### Tagging 

- libero container images shall always be tagged with {branch-full_git_hash}
- images shall receive semver tags when the git repo receives them
- a semver tagging event bumps its parent tags

Examples:

- liberoadmin/reviewer-client:master-full_git_hash
- liberoadmin/reviewer-client:dev-full_git_hash
- liberoadmin/reviewer-client:1.2.43

Example Workflow:

1. head of `libero-reviewer` `master` moves  
   the hash is `2d57c085e31cad0c81bd2a7db8dfe28d93aec99b`
   1. new container image gets built
   2. image gets pushed to registry as  
      `liberoadmin/reviewer-client:master-2d57c085e31cad0c81bd2a7db8dfe28d93aec99b`  
      its short digest is: `dd75d2e1bbfc`
2. head gets tagged as `v1.2.43`
3. image `dd75d2e1bbfc` receives additional tags
   - `liberoadmin/reviewer-client:1.2.43`
   - `liberoadmin/reviewer-client:1.2`
   - `liberoadmin/reviewer-client:1`

## Consequences

This scheme will be rolled out to all Libero products as capacity and roadmap allow.

Reviewer will be the first product to implement this and used to validate this proposal.

Libero deploys can track image changes per branch or semver, no longer relying on `latest` for continuous deploys.
