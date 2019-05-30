<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d236a-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="d236a-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d236a-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="d236a-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d236a-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="d236a-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="d236a-104">Aktualisieren Sie zunächst die Datei **CalendarPage. XAML** im **GraphTutorial** -Projekt.</span><span class="sxs-lookup"><span data-stu-id="d236a-104">Start by updating the **CalendarPage.xaml** file in the **GraphTutorial** project.</span></span> <span data-ttu-id="d236a-105">Öffnen Sie die Datei, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d236a-105">Open the file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Content>
        <StackLayout>
            <Editor x:Name="JSONResponse"
                    IsSpellCheckEnabled="False"
                    HorizontalOptions="FillAndExpand"
                    VerticalOptions="FillAndExpand"/>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="d236a-106">Öffnen `CalendarPage.xaml.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d236a-106">Open `CalendarPage.xaml.cs` and add the following `using` statements at the top of the file.</span></span>

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

<span data-ttu-id="d236a-107">Fügen Sie dann den folgenden Code hinzu, um die Ereignisse des Benutzers abzurufen und die JSON-Antwort anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d236a-107">Then add the following code to get the user's events and display the JSON response.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Get the events
    var events = await App.GraphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
}
```

<span data-ttu-id="d236a-108">Überprüfen Sie, was `OnAppearing` der Code in tut.</span><span class="sxs-lookup"><span data-stu-id="d236a-108">Consider what the code in `OnAppearing` is doing.</span></span>

- <span data-ttu-id="d236a-109">Die URL, die aufgerufen wird `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="d236a-109">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="d236a-110">Die `Select` -Funktion schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d236a-110">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="d236a-111">Die `OrderBy` -Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d236a-111">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="d236a-112">Sie können nun die app ausführen, sich anmelden und im Menü auf das Navigationselement **Kalender** klicken.</span><span class="sxs-lookup"><span data-stu-id="d236a-112">You can now run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="d236a-113">Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d236a-113">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d236a-114">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="d236a-114">Display the results</span></span>

<span data-ttu-id="d236a-115">Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d236a-115">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="d236a-116">Erstellen Sie zunächst einen [Binding-Wertkonverter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) , um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Werte in die vom Benutzer erwarteten Datums-und Uhrzeitformate zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="d236a-116">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span> <span data-ttu-id="d236a-117">Klicken Sie mit der rechten Maustaste auf den Ordner **Models** im **GraphTutorial** -Projekt, und wählen Sie **Hinzufügen**und dann **Klasse...**. Nennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d236a-117">Right-click the **Models** folder in the **GraphTutorial** project and choose **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and choose **Add**.</span></span> <span data-ttu-id="d236a-118">Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d236a-118">Replace the entire contents of the file with the following.</span></span>

```cs
using Microsoft.Graph;
using System;
using System.Globalization;
using Xamarin.Forms;

namespace GraphTutorial.Models
{
    class GraphDateTimeTimeZoneConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value is DateTimeTimeZone date)
            {
                // Resolve the time zone
                var timezone = TimeZoneInfo.FindSystemTimeZoneById(date.TimeZone);
                // Parse method assumes local time, which may not be the case
                var parsedDateAsLocal = DateTimeOffset.Parse(date.DateTime);
                // Determine the offset from UTC time for the specific date
                // Making this call adjusts for DST as appropriate
                var tzOffset = timezone.GetUtcOffset(parsedDateAsLocal.DateTime);
                // Create a new DateTimeOffset with the specific offset from UTC
                var correctedDate = new DateTimeOffset(parsedDateAsLocal.DateTime, tzOffset);
                // Return the local date time string
                return $"{correctedDate.LocalDateTime.ToShortDateString()} {correctedDate.LocalDateTime.ToShortTimeString()}";
            }

            return string.Empty;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
}
```

<span data-ttu-id="d236a-119">Ersetzen Sie als nächstes den gesamten Inhalt `CalendarPage.xaml` durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d236a-119">Next, replace the entire contents of `CalendarPage.xaml` with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial.Models"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Resources>
        <local:GraphDateTimeTimeZoneConverter x:Key="DateConverter" />
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout>
            <ListView x:Name="CalendarList"
                      HasUnevenRows="true"
                      Margin="10,10,10,10">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Margin="10,10,10,10">
                                <Label Text="{Binding Path=Subject}"
                                       FontAttributes="Bold"
                                       FontSize="Medium" />
                                <Label Text="{Binding Path=Organizer.EmailAddress.Name}"
                                       FontSize="Small" />
                                <StackLayout Orientation="Horizontal">
                                    <Label Text="{Binding Path=Start, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                    <Label Text="to"
                                           FontSize="Micro" />
                                    <Label Text="{Binding Path=End, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                </StackLayout>
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="d236a-120">Dadurch wird die `Editor` durch a `ListView`ersetzt.</span><span class="sxs-lookup"><span data-stu-id="d236a-120">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="d236a-121">Der `DataTemplate` zum Rendern der einzelnen Elemente verwendete verwendet `GraphDateTimeTimeZoneConverter` , um die `Start` - `End` und-Eigenschaften des Ereignisses in eine Zeichenfolge zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="d236a-121">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span> <span data-ttu-id="d236a-122">Öffnen `CalendarPage.xaml.cs` Sie nun die folgenden Zeilen aus der `OnAppearing` -Funktion, und entfernen Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="d236a-122">Now open `CalendarPage.xaml.cs` and remove the following lines from the `OnAppearing` function.</span></span>

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

<span data-ttu-id="d236a-123">Fügen Sie an ihrer Stelle den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d236a-123">In their place, add the following code.</span></span>

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

<span data-ttu-id="d236a-124">Führen Sie die APP aus, melden Sie sich an, und klicken Sie auf das Navigationselement **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="d236a-124">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="d236a-125">Die Liste der Ereignisse sollte angezeigt werden, wobei die Werte **Start** und **End** formatiert sind.</span><span class="sxs-lookup"><span data-stu-id="d236a-125">You should see the list of events with the **Start** and **End** values formatted.</span></span>

![Ein Screenshot der Ereignistabelle](./images/calendar-page.png)
