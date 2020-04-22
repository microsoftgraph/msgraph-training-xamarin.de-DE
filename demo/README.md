# <a name="how-to-run-the-completed-project"></a>Vorgehensweise Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert. (**Hinweis:** dieses Lernprogramm wurde mit Visual Studio 2019 Version 16.5.2 und Visual Studio für Mac-Version 8.5.1 geschrieben. Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)
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
    - Ändern Sie unter **Umleitungs-URI (optional)** das Dropdown-Menü in **Public Client (Mobile & Desktop)**, `msauth://com.companyname.GraphTutorial`und legen Sie den Wert auf fest.

    ![Screenshot der Seite "Anwendung registrieren"](../../tutorial/images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **Xamarin Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, die Sie im nächsten Schritt benötigen.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` die Datei `OAuthSettings.cs`in.
1. Bearbeiten Sie `OAuthSettings.cs` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie aus dem Azure-Portal erhalten haben.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Drücken Sie in Visual Studio **F5** , oder wählen Sie **Debug > Debuggen starten**aus.
