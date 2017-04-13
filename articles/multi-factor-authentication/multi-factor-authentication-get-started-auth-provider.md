<properties
    pageTitle="Guida introduttiva di Provider di autenticazione a più fattori Azure | Microsoft Azure"
    description="Informazioni su come creare un Provider di autenticazione a più fattori Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Guida introduttiva a un Provider di autenticazione a più fattori Azure
Verifica in due passaggi è disponibile per impostazione predefinita per gli amministratori globali che dispongono di Azure Active Directory e utenti di Office 365. Tuttavia, se si desidera sfruttare le [funzionalità avanzate](multi-factor-authentication-whats-next.md) è necessario acquistare la versione completa di autenticazione a più fattori Azure (MFA).

> [AZURE.NOTE]  Un Provider di autenticazione a più fattori Azure viene utilizzato per usufruire delle caratteristiche disponibili nella versione completa di Azure MFA. Per gli utenti che **non hanno licenze tramite MFA Azure, Azure Active Directory Premium o EMS**.  Azure MFA, Azure Active Directory Premium e questa funzionalità includono la versione completa di Azure MFA per impostazione predefinita.  Se si dispone di licenze, un Provider di autenticazione a più fattori Azure non è necessaria.

Per scaricare il SDK è necessario un provider di autenticazione a più fattori Azure.

> [AZURE.IMPORTANT]  Per scaricare questo SDK, creare un Provider di autenticazione a più fattori Azure anche se si dispone di Azure MFA, AAD Premium o EMS licenze.  Se si crea un Provider di autenticazione a più fattori Azure a questo scopo e si dispongono già di licenze, assicurarsi di creare il Provider con il modello **Per ogni utente abilitato** . Quindi, collegare il Provider di directory che contiene le licenze MFA Azure, Azure Active Directory Premium o EMS.  In questo modo che sono effettuata solo se si dispone di più utenti univoci utilizzando SDK rispetto al numero di licenze che si è proprietari.


## <a name="to-create-a-multi-factor-auth-provider"></a>Per creare un Provider di autenticazione a più fattori

Utilizzare la procedura seguente per creare un Provider di autenticazione a più fattori Azure.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory, nella parte superiore, selezionare **Il provider di autenticazione a più fattori**.
![Creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Nell'angolo inferiore fare clic su **Nuovo**.
![Creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. In servizi di App, selezionare **Il Provider di autenticazione a più fattori**
![creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selezionare **Crea veloce**.
![Creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Compilare i campi seguenti e selezionare **Crea**.
    1. **Nome** : il nome del Provider di autenticazione a più fattori.
    2. **Modello di utilizzo** : se si vuole consentire agli utenti singoli o una tariffa per la verifica. Scegliere una delle due opzioni:
        - Per l'autenticazione-modello acquisto di costi per l'autenticazione. In genere utilizzati per scenari che usa l'autenticazione a più fattori Azure in un'applicazione consumer esposto.
        - Per ogni utente abilitato: modello acquisto di costi per ogni utente abilitato. In genere utilizzati per l'accesso del dipendente alle applicazioni, ad esempio Office 365. Scegliere questa opzione se si dispone di alcuni utenti già concesso in licenza per MFA Azure.
    2. **Directory** -tenant di Azure Active Directory in cui è associato il Provider di autenticazione a più fattori. Tenere presente quanto segue:
        - Non è necessaria una directory di Azure Active Directory per creare un Provider di autenticazione a più fattori. Lasciare vuoto se si intende utilizzare il Server di autenticazione a più fattori Azure o SDK.
        - Il Provider di autenticazione a più fattori devono essere associato a una directory di Azure Active Directory per sfruttare le funzionalità avanzate.
        - Azure AD Connect, AAD Sync o DirSync sono solo un requisito se si sta sincronizzando l'ambiente di Active Directory locale con una directory di Azure Active Directory.  Se si utilizza solo una directory di Azure Active Directory che non è sincronizzata, quindi non se è necessario.
![Creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Quando si fa clic su Crea, viene creato il Provider di autenticazione a più fattori e verrà visualizzato un messaggio che informa: **creato il Provider di autenticazione a più fattori**. Fare clic su **Ok**.
![Creazione di un Provider MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
