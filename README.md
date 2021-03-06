# Vaccine Watch
Bot to notify when vaccine appointments are available.

Supports checking Hy-Vee, and sending notifications to Slack and [Twitter](https://twitter.com/kcvaccinewatch).

Notifications are sent when a location has appointments. No more notifications are sent for that location until it becomes unavailable again.

PRs welcome to support other clinics or notification methods.

This project has no affiliation with Hy-Vee.

## Example
Slack

<img src="https://user-images.githubusercontent.com/5343931/109823900-f4446f00-7bfd-11eb-8661-68167f013cf2.png" alt="Example of messages in Slack" width=500 />

Twitter

<img src="https://user-images.githubusercontent.com/5343931/109823499-9b74d680-7bfd-11eb-88f4-45743d824e57.png" alt="Example of messages in Twitter" width=500 />

## Setup
1. [Install docker](https://docs.docker.com/get-docker/)
1. Run `cp .env.template .env`
1. Fill in the variables in `.env` if you want slack or twitter integration.
1. `docker-compose up --build`

### Slack
1. While logged into your slack account, go to https://api.slack.com/apps/
1. Click `Create New App`
1. Choose a name and workspace
1. Click `Permissions`, and `Add an OAuth Scope`
1. Add `chat:write:public` and `chat:write`
1. At the top, click `Install to Workspace`
1. Copy your OAuth Token to use as `SLACK_BOT_TOKEN`

### Twitter
1. Apply for a [Twitter Developer account](https://developer.twitter.com/en/portal/petition/use-case)
1. Once you have the account, go to the [Developer Portal](https://developer.twitter.com/en/portal/dashboard)
1. Create a project and an app for your bot
1. Add `Read & Write` permissions to your app
1. In `Keys & Tokens`, generate Consumer Keys and Access Token/Secret to use as environment variables

### Walgreens
1. Go to [this page](https://www.walgreens.com/findcare/vaccination/covid-19/location-screening).
1. Open the network tab of your browser, and search for a zip code
1. On the request labeled `availability`, grab the request header named `X-XSRF-TOKEN` and set it to env var `WALGREENS_X_XSRF_TOKEN`
1. In the cookies for walgreens.com, grab the cookie named `XSRF-TOKEN` and put its value in env var `WALGREENS_XSRF_TOKEN_COOKIE`

## Lint
1. Install [pre-commit](https://pre-commit.com)
1. `pre-commit install`

Lint is run as a pre-commit, or on-demand with `pre-commit run --all-files`

## Deployment Instructions
### Docker
You can build a docker image with the Dockerfile, and run it with a redis server.

### Heroku
You can create an app in heroku, add a free redis plan, and push the source. Configure the dynos to enable `clock`.

Required Environment Variables:
- `REDIS_URL`: A redis service
- `VACCINE_CHECK_INTERVAL`: How often (in seconds) to check
- `RADIUS`: Within how many miles to check (Must be less than 25)
- `LATITUDE`: Latitude of the location to check (e.g. 39.1040382)
- `LONGITUDE`: Latitude of the location to check (e.g. -94.5701803)
- `ZIP_CODE`: Zip Code of the location to check (e.g. 64106)

Optional Environment Variables:
- `ENABLE_HYVEE`: If you want to check Hy-Vee pharmacies
- `ENABLE_WALGREENS`: If you want to check Walgreens pharmacies
- `ENABLE_CVS`: If you want to check CVS pharmacies
- `ENABLE_COSENTINOS`: If you want to check stores in the [Cosentino's family](https://app.squarespacescheduling.com/schedule.php?owner=21943707&calendarID=5070652) (Kansas City only)
- Slack:
  - `SLACK_BOT_TOKEN`: Token for your slack integration
  - `SLACK_TAG_CHANNEL`: If the channel should be tagged when appointments are available
  - `SLACK_CHANNEL`: Channel for the bot to post in (e.g. `#vaccine-watch`)
- Twitter:
  - `TWITTER_CONSUMER_KEY`, `TWITTER_CONSUMER_SECRET`, `TWITTER_ACCESS_TOKEN_KEY`, `TWITTER_ACCESS_TOKEN_SECRET`
