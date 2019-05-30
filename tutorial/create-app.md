<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="15348-101">Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-101">Open Visual Studio, and select **Create a new project**.</span></span> <span data-ttu-id="15348-102">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Mobile App (Xamarin. Forms)** aus, und klicken Sie dann auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="15348-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then choose **Next**.</span></span>

![Visual Studio 2019 Dialogfeld "Neues Projekt erstellen"](images/new-project-dialog.png)

<span data-ttu-id="15348-104">Geben `GraphTutorial` Sie im Dialogfeld **Neues Projekt konfigurieren** für den **Projektnamen** und den **Lösungsnamen**ein, und wählen Sie dann **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then choose **Create**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="15348-105">Stellen Sie sicher, dass Sie genau den gleichen Namen für das Visual Studio Projekt eingeben, das in diesen Übungseinheiten angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="15348-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="15348-106">Der Visual Studio Projektname wird Teil des Namespaces im Code.</span><span class="sxs-lookup"><span data-stu-id="15348-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="15348-107">Der Code in diesen Anweisungen hängt vom Namespace ab, der dem in diesen Anweisungen angegebenen Visual Studio Projektnamen entspricht.</span><span class="sxs-lookup"><span data-stu-id="15348-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="15348-108">Wenn Sie einen anderen Projektnamen verwenden, wird der Code nur dann kompiliert, wenn Sie alle Namespaces so anpassen, dass Sie dem Visual Studio Projektnamen entsprechen, den Sie beim Erstellen des Projekts eingeben.</span><span class="sxs-lookup"><span data-stu-id="15348-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

![Visual Studio 2019 Dialogfeld "Neues Projekt konfigurieren"](images/configure-new-project-dialog.png)

<span data-ttu-id="15348-110">Wählen Sie im Dialogfeld **neue plattformübergreifende App** die **leere** Vorlage aus, und wählen Sie die Plattformen aus, die Sie unter **Plattformen**erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="15348-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="15348-111">Wählen Sie **OK** aus, um die Lösung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15348-111">Select **OK** to create the solution.</span></span>

![Visual Studio 2019 neue plattformübergreifende App-Dialogfeld](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="15348-113">Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="15348-113">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="15348-114">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) zum behandeln Azure AD Authentifizierung und Tokenverwaltung.</span><span class="sxs-lookup"><span data-stu-id="15348-114">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="15348-115">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für das tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="15348-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="15348-116">Wählen Sie **Extras > NuGet Paket-Manager->-Paket-Manager-Konsole**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-116">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="15348-117">Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein.</span><span class="sxs-lookup"><span data-stu-id="15348-117">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.15.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="15348-118">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="15348-118">Design the app</span></span>

<span data-ttu-id="15348-119">Aktualisieren Sie zunächst die `App` Klasse, um Variablen hinzuzufügen, um den Authentifizierungsstatus und den angemeldeten Benutzer nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="15348-119">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="15348-120">Erweitern Sie im **Projektmappen-Explorer**das **GraphTutorial** -Projekt, und erweitern Sie dann die Datei **app. XAML** .</span><span class="sxs-lookup"><span data-stu-id="15348-120">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="15348-121">Öffnen Sie die Datei **app.XAML.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-121">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="15348-122">Fügen Sie als nächstes `INotifyPropertyChanged` die Schnittstelle zur Klassendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-122">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="15348-123">Fügen Sie der `App` -Klasse nun die folgenden Eigenschaften hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-123">Now add the following properties to the `App` class.</span></span>

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

<span data-ttu-id="15348-124">Fügen Sie der `App` -Klasse nun die folgenden Funktionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-124">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="15348-125">Die `SignIn`- `SignOut`,- `GetUserInfo` und-Funktionen sind jetzt nur Platzhalter.</span><span class="sxs-lookup"><span data-stu-id="15348-125">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

