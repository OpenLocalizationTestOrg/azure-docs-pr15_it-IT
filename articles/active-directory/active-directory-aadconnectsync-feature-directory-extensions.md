<properties
   pageTitle="Sincronizzazione di Azure AD Connect: estensioni Directory | Microsoft Azure"
   description="In questo argomento viene descritta la funzionalità di estensioni directory di Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronizzazione di Azure AD Connect: estensioni Directory
Estensioni directory consente di estendere lo schema di Azure Active Directory con attributi personalizzati da Active Directory locale. Questa caratteristica consente di creare applicazioni line costituita attributi che continuare a gestire locale. Questi attributi possono essere utilizzati tramite [estensioni di grafico di Azure Active directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). È possibile visualizzare gli attributi disponibili utilizzando rispettivamente [Azure Active Directory grafico explorer](https://graphexplorer.cloudapp.net) ed [Esplora file di Microsoft Graph](https://graphexplorer2.azurewebsites.net/) .

Attualmente non carico di lavoro di Office 365 implica l'uso di questi attributi.

Configurare gli attributi aggiuntivi che si desidera sincronizzare nel percorso impostazioni personalizzate nell'installazione guidata.
![Creazione guidata estensione schema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) l'installazione Mostra gli attributi seguenti, che risultano validi:

- Tipi di oggetto utenti e gruppi
- Attributi a valore singolo: stringa, booleano, intero, binario
- Attributi multivalore: stringa, binario

L'elenco degli attributi viene letto dalla cache creata durante l'installazione di Azure AD Connect. Se è stato esteso lo schema di Active Directory con attributi aggiuntivi, il [schema deve essere aggiornato](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) prima di questi nuovi attributi sono visibili.

È possibile associare fino a 100 attributi delle estensioni directory. La lunghezza massima è 250 caratteri. Se un valore dell'attributo non è più lungo, viene troncato dal motore di sincronizzazione.

Durante l'installazione di Azure AD Connect, viene registrata un'applicazione in cui sono disponibili degli attributi seguenti. È possibile vedere questa applicazione nel portale di Azure.  
![Schema estensione App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Questi attributi sono ora disponibili tramite grafico:  
![Grafico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Gli attributi sono preceduti estensione\_{AppClientId}\_. Il AppClientId ha lo stesso valore per tutti gli attributi nella directory di Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
