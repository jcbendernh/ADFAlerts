# ADFAlerts
This repository utilizes a creative method to utilize alerting in Azure Data Factory via Logic Apps.  Below are the steps to setup a failure alert.  You could also modify these to create success alerts as well.

1.	Create the <B>Logic App</B> using the settings below as guidance.  <BR> &nbsp; 
![Create Logic App](/img/1-createlogicapp.png)
2. Once Deployed, go into the Logic App Designer and add a Trigger of <B>When a HTTP request is received</B>.<BR> &nbsp; 
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
<br> &nbsp; 
    <div style="padding-left: 40px">
          ![Logic App Trigger](/img/2-logicapptrigger.png)
    </div>
