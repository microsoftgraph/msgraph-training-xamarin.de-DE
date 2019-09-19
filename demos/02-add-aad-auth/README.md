# <a name="completed-module-add-azure-ad-authentication"></a>Abgeschlossenes Modul: Azure AD Authentifizierung hinzufügen

Die Version des Projekts in diesem Verzeichnis spiegelt das Abschließen des Lernprogramms bis zum [Add Azure AD-Authentifizierung](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=3)wider. Wenn Sie diese Version des Projekts verwenden, müssen Sie den Rest des Lernprogramms ab dem [Abrufen von Kalenderdaten](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=4)ausführen.

> **Hinweis:** Es wird davon ausgegangen, dass Sie bereits eine Anwendung im Azure-Portal registriert haben, wie unter [Registrieren der APP im Portal](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=2)angegeben. Sie müssen diese Version des Beispiels wie folgt konfigurieren:
>
> 1. Benennen Sie `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` die Datei `OAuthSettings.cs`in.
> 1. Bearbeiten Sie `OAuthSettings.cs` die Datei, und nehmen Sie die folgenden Änderungen vor.
>     1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.
> 1. Öffnen Sie im Projekt **GraphTutorial. IOS** die `Info.plist` Datei.
>    1. Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Ersetzen `YOUR_APP_ID_HERE` Sie im Wert der **URL-Schemas** durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.
> 1. Öffnen Sie im **GraphTutorial. Android** -Projekt die `Properties/AndroidManifest.xml` Datei.
>     1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.
