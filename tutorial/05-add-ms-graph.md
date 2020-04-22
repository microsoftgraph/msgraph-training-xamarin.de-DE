<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="085e8-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="085e8-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="085e8-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="085e8-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="085e8-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="085e8-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="085e8-104">Öffnen Sie **CalendarPage. XAML** im **GraphTutorial** -Projekt, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="085e8-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="085e8-105">Öffnen Sie **CalendarPage.XAML.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="085e8-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="085e8-106">Fügen Sie die folgende Funktion zur `CalendarPage` -Klasse hinzu, um die Ereignisse des Benutzers abzurufen und die JSON-Antwort anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="085e8-106">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get the events
        var events = await App.GraphClient.Me.Events.Request()
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="085e8-107">Überprüfen Sie, was `OnAppearing` der Code in tut.</span><span class="sxs-lookup"><span data-stu-id="085e8-107">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="085e8-108">Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="085e8-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="085e8-109">Die `Select`-Funktion beschränkt die Felder, die für jedes Ereignis zurückgegeben werden, auf nur diejenigen, die von der Ansicht tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="085e8-109">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="085e8-110">Die `OrderBy`-Funktion sortiert die Ergebnisse nach Datum und Uhrzeit ihrer Erstellung, wobei das aktuellste Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="085e8-110">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="085e8-111">Führen Sie die APP aus, melden Sie sich an, und klicken Sie im Menü auf das Navigationselement **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="085e8-111">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="085e8-112">Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="085e8-112">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="085e8-113">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="085e8-113">Display the results</span></span>

<span data-ttu-id="085e8-114">Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="085e8-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="085e8-115">Erstellen Sie zunächst einen [Binding-Wertkonverter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) , um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Werte in die vom Benutzer erwarteten Datums-und Uhrzeitformate zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="085e8-115">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="085e8-116">Klicken Sie im **GraphTutorial** -Projekt mit der rechten Maustaste auf den Ordner **Models** , und wählen Sie dann **Hinzufügen**und dann **Klasse...**. Nennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="085e8-116">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="085e8-117">Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="085e8-117">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="085e8-118">Ersetzen Sie den gesamten Inhalt von **CalendarPage. XAML** durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="085e8-118">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    <span data-ttu-id="085e8-119">Dadurch wird die `Editor` durch a `ListView`ersetzt.</span><span class="sxs-lookup"><span data-stu-id="085e8-119">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="085e8-120">Der `DataTemplate` zum Rendern der einzelnen Elemente verwendete verwendet `GraphDateTimeTimeZoneConverter` , um die `Start` - `End` und-Eigenschaften des Ereignisses in eine Zeichenfolge zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="085e8-120">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="085e8-121">Öffnen Sie **CalendarPage.XAML.cs** , und entfernen Sie die folgenden `OnAppearing` Zeilen aus der Funktion.</span><span class="sxs-lookup"><span data-stu-id="085e8-121">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="085e8-122">Fügen Sie an ihrer Stelle den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="085e8-122">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="085e8-123">Führen Sie die APP aus, melden Sie sich an, und klicken Sie auf das Navigationselement **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="085e8-123">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="085e8-124">Die Liste der Ereignisse sollte angezeigt werden, wobei die Werte **Start** und **End** formatiert sind.</span><span class="sxs-lookup"><span data-stu-id="085e8-124">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-page.png)
