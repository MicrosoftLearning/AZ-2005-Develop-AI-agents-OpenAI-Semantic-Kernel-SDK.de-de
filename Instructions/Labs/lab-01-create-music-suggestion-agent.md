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

1. Wählen Sie auf der Seite **Übersicht** die Option **Zu Azure OpenAI Studio wechseln** aus.

:::image type="content" source="../media/model-deployments.png" alt-text="Screenshot: Azure OpenAI-Bereitstellungsseite":::

1. Wählen Sie **Neue Bereitstellung erstellen** und dann **Modell bereitstellen** aus.

1. Wählen Sie unter **Modell auswählen** **gpt-35-turbo-16k** aus.

    Verwenden der Standardmodellversion

1. Geben Sie einen Namen für Ihre Bereitstellung ein.

1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zurück zu Ihrer Azure OpenAI-Ressource.

1. Wählen Sie unter **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus.

    Sie verwenden die hier verwendeten Daten in der nächsten Aufgabe, um Ihren Kernel zu erstellen. Denken Sie daran, Ihre Schlüssel privat und sicher aufzubewahren.

### Aufgabe 2: Erstellen Ihres Kernels

In dieser Übung erfahren Sie, wie Sie Ihr erstes Semantic Kernel SDK-Projekt erstellen. Sie erfahren, wie Sie ein neues Projekt erstellen, das NuGet-Paket des Semantic Kernel SDK und einen Verweis auf das Semantic Kernel SDK hinzufügen. Legen wir los.

1. Kehren Sie zu Ihrem Visual Studio Code-Projekt zurück.

1. Öffnen Sie das Terminal, indem Sie **Terminal** > **Neues Terminal** auswählen.

1. Führen Sie im Terminal den folgenden Befehl aus, um das Semantic Kernel SDK zu installieren:

    `dotnet add package Microsoft.SemanticKernel --version 1.2.0`

1. Um den Kernel zu erstellen, fügen Sie den folgenden Code in Ihre **Program.cs**-Datei ein:
    
    ```c#
    using Microsoft.SemanticKernel;

    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(
        "your-deployment-name",
        "your-endpoint",
        "your-api-key",
        "deployment-model");
    var kernel = builder.Build();
    ```

    Achten Sie darauf, die Platzhalter durch die Werte aus Ihrer Azure-Ressource zu ersetzen.

1. Um zu überprüfen, ob Ihr Kernel und Ihr Endpunkt funktioniert, geben Sie den folgenden Code ein:

    ```c#
    var result = await kernel.InvokePromptAsync(
        "Who are the top 5 most famous musicians in the world?");
    Console.WriteLine(result);
    ```

1. Geben Sie `dotnet run` ein, um den Code auszuführen, und überprüfen Sie, ob eine Antwort des Azure OpenAI-Modells angezeigt wird, das die fünf bekanntesten Musikerinnen und Musiker der Welt enthält.

    Die Antwort stammt aus dem Azure Open AI-Modell, das Sie an den Kernel übergeben haben. Das Semantic Kernel SDK kann eine Verbindung mit dem großen Sprachmodell (LLM) herstellen und die Eingabeaufforderung ausführen. Beachten Sie, wie schnell Sie Antworten vom LLM erhalten haben. Das Semantic Kernel SDK macht das Erstellen intelligenter Anwendungen einfach und effizient.

## Übung 2: Erstellen benutzerdefinierter Musikbibliotheks-Plug-Ins

In dieser Übung erstellen Sie benutzerdefinierte Plug-Ins für Ihre Musikbibliothek. Sie erstellen Funktionen, die der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen, die Liste der zuletzt wiedergegebenen Songs abrufen und personalisierte Songempfehlungen geben können. Sie erstellen außerdem eine Funktion, die basierend auf dem Standort des Benutzers und seinen zuletzt wiedergegebenen Songs ein Konzert vorschlägt.

**Geschätzte Bearbeitungsdauer für die Übung**: 15 Minuten

