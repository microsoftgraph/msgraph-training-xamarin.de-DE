<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="65e39-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung zur Unterstützung der Authentifizierung mit Azure AD.</span><span class="sxs-lookup"><span data-stu-id="65e39-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="65e39-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken für den Aufruf von Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="65e39-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="65e39-103">In diesem Schritt integrieren Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="65e39-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="65e39-104">Erweitern Sie im projektMappen- **Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** .</span><span class="sxs-lookup"><span data-stu-id="65e39-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="65e39-105">Wählen Sie **Add _GT_ Class.**... Benennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="65e39-105">Choose **Add > Class...**. Name the class `OAuthSettings` and choose **Add**.</span></span> <span data-ttu-id="65e39-106">Öffnen Sie die **OAuthSettings.cs** -Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="65e39-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string RedirectUri = "YOUR_REDIRECT_URI_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="65e39-107">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um versehentlich Ihre APP-ID zu vernichten.</span><span class="sxs-lookup"><span data-stu-id="65e39-107">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="65e39-108">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="65e39-108">Implement sign-in</span></span>

<span data-ttu-id="65e39-109">Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie am Anfang der Datei die folgenden Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-109">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="65e39-110">Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-110">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

<span data-ttu-id="65e39-111">Als Nächstes erstellen Sie eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.</span><span class="sxs-lookup"><span data-stu-id="65e39-111">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

<span data-ttu-id="65e39-112">Aktualisieren Sie nun `SignIn` die-Funktion, `PublicClientApplication` um die zum Abrufen eines Zugriffstokens zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="65e39-112">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="65e39-113">Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-113">Add the following code above the `await GetUserInfo();` line.</span></span>

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();
    var silentAuthResult = await PCA.AcquireTokenSilentAsync(
        scopes, accounts.FirstOrDefault());

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    // Prompt the user to sign-in
    var authResult = await PCA.AcquireTokenAsync(scopes, AuthUIParent);
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

<span data-ttu-id="65e39-114">Dieser Code versucht zunächst, ein Zugriffstoken im Hintergrund abzurufen.</span><span class="sxs-lookup"><span data-stu-id="65e39-114">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="65e39-115">Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), ist dies erfolgreich, und es gibt keinen Grund, den Benutzer aufzufordern.</span><span class="sxs-lookup"><span data-stu-id="65e39-115">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="65e39-116">Wenn keine Benutzerinformationen im Cache vorhanden sind, `AcquireTokenSilentAsync` wird eine. `MsalUiRequiredException`</span><span class="sxs-lookup"><span data-stu-id="65e39-116">If there is not a user's information in the cache, the `AcquireTokenSilentAsync` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="65e39-117">In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenAsync`.</span><span class="sxs-lookup"><span data-stu-id="65e39-117">In this case, the code calls the interactive function to get a token, `AcquireTokenAsync`.</span></span>

<span data-ttu-id="65e39-118">Aktualisieren Sie nun `SignOut` die-Funktion, um die Informationen des Benutzers aus dem Cache zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="65e39-118">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="65e39-119">Fügen Sie den folgenden Code am Anfang der `SignOut` Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-119">Add the following code to the beginning of the `SignOut` function.</span></span>

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

