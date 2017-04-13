<properties
    pageTitle="Azure B2C Directory attiva: La reimpostazione della password self-service | Microsoft Azure"
    description="Un argomento illustra come configurare la modalità self-service reimpostazione della password per i clienti in Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C Directory attiva: Configurare la modalità self-service reimpostazione della password per i clienti

Con la caratteristica di reimpostazione password self-service, i consumatori (che hanno effettuato l'iscrizione per gli account locali) possono reimpostare le password autonomamente. Questo riduce notevolmente il carico il team di supporto, soprattutto se l'applicazione ha milioni di clienti utilizzando regolarmente. Attualmente non è supportato solo tramite un indirizzo di posta elettronica verificato come un metodo di ripristino. Metodi di ripristino aggiuntive (numero di telefono verificato, sicurezza domande e così via) verrà aggiunto in futuro.

> [AZURE.NOTE]
In questo articolo si applica a password self-service Reimposta utilizzata nel contesto di un criterio di accesso. Se è necessario completamente personalizzabili criteri richiamati dell'App la reimpostazione della password, vedere [questo articolo](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Per impostazione predefinita, la directory non ha password self-service reimpostare attivato. Per attivare il componente aggiuntivo, procedere come segue:

1. Accedere al [portale classica Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Questo è lo stesso lavoro o dell'istituto di istruzione account o lo stesso account Microsoft usato per creare la directory.
2. Individuare l'estensione di Active Directory sulla barra di spostamento sul lato sinistro.
3. Individuare la directory sotto la scheda **Directory** e fare clic su esso.
4. Fare clic sulla scheda **Configura** .
5. Scorrere fino alla sezione **criteri di reimpostazione della password utente** e attivare o disattivare l'opzione **Reimposta utenti abilitati per la password,** su **Sì**. Si noti che sia selezionata l'opzione di **Indirizzo di posta elettronica alternativo** ; lasciarlo in quanto è.

    ![Reimpostazione della password self-service](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Fare clic su **Salva** nella parte inferiore della pagina. Fatto!

Per testare, utilizzare la funzionalità "Esegui" in un criterio di accesso che contiene gli account locali come provider di identità. L'account locale accesso in pagina (in cui si immette un indirizzo di posta elettronica e password, o un nome utente e password), fare clic su **non è possibile accedere all'account?** per verificare l'esperienza utente.

> [AZURE.NOTE]
Le pagine di reimpostazione password self-service possono personalizzate tramite la [caratteristica di personalizzazione della società](../active-directory/active-directory-add-company-branding.md).
