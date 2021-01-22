<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="06c9b-101">In diesem Abschnitt fügen Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzu.</span><span class="sxs-lookup"><span data-stu-id="06c9b-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="06c9b-102">Fügen Sie eine neue Seite für die neue Ereignisansicht hinzu.</span><span class="sxs-lookup"><span data-stu-id="06c9b-102">Add a new page for the new event view.</span></span> <span data-ttu-id="06c9b-103">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **GraphTutorial,** und wählen **Sie > Neues Element hinzufügen... aus.** Wählen **Sie "Leere Seite"** aus, `NewEventPage.xaml` geben Sie das Feld **"Name"** ein, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="06c9b-103">Right-click the **GraphTutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Blank Page**, enter `NewEventPage.xaml` in the **Name** field, and select **Add**.</span></span>

1. <span data-ttu-id="06c9b-104">Öffnen **Sie NewEventPage.xaml,** und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="06c9b-104">Open **NewEventPage.xaml** and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml" id="NewEventPageXamlSnippet":::

1. <span data-ttu-id="06c9b-105">Öffnen **NewEventPage.xaml.cs,** und fügen Sie die folgenden `using` Anweisungen am Oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="06c9b-105">Open **NewEventPage.xaml.cs** and add the following `using` statements to the top of the file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="UsingStatementsSnippet":::

1. <span data-ttu-id="06c9b-106">Fügen Sie **die Schnittstelle "INotifyPropertyChange"** der **Klasse "NewEventPage"** hinzu.</span><span class="sxs-lookup"><span data-stu-id="06c9b-106">Add the **INotifyPropertyChange** interface to the **NewEventPage** class.</span></span> <span data-ttu-id="06c9b-107">Ersetzen Sie die vorhandene Klassendeklaration durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="06c9b-107">Replace the existing class declaration with the following.</span></span>

    ```csharp
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class NewEventPage : ContentPage, INotifyPropertyChanged
    {
        public NewEventPage()
        {
            InitializeComponent();
            BindingContext = this;
        }
    }
    ```

1. <span data-ttu-id="06c9b-108">Fügen Sie der Klasse **NewEventPage die folgenden Eigenschaften** hinzu.</span><span class="sxs-lookup"><span data-stu-id="06c9b-108">Add the following properties to the **NewEventPage** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. <span data-ttu-id="06c9b-109">Fügen Sie den folgenden Code hinzu, um das Ereignis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="06c9b-109">Add the following code to create the event.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. <span data-ttu-id="06c9b-110">Speichern Sie die Änderungen, und führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="06c9b-110">Save your changes and run the app.</span></span> <span data-ttu-id="06c9b-111">Melden Sie sich an, wählen Sie **das** Menüelement  "Neues Ereignis", füllen Sie das Formular aus, und wählen Sie "Erstellen" aus, um dem Kalender des Benutzers ein Ereignis hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="06c9b-111">Sign in, select the **New event** menu item, fill in the form, and select **Create** to add an event to the user's calendar.</span></span>

    ![Screenshot der neuen Ereignisseite](images/new-event-page.png)
