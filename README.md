# action-git-sha

NOTE: this code is not included in the application monorepo because accessing actions in the monoorepo requires repo checkout and this code is used to determine which sha of the repo we wish to checkout. placing this code in the repo would required each workflow to perform extra inefficient checkout actions

receive as input a git ref (branch, tag or sha)

```yaml
inputs:
  ref:
    default: main
    description: The branch, tag or SHA to make into a Docker image
    required: true
```

return as output the input ref and two sha values

```yaml
outputs:
  default-sha:
    description: commit sha for the default branch
    value: ${{ steps.default-sha.outputs.default-sha }}
  ref:
    description: The branch, tag or SHA
    value: ${{ inputs.ref }}
  sha:
    description: commit sha for the provided ref
    value: ${{ steps.sha.outputs.sha }}
```

## usage

reference this action within a workflow

```yaml
name: Calling Workflow

on:
  workflow_dispatch:
    inputs:
      ref:
        description: The branch, tag or SHA to make into a Docker image.
        required: true
        default: main

jobs:
  git-sha:
    name: Git SHA
    outputs:
      ref: ${{ steps.git-sha.outputs.ref }}
      sha: ${{ steps.git-sha.outputs.sha }}
      default-sha: ${{ steps.git-sha.outputs.default-sha }}
    runs-on: ubuntu-latest
    steps:
      - id: git-sha
        uses: palolotech/action-git-sha@0.1.0
        with:
          ref: ${{ inputs.ref }}

  debug:
    runs-on: ubuntu-latest
    needs:
      - git-sha
    steps:
      - name: Persist Outputs
        run: |
          {
          echo "ref=${{ needs.git-sha.outputs.ref }}"
          echo "default-sha=${{ needs.git-sha.outputs.default-sha }}"
          echo "sha=${{ needs.git-sha.outputs.sha }}"
          } > $GITHUB_OUTPUT
          cat $GITHUB_OUTPUT
```
