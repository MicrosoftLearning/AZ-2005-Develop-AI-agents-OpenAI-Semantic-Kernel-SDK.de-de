---
lab:
  title: Erstellen Sie einen AI-Assistenten mit semantischem Kernel
  description: 'Erfahren Sie, wie Sie den semantischen Kernel verwenden, um einen generativen KI-Assistenten zu erstellen, der DevOps-Aufgaben übernehmen kann.'
---

# Erstellen Sie einen AI-Assistenten mit semantischem Kernel

In dieser Übung entwickeln Sie den Code für einen KI-gesteuerten Assistenten, der Entwicklungsvorgänge automatisieren und Aufgaben rationalisieren soll. Sie verwenden das SDK für den semantischen Kernel, um den KI-Assistenten zu erstellen und ihn mit dem LLM-Dienst (Large Language Model) zu verbinden. Mit dem semantischen Kernel SDK können Sie eine intelligente Anwendung erstellen, die mit dem LLM-Dienst interagieren, auf Abfragen in natürlicher Sprache antworten und dem Benutzenden personalisierte Einblicke bereitstellen kann. Für diese Übung werden Scheinfunktionen bereitgestellt, die typische Devops-Aufgaben darstellen. Legen wir los.

Diese Übung dauert ca. **30** Minuten.

## Setzen Sie ein Chat-Abschlussmodell um.

