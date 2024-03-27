# ADFAlerts
This repository utilizes a creative method to utilize alerting in Azure Data Factory via Logic Apps.  Below are the steps to setup a failure alert.  You could also modify these to create success alerts as well.

1.	Create the <B>Logic App</B> using the settings below as guidance. <BR>
Please keep <b>Plan type = Consumption</b>.  All other settings can be modified is needed. <br> &nbsp; <br>
![Create Logic App](/img/1-createlogicapp.png)

2. Once Deployed, go into the Logic App Designer and add a Trigger of <B>When a HTTP request is received</B>.
3.	In the <b>Request Body JSON Schema</b> of the newly added trigger, add the following code.<br> &nbsp;<br>

    ```json
     {
        "properties": {
            "DataFactory": {
                "type": "string"
            },
            "DataFactoryURL": {
                "type": "string"
            },
            "Pipeline": {
                "type": "string"
            },
            "PipelineRunID": {
               "type": "string"
        	},
            "PipelineStatus": {
                "type": "string"
            },
            "Recipient": {
                "type": "string"
            }
        },
    	"type": "object"
    }

    ```
<br> &nbsp;<br> 
    <div style="padding-left: 40px">
          ![Logic App Trigger](/img/2-logicapptrigger.png)
    </div>

**NOTE**: For my example, I will send out an email, you can send a text via [Twilio](https://learn.microsoft.com/en-us/connectors/twilio/) or a [Microsoft Teams](https://learn.microsoft.com/en-us/connectors/teams/) notification as an alternative.

4. Next,  create a New Step by adding the following action: **Office 365 Outlook – Send an email (V2) action**. <br> &nbsp; <br>
![Logic App Action](/img/3-logicappaction.png)

5. Sign in using your Office 365 account and use the Enter the data from the previous step option so that you have an intuitive email subject and body. <br> &nbsp; <br>
![Logic App Email](/img/4-logicappemail.png)

6. Next Save your Logic App and go back to the **When a HTTP request is received trigger** and retrieve the **HTTP POST URL** and copy it to Notepad.<br> &nbsp; <br>
![HTTP POST URL](/img/5-requestbodyjson.png)
