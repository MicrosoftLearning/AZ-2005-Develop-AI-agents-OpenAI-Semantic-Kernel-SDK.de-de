---
lab:
  title: 'Lab: Entwickeln von KI-Agents mit Azure OpenAI und dem Semantic Kernel-SDK'
  module: 'Module 01: Build your kernel'
---

# Lab: Fertigstellen eines KI-Reisebüros
# Lab-Handbuch für Kursteilnehmer

In dieser Übung stellen Sie ein KI-Reisebüro mit dem Semantic Kernel-SDK fertig. Sie erstellen einen Endpunkt für den LLM-Dienst (Large Language Model), erstellen Semantic Kernel-Funktionen und verwenden die Funktion für automatische Funktionsaufrufe des Semantic Kernel-SDK, um die Absicht des Benutzers bzw. der Benutzerin an die entsprechenden Plug-Ins weiterzuleiten, zu denen einige vorgefertigter Plug-Ins, die bereitgestellt wurden, gehören. Sie stellen dem LLM auch Kontext bereit, indem Sie den Unterhaltungsverlauf verwenden und den Benutzern und Benutzerinnen erlauben, die Unterhaltung fortzusetzen.

## Übungsszenario

Sie sind bei einem Reisebüro, das sich auf die Erstellung personalisierter Reiseerlebnisse für der Kunden spezialisiert hat, Fachkraft in der Entwicklung. Sie wurden beauftragt, ein KI-Reisebüro zu erstellen, das Kunden dabei helfen kann, mehr über Reiseziele zu erfahren und Aktivitäten für ihre Reisen zu planen. Der KI-Reisebüro sollte in der Lage sein, Währungsbeträge umzurechnen, Reiseziele und Aktivitäten vorzuschlagen, hilfreiche Ausdrücke in verschiedenen Sprachen bereitzustellen und Sätze zu übersetzen. Der KI-Reisebüro sollte auch anhand des Unterhaltungsverlaufs kontextbezogene Antworten auf die Anfragen des Benutzers oder der Benutzerin geben können.

## Ziele

Durch die Bearbeitung dieses Labs führen Sie die folgenden Aufgaben aus:

* Erstellen eines Endpunkts für den LLM-Dienst (Large Language Model)
* Erstellen eines Semantic Kernel-Objekts
* Ausführen von Prompts mithilfe des Semantic Kernel-SDK
* Erstellen von Semantic Kernel-Funktionen und Semantic Kernel-Plug-Ins
* Verwenden der Funktion für automatische Funktionsaufrufe des Semantic Kernel-SDK

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
   
     `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/02/Lab-02-Starter.zip`

1. Laden Sie die ZIP-Datei herunter, indem Sie auf die Schaltfläche <kbd>...</kbd> klicken, die sich auf der oberen rechten Seite der Seite befindet, oder drücken Sie <kbd>STRG</kbd>+<kbd>UMSCHALT</kbd>+<kbd>S</kbd>.

1. Extrahieren Sie den Inhalt der ZIP-Datei in einen Speicherort, der leicht zu finden und zu merken ist, z. B. in einen Ordner auf Ihrem Desktop.

1. Öffnen Sie Visual Studio Code, und wählen Sie **Datei** > **Ordner öffnen** aus.

1. Navigieren Sie zum Ordner **Starter**, den Sie extrahiert haben, und wählen Sie **Ordner auswählen** aus.

1. Öffnen Sie die Datei **Program.cs** im Code-Editor.

> [!NOTE]
> Wenn Sie aufgefordert werden, den Dateien im Ordner zu vertrauen, wählen Sie **Ja, ich vertraue den Autoren** aus.

## Übung 1: Erstellen eines Plug-Ins mit dem Semantic Kernel-SDK

Für diese Übung erstellen Sie einen Endpunkt für den LLM-Dienst (Large Language Model). Das Semantic Kernel SDK verwendet diesen Endpunkt, um eine Verbindung mit dem LLM herzustellen und Eingabeaufforderungen auszuführen. Das Semantic Kernel SDK unterstützt HuggingFace, OpenAI und Azure Open AI LLMs. In diesem Beispiel verwenden Sie Azure Open AI.

