# Teams Priority Incident Response
Enable more connected and complex incident response scenarios by posting messages in Teams, creating new teams, notifying incident response members via teams, etc. from ServiceNow (or your favorite ITSM solution).

This example starts with a Priority Incident being created in ServiceNow, which will trigger a process that posts a message to Teams, after which you can stand up a Teams meeting and invite participants to begin triage, record the meeting, chat, share screens, etc.

We have separated the potential solutions/implementations into three paradigms:

1. [CRAWL: Incoming Webhook](Crawl.md)
2. [WALK: Power Automate](Walk.md)
3. [RUN: C# Bot](Run.md)


|                    | CRAWL: Incoming Webhook	        | WALK: Power Automate + HTTP Trigger	                        | RUN: C# bot    |
|:-------------------|:-----------------|:------------------------------|:-------|
| Call from any system	| X	| X | X |
| Need > O365 license	|   | X | 3 |
| Brandable notifier	| X	|   | X |
| IM individuals		| X	| X	| X |
| Requires configuration of each team/channel| X | 1 | 2 |
| Adaptive cards		| 	| X	| X |
| Do more than notify	| 	| X	| X |
| Update posted message	| 	| 	| X |
| Requires development	| 	| 	| X |

1. You don’t have to configure in each channel, but you have to have access to the Team to configure the flow
2. You have to install your bot for each Team/user you want to notify, but there is a way to proactively install via automation
3. REST API hosting (i.e. in Azure)