<span data-ttu-id="15348-126">Die `GetUserPhoto` -Funktion gibt jetzt ein Standard Foto zurück.</span><span class="sxs-lookup"><span data-stu-id="15348-126">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="15348-127">Sie können entweder hier eine eigene Datei angeben oder die im Beispiel verwendete [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)herunterladen.</span><span class="sxs-lookup"><span data-stu-id="15348-127">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="15348-128">Kopieren Sie die Datei in `./GraphTutorial/GraphTutorial` das Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="15348-128">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="15348-129">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt im **Projektmappen-Explorer** , und wählen Sie **Hinzufügen**, dann **Vorhandenes Element...**. Wählen Sie `no-profile-pic.png` die Datei aus, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="15348-129">Right-click the **GraphTutorial** project in **Solution Explorer** and choose **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and choose **Add**.</span></span> <span data-ttu-id="15348-130">Klicken Sie nun im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="15348-130">Now right-click the file in **Solution Explorer** and choose **Properties**.</span></span> <span data-ttu-id="15348-131">Ändern Sie im Fenster **Eigenschaften** den Wert der **Aktion erstellen** in **eingebettete Ressource**.</span><span class="sxs-lookup"><span data-stu-id="15348-131">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![Screenshot des Eigenschaftenfensters für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="15348-133">App-Navigation</span><span class="sxs-lookup"><span data-stu-id="15348-133">App navigation</span></span>

<span data-ttu-id="15348-134">Ändern Sie als nächstes die Hauptseite der Anwendung in eine [Master-Detail-Seite](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="15348-134">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="15348-135">Dadurch wird ein Navigationsmenü bereitgestellt, um zwischen der Ansicht in der APP zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="15348-135">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="15348-136">Öffnen Sie die Datei "Haupt **. XAML** " im **GraphTutorial** -Projekt, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-136">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

#### <a name="implement-the-menu"></a><span data-ttu-id="15348-137">Implementieren des Menüs</span><span class="sxs-lookup"><span data-stu-id="15348-137">Implement the menu</span></span>

<span data-ttu-id="15348-138">Erstellen Sie zunächst ein Modell zur Darstellung der Menüelemente.</span><span class="sxs-lookup"><span data-stu-id="15348-138">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="15348-139">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **neuer Ordner**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-139">Right-click the **GraphTutorial** project and choose **Add**, then **New Folder**.</span></span> <span data-ttu-id="15348-140">Nennen Sie den `Models`Ordner.</span><span class="sxs-lookup"><span data-stu-id="15348-140">Name the folder `Models`.</span></span>

<span data-ttu-id="15348-141">Klicken Sie mit der rechten Maustaste auf den Ordner **Models** , und wählen Sie **Hinzufügen**und dann **Klasse...**. Nennen Sie die `NavMenuItem` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-141">Right-click the **Models** folder and choose **Add**, then **Class...**. Name the class `NavMenuItem` and choose **Add**.</span></span> <span data-ttu-id="15348-142">Öffnen Sie die Datei **NavMenuItem.cs** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="15348-143">Fügen Sie nun die Menüseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-143">Now add the menu page.</span></span> <span data-ttu-id="15348-144">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `MenuPage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="15348-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="15348-145">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="15348-145">Choose **Add**.</span></span> <span data-ttu-id="15348-146">Öffnen Sie die Datei **MenuPage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="15348-147">Erweitern Sie nun **MenuPage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **MenuPage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="15348-147">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="15348-148">Ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-148">Replace its contents with the following.</span></span>

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
> <span data-ttu-id="15348-149">In **MenuPage.XAML.cs**werden von Visual Studio Fehler gemeldet.</span><span class="sxs-lookup"><span data-stu-id="15348-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="15348-150">Diese Fehler werden in einem späteren Schritt behoben.</span><span class="sxs-lookup"><span data-stu-id="15348-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="15348-151">Implementieren der Willkommensseite</span><span class="sxs-lookup"><span data-stu-id="15348-151">Implement the welcome page</span></span>

<span data-ttu-id="15348-152">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `WelcomePage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="15348-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="15348-153">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="15348-153">Choose **Add**.</span></span> <span data-ttu-id="15348-154">Öffnen Sie die Datei **WelcomePage. XAML** , und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="15348-155">Erweitern Sie nun **WelcomePage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **WelcomePage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="15348-155">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="15348-156">Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="15348-156">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="15348-157">Seite "Kalender hinzufügen"</span><span class="sxs-lookup"><span data-stu-id="15348-157">Add calendar page</span></span>

<span data-ttu-id="15348-158">Fügen Sie nun eine Kalender Seite hinzu.</span><span class="sxs-lookup"><span data-stu-id="15348-158">Now add a calendar page.</span></span> <span data-ttu-id="15348-159">Dies ist nur ein Platzhalter für jetzt.</span><span class="sxs-lookup"><span data-stu-id="15348-159">This will just be a placeholder for now.</span></span> <span data-ttu-id="15348-160">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.** Wählen Sie **Inhaltsseite** aus, und `CalendarPage`benennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="15348-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="15348-161">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="15348-161">Choose **Add**.</span></span>

<span data-ttu-id="15348-162">Lassen Sie die hinzugefügte Seite unverändert.</span><span class="sxs-lookup"><span data-stu-id="15348-162">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="15348-163">Aktualisieren der Hauptseite Code-Behind</span><span class="sxs-lookup"><span data-stu-id="15348-163">Update MainPage code-behind</span></span>

<span data-ttu-id="15348-164">Nachdem alle Seiten vorhanden sind, aktualisieren Sie den Code-Behind für Hauptseite **. XAML**.</span><span class="sxs-lookup"><span data-stu-id="15348-164">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="15348-165">Erweitern Sie Haupt **. XAML** im **Projektmappen-Explorer** , und öffnen Sie die Datei **MainPage.XAML.cs** , und ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="15348-165">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

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

<span data-ttu-id="15348-166">Speichern Sie alle Änderungen.</span><span class="sxs-lookup"><span data-stu-id="15348-166">Save all of your changes.</span></span> <span data-ttu-id="15348-167">Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, IOS oder UWP), und wählen Sie **als Startprojekt festlegen**aus.</span><span class="sxs-lookup"><span data-stu-id="15348-167">Right-click the project that you want to run (Android, iOS, or UWP) and choose **Set as StartUp Project**.</span></span> <span data-ttu-id="15348-168">Drücken Sie **F5** , oder wählen Sie **Debug > Start Debugging** in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="15348-168">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![Screenshots der Android-, IOS-und UWP-Versionen der Anwendung](./images/welcome-page.png)