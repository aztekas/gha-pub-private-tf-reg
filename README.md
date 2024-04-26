# gha-pub-private-tf-reg

## Github Action example workflow

```yaml
name: Release
on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  releaser:
    runs-on: ubuntu-latest
    steps:
        .....

      - name: Run GoReleaser
        uses: goreleaser/goreleaser-action@7ec5c2b0c6cdda6e8bbb49444bc797dd33d74dd8 # v5.0.0
        with:
          args: release --clean
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GPG_FINGERPRINT: ${{ steps.import_gpg.outputs.fingerprint }}

      - name: Publish Private registry
        uses: aztekas/gha-pub-private-tf-reg@v0.0.2
        with:
          tf_org_name: "org-name"
          tf_provider_name: "cleura"
          tf_provider_version: ${{ github.ref_name }} # Expects vX.X.X format
          tf_pub_key_id: ${{ secrets.TF_PUBKEY_ID }}
          tf_pub_key: ${{ secrets.TF_PUBKEY }}
          tf_api_token: ${{ secrets.TF_SECRET_TOKEN }}
          assets_path: "./dist"
          assets_prefix: "terraform-provider-cleura"
          force_create: "true"

```

> [!NOTE]
> assets_path is expected to contain(subdirs are checked recursively): zip archived platform files of the following format '{{ .ProjectName }}_{{ .Version }}_{{ .Os }}_{{ .Arch }}', SHA265 files - '{{ .ProjectName }}_{{ .Version }}_SHA256SUMS' and '{{ .ProjectName }}_{{ .Version }}_SHA256SUMS.sig'

Example goreleaser output:

```shell
      ....
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_darwin_amd64.zip name=terraform-provider-cleura_0.0.2_darwin_amd64.zip
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_linux_amd64.zip name=terraform-provider-cleura_0.0.2_linux_amd64.zip
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_darwin_arm64.zip name=terraform-provider-cleura_0.0.2_darwin_arm64.zip
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_windows_amd64.zip name=terraform-provider-cleura_0.0.2_windows_amd64.zip
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_SHA256SUMS name=terraform-provider-cleura_0.0.2_SHA256SUMS
      • uploading to release                         file=dist/terraform-provider-cleura_0.0.2_SHA256SUMS.sig name=terraform-provider-cleura_0.0.2_SHA256SUMS.sig
```

Refer to: <https://github.com/hashicorp/terraform-provider-scaffolding-framework/blob/main/.github/workflows/release.yml> for details regarding goreleaser.
