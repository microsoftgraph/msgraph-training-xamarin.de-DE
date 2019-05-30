<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie mithilfe des Azure Active Directory Admin Center eine neue Azure AD systemeigene Anwendung.

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App** -Registrierungen unter **Manage**aus.

    ![Ein Screenshot der APP-Registrierungen ](./images/aad-portal-app-registrations.png)

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `Xamarin Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Ändern Sie unter Umleitungs- **URI (optional)** das Dropdown-Menü in **Public Client (Mobile & Desktop)**, `urn:ietf:wg:oauth:2.0:oob`und legen Sie den Wert auf fest.

    ![Screenshot der Seite "Anwendung registrieren"](./images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **Xamarin Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, die Sie im nächsten Schritt benötigen.

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. Wählen Sie die **Authentifizierung** unter **Manage**aus. Suchen Sie den Abschnitt **vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobile, Desktop)** . Wählen Sie den URI aus, `msal`der mit beginnt, und wählen Sie dann **Speichern**aus.

    ![Screenshot der Seite "Umleitungs-URIs"](./images/aad-redirect-uris.png)
