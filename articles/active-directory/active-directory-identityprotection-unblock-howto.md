<properties
    pageTitle="Azure Active Directory la protezione dell'identità - come sbloccare utenti | Microsoft Azure"
    description="Informazioni su come sbloccare gli utenti che sono stati bloccati da un criterio di protezione di Azure Active Directory dell'identità."
    services="active-directory"
    keywords="la protezione dell'identità di Azure active directory, sbloccare utente"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory la protezione dell'identità - come sbloccare gli utenti

Con protezione dell'identità di Azure Active Directory, è possibile configurare i criteri per impedire agli utenti se vengono soddisfatte le condizioni configurate. In genere, un utente bloccati contatti help desk per imparare a sbloccate. Negli argomenti viene illustrata la procedura è possibile eseguire per sbloccare un utente bloccato.


## <a name="determine-the-reason-for-blocking"></a>Determinare il motivo per il blocco

Come primo passaggio per lo sblocco di un utente, è necessario determinare il tipo di criteri che ha bloccato l'utente perché i passaggi successivi sono dipendono da essa. Con protezione dell'identità di Azure Active Directory, un utente può essere sia bloccato da un criterio di rischio di accesso o un criterio di rischio utente. 

È possibile ottenere il tipo di criteri che ha bloccato un utente dal titolo nella finestra di dialogo che è stato inserito l'utente durante un tentativo di accesso:

|Criteri | Finestra di dialogo utente|
|--- | --- |
|Rischio di accesso | ![Accessohttp bloccati](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Rischio utente | ![Account bloccato](./media/active-directory-identityprotection-unblock-howto/104.png) |


Un utente che è bloccato da:

- Criteri di rischio di accesso sono noto anche come accessohttp sospetti
- Un criterio di rischio utente è noto anche come un account di rischio

 
## <a name="unblocking-suspicious-sign-ins"></a>Sblocco accessi sospetti

Per sbloccare un accesso aggiuntivo sospetto, sono disponibili le opzioni seguenti:

1. **Accesso in una posizione familiare dal o dal dispositivo** - un motivo comune per bloccati accessi sospetti sono tentativi di accesso da dispositivi o con percorsi. Gli utenti possono determinare rapidamente se questo è il motivo blocco quando si tenta di accedere da una posizione familiare o dispositivo.


3. **Escludere dai criteri** - se si ritiene che la configurazione dei criteri di accesso corrente causa problemi per utenti specifici, è possibile escludere gli utenti del file. Vedere [i criteri di rischio di accesso](active-directory-identityprotection.md#sign-in-risk-policy) per altri dettagli.
 
4. **Criterio** - se si ritiene che la configurazione dei criteri causa problemi per tutti gli utenti, è possibile disabilitare i criteri. Vedere [accesso rischio](active-directory-identityprotection.md#sign-in-risk-policy) per altri dettagli.


## <a name="unblocking-accounts-at-risk"></a>Sblocco account rischio

Per sbloccare un account di rischio, sono disponibili le opzioni seguenti:

1. **Reimpostare la password** , è possibile reimpostare la password dell'utente. Vedere [la reimpostazione della password di protezione manuale](active-directory-identityprotection.md#manual-secure-password-reset) per altri dettagli.

2. **Chiudere tutti gli eventi di rischio** , rischio utente blocca un utente se è stato raggiunto il livello di rischio utente configurato per bloccare l'accesso. È possibile ridurre un utente del livello di rischio chiudendo manualmente segnalati eventi di rischio. Per ulteriori informazioni, vedere [chiusura eventi di rischio manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Escludere dai criteri** - se si ritiene che la configurazione dei criteri di accesso corrente causa problemi per utenti specifici, è possibile escludere gli utenti del file. Vedere [i criteri di rischio utente](active-directory-identityprotection.md#user-risk-policy) per altri dettagli.
 
4. **Criterio** - se si ritiene che la configurazione dei criteri causa problemi per tutti gli utenti, è possibile disabilitare i criteri. Vedere [i criteri di rischio utente](active-directory-identityprotection.md#user-risk-policy) per altri dettagli.




## <a name="next-steps"></a>Passaggi successivi

 Si desidera conoscere altre informazioni sulla protezione di Azure Active Directory identità? Estrarre [La protezione di Azure Active Directory identità](active-directory-identityprotection.md).
 

