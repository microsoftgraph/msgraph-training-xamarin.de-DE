<!-- markdownlint-disable MD002 MD041 -->

Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen**aus. Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Mobile App (Xamarin. Forms)** aus, und wählen Sie dann **weiter**aus.

![Visual Studio 2019 Dialogfeld "Neues Projekt erstellen"](images/new-project-dialog.png)

Geben `GraphTutorial` Sie im Dialogfeld **Neues Projekt konfigurieren** für den **Projektnamen** und den **Lösungsnamen**ein, und wählen Sie dann **Erstellen**aus.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie genau den gleichen Namen für das Visual Studio Projekt eingeben, das in diesen Übungseinheiten angegeben ist. Der Visual Studio Projektname wird Teil des Namespaces im Code. Der Code in diesen Anweisungen hängt vom Namespace ab, der dem in diesen Anweisungen angegebenen Visual Studio Projektnamen entspricht. Wenn Sie einen anderen Projektnamen verwenden, wird der Code nur dann kompiliert, wenn Sie alle Namespaces so anpassen, dass Sie dem Visual Studio Projektnamen entsprechen, den Sie beim Erstellen des Projekts eingeben.

![Visual Studio 2019 Dialogfeld "Neues Projekt konfigurieren"](images/configure-new-project-dialog.png)

Wählen Sie im Dialogfeld **neue plattformübergreifende App** die **leere** Vorlage aus, und wählen Sie die Plattformen aus, die Sie unter **Plattformen**erstellen möchten. Wählen Sie **OK** aus, um die Lösung zu erstellen.

![Visual Studio 2019 neue plattformübergreifende App-Dialogfeld](images/new-cross-platform-app-dialog.png)

Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.

- [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) zum behandeln Azure AD Authentifizierung und Tokenverwaltung.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für das tätigen von Anrufen an Microsoft Graph.

Wählen Sie **Tools #a0 NuGet-Paket-Manager #a1-Paket-Manager-Konsole**aus. Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein.

```Powershell
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.20.0 -Project GraphTutorial
```

## <a name="design-the-app"></a>Entwerfen der APP

Aktualisieren Sie zunächst die `App` Klasse, um Variablen hinzuzufügen, um den Authentifizierungsstatus und den angemeldeten Benutzer nachzuverfolgen. Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und erweitern Sie dann die Datei **app. XAML** . Öffnen Sie die Datei **app.XAML.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

Fügen Sie als nächstes `INotifyPropertyChanged` die Schnittstelle zur Klassendeklaration hinzu.

```cs
public partial class App : Application, INotifyPropertyChanged
```

Fügen Sie der `App` -Klasse nun die folgenden Eigenschaften hinzu.

```cs
// Is a user signed in?
private bool isSignedIn;
public bool IsSignedIn
{
    get { return isSignedIn; }
    set
    {
        isSignedIn = value;
        OnPropertyChanged("IsSignedIn");
        OnPropertyChanged("IsSignedOut");
    }
}

public bool IsSignedOut { get { return !isSignedIn; } }

// The user's display name
private string userName;
public string UserName
{
    get { return userName; }
    set
    {
        userName = value;
        OnPropertyChanged("UserName");
    }
}

// The user's email address
private string userEmail;
public string UserEmail
{
    get { return userEmail; }
    set
    {
        userEmail = value;
        OnPropertyChanged("UserEmail");
    }
}

// The user's profile photo
private ImageSource userPhoto;
public ImageSource UserPhoto
{
    get { return userPhoto; }
    set
    {
        userPhoto = value;
        OnPropertyChanged("UserPhoto");
    }
}
```

Fügen Sie der `App` -Klasse nun die folgenden Funktionen hinzu. Die `SignIn`- `SignOut`,- `GetUserInfo` und-Funktionen sind jetzt nur Platzhalter.

