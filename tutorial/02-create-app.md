<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="ffd65-101">Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="ffd65-102">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Mobile App (Xamarin. Forms)** aus, und wählen Sie dann **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 Dialogfeld "Neues Projekt erstellen"](images/new-project-dialog.png)

1. <span data-ttu-id="ffd65-104">Geben `GraphTutorial` Sie im Dialogfeld **Neues Projekt konfigurieren** für den **Projektnamen** und den **Lösungsnamen**ein, und wählen Sie dann **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="ffd65-105">Stellen Sie sicher, dass Sie genau den gleichen Namen für das Visual Studio Projekt eingeben, das in diesen Übungseinheiten angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="ffd65-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="ffd65-106">Der Visual Studio-Projektname wird Teil des Namespace im Code.</span><span class="sxs-lookup"><span data-stu-id="ffd65-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="ffd65-107">Der Code in diesen Anweisungen hängt vom Namespace ab, der mit dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="ffd65-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="ffd65-108">Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie dem von Ihnen beim Erstellen des Projekts eingegebenen Visual Studio-Projektnamen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="ffd65-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 Dialogfeld "Neues Projekt konfigurieren"](images/configure-new-project-dialog.png)

1. <span data-ttu-id="ffd65-110">Wählen Sie im Dialogfeld **neue plattformübergreifende App** die **leere** Vorlage aus, und wählen Sie die Plattformen aus, die Sie unter **Plattformen**erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="ffd65-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="ffd65-111">Wählen Sie **OK** aus, um die Lösung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ffd65-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 neue plattformübergreifende App-Dialogfeld](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="ffd65-113">Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="ffd65-113">Install packages</span></span>

<span data-ttu-id="ffd65-114">Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="ffd65-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="ffd65-115">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) zum behandeln Azure AD Authentifizierung und Tokenverwaltung.</span><span class="sxs-lookup"><span data-stu-id="ffd65-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="ffd65-116">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für das tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ffd65-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="ffd65-117">Wählen Sie **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-117">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="ffd65-118">Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="ffd65-118">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="ffd65-119">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="ffd65-119">Design the app</span></span>

<span data-ttu-id="ffd65-120">Aktualisieren Sie zunächst die `App` Klasse, um Variablen hinzuzufügen, um den Authentifizierungsstatus und den angemeldeten Benutzer nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="ffd65-120">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="ffd65-121">Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und erweitern Sie dann die Datei **app. XAML** .</span><span class="sxs-lookup"><span data-stu-id="ffd65-121">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="ffd65-122">Öffnen Sie die Datei **app.XAML.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffd65-122">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="ffd65-123">Fügen Sie `INotifyPropertyChanged` die Schnittstelle zur Klassendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffd65-123">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="ffd65-124">Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffd65-124">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="ffd65-125">Fügen Sie der `App` -Klasse die folgenden Funktionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffd65-125">Add the following functions to the `App` class.</span></span> <span data-ttu-id="ffd65-126">Die `SignIn`- `SignOut`,- `GetUserInfo` und-Funktionen sind jetzt nur Platzhalter.</span><span class="sxs-lookup"><span data-stu-id="ffd65-126">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