1. Navigieren Sie zu [https://portal.azure.com](https://portal.azure.com).

1. Erstellen Sie eine neue Azure OpenAI-Ressource mit den Standardeinstellungen.

1. Wählen Sie nach Erstellung der Ressource die Option **Zu Ressource wechseln** aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Gehe zum Azure Foundry-Portal** aus.

1. Wählen Sie **Neue Bereitstellung erstellen** und dann **aus Basismodell bereitstellen** aus.

1. Suchen Sie in der Modellliste nach **gpt-4o**, wählen Sie es aus und bestätigen Sie es.

1. Geben Sie einen Namen für Ihre Bereitstellung ein, und behalten Sie die Standardoptionen bei.

1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zurück zu Ihrer Azure OpenAI-Ressource im Azure-Portal.

1. Wählen Sie unter **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus.

    Sie verwenden die hier verwendeten Daten in der nächsten Aufgabe, um Ihren Kernel zu erstellen. Denken Sie daran, Ihre Schlüssel privat und sicher aufzubewahren.

## Vorbereiten der Anwendungskonfiguration

1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
    rm -r semantic-kernel -f
    git clone https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK semantic-kernel
    ```

    > **TIPP**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Chatanwendung enthält:

    > **Hinweis**: Führen Sie die Schritte für die gewählte Programmiersprache aus.

    **Python**
    ```
    cd semantic-kernel/Allfiles/Labs/Devops/python
    ```

    **C#**
    ```
    cd semantic-kernel/Allfiles/Labs/Devops/c-sharp
    ```

1. Geben Sie im Befehlszeilenfenster der Cloud Shell den folgenden Befehl ein, um die zu verwendenden Bibliotheken zu installieren:

    **Python**
    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install python-dotenv azure-identity semantic-kernel[azure] 
    ```

    **C#**
    ```
    dotnet add package Microsoft.Extensions.Configuration
    dotnet add package Microsoft.Extensions.Configuration.Json
    dotnet add package Microsoft.SemanticKernel
    dotnet add package Microsoft.SemanticKernel.PromptTemplates.Handlebars
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**
    ```
    code .env
    ```

    **C#**
    ```
    code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Werte mit Ihrer Azure OpenAI Dienst-Modell-ID, dem Endpunkt und dem API-Schlüssel.

    **Python**
    ```python
    MODEL_DEPLOYMENT=""
    BASE_URL=""
    API_KEY="
    ```

    **C#**
    ```json
    {
        "modelName": "",
        "endpoint": "",
        "apiKey": ""
    }
    ```

1. Nachdem Sie die Werte aktualisiert haben, verwenden Sie den Befehl **CTRL+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **CTRL+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud-Shell geöffnet bleibt.

## Erstellen eines Plugins für den semantischen Kernel

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**
    ```
    code devops.py
    ```

    **C#**
    ```
    code Program.cs
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a kernel builder with Azure OpenAI chat completion** hinzu:

    **Python**
    ```python
    # Create a kernel builder with Azure OpenAI chat completion
    kernel = Kernel()
    chat_completion = AzureChatCompletion(
        deployment_name=deployment_name,
        api_key=api_key,
        base_url=base_url,
    )
    kernel.add_service(chat_completion)
    ```
    **C#**
     ```c#
    // Create a kernel builder with Azure OpenAI chat completion
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(modelId, endpoint, apiKey);
    var kernel = builder.Build();
    ```

1. Finden Sie am Ende der Datei den Kommentar **Create a kernel function to build the stage environment** und fügen Sie den folgenden Code hinzu, um eine Scheinfunktion für das Plugin zu erstellen, das die Staging-Umgebung erstellt:

    **Python**
    ```python
    # Create a kernel function to build the stage environment
    @kernel_function(name="BuildStageEnvironment")
    def build_stage_environment(self):
        return "Stage build completed."
    ```

    **C#**
    ```c#
    // Create a kernel function to build the stage environment
    [KernelFunction("BuildStageEnvironment")]
    public string BuildStageEnvironment() 
    {
        return "Stage build completed.";
    }
    ```

    Der `KernelFunction`-Decorator deklariert Ihre systemeigene Funktion. Sie verwenden einen beschreibenden Namen für die Funktion, damit die KI sie richtig aufrufen kann. 

1. Navigieren Sie zu dem Kommentar **Import plugins to the kernel** und fügen Sie den folgenden Code hinzu:

    **Python**
    ```python
    # Import plugins to the kernel
    kernel.add_plugin(DevopsPlugin(), plugin_name="DevopsPlugin")
    ```

    **C#**
    ```c#
    // Import plugins to the kernel
    kernel.ImportPluginFromType<DevopsPlugin>();
    ```


1. Fügen Sie unter dem Kommentar **Create prompt execution settings** den folgenden Code hinzu, um die Funktion automatisch aufzurufen:

    **Python**
    ```python
    # Create prompt execution settings
    execution_settings = AzureChatPromptExecutionSettings()
    execution_settings.function_choice_behavior = FunctionChoiceBehavior.Auto()
    ```

    **C#**
    ```c#
    // Create prompt execution settings
    OpenAIPromptExecutionSettings openAIPromptExecutionSettings = new() 
    {
        FunctionChoiceBehavior = FunctionChoiceBehavior.Auto()
    };
    ```

    Wenn Sie diese Einstellung verwenden, kann der Kernel automatisch Funktionen aufrufen, ohne dass diese im Prompt angegeben werden müssen.

1. Fügen Sie den folgenden Code unter dem Kommentar **Create chat history** hinzu:

    **Python**
    ```python
    # Create chat history
    chat_history = ChatHistory()
    ```

    **C#**
    ```c#
    // Create chat history
    var chatCompletionService = kernel.GetRequiredService<IChatCompletionService>();
    ChatHistory chatHistory = [];
    ```

1. Dekommentieren Sie den Codeblock am Standort nach dem Kommentar **User interaction logic**

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

## Führen Sie den Code Ihres Devops-Assistenten aus

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
    az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Fällen ist es ausreichend, *az Anmeldung* zu verwenden. Wenn Sie jedoch Abonnements in mehreren Mandanten haben, müssen Sie möglicherweise den Mandanten mithilfe des Parameters *--tenant* angeben. Siehe [Interaktiv bei Azure anmelden mithilfe der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively) für Details.

1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den bereitgestellten Authentifizierungscode und Ihre Anmeldeinformationen für Azure ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry-Hub enthält, wenn Sie dazu aufgefordert werden.

1. Nachdem Sie sich angemeldet haben, geben Sie den folgenden Befehl ein, um die Anwendung zu starten:


    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie das folgende Prompt ein `Please build the stage environment`

1. Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Assistant: The stage environment has been successfully built.
    ```

1. Geben Sie dann das folgende Prompt ein `Please deploy the stage environment`

1. Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Assistant: The staging site has been deployed successfully.
    ```

1. Drücken Sie die <kbd>EINGABETASTE</kbd>, um das Programm zu beenden.

## Erstellen einer Kernel-Funktion über einen Prompt

1. Fügen Sie den folgenden Code unter dem Kommentar `Create a kernel function to deploy the staging environment` hinzu

     **Python**
    ```python
    # Create a kernel function to deploy the staging environment
    deploy_stage_function = KernelFunctionFromPrompt(
        prompt="""This is the most recent build log:
        {{DevopsPlugin.ReadLogFile}}

        If there are errors, do not deploy the stage environment. Otherwise, invoke the stage deployment function""",
        function_name="DeployStageEnvironment",
        description="Deploy the staging environment"
    )

    kernel.add_function(plugin_name="DeployStageEnvironment", function=deploy_stage_function)
    ```

    **C#**
    ```c#
    // Create a kernel function to deploy the staging environment
    var deployStageFunction = kernel.CreateFunctionFromPrompt(
    promptTemplate: @"This is the most recent build log:
    {{DevopsPlugin.ReadLogFile}}

    If there are errors, do not deploy the stage environment. Otherwise, invoke the stage deployment function",
    functionName: "DeployStageEnvironment",
    description: "Deploy the staging environment"
    );

    kernel.Plugins.AddFromFunctions("DeployStageEnvironment", [deployStageFunction]);
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

1. Geben Sie im Bereich der Cloud-Shell-Befehlszeile den folgenden Befehl ein, um die Anwendung auszuführen:

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie das folgende Prompt ein `Please deploy the stage environment`

1. Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Assistant: The stage environment cannot be deployed because the earlier stage build failed due to unit test errors. Deploying a faulty build to stage may cause eventual issues and compromise the environment.
    ```

    Die Antwort des LLM kann unterschiedlich ausfallen, verhindert aber dennoch, dass Sie die Phase Site bereitstellen können.

## Erstellen eines Handlebar-Prompts

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a handlebars prompt** hinzu:

    **Python**
    ```python
    # Create a handlebars prompt
    hb_prompt = """<message role="system">Instructions: Before creating a new branch for a user, request the new branch name and base branch name/message>
        <message role="user">Can you create a new branch?</message>
        <message role="assistant">Sure, what would you like to name your branch? And which base branch would you like to use?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">"""
    ```

    **C#**
    ```c#
    // Create a handlebars prompt
    string hbprompt = """
        <message role="system">Instructions: Before creating a new branch for a user, request the new branch name and base branch name/message>
        <message role="user">Can you create a new branch?</message>
        <message role="assistant">Sure, what would you like to name your branch? And which base branch would you like to use?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">
        """;
    ```

    In diesem Code erstellen Sie mithilfe des Handlebars-Vorlagenformats eine Few-Shot-Eingabeaufforderung. Der Prompt leitet das Modell dazu an, weitere Informationen vom Benutzenden abzurufen, bevor ein neuer Zweig erstellt wird.

1. Fügen Sie den folgenden Code unter dem Kommentar **Create the prompt template config using handlebars format** hinzu:

    **Python**
    ```python
    # Create the prompt template config using handlebars format
    hb_template = HandlebarsPromptTemplate(
        prompt_template_config=PromptTemplateConfig(
            template=hb_prompt, 
            template_format="handlebars",
            name="CreateBranch", 
            description="Creates a new branch for the user",
            input_variables=[
                InputVariable(name="input", description="The user input", is_required=True)
            ]
        ),
        allow_dangerously_set_content=True,
    )
    ```

    **C#**
    ```c#
    // Create the prompt template config using handlebars format
    var templateFactory = new HandlebarsPromptTemplateFactory();
    var promptTemplateConfig = new PromptTemplateConfig()
    {
        Template = hbprompt,
        TemplateFormat = "handlebars",
        Name = "CreateBranch",
    };
    ```

    Dieser Code erstellt eine Konfiguration der Handlebars-Vorlage aus dem Prompt. Sie können ihn verwenden, um eine Plug-In-Funktion zu erstellen.

1. Fügen Sie den folgenden Code unter dem Kommentar **Create a plugin function from the prompt** hinzu: 

    **Python**
    ```python
    # Create a plugin function from the prompt
    prompt_function = KernelFunctionFromPrompt(
        function_name="CreateBranch",
        description="Creates a branch for the user",
        template_format="handlebars",
        prompt_template=hb_template,
    )
    kernel.add_function(plugin_name="BranchPlugin", function=prompt_function)
    ```

    **C#**
    ```c#
    // Create a plugin function from the prompt
    var promptFunction = kernel.CreateFunctionFromPrompt(promptTemplateConfig, templateFactory);
    var branchPlugin = kernel.CreatePluginFromFunctions("BranchPlugin", [promptFunction]);
    kernel.Plugins.Add(branchPlugin);
    ```

    Dieser Code erstellt eine Plug-In-Funktion für den Prompt und fügt sie dem Kernel hinzu. Nun können Sie Ihre Funktion aufrufen.

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

1. Geben Sie im Bereich der Cloud-Shell-Befehlszeile den folgenden Befehl ein, um die Anwendung auszuführen:

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie den folgenden Text ein: `Please create a new branch`

1. Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Assistant: Could you please provide the following details?

    1. The name of the new branch.
    2. The base branch from which the new branch should be created.
    ```

1. Geben Sie den folgenden Text ein `feature-login main`

1. Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

    ```output
    Assistant: The new branch `feature-login` has been successfully created from `main`.
    ```

## Erforderliche Zustimmung des Benutzenden für Aktionen

1. Am Ende der Datei finden Sie den Kommentar **Erstellen Sie einen Funktionsfilter**, und fügen Sie den folgenden Code hinzu:

    **Python**
    ```python
    # Create a function filter
    async def permission_filter(context: FunctionInvocationContext, next: Callable[[FunctionInvocationContext], Awaitable[None]]) -> None:
        await next(context)
        result = context.result
        
        # Check the plugin and function names
    ```

    **C#**
    ```c#
    // Create a function filter
    class PermissionFilter : IFunctionInvocationFilter
    {
        public async Task OnFunctionInvocationAsync(FunctionInvocationContext context, Func<FunctionInvocationContext, Task> next)
        {
            // Check the plugin and function names
            
            await next(context);
        }
    }
    ```

1. Fügen Sie den folgenden Code unter dem Kommentar **Check the plugin and function names** hinzu, um zu erkennen, wann die Funktion `DeployToProd` aufgerufen wird:

     **Python**
    ```python
    # Check the plugin and function names
    if context.function.plugin_name == "DevopsPlugin" and context.function.name == "DeployToProd":
        # Request user approval
        
        # Proceed if approved
    ```

    **C#**
    ```c#
    // Check the plugin and function names
    if ((context.Function.PluginName == "DevopsPlugin" && context.Function.Name == "DeployToProd"))
    {
        // Request user approval

        // Proceed if approved
    }
    ```

    Dieser Code verwendet das `FunctionInvocationContext`-Objekt, um zu ermitteln, welches Plugin und welche Funktion aufgerufen wurden.

1. Fügen Sie die folgende Logik hinzu, um die Berechtigung des Benutzenden zum Buchen des Flugs anzufordern:

     **Python**
    ```python
    # Request user approval
    print("System Message: The assistant requires approval to complete this operation. Do you approve (Y/N)")
    should_proceed = input("User: ").strip()

    # Proceed if approved
    if should_proceed.upper() != "Y":
        context.result = FunctionResult(
            function=result.function,
            value="The operation was not approved by the user",
        )
    ```

    **C#**
    ```c#
    // Request user approval
    Console.WriteLine("System Message: The assistant requires an approval to complete this operation. Do you approve (Y/N)");
    Console.Write("User: ");
    string shouldProceed = Console.ReadLine()!;

    // Proceed if approved
    if (shouldProceed != "Y")
    {
        context.Result = new FunctionResult(context.Result, "The operation was not approved by the user");
        return;
    }
    ```

1. Navigieren Sie zu dem Kommentar **Add filters to the kernel** und fügen Sie den folgenden Code hinzu:

    **Python**
    ```python
    # Add filters to the kernel
    kernel.add_filter('function_invocation', permission_filter)
    ```

    **C#**
    ```c#
    // Add filters to the kernel
    kernel.FunctionInvocationFilters.Add(new PermissionFilter());
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

1. Geben Sie im Bereich der Cloud-Shell-Befehlszeile den folgenden Befehl ein, um die Anwendung auszuführen:

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Geben Sie einen Prompt ein, um den Build für die Produktion bereitzustellen. Die Antwort sollte ähnlich der folgenden aussehen:

    ```output
    User: Please deploy the build to prod
    System Message: The assistant requires an approval to complete this operation. Do you approve (Y/N)
    User: N
    Assistant: I'm sorry, but I am unable to proceed with the deployment.
    ```

### Überprüfung

In diesem Lab haben Sie einen Endpunkt für den LLM (Large Language Model)-Dienst erstellt, ein semantisches Kernelobjekt erstellt und Prompts mithilfe des semantischen Kernel-SDKs ausgeführt. Sie haben auch Plug-Ins erstellt und Systemmeldungen genutzt, um das Modell zu steuern. Herzlichen Glückwunsch, Sie haben dieses Lab erfolgreich abgeschlossen!