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
    <div style="padding-left: 50px">
          ![Logic App Trigger](/img/2-logicapptrigger.png)
    </div>

**NOTE**: For my example, I will send out an email, you can send a text via [Twilio](https://learn.microsoft.com/en-us/connectors/twilio/) or a [Microsoft Teams](https://learn.microsoft.com/en-us/connectors/teams/) notification as an alternative.

4. Next,  create a New Step by adding the following action: **Office 365 Outlook – Send an email (V2) action**. <br> &nbsp; <br>
![Logic App Action](/img/3-logicappaction.png)

5. Sign in using your Office 365 account and use the Enter the data from the previous step option so that you have an intuitive email subject and body. <br> &nbsp; <br>
![Logic App Email](/img/4-logicappemail.png)

6. Next Save your Logic App and go back to the **When a HTTP request is received trigger** and retrieve the **HTTP POST URL** and copy it to Notepad.<br> &nbsp; <br>
![HTTP POST URL](/img/5-requestbodyjson.png)

7. Within **Azure Data Factory**, open your pipeline and add the following parameters to your pipeline that will be used to populate the Logic App.  Make sure to add a **valid email** and a **valid URL to your Data Factory Studio** under Default value for both values.<br> &nbsp; <br>
![ADF Parameters](/img/6-adfparameters.png)

8.	Add a **Web** activity to the canvas and link it to the **On Fail output** of your activity and rename the Web activity to **Send Notification**.<br> &nbsp; <br>
![ADF Parameters](/img/7-webactivity.png)

9.	On the settings tab, perform the following steps.
    a. Paste the **HTTP POST URL** from your Logic App to the URL field.
    b. Set Method to **POST**.
    c. Under Body, add the following JSON…

    ```json
    {
        "DataFactory":"@{pipeline().DataFactory}",
        "DataFactoryURL":"@{concat('https://adf.azure.com/en/monitoring/pipelineruns/', pipeline().RunId, substring(pipeline().parameters.DataFactoryURL, 29, add(length(pipeline().parameters.DataFactoryURL),-29)))}",
        "PipelineName":"@{pipeline().Pipeline}",
        "PipelineRunID":"@{pipeline().RunId}",
        "PipelineStatus":"Success",
        "Recipient": "@{pipeline().parameters.Recipient"
    }

    ```
**NOTE**:  The DataFactoryURL has 3 parts to it.  Its purpose is to be used as a link that will take you right the Pipeline Run Summary page within the Azure Data Factory Studio under Monitor. <br>
     i. https://adf.azure.com/en/monitoring/pipelineruns/ this is the standard ADF path for US/Enaglish based Azure Data Factories.  If your region/language is different, your path may vary.<br>
     ii. The GUID for the Pipeline Run ID. **DO NOT MODIFY THIS**.<br>
     iii. This section is the last x characters of your Data Factory Studio URL.  It is based on the value you added for the **DataFactoryURL** parameter for the pipeline. 

10.	Next save your pipeline and when you get a failure on an activity you should receive an alert.  Alternatively, you can test on a success of the pipeline by switching the input of the Web activity to the **On success output** of your activity.<br.>  
*Just remeber to switch it back after testing.* <br> &nbsp; <br>
![ADF Parameters](/img/8-onsuccess.png)

**NOTE**:  One **Send Notification** activity can be tied to multiple activity outputs in the same pipeline. 