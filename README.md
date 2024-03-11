# Test Flutter project

This project is a starting point for a Flutter application, basically 'Hello World' default Flutter sample project is used here. This project is created only for testing Flutter CI processes with GitHub actions. 

## Configured process description

Configured process includes four different types of GitHub Actions workflows:

1. **Pull Request(PR)** [pr_ci.yml](https://github.com/mbudaev7/hi_flutter/blob/main/.github/workflows/pr_ci.yml) workflow that triggers on each commit in PR created to `main` branch. 
  This workflow checks:
    * Install Java(for Gradle) and Flutter(Dart SDK) and Flutter dependencies (with `flutter pub get` command)
    * Dart language formatting (with `dart format` command)
    * Dart linter checks with failing build if we get 'info' level warnings (with `dart analyze` command)
    * Runs all Flutter test suites (with `flutter test` command)

    > Note. PR is not permitted to be merged if this workflow fails.

2. **Nightly build** [nightly_ci.yml](https://github.com/mbudaev7/hi_flutter/blob/main/.github/workflows/nightly_ci.yml) workflow that triggers each night at 1:05 AM UTC. This workflow run the same checks as previos one on current state of `main` branch. We need this to ensure our `main` is stable.

3. **Android Release** [release_ci.yml](https://github.com/mbudaev7/hi_flutter/blob/main/.github/workflows/release_ci.yml) workflow is triggered only for building and signing final Android release. To trigger the workflow you need create GitHub release (tag) and publish it (only published releases will trigger artifact build). 
  This workflow will do following:
    * Install Java(for Gradle) and Flutter(Dart SDK) and Flutter dependencies (with `flutter pub get` command)
    * Dart linter checks with failing build if we get 'info' level warnings (with `dart analyze` command)
    * Runs all Flutter test suites (with `flutter test` command)
    * Build Android application bundle (with `flutter build appbundle` command), it will also set appbundle version the same as Release tag
    * Sign Android application bundle with self-generated keystore
    * Publish built and signed Android application bundle to GitHub artifacts (will be available in workflow artifact section)
    * Deploy built and signed Android application bundle to Google Play Store (currently commented out)

4. **Web Release** [release_ci.yml](https://github.com/mbudaev7/hi_flutter/blob/main/.github/workflows/release_ci.yml) workflow running each time we do push to `main` branch. As repository have protection rules, basically we run it each time we merge PR. This worflow is required to show current web preview version of `main` branch after we make changes.   

  This workflow will do following:
    * Install Java(for Gradle) and Flutter(Dart SDK) and Flutter dependencies (with `flutter pub get` command)
    * Dart linter checks with failing build if we get 'info' level warnings (with `dart analyze` command)
    * Runs all Flutter test suites (with `flutter test` command)
    * Build Flutter application web version (with `flutter build web --release` command)
    * Publish Flutter application web version to GitHub artifacts (will be available in workflow artifact section)
    * Deploy published artifact to GitHub pages in the same repo (`gh-pages` branch) so it could be acessible on the URL [https://mbudaev7.github.io/hi_flutter/](https://mbudaev7.github.io/hi_flutter/)


## Expected development process points

* We are using simple [GitHub flow](https://docs.github.com/en/get-started/using-github/github-flow) with `main` branch and `feature/*` branches for each feature (for any devops related changes please use `devops/*` pattern).
* We create PR to merge changes to `main` branch.
* Developer should test changes locally in IDE (VS code Dart debug).
* Developer should create GitHub Release with new release tag (currently standard [semver](https://semver.org/) is used in the project) to create final signed Android aritfact and publish it to Google Play store. 
* Developer can check current Flutter web release state of `main` branch in GitHub pages [https://mbudaev7.github.io/hi_flutter/](https://mbudaev7.github.io/hi_flutter/) after PR is merged.

## Problems encountered and ongoing

The list of issues encountered during configuration:

1. Dart SDK version. 
   Issue was:
   
   ```The current Dart SDK version is 2.18.0. Because hi_flutter requires SDK version >=3.3.0 <4.0.0, version solving failed.```

   This was due to Flutter test application SDK version requirement was higher than default SDK we get when we install Flutter on GitHub agent (with step `subosito/flutter-action@v2`). Trying to install Dart SDK with separate step `dart-lang/setup-dart@v1` didn't solved the issue, so had to find Flutter-Dart version match table and just raise Flutter version from `3.3.0` to `3.19.2`.

2. GitHub pages deployment. 

### Ongoing problems

1. Cache flutter/Dart/Java SDK

## Documentation used

1. [Flutter documentation](https://docs.flutter.dev/)
2. [Flutter first project setup](https://codelabs.developers.google.com/codelabs/flutter-codelab-first#0)
3. [Flutter Android deployment guide](https://docs.flutter.dev/deployment/android)
4. [GitHub actions documentation](https://docs.github.com/en/actions/quickstart)