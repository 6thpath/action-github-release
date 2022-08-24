<div align="center">
  :octocat:
</div>

<h1 align="center">
  Action Github Release
</h1>

<p align="center">
   A GitHub Action for creating GitHub Releases on Linux, Windows, and macOS virtual environments
</p>

<div align="center">
  <a href="https://github.com/6thpath/action-github-release/actions">
    <img src="https://github.com/6thpath/action-github-release/workflows/Publish/badge.svg"/>
 </a>
</div>

## Usage

### Quick example

Add step below to your `step` section in your Github action file

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      # ...other steps

      - name: Create Release
        uses: 6thpath/action-github-release@v1
```

### When to create release

You can use `step.if` or `push config tag filter` to achieve this

* `step.if` example usage:

  ```yaml
  on: push

  jobs:
    build:
      runs-on: ubuntu-latest

      steps:
        - name: Checkout
          uses: actions/checkout@v3

        - name: Create Release
          uses: 6thpath/action-github-release@v1
          if: startsWith(github.ref, 'refs/tags/')
  ```

* `push config tag filter` example usage:

  ```yaml
  on:
    push:
      tags:
        - "v*.*.*"

  jobs:
    build:
      runs-on: ubuntu-latest

      steps:
        - name: Checkout
          uses: actions/checkout@v3

        - name: Release
          uses: 6thpath/action-github-release@v1
  ```

### Customizing

#### inputs

The following are optional as `step.with` keys

| Name                       | Type    |                                                                  |
| -------------------------- | ------- | ---------------------------------------------------------------- |
| `body`                     | String  | Text communicating notable changes in this release               |
| `body_path`                | String  | Path to load text communicating notable changes in this release  |
| `draft`                    | Boolean | Indicator of whether or not this release is a draft              |
| `prerelease`               | Boolean | Indicator of whether or not is a prerelease                      |
| `files`                    | String  | Newline-delimited globs of paths to assets to upload for release |
| `name`                     | String  | Name of the release. defaults to tag name                        |
| `tag_name`                 | String  | Name of a tag. defaults to `github.ref`                          |
| `fail_on_unmatched_files`  | Boolean | Indicator of whether to fail if any of the `files` globs match nothing |
| `target_commitish`         | String  | Commitish value that determines where the Git tag is created from. Can be any branch or commit SHA. Defaults to repository default branch. |
| `token`                    | String  | Secret GitHub Personal Access Token. Defaults to `${{ github.token }}` |
| `discussion_category_name` | String  | If specified, a discussion of the specified category is created and linked to the release. The value must be a category that already exists in the repository. For more information, see ["Managing categories for discussions in your repository."](https://docs.github.com/en/discussions/managing-discussions-for-your-community/managing-categories-for-discussions-in-your-repository)           |
| `generate_release_notes`   | Boolean | Whether to automatically generate the name and body for this release. If name is specified, the specified name will be used; otherwise, a name will be automatically generated. If body is specified, the body will be pre-pended to the automatically generated notes. See the [GitHub docs for this feature](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes) for more information |
| `append_body`              | Boolean | Append to existing body instead of overwriting it                |

💡 When providing a `body` and `body_path` at the same time, `body_path` will be
attempted first, then falling back on `body` if the path can not be read from.

💡 When the release info keys (such as `name`, `body`, `draft`, `prerelease`, etc.)
are not explicitly set and there is already an existing release for the tag, the
release will retain its original info.

#### outputs

The following outputs can be accessed via `${{ steps.<step-id>.outputs }}` from this action

| Name         | Type   | Description                                                                                                                                                                               |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `url`        | String | Github.com URL for the release                                                                                                                                                            |
| `id`         | String | Release ID                                                                                                                                                                                |
| `upload_url` | String | URL for uploading assets to the release                                                                                                                                                   |
| `assets`     | String | JSON array containing information about each uploaded asset, in the format given [here](https://docs.github.com/en/rest/releases/assets#get-a-release-asset) (minus the `uploader` field) |

As an example, you can use `${{ fromJSON(steps.<step-id>.outputs.assets)[0].browser_download_url }}` to get the download URL of the first asset.

### Permissions

This Action requires the following permissions on the GitHub integration token:

```yaml
permissions:
  contents: write
```

When used with `discussion_category_name`, additional permission is needed:

```yaml
permissions:
  contents: write
  discussions: write
```

[GitHub token permissions](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token) can be set for an individual job, workflow, or for Actions as a whole.

This action is forked from [softprops/action-gh-release](https://github.com/softprops/action-gh-release) and updated by @6thpath
