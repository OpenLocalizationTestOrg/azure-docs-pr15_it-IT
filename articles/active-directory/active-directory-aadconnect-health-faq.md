<properties
    pageTitle="Azure AD Connect integrità domande frequenti"
    description="In questo argomento fornisce le risposte alle domande sull'integrità di connessione di Azure Active Directory. In questo argomento è descritta domande sull'utilizzo del servizio, inclusi il modello fatturazione, funzionalità, limitazioni e supporto tecnico."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect integrità frequenti domande frequenti

In questo argomento fornisce le risposte alle domande sull'integrità di connessione di Azure Active Directory. In questo argomento è descritta domande sull'utilizzo del servizio, inclusi il modello fatturazione, funzionalità, limitazioni e supporto tecnico.

## <a name="general-questions"></a>Domande di carattere generale



**D: si gestiscono più directory di Azure Active Directory. Come si passa a quello con Azure Active Directory Premium?**

È possibile passare da diversi tenant di Azure Active Directory, selezionare attualmente firmato nella casella nome utente in angolo superiore destro e scegliere l'account appropriato. Se l'account non è presente nell'elenco, fare clic su Disconnetti e quindi utilizzare le credenziali di amministratore globale della Directory con Azure Active Directory Premium abilitato ad accedere.

## <a name="installation-questions"></a>Domande sull'installazione



**D: qual è l'impatto dell'installazione di integrità agente connettersi Azure Active Directory sui singoli server?**

L'impatto dell'installazione di Microsoft Azure Active Directory connettersi integrità agenti ADFS Server Proxy di applicazione Web, server Azure AD Connect (sycn), controller di dominio è minima rispetto alla CPU, larghezza di banda consumo della memoria e spazio di archiviazione.

I numeri sono un'approssimazione.

- Utilizzo della CPU: aumentare ~ 1%
- Utilizzo della memoria: fino a 10% della memoria di sistema totale

>[AZURE.NOTE] In caso di agente non sarà in grado di comunicare su Azure, l'agente sono archiviati i dati in locale, fino a un limite massimo definito. L'agente sovrascriverà i dati "memorizzati nella cache" alla scala cronologica "meno di recente servita".

- Spazio di archiviazione di buffer locale di Azure Active Directory connettersi agente: ~ 20 MB
- Per i server ADFS, è consigliabile eseguire il provisioning uno spazio su disco di 1024 MB (1 GB) per il canale di controllo AD FS di agenti integrità connettersi Azure Active Directory per l'elaborazione di tutti i dati di controllo prima che vengano sovrascritti.

**D: è necessario riavviare il server durante l'installazione di integrità agenti connettersi Azure Active Directory?**

No. L'installazione di agenti non è necessario il riavvio del server. Tuttavia, l'installazione di alcune delle procedure prerequisite può richiedere il riavvio del server.

Ad esempio, in Windows Server 2008 R2 l'installazione di .net Framework 4.5 è necessario riavviare il server.


**D: indica Azure Active Directory connettersi integrità dei servizi funzionano tramite un proxy http pass-through?**

Sì.  Per in esecuzione operazioni, è possibile configurare l'agente di integrità per inoltrare le richieste http in uscita con un HTTP Proxy. Per ulteriori informazioni, vedere [configurare Azure Active Directory connettersi agenti integrità usare HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Se è necessario configurare un proxy durante la registrazione agente, potrebbe essere necessario modificare le impostazioni del Proxy di Internet Explorer in anticipo.
1. Aprire Internet Explorer -> Impostazioni -> Internet Opzioni -> connessioni -> Impostazioni LAN.
2. Selezionare Usa un Server Proxy per la rete LAN.
3. Se si dispone di porte proxy diversi per HTTP e HTTPS/sicura, selezionare Avanzate.

**D: Azure AD connettersi integrità dei servizi supportano l'autenticazione di base per la connessione al proxy Http?**

No. Un meccanismo per specificare il nome utente non autorizzata e la password per l'autenticazione di base non è attualmente supportato.


**D: quali versione di Active Directory sono supportati da Azure Active Directory connettersi integrità per Active Directory?**

Monitoraggio di dominio Active Directory è supportato durante l'installazione di versioni del sistema operativo seguenti:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Operazioni domande



**D: è necessario attivare il controllo il server Proxy di AD FS applicazione o il server Proxy di applicazioni Web?**

No, il controllo non è necessario attivare il server Proxy di applicazione Web (WAP). Abilitarla in server ADFS.


**D: come Azure Active Directory connettersi integrità avvisi risolvere?**

Azure Active Directory connettersi integrità avvisi risolvere una condizione di successo. Azure Active Directory connettersi agente rilevare e segnalare le condizioni di successo al servizio periodicamente. Per alcuni avvisi, l'eliminazione è basate sul tempo. In altre parole, se la stessa condizione di errore non è rispettata entro 72 ore dalla generazione degli avvisi, l'avviso verrà risolto automaticamente.




**D: quali porte del firewall necessari aprire l'Azure Active Directory connettersi Agente integrità per l'uso**

È necessario che le porte TCP/UDP 443 e 5671 aperto per la Azure Active Directory connettersi Agente integrità la possibilità di comunicare con gli endpoint del servizio di integrità di Azure Active Directory.


**D: perché visibile due server con lo stesso nome nello stato portale connettersi Azure Active Directory**

Quando si rimuove un agente da un server, il server non vengono rimosse automaticamente dal portale di connessione di Azure Active Directory.  Se si rimosso un agente da un server o rimosso manualmente il server, è necessario eliminare manualmente la voce del server dal portale di Azure Active Directory connettersi integrità. Per ulteriori informazioni, passare a [eliminare un'istanza di server o il servizio.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Se si ricostruire l'immagine di un server o creare un nuovo server con gli stessi dettagli (ad esempio nome del computer) e non è stata rimossa server già registrato dal portale di Azure Active Directory connettersi integrità, installato l'agente nel nuovo server possono essere presenti due voci con lo stesso nome.  
In questo caso, è necessario eliminare la voce che appartengono al server precedente manualmente. I dati per il server devono essere non aggiornati.

## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect dello stato dell'installazione dell'agente](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operazioni integrità](active-directory-aadconnect-health-operations.md)
* [Collegare mediante Azure Active Directory integrità con ADFS](active-directory-aadconnect-health-adfs.md)
* [Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Collegare mediante Azure Active Directory integrità con Active Directory](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
