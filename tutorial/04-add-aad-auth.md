<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="401e4-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="401e4-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="401e4-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="401e4-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="401e4-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="401e4-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="401e4-104">Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** .</span><span class="sxs-lookup"><span data-stu-id="401e4-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="401e4-105">Wählen Sie **#a0 Klasse hinzufügen**aus.... Nennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="401e4-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span> <span data-ttu-id="401e4-106">Öffnen Sie die Datei **OAuthSettings.cs** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="401e4-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

<span data-ttu-id="401e4-107">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus Ihrer APP-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="401e4-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="401e4-108">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="401e4-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="401e4-109">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="401e4-109">Implement sign-in</span></span>

<span data-ttu-id="401e4-110">Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="401e4-111">Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-111">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static object AuthUIParent = null;

// Keychain security group used by iOS version of the app
public static string iOSKeychainSecurityGroup = null;

// Microsoft Authentication client for native/mobile apps
public static IPublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

<span data-ttu-id="401e4-112">Erstellen Sie als nächstes eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.</span><span class="sxs-lookup"><span data-stu-id="401e4-112">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

```cs
public App()
{
    InitializeComponent();

    var builder = PublicClientApplicationBuilder
        .Create(OAuthSettings.ApplicationId);

    if (!string.IsNullOrEmpty(iOSKeychainSecurityGroup))
    {
        builder = builder.WithIosKeychainSecurityGroup(iOSKeychainSecurityGroup);
    }

    PCA = builder.Build();

    MainPage = new MainPage();
}
```

<span data-ttu-id="401e4-113">Aktualisieren Sie jetzt `SignIn` die-Funktion, `PublicClientApplication` um ein Zugriffstoken abzurufen.</span><span class="sxs-lookup"><span data-stu-id="401e4-113">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="401e4-114">Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-114">Add the following code above the `await GetUserInfo();` line.</span></span>

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
string accessToken = string.Empty;
try
{
    var accounts = await PCA.GetAccountsAsync();
    if (accounts.Count() > 0)
    {
        var silentAuthResult = await PCA
            .AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();

        Debug.WriteLine("User already signed in.");
        Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
        accessToken = silentAuthResult.AccessToken;
    }
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    Debug.WriteLine("Silent token request failed, user needs to sign-in");
}