```cs
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

Die `GetUserPhoto` -Funktion gibt jetzt ein Standard Foto zurück. Sie können entweder hier eine eigene Datei angeben oder die im Beispiel verwendete [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)herunterladen. Kopieren Sie die Datei in `./GraphTutorial/GraphTutorial` das Verzeichnis. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt im **Projektmappen-Explorer** , und wählen Sie **Hinzufügen**, dann **Vorhandenes Element...**. Wählen Sie `no-profile-pic.png` die Datei aus, und wählen Sie **Hinzufügen**aus. Klicken Sie nun im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften**. Ändern Sie im Fenster **Eigenschaften** den Wert der **Aktion erstellen** in **eingebettete Ressource**.

![Screenshot des Eigenschaftenfensters für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a>App-Navigation

Ändern Sie als nächstes die Hauptseite der Anwendung in eine [Master-Detail-Seite](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page). Dadurch wird ein Navigationsmenü bereitgestellt, um zwischen der Ansicht in der APP zu wechseln.

Öffnen Sie die Datei "Haupt **. XAML** " im **GraphTutorial** -Projekt, und ersetzen Sie den Inhalt durch Folgendes.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial"
             x:Class="GraphTutorial.MainPage">

    <MasterDetailPage.Master>
        <local:MenuPage/>
    </MasterDetailPage.Master>

    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:WelcomePage/>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>

</MasterDetailPage>
```

#### <a name="implement-the-menu"></a>Implementieren des Menüs

Erstellen Sie zunächst ein Modell zur Darstellung der Menüelemente. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **neuer Ordner**aus. Nennen Sie den `Models`Ordner.

Klicken Sie mit der rechten Maustaste auf den Ordner **Models** , und wählen Sie dann **Hinzufügen**und dann **Klasse...**. Nennen Sie die `NavMenuItem` Klasse, und wählen Sie **Hinzufügen**aus. Öffnen Sie die Datei **NavMenuItem.cs** , und ersetzen Sie den Inhalt durch Folgendes.

```cs
namespace GraphTutorial.Models
{
    public enum MenuItemType
    {
        Welcome,
        Calendar
    }

    public class NavMenuItem
    {
        public MenuItemType Id { get; set; }

        public string Title { get; set; }
    }
}
```

Fügen Sie nun die Menüseite hinzu. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `MenuPage`benennen Sie die Seite. Klicken Sie auf **Hinzufügen**. Öffnen Sie die Datei **MenuPage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.UseSafeArea="true"
             Title="Menu"
             x:Class="GraphTutorial.MenuPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout VerticalOptions="Start" HorizontalOptions="Center">
            <StackLayout x:Name="UserArea" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Sign in to get started"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Medium"
                       Margin="10,20,10,20" />
                <Button Text="Sign in"
                        Clicked="OnSignIn"
                        HorizontalOptions="Center" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Image Source="{Binding Path=UserPhoto, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       Margin="0,20,0,10" />
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Small" />
                <Label Text="{Binding Path=UserEmail, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Italic" />
                <Button Text="Sign out"
                        Margin="0,20,0,20"
                        Clicked="OnSignOut"
                        HorizontalOptions="Center" />
                <ListView x:Name="ListViewMenu"
                          HasUnevenRows="True"
                          HorizontalOptions="Start">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <ViewCell>
                                <Grid Padding="10">
                                    <Label Text="{Binding Title}" FontSize="20"/>
                                </Grid>
                            </ViewCell>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Erweitern Sie nun **MenuPage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **MenuPage.XAML.cs** -Datei. Ersetzen Sie den Inhalt durch Folgendes.

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

using GraphTutorial.Models;

