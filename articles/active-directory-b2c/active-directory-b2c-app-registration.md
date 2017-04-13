<properties
    pageTitle="Azure B2C Directory attiva: Registrazione di applicazioni | Microsoft Azure"
    description="Come registrare l'applicazione con Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure B2C Directory attiva: Registrare l'applicazione

## <a name="prerequisite"></a>Prerequisito

Per creare un'applicazione che accetta consumer abbonamento e l'accesso, è prima necessario registrare l'applicazione di un tenant di Azure Active Directory B2C. È possibile ottenere il proprio tenant usando la procedura descritta in [creare un tenant di Azure Active Directory B2C](active-directory-b2c-get-started.md). Dopo l'esecuzione di tutti i passaggi descritti in questo articolo, sarà necessario e il caratteristiche B2C aggiunto al Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Passare a e il caratteristiche B2C

Se si dispone e il caratteristiche B2C aggiunto al Startboard, si vedrà e il non appena si accede al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant B2C.

È anche possibile accedere e il facendo clic su **Sfoglia** e quindi su **Azure Active Directory B2C** nel riquadro di spostamento sinistra nel [portale di Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] È necessario essere un amministratore globale del tenant B2C per poter accedere e il caratteristiche B2C. Un amministratore globale da qualsiasi altro tenant o un utente da qualsiasi tenant non è possibile accedervi.  È possibile passare al tenant di B2C utilizzando il riquadro di selezione tipo tenant in alto a destra del portale di Azure.

## <a name="register-an-application"></a>Registrazione di un'applicazione

1. In e il caratteristiche B2C nel portale di Azure, fare clic su **applicazioni**.
2. Fare clic su **Add +** nella parte superiore e il.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, è possibile immettere "Contoso B2C app".
4. Se si sta scrivendo un'applicazione web, attivare e disattivare l'opzione **Include web app o web API** su **Sì**. **URL di risposta** sono endpoint in Azure Active Directory B2C restituirà tutti i token che richiede l'applicazione. Ad esempio, immettere `https://localhost:44321/`. Se l'applicazione web anche essere chiama alcune API protetto da Azure Active Directory B2C web, sarà desiderata creare un' **Applicazione segreto** anche facendo clic sul pulsante **Genera chiave** .

    > [AZURE.NOTE] Un' **Applicazione segreto** è una credenziale di sicurezza importanti e deve essere protetto in modo appropriato.

5. Se si sta scrivendo un'applicazione mobile, attivare e disattivare l'opzione **Includi native client** su **Sì**. Copiare in basso il valore predefinito **Reindirizzare URI** che venga creato automaticamente.
6. Fare clic su **Crea** per registrare l'applicazione.
7. Fare clic sull'applicazione appena creato e copiare in basso univoci **Applicazione Client ID** che verrà utilizzato in un punto successivo del codice.

> [AZURE.IMPORTANT] Le applicazioni create in e il caratteristiche B2C necessario gestite nello stesso percorso. Se si modificano B2C applicazioni tramite PowerShell o un altro portale, che diventano non supportate e probabile che non funziona con Azure Active Directory B2C.

## <a name="build-a-quick-start-application"></a>Creare un'applicazione di avvio veloce

Dopo aver creato un'applicazione registrata con Azure Active Directory B2C, è possibile eseguire una delle corso rapida delle esercitazioni per iniziare a utilizzarlo. Ecco alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
