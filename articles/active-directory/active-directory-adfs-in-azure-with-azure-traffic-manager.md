<properties
    pageTitle="Distribuzione di ADFS disponibilità Active Directory tra geografici in Azure con Azure il traffico Manager | Microsoft Azure"
    description="In questo documento si imparerà distribuire ADFS in Azure per ad alta disponibilità."
    keywords="ADFS con gestore del traffico Azure, adfs con Azure il traffico di gestione geografici e con più Data Center, centri dati geografici, multipli geografica Data Center, distribuire ADFS in azure, distribuire adfs azure, adfs azure, ADFS azure, distribuire adfs, distribuzione di ADFS, adfs in azure, distribuire adfs in azure, distribuire ADFS azure, adfs azure, introduzione all'ADFS, Azure, ADFS in Azure, iaas , ADFS, spostare adfs azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Distribuzione di ADFS disponibilità Active Directory tra geografici in Azure con Azure il traffico Manager

[Distribuzione di ADFS in Azure](active-directory-aadconnect-azure-adfs.md) fornisce istruzioni dettagliate per come è possibile distribuire un'infrastruttura ADFS semplice per l'organizzazione in Azure. Questo articolo offre la procedura seguente per creare una distribuzione di ADFS tra geografici in Azure con [Azure il traffico Manager](../traffic-manager/traffic-manager-overview.md). Gestore del traffico Azure consente di creare un'infrastruttura di ADFS di prestazioni elevate per l'organizzazione e aree geografiche diffusione disponibilità effettuando utilizzo dell'intervallo dei metodi di routing disponibili in base alle proprie esigenze diverse dall'infrastruttura.

Consente di un'infrastruttura ADFS tra geografici disponibilità:

* **Eliminazione dei singoli punti di errore:** Con failover le funzionalità di gestione di traffico di Azure, è possibile ottenere un'infrastruttura ADFS disponibilità quando una delle aree di dati in una parte del mondo fuoriesce verso il basso
* **Miglioramento delle prestazioni:** È possibile utilizzare la distribuzione suggerita in questo articolo per fornire un'infrastruttura di ADFS prestazioni elevate che può aiutare gli utenti autenticati più rapidamente. 

##<a name="design-principles"></a>Principi di progettazione