if (string.IsNullOrEmpty(accessToken))
{
    // Prompt the user to sign-in
    var interactiveRequest = PCA.AcquireTokenInteractive(scopes);

    if (AuthUIParent != null)
    {
        interactiveRequest = interactiveRequest
            .WithParentActivityOrWindow(AuthUIParent);
    }

    var authResult = await interactiveRequest.ExecuteAsync();
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

<span data-ttu-id="401e4-115">Dieser Code versucht zunächst, ein Zugriffstoken automatisch abzurufen.</span><span class="sxs-lookup"><span data-stu-id="401e4-115">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="401e4-116">Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), wird dies erfolgreich ausgeführt, und es gibt keinen Grund, den Benutzer aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="401e4-116">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="401e4-117">Wenn keine Benutzerinformationen im Cache vorhanden sind, löst die `AcquireTokenSilent().ExecuteAsync()` Funktion eine `MsalUiRequiredException`aus.</span><span class="sxs-lookup"><span data-stu-id="401e4-117">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="401e4-118">In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenInteractive`.</span><span class="sxs-lookup"><span data-stu-id="401e4-118">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

<span data-ttu-id="401e4-119">Aktualisieren Sie nun `SignOut` die-Funktion, um die Benutzerinformationen aus dem Cache zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="401e4-119">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="401e4-120">Fügen Sie am Anfang der `SignOut` Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-120">Add the following code to the beginning of the `SignOut` function.</span></span>

```cs
// Get all cached accounts for the app
// (Should only be one)
var accounts = await PCA.GetAccountsAsync();
while (accounts.Any())
{
    // Remove the account info from the cache
    await PCA.RemoveAsync(accounts.First());
    accounts = await PCA.GetAccountsAsync();
}
```

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="401e4-121">Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="401e4-121">Update Android project to enable sign-in</span></span>

<span data-ttu-id="401e4-122">Bei Verwendung in einem Xamarin-Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="401e4-122">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="401e4-123">Zunächst müssen Sie das Android-Manifest aktualisieren, um den Umleitungs-URI zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="401e4-123">First, you need to update the Android manifest to register the redirect URI.</span></span> <span data-ttu-id="401e4-124">Erweitern Sie im **GraphTutorial. Android** -Projekt den Ordner **Eigenschaften** , und öffnen Sie dann **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="401e4-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="401e4-125">Wenn Sie Visual Studio für Mac verwenden, wechseln Sie mithilfe der Registerkarten am unteren Rand der Datei zur **Quell** Ansicht.</span><span class="sxs-lookup"><span data-stu-id="401e4-125">If you're using Visual Studio for Mac, switch to the **Source** view using the tabs at the bottom of the file.</span></span> <span data-ttu-id="401e4-126">Ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="401e4-126">Replace the entire contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.GraphTutorial">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="28" />
    <application android:label="GraphTutorial.Android">
        <activity android:name="microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msalYOUR_APP_ID_HERE" android:host="auth" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

<span data-ttu-id="401e4-127">Ersetzen `YOUR_APP_ID_HERE` Sie durch Ihre APP-Registrierung durch Ihre Anwendungs-ID.</span><span class="sxs-lookup"><span data-stu-id="401e4-127">Replace `YOUR_APP_ID_HERE` with your application ID from your app registration.</span></span>

<span data-ttu-id="401e4-128">Öffnen Sie dann **MainActivity.cs** , und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="401e4-129">Überschreiben Sie dann `OnActivityResult` die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="401e4-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="401e4-130">Fügen Sie der `MainActivity` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

<span data-ttu-id="401e4-131">Fügen Sie schließlich in `OnCreate` der-Funktion die folgende Folge nach der `LoadApplication(new App());` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-131">Finally, in the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="401e4-132">Update des IOS-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="401e4-132">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="401e4-133">Da für MSAL die Verwendung einer Datei mit Berechtigungen. plist erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die Bereitstellung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="401e4-133">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="401e4-134">Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie die **Berechtigungen. plist** in das Feld **benutzerdefinierte Berechtigungen** in **GraphTutorial. IOS** -Projekteinstellungen, **Build->IOS-Bundle-Signierung**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="401e4-134">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="401e4-135">Weitere Informationen finden Sie unter [Device Provisioning for Xamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="401e4-135">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="401e4-136">Bei Verwendung in einem Xamarin IOS-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="401e4-136">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="401e4-137">Zunächst müssen Sie den Schlüsselbund-Zugriff aktivieren.</span><span class="sxs-lookup"><span data-stu-id="401e4-137">First, you need to enable Keychain access.</span></span> <span data-ttu-id="401e4-138">Erweitern Sie im Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei entitles **. plist** .</span><span class="sxs-lookup"><span data-stu-id="401e4-138">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="401e4-139">Suchen Sie nach der **Schlüsselbund** Berechtigung, und wählen Sie **Schlüsselbund aktivieren**aus.</span><span class="sxs-lookup"><span data-stu-id="401e4-139">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="401e4-140">Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.YOUR_DOMAIN.GraphTutorial`Format hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-140">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![Screenshot der Schlüsselbund-Berechtigungskonfiguration](./images/enable-keychain-access.png)

