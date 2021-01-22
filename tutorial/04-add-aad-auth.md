<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen. In diesem Schritt integrieren Sie die [Microsoft-Authentifizierungsbibliothek für .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung.

1. Erweitern **Sie im Projektmappen-Explorer** das **Projekt GraphTutorial,** und klicken Sie mit der rechten Maustaste auf den Ordner **"Modelle".** Select **Add > Class...**. Benennen Sie die `OAuthSettings` Klasse, und wählen Sie **"Hinzufügen" aus.**

1. Öffnen Sie **OAuthSettings.cs** Datei, und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. Ersetzen `YOUR_APP_ID_HERE` Sie diese durch die Anwendungs-ID aus Ihrer App-Registrierung.

    > [!IMPORTANT]
    > Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass Versehentliches Leck an Ihrer `OAuthSettings.cs` App-ID geht.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

1. Öffnen Sie **App.xaml.cs** datei im **GraphTutorial-Projekt,** und fügen Sie die folgenden Anweisungen am `using` Oberen Rand der Datei hinzu.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. Ändern  Sie die Deklarationszeile der App-Klasse, um den Namenskonflikt für **Application zu beheben.**

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. Fügen Sie der Klasse die folgenden Eigenschaften `App` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. Erstellen Sie als Nächstes im `PublicClientApplication` Konstruktor der Klasse ein `App` neues.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. Aktualisieren Sie `SignIn` die Funktion so, dass sie zum `PublicClientApplication` Erhalten eines Zugriffstokens verwendet wird. Fügen Sie den folgenden Code über der Zeile `await GetUserInfo();` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    Dieser Code versucht zunächst, ein Zugriffstoken im Hintergrund abzurufen. Wenn sich die Informationen eines Benutzers bereits im Cache der App befinden (z. B. wenn der Benutzer die App zuvor geschlossen hat, ohne sich ab signieren zu müssen), ist dies erfolgreich, und es gibt keinen Grund, den Benutzer dazu aufforderen. Wenn sich keine Informationen eines Benutzers im Cache befinden, wird von der `AcquireTokenSilent().ExecuteAsync()` Funktion ein . `MsalUiRequiredException` In diesem Fall ruft der Code die interaktive Funktion auf, um ein Token abzurufen: `AcquireTokenInteractive` .

1. Aktualisieren Sie `SignOut` die Funktion, um die Informationen des Benutzers aus dem Cache zu entfernen. Fügen Sie den folgenden Code am Anfang der Funktion `SignOut` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Aktualisieren des Android-Projekts, um die Anmeldung zu aktivieren

Bei Verwendung in einem Xamarin -Android-Projekt hat die Microsoft-Authentifizierungsbibliothek einige [androidspezifische Anforderungen.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)

1. Erweitern **Sie im GraphTutorial.Android-Projekt** den Ordner **"Eigenschaften",** und öffnen Sie **AndroidManifest.xml**. If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then Source **Code Editor**. Ersetzen Sie den gesamten Inhalt durch Folgendes.

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. Öffnen **MainActivity.cs,** und fügen Sie die folgenden `using` Anweisungen am Oberen Rand der Datei hinzu.

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. Überschreiben Sie `OnActivityResult` die Funktion, um das Steuerelement an die MSAL-Bibliothek zu übergeben. Fügen Sie der Klasse Folgendes `MainActivity` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. Fügen Sie `OnCreate` in der Funktion die folgende Zeile hinter der Zeile `LoadApplication(new App());` hinzu.

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>Aktualisieren des iOS-Projekts, um die Anmeldung zu aktivieren

> [!IMPORTANT]
> Da MSAL die Verwendung einer Datei "Entitlements.plist" erfordert, müssen Sie Visual Studio mit Ihrem Apple-Entwicklerkonto konfigurieren, um die Bereitstellung zu aktivieren. Wenn Sie dieses Lernprogramm im iPhone-Simulator ausführen, müssen Sie  **"Entitlements.plist"** im Feld "Benutzerdefinierte Berechtigungen" in den Einstellungen des **GraphTutorial.iOS-Projekts,** **build->iOS Bundle Signing,** hinzufügen. Weitere Informationen finden Sie unter [Gerätebereitstellung für Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).

Bei Verwendung in einem Xamarin iOS-Projekt hat die Microsoft Authentication Library einige spezifische [Anforderungen für iOS.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)

1. Erweitern Sie im Projektmappen-Explorer das **Projekt "GraphTutorial.iOS",** und öffnen Sie dann die **Datei "Entitlements.plist".**

1. Suchen Sie die **Schlüsselbundberechtigung,** und wählen **Sie "Schlüsselkette aktivieren" aus.**

1. Fügen **Sie in Schlüsselbundgruppen** einen Eintrag im Format `com.companyname.GraphTutorial` hinzu.

    ![Screenshot der Konfiguration der Schlüsselbundberechtigung](./images/enable-keychain-access.png)

1. Aktualisieren Sie den Code im **GraphTutorial.iOS-Projekt,** um die Umleitung während der Authentifizierung zu verarbeiten. Öffnen Sie **AppDelegate.cs** Datei, und fügen Sie die folgende Anweisung `using` am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. Fügen Sie die folgende Zeile `FinishedLaunching` hinzu, damit sie direkt vor der Zeile `LoadApplication(new App());` funktioniert.

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. Überschreiben Sie `OpenUrl` die Funktion, um die URL an die MSAL-Bibliothek zu übergeben. Fügen Sie der Klasse Folgendes `AppDelegate` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>Speichern des Tokens

Wenn die Microsoft Authentication Library in einem Xamarin-Projekt verwendet wird, nutzt sie standardmäßig den systemeigenen sicheren Speicher zum Zwischenspeichern von Token. Weitere [Informationen finden Sie unter Serialisierung des Tokencaches.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)

## <a name="test-sign-in"></a>Testen der Anmeldung

Wenn Sie jetzt die Anwendung ausführen  und auf die Anmeldeschaltfläche tippen, werden Sie aufgefordert, sich zu anmelden. Bei erfolgreicher Anmeldung sollte das Zugriffstoken in der Ausgabe des Debuggers angezeigt werden.

![Screenshot des Ausgabefensters in Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Benutzerdetails abrufen

1. Fügen Sie der App-Klasse eine neue Funktion **hinzu,** um die zu `GraphServiceClient` initialisieren.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. Aktualisieren Sie `SignIn` die Funktion in **App.xaml.cs,** um diese Funktion anstelle von `GetUserInfo` aufrufen. Entfernen Sie Folgendes aus der `SignIn` Funktion.

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. Fügen Sie am Ende der Funktion Folgendes `SignIn` hinzu.

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. Aktualisieren Sie `GetUserInfo` die Funktion, um die Details des Benutzers aus Microsoft Graph zu erhalten. Ersetzen Sie die vorhandene `GetUserInfo`-Funktion durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. Speichern Sie die Änderungen, und führen Sie die App aus. Nach der Anmeldung wird die Benutzeroberfläche mit dem Anzeigenamen und der E-Mail-Adresse des Benutzers aktualisiert.
