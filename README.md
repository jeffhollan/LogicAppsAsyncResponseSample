[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/)

# Logic Apps Async Response Sample
Sample of how to do an HTTP Async Response pattern to work with Azure Logic Apps.  See the AsyncController for the logic.  The Logic Apps engine will timeout by default after 1-2 minutes of an open HTTP Request.  By setting up this async pattern you can keep the Logic Apps engine waiting for a task for much longer (as long as you have data stored for your flow - which is up to 1 year for Premium plans).

## How it Works ##

When you get the initial request to start work, you start a thread with the long-running task, and immediatly return an HTTP Response "202 Accepted" with a location header.  The location header points to the URL for the Logic Apps to check the status of the long-running job.  By default the engine will check every 20 seconds, but you can also add a "Retry-after" header to specify the amount of seconds until the next poll.

After the alloted time (20 seconds), the engine will poll the URL on the location header.  If the long-running job is still going, you should return another "202 Accepted" with a location header.  If the job has completed, you should return a "200 OK", along with any relevant data.  This is what the Logic Apps engine will continue the workflow with.