### Aufgabe 1: Erstellen eines Musikbibliotheks-Plug-Ins

In dieser Aufgabe erstellen Sie ein Plug-In, mit dem Sie der Liste „Zuletzt wiedergegeben“ des Benutzers Songs hinzufügen und die Liste der zuletzt wiedergegebenen Songs abrufen können. Der Einfachheit halber werden die zuletzt wiedergegebenen Songs in einer Textdatei gespeichert.

1. Erstellen Sie einen neuen Ordner im Verzeichnis „Lab01-Project“, und nennen Sie ihn „Plugins“.

1. Erstellen Sie im Ordner „Plugins“ eine neue Datei „MusicLibraryPlugin.cs“

    Erstellen Sie zunächst einige schnelle Funktionen, um Songs zur Liste „Zuletzt wiedergegeben“ des Benutzers abzurufen und hinzuzufügen.

1. Geben Sie den folgenden Code ein:

    ```c#
    using System.ComponentModel;
    using System.Text.Json;
    using System.Text.Json.Nodes;
    using Microsoft.SemanticKernel;

    public class MusicLibraryPlugin
    {
        [KernelFunction, 
        Description("Get a list of music recently played by the user")]
        public static string GetRecentPlays()
        {
            string content = File.ReadAllText($"Files/RecentlyPlayed.txt");
            return content;
        }
    }
    ```

    In diesem Code verwenden Sie den `KernelFunction`-Decorator um Ihre systemeigene Funktion zu deklarieren. Sie verwenden auch den `Description`-Decorator, um eine Beschreibung der Funktion hinzuzufügen. Die Liste der zuletzt verwendeten Wiedergaben des Benutzers wird in einer Textdatei mit dem Namen „RecentlyPlayed.txt“ gespeichert. Als Nächstes können Sie Code hinzufügen, um der Liste einen Song hinzuzufügen.

1. Fügen Sie der `MusicLibraryPlugin`-Klasse den folgenden Code hinzu:

    ```c#
    [KernelFunction, Description("Add a song to the recently played list")]
    public static string AddToRecentlyPlayed(
        [Description("The name of the artist")] string artist, 
        [Description("The title of the song")] string song, 
        [Description("The song genre")] string genre)
    {
        // Read the existing content from the file
        string filePath = "Files/RecentlyPlayed.txt";
        string jsonContent = File.ReadAllText(filePath);
        var recentlyPlayed = (JsonArray) JsonNode.Parse(jsonContent);

        var newSong = new JsonObject
        {
            ["title"] = song,
            ["artist"] = artist,
            ["genre"] = genre
        };

        recentlyPlayed.Insert(0, newSong);
        File.WriteAllText(filePath, JsonSerializer.Serialize(recentlyPlayed,
            new JsonSerializerOptions { WriteIndented = true }));

        return $"Added '{song}' to recently played";
    }
    ```

    In diesem Code erstellen Sie eine Funktion, die den Künstler, Song und Genre als Zeichenfolgen akzeptiert. Zusätzlich zur `Description`-Funktion fügen Sie auch Beschreibungen der Eingabevariablen hinzu. Die Datei „RecentlyPlayed.txt“ enthält eine Liste von Songs im JSON-Format, die der Benutzer zuletzt wiedergegeben hat. Dieser Code liest den vorhandenen Inhalt aus der Datei, analysiert ihn und fügt den neuen Song der Liste hinzu. Danach wird die aktualisierte Liste wieder in die Datei geschrieben.

1. Aktualisieren Sie die Datei **Program.cs** mit dem folgenden Code:

    ```c#
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();

    var result = await kernel.InvokeAsync(
        "MusicLibraryPlugin", 
        "AddToRecentlyPlayed", 
        new() {
            ["artist"] = "Tiara", 
            ["song"] = "Danse", 
            ["genre"] = "French pop, electropop, pop"
        }
    );
    
    Console.WriteLine(result);
    ```

    In diesem Code importieren Sie das MusicLibraryPlugin in den Kernel mit ImportPluginFromType. Anschließend rufen Sie InvokeAsync mit dem Namen des Plug-Ins und dem Namen der Funktion auf, die Sie aufrufen möchten. Sie geben auch den Künstler, Song und Genre als Argumente an.

