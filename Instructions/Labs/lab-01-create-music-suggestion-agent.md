---
lab:
  title: 'Lab: Entwickeln von KI-Agents mit Azure OpenAI und dem Semantic Kernel-SDK'
  module: 'Module 01: Build your kernel'
---

# Lab: Erstellen eines KI-Agents für Musikempfehlungen
# Lab-Handbuch für Kursteilnehmer

In diesem Lab erstellen Sie den Code für einen KI-Agent, der die Musikbibliothek des Benutzers verwalten und personalisierte Song- und Konzertempfehlungen geben kann. Sie verwenden das Semantic Kernel-SDK, um den KI-Agent zu erstellen und diesen mit dem LLM-Dienst (Large Language Model, großes Sprachmodell) zu verbinden. Mit dem Semantic Kernel-SDK können Sie eine intelligente Anwendung erstellen, die mit dem LLM-Dienst interagieren und dem Benutzer personalisierte Empfehlungen geben kann.

## Übungsszenario

Sie sind ein Entwickler bei einem internationalen Audiostreamingdienst. Sie wurden mit der Integration von KI mit dem Dienst beauftragt, um Benutzern persönlichere Empfehlungen zu geben. Die KI sollte basierend auf dem Hörverlauf und den Vorlieben des Benutzers Songs und anstehende Künstlertouren empfehlen können. Sie entscheiden sich dazu, das Semantic Kernel-SDK zur Erstellung eines KI-Agents zu verwenden, der mit dem LLM-Dienst (Large Language Model, großes Sprachmodell) interagieren kann.

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

1. Wählen Sie in der Liste der Modelle **gpt-35-turbo-16k** aus.

1. Wählen Sie **Bestätigen** aus.

1. Geben Sie einen Namen für Ihren Einsatz ein und behalten Sie die Standardoptionen bei.

1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie im Azure-Portal zurück zu Ihrer Azure-OpenAI-Ressource.

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

1. Um den Kernel zu erstellen, fügen Sie den folgenden Code in Ihre **Program.cs**-Datei ein:
    
    ```c#
    // Create a kernel builder with Azure OpenAI chat completion
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(modelId, endpoint, apiKey);

    // Build the kernel
    var kernel = builder.Build();
    ```

1. Um zu überprüfen, ob Ihr Kernel und Ihr Endpunkt funktioniert, geben Sie den folgenden Code ein:

    ```c#
    var result = await kernel.InvokePromptAsync("Who are the top 5 most famous musicians in the world?");
    Console.WriteLine(result);
    ```

1. Klicken Sie mit der rechten Maustaste auf den **Starter**-Ordner und wählen Sie **In integriertem Terminal öffnen**.

1. Geben Sie im Terminal `dotnet run` ein, um Ihren Code auszuführen.

    Überprüfen Sie, ob Sie eine Antwort vom Azure OpenAI-Modell erhalten, das die 5 berühmtesten Musikanten der Welt enthält.

    Die Antwort stammt aus dem Azure Open AI-Modell, das Sie an den Kernel übergeben haben. Das Semantic Kernel SDK kann eine Verbindung mit dem großen Sprachmodell (LLM) herstellen und die Eingabeaufforderung ausführen. Beachten Sie, wie schnell Sie Antworten vom LLM erhalten haben. Das Semantic Kernel SDK macht das Erstellen intelligenter Anwendungen einfach und effizient.

## Übung 2: Erstellen benutzerdefinierter Musikbibliotheks-Plug-Ins

In dieser Übung erstellen Sie benutzerdefinierte Plug-Ins für Ihre Musikbibliothek. Sie erstellen Funktionen, die der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen, die Liste der zuletzt wiedergegebenen Songs abrufen und personalisierte Songempfehlungen geben können. Sie erstellen außerdem eine Funktion, die basierend auf dem Standort des Benutzers und seinen zuletzt wiedergegebenen Songs ein Konzert vorschlägt.

**Geschätzte Bearbeitungsdauer für die Übung**: 15 Minuten

### Aufgabe 1: Erstellen eines Musikbibliotheks-Plug-Ins

In dieser Aufgabe erstellen Sie ein Plug-In, mit dem Sie der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen und die Liste der zuletzt wiedergegebenen Songs abrufen können. Der Einfachheit halber werden die zuletzt wiedergegebenen Songs in einer Textdatei gespeichert.

1. Erstellen Sie im Ordner **Plug-Ins** eine neue Datei namens **MusicLibraryPlugin.cs**.

    Erstellen Sie zunächst einige schnelle Funktionen, um Songs zur Liste „Zuletzt wiedergegeben“ des Benutzers abzurufen und hinzuzufügen.

