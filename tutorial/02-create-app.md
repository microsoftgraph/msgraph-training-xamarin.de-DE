<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="dbe2e-101">Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="dbe2e-102">Wählen Sie **im Dialogfeld "Neues** Projekt erstellen" **die Option "Mobile App (Xamarin.Forms)"** und dann **"Weiter" aus.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 Erstellen eines neuen Projektdialogfelds](images/new-project-dialog.png)

1. <span data-ttu-id="dbe2e-104">Geben Sie **im Dialogfeld "Neues Projekt konfigurieren"** den Projektnamen und den Projektmappennamen ein, und wählen Sie `GraphTutorial` dann **"Erstellen" aus.**  </span><span class="sxs-lookup"><span data-stu-id="dbe2e-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="dbe2e-105">Stellen Sie sicher, dass Sie genau den gleichen Namen für das Visual Studio Projekt eingeben, das in diesen Übungsanleitungen angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="dbe2e-106">Der Visual Studio-Projektname wird Teil des Namespace im Code.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="dbe2e-107">Der Code in diesen Anweisungen hängt vom Namespace ab, der mit dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="dbe2e-108">Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie dem von Ihnen beim Erstellen des Projekts eingegebenen Visual Studio-Projektnamen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 konfigurieren des Dialogfelds "Neues Projekt"](images/configure-new-project-dialog.png)