**Geschätzte Bearbeitungsdauer für die Übung**: 10 Minuten

### Aufgabe 1: Erstellen einer Azure OpenAI-Ressource

1. Navigieren Sie zu [https://portal.azure.com](https://portal.azure.com).

1. Erstellen Sie eine neue Azure OpenAI-Ressource mit den Standardeinstellungen.

    > [!NOTE]
    > Wenn Sie bereits über eine Azure OpenAI-Ressource verfügen, können Sie diesen Schritt überspringen.

1. Wählen Sie nach Erstellung der Ressource die Option **Zu Ressource wechseln** aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Gehe zum Azure Foundry-Portal** aus.

1. Wählen Sie **Neue Bereitstellung erstellen** und dann **aus Basismodell bereitstellen** aus.

1. Wählen Sie in der Modellliste **gpt-35-turbo-16k** aus.

1. Wählen Sie **Bestätigen** aus.

1. Geben Sie einen Namen für Ihre Bereitstellung ein, und behalten Sie die Standardoptionen bei.

1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zurück zu Ihrer Azure OpenAI-Ressource im Azure-Portal.

1. Wählen Sie unter **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus.

    Sie verwenden die hier verwendeten Daten in der nächsten Aufgabe, um Ihren Kernel zu erstellen. Denken Sie daran, Ihre Schlüssel privat und sicher aufzubewahren.

### Aufgabe 2: Erstellen eines nativen Plug-Ins

In dieser Aufgabe erstellen Sie ein natives Funktios-Plug-In, das einen Betrag aus einer Basiswährung in eine Zielwährung umrechnen kann.

1. Kehren Sie zu Ihrem Visual Studio Code-Projekt zurück.

1. Öffnen Sie die Datei **appsettings.json**, und aktualisieren Sie die Werte mit Ihrer Azure OpenAI Services-Modell-ID, ihrem Endpunkt und dem API-Schlüssel.

    ```json
    {
        "modelId": "gpt-35-turbo-16k",
        "endpoint": "",
        "apiKey": ""
    }
    ```

1. Navigieren Sie zu einer neuen Datei namens **CurrencyConverter.cs** im Ordner **Plugins/ConvertCurrency**

1. Fügen Sie in der Datei **CurrencyConverter.cs** den folgenden Code hinzu, um eine Plug-In-Funktion zu erstellen:

    ```c#
    class CurrencyConverter
    {
        [KernelFunction("convert_currency")]
        [Description("Converts an amount from one currency to another, for example USD to EUR")]
        public static decimal ConvertCurrency(decimal amount, string fromCurrency, string toCurrency)
        {
            decimal exchangeRate = GetExchangeRate(fromCurrency, toCurrency);
            return amount * exchangeRate;
        }
    }
    ```

    In diesem Code verwenden Sie den Decorator **KernelFunction**, um Ihre systemeigene Funktion zu deklarieren. Sie verwenden auch den **Description**-Decorator, um eine Beschreibung der Funktion hinzuzufügen. Als Nächstes fügen Sie eine Logik hinzu, um einen bestimmten Betrag von einer Währung in eine andere zu konvertieren.

1. Importieren Sie in der Datei **Program.cs** ein neues Plugin mit dem folgenden Code:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    ```

    Als Nächstes testen wir Ihr Plug-In.

1. Klicken Sie mit der rechten Maustaste auf Ihre **Program.cs**-Datei, und klicken Sie auf „In integriertem Terminal öffnen“

1. Geben Sie im Terminal `dotnet run` ein. 

    Geben Sie eine Eingabenaufforderung zur Währungsumrechnung ein, z. B. „Wie viel sind 10 USD in Hongkong?“

    Etwa folgende Ausgabe sollte angezeigt werden:

    ```output
    Assistant: 10 USD is equivalent to 77.70 Hong Kong dollars (HKD).
    ```

## Übung 2: Erstellen einer Handlebars-Eingabeaufforderung

In dieser Übung erstellen Sie eine Funktion aus einer Handlebars-Eingabeaufforderung. Die Funktion fordert die LLM auf, einen Reiseplan für den Benutzenden zu erstellen. Legen wir los.

**Geschätzte Bearbeitungsdauer für die Übung**: 10 Minuten

### Aufgabe 1: Erstellen einer Funktion aus einer Handlebars-Eingabeaufforderung

1. Fügen Sie die folgende `using`-Anweisung zur **Program.cs**-Datei hinzu:

    `using Microsoft.SemanticKernel.PromptTemplates.Handlebars;`

1. Aktualisieren Sie die Datei **Program.cs** mit dem folgenden Code:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverterPlugin>();

    string hbprompt = """
        <message role="system">Instructions: Before providing the the user with a travel itenerary, ask how many days their trip is</message>
        <message role="user">I'm going to {{city}}. Can you create an itenerary for me?</message>
        <message role="assistant">Sure, how many days is your trip?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">
        """;
    ```

    In diesem Code erstellen Sie mithilfe des Handlebars-Vorlagenformats eine Few-Shot-Eingabeaufforderung. Die Eingabeaufforderung leitet das Modell dazu an, weiterer Informationen vom Benutzenden abzurufen, bevor ein Reiseplan erstellt wird.

1. Fügen Sie den folgenden Code in Ihre Datei **Program.cs** ein:

    ```c#
    // Create the prompt template config using handlebars format
    var templateFactory = new HandlebarsPromptTemplateFactory();
    var promptTemplateConfig = new PromptTemplateConfig()
    {
        Template = hbprompt,
        TemplateFormat = "handlebars",
        Name = "GetItenerary",
    };

    // Create a plugin from the prompt
    var promptFunction = kernel.CreateFunctionFromPrompt(promptTemplateConfig, templateFactory);
    var iteneraryPlugin = kernel.CreatePluginFromFunctions("TravelItenerary", [promptFunction]);

    // Add the new plugin to the kernel
    kernel.Plugins.Add(iteneraryPlugin);
    ```

    In diesem Code erstellen Sie eine Handlebars-Vorlagenkonfiguration aus der Eingabeaufforderung. Anschließend erstellen Sie eine Plug-In-Funktion für die Eingabeaufforderung und fügen sie dem Kernel hinzu. Nun können Sie Ihre Funktion aufrufen.

1. Geben Sie `dotnet run` in das Terminal ein, um den Code auszuführen.

    Versuchen Sie die folgende Eingabe, um die LLM zur Erstellung eines Reisplans aufzufordern.

    ```output
    Assistant: How may I help you?
    User: I'm going to Hong Kong, can you create an itenerary for me?
    Assistant: Sure! How many days will you be staying in Hong Kong?
    User: 10
    Assistant: Great! Here's a 10-day itinerary for your trip to Hong Kong:
    ...
    ```

    Jetzt haben Sie die Anfänge eines KI-Reiseassistenten erstellt. Verwenden wir Eingabeaufforderungen und Plug-Ins, um weitere Features hinzuzufügen

1.  Fügen Sie den folgenden Code in Ihre Datei **Program.cs** ein:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverterPlugin>();
    kernel.ImportPluginFromType<FlightBookingPlugin>();
    ```

    Dieses Plug-In simuliert Flugbuchungen mithilfe der **flights.json**-Datei mit simulierten Details. Fügen Sie dem Assistenten als Nächstes weitere Systemaufforderungen hinzu.

1.  Fügen Sie den folgenden Code in Ihre Datei **Program.cs** ein:

    ```c#
    // Setup the assistant chat
    var history = new ChatHistory();
    history.AddSystemMessage("The current date is 01/10/2025");
    history.AddSystemMessage("You are a helpful travel assistant.");
    history.AddSystemMessage("Before providing destination recommendations, ask the user about their budget.");
    ```

    Diese Eingabeaufforderungen tragen zu einem reibungslosen Benutzererlebnis bei und helfen bei der Simulation des Flugbuchungs-Plug-Ins. Sie können jetzt Ihren Code testen.

1. Geben Sie im Terminal `dotnet run` ein.

    Versuchen Sie, einige der folgenden Eingabeaufforderungen einzugeben:

    ```output
    1. Can you give me some destination recommendations for Europe?
    2. I want to go to Barcelona, can you create an itenerary for me?
    3. How many Euros is 100 USD?
    4. Can you book me a flight to Barcelona?
    ```

    Probieren Sie andere Eingaben aus, und sehen Sie, wie Ihr Reiseassistent reagiert.

## Übung 3: Einholen der Zustimmung des Benutzenden für Aktionen

In dieser Übung fügen Sie eine Filteraufruffunktion hinzu, die die Genehmigung der Benutzenden anfordert, bevor der Agent einen Flug in deren Namen buchen kann. Legen wir los.

### Aufgabe 1: Erstellen eines Funktionsaufruffilters

1. Erstellen Sie eine neue Datei mit dem Namen **PermissionFilter.cs**.

1. Geben Sie in der neuen Datei den folgenden Code ein:

    ```c#
    #pragma warning disable SKEXP0001 
    using Microsoft.SemanticKernel;
    
    public class PermissionFilter : IFunctionInvocationFilter
    {
        public async Task OnFunctionInvocationAsync(FunctionInvocationContext context, Func<FunctionInvocationContext, Task> next)
        {
            
        }
    }
    ```

    >[!NOTE] 
    > In Version 1.30.0 des semantischen Kernel-SDK können Funktionsfilter geändert werden und erfordern eine Unterdrückung von Warnungen. 

    In diesem Code implementieren Sie die `IFunctionInvocationFilter`-Schnittstelle. Die `OnFunctionInvocationAsync`-Methode wird immer aufgerufen, wenn eine Funktion von einem KI-Agenten aufgerufen wird.

1. Fügen Sie den folgenden Code hinzu, um zu erkennen, wann die `book_flight`-Funktion aufgerufen wird:

    ```c#
    if ((context.Function.PluginName == "FlightBooking" && context.Function.Name == "book_flight"))
    {
    
    }

    await next(context);
    ```

    Dieser Code bestimmt anhand des `FunctionInvocationContext` Codes, welches Plug-In und welche Funktion aufgerufen wurden.

1. Fügen Sie die folgende Logik hinzu, um die Berechtigung des Benutzenden zum Buchen des Flugs anzufordern:

    ```c#
    if ((context.Function.PluginName == "FlightBooking" && context.Function.Name == "book_flight"))
    {
        Console.WriteLine("System Message: The agent requires an approval to complete this operation. Do you approve (Y/N)");
        Console.Write("User: ");
        string shouldProceed = Console.ReadLine()!;

        if (shouldProceed != "Y")
        {
            context.Result = new FunctionResult(context.Result, "The operation was not approved by the user");
            return;
        }
    }

    await next(context);
    ```

1. Navigieren Sie zur Datei **Program.cs**.

1. Fügen Sie den Berechtigungsfilter mithilfe des folgenden Codes zu Ihrem Kernel hinzu:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverterPlugin>();
    kernel.ImportPluginFromType<FlightBookingPlugin>();
    kernel.FunctionInvocationFilters.Add(new PermissionFilter());
    ```

1. Geben Sie im Terminal `dotnet run` ein.

    Geben Sie eine Eingabeaufforderung ein, um einen Flug zu buchen. Die Antwort sollte ähnlich der folgenden aussehen:

    ```output
    User: Find me a flight to Tokyo on the 19
    Assistant: I found a flight to Tokyo on the 19th of January. The flight is with Air Japan and the price is $1200.
    User: Y
    System Message: The agent requires an approval to complete this operation. Do you approve (Y/N)
    User: N
    Assistant: I'm sorry, but I am unable to book the flight for you.
    ```

    Der Agent sollte die Genehmigung des Benutzenden einholen, bevor er mit Buchungen fortfährt.

### Überprüfung

In diesem Lab haben Sie einen Endpunkt für den LLM (Large Language Model)-Dienst erstellt, ein semantisches Kernelobjekt erstellt und Prompts mithilfe des semantischen Kernel-SDKs ausgeführt. Sie haben auch Plug-Ins erstellt und Systemmeldungen verwendet, um das Modell zu steuern. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!
