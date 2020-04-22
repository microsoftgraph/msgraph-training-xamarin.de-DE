<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek für .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.

1. Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** . Wählen Sie **> Klasse hinzufügen**aus.... Nennen Sie die `OAuthSettings` Klasse, und wählen Sie **Hinzufügen**aus.

1. Öffnen Sie die Datei **OAuthSettings.cs** , und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus Ihrer APP-Registrierung.

    > [!IMPORTANT]
    > Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `OAuthSettings.cs` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

1. Öffnen Sie die **app.XAML.cs** -Datei im **GraphTutorial** -Projekt, und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. Ändern Sie die Deklarations Reihe der **App** -Klasse, um den Namenskonflikt für **Application**aufzulösen.

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. Fügen Sie der `App` Klasse die folgenden Eigenschaften hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. Erstellen Sie als nächstes eine `PublicClientApplication` neue im Konstruktor der `App` Klasse.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. Aktualisieren Sie `SignIn` die-Funktion, `PublicClientApplication` um ein Zugriffstoken abzurufen. Fügen Sie den folgenden Code oberhalb `await GetUserInfo();` der Linie hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    Dieser Code versucht zunächst, ein Zugriffstoken automatisch abzurufen. Wenn sich die Informationen eines Benutzers bereits im Cache der APP befinden (beispielsweise, wenn der Benutzer die APP zuvor ohne abmelden geschlossen hat), wird dies erfolgreich ausgeführt, und es gibt keinen Grund, den Benutzer aufzufordern. Wenn keine Benutzerinformationen im Cache vorhanden sind, löst die `AcquireTokenSilent().ExecuteAsync()` Funktion eine `MsalUiRequiredException`aus. In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen `AcquireTokenInteractive`.

1. Aktualisieren Sie `SignOut` die-Funktion, um die Benutzerinformationen aus dem Cache zu entfernen. Fügen Sie am Anfang der `SignOut` Funktion den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Aktualisieren des Android-Projekts zur Aktivierung der Anmeldung

Bei Verwendung in einem Xamarin-Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).

1. Erweitern Sie im **GraphTutorial. Android** -Projekt den Ordner **Eigenschaften** , und öffnen Sie dann **AndroidManifest. XML**. Wenn Sie Visual Studio für Mac verwenden, klicken Sie auf **AndroidManifest. XML** , und wählen Sie dann mit **Quell Code-Editor** **Öffnen mit**aus. Ersetzen Sie den gesamten Inhalt durch Folgendes.

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. Öffnen Sie **MainActivity.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. Überschreiben `OnActivityResult` Sie die-Funktion, um die Steuerung an die MSAL-Bibliothek zu übergeben. Fügen Sie der `MainActivity` -Klasse Folgendes hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. Fügen Sie `OnCreate` in der-Funktion die folgende Folge nach `LoadApplication(new App());` der-Verbindung hinzu.

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>Update des IOS-Projekts zur Aktivierung der Anmeldung

> [!IMPORTANT]
> Da für MSAL die Verwendung einer Datei mit Berechtigungen. plist erforderlich ist, müssen Sie Visual Studio mit Ihrem Apple Developer-Konto konfigurieren, um die Bereitstellung zu aktivieren. Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie die **Berechtigungen. plist** in das Feld **benutzerdefinierte Berechtigungen** in **GraphTutorial. IOS** -Projekteinstellungen, **Build->IOS-Bundle-Signierung**hinzufügen. Weitere Informationen finden Sie unter [Device Provisioning for Xamarin. IOS](/xamarin/ios/get-started/installation/device-provisioning).

Bei Verwendung in einem Xamarin IOS-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [spezifische Anforderungen an IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).

1. Erweitern Sie im Projektmappen-Explorer das Projekt **GraphTutorial. IOS** , und öffnen Sie dann die Datei **entitles. plist** .

1. Suchen Sie nach der **Schlüsselbund** Berechtigung, und wählen Sie **Schlüsselbund aktivieren**aus.

1. Fügen Sie in **Schlüsselbund Gruppen**einen Eintrag mit dem `com.companyname.GraphTutorial`Format hinzu.

    ![Screenshot der Schlüsselbund-Berechtigungskonfiguration](./images/enable-keychain-access.png)

1. Aktualisieren Sie den Code im **GraphTutorial. IOS-** Projekt, um die Umleitung während der Authentifizierung zu verarbeiten. Öffnen Sie die Datei **AppDelegate.cs** , und fügen `using` Sie die folgende Anweisung am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. Fügen Sie die folgende- `FinishedLaunching` Funktion direkt vor der `LoadApplication(new App());` -Verbindung ein.

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. Überschreiben `OpenUrl` Sie die-Funktion, um die URL an die MSAL-Bibliothek zu übergeben. Fügen Sie der `AppDelegate` -Klasse Folgendes hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>Speichern des Tokens

Wenn die Microsoft-Authentifizierungsbibliothek in einem Xamarin-Projekt verwendet wird, nutzt Sie den systemeigenen sicheren Speicher für die standardmäßige Zwischenspeicherung von Token. Weitere Informationen finden Sie unter [Token Cache Serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .

## <a name="test-sign-in"></a>Testen der Anmeldung

Wenn Sie die Anwendung ausführen und auf die Schaltfläche **Anmelden** tippen, werden Sie zu diesem Zeitpunkt aufgefordert, sich anzumelden. Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers gedruckt angezeigt werden.

![Ein Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Benutzerdetails abrufen

1. Fügen Sie der **App** -Klasse eine neue Funktion hinzu, `GraphServiceClient`um die zu initialisieren.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. Aktualisieren Sie `SignIn` die-Funktion in **app.XAML.cs** , um diese Funktion `GetUserInfo`anstelle von aufzurufen. Entfernen Sie aus der `SignIn` -Funktion Folgendes.

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. Fügen Sie am Ende der `SignIn` Funktion Folgendes hinzu.

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. Aktualisieren Sie `GetUserInfo` die-Funktion, um die Benutzer Details aus dem Microsoft Graph abzurufen. Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. Speichern Sie die Änderungen, und führen Sie die App aus. Nachdem die Benutzeroberfläche für die Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert wurde.
