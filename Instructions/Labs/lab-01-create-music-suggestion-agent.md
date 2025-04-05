---
lab:
  title: 'Lab: Entwickeln von KI-Agents mit Azure OpenAI und dem Semantic Kernel-SDK'
  module: 'Module 01: Build your kernel'
---

# Lab: Erstellen eines KI-Agents für Musikempfehlungen
# Lab-Handbuch für Kursteilnehmende

In diesem Lab erstellen Sie den Code für einen KI-Assistenten, der die Musikbibliothek der Benutzenden verwalten und personalisierte Empfehlungen für Songs und Konzerte bereitstellen kann. Sie verwenden das SDK für den semantischen Kernel, um den KI-Assistenten zu erstellen und ihn mit dem LLM-Dienst (Large Language Model) zu verbinden. Mit dem Semantic Kernel-SDK können Sie eine intelligente Anwendung erstellen, die mit dem LLM-Dienst interagieren und dem Benutzer personalisierte Empfehlungen geben kann.

## Übungsszenario

Sie sind ein Entwickler bei einem internationalen Audiostreamingdienst. Sie wurden mit der Integration von KI mit dem Dienst beauftragt, um Benutzern persönlichere Empfehlungen zu geben. Die KI sollte basierend auf dem Hörverlauf und den Vorlieben des Benutzers Songs und anstehende Künstlertouren empfehlen können. Sie entscheiden sich, das Semantic Kernel SDK zu verwenden, um einen KI-Assistenten zu erstellen, der mit dem Large Language Model (LLM)-Dienst interagieren kann.

## Ziele

Durch die Bearbeitung dieses Labs führen Sie die folgenden Aufgaben aus:

* Erstellen eines Endpunkts für den LLM-Dienst (Large Language Model)
* Erstellen eines Semantic Kernel-Objekts
* Ausführen von Prompts mithilfe des Semantic Kernel-SDK
* Erstellen von Semantic Kernel-Funktionen und Semantic Kernel-Plug-Ins
* Aktivieren des automatischen Funktionsaufrufs zum Automatisieren von Aufgaben

## Lab-Einrichtung

### Voraussetzungen

Um die Übung durchzuführen, müssen die folgenden Komponenten auf Ihrem System installiert sein:

