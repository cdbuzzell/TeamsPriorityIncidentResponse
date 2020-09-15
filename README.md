# Teams Priority Incident Response
Enable more connected and complex incident response scenarios by posting messages in Teams, creating new teams, notifying incident response members via teams, etc. from ServiceNow (or your favorite ITSM solution).

This example starts with a Priority Incident being created in ServiceNow, which will trigger a process that posts a message to Teams, after which you can stand up a Teams meeting and invite participants to begin triage, record the meeting, chat, share screens, etc.

We have separated the potential solutions/implementations into three paradigms:

1. [CRAWL: Incoming Webhook](Crawl.md)
2. [WALK: Power Automate](Walk.md)
3. [RUN: C# Bot](Run.md)