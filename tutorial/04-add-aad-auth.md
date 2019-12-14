<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f0e2a-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f0e2a-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f0e2a-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="f0e2a-104">Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** .</span><span class="sxs-lookup"><span data-stu-id="f0e2a-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="f0e2a-105">Wählen Sie **#a0 Klasse hinzufügen**aus.... Nennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span> <span data-ttu-id="f0e2a-106">Öffnen Sie die Datei **OAuthSettings.cs** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="f0e2a-107">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus Ihrer APP-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f0e2a-108">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f0e2a-109">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="f0e2a-109">Implement sign-in</span></span>

<span data-ttu-id="f0e2a-110">Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="f0e2a-111">Ändern Sie die Deklarations Reihe der **App** -Klasse, um den Namenskonflikt für **Application**aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

```cs
public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
```

<span data-ttu-id="f0e2a-112">Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-112">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static object AuthUIParent = null;

// Keychain security group used by iOS version of the app
public static string iOSKeychainSecurityGroup = null;

// Microsoft Authentication client for native/mobile apps
public static IPublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;

// Microsoft Graph permissions used by app
private readonly string[] Scopes = OAuthSettings.Scopes.Split(' ');
```

<span data-ttu-id="f0e2a-113">Erstellen Sie als nächstes eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

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

<span data-ttu-id="f0e2a-114">Aktualisieren Sie jetzt `SignIn` die-Funktion, `PublicClientApplication` um ein Zugriffstoken abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-114">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="f0e2a-115">Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-115">Add the following code above the `await GetUserInfo();` line.</span></span>

```cs
// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();

    var silentAuthResult = await PCA
        .AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
        .ExecuteAsync();

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Successful silent authentication for: {silentAuthResult.Account.Username}");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException msalEx)
{
    // This exception is thrown when an interactive sign-in is required.
    Debug.WriteLine("Silent token request failed, user needs to sign-in: " + msalEx.Message);
    // Prompt the user to sign-in
    var interactiveRequest = PCA.AcquireTokenInteractive(Scopes);

    if (AuthUIParent != null)
    {
        interactiveRequest = interactiveRequest
            .WithParentActivityOrWindow(AuthUIParent);
    }

    var interactiveAuthResult = await interactiveRequest.ExecuteAsync();
    Debug.WriteLine($"Successful interactive authentication for: {interactiveAuthResult.Account.Username}");
    Debug.WriteLine($"Access token: {interactiveAuthResult.AccessToken}");
}
catch (Exception ex)
{
    Debug.WriteLine("Authentication failed. See exception messsage for more details: " + ex.Message);
}
```

<span data-ttu-id="f0e2a-116">Dieser Code versucht zunächst, ein Zugriffstoken automatisch abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="f0e2a-117">Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), wird dies erfolgreich ausgeführt, und es gibt keinen Grund, den Benutzer aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="f0e2a-118">Wenn keine Benutzerinformationen im Cache vorhanden sind, löst die `AcquireTokenSilent().ExecuteAsync()` Funktion eine `MsalUiRequiredException`aus.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="f0e2a-119">In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenInteractive`.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

<span data-ttu-id="f0e2a-120">Aktualisieren Sie nun `SignOut` die-Funktion, um die Benutzerinformationen aus dem Cache zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-120">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="f0e2a-121">Fügen Sie am Anfang der `SignOut` Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-121">Add the following code to the beginning of the `SignOut` function.</span></span>

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

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="f0e2a-122">Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="f0e2a-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="f0e2a-123">Bei Verwendung in einem Xamarin-Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="f0e2a-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="f0e2a-124">Zunächst müssen Sie das Android-Manifest aktualisieren, um den Umleitungs-URI zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-124">First, you need to update the Android manifest to register the redirect URI.</span></span> <span data-ttu-id="f0e2a-125">Erweitern Sie im **GraphTutorial. Android** -Projekt den Ordner **Eigenschaften** , und öffnen Sie dann **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-125">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="f0e2a-126">Wenn Sie Visual Studio für Mac verwenden, wechseln Sie mithilfe der Registerkarten am unteren Rand der Datei zur **Quell** Ansicht.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-126">If you're using Visual Studio for Mac, switch to the **Source** view using the tabs at the bottom of the file.</span></span> <span data-ttu-id="f0e2a-127">Ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-127">Replace the entire contents with the following.</span></span>

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

