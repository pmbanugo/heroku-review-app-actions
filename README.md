# GitHub Action - Manage Heroku Review Apps

This GitHub action manages Heroku review apps for your software delivery pipeline. This was born out of the frustration of not being able to use review apps for a long time and there's no timeline when Heroku will have this sorted. See [status.heroku.com/incidents/2413](https://status.heroku.com/incidents/2413) for more info.

### Features

-   ⚙️ Focuses on automated preview deploy. You get the same automated preview deploy experience for your PRs.
-   🏎 Fast . No need to upload all your git info/metadata or history to Heroku.(coming soon - configure which files to omit from source code upload).
-   🧘🏽‍♀️ No fear of 3rd party having access to all your private GitHub data/repository.

## How To Use

Here's an example workflow that uses this action. This example workflow runs when you open (reopen), close, or push a new commit to your PR branch.

This action delete review apps when the associating PR is closed.

```yaml
on:
    pull_request:
        types: [opened, reopened, closed, synchronize]

jobs:
    automate-review-app:
        runs-on: ubuntu-latest
        name: A job to create and delete review a review app
        steps:
            - name: Checkout
              uses: actions/checkout@v3
            - name: Deploy Heroku app
              uses: pmbanugo/heroku-review-app-actions@v2.0.0 # Uses the action
              id: deploy
              with:
                  api-key: ${{ secrets.HEROKU_API_KEY }}
                  pipeline-id: ${{ secrets.HEROKU_PIPELINE_ID }}
                  base-name: getting-started
                  file-glob: '* .prettierrc' # optional
                  build-on-open: false
            - name: Get the review app url # Use the output from the `deploy` step
              run: echo "The URL is ${{ steps.deploy.outputs.url }}"
            - name: Comment on commit # Optional: You can use any action to comment on the PR
              uses: phulsechinmay/rewritable-pr-comment@v0.2.1
              with:
                  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
                  COMMENT_IDENTIFIER: 'Deploy'
                  message: |
                      I deployed a review app for you to try out this pull request 👇🏽

                      Heroku App: ${{ steps.deploy.outputs.url }}
```

The action expects a few input parameters which are defined below.

-   **api-key:** _(required)_ Your Heroku API key.
-   **pipeline-id:** _(required)_ The id of the pipeline for the review apps.
-   **files-glob:** _(Optional - Default `*`)_ The glob pattern for files to include in the deployment.
-   **base-name:** _(required)_ The prefix used to generate review app name. This should be the same as what you specified for the review app URL pattern in Heroku Dashboard.
-   **build-on-open** _(Optional - Default `false`)_ Choose to create / monitor the build in this action on `opened` action for PR.

## Difference between v1.x & v2.x

Earlier version from v1.0 created apps and placed them into the pipeline as a review app. This worked ok because you could control the review app name. However, it came at the cost of not being able to automatically use the config variables defined in the Review Apps settings. The workaround was to use something like what you see in this [page](https://help.heroku.com/RVEKYMZQ/how-to-copy-staging-dev-production-application-config-variable-to-review-app-config-vars).

In order to utilise the config vars defined for review apps, I decided to use the _/review-apps_ Heroku API endpoint for v2 of this action. There are less things to configure, therefore the inputs are different and caused a breaking change. Both versions are stable and serves different use-cases. Bug fixes will still be treated for both, but with the most focus on v2.

### How to choose?

If you already have review apps enabled for your pipeline, use v2. If you have a pipeline with review apps disabled, use v1.2.x.

If you use v1.x and want to see the README for v1.x, switch to the v1.x [branch](/tree/v1.x). The main branch has code for v2.x.

## Sponsor

To better support the maintenance and active development of this projects (as well as others), it'll be helpful if you [sponsor me here on GitHub](https://github.com/sponsors/pmbanugo).

## Contributing

You're welcome to submit PR to fix a bug or propose a feature. When you send a PR, pls point it to the **dev** branch
