<properties
    pageTitle="Active Directory Federation Services in Azure | Microsoft Azure"
    description="In questo documento si imparerà distribuire ADFS in Azure per ad alta disponibilità."
    keywords="distribuzione di ADFS in azure, distribuire adfs azure, adfs azure, ADFS azure, distribuire adfs, distribuzione di ADFS, adfs in azure, distribuire adfs in azure, distribuzione di ADFS azure, adfs azure, introduzione all'ADFS, Azure, ADFS in Azure, iaas, ADFS, sposta adfs in azure"
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
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Distribuzione di ADFS Active Directory in Azure 

ADFS offre la federazione delle identità semplificata, protetta e funzionalità Web single sign-on (SSO). La federazione con Azure Active Directory o Office 365 consente agli utenti di eseguire l'autenticazione tramite le credenziali locali e accedere a tutte le risorse nel cloud. Di conseguenza, diventa importante avere un'infrastruttura di ADFS disponibilità per garantire l'accesso alle risorse sia in locale e nel cloud. Distribuzione di ADFS in Azure consente di disponibilità elevata obbligatorio con impegno minimo.
Esistono diversi vantaggi della distribuzione di ADFS in Azure, alcune di esse sono elencate di seguito:

* **Disponibilità** - con la potenza di set di disponibilità di Azure, garantisce un'infrastruttura di disponibilità.
* **Facile scala** – necessario prestazioni più elevate? Migrare facilmente al computer più potenti per pochi clic di Azure
* **Croce geografico ridondanza** con Azure geografico ridondanza, è possibile accertarsi che l'infrastruttura è altamente disponibile in tutto il mondo
* **Facile gestione** -con le opzioni di gestione altamente semplificata nel portale di Azure, la gestione dell'infrastruttura è molto semplice e chiara 

## <a name="design-principles"></a>Principi di progettazione

