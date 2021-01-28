<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .NET,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Öffnen **Sie "CalendarPage.xaml"** im **GraphTutorial-Projekt,** und ersetzen Sie den Inhalt durch Folgendes.

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

1. Öffnen **CalendarPage.xaml.cs,** und fügen Sie die folgenden `using` Anweisungen am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. Fügen Sie der Klasse die folgende Funktion hinzu, um den Beginn der aktuellen Woche in der Zeitzone des `CalendarPage` Benutzers zu erhalten.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. Fügen Sie der Klasse die folgende Funktion hinzu, um die Ereignisse des Benutzers zu erhalten und `CalendarPage` die JSON-Antwort anzeigen.

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

    Überlegen Sie, was der Code `OnAppearing` gerade macht.

    - Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarview`.
        - Die `startDateTime` Parameter definieren den Anfang und das Ende der `endDateTime` Kalenderansicht.
        - Der Header bewirkt, dass das Ereignis und die Ereignisse in der Zeitzone des `Prefer: outlook.timezone` `start` Benutzers zurückgegeben `end` werden.
        - Die Funktion beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `Select` von der App tatsächlich verwendet werden.
        - Die `OrderBy` Funktion sortiert die Ergebnisse nach Startdatum und -uhrzeit.
        - Die `Top` Funktion fordert mindestens 50 Ereignisse an.

1. Führen Sie die App aus, melden Sie sich an, und klicken **Sie** im Menü auf das Navigationselement "Kalender". Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie das JSON-Dump durch eine benutzerfreundliche Anzeige der Ergebnisse ersetzen.

Erstellen Sie [](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) zunächst einen Bindungswertkonverter, um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone-Werte](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) in die vom Benutzer erwarteten Datums- und Uhrzeitformate zu konvertieren.

1. Klicken Sie im Projekt **"GraphTutorial"** mit der rechten Maustaste auf den Ordner "Modelle", und wählen Sie **"Hinzufügen"** und dann **"Klasse...**" aus.  Benennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **"Hinzufügen" aus.**

1. Ersetzen Sie den gesamten Inhalt der Datei durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. Ersetzen Sie den gesamten Inhalt von **CalendarPage.xaml** durch Folgendes.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    Dadurch wird die durch `Editor` eine `ListView` ersetzt. The `DataTemplate` used to render each item uses the to convert the and properties of the event to `GraphDateTimeTimeZoneConverter` a `Start` `End` string.

1. Öffnen **CalendarPage.xaml.cs,** und entfernen Sie die folgenden Zeilen aus der `OnAppearing` Funktion.

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. Fügen Sie an ihrer Stelle den folgenden Code hinzu.

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. Führen Sie die App aus, melden Sie sich an, und klicken Sie auf das **Navigationselement** "Kalender". Die Liste der Ereignisse sollte mit den **Start-** und **Endwerten** formatiert sein.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-page.png)
