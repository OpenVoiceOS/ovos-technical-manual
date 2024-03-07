# OVOS Automations

## GitHub Automations

At OpenVoiceOS, we have adopted a set of GitHub automations to enhance the development and maintenance process across our repositories. While primarily designed for internal use, we highly encourage skill developers to adopt these automations, especially those related to skills.

The following document outlines the key automations we employ, focusing on human readability and providing real-world examples to facilitate understanding and implementation.

## Conventional Commits

### Overview

We are gradually incorporating [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) into our repositories. The goal is to establish a consistent commit message format that can be utilized to streamline continuous integration/deployment (CI/CD) processes and improve comprehension of commits for all involved parties.

#### Commit Types

Using specific prefixes in commit messages automates versioning and labeling of pull requests:

- `fix`: Addresses a bug in the codebase (correlates with **PATCH** in Semantic Versioning).
- `feat`: Introduces a new feature to the codebase (correlates with **MINOR** in Semantic Versioning).
- `BREAKING CHANGE`: Indicates a breaking API change, with the option to append a `!` or use a footer.
  
Example:
```plaintext
chore!: drop support for Node 6

BREAKING CHANGE: use JavaScript features not available in Node 6.
```

Other commit types include `build`, `chore`, `perf`, `refactor`, and `revert`. Some types, such as `ci`, `style`, `test`, and `docs`, do not trigger a release.

#### Scopes

Commit scopes provide context to the change, such as `config`, `parser`, `cli`, `gui`, `audio`, `tts`, `sst`, `nlp`, `plugins`, `localisation`, `resources`, and `release`.

### Premises

- `main` branch is stable, `dev` branch is for development, and `testing` branch is for testing.
- Commits without proper titles/messages will not trigger a release.
- Translations should be prefixed as `fix` or `feat` for a stable release.
- Pull requests and commits are for the `dev` branch; other branches are protected.

### Workflows/Actions