![Progettazione di distribuzione](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Il diagramma precedente mostra la topologia di base consigliata per avviare l'installazione dell'infrastruttura di ADFS in Azure. Principi dietro i vari componenti della topologia sono elencati di seguito:

* **Controller di dominio / server ADFS**: se si hanno meno di 1.000 utenti è possibile installare semplicemente ruolo ADFS nei controller di dominio. Se non si desidera qualsiasi impatto sulle prestazioni controller di dominio o se si dispone di più di 1.000 utenti, quindi distribuire ADFS su server separati.
* **Server WAP** – è necessario implementare i server Proxy di applicazione Web, in modo che gli utenti possono raggiungere l'ADFS quando non sono nella rete della società anche.
* **Rete Perimetrale**: server di Proxy di applicazione Web verranno inseriti nella rete Perimetrale e l'accesso solo TCP/443 è consentito tra la rete Perimetrale e subnet interna.
* **Servizi di bilanciamento del carico**: per garantire disponibilità dei server ADFS e Proxy di applicazione Web, è consigliabile utilizzare un servizio di bilanciamento del carico interno per server ADFS e servizio di bilanciamento del carico Azure per i server Proxy di applicazione Web.
* **Set di disponibilità**: per garantire la ridondanza per la distribuzione di ADFS, si consiglia di raggruppare due o più macchine virtuali in un Set di disponibilità per carichi di lavoro simili. Questa configurazione garantisce che durante un evento di manutenzione pianificata o non pianificato, almeno una macchina virtuale saranno disponibile
* **Gli account di archiviazione**: è consigliabile utilizzare due account di archiviazione. Avere un account di archiviazione singola può comportare la creazione di un singolo punto di errore e possono causare la distribuzione diventi disponibile in uno scenario poco probabile nel punto in cui l'account di archiviazione non è raggiungibile. Due account di archiviazione consentirà di associazione di un account di archiviazione per ogni riga di errore.
* **Separazione di rete**: Server Proxy di applicazione Web devono essere distribuiti in una rete di rete Perimetrale separata. È possibile dividere una rete virtuale in due subnet e quindi distribuire i server Proxy di applicazione Web in una subnet isolato. È sufficiente, è possibile configurare le impostazioni di sicurezza di rete per ogni subnet e consentire solo necessari comunicazioni tra le due subnet. Per uno scenario di distribuzione riportata di seguito vengono fornite ulteriori informazioni

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Procedura per la distribuzione di ADFS in Azure

I passaggi indicati in questa sezione strutturare la Guida per la distribuzione di sotto dell'infrastruttura ADFS rappresenta in Azure.

### <a name="1-deploying-the-network"></a>1. distribuzione di rete

Come descritto in precedenza, è possibile non creare due subnet in una singola rete virtuale oppure creare due reti virtuali completamente diverse (VNet). In questo articolo verrà focalizzare l'attenzione sulla distribuzione di una singola rete virtuale e dividere in due subnet. Questo è un approccio più semplice come due VNets separata richiedono VNet al gateway VNet per le comunicazioni.

**1.1 creare virtuali**

![Creare virtuali](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
Nel portale di Azure, selezionare virtuali ed è possibile distribuire la rete virtuale e una subnet immediatamente con un solo clic. INT subnet inoltre definiti ed è ora pronto per essere macchine virtuali da aggiungere.
Il passaggio successivo consiste nell'aggiungere un'altra subnet alla rete, vale a dire alla subnet di rete Perimetrale. Per creare la subnet di rete Perimetrale, è sufficiente

* Selezionare la rete appena creata
* Nella finestra delle proprietà selezionare subnet
* Nella subnet pannello fare clic sul pulsante Aggiungi
* Specificare le informazioni di spazio nome e indirizzo subnet per creare la subnet

![Subnet](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Subnet rete Perimetrale](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. creazione della rete di gruppi di sicurezza**

Un gruppo di sicurezza di rete (NSG) contiene un elenco delle regole di controllo elenco di accesso che consentire o meno il traffico di rete per le istanze di macchine Virtuali in una rete virtuale. NSGs può essere associato subnet o singole istanze di macchine Virtuali all'interno di tale subnet. Quando un NSG è associata a una subnet, vengono applicate le regole ACL a tutte le istanze di macchine Virtuali di tale subnet.
Allo scopo di questa Guida, sarà necessario creare due NSGs: uno per una rete interna e una rete Perimetrale. Si sarà NSG_INT e NSG_DMZ rispettivamente.

![Creare NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Dopo aver creato il NSG, si verificherà 0 regole in entrata e 0 in uscita. Dopo aver installato e funzionante ruoli sui server locali, le regole in entrata e in uscita possono essere eseguite in base a livello di protezione desiderato.

![Inizializzare NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Dopo aver creato il NSGs, associare subnet NSG_INT INT e NSG_DMZ con subnet rete Perimetrale. Una schermata di esempio è indicato di seguito:

![Configurare NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Fare clic su subnet per aprire il pannello per subnet
* Selezionare la subnet da associare il NSG 

Dopo la configurazione, il riquadro per subnet dovrebbe risultare analoga alla seguente:

![Subnet dopo NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. creare connessione locale**

È necessario stabilire una connessione a locale per distribuire il dominio controller di in azure. Azure offre diverse opzioni di integrazione applicativa per connettersi infrastruttura locale a infrastruttura di Azure.

* Punto al sito
* Virtuali di siti
* ExpressRoute

È consigliabile utilizzare ExpressRoute. ExpressRoute consente di creare connessioni private tra Azure Data Center e infrastruttura che non è in locale o in un ambiente di posizione condivisa. Connessioni ExpressRoute va tramite Internet. Offrono ulteriori l'affidabilità, velocità, latenza inferiore e una maggiore sicurezza rispetto alle connessioni tipiche tramite Internet.
Mentre si consiglia di utilizzare ExpressRoute, è possibile scegliere un metodo di connessione più adatto per l'organizzazione. Per ulteriori informazioni sul ExpressRoute e le varie opzioni di integrazione applicativa utilizzando ExpressRoute, leggere [Panoramica tecnica ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. creare gli account di archiviazione

Per mantenere disponibilità ed evitare dipendenza da un account di archiviazione singola, è possibile creare due account di archiviazione. Dividere i computer in ogni set di disponibilità in due gruppi e quindi assegnare un account di archiviazione separata ogni gruppo.

![Creare gli account di archiviazione](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. creare set di disponibilità

Per ogni ruolo (controller di dominio/ADFS e WAP), creare set di disponibilità contenenti 2 ogni macchina minimo. In questo modo raggiungere una maggiore disponibilità per ogni ruolo. Durante la creazione della disponibilità di set, è essenziale per stabilire le operazioni seguenti:
* **Errore di domini**: macchine virtuali nello stesso dominio guasto condividere gli stessi power origine e cambia di rete. È consigliabile utilizzare un minimo di 2 domini guasto. Il valore predefinito è 3 ed è possibile lasciarla invariata per la distribuzione
* **Aggiornare i domini**: computer che appartengono allo stesso dominio di aggiornamento, è necessario riavviare insieme durante un aggiornamento. Si vuole avere minima di 2 domini di aggiornamento. Il valore predefinito è 5 ed è possibile lasciarla invariata per la distribuzione

![Set di disponibilità](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Creare i seguenti set di disponibilità

| Set di disponibilità | Ruolo | Domini di guasto | Aggiornare i domini |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | CC/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. distribuire macchine virtuali
Il passaggio successivo consiste nel distribuire macchine virtuali che ospita i ruoli diversi dell'infrastruttura. In ogni set di disponibilità sono consigliati almeno due computer. Creare sei macchine virtuali per la distribuzione di base.

| Computer | Ruolo | Subnet | Set di disponibilità | Account di archiviazione | Indirizzo IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|CC/ADFS|INT|contosodcset|contososac1|Statica|
|contosodc2|CC/ADFS|INT|contosodcset|contososac2|Statica|
|contosowap1|WAP|RETE PERIMETRALE|contosowapset|contososac1|Statica|
|contosowap2|WAP|RETE PERIMETRALE|contosowapset|contososac2|Statica|

Come si può notare, NSG non è stata specificata. In questo modo azure consente di utilizzare NSG livello di subnet. Quindi, è possibile controllare il traffico di rete di computer utilizzando NSG singoli associato non subnet oppure l'oggetto NIC. Per saperne di più sui [che cos'è un gruppo di sicurezza di rete (NSG)](https://aka.ms/Azure/NSG).
Se si gestiscono il sistema DNS, è consigliabile indirizzo IP statico. È possibile utilizzare DNS Azure e invece nei record DNS per il dominio, fare riferimento ai computer nuovo per i nomi di Azure.
Il riquadro macchina virtuale dovrebbe essere simile di sotto al termine della distribuzione:

![Macchine virtuali di distribuzione](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. configurazione del controller di dominio / server ADFS
 Per l'autenticazione le richieste in arrivo, ADFS sarà necessario contattare il controller di dominio. Per salvare i costi di andata e ritorno da Azure Data Center locale per l'autenticazione, è consigliabile per distribuire una replica del controller di dominio in Azure. Per raggiungere disponibilità, è consigliabile creare un set di disponibilità minima 2 controller di dominio.

|Controller di dominio|Ruolo|Account di archiviazione|
|:-----:|:-----:|:-----:|
|contosodc1|Replica|contososac1|
|contosodc2|Replica|contososac2|

* Alzare di livello i due server come controller di dominio duplicata con DNS
* Configurare i server ADFS installando il ruolo di ADFS utilizzando server manager.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. distribuzione di bilanciamento del carico interno (ILB)

**6.1. creare la ILB**

Per distribuire un ILB, selezionare servizi di bilanciamento del carico nel portale di Azure e fare clic su Aggiungi (+).
>[AZURE.NOTE] Se non è presente **Servizi di bilanciamento del carico** nel menu, fare clic su **Sfoglia** nella parte inferiore sinistra del portale e scorrere fino a visualizzare **Servizi di bilanciamento del carico**.  Fare clic sulla stella gialla per aggiungerlo al menu. Ora selezionare l'icona di bilanciamento carico nuova per aprire il pannello per iniziare la configurazione di bilanciamento del carico.

![Passare di bilanciamento del carico](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nome**: assegnare un nome appropriato per il servizio di bilanciamento del carico
* **Combinazione**: poiché il servizio di bilanciamento del carico verrà inserito davanti server ADFS ed è utilizzata per le connessioni di rete interna solo, selezionare "Interni"
* **Virtuali**: scegliere la rete virtuale in cui si distribuisce l'ADFS
* **Subnet**: scegliere la subnet di interna
* **Assegnazione di indirizzi IP**: dinamico

![Servizio di bilanciamento del carico interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Dopo aver fatto clic creazione e la ILB viene distribuito, verrà visualizzato nell'elenco dei servizi di bilanciamento del carico:

![Servizi di bilanciamento del carico dopo ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Passaggio successivo consiste nel configurare il pool di back-end e la ricerca di back-end.

**6.2. configurare pool di back-end ILB**

Selezionare ILB appena creato nel Pannello di bilanciamento. Verrà aperto il pannello di impostazioni. 
1.  Selezionare il pool di back-end dal Pannello di impostazioni
2.  Nel riquadro Aggiungi del pool back-end, fare clic su computer virtuali Aggiungi
3.  Verrà visualizzato con un riquadro in cui è possibile scegliere set di disponibilità
4.  Scegliere l'insieme di disponibilità di ADFS

![Configurare il pool di back-end ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. configurazione ricerca**

Nel Pannello di impostazioni ILB selezionare le ricerche.
1.  Fare clic su Aggiungi
2.  Fornire dettagli per sondaggio un. **Nome**: verifica nome b. **Protocollo**: TCP c. **Porta**: 443 (HTTPS) d. **Intervallo**: 5 (valore predefinito): questa è l'intervallo in cui ILB verranno cercati i computer nel pool di back-end e. **Soglia non corretti**: 2 (impostazione predefinita val ue) – si tratta la soglia di errori consecutivi verifica trascorso il quale ILB verrà dichiarato una macchina nel pool di back-end non risponde e inviare il traffico interrompere.

![Configurare verifica ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. creare regole di bilanciamento del carico**

In modo da bilanciare in modo efficace il traffico, il ILB deve essere configurato con le regole di bilanciamento del carico. Per creare una regola di bilanciamento del carico 
1.  Selezionare il bilanciamento del carico regola dal Pannello di impostazioni del ILB
2.  Fare clic su Aggiungi nel Pannello di regola di bilanciamento del carico
3.  Nel Pannello di regola di bilanciamento del carico Aggiungi una. **Nome**: specificare un nome per la regola b. **Protocollo**: selezionare TCP c. **Porta**: 443 d. **Porta di back-end**: 443 e. **Pool di back-end**: selezionare il pool creato per AD FS cluster f precedenti. **Verifica**: selezionare il misuratore creato in precedenza per server ADFS

![Configurare ILB bilanciamento del carico regole](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. aggiornare il DNS con ILB**

Passare al server DNS e creare un record CNAME per il ILB. Il record CNAME deve essere per il servizio federativo con l'indirizzo IP che punta all'indirizzo IP del ILB. Ad esempio se l'indirizzo DIP ILB 10.3.0.8 e il servizio federativo installato è fs.contoso.com, quindi si crea un record CNAME per fs.contoso.com che puntano alle 10.3.0.8.
In questo modo che tutte le comunicazioni relative alle fs.contoso.com fine fino alla ILB e vengono inviate in modo appropriato.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. la configurazione del server Proxy di applicazione Web

**7.1. la configurazione dei server di Proxy di applicazione Web per raggiungere il server ADFS**

Per garantire che il server Proxy di applicazione Web siano in grado di raggiungere il server ADFS dietro la ILB, creare un record nel %systemroot%\system32\drivers\etc\hosts per il ILB. Si noti che il nome distinto distinto deve essere il nome del servizio di federazione, ad esempio fs.contoso.com. E la voce IP deve essere che dell'indirizzo IP del ILB (10.3.0.8 come nell'esempio).

**7.2. installare il ruolo di Proxy di applicazione Web**

Dopo aver verificato che il server Proxy di applicazione Web siano in grado di raggiungere il server ADFS dietro ILB, è possibile installare successivo server Proxy di applicazione Web. Server Proxy di applicazione Web non aggiunto al dominio. Installare i ruoli di Proxy di applicazioni Web nei server Proxy di applicazione Web due selezionando il ruolo di accesso remoto. Server manager consentirà per completare l'installazione di WAP.
Per ulteriori informazioni su come distribuire WAP, vedere [installare e configurare il Server Proxy di applicazione Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. distribuzione Internet affiancate di bilanciamento del carico (pubblico)

**8.1. creare Internet affiancate di bilanciamento del carico (pubblico)**
 
Nel portale di Azure, selezionare servizi di bilanciamento del carico e quindi fare clic su Aggiungi. Nel Pannello di bilanciamento carico crea, immettere le informazioni seguenti
1. **Nome**: nome per il servizio di bilanciamento del carico
2. **Combinazione**: pubblico: questa opzione indica Azure che questo servizio di bilanciamento del carico sarà necessario un indirizzo pubblico.
3. **Indirizzo IP**: creare un nuovo indirizzo IP (dinamico)

![Bilanciamento del carico esposto Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Dopo la distribuzione, bilanciamento del carico verrà visualizzato nell'elenco di bilanciamento del carico di carico.

![Elenco di bilanciamento carico](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. assegnare un'etichetta DNS per l'indirizzo IP pubblico**

Fare clic sulla voce di bilanciamento carico appena creato nel Pannello di bilanciamento del carico di carico per visualizzare il riquadro per la configurazione. Di seguito sono riportati i passaggi per configurare l'etichetta DNS per l'indirizzo IP pubblico:
1.  Fare clic su indirizzo IP pubblico. Verrà aperto il pannello per l'indirizzo IP pubblico e le relative impostazioni
2.  Fare clic su configurazione
3.  Specificare l'etichetta DNS. Diventerà l'etichetta DNS pubblico che è possibile accedere da qualsiasi luogo, ad esempio contosofs.westus.cloudapp.azure.com. È possibile aggiungere una voce nel sistema DNS esterno per il servizio federativo (ad esempio fs.contoso.com) che restituisce l'etichetta DNS del sistema di bilanciamento del carico esterno (contosofs.westus.cloudapp.azure.com).

![Configurazione di bilanciamento del carico è connessa a internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurare internet affiancate di bilanciamento del carico (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. configurare back-end pool di bilanciamento del carico Internet affiancate (pubblico)** 

Eseguire la stessa procedura come la creazione di sistema di bilanciamento del carico interno per configurare il pool di back-end per Internet affiancate (pubblico) bilanciamento del carico come la disponibilità impostare per il server WAP. Ad esempio contosowapset.

![Configurare back-end pool di Internet affiancate di bilanciamento del carico](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. configurare sondaggio**

Eseguire la stessa procedura come la configurazione di bilanciamento interno per configurare la ricerca per il pool di back-end dei server WAP.

![Configurare la ricerca di Internet affiancate di bilanciamento del carico](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8,5. creare regole di bilanciamento del carico**

Eseguire la stessa procedura come ILB per configurare il bilanciamento del carico regola per TCP 443.

![Configurare le regole di bilanciamento del carico di Internet affiancate di bilanciamento del carico](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. protezione della rete

**9.1. protezione subnet interna**

In generale, è necessario le regole seguenti per proteggere in modo efficiente subnet interna (in ordine come indicato di seguito)

|Regola|Descrizione|Flusso|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Consentire la comunicazione HTTPS dalla rete Perimetrale | In ingresso |
|DenyInternetOutbound| Nessun accesso a internet | In uscita |

![Le regole di accesso INT (in ingresso)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[commento]: <> (![le regole di accesso INT (in ingresso)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [commento]: <> (![le regole di accesso INT (in uscita)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. protezione subnet rete Perimetrale**

|Regola|Descrizione|Flusso|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Consentire HTTPS da internet per la rete Perimetrale | In ingresso|
|DenyInternetOutbound|  Diverso da HTTPS a internet è bloccato | In uscita |

![Le regole di accesso est (in ingresso)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[commento]: <> (![le regole di accesso est (in ingresso)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [commento]: <> (![le regole di accesso est (in uscita)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Se l'utente del client certificato di autenticazione (autenticazione clientTLS utilizzando X509 certificati utente) è necessaria, quindi ADFS richiede TCP 49443 porta abilitare l'accesso in ingresso.

###<a name="10--test-the-ad-fs-sign-in"></a>10. test accesso-in ADFS

Il modo più semplice è testare ADFS tramite la pagina IdpInitiatedSignon.aspx. Per poter eseguire che, è necessario per abilitare IdpInitiatedSignOn proprietà ADFS. Eseguire la procedura seguente per verificare la configurazione di ADFS
1.  Eseguire il seguente cmdlet nel server ADFS, tramite PowerShell, per attivarla.
    Set AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  Da qualsiasi https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx accesso computer esterni  
3.  Verrà visualizzata la pagina di ADFS come il seguente:

![Pagina di accesso di prova](./media/active-directory-aadconnect-azure-adfs/test1.png)

Completato accesso in, fornirà con un messaggio di conferma come illustrato di seguito:

![Test successo](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modello per la distribuzione di ADFS in Azure

Il modello distribuisce una configurazione 6 automatica, 2 per controller di dominio, ADFS e WAP.

[ADFS nel modello di distribuzione di Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

È possibile utilizzare una rete virtuale esistente o creare un nuovo VNET durante la distribuzione di questo modello. I vari parametri disponibili per la personalizzazione di distribuzione sono elencati di seguito con la descrizione di utilizzo del parametro nel processo di distribuzione. 

| Parametro | Descrizione |
|:--------|:-----|
|Posizione| Area per distribuire le risorse in, ad esempio est CONTATTACI. |
|StorageAccountType| Il tipo di Account di archiviazione creati|
|VirtualNetworkUsage| Indica se una nuova rete virtuale verrà creato o utilizzarne una esistente|
|VirtualNetworkName| Il nome della rete virtuale creare obbligatoria nei nuovi o esistenti l'uso di rete virtuale|
|VirtualNetworkResourceGroupName| Specifica il nome del gruppo di risorse in cui si trova la rete virtuale esistente. Quando si utilizza una rete virtuale esistente, questo nome diventa un parametro obbligatorio per consentire la distribuzione di trovare l'ID della rete virtuale esistente|
|VirtualNetworkAddressRange| L'intervallo di indirizzi di nuovo VNET, obbligatorio se creare una nuova rete virtuale|
|InternalSubnetName| Il nome della subnet interno obbligatorio in entrambe le opzioni l'uso di rete virtuale (nuove o esistente)|
|InternalSubnetAddressRange| L'intervallo di indirizzi della subnet interna che contiene i server controller di dominio e ADFS obbligatori se creare una nuova rete virtuale.|
|DMZSubnetAddressRange| L'intervallo di indirizzi della subnet rete perimetrale, che contiene i Windows applicazione server proxy, obbligatori se creare una nuova rete virtuale.|
|DMZSubnetName| Il nome della subnet interno obbligatorio in entrambe le opzioni l'uso di rete virtuale (nuove o esistente). |
|ADDC01NICIPAddress| L'indirizzo IP interno del primo Controller di dominio, questo indirizzo IP statico assegnato a controller di dominio e deve essere un indirizzo ip valido all'interno della subnet interno|
|ADDC02NICIPAddress| L'indirizzo IP interno del secondo Controller di dominio, questo indirizzo IP statico assegnato a controller di dominio e deve essere un indirizzo ip valido all'interno della subnet interno|
|ADFS01NICIPAddress| L'indirizzo IP interno del primo server ADFS, questo indirizzo IP statico verrà assegnato al server ADFS e deve essere un indirizzo ip valido all'interno della subnet interno|
|ADFS02NICIPAddress| L'indirizzo IP interno del secondo server ADFS, questo indirizzo IP statico verrà assegnato al server ADFS e deve essere un indirizzo ip valido all'interno della subnet interno|
|WAP01NICIPAddress| L'indirizzo IP interno del primo server WAP, questo indirizzo IP statico verrà assegnato al server WAP e deve essere un indirizzo ip valido all'interno della subnet di rete Perimetrale|
|WAP02NICIPAddress| L'indirizzo IP interno del secondo server WAP, questo indirizzo IP statico verrà assegnato al server WAP e deve essere un indirizzo ip valido all'interno della subnet di rete Perimetrale|
|ADFSLoadBalancerPrivateIPAddress| Indirizzo IP interno del servizio di bilanciamento del carico ADFS, questo indirizzo IP statico assegnato a bilanciamento del carico e deve essere un indirizzo ip valido all'interno della subnet interno|
|ADDCVMNamePrefix| Prefisso del nome macchina virtuale per controller di dominio|
|ADFSVMNamePrefix| Prefisso del nome macchina virtuale per server ADFS|
|WAPVMNamePrefix| Prefisso del nome macchina virtuale per server WAP|
|ADDCVMSize| Le dimensioni di macchine virtuali controller di dominio|
|ADFSVMSize| Le dimensioni di macchine virtuali di server ADFS|
|WAPVMSize| Le dimensioni di macchine virtuali di server WAP|
|AdminUserName| Il nome dell'amministratore locale macchine virtuali|
|AdminPassword| La password per l'account di amministratore locale macchine virtuali|

## <a name="additional-resources"></a>Risorse aggiuntive
* [Set di disponibilità](https://aka.ms/Azure/Availability ) 
* [Bilanciamento del carico Azure](https://aka.ms/Azure/ILB)
* [Servizio di bilanciamento del carico interno](https://aka.ms/Azure/ILB/Internal)
* [Servizio di bilanciamento del carico con connessione Internet](https://aka.ms/Azure/ILB/Internet)
* [Account di archiviazione](https://aka.ms/Azure/Storage )
* [Reti virtuali Azure](https://aka.ms/Azure/VNet)
* [ADFS e Proxy collegamenti alle applicazioni di Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
* [Configurare e gestire gli ADFS con Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Distribuzione di ADFS disponibilità Active Directory tra geografici in Azure con Azure il traffico Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




