This document tells you how to set up your very own back-end server for remix.js.

This involves being able to run your own Python server, your own Amazon S3 bucket, and making a few tweaks to remix.js and sandbox.html.  This is not an impossible task, but it's not trivial.  If you want to do easy uploading, you can work with [sandbox.html](http://echonest.github.io/remix/js/examples/sandbox) and use the Echo Nest's servers...but the audio files on those servers are cleared every week. If you want to make your own back-end server, read on.


How sandbox.html works:
----------------------

First, we need to know how sandbox.html uploads audio.  Uploading takes place in three parts:  picking a track and doing the upload, giving the user feedback on the upload and analysis process, and then loading & remixing the uploaded track.  

Picking a track:  This code runs if there's nothing in the query string.  The JavaScript updates the filename of the MP3, gets the Amazon policy document that allows uploading from the back-end server, and updates the amount of time that the analysis will take.  Then, in the HTML, the select-track div contains the form that sends the data to S3. 

Giving the user feedback:  This code runs if there's a parameter called 'key' in the query string.  The JavaScript here basically waits:  the analyzeAudio function queries the back-end server to see if the analysis is done.  If the analysis is not done, we wait 8 seconds, and call the function again.  If the analysis is done, we redirect to the same URL with the track ID in the query string.  

Remixing:  This code runs if there's a parameter call 'trid' in the query string.  The getProfile function queries the back-end server to find the audio URL for the track ID, and then starts the remixing process.  

How the server works:
----------------------
The Python server is responsible for sending the audio to the analysis server, and then matching Echo Nest analysis data to the URLs of the uploaded audio.  
It also keeps track of the estimated analysis time, and provides the Amazon Policy document that allows uploading audio to Amazon S3.
Thus, it has to have access to your Amazon S3 key and secret.

How Amazon S3 works:
----------------------
S3 stores the uploaded audio, which is then referenced by the Python server.  


How things connect:
----------------------

Picking a track: 
The fetchSignature function in remix.js asks the Python server for an Amazon S3 Policy document that will allow audio to be uploaded to S3.
For details on S3 Policies, go here:  http://docs.aws.amazon.com/AmazonS3/latest/dev/AccessPolicyLanguage_UseCases_s3_a.html
Or, modify our example policy:  [LINK].  
You will need to replace the server URL with your own server. 
On the server side, you'll need to insert your own bucket name, bucket path, and Amazon key

The fetchQinfo function in sandbox.html asks the Python server how much time the analysis will take.  
You will need to replace the server URL with your own server. 
On the server side, you don't need to change anything.

Giving the user feedback: 
The analyzeAudio function in sandbox.html asks the Python server if the analysis of the uploaded audio is complete.
You will need to replace the server URL with your own server. 
On the server side, you don't need to change anything.

Remixing:  The getProfile function in remix.js asks the Python server for where the audio file for a given Track ID is.  
You will need to replace the server URL with your own server. 
On the server side, you don't need to change anything.

