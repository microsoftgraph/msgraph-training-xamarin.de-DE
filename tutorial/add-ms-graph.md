<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie die [Microsoft Graph-Client Bibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Aufrufe von Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen aus Outlook

Aktualisieren Sie zunächst die Datei **CalendarPage. XAML** im **GraphTutorial** -Projekt. Öffnen Sie die Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.

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

Öffnen `CalendarPage.xaml.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

Fügen Sie dann den folgenden Code hinzu, um die Ereignisse des Benutzers abzurufen und die JSON-Antwort anzuzeigen.

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

Überlegen Sie sich, was `OnAppearing` der Code in tut.

- Die URL, die aufgerufen wird, `/v1.0/me/events`lautet.
- Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die die Ansicht tatsächlich verwendet.
- Die `OrderBy` Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu denen Sie erstellt wurden, wobei das neueste Element zuerst angezeigt wird.

Sie können jetzt die app ausführen, sich anmelden und im Menü auf das Navigationselement **Kalender** klicken. Es sollte ein JSON-Dump der Ereignisse im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie den JSON-Dump durch einen anderen ersetzen, um die Ergebnisse auf benutzerfreundliche Weise anzuzeigen. Erstellen Sie zunächst einen [bindungswert Konverter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) , um die von Microsoft Graph zurückgegebenen [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Werte in die vom Benutzer erwarteten Datums-und Uhrzeitformate umzuwandeln. Klicken Sie im **GraphTutorial** -Projekt mit der rechten Maustaste auf den Ordner **Modelle** , und wählen Sie **neu**, dann **Klasse.**... Benennen Sie die `GraphDateTimeTimeZoneConverter` Klasse, und wählen Sie **Hinzufügen**. Ersetzen Sie den gesamten Inhalt der Datei durch Folgendes.

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

Ersetzen Sie als nächstes den gesamten Inhalt `CalendarPage.xaml` von durch Folgendes.

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
                      VerticalOptions="StartAndExpand"
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

Dadurch wird die `Editor` durch eine `ListView`ersetzt. Die `DataTemplate` zum Rendern jedes Elements verwendete verwendet die `GraphDateTimeTimeZoneConverter` , um die `Start` and `End` -Eigenschaften des Ereignisses in eine Zeichenfolge zu konvertieren. Öffnen `CalendarPage.xaml.cs` Sie nun die folgenden Zeilen aus der `OnAppearing` -Funktion, und entfernen Sie Sie.

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

Fügen Sie an ihrer Stelle den folgenden Code hinzu.

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

Führen Sie die APP aus, melden Sie sich an, und klicken Sie auf das Navigationselement **Kalender** . Es sollte eine Liste der Ereignisse angezeigt werden, deren **anfangs** -und Endwerte formatiert sind. ****

![Screenshot der Ereignistabelle](./images/calendar-page.png)