<span data-ttu-id="401e4-142">Als nächstes müssen Sie den standardmäßigen MSAL-Umleitungs-URI als URL-Typ registrieren, den Ihre APP handhabt.</span><span class="sxs-lookup"><span data-stu-id="401e4-142">Next, you need to register the default MSAL redirect URI as a URL type that your app handles.</span></span> <span data-ttu-id="401e4-143">Öffnen Sie die Datei **Info. plist** , und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="401e4-143">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="401e4-144">Überprüfen Sie auf der Registerkarte **Anwendung** , ob der Wert der **Bundle-ID** mit dem Wert übereinstimmt, den Sie für **Schlüsselbund Gruppen** in **Berechtigungen. plist**festgelegt haben.</span><span class="sxs-lookup"><span data-stu-id="401e4-144">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="401e4-145">Wenn dies nicht der Fall ist, aktualisieren Sie Sie jetzt.</span><span class="sxs-lookup"><span data-stu-id="401e4-145">If it doesn't, update it now.</span></span>
- <span data-ttu-id="401e4-146">Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** .</span><span class="sxs-lookup"><span data-stu-id="401e4-146">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="401e4-147">Fügen Sie hier einen URL-Typ mit den folgenden Werten hinzu:</span><span class="sxs-lookup"><span data-stu-id="401e4-147">Add a URL type here with the following values:</span></span>
  - <span data-ttu-id="401e4-148">**Bezeichner**: wird auf den Wert der **Bundle-ID** festgelegt.</span><span class="sxs-lookup"><span data-stu-id="401e4-148">**Identifier**: set to the value of your **Bundle identifier**</span></span>
  - <span data-ttu-id="401e4-149">**URL-Schemas**: auf `msal{YOUR-APP-ID}`festlegen.</span><span class="sxs-lookup"><span data-stu-id="401e4-149">**URL Schemes**: set to `msal{YOUR-APP-ID}`.</span></span> <span data-ttu-id="401e4-150">Wenn Ihre APP-ID beispielsweise lautet `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, würden Sie dies auf `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`festlegen.</span><span class="sxs-lookup"><span data-stu-id="401e4-150">For example, if your app ID is `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, you would set this to `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.</span></span>
  - <span data-ttu-id="401e4-151">**Rolle**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="401e4-151">**Role**: `Editor`</span></span>
  - <span data-ttu-id="401e4-152">**Symbol**: leer lassen</span><span class="sxs-lookup"><span data-stu-id="401e4-152">**Icon**: Leave empty</span></span>

![Ein Screenshot des Abschnitts "URL-Typen" von Info. plist](./images/add-url-type.png)

<span data-ttu-id="401e4-154">Aktualisieren Sie schließlich den Code im **GraphTutorial. IOS** -Projekt, um die Umleitung während der Authentifizierung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="401e4-154">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="401e4-155">Öffnen Sie die Datei **AppDelegate.cs** , und fügen `using` Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-155">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="401e4-156">Fügen Sie die folgende- `FinishedLaunching` Funktion direkt vor der `LoadApplication(new App());` -Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="401e4-156">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

<span data-ttu-id="401e4-157">Überschreiben Sie schließlich `OpenUrl` die-Funktion, um die URL an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="401e4-157">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="401e4-158">Fügen Sie der `AppDelegate` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-158">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="401e4-159">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="401e4-159">Storing the tokens</span></span>

<span data-ttu-id="401e4-160">Wenn die Microsoft-Authentifizierungsbibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher für die standardmäßige Zwischenspeicherung von Token.</span><span class="sxs-lookup"><span data-stu-id="401e4-160">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="401e4-161">Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="401e4-161">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="401e4-162">Testen der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="401e4-162">Test sign-in</span></span>

<span data-ttu-id="401e4-163">Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie zu diesem Zeitpunkt aufgefordert, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="401e4-163">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="401e4-164">Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="401e4-164">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Ein Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="401e4-166">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="401e4-166">Get user details</span></span>

<span data-ttu-id="401e4-167">Aktualisieren Sie nun `SignIn` die-Funktion in **app.XAML.cs** , `GraphServiceClient`um die zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="401e4-167">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="401e4-168">Fügen Sie vor der `await GetUserInfo();` Codezeile den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="401e4-168">Add the following code before the `await GetUserInfo();` line.</span></span>

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

<span data-ttu-id="401e4-169">Aktualisieren Sie nun `GetUserInfo` die-Funktion, um die Benutzer Details aus dem Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="401e4-169">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="401e4-170">Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="401e4-170">Replace the existing `GetUserInfo` function with the following.</span></span>

```cs
private async Task GetUserInfo()
{
    // Get the logged on user's profile (/me)
    var user = await GraphClient.Me.Request().GetAsync();

    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = user.DisplayName;
    UserEmail = string.IsNullOrEmpty(user.Mail) ? user.UserPrincipalName : user.Mail;
}
```

<span data-ttu-id="401e4-171">Wenn Sie Ihre Änderungen speichern und die APP jetzt ausführen, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="401e4-171">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
