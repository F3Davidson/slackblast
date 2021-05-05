# slackblast

Python web application needed to utilize modal window inside slack to make posting backblasts easier for PAX.

# getting started

Go to https://api.slack.com/start/overview#creating to read up on how to create a slack app. Click their `Create a Slack app` while signed into your F3 region's Slack. The main idea is that you will set up a slashcommand, e.g. `/slackblast` or `/backblast`, that will send the request to your server that is running this web application (we recommend using a free Azure App Service) that will respond with a command to tell Slack to open up a modal with the fields to fill out a backblast post. When the user hits submit on the modal, the information will be sent to your server where it will then format it and post to the designated Slack channel!

Bonus: the post will be in a format friendly for Paxminer to mine and gather stats.

Go to https://azure.microsoft.com/en-us/services/app-service/ to create a Free Azure App Service to host this web application. The [VSCode Azure Extensions](https://code.visualstudio.com/docs/azure/extensions) will be helpful to upload your own .env file with your region's specific Slack and opinionated settings. See how to [integrate your Azure App Service with Github](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/app-service/deploy-continuous-deployment.md) for easy deployments.

When you finish setting up and installing the slackblast app in Slack, you will get a bot token also available under the OAuth & Permissions settings. You'll also get a verification token and signing secret on the Basic Information settings. You will plug that information into your own .env file. When you finish creating the Azure app, you will need to get the URL and add it (with `/slack/events` added to it) into three locations within the slackblast app settings. Lastly, you will need to add several Scopes to the Bot Token Scopes on the OAth & Permissions settings. Read on for the nitty gritty details.

# environment variables

slackblast requires the following environment variables:

```
SLACK_BOT_TOKEN=<YOURTOKEN>
SLACK_VERIFICATION_TOKEN=<SLACKVERIFICATIONTOKEN>
SLACK_SIGNING_SECRET=<SLACKSIGNINGSECRET>
POST_TO_CHANNEL=<False or True>
CHANNEL=<USER or THE_AO or channel-id>
```

set `SLACK_BOT_TOKEN` from the token on the oath page in the slack app

set `SLACK_VERIFICATION_TOKEN` from the Basic Information -> Verification Token field in the settings for your slack app.

set `POST_TO_CHANNEL` equal to `True` or `False`. Set to true if you are using paxminer. Indicates whether or not to take the modal data and post to a channel in slack.

set `CHANNEL=channel-id` to the channel id (ID such as C01DB7S04KH -> NOT THE NAME) you want the modal results to post to by default.
set `CHANNEL=THE_AO` to post to the channel that was selected in the modal by default.
set `CHANNEL=USER` to post a DM from the slackblast to you with the results (testing) by default.
NOTE: In the modal, the user can choose where to post to.

See .env-f3nation-community file for help on local development

# slack app configuration

The url for your deployed app needs to be placed in three locations:

1. Interactivity and Shortcuts
   - Request URL
   - Options Load URL
2. Slash Commands
   - Request URL

**Format of the URL to be used**

```
https://<YOUR APP URL>/slack/events
```

**Scopes**

```
app_mentions:read
chat:write
chat:write.public
commands
im:write
users:read
users:read.email
```

# deployment

main_slackblast contains the code to deploy on Azure via github repository. However, this will be unique to your own installation.

# notes

Use vscode locally with a `.env` file with the above variables. With vscode Azure extension you can right-click on 'Application Settings' and it will upload your `.env` variables right into the AppService.

Pushing to the github repo should trigger a new deployment to Azure if you set up the AppService correct.

# startup command(s)

To run locally:

```
pip install -r requirements.txt
gunicorn -k uvicorn.workers.UvicornWorker --bind "0.0.0.0:8000" --log-level debug app:app
```

In another console, use the url output by ngrok to update your slackblast app settings:

```
ngrok http 8000
```

See .env-f3nation-community file for more details on local development
