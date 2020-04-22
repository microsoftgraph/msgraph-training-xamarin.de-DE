<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d8101-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d8101-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="d8101-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d8101-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="d8101-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="d8101-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="d8101-104">Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** .</span><span class="sxs-lookup"><span data-stu-id="d8101-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="d8101-105">Wählen Sie **> Klasse hinzufügen**aus.... Nennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d8101-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="d8101-106">Öffnen Sie die Datei **OAuthSettings.cs** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d8101-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="d8101-107">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus Ihrer APP-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="d8101-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d8101-108">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d8101-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="d8101-109">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d8101-109">Implement sign-in</span></span>

1. <span data-ttu-id="d8101-110">Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. <span data-ttu-id="d8101-111">Ändern Sie die Deklarations Reihe der **App** -Klasse, um den Namenskonflikt für **Application**aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="d8101-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="d8101-112">Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="d8101-113">Erstellen Sie als nächstes eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d8101-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="d8101-114">Aktualisieren Sie `SignIn` die-Funktion, `PublicClientApplication` um ein Zugriffstoken abzurufen.</span><span class="sxs-lookup"><span data-stu-id="d8101-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="d8101-115">Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="d8101-116">Dieser Code versucht zunächst, ein Zugriffstoken automatisch abzurufen.</span><span class="sxs-lookup"><span data-stu-id="d8101-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="d8101-117">Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), wird dies erfolgreich ausgeführt, und es gibt keinen Grund, den Benutzer aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="d8101-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="d8101-118">Wenn keine Benutzerinformationen im Cache vorhanden sind, löst die `AcquireTokenSilent().ExecuteAsync()` Funktion eine `MsalUiRequiredException`aus.</span><span class="sxs-lookup"><span data-stu-id="d8101-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="d8101-119">In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenInteractive`.</span><span class="sxs-lookup"><span data-stu-id="d8101-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="d8101-120">Aktualisieren Sie `SignOut` die-Funktion, um die Benutzerinformationen aus dem Cache zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="d8101-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="d8101-121">Fügen Sie am Anfang der `SignOut` Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="d8101-122">Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d8101-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="d8101-123">Bei Verwendung in einem Xamarin-Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="d8101-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

1. <span data-ttu-id="d8101-124">Erweitern Sie im **GraphTutorial. Android** -Projekt den Ordner **Eigenschaften** , und öffnen Sie dann **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="d8101-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="d8101-125">Wenn Sie Visual Studio für Mac verwenden, klicken Sie auf **AndroidManifest. XML** , und wählen Sie dann mit **Quell Code-Editor** **Öffnen mit**aus.</span><span class="sxs-lookup"><span data-stu-id="d8101-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="d8101-126">Ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d8101-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="d8101-127">Öffnen Sie **MainActivity.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="d8101-128">Überschreiben `OnActivityResult` Sie die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8101-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="d8101-129">Fügen Sie der `MainActivity` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="d8101-130">Fügen Sie `OnCreate` in der-Funktion die folgende Folge nach `LoadApplication(new App());` der-Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="d8101-131">Update des IOS-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d8101-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d8101-132">Da für MSAL die Verwendung einer Datei mit Berechtigungen. plist erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die Bereitstellung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="d8101-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="d8101-133">Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie die **Berechtigungen. plist** in das Feld **benutzerdefinierte Berechtigungen** in **GraphTutorial. IOS** -Projekteinstellungen, **Build->IOS-Bundle-Signierung**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8101-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="d8101-134">Weitere Informationen finden Sie unter [Device Provisioning for Xamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="d8101-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="d8101-135">Bei Verwendung in einem Xamarin IOS-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="d8101-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

1. <span data-ttu-id="d8101-136">Erweitern Sie im Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei **entitles. plist** .</span><span class="sxs-lookup"><span data-stu-id="d8101-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="d8101-137">Suchen Sie nach der **Schlüsselbund** Berechtigung, und wählen Sie **Schlüsselbund aktivieren**aus.</span><span class="sxs-lookup"><span data-stu-id="d8101-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="d8101-138">Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.companyname.GraphTutorial`Format hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![Screenshot der Schlüsselbund-Berechtigungskonfiguration](./images/enable-keychain-access.png)

1. <span data-ttu-id="d8101-140">Aktualisieren Sie den Code im **GraphTutorial. IOS-** Projekt, um die Umleitung während der Authentifizierung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8101-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="d8101-141">Öffnen Sie die Datei **AppDelegate.cs** , und fügen `using` Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="d8101-142">Fügen Sie die folgende- `FinishedLaunching` Funktion direkt vor der `LoadApplication(new App());` -Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="d8101-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="d8101-143">Überschreiben `OpenUrl` Sie die-Funktion, um die URL an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8101-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="d8101-144">Fügen Sie der `AppDelegate` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="d8101-145">Speichern des Tokens</span><span class="sxs-lookup"><span data-stu-id="d8101-145">Storing the tokens</span></span>

<span data-ttu-id="d8101-146">Wenn die Microsoft-Authentifizierungsbibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher für die standardmäßige Zwischenspeicherung von Token.</span><span class="sxs-lookup"><span data-stu-id="d8101-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="d8101-147">Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="d8101-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="d8101-148">Testen der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d8101-148">Test sign-in</span></span>

<span data-ttu-id="d8101-149">Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie zu diesem Zeitpunkt aufgefordert, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="d8101-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="d8101-150">Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d8101-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Ein Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="d8101-152">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="d8101-152">Get user details</span></span>

1. <span data-ttu-id="d8101-153">Fügen Sie der **App** -Klasse eine neue Funktion hinzu, `GraphServiceClient`um die zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="d8101-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="d8101-154">Aktualisieren Sie `SignIn` die-Funktion in **app.XAML.cs** , um diese Funktion `GetUserInfo`anstelle von aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d8101-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="d8101-155">Entfernen Sie aus der `SignIn` -Funktion Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d8101-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="d8101-156">Fügen Sie am Ende der `SignIn` Funktion Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8101-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="d8101-157">Aktualisieren Sie `GetUserInfo` die-Funktion, um die Benutzer Details aus dem Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="d8101-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="d8101-158">Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d8101-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="d8101-159">Speichern Sie die Änderungen, und führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d8101-159">Save your changes and run the app.</span></span> <span data-ttu-id="d8101-160">Nachdem die Benutzeroberfläche für die Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d8101-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
