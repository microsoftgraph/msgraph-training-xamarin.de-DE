<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.

Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** . Wählen Sie **#a0 Klasse hinzufügen**aus.... Nennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**aus. Öffnen Sie die Datei **OAuthSettings.cs** , und ersetzen Sie den Inhalt durch Folgendes.

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

Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus Ihrer APP-Registrierung.

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.

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

Erstellen Sie als nächstes eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.

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

Aktualisieren Sie jetzt `SignIn` die-Funktion, `PublicClientApplication` um ein Zugriffstoken abzurufen. Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.

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

Dieser Code versucht zunächst, ein Zugriffstoken automatisch abzurufen. Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), wird dies erfolgreich ausgeführt, und es gibt keinen Grund, den Benutzer aufzufordern. Wenn keine Benutzerinformationen im Cache vorhanden sind, löst die `AcquireTokenSilent().ExecuteAsync()` Funktion eine `MsalUiRequiredException`aus. In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenInteractive`.

Aktualisieren Sie nun `SignOut` die-Funktion, um die Benutzerinformationen aus dem Cache zu entfernen. Fügen Sie am Anfang der `SignOut` Funktion den folgenden Code hinzu.

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

### <a name="update-android-project-to-enable-sign-in"></a>Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung

Bei Verwendung in einem Xamarin-Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).

Zunächst müssen Sie das Android-Manifest aktualisieren, um den Umleitungs-URI zu registrieren. Erweitern Sie im **GraphTutorial. Android** -Projekt den Ordner **Eigenschaften** , und öffnen Sie dann **AndroidManifest. XML**. Wenn Sie Visual Studio für Mac verwenden, wechseln Sie mithilfe der Registerkarten am unteren Rand der Datei zur **Quell** Ansicht. Ersetzen Sie den gesamten Inhalt durch Folgendes.

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

Ersetzen `YOUR_APP_ID_HERE` Sie durch Ihre APP-Registrierung durch Ihre Anwendungs-ID.

Öffnen Sie dann **MainActivity.cs** , und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

Überschreiben Sie dann `OnActivityResult` die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übergeben. Fügen Sie der `MainActivity` -Klasse Folgendes hinzu.

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Fügen Sie schließlich in `OnCreate` der-Funktion die folgende Folge nach der `LoadApplication(new App());` -Verbindung hinzu.

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a>Update des IOS-Projekts zur Aktivierung der Anmeldung

> [!IMPORTANT]
> Da für MSAL die Verwendung einer Datei mit Berechtigungen. plist erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die Bereitstellung zu aktivieren. Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie die **Berechtigungen. plist** in das Feld **benutzerdefinierte Berechtigungen** in **GraphTutorial. IOS** -Projekteinstellungen, **Build->IOS-Bundle-Signierung**hinzufügen. Weitere Informationen finden Sie unter [Device Provisioning for Xamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).

Bei Verwendung in einem Xamarin IOS-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).

Zunächst müssen Sie den Schlüsselbund-Zugriff aktivieren. Erweitern Sie im Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei entitles **. plist** . Suchen Sie nach der **Schlüsselbund** Berechtigung, und wählen Sie **Schlüsselbund aktivieren**aus. Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.YOUR_DOMAIN.GraphTutorial`Format hinzu.

![Screenshot der Schlüsselbund-Berechtigungskonfiguration](./images/enable-keychain-access.png)

Als nächstes müssen Sie den standardmäßigen MSAL-Umleitungs-URI als URL-Typ registrieren, den Ihre APP handhabt. Öffnen Sie die Datei **Info. plist** , und nehmen Sie die folgenden Änderungen vor.

- Überprüfen Sie auf der Registerkarte **Anwendung** , ob der Wert der **Bundle-ID** mit dem Wert übereinstimmt, den Sie für **Schlüsselbund Gruppen** in **Berechtigungen. plist**festgelegt haben. Wenn dies nicht der Fall ist, aktualisieren Sie Sie jetzt.
- Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Fügen Sie hier einen URL-Typ mit den folgenden Werten hinzu:
  - **Bezeichner**: wird auf den Wert der **Bundle-ID** festgelegt.
  - **URL-Schemas**: auf `msal{YOUR-APP-ID}`festlegen. Wenn Ihre APP-ID beispielsweise lautet `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, würden Sie dies auf `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`festlegen.
  - **Rolle**:`Editor`
  - **Symbol**: leer lassen

![Ein Screenshot des Abschnitts "URL-Typen" von Info. plist](./images/add-url-type.png)

Aktualisieren Sie schließlich den Code im **GraphTutorial. IOS** -Projekt, um die Umleitung während der Authentifizierung zu verarbeiten. Öffnen Sie die Datei **AppDelegate.cs** , und fügen `using` Sie die folgende Anweisung am Anfang der Datei hinzu.

```cs
using Microsoft.Identity.Client;
```

Fügen Sie die folgende- `FinishedLaunching` Funktion direkt vor der `LoadApplication(new App());` -Verbindung ein.

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

Überschreiben Sie schließlich `OpenUrl` die-Funktion, um die URL an die MSAL-Bibliothek zu übergeben. Fügen Sie der `AppDelegate` -Klasse Folgendes hinzu.

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a>Speichern der Token

Wenn die Microsoft-Authentifizierungsbibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher für die standardmäßige Zwischenspeicherung von Token. Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .

## <a name="test-sign-in"></a>Testen der Anmeldung

Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie zu diesem Zeitpunkt aufgefordert, sich anzumelden. Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.

![Ein Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Abrufen von Benutzer Details

Aktualisieren Sie nun `SignIn` die-Funktion in **app.XAML.cs** , `GraphServiceClient`um die zu initialisieren. Fügen Sie vor der `await GetUserInfo();` Codezeile den folgenden Code hinzu.

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

Aktualisieren Sie nun `GetUserInfo` die-Funktion, um die Benutzer Details aus dem Microsoft Graph abzurufen. Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.

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

Wenn Sie Ihre Änderungen speichern und die APP jetzt ausführen, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.