1. Führen Sie den Code aus, indem Sie `dotnet run` in den Terminal eingeben.

    Die folgende Ausgabe wird angezeigt.

    ```output
    Added 'Danse' to recently played
    ```

    Wenn Sie „Files/RecentlyPlayed.txt“ öffnen, sollte der neue Song der Liste hinzugefügt werden.

> [!NOTE]
> Wenn das Terminal Warnungen für Nullwerte anzeigt, können Sie diese ignorieren, da sie das Ergebnis nicht beeinflussen.

### Aufgabe 2: Abgeben personalisierter Songempfehlungen

In dieser Aufgabe erstellen Sie einen Prompt, der dem Benutzer basierend auf seinen zuletzt wiedergegebenen Songs personalisierte Songempfehlungen gibt. Der Prompt kombiniert die nativen Funktionen zum Generieren einer Songempfehlung. Außerdem erstellen Sie aus dem Prompt eine Funktion, damit dieser erneut verwendet werden kann.

1. Fügen Sie in Ihrer `MusicLibraryPlugin.cs`-Datei die folgende Funktion hinzu:

    ```c#
    [KernelFunction, Description("Get a list of music available to the user")]
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
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    
    string prompt = @"This is a list of music available to the user:
        {{MusicLibraryPlugin.GetMusicLibrary}} 

        This is a list of music the user has recently played:
        {{MusicLibraryPlugin.GetRecentPlays}}

        Based on their recently played music, suggest a song from
        the list to play next";

    var result = await kernel.InvokePromptAsync(prompt);
    Console.WriteLine(result);
    ```

