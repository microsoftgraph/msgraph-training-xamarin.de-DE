# <a name="completed-module-add-azure-ad-authentication"></a>Abgeschlossenes Modul: Hinzufügen der Azure AD-Authentifizierung

Die Version des Projekts in diesem Verzeichnis spiegelt das Abschließen des Lernprogramms durch [Hinzufügen der Azure AD-Authentifizierung](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=3)wider. Wenn Sie diese Version des Projekts verwenden, müssen Sie den Rest des Lernprogramms beginnend beim [Abrufen von Kalenderdaten](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=4)abschließen.

> **Hinweis:** Es wird davon ausgegangen, dass Sie bereits eine Anwendung im Azure-Portal registriert haben, wie in [Registrieren der APP im Portal](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=2)angegeben. Sie müssen diese Version des Beispiels wie folgt konfigurieren:
>
> 1. Benennen Sie `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` die Datei `OAuthSettings.cs`in um.
> 1. Bearbeiten Sie `OAuthSettings.cs` die Datei, und nehmen Sie die folgenden Änderungen vor.
>     1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie vom Azure-Portal erhalten haben.
>     1. Ersetzen `YOUR_REDIRECT_URI_HERE` Sie durch den Umleitungs- `msal` URI, der mit dem Azure-Portal beginnt.
> 1. Öffnen Sie im Projekt **GraphTutorial. IOS** die `Info.plist` Datei.
>     1. Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Ändern Sie den Eintrag **URL** - `YOUR_REDIRECT_URI_HERE` Schemas von in den Umleitungs-URI aus der `msal`App-Registrierung, die mit beginnt.