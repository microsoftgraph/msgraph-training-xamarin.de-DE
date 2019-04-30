# <a name="how-to-run-the-completed-project"></a>Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert. (**Hinweis:** dieses Lernprogramm wurde mit visual Studio 2017 Version 15.9.6 und Visual Studio für Mac Version 7.7.4 geschrieben. Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, die jedoch nicht getestet wurden.)
- Xamarin wird als Teil der Visual Studio-Installation installiert. Anweisungen zum Installieren und Konfigurieren von Xamarin finden Sie unter [installIng Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) .
- Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Geschäfts-oder Schulkonto.

Wenn Sie nicht über ein Microsoft-Konto verfügen, gibt es eine Reihe von Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können [sich für das office 365-Entwicklerprogramm anmelden](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-an-application-with-the-azure-portal"></a>Registrieren einer Anwendung mit dem Azure-Portal

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `Xamarin Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Lassen Sie **URI umleiten** leer.

    ![Screenshot der Seite "Registrieren einer Anwendung"](../../tutorial/images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **Xamarin Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](../../tutorial/images/aad-application-id.png)

1. Wählen Sie den Link umLeitungs- **URI hinzufügen** aus. Suchen Sie auf der Seite **URIs umleiten** nach dem Abschnitt Empfohlene Umleitungs- **URIs für öffentliche Clients (Mobil, Desktop)** . Wählen Sie den Anfang mit `msal` **und** den **urn: IETF: WG: OAuth: 2.0: OOB** URI aus. Kopieren Sie den Wert, der `msal`mit beginnt, und wählen Sie dann **Speichern**aus. Speichern Sie den kopierten Umleitungs-URI, den Sie im nächsten Schritt benötigen.

    ![Screenshot der Seite "umLeitungs-URIs"](../../tutorial/images/aad-redirect-uris.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` die Datei `OAuthSettings.cs`in um.
1. Bearbeiten Sie `OAuthSettings.cs` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie vom Azure-Portal erhalten haben.
    1. Ersetzen `YOUR_REDIRECT_URI_HERE` Sie durch den Umleitungs- `msal` URI, der mit dem Azure-Portal beginnt.
1. Öffnen Sie im Projekt **GraphTutorial. IOS** die `Info.plist` Datei.
    1. Suchen Sie auf der Registerkarte **erweitert** den Abschnitt **URL-Typen** . Ändern Sie den Eintrag **URL** - `YOUR_REDIRECT_URI_HERE` Schemas von in den Umleitungs-URI aus der `msal`App-Registrierung, die mit beginnt.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Drücken Sie in Visual Studio **F5** , oder klicken Sie auf **Debuggen > Start Debugging**.