In diesem Code kombinieren Sie Ihre nativen Funktionen mit einer semantischen Eingabeaufforderung. Die nativen Funktionen können Benutzerdaten abrufen, auf die das große Sprachmodell (LLM) nicht zugreifen kann, und das LLM kann basierend auf der Texteingabe eine Songempfehlung generieren.

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

    var result = await kernel.InvokeAsync(songSuggesterFunction);
    Console.WriteLine(result);
    ```

    In diesem Code erstellen Sie eine Funktion aus einem Prompt, die Benutzern einen Song vorschlägt. Anschließend fügen Sie sie den Kernel-Plug-Ins hinzu. Abschließend weisen Sie den Kernel an, die Funktion auszuführen.

### Aufgabe 3: Abgeben personalisierter Konzertempfehlungen

In dieser Aufgabe erstellen Sie ein Plug-In, das Details zu anstehenden Konzerten abruft. Sie erstellen zudem ein Plug-In, das das LLM dazu anweist, ein Konzert basierend auf den zuletzt wiedergegebenen Songs und dem Standort des Benutzers vorzuschlagen.

1. Erstellen Sie im Ordner „Plug-Ins“ eine neue Datei mit dem Namen „MusicConcertPlugin.cs“.

1. Fügen Sie in der Datei „MusicConcertPlugin“ den folgenden Code hinzu:

    ```c#
    using System.ComponentModel;
    using Microsoft.SemanticKernel;

    public class MusicConcertsPlugin
    {
        [KernelFunction, Description("Get a list of upcoming concerts")]
        public static string GetConcerts()
        {
            string content = File.ReadAllText($"Files/ConcertDates.txt");
            return content;
        }
    }
    ```

    In diesem Code erstellen Sie eine Funktion, die die Liste der anstehenden Konzerte aus einer Datei mit dem Namen „ConcertDates.txt“ liest. Die Datei enthält eine Liste der anstehenden Konzerte im JSON-Format. Als Nächstes müssen Sie einen Prompt erstellen, um das LLM anzuweisen, ein Konzert vorzuschlagen.

1. Erstellen Sie im Ordner „Prompts“ einen neuen Ordner mit dem Namen „SuggestConcert“.

1. Erstellen Sie im Ordner „SuggestConcert“ eine Datei namens „config.json“ mit dem folgenden Inhalt:

    ```json
    {
        "schema": 1,
        "type": "completion",
        "description": "Suggest a concert to the user",
        "execution_settings": {
            "default": {
                "max_tokens": 4000,
                "temperature": 0
            }
        },
        "input_variables": [
            {
                "name": "location",
                "description": "The user's location",
                "required": true
            }
        ]
    }
    ```

1. Erstellen Sie im Ordner „SuggestConcert“ eine Datei namens „skprompt.txt“ mit dem folgenden Inhalt:

    ```output
    This is a list of the user's recently played songs:
    {{MusicLibraryPlugin.GetRecentPlays}}

    This is a list of upcoming concert details:
    {{MusicConcertsPlugin.GetConcerts}}

    Suggest an upcoming concert based on the user's recently played songs. 
    The user lives in {{$location}}, 
    please recommend a relevant concert that is close to their location.
    ```

    Dieser Prompt hilft dem LLM, die Benutzereingaben zu filtern und nur das Ziel aus dem Text abzurufen. Als Nächstes testen Sie Ihre Plug-Ins, um die Ausgabe zu überprüfen.

1. Öffnen Sie Ihre **Program.cs**-Datei und aktualisieren Sie sie mit dem folgenden Code:

    ```c#
    var kernel = builder.Build();    
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
    // code omitted for brevity
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);

    string location = "Redmond WA USA";
    var result = await kernel.InvokeAsync<string>(prompts["SuggestConcert"],
        new() {
            { "location", location }
        }
    );
    Console.WriteLine(result);
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Sie sollten eine Ausgabe ähnlich der folgenden Antwort erhalten:

    ```output
    Based on the user's recently played songs and their location in Redmond WA USA, a relevant concert recommendation would be the upcoming concert of Lisa Taylor in Seattle WA, USA on February 22, 2024. Lisa Taylor is an indie-folk artist, and her music genre aligns with the user's recently played songs, such as "Loanh Quanh" by Ly Hoa. Additionally, Seattle is close to Redmond, making it a convenient location for the user to attend the concert.
    ```

    Versuchen Sie, Ihren Prompt und Ihren Standort zu optimieren, um zu sehen, welche anderen Ergebnisse Sie generieren können.

## Übung 3: Automatisieren von Vorschlägen basierend auf Benutzereingaben

Sie können das manuelle Aufrufen der Plug-In-Funktionen vermeiden, indem Sie stattdessen automatische Funktionsaufrufe verwenden. Das LLM wählt automatisch die im Kernel registrierten Plug-Ins aus und kombiniert sie, um das Ziel zu erreichen. In dieser Übung aktivieren Sie automatische Funktionsaufrufe, um Empfehlungen zu automatisieren.

**Geschätzte Bearbeitungsdauer für die Übung**: 10 Minuten

### Aufgabe 1: Automatisieren von Vorschlägen basierend auf Benutzereingaben

In dieser Aufgabe aktivieren Sie automatische Funktionsaufrufe, um Vorschläge basierend auf der Eingabe der Benutzenden zu generieren.

1. Aktualisieren Sie Ihren Code in der **Program.cs**-Datei wie folgt:

    ```c#
    using Microsoft.SemanticKernel;
    using Microsoft.SemanticKernel.Connectors.OpenAI;
    
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(
        "your-deployment-name",
        "your-endpoint",
        "your-api-key",
        "deployment-model");
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    kernel.ImportPluginFromPromptDirectory("Prompts");

    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };
    
    string prompt = @$"Based on the user's recently played music, suggest a 
        concert for the user living in ${location}";

    var autoInvokeResult = await kernel.InvokePromptAsync(prompt, new(settings));
    Console.WriteLine(autoInvokeResult);
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Based on the user's recently played songs, the artist "Mademoiselle" has an upcoming concert in Seattle WA, USA on February 22, 2024, which is close to Redmond WA. Therefore, the recommended concert for the user would be Mademoiselle's concert in Seattle.
    ```

    Der semantische Kernel kann die `SuggestConcert`-Funktion automatisch mit den richtigen Parametern aufrufen. Jetzt ist Ihr Agent in der Lage, Benutzenden ein Konzert vorzuschlagen, das auf der Liste der zuletzt gespielten Musik und ihrem Standort basiert. Als Nächstes können Sie Unterstützung für Musikempfehlungen hinzufügen.