1. Geben Sie den folgenden Code ein:

    ```c#
    using System.Text.Json;
    using System.Text.Json.Nodes;
    using Microsoft.SemanticKernel;

    public class MusicLibraryPlugin
    {
        [KernelFunction("GetRecentPlays")]
        public static string GetRecentPlays()
        {
            string content = File.ReadAllText($"Files/RecentlyPlayed.txt");
            return content;
        }
    }
    ```

    In diesem Code verwenden Sie den `KernelFunction`-Decorator um Ihre systemeigene Funktion zu deklarieren. Sie verwenden einen beschreibenden Namen für die Funktion, damit die KI sie richtig aufrufen kann. Die Liste der zuletzt verwendeten Wiedergaben des Benutzers wird in einer Textdatei mit dem Namen „RecentlyPlayed.txt“ gespeichert. Als Nächstes können Sie Code hinzufügen, um der Liste einen Song hinzuzufügen.

1. Fügen Sie der `MusicLibraryPlugin`-Klasse den folgenden Code hinzu:

    ```c#
    [KernelFunction("AddToRecentPlays")]
    public static string AddToRecentlyPlayed(string artist,  string song, string genre)
    {
        // Read the existing content from the file
        string filePath = "Files/RecentlyPlayed.txt";
        string jsonContent = File.ReadAllText(filePath);
        var recentlyPlayed = (JsonArray) JsonNode.Parse(jsonContent)!;

        var newSong = new JsonObject
        {
            ["title"] = song,
            ["artist"] = artist,
            ["genre"] = genre
        };

        // Insert the new song
        recentlyPlayed.Insert(0, newSong);
        File.WriteAllText(filePath, JsonSerializer.Serialize(recentlyPlayed,
            new JsonSerializerOptions { WriteIndented = true }));

        return $"Added '{song}' to recently played";
    }
    ```

    In diesem Code erstellen Sie eine Funktion, die den Künstler, Song und Genre als Zeichenfolgen akzeptiert. Die Datei „RecentlyPlayed.txt“ enthält eine Liste von Songs im JSON-Format, die der Benutzer zuletzt wiedergegeben hat. Dieser Code liest den vorhandenen Inhalt aus der Datei, analysiert ihn und fügt den neuen Song der Liste hinzu. Danach wird die aktualisierte Liste wieder in die Datei geschrieben.

1. Aktualisieren Sie die Datei **Program.cs** mit dem folgenden Code:

    ```c#
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();

    // Get chat completion service.
    var chatCompletionService = kernel.GetRequiredService<IChatCompletionService>();

    // Create a chat history object
    ChatHistory chatHistory = [];
    ```

    In diesem Code importieren Sie das Plug-In in den Kernel und fügen die Einrichtung für den Chatabschluss hinzu.

1. Fügen Sie die folgenden Prompts hinzu, um das Plug-In aufzurufen:

    ```c#
    chatHistory.AddSystemMessage("When a user has played a song, add it to their list of recent plays.");
    chatHistory.AddSystemMessage("The listener has just played the song Danse by Tiara. It falls under these genres: French pop, electropop, pop.");

    ChatMessageContent reply = await chatCompletionService.GetChatMessageContentAsync(
        chatHistory,
        kernel: kernel
    );
    Console.WriteLine(reply.ToString());
    chatHistory.AddAssistantMessage(reply.ToString());
    ```

1. Führen Sie den Code aus, indem Sie `dotnet run` in den Terminal eingeben.

    Die folgende Ausgabe wird angezeigt.

    ```output
    Added 'Danse' to recently played
    ```

    Wenn Sie „Files/RecentlyPlayed.txt“ öffnen, sollte der neue Song der Liste hinzugefügt werden.

### Aufgabe 2: Abgeben personalisierter Songempfehlungen

In dieser Aufgabe erstellen Sie einen Prompt, der dem Benutzer basierend auf seinen zuletzt wiedergegebenen Songs personalisierte Songempfehlungen gibt. Der Prompt kombiniert die nativen Funktionen zum Generieren einer Songempfehlung. Außerdem erstellen Sie aus dem Prompt eine Funktion, damit dieser erneut verwendet werden kann.

1. Fügen Sie in Ihrer Datei **MusicLibraryPlugin.cs** die folgende Funktion hinzu:

    ```c#
    [KernelFunction("GetMusicLibrary")]
    public static string GetMusicLibrary()
    {
        string dir = Directory.GetCurrentDirectory();
        string content = File.ReadAllText($"Files/MusicLibrary.txt");
        return content;
    }
    ```

    Diese Funktion liest die Liste der verfügbaren Musik aus einer Datei mit dem Namen „MusicLibrary.txt“. Die Datei enthält eine Liste der für den Benutzer verfügbaren Songs im JSON-Format.

