<!-- markdownlint-disable MD002 MD041 -->

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen**.

1. Wählen Sie **im Dialogfeld "Neues** Projekt erstellen" **die Option "Mobile App (Xamarin.Forms)"** und dann **"Weiter" aus.**

    ![Visual Studio 2019 Erstellen eines neuen Projektdialogfelds](images/new-project-dialog.png)

1. Geben Sie **im Dialogfeld "Neues Projekt konfigurieren"** den Projektnamen und den Projektmappennamen ein, und wählen Sie `GraphTutorial` dann **"Erstellen" aus.**  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass Sie genau den gleichen Namen für das Visual Studio Projekt eingeben, das in diesen Übungsanleitungen angegeben ist. Der Visual Studio-Projektname wird Teil des Namespace im Code. Der Code in diesen Anweisungen hängt vom Namespace ab, der mit dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen übereinstimmt. Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie dem von Ihnen beim Erstellen des Projekts eingegebenen Visual Studio-Projektnamen entsprechen.

    ![Visual Studio 2019 konfigurieren des Dialogfelds "Neues Projekt"](images/configure-new-project-dialog.png)

1. Wählen Sie **im Dialogfeld "Neue plattformübergreifende App"** die Vorlage **"Leer"** aus, und wählen Sie die Plattformen aus, die Sie unter "Plattformen" **erstellen möchten.** Wählen Sie **"OK"** aus, um die Lösung zu erstellen.

    ![Visual Studio 2019: Dialogfeld "Neue plattformübergreifende App"](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>Installieren von Paketen

Installieren Sie vor dem Wechsel einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.

- [Microsoft.Identity.Client zum](https://www.nuget.org/packages/Microsoft.Identity.Client/) Behandeln der Azure AD-Authentifizierung und Tokenverwaltung.
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) für Aufrufe von Microsoft Graph.
- [TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) für die plattformübergreifende Behandlung von Zeitzonen.

1. Wählen Sie **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.

1. Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein:

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>Entwerfen der App

Aktualisieren Sie zunächst die Klasse, um Variablen zum Nachverfolgen des `App` Authentifizierungsstatus und des angemeldeten Benutzers hinzuzufügen.

1. Erweitern **Sie im Projektmappen-Explorer** das **Projekt "GraphTutorial",** und erweitern Sie dann die Datei **"App.xaml".** Öffnen Sie **App.xaml.cs** Datei, und fügen Sie die folgenden Anweisungen `using` am Oberen Rand der Datei hinzu.

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. Fügen Sie `INotifyPropertyChanged` die Schnittstelle der Klassendeklaration hinzu.

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. Fügen Sie der Klasse die folgenden Eigenschaften `App` hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. Fügen Sie der Klasse die folgenden Funktionen `App` hinzu. Die `SignIn` , und Funktionen sind derzeit nur `SignOut` `GetUserInfo` Platzhalter.

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

1. Die `GetUserPhoto` Funktion gibt jetzt ein Standardfoto zurück. Sie können entweder Ihre eigene Datei oder die im Beispiel verwendete Datei von [GitHub herunterladen.](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png) Wenn Sie Ihre eigene Datei verwenden, benennen Sie sie **in** no-profile-pic.png.

1. Kopieren Sie die Datei in das **Verzeichnis "./GraphTutorial/GraphTutorial".**

1. Klicken Sie im Projektmappen-Explorer mit der rechten **Maustaste** auf die Datei, und wählen Sie **"Eigenschaften" aus.** Ändern Sie **im Eigenschaftenfenster** den Wert der **Buildaktion** in **eingebettete Ressource.**

    ![Screenshot des Eigenschaftenfensters für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a>Navigation in der App

In diesem Abschnitt ändern Sie die Hauptseite der Anwendung in eine [Master-Detail-Seite.](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page) Dadurch wird ein Navigationsmenü zum Wechseln zwischen der Ansicht in der App angezeigt.

1. Öffnen Sie **die Datei "MainPage.xaml"** im **GraphTutorial-Projekt,** und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a>Implementieren des Menüs

1. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neuer Ordner" aus.** Benennen Sie den Ordner `Models`.

1. Klicken Sie mit der rechten Maustaste auf den Ordner **"Modelle",** und wählen **Sie "Hinzufügen"** und dann **"Klasse... " aus.** Benennen Sie die `NavMenuItem` Klasse, und wählen Sie **"Hinzufügen" aus.**

1. Öffnen Sie **NavMenuItem.cs** Datei, und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `MenuPage` Seite. Klicken Sie auf **Hinzufügen**.

1. Öffnen Sie die **Datei "MenuPage.xaml",** und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. Erweitern **Sie "MenuPage.xaml"** im Projektmappen-Explorer, und öffnen Sie **MenuPage.xaml.cs** Datei.  Ersetzen Sie den Inhalt durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio werden Fehler in der **MenuPage.xaml.cs.** Diese Fehler werden in einem späteren Schritt behoben.

#### <a name="implement-the-welcome-page"></a>Implementieren der Willkommensseite

1. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `WelcomePage` Seite. Klicken Sie auf **Hinzufügen**. Öffnen Sie die **Datei "WelcomePage.xaml",** und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. Erweitern **Sie "WelcomePage.xaml"** im Projektmappen-Explorer, und öffnen Sie **WelcomePage.xaml.cs** Datei.  Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a>Hinzufügen von Kalender- und neuen Ereignisseiten

Fügen Sie nun eine Kalenderseite und eine neue Ereignisseite hinzu. Dies sind vorer denng nur Platzhalter.

1. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `CalendarPage` Seite. Klicken Sie auf **Hinzufügen**.

1. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial-Projekt,** und wählen **Sie "Hinzufügen"** und dann **"Neues Element" aus...**. Wählen **Sie "Inhaltsseite"** aus, und benennen Sie die `NewEventPage` Seite. Klicken Sie auf **Hinzufügen**.

#### <a name="update-mainpage-code-behind"></a>Aktualisieren von MainPage-Code-Behind

Nachdem nun alle Seiten vorhanden sind, aktualisieren Sie den Code-Behind für **MainPage.xaml**.

1. Erweitern **Sie "MainPage.xaml"** im Projektmappen-Explorer, MainPage.xaml.cs öffnen Sie die Datei, und ersetzen Sie den gesamten Inhalt durch Folgendes.  

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. Speichern Sie alle Änderungen. Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, iOS oder UWP), und wählen Sie "Als Startprojekt **festlegen" aus.** Drücken **Sie F5,** oder wählen **Sie "Debuggen" > Debuggen** in Visual Studio.

    ![Screenshots der Android-, iOS- und UWP-Versionen der Anwendung](./images/welcome-page.png)
