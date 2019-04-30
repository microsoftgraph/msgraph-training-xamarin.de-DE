<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3ad8e-101">Öffnen Sie Visual Studio, und wählen Sie **Datei _GT_ neues >-Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="3ad8e-102">Führen Sie im Dialogfeld **Neues Projekt** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="3ad8e-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="3ad8e-103">Wählen Sie **Visual C#-_GT_ plattformübergreifend**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-103">Select **Visual C# > Cross-Platform**.</span></span>
1. <span data-ttu-id="3ad8e-104">Wählen Sie **Mobile App (Xamarin. Forms)** aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-104">Select **Mobile App (Xamarin.Forms)**.</span></span>
1. <span data-ttu-id="3ad8e-105">Geben Sie **GraphTutorial** für den Namen des Projekts ein.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-105">Enter **GraphTutorial** for the Name of the project.</span></span>

![Dialogfeld zum Erstellen eines neuen Projekts in Visual Studio 2017](images/new-project-dialog.png)

> [!IMPORTANT]
> <span data-ttu-id="3ad8e-107">Stellen Sie sicher, dass Sie genau denselben Namen für das Visual Studio-Projekt eingeben, das in diesen Lab-Anweisungen angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="3ad8e-108">Der Visual Studio-Projektname wird Teil des Namespaces im Code.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="3ad8e-109">Der Code in diesen Anweisungen hängt vom Namespace ab, der dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen entspricht.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="3ad8e-110">Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie mit dem Visual Studio-Projektnamen übereinstimmen, den Sie beim Erstellen des Projekts eingeben.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

<span data-ttu-id="3ad8e-111">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-111">Select **OK**.</span></span> <span data-ttu-id="3ad8e-112">Wählen Sie im Dialogfeld **neue plattformübergreifende App** die **leere** Vorlage aus, und stellen Sie sicher, dass die Auswahl Strategie für die **Code Freigabe** **.NET Standard**ist.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-112">In the **New Cross Platform App** dialog, select the **Blank** template, and ensure that the **Code Sharing Strategy** selection is **.NET Standard**.</span></span> <span data-ttu-id="3ad8e-113">Wenn Sie eine bestimmte Plattform überspringen möchten, können Sie Sie jetzt unter **Plattformen**aufheben.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-113">If you plan to skip a specific platform, you can unselect it now under **Platforms**.</span></span> <span data-ttu-id="3ad8e-114">Wählen Sie **OK** aus, um die Lösung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-114">Select **OK** to create the solution.</span></span>