1. <span data-ttu-id="ffd65-127">Die `GetUserPhoto` -Funktion gibt jetzt ein Standard Foto zurück.</span><span class="sxs-lookup"><span data-stu-id="ffd65-127">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="ffd65-128">Sie können entweder eine eigene Datei bereitstellen oder die im Beispiel verwendete [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)herunterladen.</span><span class="sxs-lookup"><span data-stu-id="ffd65-128">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="ffd65-129">Wenn Sie Ihre eigene Datei verwenden, benennen Sie Sie in **No-Profile-PIC. png**um.</span><span class="sxs-lookup"><span data-stu-id="ffd65-129">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="ffd65-130">Kopieren Sie die Datei in das Verzeichnis **./GraphTutorial/GraphTutorial** .</span><span class="sxs-lookup"><span data-stu-id="ffd65-130">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="ffd65-131">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-131">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="ffd65-132">Ändern Sie im Fenster **Eigenschaften** den Wert der **Aktion erstellen** in **eingebettete Ressource**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-132">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![Screenshot des Eigenschaftenfensters für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="ffd65-134">App-Navigation</span><span class="sxs-lookup"><span data-stu-id="ffd65-134">App navigation</span></span>

<span data-ttu-id="ffd65-135">In diesem Abschnitt ändern Sie die Hauptseite der Anwendung in eine [Master-Detail-Seite](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="ffd65-135">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="ffd65-136">Dadurch wird ein Navigationsmenü bereitgestellt, um zwischen der Ansicht in der APP zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="ffd65-136">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="ffd65-137">Öffnen Sie die Datei "Haupt **. XAML** " im **GraphTutorial** -Projekt, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-137">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="ffd65-138">Implementieren des Menüs</span><span class="sxs-lookup"><span data-stu-id="ffd65-138">Implement the menu</span></span>

1. <span data-ttu-id="ffd65-139">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **neuer Ordner**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="ffd65-140">Benennen Sie den Ordner `Models`.</span><span class="sxs-lookup"><span data-stu-id="ffd65-140">Name the folder `Models`.</span></span>

1. <span data-ttu-id="ffd65-141">Klicken Sie mit der rechten Maustaste auf den Ordner **Models** , und wählen Sie dann **Hinzufügen**und dann **Klasse...**. Nennen Sie die `NavMenuItem` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="ffd65-142">Öffnen Sie die Datei **NavMenuItem.cs** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="ffd65-143">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `MenuPage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="ffd65-143">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="ffd65-144">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-144">Select **Add**.</span></span>

1. <span data-ttu-id="ffd65-145">Öffnen Sie die Datei **MenuPage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-145">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="ffd65-146">Erweitern Sie **MenuPage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **MenuPage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="ffd65-146">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="ffd65-147">Ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-147">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="ffd65-148">In **MenuPage.XAML.cs**werden von Visual Studio Fehler gemeldet.</span><span class="sxs-lookup"><span data-stu-id="ffd65-148">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="ffd65-149">Diese Fehler werden in einem späteren Schritt behoben.</span><span class="sxs-lookup"><span data-stu-id="ffd65-149">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="ffd65-150">Implementieren der Willkommensseite</span><span class="sxs-lookup"><span data-stu-id="ffd65-150">Implement the welcome page</span></span>

1. <span data-ttu-id="ffd65-151">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `WelcomePage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="ffd65-151">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="ffd65-152">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-152">Select **Add**.</span></span> <span data-ttu-id="ffd65-153">Öffnen Sie die Datei **WelcomePage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-153">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="ffd65-154">Erweitern Sie **WelcomePage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **WelcomePage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="ffd65-154">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="ffd65-155">Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ffd65-155">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a><span data-ttu-id="ffd65-156">Seite "Kalender hinzufügen"</span><span class="sxs-lookup"><span data-stu-id="ffd65-156">Add calendar page</span></span>

<span data-ttu-id="ffd65-157">Fügen Sie nun eine Kalender Seite hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffd65-157">Now add a calendar page.</span></span> <span data-ttu-id="ffd65-158">Dies ist nur ein Platzhalter für jetzt.</span><span class="sxs-lookup"><span data-stu-id="ffd65-158">This will just be a placeholder for now.</span></span>

1. <span data-ttu-id="ffd65-159">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `CalendarPage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="ffd65-159">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="ffd65-160">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-160">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="ffd65-161">Aktualisieren der Hauptseite Code-Behind</span><span class="sxs-lookup"><span data-stu-id="ffd65-161">Update MainPage code-behind</span></span>

<span data-ttu-id="ffd65-162">Nachdem alle Seiten vorhanden sind, aktualisieren Sie den Code-Behind für Hauptseite **. XAML**.</span><span class="sxs-lookup"><span data-stu-id="ffd65-162">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="ffd65-163">Erweitern Sie Haupt **. XAML** im **Projektmappen-Explorer** , und öffnen Sie die Datei **MainPage.XAML.cs** , und ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffd65-163">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="ffd65-164">Speichern Sie alle Änderungen.</span><span class="sxs-lookup"><span data-stu-id="ffd65-164">Save all of your changes.</span></span> <span data-ttu-id="ffd65-165">Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, IOS oder UWP), und wählen Sie **als Startprojekt festlegen**aus.</span><span class="sxs-lookup"><span data-stu-id="ffd65-165">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="ffd65-166">Drücken Sie **F5** , oder wählen Sie **Debug > starten des Debuggings** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ffd65-166">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Screenshots der Android-, IOS-und UWP-Versionen der Anwendung](./images/welcome-page.png)
