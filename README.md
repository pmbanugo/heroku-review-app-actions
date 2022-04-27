# GitHub Action - Manage Heroku Review Apps

This GitHub action manages Heroku review apps for your software delivery pipeline. This was born out of the frustration of not being able to use review apps for a long time and there's no timeline when Heroku will have this sorted. See [status.heroku.com/incidents/2413](https://status.heroku.com/incidents/2413) for more info.

This mimics 💯 how review apps work with the native Heroku integration. Compared to other exisitng solutions created by the community in the last few days, this action is:

- ⚙️ Focuses on automated preview deploy. You get the same automated preview deploy experience for your PRs.
- 🏎 Fast . No need to upload all your git info/metadata or history to Heroku.(coming soon - configure which files to omit from source code upload).
- 🧘🏽‍♀️ No fear of 3rd party having access to all your private GitHub data/repository.

## How To Use

Here's an example workflow that uses this action:

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
        uses: pmbanugo/heroku-review-app-actions@v1 # Uses the action
        id: deploy
        with:
          api-key: ${{ secrets.HEROKU_API_KEY }}
          pipeline-id: ${{ secrets.HEROKU_PIPELINE_ID }}
          app-name-prefix: getting-started
          region: us
      # Use the output from the `deploy` step
      - name: Get the review app url
        run: echo "The URL is ${{ steps.deploy.outputs.url }}"
      - name: Comment on commit # Optional: You can use any action to comment on the PR
        uses: phulsechinmay/rewritable-pr-comment@v0.2.1
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          COMMENT_IDENTIFIER: "Deploy"
          message: |
            I deployed a review app for you to try out this pull request 👇🏽

            Heroku App: ${{ steps.deploy.outputs.url }}
```

The action expects a few input parameters which are defined below.

- app-name-prefix (optional): Prefix for the app. This is should generally be the name of the pipeline e.g `airtable` prefix will produce `soludo-pr-PR_NUMBER.herokuapp.com`.
- api-key (required): Your Heroku API key
- pipeline-id (required): The id of the pipeline to deploy the review app to.
- region (optional): The region to deploy to. For example `eu` or `us`. Default: `eu`
- stack (optional): The Heroku stack to deploy and build with e.g heroku-18. Default: `heroku-20`

## What's next?

I used a similar code for my private workflow since the removal of GitHub integration from Heroku. I decided to release this seeing that there's no clear indication as to when it'll be sorted. There are some edge cases that should be handled (proper error handling) but at the current release is not being done. I'll be making improvements in the coming days/weeks until Heroku restores their service (or until the community no longer needs this).

This action works well with most cases at the moment. If something is broken or you would extra features or configuration, feel free to create an issue.

## Sponsor

To better support the maintenance and active development of this projects (as well as others), it'll be helpful if you [sponsor me here on GitHub](https://github.com/sponsors/pmbanugo).
