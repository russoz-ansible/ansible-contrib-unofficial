Submitting a Pull Request (PR)
==============================

## First Time Setup

## Changing the Code

## Testing the Code

### Add Tests

### Run Tests

## Creating the Pull Request

## Creating the Changelog Fragment

## Checking the CI Results

## Raw

The steps to achieve that would be, roughly:
* Fork the communty.general repository in github
* Clone your forked repository
* In your local copy (clone), create a new branch, possibly with the command: `git checkout -b java_cert_password_fix` (or whatever branch name you prefer)
* Make the change in the code
* Test it using `ansible-test` or (naked self-promotion) a python package called `andebox`
* Then you push it, possibly with the command: `git push --set-upstream origin java_cert_password_fix`
* The `git push` output will suggest you create a Pull Request using a specific link. Do that and create the PR in the github website.
* After the PR has been created (and generated an `<PR-url>` and a `<PR-number>`), in that same branch in your local copy, create a file 
  `<PR-number>-java_cert_password_fix.yaml` under the directory `changelogs/fragments/` with the content like:
  ```yaml
  bug_fixes:
    - java_cert - fixed retrieval of certificates aliases (<PR-url>).
  ```
