Submitting a Pull Request (PR)
==============================

This guide is based on my experience with the [community.general](https://github.com/ansible-collections/community.general) collection. The procedures used here are likely to be the same or very similar in other collections in the `ansible-collections` space, but keep an eye on possible differences.

## First Time Setup
* Fork the communty.general repository in github
* Clone your forked repository `git clone <forked-repo-git-url>`
* Add the upstream remote to your local copy: `git remote add upstream <original-git-url>`

### Updating the local copy
Assuming the main branch of the repository is named `main` (other commonly found names are `master` and `devel`):
* Change the current work branch to `main`: `git checkout main`
* Update the `main` branch from the origin copy (forked repo) first: `git pull origin main`
* Fetch the updates from the origin repo: `git fetch upstream`
* Merge the changes from the upstream repo: `git merge upstream/main`
* (Optional) Push the resulting branch to origin: `git push origin main`

## Changing the Code
* In your local copy (clone), create a new branch, possibly with the command: `git checkout -b java_cert_password_fix` (or whatever branch name you prefer)
* Make the changes in the code

### Running Tests
* Test it using `ansible-test` or (naked self-promotion) a python package called `andebox`

### Adding Tests

## Creating the Pull Request

After the changes made in the local copy are satisfactory (make sure to run the tests to ensure that), you should be ready to create a Pull Request (PR).

* Push the branch to origin, possibly with the command: `git push --set-upstream origin java_cert_password_fix`
* The `git push` output will suggest you create a Pull Request using a specific link. Do that and create the PR in the github website.
* Proceed with the review - coding cycle until all stakeholders are happy. Keep in mind:
  * Small changes suggested in the PR can be commited right right there in the website. If you do that, remember to update your
    local copy by running `git pull` in the same branch used for the PR.
  * This may vary from repo to repo, but at least in the projects under the `ansible-collections` umbrella, all the commits in the PR
    are squashed into one big PR before merging. No need to `git pull --force` every time.
* If your PR shows a **Merge conflict**, that means someone else changed one or more of the files your PR intends to change, in a
  way that git is unable to merge them seamlessly. To go around that it is necessary to
  [rebase your branch](http://docs.ansible.com/ansible/devel/dev_guide/developing_rebasing.html).


## Creating the Changelog Fragment
* After the PR has been created (and generated an `<PR-url>` and a `<PR-number>`), in that same branch in your local copy, create a file
  `<PR-number>-java_cert_password_fix.yaml` under the directory `changelogs/fragments/` with the content like:
  ```yaml
  bug_fixes:
    - java_cert - fixed retrieval of certificates aliases (<PR-url>).
  ```

## Checking the CI Results
