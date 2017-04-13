<properties
    pageTitle="Sincronizzazione di Azure AD Connect: informazioni sulle utenti e contatti | Microsoft Azure"
    description="Vengono descritti gli utenti e contatti di sincronizzazione di Azure AD Connect."
    services="active-directory"
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
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Sincronizzazione di Azure AD Connect: informazioni sulle utenti e contatti

Esistono diversi motivi diversi perché si dispone di più insiemi di strutture di Active Directory e sono disponibili diverse diverse topologie di distribuzione. Modelli comuni includono una distribuzione delle risorse account e strutture sync'ed elenco indirizzi globale dopo una fusione e acquisizione di immagini. Ma anche se sono disponibili modelli puri, ibrido sono i modelli comuni anche. La configurazione predefinita in sincronizzazione di Azure AD Connect non assume un modello specifico, ma a seconda di come utente corrispondente è stata selezionata nella Guida all'installazione, è possono osservare comportamenti diversi.

In questo argomento, verrà elaborata comportamento in determinati topologie la configurazione predefinita. Verranno esaminate tramite la configurazione e l'Editor delle regole sincronizzazione può essere utilizzato per verificare la configurazione.

Esistono alcune regole generali che prende in considerazione la configurazione:

- Indipendentemente dal fatto che ordine abbiamo importare dal sito di origine di Active Directory, il risultato finale deve essere sempre lo stesso.
- Un account attivo verrà sempre fornire le informazioni di accesso, tra cui **userPrincipalName** e **sourceAnchor**.
- Un account disattivato contribuiscono userPrincipalName e sourceAnchor, a meno che non è una cassetta postale collegata, se nessun account attivo da trovare.
- Un account con una cassetta postale collegata mai essere utilizzato per userPrincipalName e sourceAnchor. Si presuppone che un account attivo si trova in un secondo momento.
- Un oggetto contatto può effettuare il provisioning di Azure Active Directory come un contatto o un utente. Davvero non sa fino a quando non sono state elaborate tutte le strutture di Active Directory di origine.

## <a name="contacts"></a>Contatti

Disporre i contatti che rappresenta un utente in un'altra foresta è comuni dopo una fusione e acquisizione di immagini in una soluzione GALSync bridging di due o più strutture di Exchange. L'oggetto contatto è sempre aggiungersi dallo spazio connettore nel Metaverse utilizzando l'attributo di posta elettronica. Se è già presente un oggetto contatto o un oggetto utente con lo stesso indirizzo di posta elettronica, gli oggetti vengono uniti. Questo è configurato nella regola **In da Active Directory: contatto partecipare**. È inoltre disponibile una regola denominata **In da Active Directory: contatto comuni** con un flusso di attributo nel Metaverse attributo **tipooggettoorigine** con la costante **contatto**. Questa regola ha la precedenza molto bassa così se qualsiasi oggetto utente è stato aggiunto per lo stesso oggetto metaverse, quindi la regola **In da Active Directory: utente comuni** contribuisce valore utente a questo attributo. Con questa regola, questo attributo avrà il valore contatto se nessun utente partecipa e il valore all'utente se è stato trovato almeno un utente.

Per il provisioning di un oggetto di Azure Active Directory, la regola in uscita **a AAD-contattare partecipare** creerà un oggetto contatto se l' attributo metaverse **tipooggettoorigine** è impostato su **contatto**. Se l'attributo è impostato su **utente**, quindi la regola **a AAD: utente partecipa** creerà un oggetto utente invece.
È possibile che un oggetto è alzati di livello da contatto all'utente quando più origine Active Directory vengono importate e sincronizzate.

Ad esempio, in una topologia GALSync si troverà oggetti contatto per tutti gli utenti nella seconda foresta quando si importa prima foresta. Questa operazione verrà passaggio nuovi oggetti contatto nel connettore AAD. Quando è in un secondo momento, importare e sincronizzare la seconda foresta, verrà trovare utenti reali e unirli l'esistente agli oggetti presenti in. Verrà quindi eliminare l'oggetto contatto in AAD e invece di creare un nuovo oggetto utente.

Se si dispone di una topologia in cui gli utenti e rappresentati come contatti, assicurarsi di selezionare in modo che corrispondano utenti sull'attributo posta nella Guida all'installazione. Se si seleziona un'altra opzione, si avrà una configurazione dipendenti ordine. Oggetti contatto verranno sempre partecipare sull'attributo mail, ma gli oggetti utente verranno solo partecipare all'attributo mail se questa opzione è stata selezionata nella Guida all'installazione. Si potrebbe quindi finisce con due diversi oggetti nel metaverse con lo stesso attributo di posta elettronica se l'oggetto contatto importato prima dell'oggetto utente. Durante l'esportazione in Azure Active Directory, verrà generato un errore. Questo comportamento alla progettazione e indica dati non validi o che la topologia non sono stata identificata correttamente durante l'installazione.

## <a name="disabled-accounts"></a>Account disattivati

Account disabilitati vengono sincronizzati anche di Azure Active Directory. Account disabilitati sono comuni per rappresentare le risorse di Exchange, ad esempio delle sale riunioni. L'eccezione è gli utenti con una cassetta postale collegata; come descritto in precedenza, questi vengono mai effettuare il provisioning di un account da Azure Active Directory.

Il presupposto è che se viene rilevato un account utente disabilitato, quindi abbiamo non sarà possibile trovare un altro account attivo in un secondo momento e l'oggetto viene eseguito il provisioning di Azure Active Directory e userPrincipalName sourceAnchor trovato. In caso di un altro account attivo verrà partecipare allo stesso oggetto metaverse, verrà utilizzate l'attributo userPrincipalName e sourceAnchor.

## <a name="changing-sourceanchor"></a>Modifica sourceAnchor

Quando un oggetto è stato esportato in Azure Active Directory, quindi non è consentito modificare la sourceAnchor più. Quando l'oggetto è stata esportata l' attributo metaverse **cloudSourceAnchor** è impostato con il valore di **sourceAnchor** accettato da Azure Active Directory. Se **sourceAnchor** viene modificato e non corrisponde a **cloudSourceAnchor**, la regola **a AAD: utente partecipa** genererà errore **attributo sourceAnchor è stato modificato**. In questo caso, le configurazione o dati devono essere corrette in modo sourceAnchor stesso nel è presente il metaverse prima di sincronizzare nuovamente l'oggetto.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Azure Active Directory connettersi sincronizzazione: Le opzioni di personalizzazione di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
