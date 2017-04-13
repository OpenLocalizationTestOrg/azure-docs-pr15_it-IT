<properties
   pageTitle="Come richiedere l'autenticazione a più fattori | Microsoft Azure"
   description="Informazioni su come richiedere l'autenticazione a più fattori (MFA) per le identità privilegi con l'estensione di Azure Active Directory privilegi gestione delle identità."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Come richiedere MFA in Azure Active Directory privilegi di gestione delle identità

È consigliabile richiedono l'autenticazione a più fattori (MFA) per tutti gli amministratori. Consente di ridurre i rischi di attacco a causa di una password compromessa.

È possibile richiedere che gli utenti completare una richiesta di MFA quando eseguire l'accesso. Post di blog [MFA per Office 365 e MFA per Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) Confronta quali applicazioni sono incluse nelle sottoscrizioni a Office e Azure, con le caratteristiche di cui l'offerta di autenticazione a più fattori di Microsoft Azure.

È anche possibile richiedere che gli utenti completare una richiesta di MFA quando si attiva un ruolo di Azure Active Directory PIM. In questo modo, se l'utente non è stata completata una richiesta di MFA quando è stato effettuato l'accesso, vengono chiesto di farlo dal PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Richiedere MFA in Azure Active Directory privilegi di gestione delle identità

Quando si gestiscono identità in PIM come amministratore ruolo privilegi, si possono vedere avvisi che consigliabile MFA per account con privilegi. Fare clic sull'avviso di sicurezza nel dashboard PIM e una nuova pala verrà aperto con un elenco degli account di amministratore che è necessario MFA.  È possibile richiedere MFA selezionando più ruoli e quindi fare clic sul pulsante **risolvere** oppure è possibile fare clic sui puntini di sospensione accanto a singoli ruoli e quindi fare clic sul pulsante **Correggi** .

> [AZURE.IMPORTANT] Ora, MFA Azure funziona solo con lavoro o dell'istituto di istruzione account, non un account di Microsoft (in genere un account personale che viene utilizzato per accedere a servizi come Skype, Xbox, Outlook.com ecc.) a destra. Per questi motivi, chiunque utilizzi un account Microsoft non può essere un amministratore idoneo perché non è possibile utilizzare MFA per attivare i ruoli. Se questi utenti è necessario continuare a gestire carichi di lavoro usando un account Microsoft, elevare loro agli amministratori permanenti per adesso.

Inoltre, è possibile modificare il requisito di MFA con un ruolo specifico facendo clic su di esso nella sezione ruoli del dashboard PIM. Quindi fare clic su **Impostazioni** e il ruolo e quindi selezionando **Abilita** secondo l'autenticazione a più fattori.

## <a name="how-azure-ad-pim-validates-mfa"></a>Il modo in cui Azure Active Directory PIM convalida MFA

Sono disponibili due opzioni per la convalida dei MFA quando un utente attiva un ruolo.

L'opzione più semplice consiste nel si basano su Azure MFA per gli utenti che si desidera attivare un ruolo privilegiato. A tale scopo, verificare che gli utenti sono concesso in licenza, se necessario e sono registrati per MFA Azure. Ulteriori informazioni su come eseguire questa operazione sono in [Guida introduttiva a autenticazione a più fattori Azure nel cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). È consigliabile, ma non è necessario, configurare Azure Active Directory per imporre MFA per questi utenti quando eseguire l'accesso. Ciò avviene perché i controlli MFA saranno alla Azure Active Directory PIM stesso.

In alternativa, se gli utenti autenticati locale è possibile impostare il provider di identità responsabile per MFA. Ad esempio, se è stato configurato Active Directory Federation Services in modo da richiedere l'autenticazione basata su smart card prima di accedere a Azure Active Directory, [le risorse cloud di protezione con autenticazione a più fattori Azure e ADFS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) include istruzioni per la configurazione di ADFS per l'invio delle attestazioni di Azure Active Directory. Quando si tenta di attivare un ruolo, Azure Active Directory PIM accetta che MFA è già stato convalidato dell'utente dopo avere ricevuto attestazioni appropriate.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
