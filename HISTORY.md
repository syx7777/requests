Release History
===============

dev
---

- Removed the deprecated versionless `License :: OSI Approved :: Apache
  Software License` classifier now that the `license` field is a valid
  SPDX expression. This allows PEP 639-compatible SBOM tools to map the
  license to an SPDX identifier. (#7610)


2.34.2 (2026-05-14)
-------------------
- Moved `headers` input type back to `Mapping` to avoid invariance issues
  with `MutableMapping` and inferred dict types. Users calling
  `Request.headers.update()` may need to narrow typing in their code. (#7441)


2.34.1 (2026-05-13)
-------------------

**Bugfixes**
- Widened `json` input type from `dict` and `list` to `Mapping`
  and `Sequence`. (#7436)
- Changed `headers` input type to MutableMapping and removed `None` from
  `Request.headers` typing to improve handling for users. (#7431)
- `Response.reason` moved from `str | None` to `str` to improve handling
  for users. (#7437)
- Fixed a bug where some bodies with custom `__getattr__` implementations
  weren't being properly detected as Iterables. (#7433)


2.34.0 (2026-05-11)
-------------------

**Announcements**
- Requests 2.34.0 introduces inline types, replacing those provided by
  typeshed. Public API types should be fully compatible with mypy, pyright,
  and ty. We believe types are comprehensive but if you find issues, please
  report them to the pinned tracking issue.

  Special thanks to @bastimeyer, @cthoyt, @edgarrmondragon, and @srittau for
  helping review and test the types ahead of the release. (#7272)

**Improvements**
- Digest Auth hashing algorithms have added `usedforsecurity=False` to clarify
  security considerations. (#7310)
- Requests added support for Python 3.15 based on beta1. Downstream projects
  should be able to start testing prior to its release in October. (#7422)
- Requests added support for Python 3.14t. (#7419)

**Bugfixes**
- ``Response.history`` no longer contains a reference to itself, preventing
  accidental looping when traversing the history list. (#7328)
- Requests no longer performs greedy matching on no_proxy domains. The
  proxy_bypass implementation has been updated with CPython's fix from
  bpo-39057. (#7427)
- Requests no longer incorrectly strips duplicate leading slashes in
  URI paths. This should address user issues with specific presigned
  URLs. Note the full fix requires urllib3 2.7.0+. (#7315)


2.33.1 (2026-03-30)
-------------------

**Bugfixes**
- Fixed test cleanup for CVE-2026-25645 to avoid leaving unnecessary
  files in the tmp directory. (#7305)
- Fixed Content-Type header parsing for malformed values. (#7309)
- Improved error consistency for malformed header values. (#7308)

2.33.0 (2026-03-25)
-------------------

**Announcements**
- 📣 Requests is adding inline types. If you have a typed code base that
uses Requests, please take a look at #7271. Give it a try, and report
any gaps or feedback you may have in the issue. 📣

**Security**
- CVE-2026-25645 ``requests.utils.extract_zipped_paths`` now extracts
  contents to a non-deterministic location to prevent malicious file
  replacement. This does not affect default usage of Requests, only
  applications calling the utility function directly.

**Improvements**
- Migrated to a PEP 517 build system using setuptools. (#7012)

**Bugfixes**
- Fixed an issue where an empty netrc entry could cause
  malformed authentication to be applied to Requests on
  Python 3.11+. (#7205)

**Deprecations**
- Dropped support for Python 3.9 following its end of support. (#7196)

**Documentation**
- Various typo fixes and doc improvements.


2.32.5 (2025-08-18)
-------------------

**Bugfixes**

- The SSLContext caching feature originally introduced in 2.32.0 has created
  a new class of issues in Requests that have had negative impact across a number
  of use cases. The Requests team has decided to revert this feature as long term
  maintenance of it is proving to be unsustainable in its current iteration.

**Deprecations**
- Added support for Python 3.14.
- Dropped support for Python 3.8 following its end of support.

2.32.4 (2025-06-10)
-------------------

**Security**
- CVE-2024-47081 Fixed an issue where a maliciously crafted URL and trusted
  environment will retrieve credentials for the wrong hostname/machine from a
  netrc file.

**Improvements**
- Numerous documentation improvements

**Deprecations**
- Added support for pypy 3.11 for Linux and macOS.
- Dropped support for pypy 3.9 following its end of support.


2.32.3 (2024-05-29)
-------------------

**Bugfixes**
- Fixed bug breaking the ability to specify custom SSLContexts in sub-classes of
  HTTPAdapter. (#6716)
- Fixed issue where Requests started failing to run on Python versions compiled
  without the `ssl` module. (#6724)

2.32.2 (2024-05-21)
-------------------

**Deprecations**
- To provide a more stable migration for custom HTTPAdapters impacted
  by the CVE changes in 2.32.0, we've renamed `_get_connection` to
  a new public API, `get_connection_with_tls_context`. Existing custom
  HTTPAdapters will need to migrate their code to use this new API.
  `get_connection` is considered deprecated in all versions of Requests>=2.32.0.

  A minimal (2-line) example has been provided in the linked PR to ease
  migration, but we strongly urge users to evaluate if their custom adapter
  is subject to the same issue described in CVE-2024-35195. (#6710)

2.32.1 (2024-05-20)
-------------------

**Bugfixes**
- Add missing test certs to the sdist distributed on PyPI.


2.32.0 (2024-05-20)
-------------------

**Security**
- Fixed an issue where setting `verify=False` on the first request from a
  Session will cause subsequent requests to the _same origin_ to also ignore
  cert verification, regardless of the value of `verify`.
  (https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56)

**Improvements**
- `verify=True` now reuses a global SSLContext which should improve
  request time variance between first and subsequent requests. It should
  also minimize certificate load time on Windows systems when using a Python
  version built with OpenSSL 3.x. (#6667)
- Requests now supports optional use of character detection
  (`chardet` or `charset_normalizer`) when repackaged or vendored.
  This enables `pip` and other projects to minimize their vendoring
  surface area. The `Response.text()` and `apparent_encoding` APIs
  will default to `utf-8` if neither library is present. (#6702)

**Bugfixes**
- Fixed bug in length detection where emoji length was incorrectly
  calculated in the request content-length. (#6589)
- Fixed deserialization bug in JSONDecodeError. (#6629)
- Fixed bug where an extra leading `/` (path separator) could lead
  urllib3 to unnecessarily reparse the request URI. (#6644)

**Deprecations**

- Requests has officially added support for CPython 3.12 (#6503)
- Requests has officially added support for PyPy 3.9 and 3.10 (#6641)
- Requests has officially dropped support for CPython 3.7 (#6642)
- Requests has officially dropped support for PyPy 3.7 and 3.8 (#6641)

**Documentation**
- Various typo fixes and doc improvements.

**Packaging**
- Requests has started adopting some modern packaging practices.
  The source files for the projects (formerly `requests`) is now located
  in `src/requests` in the Requests sdist. (#6506)
- Starting in Requests 2.33.0, Requests will migrate to a PEP 517 build system
  using `hatchling`. This should not impact the average user, but extremely old
  versions of packaging utilities may have issues with the new packaging format.


2.31.0 (2023-05-22)
-------------------

**Security**
- Versions of Requests between v2.3.0 and v2.30.0 are vulnerable to potential
  forwarding of `Proxy-Authorization` headers to destination servers when
  following HTTPS redirects.

  When proxies are defined with user info (`https://user:pass@proxy:8080`), Requests
  will construct a `Proxy-Authorization` header that is attached to the request to
  authenticate with the proxy.

  In cases where Requests receives a redirect response, it previously reattached
  the `Proxy-Authorization` header incorrectly, resulting in the value being
  sent through the tunneled connection to the destination server. Users who rely on
  defining their proxy credentials in the URL are *strongly* encouraged to upgrade
  to Requests 2.31.0+ to prevent unintentional leakage and rotate their proxy
  credentials once the change has been fully deployed.

  Users who do not use a proxy or do not supply their proxy credentials through
  the user information portion of their proxy URL are not subject to this
  vulnerability.

  Full details can be read in our [Github Security Advisory](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q)
  and [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681).


2.30.0 (2023-05-03)
-------------------

**Dependencies**
- ⚠️ Added support for urllib3 2.0. ⚠️

  This may contain minor breaking changes so we advise careful testing and
  reviewing https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html
  prior to upgrading.

  Users who wish to stay on urllib3 1.x can pin to `urllib3<2`.

2.29.0 (2023-04-26)
-------------------

**Improvements**

- Requests now defers chunked requests to the urllib3 implementation to improve
  standardization. (#6226)
- Requests relaxes header component requirements to support bytes/str subclasses. (#6356)

2.28.2 (2023-01-12)
-------------------

**Dependencies**

- Requests now supports charset\_normalizer 3.x. (#6261)

**Bugfixes**

- Updated MissingSchema exception to suggest https scheme rather than http. (#6188)

2.28.1 (2022-06-29)
-------------------

**Improvements**

- Speed optimization in `iter_content` with transition to `yield from`. (#6170)

**Dependencies**

- Added support for chardet 5.0.0 (#6179)
- Added support for charset-normalizer 2.1.0 (#6169)

2.28.0 (2022-06-09)
-------------------

**Deprecations**

- ⚠️ Requests has officially dropped support for Python 2.7. ⚠️ (#6091)
- Requests has officially dropped support for Python 3.6 (including pypy3.6). (#6091)

**Improvements**

- Wrap JSON parsing issues in Request's JSONDecodeError for payloads without
  an encoding to make `json()` API consistent. (#6097)
- Parse header components consistently, raising an InvalidHeader error in
  all invalid cases. (#6154)
- Added provisional 3.11 support with current beta build. (#6155)
- Requests got a makeover and we decided to paint it black. (#6095)

**Bugfixes**

- Fixed bug where setting `CURL_CA_BUNDLE` to an empty string would disable
  cert verification. All Requests 2.x versions before 2.28.0 are affected. (#6074)
- Fixed urllib3 exception leak, wrapping `urllib3.exceptions.SSLError` with
  `requests.exceptions.SSLError` for `content` and `iter_content`. (#6057)
- Fixed issue where invalid Windows registry entries caused proxy resolution
  to raise an exception rather than ignoring the entry. (#6149)
- Fixed issue where entire payload could be included in the error message for
  JSONDecodeError. (#6036)

2.27.1 (2022-01-05)
-------------------

**Bugfixes**

- Fixed parsing issue that resulted in the `auth` component being
  dropped from proxy URLs. (#6028)

2.27.0 (2022-01-03)
-------------------

**Improvements**

- Officially added support for Python 3.10. (#5928)

- Added a `requests.exceptions.JSONDecodeError` to unify JSON exceptions between
  Python 2 and 3. This gets raised in the `response.json()` method, and is
  backwards compatible as it inherits from previously thrown exceptions.
  Can be caught from `requests.exceptions.RequestException` as well. (#5856)

- Improved error text for misnamed `InvalidSchema` and `MissingSchema`
  exceptions. This is a temporary fix until exceptions can be renamed
  (Schema->Scheme). (#6017)

- Improved proxy parsing for proxy URLs missing a scheme. This will address
  recent changes to `urlparse` in Python 3.9+. (#5917)

**Bugfixes**

- Fixed defect in `extract_zipped_paths` which could result in an infinite loop
  for some paths. (#5851)

- Fixed handling for `AttributeError` when calculating length of files obtained
  by `Tarfile.extractfile()`. (#5239)

-
...[已截断]
