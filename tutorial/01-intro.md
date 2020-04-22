<!-- markdownlint-disable MD002 MD041 -->

In diesem Lernprogramm erfahren Sie, wie Sie eine Xamarin-app erstellen, die die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.

> [!TIP]
> Wenn Sie das fertige Lernprogramm einfach herunterladen möchten, können Sie das GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-xamarin)herunterladen oder Klonen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie dieses Lernprogramm starten, sollten Sie [Visual Studio](https://visualstudio.microsoft.com/vs/) auf einem Computer mit Windows 10 mit [aktivierter Entwicklermodus](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)installiert haben. Wenn Sie nicht über Visual Studio verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.

Außerdem sollte Xamarin als Teil Ihrer Visual Studio Installation installiert sein. Weitere Informationen zum Installieren und Konfigurieren von Xamarin finden Sie unter [Installing Xamarin](/xamarin/cross-platform/get-started/installation) .

Optional sollten Sie auch Zugriff auf einen Mac haben, auf dem Visual Studio für Mac installiert ist. Wenn Sie keinen Zugriff haben, können Sie dieses Lernprogramm dennoch ausführen, die IOS-spezifischen Abschnitte jedoch nicht abschließen.

Sie sollten auch über ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto verfügen. Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

> [!NOTE]
> Dieses Lernprogramm wurde mit Visual Studio 2019 Version 16.5.2 und Visual Studio für Mac Version 8.5.1 geschrieben. Auf beiden Computern ist die Android SDK-Plattform 28 installiert. Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.

## <a name="feedback"></a>Feedback

Geben Sie Feedback zu diesem Lernprogramm im [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-xamarin)an.
