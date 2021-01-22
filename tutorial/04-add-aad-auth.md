<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0fe59-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="0fe59-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="0fe59-103">In diesem Schritt integrieren Sie die [Microsoft-Authentifizierungsbibliothek für .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="0fe59-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="0fe59-104">Erweitern **Sie im Projektmappen-Explorer** das **Projekt GraphTutorial,** und klicken Sie mit der rechten Maustaste auf den Ordner **"Modelle".**</span><span class="sxs-lookup"><span data-stu-id="0fe59-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="0fe59-105">Select **Add > Class...**. Benennen Sie die `OAuthSettings` Klasse, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="0fe59-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="0fe59-106">Öffnen Sie **OAuthSettings.cs** Datei, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="0fe59-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="0fe59-107">Ersetzen `YOUR_APP_ID_HERE` Sie diese durch die Anwendungs-ID aus Ihrer App-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="0fe59-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="0fe59-108">Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass Versehentliches Leck an Ihrer `OAuthSettings.cs` App-ID geht.</span><span class="sxs-lookup"><span data-stu-id="0fe59-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="0fe59-109">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0fe59-109">Implement sign-in</span></span>

1. <span data-ttu-id="0fe59-110">Öffnen Sie **App.xaml.cs** datei im **GraphTutorial-Projekt,** und fügen Sie die folgenden Anweisungen am `using` Oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. <span data-ttu-id="0fe59-111">Ändern  Sie die Deklarationszeile der App-Klasse, um den Namenskonflikt für **Application zu beheben.**</span><span class="sxs-lookup"><span data-stu-id="0fe59-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="0fe59-112">Fügen Sie der Klasse die folgenden Eigenschaften `App` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="0fe59-113">Erstellen Sie als Nächstes im `PublicClientApplication` Konstruktor der Klasse ein `App` neues.</span><span class="sxs-lookup"><span data-stu-id="0fe59-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="0fe59-114">Aktualisieren Sie `SignIn` die Funktion so, dass sie zum `PublicClientApplication` Erhalten eines Zugriffstokens verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0fe59-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="0fe59-115">Fügen Sie den folgenden Code über der Zeile `await GetUserInfo();` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="0fe59-116">Dieser Code versucht zunächst, ein Zugriffstoken im Hintergrund abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="0fe59-117">Wenn sich die Informationen eines Benutzers bereits im Cache der App befinden (z. B. wenn der Benutzer die App zuvor geschlossen hat, ohne sich ab signieren zu müssen), ist dies erfolgreich, und es gibt keinen Grund, den Benutzer dazu aufforderen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="0fe59-118">Wenn sich keine Informationen eines Benutzers im Cache befinden, wird von der `AcquireTokenSilent().ExecuteAsync()` Funktion ein . `MsalUiRequiredException`</span><span class="sxs-lookup"><span data-stu-id="0fe59-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="0fe59-119">In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen: `AcquireTokenInteractive` .</span><span class="sxs-lookup"><span data-stu-id="0fe59-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="0fe59-120">Aktualisieren Sie `SignOut` die Funktion, um die Informationen des Benutzers aus dem Cache zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="0fe59-121">Fügen Sie den folgenden Code am Anfang der Funktion `SignOut` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="0fe59-122">Aktualisieren des Android-Projekts, um die Anmeldung zu aktivieren</span><span class="sxs-lookup"><span data-stu-id="0fe59-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="0fe59-123">Bei Verwendung in einem Xamarin -Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [androidspezifische Anforderungen.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)</span><span class="sxs-lookup"><span data-stu-id="0fe59-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

1. <span data-ttu-id="0fe59-124">Erweitern **Sie im GraphTutorial.Android-Projekt** den Ordner **"Eigenschaften",** und öffnen Sie **AndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="0fe59-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="0fe59-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then Source **Code Editor**.</span><span class="sxs-lookup"><span data-stu-id="0fe59-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="0fe59-126">Ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="0fe59-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="0fe59-127">Öffnen **MainActivity.cs,** und fügen Sie die folgenden `using` Anweisungen am Oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="0fe59-128">Überschreiben Sie `OnActivityResult` die Funktion, um das Steuerelement an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0fe59-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="0fe59-129">Fügen Sie der Klasse Folgendes `MainActivity` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="0fe59-130">Fügen Sie `OnCreate` in der Funktion die folgende Zeile hinter der Zeile `LoadApplication(new App());` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="0fe59-131">Aktualisieren des iOS-Projekts, um die Anmeldung zu aktivieren</span><span class="sxs-lookup"><span data-stu-id="0fe59-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0fe59-132">Da MSAL die Verwendung einer Datei "Entitlements.plist" erfordert, müssen Sie Visual Studio mit Ihrem Apple-Entwicklerkonto konfigurieren, um die Bereitstellung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="0fe59-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="0fe59-133">Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie  **"Entitlements.plist"** im Feld "Benutzerdefinierte Berechtigungen" in den Einstellungen des **GraphTutorial.iOS-Projekts,** **build->iOS Bundle Signing,** hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="0fe59-134">Weitere Informationen finden Sie unter [Gerätebereitstellung für Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="0fe59-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="0fe59-135">Bei Verwendung in einem Xamarin iOS-Projekt hat die Microsoft Authentication Library einige spezifische [Anforderungen für iOS.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)</span><span class="sxs-lookup"><span data-stu-id="0fe59-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