![Visual Studio 2017 neues plattformübergreifendes App-Dialogfeld](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="3ad8e-116">Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-116">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="3ad8e-117">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) für die Verarbeitung von Azure AD-Authentifizierung und Tokenverwaltung.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-117">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="3ad8e-118">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für Aufrufe von Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-118">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="3ad8e-119">Wählen Sie **Extras _GT_ NuGet Paket-Manager _GT_ Paket-Manager-Konsole**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-119">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="3ad8e-120">Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-120">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial
Install-Package Xamarin.Android.Support.Compat -Version 27.0.2.1 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.12.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="3ad8e-121">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="3ad8e-121">Design the app</span></span>

<span data-ttu-id="3ad8e-122">Aktualisieren Sie zunächst die `App` Klasse, um Variablen hinzuzufügen, um den Authentifizierungsstatus und den angemeldeten Benutzer nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-122">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="3ad8e-123">Erweitern Sie im projektMappen- **Explorer**das **GraphTutorial** -Projekt, und erweitern Sie dann die Datei **app. XAML** .</span><span class="sxs-lookup"><span data-stu-id="3ad8e-123">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="3ad8e-124">Öffnen Sie die **app.XAML.cs** -Datei, und `using` fügen Sie am Anfang der Datei die folgenden Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-124">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="3ad8e-125">Als Nächstes fügen Sie `INotifyPropertyChanged` die Schnittstelle zur Klassendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-125">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="3ad8e-126">Fügen Sie nun die folgenden Eigenschaften zur `App` Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-126">Now add the following properties to the `App` class.</span></span>

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

<span data-ttu-id="3ad8e-127">Fügen Sie nun die folgenden Funktionen zur `App` Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-127">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="3ad8e-128">Die `SignIn`, `SignOut`, und `GetUserInfo` die Funktionen sind nur Platzhalter für jetzt.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-128">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

<span data-ttu-id="3ad8e-129">Die `GetUserPhoto` Funktion gibt ein Standard Foto für jetzt zurück.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-129">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="3ad8e-130">Sie können entweder Ihre eigene Datei hier angeben, oder Sie können die im Beispiel verwendete Seite von [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)herunterladen.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-130">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="3ad8e-131">Kopieren Sie die Datei in `./GraphTutorial/GraphTutorial` das Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-131">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="3ad8e-132">Klicken Sie im projektMappen- **Explorer** mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Vorhandenes Element.**... Wählen Sie `no-profile-pic.png` die Datei aus, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-132">Right-click the **GraphTutorial** project in **Solution Explorer** and choose **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and choose **Add**.</span></span> <span data-ttu-id="3ad8e-133">Klicken Sie im Projektmappen- **Explorer** mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-133">Now right-click the file in **Solution Explorer** and choose **Properties**.</span></span> <span data-ttu-id="3ad8e-134">Ändern Sie im Fenster **Eigenschaften** den Wert von **Buildvorgang** in **eingebettete Ressource**.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-134">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![Screenshot des Fensters "Eigenschaften" für die PNG-Datei](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="3ad8e-136">App-Navigation</span><span class="sxs-lookup"><span data-stu-id="3ad8e-136">App navigation</span></span>

<span data-ttu-id="3ad8e-137">Ändern Sie als nächstes die Hauptseite der Anwendung in eine [Master-Detail-Seite](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="3ad8e-137">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="3ad8e-138">Dadurch wird ein Navigationsmenü zum Wechseln zwischen Ansicht in der APP bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-138">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="3ad8e-139">Öffnen Sie die Datei " **Page. XAML** " im **GraphTutorial** -Projekt, und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-139">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

#### <a name="implement-the-menu"></a><span data-ttu-id="3ad8e-140">Implementieren des Menüs</span><span class="sxs-lookup"><span data-stu-id="3ad8e-140">Implement the menu</span></span>

<span data-ttu-id="3ad8e-141">Erstellen Sie zunächst ein Modell zur Darstellung der Menüelemente.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-141">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="3ad8e-142">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **neuer Ordner**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-142">Right-click the **GraphTutorial** project and choose **Add**, then **New Folder**.</span></span> <span data-ttu-id="3ad8e-143">Benennen Sie den `Models`Ordner.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-143">Name the folder `Models`.</span></span>

<span data-ttu-id="3ad8e-144">Klicken Sie mit der rechten Maustaste auf den Ordner **Modelle** , und wählen Sie **Hinzufügen**und dann **Klasse...**. Benennen Sie die `NavMenuItem` Klasse, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-144">Right-click the **Models** folder and choose **Add**, then **Class...**. Name the class `NavMenuItem` and choose **Add**.</span></span> <span data-ttu-id="3ad8e-145">Öffnen Sie die **NavMenuItem.cs** -Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-145">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="3ad8e-146">Fügen Sie nun die Menüseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-146">Now add the menu page.</span></span> <span data-ttu-id="3ad8e-147">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.**... Wählen Sie **Inhaltsseite** aus, und `MenuPage`nennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-147">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="3ad8e-148">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-148">Choose **Add**.</span></span> <span data-ttu-id="3ad8e-149">Öffnen Sie die Datei **MenuPage. XAML** , und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-149">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="3ad8e-150">Erweitern Sie jetzt **MenuPage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **MenuPage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-150">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="3ad8e-151">Ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-151">Replace its contents with the following.</span></span>

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
        MainPage RootPage { get => Application.Current.MainPage as MainPage; }
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

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="3ad8e-152">Implementieren der Willkommensseite</span><span class="sxs-lookup"><span data-stu-id="3ad8e-152">Implement the welcome page</span></span>

<span data-ttu-id="3ad8e-153">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.**... Wählen Sie **Inhaltsseite** aus, und `WelcomePage`nennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-153">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="3ad8e-154">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-154">Choose **Add**.</span></span> <span data-ttu-id="3ad8e-155">Öffnen Sie die Datei **WelcomePage. XAML** , und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-155">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="3ad8e-156">Erweitern Sie jetzt **WelcomePage. XAML** im **Projektmappen-Explorer** , und öffnen Sie die **WelcomePage.XAML.cs** -Datei.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-156">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="3ad8e-157">Fügen Sie die folgende Funktion zur `WelcomePage`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="3ad8e-157">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="3ad8e-158">Kalender Seite hinzufügen</span><span class="sxs-lookup"><span data-stu-id="3ad8e-158">Add calendar page</span></span>

<span data-ttu-id="3ad8e-159">Fügen Sie nun eine Kalender Seite hinzu.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-159">Now add a calendar page.</span></span> <span data-ttu-id="3ad8e-160">Dies ist nur ein Platzhalter für jetzt.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-160">This will just be a placeholder for now.</span></span> <span data-ttu-id="3ad8e-161">Klicken Sie mit der rechten Maustaste auf das **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Neues Element.**... Wählen Sie **Inhaltsseite** aus, und `CalendarPage`nennen Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-161">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="3ad8e-162">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-162">Choose **Add**.</span></span>

<span data-ttu-id="3ad8e-163">Lassen Sie die hinzugefügte Seite unverändert.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-163">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="3ad8e-164">Update der Hauptseite Code-Behind</span><span class="sxs-lookup"><span data-stu-id="3ad8e-164">Update MainPage code-behind</span></span>

<span data-ttu-id="3ad8e-165">Nachdem alle Seiten vorhanden sind, aktualisieren Sie den CodeBehind für "Hauptseite **. XAML**".</span><span class="sxs-lookup"><span data-stu-id="3ad8e-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="3ad8e-166">Erweitern \*\*\*\* Sie im **Projektmappen-Explorer** die **MainPage.XAML.cs** -Datei, und ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

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

<span data-ttu-id="3ad8e-167">Speichern Sie alle Änderungen.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-167">Save all of your changes.</span></span> <span data-ttu-id="3ad8e-168">Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie ausführen möchten (Android, iOS oder UWP), und wählen Sie **als Startprojekt festlegen**aus.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-168">Right-click the project that you want to run (Android, iOS, or UWP) and choose **Set as StartUp Project**.</span></span> <span data-ttu-id="3ad8e-169">Drücken Sie **F5** , oder wählen Sie **Debug > Start Debugging** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3ad8e-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![Screenshots der Android-, iOS-und UWP-Versionen der Anwendung](./images/welcome-page.png)