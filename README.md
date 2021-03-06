# Greetings from J.A.R.V.I.S.
Just A Rather Very Intelligent System

# Requirements

The API Token key that Jarvis uses is based on the Slack app called "Bot", found here: https://fortune-demo.slack.com/apps/A0F7YS25R-bots?next_id=0


# Dependencies

Jarvis primarily listens for certain types of messages that occur via a helper slack user (what I named Jarvis-Subroutine). Subroutine user is what CodeStream sends events to as, and I send all of those events to a specific channel. Based on the message, Jarvis will respond acccordingly to the user. Jarvis.py expects JARVIS_SUBROUTINE_ID environment variable set with your own subroutine ID (this ID is a Slack ID, unique to the user, and can be found by spinning up Jarvis with a made up Subroutine ID and looking in the console for a new message from your own subroutine/helper, specifically the `id` under `bot profile`).

It also sends the push to production timing to [Tanzu Observability](https://www.wavefront.com), using the metric `buildtime`.

Examples:
- Subroutine: `has been rolled back due to qa rejection.` Jarvis will respond to user: `I've successfully killed QA.`
- Subroutine: `QA environment pending approval. Git Commit: ` Jarvis will respond (hardcoded) that `I've deployed git commit " + gitCommit + " to QA (http://qa.fortune.local:32554/). Shall I push it to production?`
- Subroutine: `Production has been updated with git commit `Jarvis will respond to user: `That commit is officially in production (http://fortune.local:30205/index.html). Congradulations, sir.`
- Subroutine: `CRITICAL: Issue with Git Commit ` Jarvis will respond to user: `I had to roll back git commit " + gitCommit + " due to higher than expected CPU usage.`

# Additional Commands / Responses

- User: `Thanks` Jarvis: `My pleasure.`
- User: `Hello` Jarvis: `Greetings, sir.`
- User: `Are you ready` Jarvis: `I'll continue to run variations on the interface, but you should probably prepare for your guests. I'll notify you if there are any developments.` (An Ironman movie reference)
- User: `Thanks buddy` Jarvis: `Enjoy yourself, sir.` (part of above Ironman movie reference)
- When Jarvis prompts user if they should push to production...
  + User: `dear god no` Jarvis will roll back the deployment.
  + User: `go ahead` Jarvis will continue with the deployment.

# Installation
Simpilist way of running this bot is via docker run:

> docker run --rm -it -e SLACK_API_TOKEN=`[slacktoken]` -e BASE_URL_CODESTREAM=`[ex:codestream.local]` -e CS_USERNAME=`[Code Stream username>` -e CS_PASSWORD=`[Code Stream password]` -e JARVIS_OVERRIDE=`[unique channel ID of channel you want Jarvis to respond to]` -e JARVIS_SUBROUTINE_ID=`[unique userID of subroutine]` -e TO_BASE=`[Tanzu Observability base url. ex: https://surf.wavefront.com]` -e TO_TOKEN=`[Tanzu Observability API Token]` ryandotclair/jarvis:2.0
  
 DockerFile:
 
    FROM python:3.8-slim-buster

    RUN mkdir /app
    RUN pip3 install slackclient requests
    COPY ./jarvis.py /app


    ENTRYPOINT [ "python3", "/app/jarvis.py" ]