<span data-ttu-id="65e39-120">Aktualisieren Sie schließlich die `MainPage` Klasse, um sich beim Laden anzumelden.</span><span class="sxs-lookup"><span data-stu-id="65e39-120">Finally, update the `MainPage` class to sign-in when it loads.</span></span> <span data-ttu-id="65e39-121">Fügen Sie der `MainPage` klasse in **MainPage.XAML.cs**die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-121">Add the following function to the `MainPage` class in **MainPage.xaml.cs**.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="65e39-122">Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="65e39-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="65e39-123">Wenn Sie in einem Xamarin Android-Projekt verwendet werden, weist die Microsoft-AuthentifizierungsBibliothek einige [spezifische Anforderungen für Android auf](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="65e39-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="65e39-124">Zunächst müssen Sie den Umleitungs-URI aus Ihrer APP-Registrierung zum Android-Manifest hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="65e39-124">First, you need to add the redirect URI from your app registration to the Android manifest.</span></span> <span data-ttu-id="65e39-125">Zu diesem Zweck fügen Sie eine neue Aktivität zum **GraphTutorial. Android** -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-125">To do this, add a new activity to the **GraphTutorial.Android** project.</span></span> <span data-ttu-id="65e39-126">Klicken Sie mit der rechten Maustaste auf **GraphTutorial. Android** , und wählen Sie **Hinzufügen**und dann **Neues Element**.... Wählen Sie **Aktivität**, benennen Sie `MsalActivity`die Aktivität, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="65e39-126">Right-click the **GraphTutorial.Android** and choose **Add**, then **New Item...**. Choose **Activity**, name the activity `MsalActivity`, and choose **Add**.</span></span>

<span data-ttu-id="65e39-127">Öffnen Sie die **MsalActivity.cs** -Datei, `[Activity(Label = "MsalActivity")]` und löschen Sie die Linie, und fügen Sie dann die folgenden Attribute oberhalb der Klassendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-127">Open the **MsalActivity.cs** file and delete the `[Activity(Label = "MsalActivity")]` line, then add the following attributes above the class declaration.</span></span>

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

<span data-ttu-id="65e39-128">Öffnen Sie als nächstes **MainActivity.cs** , und fügen `using` Sie am Anfang der Datei die folgenden Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="65e39-129">Überschreiben Sie dann `OnActivityResult` die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="65e39-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="65e39-130">Fügen Sie der `MainActivity` Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="65e39-131">IOS-Projekt aktualisieren, um die Anmeldung zu aktivieren</span><span class="sxs-lookup"><span data-stu-id="65e39-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="65e39-132">Da für MSAL die Verwendung einer Datei mit den Berechtigungen ". plist" erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die bereitstellen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="65e39-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="65e39-133">Weitere Informationen finden Sie unter [Device proXamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="65e39-133">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="65e39-134">Bei Verwendung in einem Xamarin-iOS-Projekt weist die Microsoft-AuthentifizierungsBibliothek einige [spezifische Anforderungen für IOS auf](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="65e39-134">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="65e39-135">Zunächst müssen Sie den Schlüsselbund Zugriff aktivieren.</span><span class="sxs-lookup"><span data-stu-id="65e39-135">First, you need to enable Keychain access.</span></span> <span data-ttu-id="65e39-136">Erweitern Sie in Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei mit den **Berechtigungen. plist** .</span><span class="sxs-lookup"><span data-stu-id="65e39-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="65e39-137">Suchen Sie den **Schlüsselbund** , und wählen Sie **Schlüsselbund aktivieren**aus.</span><span class="sxs-lookup"><span data-stu-id="65e39-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="65e39-138">Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.YOUR_DOMAIN.GraphTutorial`Format hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-138">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![Screenshot der Konfiguration des Keychain-Anspruchs](./images/enable-keychain-access.png)

<span data-ttu-id="65e39-140">Als nächstes müssen Sie den Umleitungs-URI, den Sie im Schritt der APP-Registrierung konfiguriert haben, als URL-Typ registrieren, den Ihre APP verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="65e39-140">Next, you need to register the redirect URI you configured in the app registration step as a URL type that your app handles.</span></span> <span data-ttu-id="65e39-141">Öffnen Sie die Datei " **Info. plist** ", und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="65e39-141">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="65e39-142">Überprüfen Sie auf der Registerkarte **Anwendung** , ob der Wert von **Bundle Identifier** mit dem Wert übereinstimmt, den Sie für **Schlüsselbund Gruppen** in **Berechtigungen. plist**festgelegt haben.</span><span class="sxs-lookup"><span data-stu-id="65e39-142">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="65e39-143">Wenn dies nicht der Fall ist, aktualisieren Sie Sie jetzt.</span><span class="sxs-lookup"><span data-stu-id="65e39-143">If it doesn't, update it now.</span></span>
- <span data-ttu-id="65e39-144">Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** .</span><span class="sxs-lookup"><span data-stu-id="65e39-144">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="65e39-145">Fügen Sie hier einen URL-Typ mit den folgenden Werten hinzu:</span><span class="sxs-lookup"><span data-stu-id="65e39-145">Add a URL type here with the following values:</span></span>
    - <span data-ttu-id="65e39-146">**Identifier**: auf den Wert Ihres Bundle- **Bezeichners** festgelegt</span><span class="sxs-lookup"><span data-stu-id="65e39-146">**Identifier**: set to the value of your **Bundle identifier**</span></span>
    - <span data-ttu-id="65e39-147">**URL-Schemas**: wird auf den Umleitungs-URI aus der APP-Registrierung festgelegt, die mit`msal`</span><span class="sxs-lookup"><span data-stu-id="65e39-147">**URL Schemes**: set to the redirect URI from your app registration that begins with `msal`</span></span>
    - <span data-ttu-id="65e39-148">**Rolle**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="65e39-148">**Role**: `Editor`</span></span>
    - <span data-ttu-id="65e39-149">**Symbol**: leer lassen</span><span class="sxs-lookup"><span data-stu-id="65e39-149">**Icon**: Leave empty</span></span>

![Screenshot des Abschnitts "URL-Typen" von "Info. plist"](./images/add-url-type.png)

<span data-ttu-id="65e39-151">Aktualisieren Sie schließlich den Code im **GraphTutorial. IOS** -Projekt, um die Umleitung während der Authentifizierung zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="65e39-151">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="65e39-152">Öffnen Sie die **AppDelegate.cs** -Datei, und `using` fügen Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-152">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="65e39-153">Fügen Sie die folgende Codezeile `FinishedLaunching` direkt vor der `return` Anweisung hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-153">Add the following line to `FinishedLaunching` function just before the `return` statement.</span></span>

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

<span data-ttu-id="65e39-154">Überschreiben Sie schließlich `OpenUrl` die-Funktion, um die URL an die MSAL-Bibliothek zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="65e39-154">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="65e39-155">Fügen Sie der `AppDelegate` Klasse Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-155">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="65e39-156">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="65e39-156">Storing the tokens</span></span>

<span data-ttu-id="65e39-157">Wenn die Microsoft-AuthentifizierungsBibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher, um Token standardmäßig zwischenzuspeichern.</span><span class="sxs-lookup"><span data-stu-id="65e39-157">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="65e39-158">Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="65e39-158">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="65e39-159">Testen der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="65e39-159">Test sign-in</span></span>

<span data-ttu-id="65e39-160">Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie aufgefordert, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="65e39-160">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="65e39-161">Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="65e39-161">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="65e39-163">Benutzer Details abrufen</span><span class="sxs-lookup"><span data-stu-id="65e39-163">Get user details</span></span>

<span data-ttu-id="65e39-164">Aktualisieren Sie nun `SignIn` die-funktion in **app.XAML.cs** , `GraphServiceClient`um die zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="65e39-164">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="65e39-165">Fügen Sie den folgenden Code vor `await GetUserInfo();` der Leitung hinzu.</span><span class="sxs-lookup"><span data-stu-id="65e39-165">Add the following code before the `await GetUserInfo();` line.</span></span>

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

<span data-ttu-id="65e39-166">Aktualisieren Sie nun `GetUserInfo` die-Funktion, um die Details des Benutzers von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="65e39-166">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="65e39-167">Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="65e39-167">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="65e39-168">Wenn Sie Ihre Änderungen speichern und die APP jetzt ausführen, wird die benutzerOBERFLÄCHE nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="65e39-168">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