* [Visual Studio Code](https://code.visualstudio.com)
* [Das aktuelle .NET 8.0 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
* [Die C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code


### Vorbereiten Ihrer Entwicklungsumgebung

Für diese Übungen steht Ihnen ein Einstiegsprojekt zur Verfügung, das Sie verwenden können. Führen Sie die folgenden Schritte aus, um das Einstiegsprojekt einzurichten:

> [!IMPORTANT]
> Sie müssen .NET Framework 8.0 sowie die VS Code-Erweiterungen für C# und den NuGet-Paket-Manager installiert haben.

1. Fügen Sie den folgenden Link in einem neuen Browserfenster ein:
   
     `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/01/Lab-01-Starter.zip`

1. Laden Sie die ZIP-Datei herunter, indem Sie auf die `...` Schaltfläche klicken, die sich auf der oberen rechten Seite der Seite befindet, oder drücken Sie <kbd>STRG</kbd>+<kbd>UMSCHALT</kbd>+<kbd>S.</kbd>

1. Extrahieren Sie den Inhalt der ZIP-Datei in einen Speicherort, der leicht zu finden und zu merken ist, z. B. in einen Ordner auf Ihrem Desktop.

1. Öffnen Sie Visual Studio Code, und wählen Sie **Datei** > **Ordner öffnen** aus.

1. Navigieren Sie zum Ordner **Starter**, den Sie extrahiert haben, und wählen Sie **Ordner auswählen** aus.

1. Öffnen Sie die Datei **Program.cs** im Code-Editor.

> [!NOTE]
> Wenn Sie aufgefordert werden, den Dateien im Ordner zu vertrauen, wählen Sie **Ja, ich vertraue den Autoren** aus. 

## Übung 1: Ausführen eines Prompts mit dem Semantic Kernel-SDK

Für diese Übung erstellen Sie einen Endpunkt für den LLM-Dienst (Large Language Model). Das Semantic Kernel SDK verwendet diesen Endpunkt, um eine Verbindung mit dem LLM herzustellen und Eingabeaufforderungen auszuführen. Das Semantic Kernel SDK unterstützt HuggingFace, OpenAI und Azure Open AI LLMs. In diesem Beispiel verwenden Sie Azure Open AI.

**Geschätzte Bearbeitungsdauer für die Übung**: 10 Minuten

### Aufgabe 1: Erstellen einer Azure OpenAI-Ressource

1. Navigieren Sie zu [https://portal.azure.com](https://portal.azure.com).

1. Erstellen Sie eine neue Azure OpenAI-Ressource mit den Standardeinstellungen.

    > [!NOTE]
    > Wenn Sie bereits über eine Azure OpenAI-Ressource verfügen, können Sie diesen Schritt überspringen.

1. Wählen Sie nach Erstellung der Ressource die Option **Zu Ressource wechseln** aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Zum Azure AI Foundry-Portal wechseln** aus.

1. Wählen Sie **Neue Bereitstellung erstellen** und dann **aus Basismodellen** aus.

1. Wählen Sie in der Modellliste **gpt-35-turbo-16k** aus.

1. Wählen Sie **Bestätigen** aus.

1. Geben Sie einen Namen für Ihre Bereitstellung ein, und behalten Sie die Standardoptionen bei.

1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zurück zu Ihrer Azure OpenAI-Ressource im Azure-Portal.

1. Wählen Sie unter **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus.

    Sie verwenden die hier verwendeten Daten in der nächsten Aufgabe, um Ihren Kernel zu erstellen. Denken Sie daran, Ihre Schlüssel privat und sicher aufzubewahren.

### Aufgabe 2: Erstellen Ihres Kernels

In dieser Übung erfahren Sie, wie Sie Ihr erstes Semantic Kernel SDK-Projekt erstellen. Sie erfahren, wie Sie ein neues Projekt erstellen, das NuGet-Paket des Semantic Kernel SDK und einen Verweis auf das Semantic Kernel SDK hinzufügen. Legen wir los.

1. Kehren Sie zu Ihrem Visual Studio Code-Projekt zurück.

1. Öffnen Sie die Datei **appsettings.json** und aktualisieren Sie die Werte mit Ihrer Azure OpenAI Services-Modell-ID, Ihrem Endpunkt und Ihrem API-Schlüssel.

    ```json
    {
        "modelId": "gpt-35-turbo-16k",
        "endpoint": "",
        "apiKey": ""
    }
    ```

1. Öffnen Sie das Terminal, indem Sie **Terminal** > **Neues Terminal** auswählen.

1. Führen Sie im Terminal den folgenden Befehl aus, um das Semantic Kernel SDK zu installieren:

    `dotnet add package Microsoft.SemanticKernel --version 1.30.0`

1. 1. Fügen Sie die folgenden `using`-Anweisungen in die Datei **Program.cs** ein:

    ```c#
    using Microsoft.SemanticKernel;
    using Microsoft.SemanticKernel.ChatCompletion;
    using Microsoft.SemanticKernel.Connectors.OpenAI;
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a kernel builder with Azure OpenAI chat completion** hinzu:

    ```c#
    // Create a kernel builder with Azure OpenAI chat completion
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(modelId, endpoint, apiKey);
    ```

1. Erstellen Sie den Kernel, indem Sie diesen Code unter dem Kommentar **Build the kernel** einfügen:

    ```c#
    // Build the kernel
    var kernel = builder.Build();
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Verify the endpoint and run a prompt** hinzu:

    ```c#
    // Verify the endpoint and run a prompt
    var result = await kernel.InvokePromptAsync("Who are the top 5 most famous musicians in the world?");
    Console.WriteLine(result);
    ```

1. Klicken Sie mit der rechten Maustaste auf den **Starter**-Ordner und wählen Sie **In integriertem Terminal öffnen**.

1. Geben Sie im Terminal `dotnet run` ein, um Ihren Code auszuführen.

    Überprüfen Sie, ob Sie eine Antwort vom Azure OpenAI-Modell erhalten, das die 5 berühmtesten Musikanten der Welt enthält.

    Die Antwort stammt aus dem Azure Open AI-Modell, das Sie an den Kernel übergeben haben. Das Semantic Kernel SDK kann eine Verbindung mit dem großen Sprachmodell (LLM) herstellen und die Eingabeaufforderung ausführen. Beachten Sie, wie schnell Sie Antworten vom LLM erhalten haben. Das Semantic Kernel SDK macht das Erstellen intelligenter Anwendungen einfach und effizient.

Sie können den Überprüfungscode entfernen, nachdem Sie Ihre Antwort bestätigt haben.

## Übung 2: Erstellen benutzerdefinierter Musikbibliotheks-Plug-Ins

In dieser Übung erstellen Sie benutzerdefinierte Plug-Ins für Ihre Musikbibliothek. Sie erstellen Funktionen, die der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen, die Liste der zuletzt wiedergegebenen Songs abrufen und personalisierte Songempfehlungen geben können. Sie erstellen außerdem eine Funktion, die basierend auf dem Standort des Benutzers und seinen zuletzt wiedergegebenen Songs ein Konzert vorschlägt.

**Geschätzte Bearbeitungsdauer für die Übung**: 15 Minuten

### Aufgabe 1: Erstellen eines Musikbibliotheks-Plug-Ins

In dieser Aufgabe erstellen Sie ein Plug-In, mit dem Sie der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen und die Liste der zuletzt wiedergegebenen Songs abrufen können. Der Einfachheit halber werden die zuletzt wiedergegebenen Songs in einer Textdatei gespeichert.

1. Öffnen Sie im Ordner **Plug-Ins** die Datei **MusicLibraryPlugin.cs**.

1. Fügen Sie unter dem Kommentar **Create a kernel function to get recently played songs** den Kernel-Funktions-Decorator hinzu:


    ```c#
    // Create a kernel function to get recently played songs
    [KernelFunction("GetRecentPlays")]
    public static string GetRecentPlays()
    ```

    Der `KernelFunction`-Decorator deklariert Ihre systemeigene Funktion. Sie verwenden einen beschreibenden Namen für die Funktion, damit die KI sie richtig aufrufen kann. Die Liste der zuletzt verwendeten Wiedergaben des Benutzers wird in einer Textdatei mit dem Namen „RecentlyPlayed.txt“ gespeichert.

1. Fügen Sie unter dem Kommentar **Create a kernel function to add a song to the recently played list** den Kernel-Funktions-Decorator hinzu:

    ```c#
    // Create a kernel function to add a song to the recently played list
    [KernelFunction("AddToRecentPlays")]
    public static string AddToRecentlyPlayed(string artist,  string song, string genre)
    ```

    Wenn diese Plug-In-Klasse nun dem Kernel hinzugefügt wird, kann sie die Funktionen identifizieren und aufrufen.

1. Navigieren Sie zur Datei **Program.cs**.

1. Fügen Sie den folgenden Code unter dem Kommentar **Import plugins to the kernel** hinzu:

    ```c#
    // Import plugins to the kernel
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    ```

1. Fügen Sie unter dem Kommentar **Create prompt execution settings** den folgenden Code hinzu, um die Funktion automatisch aufzurufen:

    ```c#
    // Create prompt execution settings
    OpenAIPromptExecutionSettings openAIPromptExecutionSettings = new() 
    {
        FunctionChoiceBehavior = FunctionChoiceBehavior.Auto()
    };
    ```

    Wenn Sie diese Einstellung verwenden, kann der Kernel automatisch Funktionen aufrufen, ohne dass diese im Prompt angegeben werden müssen.

1. Fügen Sie den folgenden Code unter dem Kommentar **Get chat completion service** ein:

    ```c#
    // Get chat completion service.
    var chatCompletionService = kernel.GetRequiredService<IChatCompletionService>();
    ChatHistory chatHistory = [];
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a helper function to await and output the reply from the chat completion service** hinzu:

    ```c#
    // Create a helper function to await and output the reply from the chat completion service
    async Task GetAssistantReply() {
        ChatMessageContent reply = await chatCompletionService.GetChatMessageContentAsync(
            chatHistory,
            kernel: kernel,
            executionSettings: openAIPromptExecutionSettings
        );
        chatHistory.AddAssistantMessage(reply.ToString());
        Console.WriteLine(reply.ToString());
    }
    ```


1. Fügen Sie den folgenden Code unter dem Kommentar **Add system messages to the chat** hinzu:

    ```c#
    // Add system messages to the chat
    chatHistory.AddSystemMessage("When a user has played a song, add it to their list of recent plays.");
    chatHistory.AddSystemMessage("The listener has just played the song Danse by Tiara. It falls under these genres: French pop, electropop, pop.");
    await GetAssistantReply();
    ```

1. Führen Sie den Code aus, indem Sie `dotnet run` in den Terminal eingeben.

    Die hinzugefügten Systemmeldungs-Prompts sollten die Plug-In-Funktionen aufrufen und die folgende Ausgabe anzeigen:

    ```output
    Added 'Danse' to recently played
    ```

    Wenn Sie **Files/RecentlyPlayed.txt** öffnen, sollte der neue Titel in der Liste angezeigt werden.

### Aufgabe 2: Abgeben personalisierter Songempfehlungen

In dieser Aufgabe erstellen Sie einen Prompt, der dem Benutzer basierend auf seinen zuletzt wiedergegebenen Songs personalisierte Songempfehlungen gibt. Der Prompt kombiniert die nativen Funktionen zum Generieren einer Songempfehlung. Außerdem erstellen Sie aus dem Prompt eine Funktion, damit dieser erneut verwendet werden kann.

1. Fügen Sie in Ihrer **Program.cs**-Datei den folgenden Code unter dem Kommentar **Create a song suggester function using a prompt** hinzu:

    ```c#
    // Create a song suggester function using a prompt
    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"This is a list of music available to the user:
        {{MusicLibraryPlugin.GetMusicLibrary}} 

        This is a list of music the user has recently played:
        {{MusicLibraryPlugin.GetRecentPlays}}

        Based on their recently played music, suggest a song from
        the list to play next",
        functionName: "SuggestSong",
        description: "Suggest a song to the user"
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);
    ```

    In diesem Code erstellen Sie eine Funktion aus Ihrem Prompt und fügen sie den Kernel-Plug-Ins hinzu.

1. Fügen Sie unter dem Kommentar „**Invoke the song suggester function with a prompt from the user***“ den folgenden Code hinzu:

    ```c#
    // Invoke the song suggester function with a prompt from the user
    chatHistory.AddUserMessage("What song should I play next?");
    await GetAssistantReply();
    ```

    Jetzt kann Ihre Anwendung Ihre Plug-In-Funktionen automatisch entsprechend der Anfrage der Benutzenden aufrufen. Lassen Sie uns diesen Code erweitern, um auch Konzertempfehlungen auf der Grundlage der Informationen der Benutzenden bereitzustellen.

### Aufgabe 3: Abgeben personalisierter Konzertempfehlungen

In dieser Aufgabe erstellen Sie ein Plug-In, das die LLM auffordert, ein Konzert basierend auf den kürzlich gespielten Liedern und dem Standort der Benutzenden vorzuschlagen.

1. Importieren Sie in Ihrer **Program.cs**-Datei das Plug-In für Konzerte unter dem Kommentar **Import plugins to the kernel**:

    ```c#
    // Import plugins to the kernel 
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a concert suggester function using a prompt** hinzu:

    ```c#
    // Create a concert suggester function using a prompt
    var concertSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"This is a list of the user's recently played songs:
        {{MusicLibraryPlugin.GetRecentPlays}}

        This is a list of upcoming concert details:
        {{MusicConcertsPlugin.GetConcerts}}

        Suggest an upcoming concert based on the user's recently played songs. 
        The user lives in {{$location}}, 
        please recommend a relevant concert that is close to their location.",
        functionName: "SuggestConcert",
        description: "Suggest a concert to the user"
    );

    kernel.Plugins.AddFromFunctions("SuggestConcertPlugin", [concertSuggesterFunction]);
    ```

    Dieser Funktions-Prompt fragt die Musikbibliothek und die Informationen zu bevorstehenden Konzerten sowie den Standort der Benutzenden ab und gibt eine Empfehlung ab.

1. Fügen Sie den folgenden Prompt hinzu, um die neue Funktion des Plug-Ins aufzurufen:

    ```c#
    // Invoke the concert suggester function with a prompt from the user
    chatHistory.AddUserMessage("Can you recommend a concert for me? I live in Washington");
    await GetAssistantReply();
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Sie sollten eine Ausgabe ähnlich der folgenden Antwort erhalten:

    ```output
    I recommend you attend the concert of Lisa Taylor. She will be performing in Seattle, Washington, USA on 2/22/2024. Enjoy the show!
    ```
    
    Ihre Antwort vom LLM kann variieren. Versuchen Sie, Ihren Prompt und Ihren Standort zu optimieren, um zu sehen, welche anderen Ergebnisse Sie generieren können.

Jetzt kann Ihre Assistentin basierend auf den Eingaben der Benutzenden automatisch verschiedene Aktionen ausführen. Gut gemacht!

### Überprüfung

In diesem Lab haben Sie einen KI-Assistenten erstellt, der die Musikbibliothek der Benutzenden verwalten und personalisierte Empfehlungen für Songs und Konzerte bereitstellen kann. Sie haben den semantischen Kernel SDK verwendet, um den KI-Assistenten zu erstellen und ihn mit dem Large Language Model (LLM)-Dienst zu verbinden. Sie haben benutzerdefinierte Plug-Ins für Ihre Musikbibliothek erstellt und die automatische Funktionsaufrufung aktiviert, damit Ihr Assistent dynamisch auf die Eingaben der Benutzenden reagiert. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!
