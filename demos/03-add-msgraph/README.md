# <a name="how-to-run-the-completed-project"></a>Vorgehensweise Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert. (**Hinweis:** dieses Lernprogramm wurde mit Visual Studio 2017 Version 15.9.6 und Visual Studio für Mac-Version 7.7.4 geschrieben. Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)
- Xamarin wird als Teil Ihrer Visual Studio Installation installiert. Weitere Informationen zum Installieren und Konfigurieren von Xamarin finden Sie unter [Installing Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) .
- Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto.

Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-an-application-with-the-azure-portal"></a>Registrieren einer Anwendung im Azure-Portal

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `Xamarin Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Lassen Sie **URI umleiten** leer.

    ![Screenshot der Seite "Anwendung registrieren"](../../tutorial/images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **Xamarin Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, die Sie im nächsten Schritt benötigen.

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](../../tutorial/images/aad-application-id.png)

1. Klicken Sie auf den Link **Umleitungs-URI hinzufügen** . Suchen Sie auf der Seite " **Umleitungs-URIs** " den Abschnitt **vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobile, Desktop)** . Wählen Sie den URI, mit `msal` dem begonnen wird, **und** den URI **urn: IETF: WG: OAuth: 2.0: OOB** aus. Kopieren Sie den Wert, der `msal`mit beginnt, und wählen Sie dann **Speichern**aus. Speichern Sie den kopierten Umleitungs-URI, den Sie im nächsten Schritt benötigen.

    ![Screenshot der Seite "Umleitungs-URIs"](../../tutorial/images/aad-redirect-uris.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` die Datei `OAuthSettings.cs`in.
1. Bearbeiten Sie `OAuthSettings.cs` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.
1. Öffnen Sie im Projekt **GraphTutorial. IOS** die `Info.plist` Datei.
    1. Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Ersetzen `YOUR_APP_ID_HERE` Sie im Wert der **URL-Schemas** durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.
1. Öffnen Sie im **GraphTutorial. Android** -Projekt die `Properties/AndroidManifest.xml` Datei.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Drücken Sie in Visual Studio **F5** , oder wählen Sie **Debug #a0 Debuggen starten**aus.
