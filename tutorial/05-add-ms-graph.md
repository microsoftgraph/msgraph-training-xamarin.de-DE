<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f0e1a-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="f0e1a-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .NET,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="f0e1a-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="f0e1a-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="f0e1a-104">Öffnen **Sie "CalendarPage.xaml"** im **GraphTutorial-Projekt,** und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

    ```xaml
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

1. <span data-ttu-id="f0e1a-105">Öffnen **CalendarPage.xaml.cs,** und fügen Sie die folgenden `using` Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="f0e1a-106">Fügen Sie der Klasse die folgende Funktion hinzu, um die Ereignisse des Benutzers zu erhalten und `CalendarPage` die JSON-Antwort anzeigen.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-106">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get start and end of week in user's time zone
        var startOfWeek = GetUtcStartOfWeekInTimeZone(DateTime.Today, App.UserTimeZone);
        var endOfWeek = startOfWeek.AddDays(7);

        var queryOptions = new List<QueryOption>
        {
            new QueryOption("startDateTime", startOfWeek.ToString("o")),
            new QueryOption("endDateTime", endOfWeek.ToString("o"))
        };

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{App.UserTimeZone.DisplayName}\"")
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("start/DateTime")
            .Top(50)
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="f0e1a-107">Überlegen Sie, was der Code `OnAppearing` gerade macht.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-107">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="f0e1a-108">Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="f0e1a-109">Die `startDateTime` Parameter definieren den Anfang und das Ende der `endDateTime` Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-109">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
        - <span data-ttu-id="f0e1a-110">Der Header bewirkt, dass das Ereignis und die Ereignisse in der Zeitzone des `Prefer: outlook.timezone` `start` Benutzers zurückgegeben `end` werden.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-110">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
        - <span data-ttu-id="f0e1a-111">Die Funktion beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `Select` tatsächlich von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-111">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
        - <span data-ttu-id="f0e1a-112">Die `OrderBy` Funktion sortiert die Ergebnisse nach Startdatum und -uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-112">The `OrderBy` function sorts the results by the start date and time.</span></span>
        - <span data-ttu-id="f0e1a-113">Die `Top` Funktion fordert mindestens 50 Ereignisse an.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-113">The `Top` function requests at most 50 events.</span></span>

1. <span data-ttu-id="f0e1a-114">Führen Sie die App aus, melden Sie sich an, und klicken **Sie** im Menü auf das Navigationselement "Kalender".</span><span class="sxs-lookup"><span data-stu-id="f0e1a-114">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="f0e1a-115">Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-115">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="f0e1a-116">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="f0e1a-116">Display the results</span></span>

<span data-ttu-id="f0e1a-117">Jetzt können Sie das JSON-Dump durch eine benutzerfreundliche Anzeige der Ergebnisse ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-117">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="f0e1a-118">Erstellen Sie [](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) zunächst einen Bindungswertkonverter, um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone-Werte](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) in die vom Benutzer erwarteten Datums- und Uhrzeitformate zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-118">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="f0e1a-119">Klicken Sie im Projekt **"GraphTutorial"** mit der rechten Maustaste auf den Ordner "Modelle", und wählen Sie **"Hinzufügen"** und dann **"Klasse...**" aus.  Benennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="f0e1a-119">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="f0e1a-120">Ersetzen Sie den gesamten Inhalt der Datei durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-120">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="f0e1a-121">Ersetzen Sie den gesamten Inhalt von **CalendarPage.xaml** durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-121">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    <span data-ttu-id="f0e1a-122">Dadurch wird die durch `Editor` eine `ListView` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-122">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="f0e1a-123">The `DataTemplate` used to render each item uses the to convert the and properties of the event to `GraphDateTimeTimeZoneConverter` a `Start` `End` string.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-123">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="f0e1a-124">Öffnen **CalendarPage.xaml.cs,** und entfernen Sie die folgenden Zeilen aus der `OnAppearing` Funktion.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-124">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="f0e1a-125">Fügen Sie an ihrer Stelle den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-125">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="f0e1a-126">Führen Sie die App aus, melden Sie sich an, und klicken Sie auf das **Navigationselement** "Kalender".</span><span class="sxs-lookup"><span data-stu-id="f0e1a-126">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="f0e1a-127">Die Liste der Ereignisse mit den **Start-** und **Endwerten** sollte formatiert sein.</span><span class="sxs-lookup"><span data-stu-id="f0e1a-127">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-page.png)