Visit the [`skill-template-repo`](https://github.com/OpenVoiceOS/skill-template-repo) for workflow implementations.

#### Release Handling (alpha/patch/minor/major versions)

The release workflow follows a 3-staged strategy:

1. Manually or automatically propose a testing start.
2. Manually conclude the testing phase and propose a stable release.
3. Publish the stable release on merge and feed back changes to the `dev` branch.

##### Start Release Mechanism

This workflow, triggered manually or automatically, initiates the release process.

Example:
```yaml
# Start release mechanism
name: Start release mechanism
on:
  workflow_dispatch:
    inputs:
      release_type:
        type: choice
        options:
          - "alpha"
          - "patch"
          - "minor"
          - "major"
  push:
    branches:
      - dev
    paths-ignore:
      - 'ovos_testpkg/version.py'
      - 'test/**'
      - 'examples/**'
      - '.github/**'
      - '.gitignore'
      - 'CHANGELOG.md'
      - 'MANIFEST.in'
      - 'scripts/**'

jobs:
  start_semver_release_mechanism:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/release_semver_start.yml@feat/shared_actions1
    with:
      branch: dev
      action_branch: feat/shared_actions1
      python_version: "3.10"
      version_file: "ovos_testpkg/version.py"
      locale_folder: ovos_testpkg/locale
      update_intentfile: test/unittests/test_intent.yaml
      release_type: ${{ inputs.release_type || null }}
      subject: ${{ github.event.head_commit.message || null }}
      kickoff_pr: true
```

##### Start Testing Phase

This workflow starts the testing phase, either immediately or delayed based on the merged pull request.

Example:
```yaml
# Start Testing Phase
on:
  pull_request:
    types: [ closed ]
    branches:
      - testing

jobs:
  start_testing_phase:
    if: github.actor != 'EggmanBot' && github.event.pull_request.merged == true && contains(fromJson('["patch release", "minor release", "major release"]'), github.event.pull_request.title)
    uses: openvoiceos/.github/.github/workflows/release_semver_versioning.yml@feat/shared_actions1
    secrets: inherit
    with:
      action_branch: feat/shared_actions1
      version_file: ovos_testpkg/version.py
      subject: ${{ github.event.pull_request.title }}
```

##### Conclude Testing Phase

After the testing phase, this workflow proposes a stable release.

Example:
```yaml
# Conclude testing phase
on:
  workflow_dispatch:

jobs:
  pull_to_master:
    uses: openvoiceos/.github/.github/workflows/release_semver_pull_master.yml@feat/shared_actions1
    secrets: inherit
    with:
      action_branch: shared_actions1
      python_version: "3.10"
```

##### Publishing Stable Release

This workflow automatically publishes a stable release upon merge.

Example:
```yaml
# Publish Stable Release
on:
  pull_request:
    types: [ closed ]
    branches:
      - master

jobs:
  publish_stable_release:
    if: github.actor != 'EggmanBot' && github.event.pull_request.merged == true && contains(fromJson('["patch release stable", "minor release stable", "major release stable"]'), github.event.pull_request.title)
    uses: openvoiceos/.github/.github/workflows/release_semver_publish.yml@feat/shared_actions1
    secrets: inherit
    with:
      action_branch: feat/shared_actions1
      python_version: "3.10"
      subject: ${{ github.event.pull_request.title }}
```

## Propose Translations

This action is used to introduce a new language localization by proposing a translation via pull request. 
The user can select a language from a predefined list or enter a language code.

Example:
```yaml
# Propose Translations
on:
  workflow_dispatch:
    inputs:
      translation:
        type: choice
        options:
          - "de-de"
          ...
          - "zh-cn"
      translations:
        type: string
        required: true

jobs:
  propose_translation:
    uses: openvoiceos/.github/.github/workflows/propose_translation.yml@main
    secrets: inherit
    with:
      branch: dev
      python_version: "3.8"
      language: ${{ inputs.translation }}
      locale_folder: ovos_core/locale/
      reviewers: "jarbasai,emphasize"
```

## License Testing

This automation validates the licenses of all packages (explicit and transitive).

Example:
```yaml
# License testing
on:
  <trigger strategy>

jobs:
  license_tests:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/license_tests.yml@main
    with:
      runner: ubuntu-latest
      branch: dev
      system_deps: "somepkg"
      pip_packages: "random

-pkg"
      python_version: "3.8"
      install_extras: test
      packages-exclude: '^(fann2|tqdm|bs4).*'
      licenses-exclude: '^(BSD-3).*$'
```

## Build Testing

Tests the build of the Python package using GitHub Actions. This includes installing system dependencies, Python packages, and testing against different Python versions.

Example:
```yaml
# Build testing
on:
  <trigger strategy>

jobs:
  build_tests:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/python_build_tests.yml@main
    with:
      runner: ubuntu-latest
      branch: dev
      system_deps: "libfann-dev libfann2"
      pip_packages: "pytest pytest-cov"
      python_matrix: '["3.8", "3.9", "3.10"]'
      test_manifest: true
      manifest_ignored: "test/** qt5/**"
      test_relative_paths: false
      test_pipaudit: true
      pipaudit_ignored: ""
```

## Unit Tests (File or Directory)

Runs unit tests for a specified file or directory. The example includes configurations for different Python versions, system dependencies, and optional features such as code coverage.

Example:
```yaml
# Unit Tests
on:
  <trigger strategy>

jobs:
  unit_tests:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/pytest_file_or_dir.yml@main
    with:
      runner: ubuntu-latest
      branch: dev
      action_branch: custom/branch
      timeout_minutes: 15
      system_deps: "libfann-dev libfann2"
      python_matrix: '["3.8", "3.9", "3.10"]'
      pip_packages: "pytest pytest-cov"
      pip_install_dirs: |
        relpath/to/package1
        relpath/to/package2
      install_extras: lgpl,mycroft
      test_location: test/unittests
      is_skill: true
      codecov: true
      upload_coverage: true
```

## Skills

Skills-related GitHub Actions for skill installation tests and skill resource tests.

- Skill Installation Tests: Tests the installation of a skill by specifying the skill ID and location.
  
- Skill Resource Tests: Tests the resources of a skill (e.g., dialogs, vocabs, regex, or intent resources) for completeness and workability.

  
### Skill Installation Tests

This automation tests the installation of a skill.

Example:
```yaml
# Skill Installation Tests
on:
  <trigger strategy>

jobs:
  skill_installation_tests:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/skill_test_installation.yml@main
    with:
      runner: ubuntu-latest
      branch: dev
      action_branch: custom/branch
      system_deps: "libfann-dev libfann2"
      python_matrix: '["3.8", "3.9", "3.10"]'
      pip_packages: "pytest pytest-cov"
      skill_id: "ovos-skill-x.openvoiceos"
      skill_location: "skill"
```

### Skill Resource Tests

This automation tests skill resources for completeness and functionality.

Example:
```yaml
# Skill Resource Tests
on:
  <trigger strategy>

jobs:
  skill_resource_tests:
    if: github.actor != 'EggmanBot'
    uses: openvoiceos/.github/.github/workflows/skill_test_resources.yml@main
    with:
      runner: ubuntu-latest
      timeout: 15
      branch: dev
      action_branch: custom/branch
      system_deps: "libfann-dev libfann2"
      python_matrix: '["3.8", "3.9", "3.10"]'
      pip_packages: "pytest pytest-cov"
      intent_testfile: test/test_intents.yaml
      test_padatious: true
      test_padacioso: true
```

## Notifications

### Notify Matrix on Pull Request

This automation notifies a Matrix chat room when a pull request is closed.

Example:
```yaml
# Notify Matrix on Pull Request
on:
  pull_request:
    types: [ closed ]

jobs:
  notify_pr_matrix:
    if: github.event.pull_request.merged == true
    secrets: inherit
    uses: openvoiceos/.github/.github/workflows/notify_pr_matrix.yml@main
    with:
      pr_id: ${{ github.event.number }}
```

These automations enhance our development process, providing clarity and efficiency. We encourage developers to explore and adopt them to streamline their workflows and contribute to the OpenVoiceOS community. For more details, refer to the implementation in our repositories.
