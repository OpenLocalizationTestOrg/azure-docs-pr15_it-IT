<properties
    pageTitle="Servizi di dominio Azure Active Directory: Guida di amministrazione | Microsoft Azure"
    description="Creare un'unità Organizzativa in servizi di dominio Active Directory di Azure gestiti domini"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Creare un'unità Organizzativa su un dominio gestito di servizi di dominio Active Directory di Azure
Azure domini gestiti di servizi di dominio Active Directory includono due contenitori predefiniti denominati rispettivamente "AADDC computer" e "AADDC utenti". Il contenitore 'AADDC computer' include oggetti computer per tutti i computer che fanno parte di dominio gestito. Il contenitore "Utenti AADDC" include utenti e gruppi nel tenant di Azure Active Directory. In alcuni casi, potrebbe essere necessario creare gli account di servizio nel dominio gestito per distribuire carichi di lavoro. A tale scopo, è possibile creare un personalizzato unità Organizzativa nel dominio gestito e creare gli account di servizio all'interno di tale unità Organizzativa. In questo articolo viene illustrato come creare un'unità Organizzativa nel dominio gestito.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installare gli strumenti di amministrazione di Active Directory in una macchina virtuale dominio per l'amministrazione remota
Azure domini gestiti di servizi di dominio Active Directory possono essere gestiti in remoto utilizzando familiari strumenti di amministrazione di Active Directory, ad esempio la Active Directory Amministrazione centrale (ADAC) o PowerShell Active Directory. Amministratori tenant non dispongano di privilegi per connettersi a controller di dominio nel dominio gestito tramite Desktop remoto. Per amministrare il dominio gestito, installare la funzionalità di strumenti di amministrazione di Active Directory in una macchina virtuale al dominio gestito. Fare riferimento all'articolo intitolato [amministrare un dominio di servizi di dominio Active Directory di Azure gestiti](active-directory-ds-admin-guide-administer-domain.md) per le istruzioni.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Creare un'unità organizzativa nel dominio gestito
Ora che gli strumenti di amministrazione di Active Directory sono installati nel dominio macchina virtuale, è possibile utilizzare questi strumenti per creare un'unità organizzativa nel dominio gestito. Eseguire i passaggi seguenti:

> [AZURE.NOTE] Solo i membri del gruppo 'AAD controller di dominio Administrators' dispongono dei privilegi necessari per creare un'unità Organizzativa. Assicurarsi di eseguire la procedura seguente come utente che appartiene a questo gruppo.

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Verranno visualizzati gli strumenti di amministrazione di Active Directory è installati nel computer virtuale.

    ![Strumenti di amministrazione installati sul server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Fare clic su **Centro amministrativo di Active Directory**.

    ![Centro di amministrazione di Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Per visualizzare il dominio, fare clic sul nome di dominio nel riquadro a sinistra (ad esempio, ' contoso100.com').

    ![ADAC - Visualizza dominio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Nel riquadro **attività** sul lato destro, fare clic su **Nuovo** sotto il nodo di nome di dominio. In questo esempio abbiamo fare clic su **Nuovo** sotto il nodo 'contoso100(local)' nel riquadro **attività** sul lato destro.

    ![ADAC - nuova unità Organizzativa](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Verrà visualizzata l'opzione per creare un'unità organizzativa. Fare clic su **Unità organizzativa** per avviare la finestra di dialogo **Crea unità organizzativa** .

6. Nella finestra di dialogo **Crea unità organizzativa** , specificare un **nome** per la nuova unità Organizzativa. Fornire una breve descrizione per l'unità Organizzativa. È inoltre possibile impostare il campo **Gestito da** per l'unità Organizzativa. Per creare l'unità Organizzativa personalizzato, fare clic su **OK**.

    ![ADAC - finestra di dialogo unità Organizzativa](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. L'unità Organizzativa appena creato verrà visualizzato nell'interfaccia di amministrazione Active Directory (ADAC).

    ![ADAC - unità Organizzativa creata](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Autorizzazioni/sicurezza per le unità organizzative appena create
Per impostazione predefinita, l'utente (membro del gruppo 'AAD controller di dominio Administrators') che ha creato l'unità Organizzativa personalizzato viene concesso privilegi di amministratore (controllo completo) per l'unità Organizzativa. L'utente può quindi procedere e privilegi ad altri utenti o al gruppo 'AAD controller di dominio Administrators' in base alle esigenze. Come illustrato nella schermata riportata di seguito, l'utente 'bob@domainservicespreview.onmicrosoft.com' persona che ha creato la nuova unità organizzativa 'MyCustomOU' concessa l'autorizzazione controllo completo su di esso.

 ![ADAC - nuovi protezione unità Organizzativa](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Note sull'amministrazione unità organizzative personalizzate
Dopo avere creato un'unità Organizzativa, è possibile procedere e creare gli utenti, gruppi, computer e gli account di servizio in questa unità Organizzativa. È possibile spostare gli utenti o gruppi da 'AAD controller di dominio utenti' OU per unità organizzative personalizzate.

> [AZURE.WARNING] Account utente, gruppi, gli account di servizio e gli oggetti computer creati in unità organizzative personalizzate non sono disponibili nel tenant di Azure Active Directory. In altre parole, questi oggetti non presentino verso l'alto tramite l'API di Azure Active Directory grafico o nell'interfaccia utente Azure Active Directory. Questi oggetti sono disponibili solo nel dominio gestito servizi di dominio Active Directory Azure.


## <a name="related-content"></a>Contenuti correlati

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Centro di amministrazione di Active Directory: Guida introduttiva](https://technet.microsoft.com/library/dd560651.aspx)

- [Guida dettagliata account del servizio](https://technet.microsoft.com/library/dd548356.aspx)