1. Aktualisieren Sie die Datei **Program.cs** mit dem folgenden Code:

    ```c#
    chatHistory.AddSystemMessage("When a user has played a song, add it to their list of recent plays.");
    
    string prompt = @"This is a list of music available to the user:
        {{MusicLibraryPlugin.GetMusicLibrary}} 

        This is a list of music the user has recently played:
        {{MusicLibraryPlugin.GetRecentPlays}}

        Based on their recently played music, suggest a song from
        the list to play next";

    var result = await kernel.InvokePromptAsync(prompt);
    Console.WriteLine(result);
    ```

    Zuerst können Sie den Code entfernen, der ein Lied an die Liste anhängt. Anschließend kombinieren Sie Ihre nativen Plug-In-Funktionen mit einem semantischen Prompt. Die nativen Funktionen können Benutzerdaten abrufen, auf die das große Sprachmodell (LLM) nicht zugreifen kann, und das LLM kann basierend auf der Texteingabe eine Songempfehlung generieren.

1. Um Ihren Code zu testen, geben Sie `dotnet run` in das Terminal ein.

    Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output 
    Based on the user's recently played music, a suggested song to play next could be "Sabry Aalil" by Yasemin since the user seems to enjoy pop and Egyptian pop music.
    ```

    >[!NOTE] Der empfohlene Song kann sich von dem hier gezeigten unterscheiden.

1. Ändern Sie den Code, um aus dem Prompt eine Funktion zu erstellen:

    ```c#
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

1. Fügen Sie den folgenden Code hinzu, um die Funktion automatisch aufzurufen:

    ```c#
    OpenAIPromptExecutionSettings openAIPromptExecutionSettings = new() 
    {
        FunctionChoiceBehavior = FunctionChoiceBehavior.Auto()
    };

    chatHistory.AddUserMessage("What song should I play next?");

    reply = await chatCompletionService.GetChatMessageContentAsync(
        chatHistory,
        kernel: kernel,
        executionSettings: openAIPromptExecutionSettings
    );
    Console.WriteLine(reply.ToString());
    chatHistory.AddAssistantMessage(reply.ToString());
    ```

    In diesem Code legen Sie die Einstellung fest, um automatische Funktionsaufrufe zu ermöglichen. Dann fügen Sie einen Prompt hinzu, der die Funktion aufruft und die Antwort abruft.

### Aufgabe 3: Abgeben personalisierter Konzertempfehlungen

In dieser Aufgabe erstellen Sie ein Plug-In, das die LLM auffordert, ein Konzert basierend auf den kürzlich gespielten Liedern und dem Standort der Benutzenden vorzuschlagen.

1. Fügen Sie in Ihrer **Program.cs**-Datei das Plug-In für Musikkonzerte zum Kernel hinzu:

    ```c#
    var kernel = builder.Build();    
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    ```

1. Fügen Sie Code hinzu, um eine Funktion aus einem Prompt zu erstellen:

    ```c#
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
    chatHistory.AddUserMessage("Can you recommend a concert for me? I live in Washington");

    reply = await chatCompletionService.GetChatMessageContentAsync(
        chatHistory,
        kernel: kernel,
        executionSettings: openAIPromptExecutionSettings
    );
    Console.WriteLine(reply.ToString());
    chatHistory.AddAssistantMessage(reply.ToString());
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Sie sollten eine Ausgabe ähnlich der folgenden Antwort erhalten:

    ```output
    I recommend you attend the concert of Lisa Taylor. She will be performing in Seattle, Washington, USA on 2/22/2024. Enjoy the show!
    ```
    
    Ihre Antwort vom LLM kann variieren. Versuchen Sie, Ihren Prompt und Ihren Standort zu optimieren, um zu sehen, welche anderen Ergebnisse Sie generieren können.

Ihr Agent ist nun in der Lage, auf der Grundlage der Benutzereingaben automatisch verschiedene Aktionen durchzuführen. Gut gemacht!

### Überprüfung

In diesem Lab haben Sie einen KI-Agent erstellt, der die Musikbibliothek des Benutzers verwalten und personalisierte Song- und Konzertempfehlungen geben kann. Sie haben das Semantic Kernel-SDK verwendet, um den KI-Agent zu erstellen und mit dem LLM-Dienst (Large Language Model, großes Sprachmodell) zu verbinden. Sie haben benutzerdefinierte Plug-Ins für Ihre Musikbibliothek erstellt und automatische Funktionsaufrufe aktiviert, damit Ihr Agent dynamisch auf die Eingabe der Benutzenden reagiert. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!