<span data-ttu-id="f0e2a-128">Ersetzen `YOUR_APP_ID_HERE` Sie durch Ihre APP-Registrierung durch Ihre Anwendungs-ID.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-128">Replace `YOUR_APP_ID_HERE` with your application ID from your app registration.</span></span>

<span data-ttu-id="f0e2a-129">Öffnen Sie dann **MainActivity.cs** , und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-129">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="f0e2a-130">Überschreiben Sie dann `OnActivityResult` die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-130">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="f0e2a-131">Fügen Sie der `MainActivity` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-131">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

<span data-ttu-id="f0e2a-132">Fügen Sie schließlich in `OnCreate` der-Funktion die folgende Folge nach der `LoadApplication(new App());` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-132">Finally, in the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="f0e2a-133">Update des IOS-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="f0e2a-133">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f0e2a-134">Da für MSAL die Verwendung einer Datei mit Berechtigungen. plist erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die Bereitstellung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-134">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="f0e2a-135">Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie die **Berechtigungen. plist** in das Feld **benutzerdefinierte Berechtigungen** in **GraphTutorial. IOS** -Projekteinstellungen, **Build->IOS-Bundle-Signierung**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-135">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="f0e2a-136">Weitere Informationen finden Sie unter [Device Provisioning for Xamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="f0e2a-136">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="f0e2a-137">Bei Verwendung in einem Xamarin IOS-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="f0e2a-137">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="f0e2a-138">Zunächst müssen Sie den Schlüsselbund-Zugriff aktivieren.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-138">First, you need to enable Keychain access.</span></span> <span data-ttu-id="f0e2a-139">Erweitern Sie im Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei **entitles. plist** .</span><span class="sxs-lookup"><span data-stu-id="f0e2a-139">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="f0e2a-140">Suchen Sie nach der **Schlüsselbund** Berechtigung, und wählen Sie **Schlüsselbund aktivieren**aus.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-140">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="f0e2a-141">Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.YOUR_DOMAIN.GraphTutorial`Format hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-141">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![Screenshot der Schlüsselbund-Berechtigungskonfiguration](./images/enable-keychain-access.png)

<span data-ttu-id="f0e2a-143">Als nächstes müssen Sie den standardmäßigen MSAL-Umleitungs-URI als URL-Typ registrieren, den Ihre APP handhabt.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-143">Next, you need to register the default MSAL redirect URI as a URL type that your app handles.</span></span> <span data-ttu-id="f0e2a-144">Öffnen Sie die Datei **Info. plist** , und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-144">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="f0e2a-145">Überprüfen Sie auf der Registerkarte **Anwendung** , ob der Wert der **Bundle-ID** mit dem Wert übereinstimmt, den Sie für **Schlüsselbund Gruppen** in **Berechtigungen. plist**festgelegt haben.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-145">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="f0e2a-146">Wenn dies nicht der Fall ist, aktualisieren Sie Sie jetzt.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-146">If it doesn't, update it now.</span></span>
- <span data-ttu-id="f0e2a-147">Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** .</span><span class="sxs-lookup"><span data-stu-id="f0e2a-147">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="f0e2a-148">Fügen Sie hier einen URL-Typ mit den folgenden Werten hinzu:</span><span class="sxs-lookup"><span data-stu-id="f0e2a-148">Add a URL type here with the following values:</span></span>
  - <span data-ttu-id="f0e2a-149">**Bezeichner**: wird auf den Wert der **Bundle-ID** festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-149">**Identifier**: set to the value of your **Bundle identifier**</span></span>
  - <span data-ttu-id="f0e2a-150">**URL-Schemas**: auf `msal{YOUR-APP-ID}`festlegen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-150">**URL Schemes**: set to `msal{YOUR-APP-ID}`.</span></span> <span data-ttu-id="f0e2a-151">Wenn Ihre APP-ID beispielsweise lautet `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, würden Sie dies auf `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`festlegen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-151">For example, if your app ID is `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, you would set this to `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.</span></span>
  - <span data-ttu-id="f0e2a-152">**Rolle**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="f0e2a-152">**Role**: `Editor`</span></span>
  - <span data-ttu-id="f0e2a-153">**Symbol**: leer lassen</span><span class="sxs-lookup"><span data-stu-id="f0e2a-153">**Icon**: Leave empty</span></span>

![Ein Screenshot des Abschnitts "URL-Typen" von Info. plist](./images/add-url-type.png)

<span data-ttu-id="f0e2a-155">Aktualisieren Sie schließlich den Code im **GraphTutorial. IOS** -Projekt, um die Umleitung während der Authentifizierung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-155">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="f0e2a-156">Öffnen Sie die Datei **AppDelegate.cs** , und fügen `using` Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-156">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="f0e2a-157">Fügen Sie die folgende- `FinishedLaunching` Funktion direkt vor der `LoadApplication(new App());` -Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-157">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

<span data-ttu-id="f0e2a-158">Überschreiben Sie schließlich `OpenUrl` die-Funktion, um die URL an die MSAL-Bibliothek zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-158">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="f0e2a-159">Fügen Sie der `AppDelegate` -Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-159">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="f0e2a-160">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="f0e2a-160">Storing the tokens</span></span>

<span data-ttu-id="f0e2a-161">Wenn die Microsoft-Authentifizierungsbibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher für die standardmäßige Zwischenspeicherung von Token.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-161">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="f0e2a-162">Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="f0e2a-162">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="f0e2a-163">Testen der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="f0e2a-163">Test sign-in</span></span>

<span data-ttu-id="f0e2a-164">Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie zu diesem Zeitpunkt aufgefordert, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-164">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="f0e2a-165">Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-165">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Ein Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="f0e2a-167">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="f0e2a-167">Get user details</span></span>

<span data-ttu-id="f0e2a-168">Fügen Sie der **App** -Klasse eine neue Funktion hinzu, `GraphServiceClient`um die zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-168">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

```cs
private async Task InitializeGraphClientAsync()
{
    var currentAccounts = await PCA.GetAccountsAsync();
    try
    {
        if (currentAccounts.Count() > 0)
        {
            // Initialize Graph client
            GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
                async (requestMessage) =>
                {
                    var result = await PCA.AcquireTokenSilent(Scopes, currentAccounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));

            await GetUserInfo();

            IsSignedIn = true;
        }
        else
        {
            IsSignedIn = false;
        }
    }
    catch(Exception ex)
    {
        Debug.WriteLine(
            $"Failed to initialized graph client. Accounts in the msal cache: {currentAccounts.Count()}. See exception message for details: {ex.Message}");
    }
}
```

<span data-ttu-id="f0e2a-169">Aktualisieren Sie nun `SignIn` die-Funktion in **app.XAML.cs** , um diese Funktion `GetUserInfo`anstelle von aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-169">Now update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="f0e2a-170">Entfernen Sie aus der `SignIn` -Funktion Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-170">Remove the following from the `SignIn` function.</span></span>

```cs
await GetUserInfo();

IsSignedIn = true;
```

<span data-ttu-id="f0e2a-171">Fügen Sie am Ende der `SignIn` Funktion Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-171">Add the following to the end of the `SignIn` function.</span></span>

```cs
await InitializeGraphClientAsync();
```

<span data-ttu-id="f0e2a-172">Aktualisieren Sie nun `GetUserInfo` die-Funktion, um die Benutzer Details aus dem Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-172">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="f0e2a-173">Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-173">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="f0e2a-174">Wenn Sie Ihre Änderungen speichern und die APP jetzt ausführen, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f0e2a-174">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