1. Ändern Sie die **Program.cs**-Datei mit dem folgenden Code:

    ```c#
    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };
    
    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"Based on the user's recently played music:
        {{$recentlyPlayedSongs}}
        recommend a song to the user from the music library:
        {{$musicLibrary}}",
        functionName: "SuggestSong",
        description: "Recommend a song from the music library"
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);

    string prompt = "Can you recommend a song from the music library?";

    var autoInvokeResult = await kernel.InvokePromptAsync(prompt, new(settings));
    Console.WriteLine(autoInvokeResult);
    ```

    In diesem Code erstellen Sie eine KernelFunction aus einer Prompt-Vorlage, die dem LLM mitteilt, wie ein Lied vorschlagen werden soll. Registrieren Sie es anschließend im Kernel und rufen Sie einen Prompt mit aktivierter Einstellung für den automatischen Funktionsaufruf auf. Der Kernel kann die Funktion ausführen und die richtigen Parameter angeben, um den Prompt zu vervollständigen.

1. Geben Sie im Terminal `dotnet run` ein, um Ihren Code auszuführen.

    Die generierte Ausgabe sollte dem Benutzer basierend auf seiner kürzlich wiedergegebenen Musik einen Song empfehlen. Ihre Antwort sieht möglicherweise ähnlich wie die folgende Ausgabe aus:
    
    ```
    Based on your recently played music, I recommend you listen to the song "Luv(sic)". It falls under the genres of hiphop and rap, which aligns with some of your recently played songs. Enjoy!  
    ```

    Als Nächstes probieren wir einen Prompt aus, um die Liste der zuletzt wiedergegebenen Songs zu aktualisieren.

1. Aktualisieren Sie die Datei **Program.cs** mit dem folgenden Code:

    ```c#
    string prompt = @"Add this song to the recently played songs list:  title: 'Touch', artist: 'Cat's Eye', genre: 'Pop'";

    var result = await kernel.InvokePromptAsync(prompt, new(settings));

    Console.WriteLine(result);
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Die Ausgabe sollte ähnlich der Folgenden aussehen:

    ```
    I have added the song 'Touch' by Cat's Eye to the recently played songs list.
    ```

    Wenn Sie die Datei „recentlyplayed.txt“ öffnen, sollte der neue Song am Anfang der Liste hinzugefügt worden sein.
    

Mithilfe der Einstellung `AutoInvokeKernelFunctions` können Sie sich auf das Erstellen von Plug-Ins konzentrieren, um Benutzeranforderungen zu erfüllen. Ihr Agent ist nun in der Lage, auf der Grundlage der Benutzereingaben automatisch verschiedene Aktionen durchzuführen. Gut gemacht!

### Überprüfung

In diesem Lab haben Sie einen KI-Agent erstellt, der die Musikbibliothek des Benutzers verwalten und personalisierte Song- und Konzertempfehlungen geben kann. Sie haben das Semantic Kernel-SDK verwendet, um den KI-Agent zu erstellen und mit dem LLM-Dienst (Large Language Model, großes Sprachmodell) zu verbinden. Sie haben benutzerdefinierte Plug-Ins für Ihre Musikbibliothek erstellt und automatische Funktionsaufrufe aktiviert, damit Ihr Agent dynamisch auf die Eingabe der Benutzenden reagiert. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!
