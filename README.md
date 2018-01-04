# SmartCap

Smart Cap is an assistant for visually impaired which narrates the description of scene by taking pictures from webcam.

Why:

There are about 285 million visually impaired people in the world. They are not able to experience the world the way we do. Smart cap aims to provide this missing experience for them. The system uses state of the art deep learning techniques from Microsoft Cognitive Services for image classification and tagging. 

What:

The smart cap aims bring the beautiful world as a narrative to the visually impaired. The narrative is generated by converting the scenes in front of them to text which describes the important objects in the scene. Examples of text include 'A group of people playing a game of football', 'yellow truck parked next to the car', a bowl of salad kept on table'. For the first prototype of the system, one line along with some keywords are played as an audio to the users but in the later versions a detailed description would be added as the feature.

How:

The architecture of the system includes Amazon Echo, Dragonboard 410c/Raspberry Pi and online computer vision API's. 

A webcam which is retrofitted into a regular cap is connected to the Dragonboard/Raspberry Pi. The code given here runs on Dragonboard 410c/Raspberry Pi. The function of the code is to capture the image from the webcam and send it to Microsoft API's for recognition task. The response is then inserted to DynamoDB. 
When the user asks Alexa to describe the scene, the Alexa Skills Kit triggers Amazon Lambda function to fetch the data from the database (DynamoDB). The correct text is the played as an audio on the Alexa device.


#Process to be followed for Dragonboard/Raspbery pi after getting linux OS up and running

# Update the system
1. <pre>sudo apt-get update</pre>
2. <pre>sudo apt-get upgrade</pre>

# [Terminal] Get required libraries
3. <pre>sudo apt-get install python-pip</pre>
4. <pre>sudo apt-get install libopencv-dev python-opencv</pre>
5. <pre>pip install matplotlib</pre>

# Configure for AWS
6. <pre>pip install boto3</pre>
7. <pre>pip install awscli</pre>
8. Go to https://console.aws.amazon.com/iam/
   a. Users -> <yourname> -> Security Credentials -> 'Create Access Key'
9. <pre>sudo aws configure</pre> and use the credentials (from above step)

# Get your key for Microsoft Vision API's 
10. Login to http://manage.windowsazure.com with your Microsoft Account
11. Go to New -> Marketplace ->ComputerVision API's (complete the form)

# Clone the code
12. <pre>git clone https://github.com/TusharChugh/SmartCap.git</pre>
13. Open ms_visionapi and paste the key that you got from previous section

# AWS Dynamo DB
14. Login to your Amazon Console - https://console.aws.amazon.com/
15. Go to Services -> DynamoDb -> Create Table
16. Give table name (smartcap) and Primary partition key as guid (String). Click Done

# AWS Lambda Function 
17. Login to your Amazon Console - https://console.aws.amazon.com/
18. Go to Services -> Lambda -> New (seach for alexa and get the template related to color app)
19. Copy the code given in 'aws_dynamodb.py' to your lambda function
20. Change the table name in the code

# AWS Alexa Skills Kit  
21. Got to https://developer.amazon.com/edw/home.html#/skills  
22. Click on Add a new skill    
23. Skill Information - Custom Skills, Give name like 'Smart Cap'  
24. Go to interaction model and copy the code from aws_ask.json and paste it in Intent Schema  
25. From 'aws_ask_helper.txt' file use custom slot types and sample utterances  
26. Click save and make sure there are no errors  
27. In the configuration tab: In the Endpoint past your Amazon Resource Name (Go to your Amazon lambda function to find it, would be something like  arn:aws:lambda:us-east-1:xxxx:function:xxxx)  

# Install the Alexa app and smart cap skills
28. Install the Alexa app on your phone and login with your credential
29. Enable smartcap skills (or your own skills)

# When you are done with all the above steps
#Testing instructions
1. Speak to Amazon Echo - "Alexa start smart cap" (you should hear the response as: "Sure, You can ask me to describe the scene")
2. Speak to Amazon Echo - "Alexa ask smart cap" -wait- "describe the scene"" (you should hear the response as: "No data received from device in past one minute"). This makes sure that the Alexa skills kit and dynamoDb are working as expected.
3. Get the userId. Speak to Amazon Echo - "Alexa ask smart cap to get the user info" (you should hear a long code)
4. Open http://alexa.amazon.com/ and login
5. In the userId card, you would see a long string
6. Copy the userId and paste it in aws_dynamodb.py file
7. Make sure you have python 2.7.9 +. [Terminal] which python. [Terminal] python --version
8. Run camera_image.py: <pre>python camera_image.py</pre> (You should see the images in the same folder)
9. Run ms_visionapi.py: <pre>python ms_visionapi.py</pre> (You should see the results in the terminal)
10. Run aws_dynamodb.py: <pre>python aws_dynamosb.py</pre> (Note: this might require sudo access depending on if you used sudo while doing aws configure. It will tell you if update item succedded for dynamodb)
11. Alternatively, you can use the autorun script instead of step 9 and 10. <pre>sh autorun.sh</pre>
12. Speak to Amazon Echo - "Alexa start smart cap" - wait - "describe the scene". If everything went well, you should now hear some relevant to the image that was capture by the camera 

Example: 'I think it is a yellow truck going on the road and the keywords are road, car, trees, sky'










