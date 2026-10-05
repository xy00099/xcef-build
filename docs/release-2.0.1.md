# XCef 2.0.1 Windows x64 release preparation

The XCef source commit is `b864636eff17993c2e6cf1abaa8ecb3c97cd42c6`,
tagged and pushed as `v2.0.1` in `x-cef/XCef`. This patch retains C ABI 2
and CEF `151.3.24+g2384915+chromium-151.0.7922.174`.

The Windows x64 Release build using VS2022 and all 47 registered CTest
regressions passed locally. This includes real navigation/search, archive
failures, fault notifications, DevTools, Host disconnection, callback policy,
and core RPC/ownership tests. These results do not certify other platforms,
Chromium security patches, or downstream Unity release acceptance.

## Maintainer action

The requesting account can push the private source repository but currently has
READ access to this public build repository. Manual workflow dispatch returned
HTTP 403. This PR supplies reviewed source identity and exact inputs for a
maintainer with release/Actions access; merging it does not trigger publication.

1. Confirm that `x-cef/XCef` tag `v2.0.1` resolves to the source commit above.
   The annotated tag object's SHA is different from the dereferenced commit SHA.
   Confirm the source automation credential can read that tagged checkout.
2. In Actions, run **Build XCef release** (`build-release.yml`) with the inputs
   in [the release request](../config/release-requests/v2.0.1.json), or run:

   ```shell
   gh workflow run build-release.yml --repo x-cef/xcef-build -f source_tag=v2.0.1 -f 'cef_packages=["cef_binary_151.3.24+g2384915+chromium-151.0.7922.174_windows64.tar.bz2"]'
   ```

3. Review the new build and publication receipts. Verify that the final SDK
   includes complete supplier notices for CEF/Chromium, Boost, msgpack-cxx,
   spdlog and bundled fmt. The locally prepared SDK retained these texts from
   the actual build dependencies; they must also be present in the published
   artifact. This PR does not alter the build pipeline to add them.
4. Verify the public Release, catalog provenance, `.package` identity, archive
   size and SHA-256. Download the actual public ZIP without authentication and
   verify its inventory before changing consumer pins.

Expected SDK filename:

```text
xcef-2.0.1-151.3.24+g2384915+chromium-151.0.7922.174-windows-x64.zip
```

Local SDK preparation produced a ZIP with complete notices and SHA-256
`f5bffbc888dae62817435ad00d85e2fc7f3ea86e25e6966726bc8c8b23495bc8`.
That is a local artifact identity, **not a predicted cloud checksum or public
download proof**. A rebuild may produce different bytes. Consumers must pin the
verified published artifact, not the local checksum by assumption.

## Consumer handoff and limitations

UCefView still pins its previous local XCef 2.0.0 SDK. After public download
verification, update its URL, archive/file hashes, source identity, public headers,
bindings and license manifest together, then revalidate deployment and ABI.
Previous Unity candidate/runtime receipts do not automatically apply to 2.0.1.

The source release notes retain the unresolved exceptional shutdown/destructor
boundary and platform limitations. This patch is not a Chromium security upgrade,
a sandbox certification or Unity Asset Store approval. Preserve other published
SDK versions and their catalogs when publishing 2.0.1.
