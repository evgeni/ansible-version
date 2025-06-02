# Ansible version determination

Determine the Ansible versions for your testing matrix based on `requires_ansible` in `meta/runtime.yml`.

This action reads your runtime metadata and provides an output of compatible Ansible versions.

## Example

A minimal example has a single job to determine the Ansible version matrix, using this repository.
Then there should be some task that uses the matrix, but the exact steps likely differ.

```yaml
jobs:
  setup_matrix:
    name: "Determine Ansible versions"
    runs-on: ubuntu-latest
    outputs:
      ansible: ${{ steps.ansible.outputs.versions }}
    steps:
      - id: ansible
        uses: evgeni/ansible-version@v0

  sanity:
    name: Sanity (Ⓐ${{ matrix.ansible }})
    needs: setup_matrix
    strategy:
      matrix:
        ansible: ${{ fromJSON(needs.setup_matrix.outputs.ansible) }}
    runs-on: ubuntu-latest
    steps:
      - name: Perform sanity testing
        uses: ansible-community/ansible-test-gh-action@release/v1
        with:
          ansible-core-version: ${{ matrix.ansible }}
          testing-type: sanity
          pull-request-change-detection: false
```
