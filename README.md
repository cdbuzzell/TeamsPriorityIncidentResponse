# Teams Priority Incident Response
Enable more complex incident response scenarios by triggering a Microsoft Flow from ServiceNow (or your favorite ITSM solution) to create Teams channels, notify incident response members via teams, etc.

This example starts with a Priority Incident being created in ServiceNow, which will trigger a flow that calls a Microsoft Flow via REST, which adds a channel to an existing **Service Desk** team in Microsoft Teams and notifies relevant parties via Teams message.

In the future, I plan to include more complex capabilities, including calling the Microsoft Graph API to create/clone Teams, add tabs, trigger Bot to call triage members, etc. At that point, I'll also include a copy of the flow to import.

## BASIC

### Create an incoming webhook in Microsoft Teams
1. In Microsoft Teams, add an Incoming Webhook connector to the appropriate channel in the appropriate team.
![Teams Connector](Images/Teams-Connector.png)
![Teams Connector2](Images/Teams-Connector2.png)
2. Copy the URL for the webhook and paste in OneNote/notepad

### Create ServiceNow flow to send notification to Microsoft Teams
1. Enable the ServiceNow IntegrationHub Installer plugin in ServiceNow (if not already enabled)
2. Open **Flow Designer** from the left nav in ServiceNow
3. Create a new flow
4. Trigger on a new Incident, filter on Priority = Critical
![ServiceNow Trigger](Images/SN-Trigger.png)
5. Add an action for **Microsoft Teams**: **Post Incident Details**, pasting in the incoming webhook we created earlier in Teams
![ServiceNow Teams](Images/SN-Teams.png)
6. Activate the flow
![ServiceNow Flow](Images/SN-Flow.png)
7. Test it by adding a new Incident in ServiceNow with the **Impact** and **Urgency** both set to **High**

## ADVANCED
### Create a flow in Microsoft Flow
1. Browse to https://flow.microsoft.com
2. Click on **My flows**
3. Click **New -> Instant**, then click **Skip**
4. Add a **When a HTTP request is received** trigger
![Flow Trigger](Images/Flow-Trigger.jpg)
5. Use this sample payload (feel free to alter it to fit your needs) to generate the schema for this action (the URL will show up after you save this flow):
    ```json
    {
        "incident":"INC00001",
        "title":"Incident Title 1",
        "description":"Incident description 1",
        "url":"https://TENANT.service-now.com/nav_to.do?uri=incident.do?sysparm_query=number=INC00001",
        "triageMembers":"admin@TENANT.onmicrosoft.com,USER@TENANT.onmicrosoft.com"
    }
    ```
![Flow Trigger 2](Images/Flow-Trigger2.jpg)
6. Add an action to **Create a channel** in **Microsoft Teams**, selecting the appropriate Team and display name (I used the incident property from the trigger)
![Flow Channel](Images/Flow-Channel.jpg)
7. Add a **Compose** action to split the comma-delimited triageMembers email addresses into an array

    split(triggerBody()?['triageMembers'],',')
![Flow Compose](Images/Flow-Compose.jpg)
8. Add an action to **Post a message as the Flow bot to a user (Preview)** in **Microsoft Teams** (when you drop the Output of the Compose action into the Recipient field, the system will automatically add the **Apply to each** action)
![Flow Post Message](Images/Flow-PostMessage.jpg)
9. Alter this Flow to meet your needs, save it, then copy the HTTP POST URL from the trigger

Your Flow should look something like this:
![Flow Canvas](Images/Flow-Canvas.jpg)

TODO: Test with PostMan

### Create ServiceNow Action to call Microsoft Flow
TODO: remove this section
Notice: I am not an expert at anything, especially ServiceNow.
1. Open **Action Designer** in ServiceNow
2. Create a new Action
3. Add Inputs that match the attributes in the sample payload used in the Flow above
![ServiceNow Action Inputs](Images/SN-Action-Inputs.png)
4. Add a REST step. You'll need to split up the host, path, and query string of the URL from the Microsoft Flow into separate attributes as shown here:
![ServiceNow Action REST Step 1](Images/SN-Action-REST1.png)
![ServiceNow Action REST Step 2](Images/SN-Action-REST2.png)
5. Add Outputs
![ServiceNow Action Outputs](Images/SN-Action-Outputs.png)
6. Save and Publish

### Create ServiceNow Outbound REST Message to call Microsoft Flow
Notice: I am not an expert at anything, especially ServiceNow.
1. In ServiceNow, under System Web Services > Outbound, click on REST Message and create a new one.
2. Truncate your flow URL up to the *invoke* part of the path and enter that as the Endpoint (it won't let us just paste the whole URI, so enter everything before the ? mark, we'll add the rest of the URI [query string] later). Set Authentication to "No authentication"
![ServiceNow REST Message Authentication](Images/SN-REST-Auth.png)
3. Add an HTTP Header for Content-Type
![ServiceNow REST Message Request](Images/SN-REST-Request.png)
4. Add/use the Default POST HTTP Method
![ServiceNow HTTP Post 1](Images/SN-REST-Post1.png)
5. Copy/paste the json from #5 above into the *Content* field and replace values with variables (example: *${variableName}*)
6. Add the four Query Paramters that we stripped onf the URL in #2. Pro tip: replace the *%2F* in teh *sp* parameter with */* (*/triggers/manual/run*)
![ServiceNow HTTP Post 1](Images/SN-REST-Post2.png)
7. Add variable substitutions and Test

### Update ServiceNow Flow to call Microsoft Flow
Note: I have not gotten this to successfully execute the Microsoft Flow we created above. It calls it, but the flow fails because it doesn't have anything in the body of the request. Working on it.
1. Open the ServiceNow flow you created in the BASIC section
2. Add an action, selecting **Global** to filter down to see the action you created
3. Set the inputs of the action to values from the Trigger -> Incident Record
![ServiceNow Flow Advanced](Images/SN-Flow-Advanced.png)
4. I hardcoded the triageMembers in my example, but you should look up who should be contacted according to your incident response process.