1. <span data-ttu-id="dbe2e-110">Wählen Sie **im Dialogfeld "Neue plattformübergreifende App"** die Vorlage **"Leer"** aus, und wählen Sie die Plattformen aus, die Sie unter "Plattformen" **erstellen möchten.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="dbe2e-111">Wählen Sie **"OK"** aus, um die Lösung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019: Dialogfeld "Neue plattformübergreifende App"](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="dbe2e-113">Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="dbe2e-113">Install packages</span></span>

<span data-ttu-id="dbe2e-114">Installieren Sie vor dem Wechsel einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="dbe2e-115">[Microsoft.Identity.Client zum](https://www.nuget.org/packages/Microsoft.Identity.Client/) Behandeln der Azure AD-Authentifizierung und Tokenverwaltung.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="dbe2e-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) für Aufrufe von Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="dbe2e-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) für die plattformübergreifende Behandlung von Zeitzonen.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="dbe2e-118">Wählen Sie **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="dbe2e-119">Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="dbe2e-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="dbe2e-120">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="dbe2e-120">Design the app</span></span>

<span data-ttu-id="dbe2e-121">Aktualisieren Sie zunächst die Klasse, um Variablen zum Nachverfolgen des `App` Authentifizierungsstatus und des angemeldeten Benutzers hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="dbe2e-122">Erweitern **Sie im Projektmappen-Explorer** das **Projekt "GraphTutorial",** und erweitern Sie dann die Datei **"App.xaml".**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="dbe2e-123">Öffnen Sie **App.xaml.cs** Datei, und fügen Sie die folgenden Anweisungen `using` am Oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="dbe2e-124">Fügen Sie `INotifyPropertyChanged` die Schnittstelle der Klassendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="dbe2e-125">Fügen Sie der Klasse die folgenden Eigenschaften `App` hinzu.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="dbe2e-126">Fügen Sie der Klasse die folgenden Funktionen `App` hinzu.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="dbe2e-127">Die `SignIn` , und Funktionen sind derzeit nur `SignOut` `GetUserInfo` Platzhalter.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. <span data-ttu-id="dbe2e-128">Die `GetUserPhoto` Funktion gibt jetzt ein Standardfoto zurück.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="dbe2e-129">Sie können entweder Ihre eigene Datei oder die im Beispiel verwendete Datei von [GitHub herunterladen.](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)</span><span class="sxs-lookup"><span data-stu-id="dbe2e-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="dbe2e-130">Wenn Sie Ihre eigene Datei verwenden, benennen Sie sie **in** no-profile-pic.png.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="dbe2e-131">Kopieren Sie die Datei in das **Verzeichnis "./GraphTutorial/GraphTutorial".**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="dbe2e-132">Klicken Sie im Projektmappen-Explorer mit der rechten **Maustaste** auf die Datei, und wählen Sie **"Eigenschaften" aus.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="dbe2e-133">Ändern Sie **im Eigenschaftenfenster** den Wert der **Buildaktion** in **eingebettete Ressource.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![Screenshot des Eigenschaftenfensters für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="dbe2e-135">Navigation in der App</span><span class="sxs-lookup"><span data-stu-id="dbe2e-135">App navigation</span></span>

<span data-ttu-id="dbe2e-136">In diesem Abschnitt ändern Sie die Hauptseite der Anwendung in eine [Master-Detail-Seite.](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)</span><span class="sxs-lookup"><span data-stu-id="dbe2e-136">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="dbe2e-137">Dadurch wird ein Navigationsmenü zum Wechseln zwischen der Ansicht in der App angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="dbe2e-138">Öffnen Sie **die Datei "MainPage.xaml"** im **GraphTutorial-Projekt,** und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a><span data-ttu-id="dbe2e-139">Implementieren des Menüs</span><span class="sxs-lookup"><span data-stu-id="dbe2e-139">Implement the menu</span></span>

1. <span data-ttu-id="dbe2e-140">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neuer Ordner" aus.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="dbe2e-141">Benennen Sie den Ordner `Models`.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="dbe2e-142">Klicken Sie mit der rechten Maustaste auf den Ordner **"Modelle",** und wählen **Sie "Hinzufügen"** und dann **"Klasse... " aus.** Benennen Sie die `NavMenuItem` Klasse, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="dbe2e-143">Öffnen Sie **NavMenuItem.cs** Datei, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="dbe2e-144">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `MenuPage` Seite.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="dbe2e-145">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-145">Select **Add**.</span></span>

1. <span data-ttu-id="dbe2e-146">Öffnen Sie die **Datei "MenuPage.xaml",** und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. <span data-ttu-id="dbe2e-147">Erweitern **Sie "MenuPage.xaml"** im Projektmappen-Explorer, und öffnen Sie **MenuPage.xaml.cs** Datei. </span><span class="sxs-lookup"><span data-stu-id="dbe2e-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="dbe2e-148">Ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="dbe2e-149">Visual Studio werden Fehler in der **MenuPage.xaml.cs.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="dbe2e-150">Diese Fehler werden in einem späteren Schritt behoben.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="dbe2e-151">Implementieren der Willkommensseite</span><span class="sxs-lookup"><span data-stu-id="dbe2e-151">Implement the welcome page</span></span>

1. <span data-ttu-id="dbe2e-152">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `WelcomePage` Seite.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="dbe2e-153">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-153">Select **Add**.</span></span> <span data-ttu-id="dbe2e-154">Öffnen Sie die **Datei "WelcomePage.xaml",** und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. <span data-ttu-id="dbe2e-155">Erweitern **Sie "WelcomePage.xaml"** im Projektmappen-Explorer, und öffnen Sie **WelcomePage.xaml.cs** Datei. </span><span class="sxs-lookup"><span data-stu-id="dbe2e-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="dbe2e-156">Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="dbe2e-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="dbe2e-157">Hinzufügen von Kalender- und neuen Ereignisseiten</span><span class="sxs-lookup"><span data-stu-id="dbe2e-157">Add calendar and new event pages</span></span>

<span data-ttu-id="dbe2e-158">Fügen Sie nun eine Kalenderseite und eine neue Ereignisseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="dbe2e-159">Dies sind vorer denng nur Platzhalter.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="dbe2e-160">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `CalendarPage` Seite.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="dbe2e-161">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-161">Select **Add**.</span></span>

1. <span data-ttu-id="dbe2e-162">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `NewEventPage` Seite.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="dbe2e-163">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="dbe2e-164">Aktualisieren von MainPage-Code-Behind</span><span class="sxs-lookup"><span data-stu-id="dbe2e-164">Update MainPage code-behind</span></span>

<span data-ttu-id="dbe2e-165">Nachdem nun alle Seiten vorhanden sind, aktualisieren Sie den Code-Behind für **MainPage.xaml**.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="dbe2e-166">Erweitern **Sie "MainPage.xaml"** im Projektmappen-Explorer, MainPage.xaml.cs öffnen Sie die Datei, und ersetzen Sie den gesamten Inhalt durch Folgendes.  </span><span class="sxs-lookup"><span data-stu-id="dbe2e-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="dbe2e-167">Speichern Sie alle Änderungen.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-167">Save all of your changes.</span></span> <span data-ttu-id="dbe2e-168">Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, iOS oder UWP), und wählen Sie "Als Startprojekt **festlegen" aus.**</span><span class="sxs-lookup"><span data-stu-id="dbe2e-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="dbe2e-169">Drücken **Sie F5,** oder wählen **Sie "Debuggen" > Debuggen** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dbe2e-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Screenshots der Android-, iOS- und UWP-Versionen der Anwendung](./images/welcome-page.png)
