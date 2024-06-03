![image](https://github.com/MotazSabri/Hanami-release/assets/72955854/072bc9b2-c626-4c9f-8ebe-a342afab393d)

# Hanami Live translator 

This is Hanami, the live translator that captures any audio that comes from a WINDOWS speaker and microphones. Keep in mind that this is not an official product nor a research topic. It is a toy-proof of concept that may help some people find inspiration. Use it at your own risk. To use it, you select the speaker that you want the application to listen to (usually it’s the one that appears when you click on the speaker icon next to the taskbar clock) and select the source and target language. That’s it! now the spoken words will be translated! The app uses lightweight multiprocessing and processes audio in chunks where each chunk takes about 3 - 5 seconds to get processed.
[![image](https://user-images.githubusercontent.com/72955854/217488774-75487561-2d4d-4a65-ac68-3d1490bbff30.png)](https://github.com/MotazSabri/Hanami-release/releases/download/Hanami-release1.9/Hanami.Installer.exe)

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
The application is operated by Python, it uses soundcard to capture the audio signal (which is a python Library), SpeechRecognition to convert binary audio to text (which is also a python library), and selenium to simulate web calls for deepl and openAI servers without API calls (leading to free of charge translations), The selenium (which is a python library) requires a browser to be available in the current environment. To avoid conflict in other environments, a portable version of google chrome with its matching chrome driver is provided with the app.

## Running The application
You can also run the standalone version of Hanami (beta) from here with no dependency. As soon as you run the app, Hanami will check for updates and make sure you have the latest version. It also checks the connection with all translation servers starting. When the starting-up phase is complete, the app becomes usable. The layout of simplistic as shown below.

![image](https://github.com/MotazSabri/Hanami-release/assets/72955854/484e4c48-00ed-47b5-98a0-b23888db6aae)
![image](https://github.com/MotazSabri/Hanami-release/assets/72955854/1fdedafb-771c-40f9-a803-7ae3052cf7bb)

1.	The list of devices loads automatically as soon as the app is launched. The default speaker is selected, and listening begins immediately. Users can update the speaker or microphone at any time via this list. The app will finish processing the last chunk and switch to the newly selected device as soon as the resource becomes available.
2. TThe Hanami operating status has three modes that are triggered automatically depending on the speaker device condition. The color of the small label next to the device list indicates the app status. Once an audio device is selected, the app enters listening mode, and the status label turns yellow. If the app fails to detect any audio signal from the selected source for any reason (e.g., no detected audio or an incorrect speaker selection), the label color turns red. If the app captures the audio correctly and starts the audio operations (text-to-speech and translation), the label color turns green.
![image](https://user-images.githubusercontent.com/72955854/216913404-f8ff936c-aaf1-4e3d-bbf5-fa47be424e55.png)
3. Users can update the device list from the refresh menu item. The refresh functionality will automatically select the previously selected device upon completing the refresh process.
4. For easier readability, users can hide the control panel that contains the device list and selected languages. The user can toggle the visibility of the control panel by clicking the control visibility button again.
5. To improve readability for all users, the day/night mode button allows users to switch the entire look and feel of the experience between dark mode and light mode. Users can switch between modes by clicking the day/night button again.
6. Users can pin the app to always stay on top by clicking the pin button. The app can be unpinned by clicking the pin button once more.
7. Users can close the app and all its operations by clicking the close button. The app will close all threads and running services before shutting down.
8. Users can select from a wide list of input languages. The target language and source language cannot be the same; therefore, any attempt to set them to an equal value will trigger a protection switch. Listening to languages marked with a ☆ offers better transcription.
9. Users can select from a wide list of target languages. Some languages' text-to-speech and translation capabilities are stronger than others. To indicate language strength, a star is added next to languages with higher robustness. Translation to languages marked with a ☆ offers better quality.
10. Users can choose whether they want to use the translation service, the text-to-speech service, or a mixture of both. The names of Hanami services are:
- Translate (Translation Feature): Transforms input language audio to target language text.
- Contrans (Concatenated Translation): Appends the last processed translation to the current one. The old translation appears on top in a smaller font, followed by the current (latest) translation at the bottom in a larger font.
- Transcript (Transcription Feature): Converts detected audio to input language text.
- Mixture (Mixture Feature): Appends transcribed text with translated text. The transcription appears on top in a smaller font, followed by the translation at the bottom in a larger font.

11. The translation content is shown in the translation area with a font that matches the app's color mode.
12. Users may visit our site to place a request, show support, or suggest improvements.
13. Users can use the translation navigator to browse previously translated content.
14. Users can erase all translation logs and chat history using the erase content feature. This is useful for continuous meetings where the user wishes to have separate contexts for further analysis.
15. Users can store all translation and transcription logs to a text file. The file will be placed on the desktop as a text file. If users flush the logs using the reset content button, no logs will be saved until the audio detects more spoken content.
16. Users can interact with their detected content using the GPT-based feature. Users can ask questions, request analysis, brainstorm ideas related to the data that Hanami has heard. Advanced requests such as creating meeting agendas, summarizing content, and more can be made. This GPT service is built on top of OpenAI GPT. Please be cautious about what you share, as it may be used for OpenAI model training.
17. Within the GPT window, users will receive a system message with a yellow label. Users cannot copy this message (no copy button).
18. Users can view their own requests, marked with a green label.
19. Users can get the GPT response within the GPT window and may copy this text.
20. The copy button overwrites the content in the user's clipboard. Please ensure your clipboard content is not lost.
21. Users can interact with their data using the textbox, which only allows text entry. As soon as users enter text, the submit button becomes enabled and allows submitting the prompt.
22. The submit button triggers a GPT request, which, once completed, will be shown to the user in the chatbox window.

Please ensure not to share any sensitive information with Hanami.

### Customize Layout

Hanami is designed to be an extension, not a full-windowed application. However, users may make minimal layout changes to alter:

1. **Sub Text Fonts**: Modify the fonts used for subtext in Mixture Mode and ConTrans Mode.
2. **Text Fonts**: Adjust the fonts used for the main application services, such as translation and transcriptions.
3. **GPT Window Font**: Change the font in the GPT window, which is smaller than the main app screen and uses a smaller font by default.
4. **Sample Rate**: Control the audio quality for transcription and translation services. A higher value means better results but at a slower rate.
5. **Application Resolution**: Manage the app's width and height in two modes:
   - **Extended Mode**: When the top controls are visible.
   - **Compact Mode**: When the top controls are collapsed. 
Users can customize the dimensions of the app in these two modes.

User may find these variables at Hanami directory under Assets folder in Interface_structure.json
![image](https://github.com/MotazSabri/Hanami-release/assets/72955854/ffd136f6-9c98-4938-8c35-042eaa9648b5)


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
10.	✅Add more stability to audio regularization and noise reduction, especially in weakly supported languages.
11.	Introduce speaker identification to ultimately create a meeting log with speakers and their corresponding sentences.
12.	✅Introduce a meeting summary in which every session log will be stored in a google doc with a summary of the meeting at the end.
13.	✅create a knowledge tree where all the meeting logs are appended together to create a robust backend that can answer any question about whatever was spoken and heard by Hanami.

## Disclaimer

Use of this service is limited only to non-sensitive content. Users must not use, share, or store any kind of sensitive data like health status, provision or payment of healthcare, Personally Identifiable Information (PII), and/or Protected Health Information (PHI), etc. under ANY circumstance.
The material embodied in this software is provided to you "as-is" and without warranty of any kind, express implied, or otherwise, including without limitation, any warranty of fitness for a particular purpose. In no event shall any agency or government be liable to you or anyone else for any direct, special, incidental, indirect, or consequential damages of any kind, or any damages whatsoever, including without limitation, loss of profit, loss of use, savings or revenue, or the claims of third parties, has been advised of the possibility of such loss, however, caused and on any theory of liability, arising out of or in connection with the possession, use or performance of this software.

