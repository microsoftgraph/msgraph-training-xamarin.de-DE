<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie eine neue Azure AD native-Anwendung mithilfe des Azure Active Directory Admin Center.

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen (Vorschau)** unter **Verwalten** aus.

    ![Screenshot der APP-Registrierungen ](./images/aad-portal-app-registrations.png)

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `Xamarin Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Lassen Sie **URI umleiten** leer.

    ![Screenshot der Seite "Registrieren einer Anwendung"](./images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **Xamarin Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. Wählen Sie den Link umLeitungs- **URI hinzufügen** aus. Suchen Sie auf der Seite **URIs umleiten** nach dem Abschnitt Empfohlene Umleitungs- **URIs für öffentliche Clients (Mobil, Desktop)** . Wählen Sie den Anfang mit `msal` **und** den **urn: IETF: WG: OAuth: 2.0: OOB** URI aus. Kopieren Sie den Wert, der `msal`mit beginnt, und wählen Sie dann **Speichern**aus. Speichern Sie den kopierten Umleitungs-URI, den Sie im nächsten Schritt benötigen.

    ![Screenshot der Seite "umLeitungs-URIs"](./images/aad-redirect-uris.png)