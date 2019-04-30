<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung zur Unterstützung der Authentifizierung mit Azure AD. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken für den Aufruf von Microsoft Graph abzurufen. In diesem Schritt integrieren Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung.

Erweitern Sie im projektMappen- **Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** . Wählen Sie **Add _GT_ Class.**... Benennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**. Öffnen Sie die **OAuthSettings.cs** -Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.

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
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um versehentlich Ihre APP-ID zu vernichten.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie am Anfang der Datei die folgenden Anweisungen hinzu.

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
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

Als Nächstes erstellen Sie eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

Aktualisieren Sie nun `SignIn` die-Funktion, `PublicClientApplication` um die zum Abrufen eines Zugriffstokens zu verwenden. Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.

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

Dieser Code versucht zunächst, ein Zugriffstoken im Hintergrund abzurufen. Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), ist dies erfolgreich, und es gibt keinen Grund, den Benutzer aufzufordern. Wenn keine Benutzerinformationen im Cache vorhanden sind, `AcquireTokenSilentAsync` wird eine. `MsalUiRequiredException` In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenAsync`.

Aktualisieren Sie nun `SignOut` die-Funktion, um die Informationen des Benutzers aus dem Cache zu entfernen. Fügen Sie den folgenden Code am Anfang der `SignOut` Funktion hinzu.

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

Aktualisieren Sie schließlich die `MainPage` Klasse, um sich beim Laden anzumelden. Fügen Sie der `MainPage` klasse in **MainPage.XAML.cs**die folgende Funktion hinzu.

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a>Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung

Wenn Sie in einem Xamarin Android-Projekt verwendet werden, weist die Microsoft-AuthentifizierungsBibliothek einige [spezifische Anforderungen für Android auf](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).

Zunächst müssen Sie den Umleitungs-URI aus Ihrer APP-Registrierung zum Android-Manifest hinzufügen. Zu diesem Zweck fügen Sie eine neue Aktivität zum **GraphTutorial. Android** -Projekt hinzu. Klicken Sie mit der rechten Maustaste auf **GraphTutorial. Android** , und wählen Sie **Hinzufügen**und dann **Neues Element**.... Wählen Sie **Aktivität**, benennen Sie `MsalActivity`die Aktivität, und wählen Sie **Hinzufügen**.

Öffnen Sie die **MsalActivity.cs** -Datei, `[Activity(Label = "MsalActivity")]` und löschen Sie die Linie, und fügen Sie dann die folgenden Attribute oberhalb der Klassendeklaration hinzu.

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

Öffnen Sie als nächstes **MainActivity.cs** , und fügen `using` Sie am Anfang der Datei die folgenden Anweisungen hinzu.

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

Überschreiben Sie dann `OnActivityResult` die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übertragen. Fügen Sie der `MainActivity` Klasse Folgendes hinzu.

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a>IOS-Projekt aktualisieren, um die Anmeldung zu aktivieren

> [!IMPORTANT]
> Da für MSAL die Verwendung einer Datei mit den Berechtigungen ". plist" erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die bereitstellen zu aktivieren. Weitere Informationen finden Sie unter [Device proXamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).

Bei Verwendung in einem Xamarin-iOS-Projekt weist die Microsoft-AuthentifizierungsBibliothek einige [spezifische Anforderungen für IOS auf](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).

Zunächst müssen Sie den Schlüsselbund Zugriff aktivieren. Erweitern Sie in Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei mit den **Berechtigungen. plist** . Suchen Sie den **Schlüsselbund** , und wählen Sie **Schlüsselbund aktivieren**aus. Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.YOUR_DOMAIN.GraphTutorial`Format hinzu.

![Screenshot der Konfiguration des Keychain-Anspruchs](./images/enable-keychain-access.png)

Als nächstes müssen Sie den Umleitungs-URI, den Sie im Schritt der APP-Registrierung konfiguriert haben, als URL-Typ registrieren, den Ihre APP verarbeitet. Öffnen Sie die Datei " **Info. plist** ", und nehmen Sie die folgenden Änderungen vor.

- Überprüfen Sie auf der Registerkarte **Anwendung** , ob der Wert von **Bundle Identifier** mit dem Wert übereinstimmt, den Sie für **Schlüsselbund Gruppen** in **Berechtigungen. plist**festgelegt haben. Wenn dies nicht der Fall ist, aktualisieren Sie Sie jetzt.
- Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Fügen Sie hier einen URL-Typ mit den folgenden Werten hinzu:
    - **Identifier**: auf den Wert Ihres Bundle- **Bezeichners** festgelegt
    - **URL-Schemas**: wird auf den Umleitungs-URI aus der APP-Registrierung festgelegt, die mit`msal`
    - **Rolle**:`Editor`
    - **Symbol**: leer lassen

![Screenshot des Abschnitts "URL-Typen" von "Info. plist"](./images/add-url-type.png)

Aktualisieren Sie schließlich den Code im **GraphTutorial. IOS** -Projekt, um die Umleitung während der Authentifizierung zu behandeln. Öffnen Sie die **AppDelegate.cs** -Datei, und `using` fügen Sie die folgende Anweisung am Anfang der Datei hinzu.

```cs
using Microsoft.Identity.Client;
```

Fügen Sie die folgende Codezeile `FinishedLaunching` direkt vor der `return` Anweisung hinzu.

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

Überschreiben Sie schließlich `OpenUrl` die-Funktion, um die URL an die MSAL-Bibliothek zu übertragen. Fügen Sie der `AppDelegate` Klasse Folgendes hinzu.

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

Wenn die Microsoft-AuthentifizierungsBibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher, um Token standardmäßig zwischenzuspeichern. Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .

## <a name="test-sign-in"></a>Testen der Anmeldung

Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie aufgefordert, sich anzumelden. Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.

![Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Benutzer Details abrufen

Aktualisieren Sie nun `SignIn` die-funktion in **app.XAML.cs** , `GraphServiceClient`um die zu initialisieren. Fügen Sie den folgenden Code vor `await GetUserInfo();` der Leitung hinzu.

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

Aktualisieren Sie nun `GetUserInfo` die-Funktion, um die Details des Benutzers von Microsoft Graph zu erhalten. Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.

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

Wenn Sie Ihre Änderungen speichern und die APP jetzt ausführen, wird die benutzerOBERFLÄCHE nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.

