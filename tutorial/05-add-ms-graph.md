<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Öffnen Sie **CalendarPage. XAML** im **GraphTutorial** -Projekt, und ersetzen Sie den Inhalt durch Folgendes.

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

1. Öffnen Sie **CalendarPage.XAML.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. Fügen Sie die folgende Funktion zur `CalendarPage` -Klasse hinzu, um die Ereignisse des Benutzers abzurufen und die JSON-Antwort anzuzeigen.

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

    Überprüfen Sie, was `OnAppearing` der Code in tut.

    - Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.
    - Die `Select`-Funktion beschränkt die Felder, die für jedes Ereignis zurückgegeben werden, auf nur diejenigen, die von der Ansicht tatsächlich verwendet werden.
    - Die `OrderBy`-Funktion sortiert die Ergebnisse nach Datum und Uhrzeit ihrer Erstellung, wobei das aktuellste Element zuerst angezeigt wird.

1. Führen Sie die APP aus, melden Sie sich an, und klicken Sie im Menü auf das Navigationselement **Kalender** . Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.

Erstellen Sie zunächst einen [Binding-Wertkonverter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) , um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Werte in die vom Benutzer erwarteten Datums-und Uhrzeitformate zu konvertieren.

1. Klicken Sie im **GraphTutorial** -Projekt mit der rechten Maustaste auf den Ordner **Models** , und wählen Sie dann **Hinzufügen**und dann **Klasse...**. Nennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **Hinzufügen**aus.

1. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. Ersetzen Sie den gesamten Inhalt von **CalendarPage. XAML** durch Folgendes.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    Dadurch wird die `Editor` durch a `ListView`ersetzt. Der `DataTemplate` zum Rendern der einzelnen Elemente verwendete verwendet `GraphDateTimeTimeZoneConverter` , um die `Start` - `End` und-Eigenschaften des Ereignisses in eine Zeichenfolge zu konvertieren.

1. Öffnen Sie **CalendarPage.XAML.cs** , und entfernen Sie die folgenden `OnAppearing` Zeilen aus der Funktion.

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. Fügen Sie an ihrer Stelle den folgenden Code hinzu.

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. Führen Sie die APP aus, melden Sie sich an, und klicken Sie auf das Navigationselement **Kalender** . Die Liste der Ereignisse sollte angezeigt werden, wobei die Werte **Start** und **End** formatiert sind.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-page.png)
