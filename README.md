![image](https://user-images.githubusercontent.com/72955854/220009519-8a178b7c-0321-4c1e-acee-51c36d769fbb.png)

# Hanami Live translator 

This is Hanami, the live translator that captures any audio that comes from a WINDOWS speaker and microphones. Keep in mind that this is not an official product nor a research topic. It is a toy-proof of concept that may help some people find inspiration. Use it at your own risk. To use it, you select the speaker that you want the application to listen to (usually it’s the one that appears when you click on the speaker icon next to the taskbar clock) and select the source and target language. That’s it! now the spoken words will be translated! The app uses lightweight multiprocessing and processes audio in chunks where each chunk takes about 3 - 5 seconds to get processed.
[![image](https://user-images.githubusercontent.com/72955854/217488774-75487561-2d4d-4a65-ac68-3d1490bbff30.png)](https://github.com/MotazSabri/Hanami-release/releases/download/Hanami-Release/Hanami.Installer.exe)

## WorkFlow
The application will capture any sound that comes from the WINDOWS machine regardless of the app. (google meet, Microsoft teams, zoom, YouTube video, or anything that uses the speaker). The process uses low-level access to create hardware loopback which means even if the speaker is muted, it can listen to the content as it hooks itself to the motherboard audio stream. To run, the application will do the following:
1. Via threading the app will listen to your speaker and make a temporary denoised wav file every 5 seconds. This thread is always running resulting in no audio gaps. 
2. The wav file content is turned into a NumPy array that is scaled and later transduced to text through google API given the source language.
3. Via threading the app will translate the text (given the source language) to the target language via apis.

The chart below shows the main steps that the Hanami Live translator takes to operate. With its three stages, it makes sure that there are no gaps between its audio chunks. The audio buffers and the resource utilization are maintained all the time to avoid any memory overhead. Needless to say, Hanami is an online system and requires an internet connection to operate. It checks access to the operating servers as soon as it starts and operates depending on the connection status. 

![image](https://user-images.githubusercontent.com/72955854/216901818-7dc7630b-af0e-4d93-ae42-509c7d167523.png)

The table below shows the task vs time for every chunk. The app ensures all its calls are thread-safe and clean its cache, buffers, and pipes once a task is complete. 


![image](https://user-images.githubusercontent.com/72955854/216901102-7cbcbd13-7a14-4cc2-9403-d45a39eb9f57.png)


## Time Complexity 
The delay between the beginning of the speech and the translation appearing is 4-5 seconds which includes the fixed 4 seconds of listening + 1 second to transduce speech to text and 1 second to translate the text.
 All app operations are logged to Traces.log in the OS temp file allowing further debugging and optimization at the client side. No information is shared with the app developers.
 
## Components
The application is operated by Python, it uses soundcard to capture the audio signal (which is a python Library), SpeechRecognition to convert binary audio to text (which is also a python library), and selenium to simulate web calls for deepl servers without API calls (leading to free of charge translations), The selenium (which is a python library) requires a browser to be available in the current environment. To avoid conflict in other environments, a portable version of google chrome with its matching chrome driver is provided with the app.

## Running The application
You can also run the standalone version of Hanami (beta) from here with no dependency. As soon as you run the app, Hanami will check for updates and make sure you have the latest version. It also checks the connection with all translation servers starting. When the starting-up phase is complete, the app becomes usable. The layout of simplistic as shown below.

![image](https://user-images.githubusercontent.com/72955854/218648169-cacb6c7e-ca48-43d4-a35a-3d720aa7688a.png)

1.	The list of devices is loaded automatically as soon as the app is launched. the default speaker is selected. Listening starts as soon as the app starts. Users may update the speaker (or microphone) at any time via this list. The app will finish processing the last chunk and switch to the newly selected device as soon as the resource becomes available. 
2. The Hanami operating status has three modes that are triggered automatically depending on the speaker device condition. The color of the small label next to the devices list will indicate the app status. once an audio device is selected, the app will be under the listening status, and the status label will be yellow. If the app failed to detect any audio signal from the selected source for any reason (there was no detected audio or the user selected an incorrect speaker, etc.) then the label color will be red. Finally, if the app captured the audio correctly and started the audio operations (text to speech and the translation) then the label color will be green.


![image](https://user-images.githubusercontent.com/72955854/216913404-f8ff936c-aaf1-4e3d-bbf5-fa47be424e55.png)
3. User may update the devices list from the refresh menu item. the refresh functionality will automatically select the selected device upon completing the refresh process.
4. To ease the readability, the user may hide the control panel that contains the device list and the selected languages. the user can toggle the visibility of the control panel by clicking the control visibility button again.
5. To improve readability for all users, the day/night mode button will allow the user to change the entire look and feel of the experience from dark mode to light mode. Users may switch between modes by clicking on the day/night button again.
6. User can pin the app to be always on top by clicking the pin button. The user may unpin the app by clicking on the pin button one more time.
7. The user may close the app and all its operations by clicking the close button. the app will close all the threads and running services before shutting itself down.
8. The user may select between a wide list of input languages. The target language and source language can’t be the same. therefore, any trial to set them to an equal value will trigger a protection switch.
9. The user may select between a wide list of target languages. some languages text to speech and translation are more capable than others. to indicate language strength, a star is added next to the language with higher robustness.
10. The translation content is shown in the translation area with a font that matches the color mode of the app.
11. Users may visit our site to place a request, show support or suggest an improvement.
[12,13,14,15,16]. The user can use the translation navigator to go through previously translated content.

## What are we doing next
1.	✅Create night mode and day mode to ease readability. 
2.	✅Introduce basic noise reduction to empower the speech-to-text features.
3.	✅Introduce enhanced logging to debug issues reported by users.
4.	✅Support microphones listening.
5.	✅Introduce weakly supported languages.
6.	✅Introduce enhanced word tokenization to prevent gaps and overlaps between spoken statements.
7.	✅Introduce Hanami modes to avoid computational overheads.
8.	✅Add translation navigation, resuming, and pausing.
9.	✅Add a single channel. double-channel and quadruple channel audio device support.
10.	Add more stability to audio regularization and noise reduction, especially in weakly supported languages.
11.	Introduce speaker identification to ultimately create a meeting log with speakers and their corresponding sentences.
12.	Introduce a meeting summary in which every session log will be stored in a google doc with a summary of the meeting at the end.
13.	create a knowledge tree where all the meeting logs are appended together to create a robust backend that can answer any question about whatever was spoken and heard by Hanami.

## Disclaimer

Use of this service is limited only to non-sensitive content. Users must not use, share, or store any kind of sensitive data like health status, provision or payment of healthcare, Personally Identifiable Information (PII), and/or Protected Health Information (PHI), etc. under ANY circumstance.
The material embodied in this software is provided to you "as-is" and without warranty of any kind, express implied, or otherwise, including without limitation, any warranty of fitness for a particular purpose. In no event shall any agency or government be liable to you or anyone else for any direct, special, incidental, indirect, or consequential damages of any kind, or any damages whatsoever, including without limitation, loss of profit, loss of use, savings or revenue, or the claims of third parties, has been advised of the possibility of such loss, however, caused and on any theory of liability, arising out of or in connection with the possession, use or performance of this software.

