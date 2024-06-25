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
* [Das aktuelle .NET 7.0 SDK](https://dotnet.microsoft.com/download/dotnet/7.0)
* [Die C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code

### Vorbereiten Ihrer Entwicklungsumgebung

Für diese Übungen steht Ihnen ein Einstiegsprojekt zur Verfügung, das Sie verwenden können. Führen Sie die folgenden Schritte aus, um das Einstiegsprojekt einzurichten:

> [!IMPORTANT]
> Sie müssen .NET Framework 8.0 sowie die VS Code-Erweiterungen für C# und den NuGet-Paket-Manager installiert haben.

1. Laden Sie die ZIP-Datei unter `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/02/Lab-02-Starter.zip` herunter.

1. Extrahieren Sie den Inhalt der ZIP-Datei in einen Speicherort, der leicht zu finden und zu merken ist, z. B. in einen Ordner auf Ihrem Desktop.

1. Öffnen Sie Visual Studio Code, und wählen Sie **Datei** > **Ordner öffnen** aus.

1. Navigieren Sie zum Ordner **Starter**, den Sie extrahiert haben, und wählen Sie **Ordner auswählen** aus.

1. Öffnen Sie die Datei **Program.cs** im Code-Editor.

## Übung 1: Erstellen eines Plug-Ins mit dem Semantic Kernel-SDK

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

    Sie verwenden diese Werte in der nächsten Aufgabe, um Ihren Kernel zu erstellen. Denken Sie daran, Ihre Schlüssel privat und sicher aufzubewahren.

1. Kehren Sie zur Datei **Program.cs** in Visual Studio Code zurück.

1. Aktualisieren Sie die folgenden Variablen mit ihrem Azure OpenAI Services-Bereitstellungsnamen, API-Schlüssel und Endpunkt.

    ```csharp
    string yourDeploymentName = "";
    string yourEndpoint = "";
    string yourApiKey = "";
    ```

    > [!NOTE]
    > Das Bereitstellungsmodell muss "gpt-35-turbo-16k" sein, damit einige der Semantischen Kernel-SDK-Features funktionieren.

### Aufgabe 2: Erstellen einer nativen Funktion

In dieser Aufgabe erstellen Sie eine native Funktion, die einen Betrag aus einer Basiswährung in eine Zielwährung umrechnen kann.

1. Erstellen einer neuen Datei namens `CurrencyConverter.cs` im **Plugins/ConvertCurrency**-Ordner

1. Fügen Sie in der `CurrencyConverter.cs`-Datei den folgenden Code hinzu, um eine Plug-In-Funktion zu erstellen:

    ```c#
    using AITravelAgent;

    class CurrencyConverter
    {
        [KernelFunction, 
        Description("Convert an amount from one currency to another")]
        public static string ConvertAmount()
        {
            var currencyDictionary = Currency.Currencies;
        }
    }
    ```

    In diesem Code verwenden Sie den `KernelFunction`-Decorator, um Ihre systemeigene Funktion zu deklarieren. Sie verwenden auch den `Description`-Decorator, um eine Beschreibung der Funktion hinzuzufügen. Sie können `Currency.Currencies` verwenden, um ein Wörterbuch mit Währungen und deren Wechselkursen zu erhalten. Fügen Sie als Nächstes eine Logik hinzu, um einen bestimmten Betrag von einer Währung in eine andere zu konvertieren.

1. Ändern Sie Ihre `ConvertAmount`-Funktion mit folgendem Code:

    ```c#
    [KernelFunction, Description("Convert an amount from one currency to another")]
    public static string ConvertAmount(
        [Description("The target currency code")] string targetCurrencyCode, 
        [Description("The amount to convert")] string amount, 
        [Description("The starting currency code")] string baseCurrencyCode)
    {
        var currencyDictionary = Currency.Currencies;
        
        Currency targetCurrency = currencyDictionary[targetCurrencyCode];
        Currency baseCurrency = currencyDictionary[baseCurrencyCode];

        if (targetCurrency == null)
        {
            return targetCurrencyCode + " was not found";
        }
        else if (baseCurrency == null)
        {
            return baseCurrencyCode + " was not found";
        }
        else
        {
            double amountInUSD = Double.Parse(amount) * baseCurrency.USDPerUnit;
            double result = amountInUSD * targetCurrency.UnitsPerUSD;
            return @$"${amount} {baseCurrencyCode} is approximately 
                {result.ToString("C")} in {targetCurrency.Name}s ({targetCurrencyCode})";
        }
    }
    ```

    In diesem Code verwenden Sie das `Currency.Currencies`-Wörterbuch, um das Objekt für die `Currency`-Ziel- und Basiswährungen abzurufen. Anschließend verwenden Sie das `Currency`-Objekt, um den Betrag aus der Basiswährung in die Zielwährung zu konvertieren. Schließlich geben Sie eine Zeichenfolge mit dem konvertierten Betrag zurück. Als Nächstes testen wir Ihr Plug-In.

1. Importieren und aufrufen Sie in der `Program.cs`-Datei die neue Plug-In-Funktion mit dem folgenden Code:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    kernel.ImportPluginFromType<ConversationSummaryPlugin>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var result = await kernel.InvokeAsync("CurrencyConverter", 
        "ConvertAmount", 
        new() {
            {"targetCurrencyCode", "USD"}, 
            {"amount", "52000"}, 
            {"baseCurrencyCode", "VND"}
        }
    );

    Console.WriteLine(result);
    ```

    In diesem Code verwenden Sie die `ImportPluginFromType`-Methode, um Ihr Plug-In zu importieren. Anschließend verwenden Sie die `InvokeAsync`-Methode, um Ihre Plug-In-Funktion aufzurufen. Die `InvokeAsync`-Methode verwendet den Plug-In-Namen, den Funktionsnamen und ein Wörterbuch mit Parametern. Schließlich drucken Sie das Ergebnis in der Konsole. Führen Sie als Nächstes den Code aus, um sicherzustellen, dass er funktioniert.

1. Geben Sie im Terminal `dotnet run` ein. Die folgende Ausgabe wird angezeigt.

    ```output
    $52000 VND is approximately $2.13 in US Dollars (USD)
    ```

    Nachdem Ihr Plug-In ordnungsgemäß funktioniert, erstellen wir nun eine natürliche Sprachaufforderung, die erkennen kann, welche Währungen und welchen Betrag der Benutzer konvertieren möchte.

### Aufgabe 3: Analysieren von Benutzereingaben mit einem Prompt

In dieser Aufgabe erstellen Sie eine Eingabeaufforderung, welche die Eingabe des Benutzers analysiert, um die Zielwährung, Basiswährung und den zu konvertierenden Betrag zu identifizieren.

1. Erstellen eines neuen Ordners namens `GetTargetCurrencies` im Ordner **Eingabeaufforderung**

1. Erstellen Sie im Ordner `GetTargetCurrencies` eine neue Datei namens `config.json`

1. Fügen Sie den folgenden Text in die `config.json`-Datei ein:

    ```output
    {
        "schema": 1,
        "type": "completion",
        "description": "Identify the target currency, base currency, and amount to convert",
        "execution_settings": {
            "default": {
                "max_tokens": 800,
                "temperature": 0
            }
        },
        "input_variables": [
            {
                "name": "input",
                "description": "Text describing some currency amount to convert",
                "required": true
            }
        ]
    }
    ```

1. Erstellen Sie im Ordner `GetTargetCurrencies` eine neue Datei namens `skprompt.txt`

1. Fügen Sie den folgenden Text in die `skprompt.txt`-Datei ein:

    ```html
    <message role="system">Identify the target currency, base currency, and 
    amount from the user's input in the format target|base|amount</message>

    For example: 

    <message role="user">How much in GBP is 750.000 VND?</message>
    <message role="assistant">GBP|VND|750000</message>

    <message role="user">How much is 60 USD in New Zealand Dollars?</message>
    <message role="assistant">NZD|USD|60</message>

    <message role="user">How many Korean Won is 33,000 yen?</message>
    <message role="assistant">KRW|JPY|33000</message>

    <message role="user">{{$input}}</message>
    <message role="assistant">target|base|amount</message>
    ```

### Aufgabe 4: Überprüfen Ihrer Arbeit

In dieser Aufgabe führen Sie Ihre Anwendung aus und überprüfen, ob der Code ordnungsgemäß funktioniert. 

1. Testen Sie Ihre neue Aufforderung, indem Sie die `Program.cs`-Datei mit dem folgenden Code aktualisieren:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var result = await kernel.InvokeAsync(prompts["GetTargetCurrencies"],
        new() {
            {"input", "How many Australian Dollars is 140,000 Korean Won worth?"}
        }
    );

    Console.WriteLine(result);
    ```

1. Geben Sie im Terminal `dotnet run` ein. Die folgende Ausgabe wird angezeigt.

    ```output
    AUD|KRW|140000
    ```

    > [!NOTE]
    > Wenn Ihr Code die erwartete Ausgabe nicht erzeugt, können Sie den Code im **Lösungsordner** überprüfen. Möglicherweise müssen Sie de Prompt in der Datei `skprompt.txt` anpassen, um genauere Ergebnisse zu erzielen.

Jetzt haben Sie ein Plug-In, das einen Betrag von einer Währung in eine andere konvertieren kann, und eine Eingabeaufforderung, die verwendet werden kann, um die Eingabe des Benutzers in ein Format zu analysieren, das die `ConvertAmount`-Funktion verwenden kann. Auf diese Weise können Benutzer und Benutzerinnen Währungsbeträge einfach mit Ihrem KI-Reisebüro konvertieren.

## Übung 2: Automatisieren der Plug-In-Auswahl basierend auf der Benutzerabsicht

In dieser Übung erkennen Sie die Absicht des Benutzers und leiten die Unterhaltung an die gewünschten Plug-Ins weiter. Sie können ein bereitgestelltes Plug-In verwenden, um die Absicht des Benutzers abzurufen. Legen wir los.

**Geschätzte Bearbeitungsdauer für die Übung**: 10 Minuten

### Aufgabe 1: Verwenden des GetIntent-Plug-Ins

1. Aktualisieren Sie Ihre`Program.cs`-Datei mit folgendem Code:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    Console.WriteLine("What would you like to do?");
    var input = Console.ReadLine();

    var intent = await kernel.InvokeAsync<string>(
        prompts["GetIntent"], 
        new() {{ "input",  input }}
    );

    ```

    In diesem Code verwenden Sie die `GetIntent`-Eingabeaufforderung, um die Absicht des Benutzers zu erkennen. Anschließend speichern Sie die Absicht in einer Variablen namens `intent`. Als Nächstes leiten Sie die Absicht an Ihr `CurrencyConverter`-Plug-In weiter.

1. Fügen Sie Ihrer Datei `Program.cs` den folgenden Code hinzu:

    ```c#
    switch (intent) {
        case "ConvertCurrency": 
            var currencyText = await kernel.InvokeAsync<string>(
                prompts["GetTargetCurrencies"], 
                new() {{ "input",  input }}
            );
            var currencyInfo = currencyText!.Split("|");
            var result = await kernel.InvokeAsync("CurrencyConverter", 
                "ConvertAmount", 
                new() {
                    {"targetCurrencyCode", currencyInfo[0]}, 
                    {"baseCurrencyCode", currencyInfo[1]},
                    {"amount", currencyInfo[2]}, 
                }
            );
            Console.WriteLine(result);
            break;
        default:
            Console.WriteLine("Other intent detected");
            break;
    }
    ```

    Das `GetIntent`-Plug-In gibt die folgenden Werte zurück: ConvertCurrency, SuggestDestinations, SuggestActivities, Translate, HelpfulPhrases, Unknown. Sie verwenden eine `switch`-Anweisung, um die Absicht des Benutzers an das entsprechende Plug-In weiterzuleiten. 
    
    Wenn der Benutzer die Währung konvertieren möchte, verwenden Sie die `GetTargetCurrencies`-Eingabeaufforderung, um die Währungsinformationen abzurufen. Anschließend verwenden Sie das `CurrencyConverter`-Plug-In, um den Betrag zu konvertieren.

    Als Nächstes fügen Sie einige Fälle hinzu, um die anderen Absichten zu behandeln. Verwenden wir nun die automatische Funktionsaufruffunktion des semantischen Kernel-SDK, um die Absicht an die verfügbaren Plug-Ins weiterzuleiten.

1. Erstellen Sie die Einstellung für den automatischen Funktionsaufruf, indem Sie der `Program.cs`-Datei den folgenden Code hinzufügen:

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };

    Console.WriteLine("What would you like to do?");
    var input = Console.ReadLine();
    var intent = await kernel.InvokeAsync<string>(
        prompts["GetIntent"], 
        new() {{ "input",  input }}
    );
    ```

    Als Nächstes fügen Sie der Switch-Anweisung Fälle für die anderen Absichten hinzu.

1. Aktualisieren Sie Ihre`Program.cs`-Datei mit folgendem Code:

    ```c#
    switch (intent) {
        case "ConvertCurrency": 
            // ...Code you entered previously...
            break;
        case "SuggestDestinations":
        case "SuggestActivities":
        case "HelpfulPhrases":
        case "Translate":
            var autoInvokeResult = await kernel.InvokePromptAsync(input!, new(settings));
            Console.WriteLine(autoInvokeResult);
            break;
        default:
            Console.WriteLine("Other intent detected");
            break;
    }
    ```

    In diesem Code verwenden Sie die `AutoInvokeKernelFunctions`-Einstellung, um automatisch Funktionen und Eingabeaufforderungen aufzurufen, auf die in Ihrem Kernel verwiesen wird. Wenn der Benutzer die Währung konvertieren möchte, führt das `CurrencyConverter`-Plug-In seine Aufgabe aus. 
    
    Wenn der Benutzer Beabsichtigt ist, Ziel- oder Aktivitätsvorschläge abzurufen, einen Ausdruck zu übersetzen oder hilfreiche Ausdrücke in einer Sprache zu erhalten, ruft die `AutoInvokeKernelFunctions`-Einstellung automatisch die vorhandenen Plug-Ins auf, die im Projektcode enthalten waren.

    Sie können auch Code hinzufügen, um die Benutzereingabe als Eingabeaufforderung für das große Sprachmodell (LLM) auszuführen, wenn sie nicht unter einen dieser Absichtsfälle fällt.

1. Aktualisieren Sie den Standardfall mit dem folgenden Code:

    ```c#
    default:
        Console.WriteLine("Sure, I can help with that.");
        var otherIntentResult = await kernel.InvokePromptAsync(input!, new(settings));
        Console.WriteLine(otherIntentResult);
        break;
    ```

    Wenn der Benutzer nun eine andere Absicht hat, kann der LLM die Anforderung des Benutzers verarbeiten. Probieren Sie es aus!

### Aufgabe 2: Überprüfen Ihrer Arbeit

In dieser Aufgabe führen Sie Ihre Anwendung aus und überprüfen, ob der Code ordnungsgemäß funktioniert. 

1. Geben Sie im Terminal `dotnet run` ein. Wenn Sie dazu aufgefordert werden, geben Sie einen Text ein, welcher der folgenden Eingabeaufforderung ähnlich ist:

    ```output
    What would you like to do?
    How many TTD is 50 Qatari Riyals?    
    ```

1. Sie sollten eine Ausgabe ähnlich der folgenden Antwort erhalten:

    ```output
    $50 QAR is approximately $93.10 in Trinidadian Dollars (TTD)
    ```

1. Geben Sie im Terminal `dotnet run` ein. Wenn Sie dazu aufgefordert werden, geben Sie einen Text ein, welcher der folgenden Eingabeaufforderung ähnlich ist:

    ```output
    What would you like to do?
    I want to go somewhere that has lots of warm sunny beaches and delicious, spicy food!
    ```

1. Sie sollten eine Ausgabe ähnlich der folgenden Antwort erhalten:

    ```output
    Based on your preferences for warm sunny beaches and delicious, spicy food, I have a few destination recommendations for you:

    1. Thailand: Known for its stunning beaches, Thailand offers a perfect combination of relaxation and adventure. You can visit popular beach destinations like Phuket, Krabi, or Koh Samui, where you'll find crystal-clear waters and white sandy shores. Thai cuisine is famous for its spiciness, so you'll have plenty of mouthwatering options to try, such as Tom Yum soup, Pad Thai, and Green Curry.

    2. Mexico: Mexico is renowned for its beautiful coastal regions and vibrant culture. You can explore destinations like Cancun, Playa del Carmen, or Tulum, which boast stunning beaches along the Caribbean Sea. Mexican cuisine is rich in flavors and spices, offering a wide variety of dishes like tacos, enchiladas, and mole sauces that will satisfy your craving for spicy food.

    ...

    These destinations offer a perfect blend of warm sunny beaches and delicious, spicy food, ensuring a memorable trip for you. Let me know if you need any further assistance or if you have any specific preferences for your trip!
    ```

1. Geben Sie im Terminal `dotnet run` ein. Wenn Sie dazu aufgefordert werden, geben Sie einen Text ein, welcher der folgenden Eingabeaufforderung ähnlich ist:

    ```output
    What would you like to do?
    Can you give me a recipe for chicken satay?

1. You should see a response similar to the following response:

    ```output
    Sure, I can help with that.
    Certainly! Here's a recipe for chicken satay:

    ...
    ```

    Die Absicht sollte an Ihren Standardfall weitergeleitet werden und der LLM sollte die Anforderung für ein Hühner-Satay-Rezept verarbeiten.

    > [!NOTE]
    > Wenn Ihr Code die erwartete Ausgabe nicht erzeugt, können Sie den Code im **Lösungsordner** überprüfen.

Lassen Sie uns als Nächstes die Routinglogik ändern, um bestimmten Plug-Ins einen Konversationsverlauf zu liefern. Das Bereitstellen des Verlaufs ermöglicht es den Plug-Ins, kontextbezogenere Antworten auf die Anforderungen des Benutzers abzurufen.

### Aufgabe 3: Vollständiges Plug-In-Routing

In dieser Übung verwenden Sie den Unterhaltungsverlauf, um Kontext für das Large Language Model (LLM) bereitzustellen. Sie passen den Code auch so an, dass der Benutzer die Unterhaltung fortsetzen kann, genau wie bei einem echten Chatbot. Legen wir los.

1. Verändern Sie den Code so, dass eine `do`-`while` Schleife verwendet wird, um die Eingabe des Benutzers zu akzeptieren:

    ```c#
    string input;

    do 
    {
        Console.WriteLine("What would you like to do?");
        input = Console.ReadLine();

        // ...
    }
    while (!string.IsNullOrWhiteSpace(input));
    ```

    Jetzt können Sie die Unterhaltung beibehalten, bis der Benutzer eine Leerzeile eingibt.

1. Erfassen Sie Einzelheiten zur Reise des Benutzers, indem Sie den Fall `SuggestDestinations` verändern:

    ```c#
    case "SuggestDestinations":
        chatHistory.AppendLine("User:" + input);
        var recommendations = await kernel.InvokePromptAsync(input!);
        Console.WriteLine(recommendations);
        break;
    ```

1. Verwenden Sie die Reisedetails im Fall `SuggestActivities` mit dem folgenden Code:

    ```c#
     case "SuggestActivities":
        var chatSummary = await kernel.InvokeAsync(
            "ConversationSummaryPlugin", 
            "SummarizeConversation", 
            new() {{ "input", chatHistory.ToString() }});
        break;
    ```

    In diesem Code verwenden Sie die integrierte `SummarizeConversation` Funktion, um den Chat mit dem Benutzer zusammenzufassen. Als Nächstes verwenden wir die Zusammenfassung, um Aktivitäten am Reiseziel vorzuschlagen.

1. Erweitern Sie den Fall `SuggestActivities` mit dem folgenden Code:

    ```c#
    var activities = await kernel.InvokePromptAsync(
        input,
        new () {
            {"input", input},
            {"history", chatSummary},
            {"ToolCallBehavior", ToolCallBehavior.AutoInvokeKernelFunctions}
    });

    chatHistory.AppendLine("User:" + input);
    chatHistory.AppendLine("Assistant:" + activities.ToString());
    
    Console.WriteLine(activities);
    break;
    ```

    In diesem Code fügen Sie `input` und `chatSummary` als Kernelargumente hinzu. Anschließend ruft der Kernel den Prompt auf und leitet ihn an das Plug-In `SuggestActivities` weiter. Außerdem hängen Sie die Eingabe des Benutzers und die Antwort des Assistenten zum Chatverlauf an und zeigen die Ergebnisse an. Als Nächstes müssen Sie die `chatSummary` Variable zum `SuggestActivities` Plug-In hinzufügen.

1. Navigieren Sie zu **Prompts/SuggestActivities/config.json** und öffnen Sie die Datei in Visual Studio Code

1. Fügen Sie unter `input_variables` eine Variable für den Chatverlauf hinzu:

    ```json
    "input_variables": [
      {
          "name": "history",
          "description": "Some background information about the user",
          "required": false
      },
      {
          "name": "destination",
          "description": "The destination a user wants to visit",
          "required": true
      }
  ]
  ```

1. Navigieren Sie zu **Prompts/SuggestActivities/skprompt.txt** und öffnen Sie die Datei

1. Fügen Sie einen Prompt hinzu, um den Chatverlauf zu verwenden:

    ```html 
    You are an experienced travel agent. 
    You are helpful, creative, and very friendly. 
    Consider the traveler's background: {{$history}}
    ```

    Lassen Sie den Rest des Prompts unverändert. Jetzt verwendet das Plug-In den Chatverlauf, um Kontext für das LLM bereitzustellen.

### Aufgabe 4: Überprüfen Ihrer Arbeit

In dieser Aufgabe führen Sie Ihre Anwendung aus und überprüfen Sie, ob der Code ordnungsgemäß funktioniert.

1. Vergleichen Sie Ihre aktualisierten Switchfälle mit dem folgenden Code:

    ```c#
    case "SuggestDestinations":
            chatHistory.AppendLine("User:" + input);
            var recommendations = await kernel.InvokePromptAsync(input!);
            Console.WriteLine(recommendations);
            break;
    case "SuggestActivities":

        var chatSummary = await kernel.InvokeAsync(
            "ConversationSummaryPlugin", 
            "SummarizeConversation", 
            new() {{ "input", chatHistory.ToString() }});

        var activities = await kernel.InvokePromptAsync(
            input!,
            new () {
                {"input", input},
                {"history", chatSummary},
                {"ToolCallBehavior", ToolCallBehavior.AutoInvokeKernelFunctions}
        });

        chatHistory.AppendLine("User:" + input);
        chatHistory.AppendLine("Assistant:" + activities.ToString());
        
        Console.WriteLine(activities);
        break;
    ```

1. Geben Sie im Terminal `dotnet run` ein. Wenn Sie dazu aufgefordert werden, geben Sie einen ähnlichen Text zum Folgenden ein:

    ```output
    What would you like to do?
    How much is 60 USD in new zealand dollars?
    ```

1. Die Ausgabe sollte in etwa wie folgt aussehen:

    ```output
    $60 USD is approximately $97.88 in New Zealand Dollars (NZD)
    What would you like to do?
    ```

1. Geben Sie einen Prompt für Zielvorschläge mit einigen Kontexthinweisen ein, z. B.:

    ```output
    What would you like to do?
    I'm planning an anniversary trip with my spouse, but they are currently using a wheelchair and accessibility is a must. What are some destinations that would be romantic for us?
    ```

1. Sie sollten eine Ausgabe mit Empfehlungen für barrierefreie Ziele erhalten.

1. Geben Sie einen Prompt für Aktivitätsvorschläge ein, z. B.:

    ```output
    What would you like to do?
    What are some things to do in Barcelona?
    ```

1. Sie sollten Empfehlungen erhalten, die in den vorherigen Kontext passen, z. B. barrierefreie Aktivitäten in Barcelona ähnlich wie die Folgenden:

    ```output
    1. Visit the iconic Sagrada Família: This breathtaking basilica is an iconic symbol of Barcelona's architecture and is known for its unique design by Antoni Gaudí.

    2. Explore Park Güell: Another masterpiece by Gaudí, this park offers stunning panoramic views of the city, intricate mosaic work, and whimsical architectural elements.

    3. Visit the Picasso Museum: Explore the extensive collection of artworks by the iconic painter Pablo Picasso, showcasing his different periods and styles.
    ```

    > [!NOTE]
    > Wenn Ihr Code die erwartete Ausgabe nicht erzeugt, können Sie den Code im **Lösungsordner** überprüfen.

Sie können die Anwendung weiterhin mit unterschiedlichen Prompts und Kontexthinweisen testen. Ausgezeichnet! Sie haben kontextbezogene Hinweise für das LLM erfolgreich bereitgestellt und den Code so angepasst, dass der Benutzer die Unterhaltung fortsetzen kann.

### Überprüfung

In dieser Übung haben Sie einen Endpunkt für den LLM-Dienst (Large Language Model) erstellt, ein Semantic Kernel-Objekt erstellt, Prompts mithilfe des Semantic Kernel-SDK ausgeführt, Semantic Kernel-Funktionen und Plug-Ins erstellt und die Funktion für automatische Funktionsaufrufe des Semantic Kernel-SDK verwendet, um die Benutzerabsicht an die entsprechenden Plug-Ins weiterzuleiten. Sie haben dem LLM auch unter Verwendung des den Unterhaltungsverlaufs einen Kontext bereitgestellt und den Benutzern und Benutzerinnen erlaubt, die Unterhaltung fortzusetzen. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!