1. <span data-ttu-id="0fe59-136">Erweitern Sie im Projektmappen-Explorer das **Projekt "GraphTutorial.iOS",** und öffnen Sie dann die **Datei "Entitlements.plist".**</span><span class="sxs-lookup"><span data-stu-id="0fe59-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="0fe59-137">Suchen Sie die **Schlüsselbundberechtigung,** und wählen **Sie "Schlüsselkette aktivieren" aus.**</span><span class="sxs-lookup"><span data-stu-id="0fe59-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="0fe59-138">Fügen **Sie in Schlüsselbundgruppen** einen Eintrag im Format `com.companyname.GraphTutorial` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![Screenshot der Konfiguration der Schlüsselbundberechtigung](./images/enable-keychain-access.png)

1. <span data-ttu-id="0fe59-140">Aktualisieren Sie den Code im **GraphTutorial.iOS-Projekt,** um die Umleitung während der Authentifizierung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="0fe59-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="0fe59-141">Öffnen Sie **AppDelegate.cs** Datei, und fügen Sie die folgende Anweisung `using` am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="0fe59-142">Fügen Sie die folgende Zeile `FinishedLaunching` hinzu, damit sie direkt vor der Zeile `LoadApplication(new App());` funktioniert.</span><span class="sxs-lookup"><span data-stu-id="0fe59-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="0fe59-143">Überschreiben Sie `OpenUrl` die Funktion, um die URL an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0fe59-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="0fe59-144">Fügen Sie der Klasse Folgendes `AppDelegate` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="0fe59-145">Speichern des Tokens</span><span class="sxs-lookup"><span data-stu-id="0fe59-145">Storing the tokens</span></span>

<span data-ttu-id="0fe59-146">Wenn die Microsoft Authentication Library in einem Xamarin-Projekt verwendet wird, nutzt sie standardmäßig den systemeigenen sicheren Speicher zum Zwischenspeichern von Token.</span><span class="sxs-lookup"><span data-stu-id="0fe59-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="0fe59-147">Weitere [Informationen finden Sie unter Serialisierung des Tokencaches.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)</span><span class="sxs-lookup"><span data-stu-id="0fe59-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="0fe59-148">Testen der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0fe59-148">Test sign-in</span></span>

<span data-ttu-id="0fe59-149">Wenn Sie jetzt die Anwendung ausführen  und auf die Anmeldeschaltfläche tippen, werden Sie aufgefordert, sich zu anmelden.</span><span class="sxs-lookup"><span data-stu-id="0fe59-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="0fe59-150">Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="0fe59-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="0fe59-152">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="0fe59-152">Get user details</span></span>

1. <span data-ttu-id="0fe59-153">Fügen Sie der App-Klasse eine neue Funktion **hinzu,** um die zu `GraphServiceClient` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="0fe59-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="0fe59-154">Aktualisieren Sie `SignIn` die Funktion in **App.xaml.cs,** um diese Funktion anstelle von `GetUserInfo` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0fe59-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="0fe59-155">Entfernen Sie Folgendes aus der `SignIn` Funktion.</span><span class="sxs-lookup"><span data-stu-id="0fe59-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="0fe59-156">Fügen Sie am Ende der Funktion Folgendes `SignIn` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0fe59-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="0fe59-157">Aktualisieren Sie `GetUserInfo` die Funktion, um die Details des Benutzers aus Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0fe59-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="0fe59-158">Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="0fe59-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="0fe59-159">Speichern Sie die Änderungen, und führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="0fe59-159">Save your changes and run the app.</span></span> <span data-ttu-id="0fe59-160">Nach der Anmeldung wird die Benutzeroberfläche mit dem Anzeigenamen und der E-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="0fe59-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
