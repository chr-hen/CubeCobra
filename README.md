# CubeCobra

CubeCobra is an open-source web application for building, managing, and playtesting Magic: the Gathering cubes.

---

## Contribution guidelines

Please read the [Contribution guidelines for this project](CONTRIBUTING.md) if you are interested in contributing to CubeCobra.

---

## Setup

### 1. Install Prerequisites

You will need to install the following prerequisites to run CubeCobra:

- NodeJS
- Redis
- LocalStack
- Code Editor (IDE) of your choice (though I recommend VSCode).

You can find links to the prerequisites here, as well as some notes on each:

#### NodeJS

Node 20

NodeJS: https://nodejs.org/en/download/

#### Redis

Redis Server:

- Windows: https://github.com/microsoftarchive/redis
- Mac: `brew install redis`
- Linux: `apt-get install redis`

After installing Redis, start the server.

On mac, a shortcut to do this is `brew services start redis`. You can seet the status with `brew services list`.

#### LocalStack

[LocalStack][LocalStack] provides a local emulation of AWS Services required to run CubeCobra including S3, DynamoDB and Cloudwatch.

You may follow the installation guidelines from the LocalStack site. The recommended setup involves running LocalStack in a docker container, which requires [Docker Desktop][docker] as well.

- Windows: Download and install the binary from LocalStack
- Mac: `brew install localstack/tap/localstack-cli`
- Linux: Use the `curl` command from LocalStack

[LocalStack]: https://docs.localstack.cloud/getting-started/installation/
[docker]: https://docs.docker.com/desktop/install/mac-install/

Once LocalStack is installed, you can start the server in the background with the CLI: `localstack start --detached`.

You can verify the status of LocalStack by running: `localstack status`.

#### Code Editor (IDE)

- VSCode (strongly recommended, but not required): https://code.visualstudio.com/
- ESLint Extension for VSCode: https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint
- Prettier Extension for VSCode: https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode

VSCode (with the ESLint and Prettier extension) is the recommended environment. When using this setup, make sure that your selected workspace is the root folder that you have cloned, this will ensure that the ESLint plugin can work with our linting rules. Prettier will automatically apply standard formatting to your code. Using these plugins will make adhering to the linting and code formatting rules significantly easier.

### 2. Perform Initial Setup

For the first setup, you will need to run:

```sh
yarn install && yarn build
yarn setup:local
```

This will:

1. Install dependencies
1. Build the application code to run setup scripts
1. Run setup scripts to:
  - Create a .env file with values for running the application locally already set
  - Setup LocalStack w/ s3 bucket
  - Setup local files for application perisistence
  - Setup LocalStack dynamodb tables (ex. Users, Cubes, Cards, etc.)
  - Download bulk card data from scryfall, persist to files and load it to LocalStack s3

If you are on Windows, you will need to set Bash as your script shell:

You will need to make sure you have `bash` installed somewhere and run the following command (with your `bash` path in place of the path below).

```sh
yarn config set script-shell "C:\\Program Files\\git\\bin\\bash.exe"
```

### 3. Run CubeCobra

With your environment prepared, run this command to start CodeCobra:

```sh
yarn start:dev
```

This script will:

1. Ensure LocalStack is running
1. Ensure nearley parsers for card filters have compiled
1. Compile and watch scss (bootstrap) styles
1. Compile and watch server javascript with nodemon
1. Run and watch webpack dev server

Once the script completes, open a web browser and connect to the app on this URL: `http://localhost:8080`

Please keep these two important points in mind while you use the application locally:

1. While node reports the application as running on port 5000 in its logs, you should indeed use port `8080` to connect.
1. Nodemon will restart the application anytime there is a change to a source file.

After accessing the application locally you will need to create a new user account using the **Register** link on the navigation menu.

### 4. Environment Variables and Connecting to AWS

Environment variables are populated from the `.env` file. There is no `.env` file checked in to this repository, so the setup script copies `.env_EXAMPLE` to `.env` and with some default values to support CubeCobra backed by LocalStack.

You can run a local instance of CubeCobra against real AWS resources rather than LocalStack, if desired. After setting up S3, DynamoDB, and Cloudwatch using your AWS account, you can insert your credentials into the `.env` file.

This table outlines how to fill out those environment variables:

