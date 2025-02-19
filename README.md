# Support Azure OpenAI ChatGPT Here

1. Build and publish this Azure Function:

https://github.com/freistli/chatgpt-api/tree/main/demos/demo-azure-chatgpt-function

Follow its readme to add environment varibles (your Auzre OpenAI Key and Endpoint) for the Azure Function

Get the function Url (azureopenai or azprompter), it will be used in the step 2.


2. Follow the [Setup](#setup) Guide and then run this command

```
dotnet user-secrets set "AzureOpenAI:ChatGPTUrl" "{Azure ChatGPT Azure Function Url}"
```
<img width="745" alt="image" src="https://user-images.githubusercontent.com/8623897/232411060-927ea765-d7c6-4d6f-93e6-d47667779eb5.png">



# Conversational Speaker
## Now with [Semantic Kernel](https://aka.ms/skrepo) and [ChatGPT](https://openai.com/blog/chatgpt)!
The Conversational Speaker, a.k.a. "Friend Bot", uses a Raspberry Pi (or desktop) to enable spoken conversation with OpenAI large language models. This implementation listens to speech, processes the conversation through the OpenAI service, and responds back.

This project is written in .NET 6 which supports Linux/Raspbian, macOS, and Windows.

![Conversational Speaker](/package.png "Conversational Speaker")

**Read time**: 15 minutes

**Build time**: 30 minutes

**Cost**: 
 - Hardware
   - $ for [Raspberry PI 4 Model B](https://aka.ms/maker/rpi/four)
   - $ for [USB Omnidirectional Speakerphone](https://aka.ms/maker/usbspeakerphone)
   - $ for an SD card (to setup the Raspberry Pi OS)
- Software
  - [Azure Cognitive Speech Services](https://aka.ms/friendbot/azurecog)
    - **Free tier**: 5 audio hours per month and 1 concurrent request. 
    - **Free $200 credit**: With a new Azure account that can be used during the first 30 days.
  - [OpenAI](https://aka.ms/maker/openai/pricing)
    - **$0.002 / 1K tokens / ~750 words**: ChatGPT (gpt-3.5-turbo)
    - **Free $18 credit**: With a new OpenAI account that can be used during your first 90 days.
    
# Setup
You will need an instance of Azure Cognitive Services and an OpenAI account. 
You can run the software on nearly any platform, but let's start with a Raspberry Pi.

You may also use an instance of Azure OpenAI in place of OpenAI as well. 


## Raspberry Pi
_If you are new to Raspberry Pis, check out this [getting started](https://aka.ms/maker/rpi/gettingstarted) guide._
### 1. OS
1. Insert an SD card into your PC.
1. Go to https://aka.ms/maker/rpi/software then download and run the Raspberry Pi Imager. 
1. Click `Choose OS` and select the default Raspberry Pi OS (32-bit).
1. Click `Choose Storage`, select the SD card.
1. Click `Write` and wait for the imaging to complete.
1. Put the SD card into your Raspberry Pi and connect a keyboard, mouse, and monitor.
1. Complete the initial setup, making sure to configure Wi-Fi.

### 2. USB Speaker/Microphone
1. Plug in the USB speaker/microphone if you have not already.
1. On the Raspberry PI OS desktop, right-click on the volume icon in the top-right of the screen and make sure the USB device is selected.
1. Right-click on the microphone icon in the top-right of the screen and make sure the USB device is selected.

## Azure
The conversational speaker uses Azure Cognitive Service for speech-to-text and text-to-speech. Below are the steps to create an Azure account and an instance of Azure Cognitive Services.
### 1. Azure Account
  1. In a web browser, navigate to https://aka.ms/friendbot/azure and click on `Try Azure for Free`.
  1. Click on `Start Free` to start creating a free Azure account.
  1. Sign in with your Microsoft or GitHub account.
  1. After signing in, you will be prompted to enter some information.
        > NOTE: Even though this is a free account, Azure still requires credit card information. You will not be charged unless you change settings later.
  1. After your account setup is complete, navigate to https://aka.ms/friendbot/azureportal.

### 2. Azure Cognitive Services
  1. Sign into your account at https://aka.ms/friendbot/azureportal.
  1. In the search bar at the top, enter `Cognitive Services`. Under `Marketplace` select `Cognitive Services`. (It may take a few seconds to populate.)
  1. Verify the correct subscription is selected. Under `Resource Group` select `Create New`. Enter a resource group name (e.g. `conv-speak-rg`).
  1. Select a region and a name for your instance of Azure Cognitive Services (e.g. `my-conv-speak-cog-001`). 
        > NOTE: EastUS, WestEurope, or SoutheastAsia are recommended, as those regions tend to support the greatest number of features.  
  1. Click on `Review + Create`. After validation passes, click `Create`.
  1. When deployment has completed you can click `Go to resource` to view your Azure Cognitive Services resource.
  1. On the left side navigation bar, under `Resourse Management`, select `Keys and Endpoint`.
  1. Copy either of the two Cognitive Services keys. Save this key in a secure location for later.

  > Windows 11 users: If the application is stalling when calling the text-to-speech API, make sure you have applied all current security updates ([link](https://learn.microsoft.com/en-us/windows/release-health/resolved-issues-windows-11-22h2#2924msgdesc)).

## OpenAI
The conversational speaker uses OpenAI's models to hold a friendly conversation. Below are the steps to create a new account and access the AI models.
### 1. OpenAI Account
  1. In a web browser, navigate to https://aka.ms/maker/openai. Click `Sign up`.
        > NOTE: can use a Google account, Microsoft account, or email to create a new account.
  1. Complete the sign-up process (e.g., create a password, verify your email, etc.).
        > NOTE: If you are new to OpenAI, please review the usage guidelines (https://beta.openai.com/docs/usage-guidelines).
  1. In the top-right corner click on your account. Click on `View API keys`.
  1. Click `+ Create new secret key`. Copy the generated key and save it in a secure location for later.

  _If you are curious to play with the large language models directly, check out the https://platform.openai.com/playground?mode=chat at the top of the page after logging in to https://aka.ms/maker/openai._

# The Code
## 1. Code Configuration
1. On the Raspberry Pi or your PC, open a command-line terminal.
1. Install .NET 6 SDK.
   - For Raspberry Pi and Linux:
     ```bash
     curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 6.0
     ``` 
     After installation is complete (it may take a few minutes), add dotnet to the command search paths.
     ```bash
     echo 'export DOTNET_ROOT=$HOME/.dotnet' >> ~/.bashrc
     echo 'export PATH=$PATH:$HOME/.dotnet' >> ~/.bashrc
     source ~/.bashrc
     ```
     Verify dotnet was installed successfully by checking the version.
     ```bash
     dotnet --version
     ```
   - For Windows, go to https://aka.ms/maker/dotnet/download, under .NET 6.0 click `Download .NET SDK x64`, and run the installer.
1. Clone the repo.
   ```bash
   git clone https://github.com/microsoft/conversational-speaker.git
   ```
1. Set your API keys: Replace `{MyCognitiveServicesKey}` with your Azure Cognitive Services key and `{MyOpenAIKey}` with your OpenAI API key from the sections above.
   ```bash
   cd ~/conversational-speaker/src/ConversationalSpeaker
   dotnet user-secrets set "AzureCognitiveServices:Key" "{MyCognitiveServicesKey}"
   dotnet user-secrets set "AzureCognitiveServices:Region" "{MyCognitiveServicesRegion (e.g., EastUS)}"
   dotnet user-secrets set "OpenAI:Key" "{MyOpenAIKey}"
   ```
1. Build and run the code!
   ```bash
   cd ~/conversational-speaker/src/ConversationalSpeaker
   dotnet build
   dotnet run
   ```

## 2. (Optional) Application Setup on Boot
There are several ways to run a program when the Raspberry Pi boots. Below is a suggested method which runs the application in a visible terminal window automatically. This allows you to not only see the output but also cancel the application by clicking on the terminal window and pressing CTRL+C. 
1. Create a file `/etc/xdg/autostart/friendbot.desktop`
   ```bash
   sudo nano /etc/xdg/autostart/friendbot.desktop
   ```
1. Put the following content into the file.
   ```bash
   [Desktop Entry]
   Exec=lxterminal --command "/bin/bash -c '~/.dotnet/dotnet run --project ~/conversational-speaker/src/ConversationalSpeaker; /bin/bash'"
   ```
   Press CTRL+O to save the file and CTRL+X to exit. This will run the application in a terminal window after the Raspberry Pi has finished booting.
1. To test out the changes by rebooting. 
   ```bash
   reboot
   ```

## 3. (Optional) Create a custom wake phrase
The code base has a default wake phrase (`"Hey, Computer."`) already, which I suggest you use first. If you want to create your own (free!) custom wake word, then follow the steps below.
  1. Create a custom keyword model using the directions here: https://aka.ms/hackster/microsoft/wakeword. 
  1. Download the model, extract the `.table` file, and overwrite `src/ConversationalSpeaker/Handlers/WakeWordModel.table`.
  1. Rebuild and run the project to use your custom wake word.

## 4. Usage
- To start a new conversation, say "Start a new conversation". 
- To set context, the following phrase is recommended: "Hello, my name is \<your name\> and I live in \<your location\>."
- Continue conversing!
- For more usage settings, view `~/conversational-speaker/src/ConversationalSpeaker/configuration.json`. 
  - Change the AI's name (`PromptEngine:OutputPrefix`), 
  - Change the AI's voice (`AzureCognitiveServices:SpeechSynthesisVoiceName`)
  - Change the AI's personality (`PromptEngine:Description`)
  - Switch to text input by changing the `System:TextListener` to `true` (good for testing changes).
- Take a look at Azure Cognitive Service's [style support page](https://learn.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support?tabs=stt-tts#voice-styles-and-roles) to see which languages support which emotional styles, then play with the `AzureCognitiveServices:SpeechSynthesisVoiceName` and `PromptEngine` settings in `src/ConverstationalSpeaker/configuration.json`.

# How it works
## Primary logic
This application uses .NET's generic "HostBuilder" paradigm. The HostBuilder encapsulates handling dependencies (i.e., dependency injection), configuration, logging, and running a set of hosted services. In this example, there is only one hosted service, `ConversationLoopHostedService`, which contains the primary logic loop.
```C#
// ConversationLoopHostedService.cs
while (!cancellationToken.IsCancellationRequested)
{     
      // Listen to the user.
      string userMessage = await _listener.ListenAsync(cancellationToken);
      // Run the message through the AI and get a response.
      string response = await _conversationHandler.ProcessAsync(userMessage, cancellationToken);
      // Speak the response.
      await _speaker.SpeakAsync(response, cancellationToken);
}
```

## Wake Word or Phrase
Azure Cognitive Service's has an excellent (and free!) wake word support. After generating a keyword model (see "Create a custom wake word" above), we load it into the speech SDK and wait for the system to recognize the keyword.
```C#
// AzCognitiveServicesWakeWordListener.cs
_keywordModel = KeywordRecognitionModel.FromFile(keywordModelPath);
_audioConfig = AudioConfig.FromDefaultMicrophoneInput();
_keywordRecognizer = new KeywordRecognizer(_audioConfig);
do
{
   result = await _keywordRecognizer.RecognizeOnceAsync(_keywordModel);
} while (result.Reason != ResultReason.RecognizedKeyword);
```

## Listening
To listen to the user, the application leverages Azure Cognitive Service's [speech-to-text feature](https://aka.ms/friendbot/speech-to-text). The feature supports many languages and configurations. This project's default language is english (`en-US`) and uses the default system microphone.
```C#
// AzCognitiveServicesListener.cs
// Configure the connection to Azure.
SpeechConfig speechConfig = SpeechConfig.FromSubscription(_options.Key, _options.Region);
speechConfig.SpeechRecognitionLanguage = _options.SpeechRecognitionLanguage;
speechConfig.SetProperty(PropertyId.SpeechServiceResponse_PostProcessingOption, "TrueText");

// Configure the local audio setup
_audioConfig = AudioConfig.FromDefaultMicrophoneInput();
_speechRecognizer = new SpeechRecognizer(speechConfig, _audioConfig);
```

## Speaking
And last, but not least, we head back to Azure Cognitive Services for its text-to-speech feature to give a voice to our AI. Since we are parsing out a style cue from OpenAI, we'll need to use the text-to-speech's Speech Synthesis Markup Language (SSML) support.
```C#
// AzCognitiveServicesSpeaker.cs
SpeechConfig speechConfig = SpeechConfig.FromSubscription(_options.Key, _options.Region);
speechConfig.SpeechSynthesisVoiceName = _options.SpeechSynthesisVoiceName;
_speechSynthesizer = new SpeechSynthesizer(speechConfig);
message = ExtractStyle(message, out string style);
string ssml = GenerateSsml(message, style, _options.SpeechSynthesisVoiceName);
await _speechSynthesizer.SpeakSsmlAsync(ssml);
```
In the case of speaking `"That's great to hear! ~~excited~~"`, the SSML sent to Azure Cognitive Services would like like this: 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-JennyNeural">
    <mstts:express-as style="excited">That's great to hear!</mstts:express-as>
  </voice>
</speak>
```

# Contributing
This project welcomes contributions and suggestions. Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the
[Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the
[Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)
or contact [opencode@microsoft.com](mailto:opencode@microsoft.com)
with any additional questions or comments.

## Trademarks
This project may contain trademarks or logos for projects, products, or services. Authorized use
of Microsoft trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion
or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.