![Progettazione complessiva](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Principi di progettazione di base sarà stessi come indicato nella principi di progettazione, vedere l'articolo distribuzione di ADFS in Azure. Il diagramma precedente mostra una semplice estensione della distribuzione di base per un'altra area geografica di appartenenza. Di seguito sono alcuni aspetti da considerare quando si estende la distribuzione a una nuova area geografica

* **Rete virtuale:** È necessario creare una nuova rete virtuale nell'area geografica che si desidera distribuire l'infrastruttura di ADFS aggiuntiva. Nella figura precedente Geo1 VNET e Geo2 VNET viene visualizzato come due reti virtuali in ogni area geografica di appartenenza.

* **Controller di dominio e i server ADFS nuovo VNET geografica:** È consigliabile distribuire dominio controller nella nuova area geografica in modo che non è necessario contattare un controller di dominio in un'altra lontano server ADFS nella nuova area di rete per completare l'autenticazione e migliorando così le prestazioni.

* **Gli account di archiviazione:** Gli account di archiviazione sono associati a un'area. Poiché si distribuire macchine in una nuova area geografica, sarà necessario creare nuovi account di archiviazione da utilizzare nell'area.  

* **Gruppi di sicurezza di rete:** Come gli account di archiviazione, gruppi di sicurezza di rete creato in un'area non possono essere utilizzati in un'altra area geografica di appartenenza. Di conseguenza, sarà necessario creare nuova rete gruppi di sicurezza simili a quelli presenti nell'area geografica prima per subnet INT e di rete Perimetrale nella nuova area geografica.

* **DNS etichette per indirizzi IP pubblici:** Gestore del traffico Azure può fare riferimento a endpoint solo tramite etichette DNS. Di conseguenza, è necessario creare etichette DNS per gli indirizzi IP pubblici degli bilanciamento carico esterni.

* **Gestore del traffico azure:** Gestione il traffico di Microsoft Azure consente di controllare la distribuzione del traffico utenti per l'endpoint del servizio esecuzione nei data center diversi in tutto il mondo. Gestore del traffico Azure funziona a livello di DNS. Utilizza le risposte DNS per indirizzare il traffico degli utenti finali a globalmente distribuiti i punti finali. Client quindi connettersi a tali endpoint direttamente. Diverse opzioni di routing di prestazioni, Weighted e priorità, è possibile scegliere facilmente instradamento più adatto alle proprie esigenze dell'organizzazione. 

* **La connettività di rete V a V-net tra due aree:** Non è necessario disporre di una connessione tra le reti virtuali stesso. Poiché ogni virtuali ha accesso a controller di dominio e ha server ADFS e WAP sé, funzionano senza una connessione tra le reti virtuali in diverse aree geografiche. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Procedura per integrare Azure il traffico Manager

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Distribuzione di ADFS nella nuova area geografica
Seguire i passaggi e le istruzioni nella [distribuzione di ADFS in Azure](active-directory-aadconnect-azure-adfs.md) per distribuire la stessa topologia nella nuova area geografica.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etichette DNS per gli indirizzi IP pubblici di bilanciamento Internet affiancate (pubblico)
Come detto in precedenza, il responsabile di traffico Azure può solo fare riferimento alle etichette DNS come endpoint e pertanto è importante creare etichette DNS per gli indirizzi IP pubblici degli bilanciamento carico esterni. Sotto screenshot che mostra come è possibile configurare l'etichetta DNS per l'indirizzo IP pubblico. 

![Etichetta DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Distribuzione di Azure il traffico Manager

Seguire la procedura seguente per creare un profilo di gestione del traffico. Per ulteriori informazioni, è anche possibile fare riferimento a [gestire un profilo di gestore del traffico Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Creare un profilo di gestore del traffico:** Assegnare un nome univoco del profilo di manager il traffico. Il nome del profilo fa parte del nome del DNS e funge da un prefisso per l'etichetta di nome di dominio gestore del traffico. Il nome / prefisso viene aggiunto a. trafficmanager.net per creare un'etichetta DNS per il traffico manager. Nella schermata seguente mostra il responsabile di traffico prefisso DNS da impostare come mysts ed etichetta DNS risultante sarà mysts.trafficmanager.net. 

    ![Creazione di un profilo gestore del traffico](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Traffico routing metodo:** Sono disponibili tre opzioni di distribuzione in gestore del traffico:

    * Priorità 
    * Prestazioni
    * Media ponderata
    
    **Prestazioni** è consigliabile per ottenere tempestivo infrastruttura ADFS. Tuttavia, è possibile scegliere qualsiasi routing metodo più adatto alle proprie esigenze di distribuzione. La funzionalità di ADFS non subisca selezionata l'opzione routing. Per ulteriori informazioni, vedere [metodi di routing il traffico di gestore del traffico](../traffic-manager/traffic-manager-routing-methods.md) . Nell'esempio di schermata sopra, è possibile visualizzare il metodo di **prestazioni** selezionato.
   
3.  **Configurare endpoint:** Nella pagina Gestione il traffico, fare clic sui punti finali e selezionare Aggiungi. Verrà aperta una pagina di endpoint Aggiungi simile alla schermata seguente
 
    ![Configurare i punti finali](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Per diversi input, seguire la procedura riportata di seguito:

    **Tipo:** Selezionare l'endpoint Azure come abbiamo verrà punta a un indirizzo IP pubblico Azure.

    **Nome:** Creare un nome che si desidera associare il punto finale. Questo non è il nome del DNS e si ha alcun effetto sui record DNS.

    **Tipo di risorsa di destinazione:** Selezionare indirizzo IP pubblico come valore a questa proprietà. 

    **Risorse di destinazione:** Fornirà un'opzione per scegliere le varie etichette DNS che è disponibile in abbonamento. Scegliere l'etichetta DNS per a.

    Aggiungere endpoint per ogni area geografica desiderata il responsabile di traffico Azure per instradare il traffico a.
    Per ulteriori informazioni e istruzioni dettagliate su come aggiungere o configurare endpoint nel gestore del traffico, consultare [aggiungere, disattivare, attivare o eliminare i punti finali](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Configura verifica:** Nella pagina Gestione il traffico, fare clic su configurazione. Nella pagina di configurazione, è necessario modificare le impostazioni di monitoraggio per verificare la presenza in HTTP porte 80 e percorso relativo /adfs/probe

    ![Configurare sondaggio](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Assicurarsi che lo stato dei punti finali sia ONLINE dopo aver completata la configurazione**. Se tutti gli endpoint ' danneggiato ', gestore del traffico Azure produrrà un tentativo di procedure per instradare il traffico presupponendo che i test di diagnostica non è corretta e tutti gli endpoint risultino.

5. **i record DNS di modifica per Azure il traffico Manager:** Il servizio di federazione deve essere un record CNAME per il nome di Azure il traffico di gestione DNS. Creare un record CNAME nei record DNS pubblico in modo che chiunque sta tentando di contattare il servizio federativo raggiunga effettivamente la gestione di traffico Azure.

    Ad esempio, per indirizzare il fs.fabidentity.com servizio federazione al gestore del traffico, sarebbe necessario aggiornare il record di risorse DNS per i seguenti:

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Verificare il routing e accesso di ADFS   

###<a name="routing-test"></a>Test di routing

Un test di base per il routing, è possibile provare a effettuare il ping il nome DNS del servizio di federazione da un computer in ogni area geografica. A seconda del metodo routing scelto, l'endpoint che effettivamente il ping verrà eseguite anche la visualizzazione di ping. Ad esempio, se è stato selezionato il routing delle prestazioni, quindi il punto finale più vicino al paese del client scade. Di seguito è snapshot di due ping da due computer client area diversa, uno nell'area asiatico e uno negli Stati Uniti ovest. 

![Test di routing](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS accesso test

Il modo più semplice per verificare ADFS è tramite la pagina IdpInitiatedSignon.aspx. Per poter eseguire che, è necessario per abilitare IdpInitiatedSignOn proprietà ADFS. Eseguire la procedura seguente per verificare la configurazione di ADFS
 
1. Eseguire il seguente cmdlet nel server ADFS, tramite PowerShell, per attivarla. Set AdfsProperties - EnableIdPInitiatedSignonPage $true
2. Da qualsiasi https:// accesso esterno computer<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Verrà visualizzata la pagina di ADFS come il seguente:

    ![Test di ADFS - richiesta di autenticazione](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    e ha esito positivo accesso in, fornirà con un messaggio di conferma come illustrato di seguito:

    ![Test di ADFS - successo di autenticazione](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Collegamenti correlati
* [Distribuzione di ADFS base in Azure](active-directory-aadconnect-azure-adfs.md)
* [Gestore del traffico Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Metodi di routing il traffico di gestore del traffico](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Passaggi successivi
* [Gestire un profilo di gestore del traffico Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Aggiungere, disattivare, attivare o eliminare i punti finali](../traffic-manager/traffic-manager-endpoints.md) 