| Variable Name          | Description                                                                                  | Required? |
| ---------------------- | -------------------------------------------------------------------------------------------- | --------- |
| AWS_ACCESS_KEY_ID      | The AWS access key for your account.                                                         | Yes       |
| AWS_ENDPOINT           | The base endpoint to use for AWS. Used to point to localstack rather than hosted AWS.        |           |
| AWS_LOG_GROUP          | The name of the AWS CloudWatch log group to use.                                             | Yes       |
| AWS_LOG_STREAM         | The name of the AWS CloudWatch log stream to use.                                            |           |
| AWS_REGION             | The AWS region to use  (default: us-east-2).                                                 | Yes       |
| AWS_SECRET_ACCESS_KEY  | The AWS secret access key for your account.                                                  | Yes       |
| CUBECOBRA_VERSION      | The version of CubeCobra.                                                                    |           |
| DATA_BUCKET            | The name of the AWS S3 bucket to use. You will need to create this bucket in your account.   | Yes       |
| DOMAIN                 | The domain name of the server. Used for external redirects such as emails.                   | Yes       |
| DOWNTIME_ACTIVE        | Whether or not the site is in downtime mode.                                                 |           |
| DYNAMO_PREFIX          | The prefix to use for DynamoDB tables. You can leave this as the default value               | Yes       |
| EMAIL_CONFIG_PASSWORD  | The password for the email account to use for sending emails.                                |           |
| EMAIL_CONFIG_USERNAME  | The username for the email account to use for sending emails.                                |           |
| ENV                    | The environment to run CubeCobra in.                                                         | Yes       |
| NITROPAY_ENABLED       | Whether or not to enable NitroPay, our ad provider.                                          |           |
| NODE_ENV               | The environment to run CubeCobra in.                                                         | Yes       |
| PATREON_CLIENT_ID      | The client ID for the Patreon OAuth app.                                                     |           |
| PATREON_CLIENT_SECRET  | The client secret for the Patreon OAuth app.                                                 |           |
| PATREON_HOOK_SECRET    | The secret for the Patreon webhook.                                                          |           |
| PATREON_REDIRECT       | The redirect URL for the Patreon OAuth app.                                                  |           |
| PORT                   | The port to run CubeCobra on.                                                                | Yes       |
| REDIS_HOST             | The URL of the Redis server.                                                                 | Yes       |
| REDIS_SETUP            | Whether or not to setup the Redis server - this is needed for Redis but not for elasticache. |           |
| SECRET                 | A secret phrase for encryption. You can leave the default value.                             | Yes       |
| SESSION_SECRET         | A secret phrase for session encryption. You can leave the default value.                     | Yes       |
| SESSION                | The name of the session cookie. You can leave the default value.                             | Yes       |
| TCG_PLAYER_PRIVATE_KEY | The private key for the TCGPlayer API.                                                       |           |
| TCG_PLAYER_PUBLIC_KEY  | The public key for the TCGPlayer API.                                                        |           |
| CACHE_ENABLED          | Whether or not to enable caching.                                                            |           |
| AUTOSCALING_GROUP      | The name of the autoscaling group this instance is run in, used for the distributed cache.   |           |
| CACHE_SECRET           | The secret for the distributed cache.                                                        |           |

---

## Updating Card Definitions and Analytics

In the initial setup scripts, `yarn update-cards` creates the card definitions. Running this script will pull the latest data from scryfall.

If you would like card analytics, run the following script:

```sh
yarn update-all
```

This script will:

1. Update draft history
1. Update cube history
1. Update metadata dictionary
1. Update cards

---

## Concepts

### Backend

#### API and Template Rendering

[Express 4][express] provides a minimalist web framework to support both template rendering with [PugJS 3][pug] and definition of JSON-based API endpoints. HTML templates are mainly used to render a minimal page for React to bootstrap itself into with initial props injected from the server.

[express]: https://expressjs.com/en/4x/api.html
[pug]: https://pugjs.org/api/getting-started.html

#### Cards

We keep all card definitions in large pre-processed files, so that nodes in production just need to download and load the files, and can fetch the latest files from S3 when they're ready. We do this because it's much faster to read from memory than to have to make requests to some other service anytime we need card data.

An external process is responsible for updating the card definitions, and uploading to S3. This same process is also responsible for updating the card analytics and data exports.

#### Multiplayer Drafting

We use Redis for concurrency control for multiplayer drafting. All Redis operations are handled in `multiplayerDrafting.js`

#### Scheduled jobs

Each instance of the express server runs a job using node-schedule on a nightly basis to update the in-memory carddb from s3.

Bash scripts (`jobs/definition`) are executed periodically on AWS to run hourly, daily and weekly jobs.

#### Card Filters

Card filters are defined that can be used by the frontend and backend. [Nearley][nearley] is a nodejs parser toolkit that is used to generate code that define filters that can be applied to the card database.

[nearley]: https://nearley.js.org/

### Frontend

#### Typescript

[TypeScript 5.5][typescript] is gradually being rolled out to replace usage of vanilla JS components with PropTypes.
[typescript]: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-5.html

#### Components and Styling

Components are provided by [Reactstrap 9][reactstrap] which is powered by [Bootstrap v5.1][boostrap], which uses SCSS.

Components typically directly use bootstrap classes for additional styling, but may also use global classnames defined in public CSS files.

[reactstrap]: https://reactstrap.github.io/
[bootstrap]: https://getbootstrap.com/docs/5.1/getting-started/introduction/