namespace GraphTutorial
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MenuPage : ContentPage
    {
        MainPage RootPage => Application.Current.MainPage as MainPage;
        List<NavMenuItem> menuItems;

        public MenuPage ()
        {
            InitializeComponent ();

            // Add items to the menu
            menuItems = new List<NavMenuItem>
            {
                new NavMenuItem {Id = MenuItemType.Welcome, Title="Home" },
                new NavMenuItem {Id = MenuItemType.Calendar, Title="Calendar" }
            };
            ListViewMenu.ItemsSource = menuItems;

            // Initialize the selected item
            ListViewMenu.SelectedItem = menuItems[0];

            // Handle the ItemSelected event to navigate to the
            // selected page
            ListViewMenu.ItemSelected += async (sender, e) =>
            {
                if (e.SelectedItem == null)
                    return;

                var id = (int)((NavMenuItem)e.SelectedItem).Id;
                await RootPage.NavigateFromMenu(id);
            };
        }

        private async void OnSignOut(object sender, EventArgs e)
        {
            var signout = await DisplayAlert("Sign out?", "Do you want to sign out?", "Yes", "No");
            if (signout)
            {
                await (Application.Current as App).SignOut();
            }
        }

        private async void OnSignIn(object sender, EventArgs e)
        {
            await (Application.Current as App).SignIn();
        }
    }
}
```

> [!NOTE]
> In **MenuPage.XAML.cs**werden von Visual Studio Fehler gemeldet. Diese Fehler werden in einem späteren Schritt behoben.

#### <a name="implement-the-welcome-page"></a>Implementieren der Willkommensseite

Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `WelcomePage`benennen Sie die Seite. Klicken Sie auf **Hinzufügen**. Öffnen Sie die Datei **WelcomePage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Home"
             x:Class="GraphTutorial.WelcomePage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Graph Xamarin Tutorial App"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Large"
                       Margin="10,20,10,20" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Please sign in to get started"
                       HorizontalOptions="Center"
                       FontSize="Medium"
                       Margin="10,0,10,20"/>
                <Button Text="Sign in"
                        HorizontalOptions="Center"
                        Clicked="OnSignIn" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}, StringFormat='Welcome \{0\}!'}"
                       HorizontalOptions="Center"
                       FontSize="Medium"/>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Erweitern Sie nun **WelcomePage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **WelcomePage.XAML.cs** -Datei. Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a>Seite "Kalender hinzufügen"

Fügen Sie nun eine Kalender Seite hinzu. Dies ist nur ein Platzhalter für jetzt. Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `CalendarPage`benennen Sie die Seite. Klicken Sie auf **Hinzufügen**.

Lassen Sie die hinzugefügte Seite unverändert.

#### <a name="update-mainpage-code-behind"></a>Aktualisieren der Hauptseite Code-Behind

Nachdem alle Seiten vorhanden sind, aktualisieren Sie den Code-Behind für Hauptseite **. XAML**. Erweitern Sie Haupt **. XAML** im **Projektmappen-Explorer** , und öffnen Sie die Datei **MainPage.XAML.cs** , und ersetzen Sie den gesamten Inhalt durch Folgendes.

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;
using GraphTutorial.Models;

namespace GraphTutorial
{
    public partial class MainPage : MasterDetailPage
    {
        Dictionary<int, NavigationPage> MenuPages = new Dictionary<int, NavigationPage>();

        public MainPage()
        {
            InitializeComponent();

            MasterBehavior = MasterBehavior.Popover;

            // Load the welcome page at start
            MenuPages.Add((int)MenuItemType.Welcome, (NavigationPage)Detail);
        }

        // Navigate to the selected page
        public async Task NavigateFromMenu(int id)
        {
            if (!MenuPages.ContainsKey(id))
            {
                switch (id)
                {
                    case (int)MenuItemType.Welcome:
                        MenuPages.Add(id, new NavigationPage(new WelcomePage()));
                        break;
                    case (int)MenuItemType.Calendar:
                        MenuPages.Add(id, new NavigationPage(new CalendarPage()));
                        break;
                }
            }

            var newPage = MenuPages[id];

            if (newPage != null && Detail != newPage)
            {
                Detail = newPage;

                if (Device.RuntimePlatform == Device.Android)
                    await Task.Delay(100);

                IsPresented = false;
            }
        }
    }
}
```

Speichern Sie alle Änderungen. Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, IOS oder UWP), und wählen Sie **als Startprojekt festlegen**aus. Drücken Sie **F5** , oder wählen Sie **Debug #a0 starten des Debuggings** in Visual Studio.

![Screenshots der Android-, IOS-und UWP-Versionen der Anwendung](./images/welcome-page.png)
