This repository shows all the steps needed to create Serverless services on AWS. Using AWS Lamda, API Gateway, SQS, SNS and EventBridge.
All the necessary code and steps are provided.
For an even better in-depth experience and explanation, it is best to have access to Digital Cloud Training's AWS Serverless for Beginners course. https://learn.digitalcloud.training/courses
<br>
<br>


## AWS Serverless

Deploying serverless applications on AWS.
<br>
Using services such as AWS Lambda, SQS, SNS, EventBridge, API Gateway – 
These services enables you to build distributed decoupled application and bind them together. With a variety of different functions for storing information or sending information between components of your application. 
<br>
<br>
API Gateway will be used to build REST APIs.
<br>

Event driven architecture -> where an event in one service is going to trigger an action in another service.
<br>
<br>
In this lesson, we are going to cover Serverless services and an architectural pattern known as event driven architecture with Serverless, we don't need to manage instances. So we've moved away from the model where we need to launch EC2 instances and then manage them. And now we can spend more time on innovation. You don't need to provision hardware. Of course, that's all done for you. AWS are managing the underlying infrastructure and there's no management of operating systems or software. So with serverless, both the operating system and the application are deployed for you and you're not worrying about things like the scalability of the underlying infrastructure or high availability. All of that is taken care of for you capacity provisioning and patching is also handled automatically all of that becomes abstracted away. So you just need to bring your code or deploy the service and configure it in some way, automatic scaling is provided for you with high availability and it can be very cheap as well. Services like Lambda are extremely cost effective to use. So let's have a look at serverless with event driven architecture. That's a pattern that where we can utilize services in such a way that an event in one is going to trigger an action in another service. Here, we have a static website and a user is accessing that static website running on S3 and performing some kind of action. In this case, the user is uploading a file through the static website. So the file ends up in an S3 bucket using a bucket configuration known as an event notification. We can configure it such that when an upload happens of an object S3 is automatically going to notify a Lambda function. And the Lambda function is then able to take that file that's been uploaded to the S3 bucket and process it in some way. So here the serverless function is processing the file and then maybe it's storing the results in another S3 bucket. Also that Lambda function may send a message to an SQS Queue. Remember queues are used for decoupling. So now we have a message in a queue and that message can be picked up by another service. Now a couple of ways. One is that that s service that function or instance, for example, can pull the queue or through event driven architecture. The queue can trigger the function to say message has been received. I know that I need to send it to a particular Lambda function that function might then process the information in that message and store the results in a dynamo DB table. So here we have an event driven architecture. The Lambda function might also use an SNS topic to send a notification via email. All of this happens automatically, the user simply uploaded an object to a website. Everything else happened automatically, it was event driven. So an event that happened in one service triggered an action in another service. And here we have a whole chain of those actions taking place.<br>
![1](https://github.com/user-attachments/assets/3e96d9db-fedc-4b21-948d-e6432d9c271e)
<br>
<br>


## AWS Lambda

Lambda is perhaps the most well known serverless service. With Lambda. We have functions, a Lambda function runs some code in response to some kind of trigger. Here, we have a developer. The developer has packaged up some code in a zip file. We can use zip or wire file formats. The code is then uploaded to the function. In this case, it's a simple Python script that runs through and logs and events are CloudWatch logs. So we've got some Python code. Now, the Python code has been added to the function at this point in time, nothing is happening and we're not spending any money. We've created a function but it's not actually running. So we're not paying anything. Then Lambda functions are invocated based on events and then the code is executed. So in this case, some kind of event, some kind of trigger is causing the lamb to function to execute the code. Pricing is based on memory assigned and the duration of the function execution. So depending on the amount of memory you assign to your function, it allocates a proportional amount of compute power of CPU and you pay based on the amount of memory you assigned and how long the execution of the function lasted. In this case, this function is simply writing an event to CloudWatch logs. With Lambda it supports many different languages such as Java, Go, PowerShell, Node.js, C#, Python and Ruby code. The execution role is an IAM role that provides the function with permissions to access other AWS services and resources. So for example, in the previous diagram, the Python code in the function was writing an event to cloud watch logs. That's only going to be possible if the function has permissions to cloud watch logs. So it's really important to make sure that your execution role for your function has the relevant permissions it needs. For example if you want to write some message to a queue or you want to add an item to a dynamo DB table all of that is going to require permissions that are assigned through the execution role. For monitoring and logging AWS Lambda will integrate with cloud watch. So you can see performance metrics as well as logs. From memory and time out, you can specify the amount of memory allocated to a function. And of course, that dictates the price as I mentioned before. And the maximum execution time, the maximum execution time is 15 minutes for a function. So you might have your code only running just for a few seconds or you can run it right up to 15 minutes. Let's have a look at function invocation. So the function runs in response to events from various services through AWS or direct invocation from the AWS SDKs or the API. Functions can be invoked either synchronously or asynchronously. With synchronous invocation, applications actually wait for the function to process the event and return a response. So you see a success or failure response. With asynchronous invocation, Lambda queues the event for processing and then just returns a response immediately. So you don't really know the outcome of that function invocation. Lambda will scale horizontally by running multiple instances of a function in parallel up to the concurrency limit. So here, if a function is invocated, it's going to execute. Here we can see that it's already got two invocations that have come, in that are running in parallel to each other and then more can occur as well. So here we have lots of different invocations of the function running in parallel to each other. And we can run many of these right up to the concurrency limit. If we want our Lambda function to access resources within a VPC, we can connect the Lambda function to the VPC. So remember that functions are regional and they don't have access to a VPC by default. So what we need to do, is connect to the VPC. And what will happen here is we'll define the subnets we want to connect to and Lambda is going to create an elastic network interface in those subnets. Now we do of course here need the function to have the relevant permissions for EC2 to create the ENI. So that has to be in the task execution role. You must select the VPC, the subnets and the security group that those ENIs are going to utilize. The function role needs to have the relevant permissions as mentioned before. A NAT gateway is required for internet connectivity. So maybe your Lambda function needs to access these applications in the private subnet, but maybe it also needs to check on the internet call an API download something or some other operation. In that case, you need a NAT gateway for the function to now be able to route out of the VPC and back out to the internet. So here we've deployed a NAT gateway in a public subnet. And now our Lambda function is able to access the internet via the NAT gateway and the internet gateway.<br>
![2](https://github.com/user-attachments/assets/4c8134c0-4c5d-4f02-b2b4-96c93ff4161e)<br>
![3](https://github.com/user-attachments/assets/2f56e5cd-389f-480e-88d9-0d0f66ec07e9)<br>
![4](https://github.com/user-attachments/assets/0ead6fc2-4fe5-4eb3-89b0-fb1448f3845d)<br>
![5](https://github.com/user-attachments/assets/d92f85c9-3a9e-4e0a-9661-aa4f44de7cf7)


 
 <br>
 
 ## Working with AWS Lambda

We're going to create a function and we're going to see how we can trigger executions of that function in a couple of different ways. What we're going to do first is we're going to go and create a Lambda function that logs a message to Amazon CloudWatch logs. And we have some Python code here. So what you'll want to do is copy all of this Python code from line 7 to 24. (this code is located in course download > aws-lambda folder > working-with-lambda.md file) 
<br>
<br>

![Capture](https://github.com/user-attachments/assets/21b6d3ca-2934-464d-a44d-56c9a51708d6)


So I'm going to copy this code now. What this code does is it simply writes an event to CloudWatch logs with a message and so we can write a customised message and it's going to appear in CloudWatch logs in one of the log files. So back in the console, I'm simply going to search for Lambda and there it is, Lambda run code without thinking about servers. So let's create a function in Lambda. And here what we can do is we can author from scratch, choose a blueprint or you can even use a container image. Well, we're going to author from scratch and I'm going to give this one a name. I'm going to call this write to Cloudwatch. Next, we need to choose the run time. We've got several different options as you can see. So what we're going to choose is we're going to use the latest Python. In fact, the latest would be up here. Python 3.12 doesn't really matter. 3.9,  3.12. Whatever the latest is when you watch this video, it could be different. Shouldn't matter with this particular code sample. So I'm going to just choose one of these run times. Next, you've got the option for the architecture. We leave that on x86_64. Permissions are really important to understand with Lambda. If you want your Lambda function to be able to interact with any other AWS services, you will need to supply permissions. Now by default, it's going to create a new role with basic Lambda permissions. And those permissions will provide access to CloudWatch logs. So I can just leave it as it is. Alternatively, you can use an existing role or choose from various policy templates which you can find down here. So I'll just leave it on the default to create a new role with basic Lambda permissions. We then have advanced settings. There are a few options here. We don't need to change any of those for now. So we'll just leave those selected or deselected at the defaults. And let's create the function. Once the function created, you're going to end up on this page here. What we can see is a function overview. We can see our triggers. If there are any, we can see destinations. We're going to go over these in just a moment. Down a little bit lower, we can see the code editor and it's just written some default Lambda code in here. Hello from Lambda. What I want to do is delete that paste in the code.<br>
<br>
![Capture](https://github.com/user-attachments/assets/3c7e4645-5712-4ab1-ba06-578929732a89)

Which we got from the document in visual studio code and then click on deploy. So that's going to deploy my update. Now along the top here, there are a couple of really important places. One is configuration. If we click on configuration, we can see there are lots of options here. For example, the default time out for our function is 3 seconds. We can increase that if we wish to. We've got memory here, 128 megabytes by default. What I want to show you is down in permissions. So permissions you can see there's a role here and that role has CloudWatch logs permissions by default. If you need to add permissions to another service, you can always go ahead and edit the role. Also on the monitor tab, this is where we can click on CloudWatch logs. Once we've executed our function. I'm going to use the test utility here. And what we're going to do is we're going to create a test event. So back in this document here, we're going to create a test event using this test data and it just says, hello CloudWatch.<br>

*{<br>
  "message": "Hello, CloudWatch!"<br>
}*

So I'm going to copy this json and back in the event json here, let's paste this in, just call this *myevent* and then I'm going to save and then run a test event. What we see at the top here is a nice green banner which is saying that it's succeeded. And we've got a response here which says message logged successfully. Bit lower we can see some more log output and there are some details here in the summary as well. So how long did it take to run? Cos you get charged based on this per millisecond, which function version was used? $LATEST is the editable version of the code where we just entered our code a few moments ago. You can then publish different versions as you make changes as well. And we can see the log output here. So this should be in CloudWatch logs. So you can click here or remember, you can come up to the monitor tab and then click on view CloudWatch logs here. So now we can see this log stream. I'm going to click on the log stream, open up the log stream here and we can see our message ‘Hello, CloudWatch’ and there's a few events related to that individual execution. Telling us when it started, when it finished. We've got a report and then we have the information in the middle there. So that all worked nicely. If I come back to the file here, *working-with-lambda.md*  what we want to do next is see how we can do the same thing from the command line interface. What we're going to do is use this test data.<br>

*{<br>
  "message": "Hello from CLI!"<br>
}*
 
We need to create a file called *payload.json*. And then we're going to run this command to invoke our function.<br> 
*aws lambda invoke --function-name <function-name> --payload fileb://payload.json response.json*<br>
So first, I'm going to copy this json data. Let's open AWS CloudShell. And I'm going to create a file called *payload.json*, paste this data in and then simply save that file. So now I have my file. Next, we want to run this command, but we need to update our function name. Mine's called right to cloud watch. Next, you'll want to copy this entire command from where it says AWS down to the letter n after json. Now the fileb indicates that it's a binary format, which is what we want here. So the file, the b on the end here under after file is not a typo, don't edit that out. So then you want to paste this in and let's try and invoke our function. Now we have a status code 200 that is a success code and it does say the $LATEST was executed, that's the latest version of our function. So what I want to do is come back to CloudWatch here and let's just see if it's created a new log stream. No, it's the same log stream. So let's just expand. And what we should see now, is we should have an additional event where it says ‘Hello from CLI’. So that's work. We've now executed the function, both using a test event in the console and then we've used the command line interface with the AWS Lambda invoke command to invoke our function. Next, I'll show you how we can trigger the function using an event notification from Amazon S3. So what we're going to do now is we're going to upload objects, so files into an S3 bucket. And when we do that, an event notification, in S3 is going to let Lambda know that something's happened and Lambda is going to pick up the name of the file and associated meta data and it's going to put that into CloudWatch logs. So we have a slightly updated version of our code here. <br>
<br>
![Capture](https://github.com/user-attachments/assets/ae6c1f58-5add-420f-bb7d-5320c9540e50)



What we want to do is copy all of this code and back in Lambda. Let's go over to the code page, delete all of this code, paste a new code in and then deploy. And so now we've updated our code. Now we will need permissions to Amazon S3. So I'm going to go over to configuration, permissions, click on the role name and for the permissions policies here. All we have is basically access to CloudWatch logs. So what we're going to do is attach a policy. If I type S3, I need *ReadOnlyAccess* and then it’s add the permissions. Ok. So now the function has permissions to read from CloudWatch logs. So what we can do, we've got the code installed. Let's add a trigger. So if we add trigger and choose source S3. Now we need a bucket. So make sure you've got a bucket. Let's have a look at what I've got and what's in my buckets. So if I head over to S3, I've got a few from previous exercises. Anyone will do, maybe this one, *mybucket*. Ok. That's got a couple of documents in it. It doesn't matter if there are things in there already and it doesn't have to be publicly accessible. So you could just create a new bucket. In fact, let's do that. So, everyone's doing exactly the same thing. So this is *myeventnotificationtest* bit of a long name to make it unique and we don't need to make any changes whatsoever. So we'll create the bucket and then we can come back to triggers here. I'll give it a refresh. So it should pick up my new bucket. Next, we're going to leave the default event types here because this is what we want object create events. I don't need to set any other settings here. I'm just going to acknowledge this warning here and click on add. So now we can see that we have the trigger. If we go back to the bucket and click on the bucket and go to properties, scroll down a little way, we'll see that an event notification is being created so you can create this configuration through this console as well. So now what should happen is if we upload objects to the bucket ,it should execute the code automatically via the trigger. And then we should see an event in CloudWatch logs that includes the name of the file we uploaded. So I'm just going to go and upload a file. Let's choose one of these ones. My pineapple for example and click on upload. I'm going to close. Let's do the same again. Add a file, this time, I'm going to add my Papaya file. You can add anything you want, doesn't matter what type of document. So what we need to do now is go and check if this works correctly. So I'm back in Lambda. Let's go to monitor. In fact, I've already got CloudWatch logs open, but don't forget you can always come and find it from the monitoring tab. Now, in CloudWatch logs, I'm going to go back up a level because I suspect that it would create a new log stream which it has. So if I click on this log stream and then expand, we can see there's a whole bunch of metadata associated with the file and the event. It's an object created, put API action, we've got a source IP address. We've got information including the bucket name here and the key. The key is of course the file name. But now our code specifically prints out the file name as well. So we can see that right here and you can see a couple of different entries where two different objects were up uploaded to Amazon S3. So now we are not just manually triggering it through the CLI or the test event, but we're also having it automatically triggered through an event notification as well. So that's it for this lab. Feel free to clean up those resources. You can simply delete your function in your bucket. But you won't be paying for Lambda functions when they're not being used and you're not going to go through the free tier with a few test events like this anyway. And S3 does have quite a good storage limit for the free tier as well.
<br>
<br>


## Application Integration Services

So as the name suggests, these are a set of services that are used for integrating different components of our applications. Whether they're server based or serverless. So let's have a look at an event-driven architecture where we utilise some application integration services. Here, we have an e-commerce website. User places an order through the website. The website in this case is running on EC2. Now this event, the order being placed on the website, causes a couple of things to happen. Firstly, EventBridge is triggered. So a rule in EventBridge is going to route some information through to the simple notification service. To notify the customer that the order has been submitted. So here SNS is sending the email out. Meanwhile, the information about the order is placed into an SQS Queue. So the simple queue service is being used here. A message goes into the queue with some information about the order. Couple of things can happen here. Either the act of the message being placed in the queue can cause a trigger which is to trigger Lambda or you can configure Lambda to constantly pull the queue and check for new messages. In this case, the SQS Queue is configured to trigger Lambda. And Lambda will then store some results of the order in a DynamoDB table. So that's an event driven architecture, where we're connecting things together with application integration services. In this case, EventBridge, SNS and SQS. Once the order has been completely fulfilled, Lambda might also use SNS to send another email to the customer notifying them of the order fulfilment. <br>
![1](https://github.com/user-attachments/assets/997407b6-d94c-4041-a523-53a75f8c3706)

 
So let's get started with a bit more detail about SQS. Here we have a direct integration between two components of an application. Now this is fine for some use cases, but there is a problem with this and the problem is that we have these two autos scanning components of the application. What if one of them can't keep up with the workload. So maybe the Web tier is receiving a large number of orders and the auto scanning group on the back end, the App tier needs to scale and that takes some minutes. During that process, there could be some orders that get lost if you have this sudden spike in activity, that's a problem. So what we can do instead is put an SQS Queue in the middle. Now, the EC2 instance is polling the queue. So that's the App tier checking for messages that have been placed by the Web tier. Now, it doesn't matter if the Web tier suddenly gets a huge spike in orders and that information just gets put into the queue. In this case, the App tier can process those orders when it's ready to. And if that requires some scaling and that requires a bit more time, that's better than losing those orders completely. So here we have what we would call a decoupled integration. <br>
![2](https://github.com/user-attachments/assets/d74761f9-8fd8-42ac-8dff-1982e4092960)


Let's have a look at SNS. This is the simple notification service. The difference is SNS is more of a push based service. It's a publisher subscriber model. We create something called a Topic, we then have subscribers. So for example, for an email, it could be something like a newsletter. You could have one or thousands of people subscribing to your newsletter. And when you send it via the Topic, that email goes out to all of those different subscribers. But likewise, it could also be Lambda could be SQS could be some other web application. There's a variety of different targets who can be subscribers for a Topic. So the information is then sent to those subscribers every time a message gets placed into the Topic. Again, we can pass information from one component of an application to another. And it supports various transport protocols like HTTP, email, email json and SMS to name just a few. So this is another example of decoupling. This is more of a publisher subscriber model and it's where its more push based, SQS is more pull based. So typically instances or Lambda functions are polling the queue and checking for messages. And with SQS, it's more 1 to 1. One function or one instance is processing a message and that's done. In this case, one message goes out to many subscribers. <br>
![3](https://github.com/user-attachments/assets/af4c7122-eadc-4e7f-9751-eb391289a920)



EventBridge supports many different event sources. Can be AWS services, custom applications or SAS applications. Some event happens in those sources and that information is then sent through to EventBridge, the event bus based on rules that we create is then going to forward that information to various different targets. They can be, for example, Lambda functions, Kinesis, data streams, SNS topics and so on. So there we have targets. So EventBridge is a really useful tool. There's so much power that we can do with EventBridge and it has lots of built in integrations for AWS services and rules that we can create very easily through a graphical wizard. <br>
![4](https://github.com/user-attachments/assets/16c4daa4-5d17-4b5d-af00-89c69473bbdb)
 


So let's just compare some of these application integration services. SQS I've just gone into a fair amount of detail there. That's a message queue. So it's what we call a store and forward pattern and it's used for building distributed and decoupled applications. SNS is a notification service. So this is pushing notifications like emails or just information from one application component to another. But the end points are the subscribers and there can be many of them. Step Functions is useful for coordinating and orchestrating workflows. So for example, you can pull together lots of different Lambda functions into an orchestrated workflow with some logic about how those functions are executed. Amazon MQ is similar to SQS and that it is a message bus, but it supports the open source Apache Active MQ and RabbitMQ. So if you're using those industry standard APIs and protocols, many users or many companies will use these on premises. Then migrating into the cloud is easier if you want to keep using those same tools for your message buses. So that's where Amazon MQ comes in. And then lastly EventBridge, a serverless event bus for connecting lots of different application components together. Now these are some of the core application integration services. There are others, of course on AWS.<br>
![1](https://github.com/user-attachments/assets/32f8d791-2689-4044-8374-088201f5fdf6)
 
<br>
<br>



## Building a serverless application with a REST API (part 1)

We're going to start building a serverless application. This serverless application is going have a rest API and that's going to be part of the web front end so that we can actually access our application from the internet.  What we're going end up with when it's complete is a static website and we can submit orders through our browser. So the static website is a simple order submission. Imagine it's an ecommerce application. That order will then be forwarded through to a rest API running on Amazon API gateway. The API will proxy the request to a Lambda function. That means it just forwards it through without actually modifying it, in any way. The Lambda function will then take that order information that's been submitted and place it in an SQS Queue so that a different function and that function is the processing function can process the message and place it in a dynamo DB table. So now, the order information ends up in the dynamo DB table. So here we've separated with a queue. So of course, we're decoupling. That means if the processing, for example, takes a lot longer, then it doesn't impact the orders coming in and getting placed into the queue. So the final result is we have a record of the order added to the table and we'll be able to see that. <br>
![2](https://github.com/user-attachments/assets/91d5da9d-f25e-425e-8c1b-51ac6bd43350)



So we're going to build this part of the application and rather than have the API front end, we're actually going to submit a request through via the CLI and via the Lambda console as well. So that we still end up with a message added to the table. The whole application works except the web front end and the API will be added on later on. So we're simply going to be submitting our test events via the console and invoking our Lambda function via the CLI. <br>
![3](https://github.com/user-attachments/assets/ec3d0680-c0c6-4391-8b1b-20e176ee92b1)



The first thing we need to do is create a Lambda function. We're going to call it, *SubmitOrderFunction*. We're going use the Python 3.9 run time and we're going add the code from the SubmitOrderFunction.py file. We also need to provide SQS full access permissions to the execution role. So I'm going to head over to AWS Lambda. Let's create a new function. Provide the name, *SubmitOrderFunction*. And it is important that you follow the naming. And I'm going choose Python 3.9 and create that function. Now that the function is created, let's go and grab our code. So we're going go to the SubmitOrderFunction.py file and I'm going to select all and copy this function code. Now what we have here, we've got some Python code. We can see the on line 5 we have the SQS queue URL. So once we've created the queue, we're going to come back and edit this here to add in the URL of our SQS queue. And what this is basically going to do, this function is going to receive information, some order information in a specific format and then it's going to place that information into the queue. So back in my function, I'm going to delete all of this code and then paste in my code. I also need to go ahead. Now I haven't deployed yet, by the way, because we're going to add our Queue URL in a moment. So let's go to configuration, permissions and I'm going to click on the execution roll name. So that will open it up in the IAM console. In here, I can click on add permissions, attach policies and type SQS and we're going to provide *SQSFullAccess* and then add those permissions. So now the function role has the relevant permissions. Let's go ahead and create our queue. That's going be the next step. So I'm going open the Simple Queue Service, back in the instructions file. We do have a name for the queue. *ProductOrdersQueue* and we're going copy this into line 5 of our *SubmitOrderFunction* code. That's all we need to do with the queue. In SQS - Let's go and create our queue. We're going use standard. We're going to supply the name for the queue and that's it. We don't need to change any other settings. All the defaults are fine for this particular use case. So once you've clicked on create, you'll then see that we have the URL here. I'll copy the URL, come back to my *SubmitOrderFunction*, head across to the code tab. And then here we're going to just paste in this URL and then deploy the function code. So now we can actually test that. This works. What I want to do before, I have the next function processing messages straight from the queue. I want to show you that the message ends up in the queue. So what we're going to do is we are going to submit a test event with this data.<br>

![Capture](https://github.com/user-attachments/assets/c4844596-ea57-4def-86cd-6dbd398ed9f6)

You can change the test product to something else if you like. So I'm just going copy this JSON code and back in my function here, click on test, create a new test event, *testorder* and then I'm going to paste in my JSON code and then all I need to do is save and then run test. Now it looks like it's done the job we can see here, we get some responses and it says order submitted to queue successfully. Now let's head across to the queue and I'm going to click on send and receive messages. And in here we can click on poll for messages and you can see straight away it found a message. If I click on the message, we can actually see the body. OK. So there might be some more information on the details there. But this is the information. And our second Lambda function is gpoing to pick up information in this format and place it into a DynamoDB table. And this message will be processed as soon as we set that piece up. So let's go back to our instructions and see what we need to do next. We've run a successful test that all worked. Now, we're going to create our processing function. We're gpoing to provide this name. *ProcessOrderFunction*                                                                   It's going to be the 3.9 Python runtime again. And we're going to use the code from the *ProcessOrderFunction* file. This time, we need both SQS and DynamoDB access. So coming back to Lambda, let's head back up to functions. Create a function. I'm going to supply the name, make sure that I choose the correct run time. Python 3.9 and then create the function while that's creating, I'll come back, go to my *ProcessOrderFunction* here. And I'm going to copy all of this information noting that I need to update my DynamoDB table, once I've created it. Back in Lambda, I can remove all this code from the Lambda function here, add the new code in and then let's go across to configuration, permissions, click on the role name and we're going to add the permissions we need. So what we need is SQS, because we need to pick up the message from the queue and DynamoDB because this function is going to place the information into the DynamoDB table. So let's click on add permissions. We now have our permissions policies. So what we need to do is come back to the instructions and we're just going to check what the table information is going to be. So here we have *ProductOrders* and *orderId*. OK. So this is the name of the table. This is the primary key. Make sure you get the capitalisation correct because it is very important. So I'm just gpoing to copy paste. So we're gpoing to come back to the console, search for DynamoDB. It's very simple to create a table in DynamoDB. We simply click on the create table button paste in the table name, I'll copy the primary key, paste in the primary key. And again, we can leave all of the defaults here, create the table and very,very quickly the table should be ready for us. So there it's creating and within a few seconds it's become active. OK. So now we have our DynamoDB table ready. Now we do need to copy the name of the table here. And what I want to do is come back to my function *ProcessOrderFunction* here. Click on code. We need to go where it says your dynamo DB table, paste in the name and then deploy. Now at this point, we have all the high level components. We have the functions, we have the DynamoDB table, we have the Queue. However, the only thing we haven't done yet is integrate the Queue with the function. What we want is for the Queue to trigger the Lambda function. So back in SQS, I'm going to click on the queue to come back up to this level. Choose Lambda triggers. And we're going to configure a Lambda function trigger and we're going to choose a function and it's going to be the *ProcessOrderFunction* and then click on save. OK. So that's going to create, it says about one minute, usually a lot faster than that. Once that's ready, then we have the integration so that every time a message goes into the queue, it's going to automatically trigger the function. So the function doesn't even need to pull the queue in this case. The queue is sending a notification to say there's something waiting for you. So now the function will automatically process the message from the queue. So there we go, that's enabled. Let's see if it actually took the information that was already placed into the queue and it did. So there, there it is. My *Test* Product has actually been added to the table. We can also go ahead and test using the invoke function command from the command line as well.<br>

<br>
```bash<br>
aws lambda invoke --function-name <function-name> --payload fileb://input.json output.json <br>
```
<br>
<br>
 
So what we're going to do here is we're going to run a second test and this one is going to be using the CLI and CloudShell. So we'll open up CloudShell. We're going to create a file called input.json  and it's going to have Test Product 2 here, quantity 2. So I'm going to open up the CloudShell console. I'm going to use nano to create a file called input.json.  I'm going to paste in my input there. And then we're going to come back and what we need to do is run this invoke command. And of course, I need my function name. So I need to invoke the first function. That is the *SubmitOrderFunction*. So let's copy the function name, paste this in.<br>

<br>
```bash<br>
aws lambda invoke --function-name SubmitOrderFunction --payload fileb://input.json output.json <br>
```
<br>
<br>

And then we're going to invoke the function. The payload is in binary form, the fileb colon / / input.json and then the output goes there to record the success. So let's just copy the entire command. And back here in CloudShell, I'll just run this command to invoke the function.<br>
*aws lambda invoke --function-name SubmitOrderFunction --payload fileb://input.json output.json*<br>
And we get a status 200 which is always good news, come back, refresh. And now we have the second product that's added into our e-commerce table. So at this point, we have the *SubmitOrderFunction*, we have the queue, we have the *ProcessOrderFunction* and the DynamoDB table. <br>
![1](https://github.com/user-attachments/assets/0933637d-306a-4ec3-887e-1ce1b4603463)<br>
And we've submitted events from the CloudShell CLi, as well as test events through the Lambda function console. And those have both ended up in the table. So that's it for this lesson. When we pick it up again, for part two, we're going to add the static website front end and the API gateway so that we can publish this out onto the internet.
<br>
<br>


## Amazon API Gateway

API Gateway is a really useful service and we've learned a bit about APIs already. Of course, they are a fairly complex topic and I understand that for many new users, but I'm going to try and help you to understand API Gateway by showing you a few diagrams. And of course, we'll do a hands on lesson as well. So let's have a look at an example of what we call a rest API with Amazon API Gateway. So imagine that in a company, you have built some kind of application that is based on Microservices and so that we therefore have multiple components of this application. Here, we can see we've got a Lambda function for the booking service. We've got a Lambda function for the payment service and we've got ECS containers for the account service. So we've got these three different Microservices. On the left hand side, we have a mobile application. So users on the Internet with their access via a phone, have their mobile application and that mobile application needs to do things like viewing existing bookings for the customer, submitting new bookings and using the payment service to accept the credit card and pay for the service. And then the account service where they might be able to look at previous orders, get some help, things like that. So we've got these different Microservices. Now, how does the application running on a mobile phone connected via the Internet, connect to these different Microservices? Well, what we want is a single endpoint. So we want a public URL that we can direct the traffic to, that's Amazon API gateway. So it provides the entry point, but also it knows how to actually forward the requests to the various different Microservices depending on what the request is. Looking at things like the URL path or what type of request using APIs. Is it a get, is it put a put method? Is it a post method and so on? So it's utilizing a rest API over HTTPS. So it's a secure connection over the Internet. And the mobile application can be coded now to speak to the API end point and the API end point can then proxy that those connection requests through on the actual information in the request through to the various different Microservices. <br>
![2](https://github.com/user-attachments/assets/8c71786b-aa15-4197-8c95-f065d7119e40)
<br>
<br>

So how is it structured? So in API Gateway, we have a graphical interface which is nice and useful to create our configuration and it looks something like this. Here, we have something called a resource. I've highlighted in the orange boxes, what the resources are essentially, these are a path in the API. So if you imagine that the API end point is a URL, so it's a publicly accessible URL, then we can configure paths that we add on to that URL. So */booking* for the booking service, */inventory* for the inventory service, */payment* for the payment service. Within each of those resources, we then create the methods. So you can see those here like GET and PUT and POST and DELETE. These are HTTP methods. So now when we want to retrieve booking information, we're going to or the application is going to issue a GET request to the booking resource. And of course, we can then integrate each of these methods to our back end Microservices databases and so on. And it supports a variety of different end points. We've got HTTP end points here. We've got Lambda, we've got ECS, we've got DynamoDB and of course, it does support more than this.<br>
![3](https://github.com/user-attachments/assets/a01fec3d-f44a-41b6-9424-c1c767b64ab7)
<br>

Let's have a look at another example to put this together. Here we have an S3 website. Let's imagine this is a static website. We then have two different Lambda functions. One for sending support emails, one for logging, support tickets. And then we have an API Gateway end point in front. So here we've created the resources. One is */sendEmail*, one is */submitTicket*. And in both cases, there is a POST method. We can see now that when the customer submits a support request, the information could get emailed through to the support team or they can submit a support ticket and that gets logged into a DynamoDB table by the Lambda function. So there's an example where we have a single endpoint, we then have the resources and the methods directing the request through to the different back end Microservices.<br>
![4](https://github.com/user-attachments/assets/eceac7e7-3775-4f10-9438-169f72500607)
<br>
<br>
 


## Building a serverless application with a REST API (part 2)

This is part two of our building a serverless application with rest API. We're going to complete what we started in part one. So in part one, we ended here.<br>
![1](https://github.com/user-attachments/assets/ada44a85-bdb4-48a3-a97a-d3323f4945d5)<br>
We used the Lambda function console to submit a test event which went through our submit order function into the queue, which triggered our process order function, which placed the product into the table. So it looks like we have an order from our e-commerce store. We also used the CLI. What we're going to do now is we're going to add a static website running on S3 and then we're going to add an Amazon API gateway rest API. <br>
![2](https://github.com/user-attachments/assets/f0a12b15-adf6-4942-b508-ede543004f6d)<br>
This time from the internet using a browser, we can submit an order and the static website will forward the request to the rest API which then proxies the request through to the Lambda function. So again, proxying just means that the API is not performing any kind of transformation of the incoming information to the format that's required for the back end. In this case, the back end, the Lambda function is able to pick up the information in the format that comes straight through from the static website from the form and process that information. So we just proxy it straight through. 

Let's head back over and finish this lab. Back in the instructions file. I'm now down on line 68 here - the part two.
*serverless-app-instructions.md*
![image](https://github.com/user-attachments/assets/9969c931-2faf-442f-8307-c47cf8df98cb)<br>
And what we're going to do first is create the API. The API is going to be called *productordersAPI*. So let's go ahead and create it and then we'll come back and look at some of the configuration. Let's go to the API gateway console (on AWS). We're going to from the main screen here. Just scroll down until you see REST API. Not the private one. This is the public one. Click on build, new API, provide a name. By default, the endpoint type is regional. We don't want edge optimised or private. So let's create the API. Now it puts us into the API configuration. What we need to do here is create resources and methods. The first thing we're going to do is create a resource and that resource is going to be orders. So the path is / and then we have orders. */orders* and click on CORS which is Cross Origin Resource Sharing. We're coming from another website, we're coming from a static website running on S3. That's where the request is going to come from to the API. So we need to enable Cross Origin Resource Sharing to allow that to happen. So what we can do then is simply create the resource. So where we are now is we've done this step here. Step 2, create the orders resource with CORS enabled. Then we need to create a POST method for */orders* that's integrated with the *SubmitOrderFunction*. Once we've done that, we need to enable CORS again. So we have to run some additional settings. Select all the options and then deploy to a new stage. So when we deploy our API, we actually deploy it to a stage and it provides a name that's part of the URL. And we need the API end point that includes the stage name. You'll see that when we get there. So firstly, let's come back what we need to do. We have the */orders* selected. So we're under resources */orders*. I need to click create method on the right hand side, we're going to choose POST. So these are all the various different HTTP methods, we're gpoing to choose POST. Because we're posting a request that's essentially uploading some information. Lambda function is the integration type. It is going to be a proxy integration as I've explained before, because we just send the information in the current form straight through to our *SubmitOrderFunction*. So make sure you choose the right function here (from the drop down box). That's all you need to do here. We can simply create the method. So now the method has been created. All we need to do is head back up to */orders*, click on enable CORS and then select all of these different options (in the CORS settings) and then save. So again, we're just making sure that we have the CORS settings enabled because the request is coming from another website. Once we've done that we can deploy our API, we deploy the API to a stage, we're going to create a new stage and the stage is going to be called *prod*, all lower case and then click on deploy. Now we have an invoke URL. So note that the invoke URL, it has a specific and unique identifier for your API and on the end it has prod. That was the stage that we deployed to. All of that's important information. So we want to copy the invoke URL. Now back in the instructions here, it's telling us to update the invoke URL in the *index.html* file. And the note here that it should it up, end up looking something like this. 
*'https://v1grynidwb.execute-api.us-east-1.amazonaws.com/prod/orders'*
Where we have */prod* and then */orders* on the end as well. So make sure you do that. Let's go to line 32 of our code here, *YOUR_API_ENDPOINT*, and paste it in, 
<br>
![Capture](https://github.com/user-attachments/assets/7168c348-c010-496d-b34e-b61fa162a1b9)



We've got the */prod*. So then I just need to type */orders*. So that remember that's the resource, the resource is part of the path, it's part of the URL path. So we have to add it on here for the request to work. Once you've done that, don't forget to save your *index.html* file. And what we want to do now is come back and create our static website. I've already got a couple, but I'll do it from scratch. So let's call this *myAPIstaticwebsite*-(add some random numbers on the end to make it unique) and then just make it unique. I do want to enable public access for this one. Let's acknowledge that I'm going to do that. Just create the bucket. We will choose our bucket, *myAPIstaticwebsite*. I'm going to go to properties, all the way to the bottom, static website hosting, enable. Index document is *index.html*. And then of course, we want to scroll down and save changes. I've got the bucket policy code that you need here. 
<br>

![Capture](https://github.com/user-attachments/assets/fb2d64ba-a968-41be-8187-59b620ad2758)

<br>

So what we'd want to do is just copy this code to allow public read of the objects. So back in our bucket, we can go to permissions, under bucket policy, edit, paste the code in and we need to of course update our bucket ARN, making sure the /* is still in there and then save the changes. <br>
![1](https://github.com/user-attachments/assets/22a1dda6-c4e6-4e74-bad2-1e9446128440)<br>
Now lastly, let's come back and we need to upload our file. So I've uploaded my *index.html*. I can close out of here. I've got the file, let's go to properties and I can click on the static website end point. <br>
![2](https://github.com/user-attachments/assets/3631540e-6676-4da6-bf6e-71ce420693e0)
 
![3](https://github.com/user-attachments/assets/0c820750-a1ab-4c94-855e-0c66da7c5663)<br>
So now I can enter a product name. Let's say it's going to be an iPhone and I want 1 of them and then let's submit and it says order submitted successfully. <br>
![4](https://github.com/user-attachments/assets/09200de8-807f-4f70-855a-fc08543744da)
![5](https://github.com/user-attachments/assets/af33914a-3d59-49c2-9e29-b0845c246ff2)

 
If I now come back to my DynamoDB table, let's refresh to scan the table and there is my iPhone.<br>
![6](https://github.com/user-attachments/assets/71fa4881-980c-43b2-a60d-52250fea5aca)<br>
So that is working correctly. 
Now, I'll show you a couple of places where things can go wrong. Firstly, the *index.html*, but make sure you've updated your endpoints and that you've got */prod* and */orders* in there on the end of your end point. So it should look very much like this one.

![7](https://github.com/user-attachments/assets/20b8174b-4d02-4852-8864-b0b6705f39fe)

 
The other place things can go wrong is back in the API. So again, I keep mentioning CORS, it can be something that you have to sometimes come back in and just do it again. So if for any reason it's not working, come back and enable CORS again, once you've done that, go and deploy the API again. <br>
![1](https://github.com/user-attachments/assets/0480e552-ec6d-4b9b-bbcc-672d2873f6c3)<br>
And when you deploy it for a second time, you can actually choose the stage.<br> 
![1](https://github.com/user-attachments/assets/54575f8b-aba3-4e4c-8acb-49d55557045b)

So deploy, wait a minute or two, sometimes the deployment of an API stage does take a minute or two to actually take effect. So that can be an issue as well. And then lastly, it's useful to use developer tools in your browser. If you want to troubleshoot what's going on. For example, for this, I prefer to use the Google Chrome and with Google Chrome, you go to view developer tools and then go to the network tab and then when we submit orders, we can see a bunch of information happening on the right hand side. 
 
![2](https://github.com/user-attachments/assets/31efffa9-beb9-4b90-808b-de2e5a6e6b41)

![3](https://github.com/user-attachments/assets/7eec0e18-b1cd-4217-b85c-361e6bc5fccf)
 

So for example, if this time I put in cheese and quantity 2 and then submit. We will see things happening, in this case. Obviously, everything is good. If you have any issues, then it should show you some sort of red Xs on the right hand side here and you can troubleshoot. It might be a CORS error, for example, or it might be a path not found or something like that. So the developer tools are very useful. So that's it for this lab. Our configuration is fully working now with a static website and an API and then our serverless application running in the cloud. 

![4](https://github.com/user-attachments/assets/65580842-a2fc-46b5-ad90-30827afd1a9d)<br>
So very easy obviously to delete all of these resources once you've finished, everything is within free tier, so shouldn't cost you anything anyway. But always a good idea to clean things up once you've finished.

<br>
<br>

## Amazon EventBridge

EventBridge is a serverless event bus that allows us to build event driven applications. EventBridge supports various different event sources. There's lots of integration for AWS services out of the box. So it's very easy to integrate with various different AWS services. But you can also integrate custom applications and SaaS applications as well. So the event sources generate events, things that are happening to those services. For example, an EC2 an instance might be terminated, that would be an event. Then the events get routed through the event bus into the rules engine where we can configure the rules that we require. And those rules will direct the traffic to various different targets like Lambda and Kinesis and SQS and others. So EventBridge helps us to ingest filter, transform and deliver events to build loosely coupled applications.<br>
![5](https://github.com/user-attachments/assets/a6907de0-4a46-49a5-abf8-fc59b0dafef8)

 
Event sources can be as I mentioned before, various different sources such as AWS services themselves, wide variety of support there for many AWS services. But then we can also utilize custom applications and SaaS applications for our sources where the events are being generated from. It supports a default event bus, which is there for AWS events. And then you can build custom event buses as well, for third-party applications. The events get routed and filtered. And so we have various different parameters that we can configure in the event rules in order to determine how we want our events to be routed and any customisations that we want to make. For scalability and reliability, it automatically scales as a serverless service based on the number of events. And it can handle millions of events per second. So very highly scalable and very reliable as well.<br>
![6](https://github.com/user-attachments/assets/3e68136f-4403-4909-88d4-0303c59ab00f)

 
Let's have a look at an example to help you understand how this works. Here we have EC2, we have an event that's been generated. In this case, it's a termination event. Now, this is an example of a very easy rule to create. There is actually a wizard that helps you to do this. You can choose EC2 as the event source and you can literally specify the EC2 instance terminated event. Then what we can do is apply those rules and route through to a target. In this case, it could be an SNS notification to let someone know that an instance has been terminated. <br>
![1](https://github.com/user-attachments/assets/4fd4499d-2687-48c4-b534-a89ee65cf52f)
 


Let's have a look at another example with EventBridge and AWS CloudTrail. CloudTrail is an auditing service. It logs all of the API actions in our account. So it keeps a record of who did what, at what time and on what resource. So it generates events, those events can then be put through to the event bus. So in this case, the S3:PutBucketPolicy API was used. That means somebody has applied a bucket policy to an S3 bucket. Maybe we want to know about that. So we create a rule in the event bus and it's going to pick up these *PutBucketPolicy* API actions. And then it's going to send them through to a target. In this case, Lambda. So what we might want to do here is secure the S3 bucket. Maybe we don't want somebody putting on a custom bucket policy. So that of course, is the logic that we would then have in our Lambda function to secure the S3 bucket. Likewise, we could easily just use an SNS notification again and notify someone that this has happened or keep a log of it in a DynamoDB table. Lots of power to do whatever we need to do. In this case, the bucket has now been secured. So those are a couple of examples of working with EventBridge.<br>
![2](https://github.com/user-attachments/assets/cbce3515-f9d9-4c6d-9641-325e776a2a57)
<br>
<br>




## Create an Amazon EventBridge Rule

We will create a simple Amazon EventBridge event bus that is going to automatically shut down any EC2 instances that aren't the t2.micro instance type. We need to create a Lambda function. In the Amazon EventBridge folder within the course download, you will find this function code. 

import boto3<br>
import json

def lambda_handler(event, context):<br>
    ec2 = boto3.client('ec2')<br>
    sns = boto3.client('sns')
    
    # Extract the instance ID from the event
    instance_id = event['detail']['instance-id']
    instance_type = ec2.describe_instances(InstanceIds=[instance_id])['Reservations'][0]['Instances'][0]['InstanceType']
    
    if instance_type != 't2.micro':
        # Stop the instance
        ec2.stop_instances(InstanceIds=[instance_id])
        print(f"Instance {instance_id} stopped because it is not a t2.micro.")
        
    else:
        print(f"Instance {instance_id} is a t2.micro. No action taken.")

    return {
        'statusCode': 200,
        'body': json.dumps('Lambda function execution completed.')
    }

All we need to do is simply copy this code and we're going to head to AWS Lambda. I'm going to create a function. It's going to be a Python function and let's call this *stopec2*. And it's gpoing to be the Python 3.9 run time and then simply create the function. Now, what I want to do is simply delete the function code.<br>
![1](https://github.com/user-attachments/assets/26ade5e9-b269-4985-a972-1cfe83b61b40)


Add in my code and then click on deploy.<br> 
![2](https://github.com/user-attachments/assets/f1e63e29-cb24-4dbe-8e9b-e4d71d14f3cb)
 

And that code is now deployed. So what this code will do is it's going to be triggered by Amazon EventBridge and it's going to check any EC2 instance that has entered the running state. That's what the trigger will cause. And it's going to check if it's a t2.micro. If it's not a t2.micro instance type, it's going to stop the instance. So what we do need to do of course is provide permissions. So on configuration permissions, click on the role name here to be taken over to IAM.<br> 
![3](https://github.com/user-attachments/assets/5622e6be-ec38-4b9b-9047-31d76909e919)

 

And we need some permissions for EC2. 
 

So I'm going to attach a policy type EC2and then just use the AmazonEC2FullAccess and then add those permissions. 
 

So that's the function ready. Now, we can head over to Amazon EventBridge. So let's find EventBridge - it is under services. In EventBridge we are going to create a rule. We'll give the rule a name, stopec2 and leave the rule with an event pattern selected.
 
Click on next, I'm gpoing to just close down this message here and we're going to leave the default here. AWS events or EventBridge partner events gpoing to go straight past the sample there, come straight the way down to where it says creation method. Skip the sample piece. It says use pattern form. We're gpoing to leave that selected. Event source is going to be AWS services, AWS service is gpoing to be EC2. So we can type EC2 and then choose the first option simply EC2. And then we're gpoing to choose the event type. I'm just gpoing to type EC2 instance, so I can just filter a little bit, there's quite a few events there and we want to use the EC2 instance state change notification. So now it's starting to create the event pattern in json format. 
 

Now we do want to choose a specific state. So rather than any state,we want to choose a specific state and then select running.

 

 

So what this means is every time an instance enters the running state, this rule is going to trigger an action. So on any instance, by the way, you could choose specific instance Ids if you wanted to, of course, we want this to be a general rule. 

So now it's filled out the event pattern for me, I can click on next, choose AWS service as the target. 
 
And then what we're going to do in here is search for Lambda, choose Lambda function and then choose my stopec2 function. Notice that you can add additional targets. So you might want a notification as well for example, with SNS. So here, what we'll do is click on next, click on next again and then simply create this rule. So the rules created and it should work. So let's head over to the EC2 console. And what we're going to do is we're going to launch a couple of instances. The first one will be a t2.micro. So I'll just call this t2.micro so that we can recognize it even more easily in the console. 
 

And then we're going to leave t2.micro. I don't need a Key pair, 
 

I just select any existing security group just so that it doesn't create one for me and then launch that instance. 
 

So that's my first instance, launching this one should enter the running state and it should remain in the running state. Let's launch another one, call this the Non t2 and choose any other instance type. 
 

So I'm gpoing to choose a t2.medium, proceed without a key pair. Select the security group. Just gpoing to choose one here. I don't really care too much which one it is. 
 

And let's try and launch this instance and see what happens. So once they enter the running state at that point in time, the function should be triggered. So the t2.micro here is certainly running. That's not a problem. It's staying up, if I refresh, yeah, seems to be fine. So let's see what happens when our non t2.micro, the t2.medium enters the running state. There we go, it's entered the running state and straight away, I just gave it a refresh almost instantly it changed straight into the stopping state. 
 

If we come back to our Lambda function, go to monitor, click on view CloudWatch logs. 
 
 
 

And now we can see there's a log stream, let's click on the log stream, expand the events and we can see in here quite a few messages. 
 
 
And this one here is informative. It says stopped because it is not a t2.micro. 
 
And if you go back and try and start it, it's just gpoing to keep doing the same thing. So our t2.micro is fine but the non t2.micro is not, it's been stopped. So that's it basically showing you how to use an EventBridge rule. I'm going to terminate my EC2 instance. And of course, you can go and clean up your EventBridge and your Lambda function as well.













Amazon Event-Bridge code:

import boto3
import json

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    sns = boto3.client('sns')
    
    # Extract the instance ID from the event
    instance_id = event['detail']['instance-id']
    instance_type = ec2.describe_instances(InstanceIds=[instance_id])['Reservations'][0]['Instances'][0]['InstanceType']
    
    if instance_type != 't2.micro':
        # Stop the instance
        ec2.stop_instances(InstanceIds=[instance_id])
        print(f"Instance {instance_id} stopped because it is not a t2.micro.")
        
    else:
        print(f"Instance {instance_id} is a t2.micro. No action taken.")

    return {
        'statusCode': 200,
        'body': json.dumps('Lambda function execution completed.')



AWS Lambda code:

Create a Lambda that logs a message to CloudWatch Logs
1.	Create a Lambda function with the Python runtime and the following code
import logging
import json

# Configure the logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    # Extract the message from the event. Assuming the input is a simple JSON object {"message": "your message here"}
    message = event.get('message', 'No message provided')
    
    # Log the message
    logger.info(message)
    
    return {
        'statusCode': 200,
        'body': json.dumps('Message logged successfully!')
    }
2.	Create a test event in the console and add the following test data
{
  "message": "Hello, CloudWatch!"
}
3.	Run the test and then view the message in CloudWatch Logs
4.	Test using the CLI. Create a file in CloudShell named "payload.json" with the following code:
{
  "message": "Hello from CLI!"
}
5.	Run the following command in AWS CloudShell
aws lambda invoke --function-name <function-name> --payload fileb://payload.json response.json
Create an event notification for S3 upploads
In this exercise we'll modify the function to write the names of files uploaded to an S3 bucket to CloudWatch Logs
1.	Update the lambda function code and deploy the update
import json
import logging
import boto3

# Initialize logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    # Log the raw event
    logger.info("Event: " + json.dumps(event))
    
    # Process each record within the event
    for record in event['Records']:
        # Extract the bucket name and file key from the event
        bucket_name = record['s3']['bucket']['name']
        file_key = record['s3']['object']['key']
        
        # Log the bucket name and file key to CloudWatch
        logger.info(f"New file uploaded: {file_key} in bucket {bucket_name}")
    
    return {
        'statusCode': 200,
        'body': json.dumps('Processed S3 upload event successfully!')
    }
2.	Edit the execution role to add permissions to Lambda to read from S3
3.	Create an event notification for all S3 object create events by adding a trigger to AWS Lambda
4.	Upload a file and check if a message is written to CloudWatch Logs that includes the file na







Serverless App:


ProcessOrderFunction.py

import json
import boto3
from datetime import datetime

dynamodb = boto3.resource('dynamodb')
table_name = 'YOUR_DYNAMODB_TABLE_NAME'
table = dynamodb.Table(table_name)

def lambda_handler(event, context):
    for record in event['Records']:
        message_body = json.loads(record['body'])
        item = {'orderId': record['messageId'], 'productName': message_body['productName'], 'quantity': message_body['quantity'], 'orderDate': datetime.now().isoformat()}
        table.put_item(Item=item)
    return {'statusCode': 200, 'body': json.dumps({'message': 'Orders processed successfully'})}


SubmiOrderFunction.py

import json
import boto3

sqs = boto3.client('sqs')
queue_url = 'YOUR_SQS_QUEUE_URL'

def lambda_handler(event, context):
    try:
        order_details = json.loads(event['body'])
        response = sqs.send_message(QueueUrl=queue_url, MessageBody=json.dumps(order_details))
        return {'statusCode': 200, 'headers': {'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Headers': 'Content-Type', 'Access-Control-Allow-Methods': 'OPTIONS,POST'}, 'body': json.dumps({'message': 'Order submitted to queue successfully'})}
    except Exception as e:
        return {'statusCode': 400, 'body': json.dumps({'error': str(e)})}




Index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Product Order Form</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f4f4; margin: 0; padding: 0; display: flex; justify-content: center; align-items: center; height: 100vh; }
        .container { background-color: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); width: 300px; }
        h1 { color: #333; text-align: center; }
        form { display: flex; flex-direction: column; }
        input[type=text], input[type=number] { padding: 10px; margin: 10px 0; border-radius: 4px; border: 1px solid #ddd; box-sizing: border-box; }
        input[type=button] { background-color: #007bff; color: white; padding: 10px; border: none; border-radius: 4px; cursor: pointer; }
        input[type=button]:hover { background-color: #0056b3; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Submit a Product Order</h1>
        <form id="orderForm">
            <label for="productName">Product Name:</label>
            <input type="text" id="productName" name="productName">
            <label for="quantity">Quantity:</label>
            <input type="number" id="quantity" name="quantity">
            <input type="button" value="Submit" onclick="submitOrder()">
        </form>
    </div>
    <script>
        async function submitOrder() {
            const productName = document.getElementById('productName').value;
            const quantity = document.getElementById('quantity').value;
            const response = await fetch('YOUR_API_ENDPOINT', {
                method: 'POST',
                body: JSON.stringify({ productName, quantity }),
                headers: { 'Content-Type': 'application/json' }
            });
            if (response.ok) { alert('Order submitted successfully!'); document.getElementById('orderForm').reset(); }
            else { alert('Failed to submit order.'); }
        }
    </script>
</body>
</html>






Serverless App instructions

Serverless Application with REST API – Part 1
1. Create the first Lambda function
1.	Create a Lambda function with the following settings
•	Name: SubmitOrderFunction
•	Python 3.9 runtime
2.	Add the code from the SubmitOrderFunction.py file
3.	Add the AmazonSQSFullAccess permissions policy to the execution role
you need to come back and add the queue URL shortly
2. Create the SQS queue
1.	Create an SQS queue
2.	Use the standard queue type
3.	Name it: ProductOrdersQueue
4.	Copy the queue URL and add it to line 5 of the SubmitOrderFunction code
5.	Deploy the Lambda function
3. Test order submissions
1.	In Lambda create and submit a test event with the following data
{
  "body": "{\"productName\":\"Test Product\",\"quantity\":3}"
}
2.	Go to SQS and poll for messages - you should see a message waiting the queue
4. Create the processing function
1.	Create a Lambda function with the following settings
•	Name: ProcessOrderFunction
•	Python 3.9 runtime
2.	Add the code from the ProcessOrderFunction.py file
3.	Add the AmazonSQSFullAccess and AmazonDynamoDBFullAccess permissions policies to the execution role
5. Create the DynamoDB table
1.	Create a DynamoDB table with the following settings
•	Name: ProductOrders
•	Primary key: orderId
6. Deploy and test the application
1.	Add the table name to line 6 of the ProcessOrderFunction function code
2.	Go to SQS and configure a Lambd trigger and specify the ProcessOrderFunction
3.	Check the DynamoDB table to see if the first test event was processed
4.	Test using the CLI. Using CloudShell create a file named input.json with the following contents
{
  "body": "{\"productName\":\"Test Product 2\",\"quantity\":2}"
}
5.	Invoke the function:
aws lambda invoke --function-name <function-name> --payload fileb://input.json output.json
Serverless Application with REST API – Part 2
1. Create the API
1.	Create a REST API in the API Gateway Console named ProductOrdersAPI
2.	Create a new resource /orders and enable CORS
3.	Create a POST method for /orders integrated with the SubmitOrderFunction
4.	Enable a Lambda proxy integration
5.	Click back up to the /orders resource and click "Enable CORS"
6.	Select all CORS options
7.	Deploy your API to a new stage named prod
8.	Update the invoke URL in the index.html file on line 32 where it says YOUR_API_ENDPOINT
note, the invoke URL on line 32 should include /prod/orders on the end and look like this example
'https://v1grynidwb.execute-api.us-east-1.amazonaws.com/prod/orders'
2. Create the static website bucket and test the application
1.	In Amazon S3 create a bucket
2.	Configure the bucket for static website hosting
3.	Set the default document to index.html
4.	Enable public access using a bucket policy
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "<YOUR-BUCKET-ARN>/*"
        }
    ]
}
5.	Upload the edited index.html that has the API endpoint URL configured
6.	Navigate to the static website endpoint
7.	Submit some order information and check it is added to the DynamoDB table
8.	If you do not receive an "Order submitted successfully!" response, use your browsers Developer Tools to troubleshoot


# Serverless Application with REST API – Part 1

## 1. Create the first Lambda function

1. Create a Lambda function with the following settings
- Name: SubmitOrderFunction
- Python 3.9 runtime

2. Add the code from the `SubmitOrderFunction.py` file
3. Add the `AmazonSQSFullAccess` permissions policy to the execution role

***you need to come back and add the queue URL shortly***

## 2. Create the SQS queue

1. Create an SQS queue
2. Use the standard queue type
3. Name it: `ProductOrdersQueue`
4. Copy the queue URL and add it to line 5 of the SubmitOrderFunction code
5. Deploy the Lambda function

## 3. Test order submissions

1. In Lambda create and submit a test event with the following data

```json
{
  "body": "{\"productName\":\"Test Product\",\"quantity\":3}"
}
```

2. Go to SQS and poll for messages - you should see a message waiting the queue

## 4. Create the processing function

1. Create a Lambda function with the following settings
- Name: ProcessOrderFunction
- Python 3.9 runtime

2. Add the code from the `ProcessOrderFunction.py` file
3. Add the `AmazonSQSFullAccess` and `AmazonDynamoDBFullAccess` permissions policies to the execution role

## 5. Create the DynamoDB table

1. Create a DynamoDB table with the following settings
- Name: ProductOrders
- Primary key: orderId

## 6. Deploy and test the application

1. Add the table name to line 6 of the ProcessOrderFunction function code
2. Go to SQS and configure a Lambd trigger and specify the ProcessOrderFunction
3. Check the DynamoDB table to see if the first test event was processed
4. Test using the CLI. Using CloudShell create a file named `input.json` with the following contents

```json
{
  "body": "{\"productName\":\"Test Product 2\",\"quantity\":2}"
}
```

5. Invoke the function:

```bash
aws lambda invoke --function-name <function-name> --payload fileb://input.json output.json
```

# Serverless Application with REST API – Part 2

## 1. Create the API

1. Create a REST API in the API Gateway Console named `ProductOrdersAPI`
2. Create a new resource `/orders` and enable CORS
3. Create a `POST` method for `/orders` integrated with the `SubmitOrderFunction`
4. Enable a Lambda proxy integration
5. Click back up to the `/orders` resource and click "Enable CORS"
6. Select all CORS options
7. Deploy your API to a new stage named `prod`
8. Update the invoke URL in the index.html file on line 32 where it says `YOUR_API_ENDPOINT`

***note, the invoke URL on line 32 should include /prod/orders on the end and look like this example***

'https://v1grynidwb.execute-api.us-east-1.amazonaws.com/prod/orders'

## 2. Create the static website bucket and test the application

1. In Amazon S3 create a bucket
2. Configure the bucket for static website hosting
3. Set the default document to `index.html`
4. Enable public access using a bucket policy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "<YOUR-BUCKET-ARN>/*"
        }
    ]
}
```

5. Upload the edited index.html that has the API endpoint URL configured
6. Navigate to the static website endpoint
7. Submit some order information and check it is added to the DynamoDB table
8. If you do not receive an "Order submitted successfully!" response, use your browsers Developer Tools to troubleshoot
