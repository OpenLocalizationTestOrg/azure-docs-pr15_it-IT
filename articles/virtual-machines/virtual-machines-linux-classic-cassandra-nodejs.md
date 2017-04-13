<properties
    pageTitle="Eseguire Cassandra con Linux su Azure | Microsoft Azure"
    description="Come eseguire un cluster Cassandra su Linux in macchine virtuali di Azure da un'app per Node"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Esecuzione Cassandra con Linux su Azure e accedervi da Node

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Panoramica
Microsoft Azure è una piattaforma cloud aperta che viene eseguito a Microsoft come anche come licenza che include sistemi operativi, server delle applicazioni, middleware messaggistica, nonché database SQL e NoSQL da entrambi i modelli commerciali e Apri origine. La creazione di servizi flessibili nel cloud pubblici inclusi Azure richiede attenzione a pianificazione e architettura intenzionale per i server di applicazioni come livelli ben dello spazio di archiviazione. Architettura di archiviazione distribuita del Cassandra consente naturalmente per la creazione di sistemi di disponibili e tolleranza per gli errori del cluster. Cassandra rappresenta una scala di cloud database NoSQL mantenuta da Apache Software Foundation cassandra.apache.org; Cassandra scritto in linguaggio e quindi viene eseguito in entrambi gli elementi di Windows, nonché Linux piattaforme.

Lo stato attivo di questo articolo è sufficiente visualizzare distribuzione Cassandra su Ubuntu come cluster centro singola e i dati più sfruttando macchine virtuali di Microsoft Azure e reti virtuali. La distribuzione di cluster per carichi di lavoro di produzione ottimizzata non è compresa nell'ambito di questo articolo perché richiede la configurazione di nodo multi-disk, topologia squillo appropriato e modellazione per supportare la replica necessaria, la coerenza dei dati, velocità e requisiti di disponibilità elevati di dati.

Accetta in questo articolo è interessato creare il cluster Cassandra un approccio fondamentale per verificare il confronto Docker, Chef o Marionetta che può semplificare la distribuzione dell'infrastruttura.  

## <a name="the-deployment-models"></a>I modelli di distribuzione
Rete di Microsoft Azure consente la distribuzione di isolato cluster privato, l'accesso dei quali può essere limitato a raggiungere la sicurezza della rete granulare correttamente.  Poiché in questo articolo riguarda che mostra la distribuzione Cassandra livello fondamentali, non esaminato l'il livello di coerenza e la struttura di archiviazione ottimale per la velocità effettiva. Ecco l'elenco dei requisiti per il cluster ipotetico di rete:

- Sistemi esterni non possono accedere a database Cassandra dall'interno o all'esterno di Azure
- Cluster Cassandra deve essere dietro un bilanciamento del carico per il traffico thrift
- Distribuire i nodi Cassandra due gruppi in ogni centro di dati per la disponibilità di un cluster avanzata
- Bloccare il cluster in modo che solo server farm applicazioni abbia accesso al database direttamente
- Nessun pubblico endpoint di rete diversa da quella SSH
- Ogni nodo Cassandra richiede un indirizzo IP interno fisso

È possibile distribuire Cassandra su un'area di Azure singola o per più paesi in base alla natura distribuita del carico di lavoro. Modello di distribuzione di più paesi può essere utilizzato per fornire agli utenti più vicino di un particolare geography tramite la stessa infrastruttura Cassandra. Nodo incorporato replica farà del Cassandra la sincronizzazione di schema più scrive provenienti da più data center e presenta una visualizzazione coerenza dei dati alle applicazioni. Distribuzione di più paesi consente inoltre con riduzione dei rischi di più ampia Azure interruzioni dei servizi. La coerenza regolabili del Cassandra e topologia di replica aiuta a soddisfare diverse esigenze rilasciato delle applicazioni.

### <a name="single-region-deployment"></a>Distribuzione singola area
Verrà iniziare con una distribuzione singola regione e raccolto le esperienze di creazione di un modello di più aree. Rete virtuale Azure verrà utilizzata per creare subnet isolata, in modo che possono essere soddisfatti i requisiti di sicurezza di rete indicati in precedenza.  La procedura descritta nella creazione di distribuzione singola area Usa Ubuntu 14.04 risultati e 2.08 Cassandra; Tuttavia, il processo può facilmente adottare altre varianti Linux. Di seguito sono alcune delle caratteristiche sistematico della distribuzione singola area.  

**Disponibilità:** I nodi Cassandra illustrati nella figura 1 vengono distribuiti in due set di disponibilità, in modo che i nodi sono suddivisi tra più domini guasto disponibilità elevata. Macchine virtuali annotate con ogni set di disponibilità siano associati ai domini guasto 2.  Usa Microsoft Azure il concetto di dominio per la gestione di inattività (ad esempio errori hardware o software) durante il concetto di aggiornamento del dominio (ad esempio host o guest OS patch/aggiornamenti, gli aggiornamenti delle applicazioni) viene utilizzato per la gestione dei tempi di inattività pianificato. Per il ruolo dell'errore, vedere [ripristino di emergenza e disponibilità per le applicazioni di Azure](http://msdn.microsoft.com/library/dn251004.aspx) ed eseguire l'aggiornamento raggiungimento disponibilità i domini.

![Distribuzione singola area](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figura 1: Singola distribuzione di area

Si noti che al momento della loro Azure non consente la mappatura esplicita di un gruppo di macchine virtuali di un dominio specifico; di conseguenza, anche con il modello di distribuzione illustrato nella figura 1, è probabile forma statistica che è possibile eseguire il mapping di tutte le macchine virtuali a due domini guasto anziché quattro.

**Il bilanciamento del carico il traffico Thrift:** Raccolte di client thrift all'interno del server web connettersi al cluster tramite un servizio di bilanciamento del carico interno. È necessario il processo di aggiunta di bilanciamento interni alla subnet "dati" (vedere Figura 1) nel contesto di servizio cloud hosting cluster Cassandra. Dopo aver definito il servizio di bilanciamento del carico interno, ogni nodo richiede l'endpoint di bilanciamento del carico da aggiungere e le annotazioni di un set di bilanciamento del carico con nome bilanciamento del carico definita in precedenza. Per ulteriori informazioni, vedere [Bilanciamento del carico Azure interno ](../load-balancer/load-balancer-internal-overview.md).

**Cluster semi:** È importante selezionare il maggior parte dei nodi disponibili per semi come nuovi nodi comunica con nodi per individuare la topologia del cluster. Un nodo da ogni set di disponibilità viene designato come nodi per evitare singolo punto di errore.

**Fattore di replica e la coerenza livello:** Compilazione di disponibilità e dati durata di Cassandra è caratterizzata dalla replica fattore di - numero di copie di ogni riga archiviati sul cluster e la coerenza livello (numero di replica da essere in lettura/scrittura prima di restituire il risultato al chiamante). Fattore di replica è specificato durante la creazione di spazio delle CHIAVI (simile a un database relazionale) mentre il livello di coerenza viene specificato durante l'esecuzione della query CRUD. Vedere la documentazione Cassandra [configurazione per la coerenza](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) per informazioni dettagliate la coerenza e la formula per il calcolo di base.

Cassandra supporta due tipi di modelli di integrità dei dati: la coerenza e la coerenza eventuale; il fattore di replica e il livello di uniformità tra loro determinerà se i dati siano coerenti come un'operazione di scrittura è stata completata o sarà possibile infine coerente. Ad esempio, che specifica di base, come il livello di coerenza verrà sempre garantisce dati coerenza mentre qualsiasi livello di coerenza, sotto il numero di replica da consentire la scrittura in base alle esigenze per raggiungere base (ad esempio uno) risultati dei dati in modo coerente alla fine.

Il cluster di 8 nodi illustrato sopra, con un fattore di replica di 3 e base (2 nodi vengono letti o scritti la coerenza) lettura/scrittura livello coerenza, possono superare le perdite teorica di al massimo 1 nodo per ogni gruppo di replica prima dell'inizio applicazione notato l'errore. Si presuppone che tutti gli spazi chiavi hanno anche bilanciato lettura/scrittura richieste.  I parametri che verrà usata per il cluster distribuito sono i seguenti:

Configurazione di cluster Cassandra singola area:

| Parametro cluster | Valore | Note |
| ----------------- | ----- | ------- |
| Numero dei nodi (N) | 8   | Numero totale di nodi del cluster |
| Replica fattore) | 3 | Numero di repliche di una determinata riga |
| Livello di coerenza (scrittura) | QUORUM[(RF/2) +1) = 2] il risultato della formula viene arrotondato per difetto | Scrive il massimo 2 repliche prima di inviata la risposta chiamante. 3 ° replica è scritto in modo coerenza alla fine. |
| Livello di coerenza (lettura) | BASE [(RF/2) = 2 + 1] il risultato della formula viene arrotondato per difetto | Legge 2 repliche prima di inviare risposta al chiamante. |
| Strategia di replica | NetworkTopologyStrategy vedere [Replica dei dati](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) nella documentazione Cassandra per ulteriori informazioni | Consente di usare la topologia di distribuzione e la inserisce repliche nei nodi in modo che ogni la replica non fine lo stesso rack |
| Snitch | GossipingPropertyFileSnitch vedere [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) nella documentazione Cassandra per ulteriori informazioni | NetworkTopologyStrategy utilizza il concetto di snitch per comprendere la topologia. GossipingPropertyFileSnitch offre maggiore controllo per eseguire il mapping di ogni nodo rack e centro dati. Il cluster utilizza quindi possono propagare queste informazioni. Questo è molto più semplice in impostazione IP dinamica rispetto PropertyFileSnitch |


**Azure considerazioni Cassandra Cluster:** La funzionalità di macchine virtuali di Microsoft Azure utilizza archiviazione Blob Azure per la persistenza disco; Spazio di archiviazione Azure Salva 3 repliche di ogni disco per l'affidabilità nel tempo. Pertanto, ogni riga di dati inseriti in una tabella Cassandra già archiviato in 3 repliche e pertanto la coerenza dei dati è già gestito anche se la replica fattore () è 1. Il problema principale con fattore di replica da 1 è che l'applicazione sarà l'esperienza i tempi di inattività anche se si verifica un errore di un singolo nodo Cassandra. Tuttavia, se un nodo verso il basso per risolvere il problema (ad esempio hardware, guasti di sistema del software) riconosciuto da Controller dell'infrastruttura di Azure, esso verrà effettuare il provisioning di un nuovo nodo nella relativa posizione usando le stesse unità di spazio di archiviazione. Il provisioning di un nuovo nodo per sostituire il vecchio può richiedere alcuni minuti.  Allo stesso modo per le attività di manutenzione pianificata come guest OS modifiche, Cassandra Aggiorna e modifiche all'applicazione Controller dell'infrastruttura di Azure esegue gli aggiornamenti dei nodi in sequenza del cluster.  Gli aggiornamenti in sequenza potrebbero richiedere verso il basso alcuni nodi, in un momento e pertanto cluster potrebbe verificarsi breve tempo di inattività per alcuni partizioni. Tuttavia, i dati non saranno persi a causa la ridondanza di archiviazione Azure incorporata.  

Per i sistemi distribuiti in Azure che non richieda disponibilità (ad esempio intorno 99,9 che corrisponde al 8,76 ore/anno, per informazioni dettagliate, vedere [Disponibilità](http://en.wikipedia.org/wiki/High_availability) ) è possibile eseguire con RF = 1 e la coerenza livello = uno.  Per le applicazioni ai requisiti di disponibilità, RF = 3 e la coerenza livello = base tollerano i tempi di inattività di uno dei nodi una replica. RF = 1 in tradizionale distribuzioni (ad esempio, locale) non possono essere utilizzate per la possibile perdita di dati risultante da problemi come errori del disco.   

## <a name="multi-region-deployment"></a>Distribuzione di più paesi
Modello di replica e la coerenza dati supporto tecnico del Cassandra descritto in precedenza consente con la distribuzione di più paesi all'esterno della casella senza la necessità di qualsiasi utensili esterni. È molto diversa dai database relazionali tradizionali in cui la configurazione di database che rispecchiano per scrive più master può essere molto complessa. Consente di Cassandra in un'area più configurare con gli scenari di utilizzo inclusi i seguenti:

**Distribuzione in base a prossimità:** Le applicazioni multi-tenant, con Cancella mapping degli utenti tenant-a-area geografica, possibile tratto dalla latenza bassa del cluster più aree. Ad esempio una gestione risorse sistemi per gli istituti possono distribuire un cluster distribuito in aree geografiche negli Stati Uniti orientali e Ovest US per servire i rispettivi campus per transazione e analitica. I dati possono essere localmente coerenti all'ora lettura e scrittura e possono essere eventualmente coerenti tra entrambe le aree geografiche. Ci sono altri esempi alla distribuzione di elementi multimediali e -commerce nulla e tutto ciò che serve base geografico concentrato utente è un caso di utilizzo ottimale per questo modello di distribuzione.

**Disponibilità:** La ridondanza è fondamentale raggiungimento disponibilità di software e hardware; Per informazioni dettagliate, vedere sistemi Cloud affidabili predefiniti in Microsoft Azure. In Microsoft Azure, l'unico modo affidabile per ottenere la ridondanza vera è distribuendo un cluster di più aree. Applicazioni possono essere distribuite in modo attivo attivo o attivo-passivo e se una delle aree verso il basso, gestore del traffico Azure possibile reindirizzare il traffico all'area attiva.  Con la distribuzione singola regione, se la disponibilità 99,9, una distribuzione a due aree può raggiungere una disponibilità di 99.9999 calcolati dalla formula: (1-(1-0.999) *(1-0,999))*100); carta sopra per informazioni dettagliate, vedere.

**Di emergenza:** Cluster Cassandra più aree, se correttamente progettata, possibile contrastare interruzioni del centro di dati. Se un'area non è attivo, l'applicazione distribuita alle altre aree può cominciare a gestire gli utenti finali. Ad esempio alcuna altri implementazione continuità aziendale, l'applicazione deve essere tolleranza perdita dei dati dei dati nella pipeline asincrono. Cassandra invece il recupero molto il processo decisionale più il tempo necessario per i processi di ripristino database tradizionale. Figura 2 mostra il modello di distribuzione più aree più comuni con otto nodi in ogni area. Entrambe aree geografiche sono immagini speculari tra loro per lo stesso di simmetria; strutture di scenari reali dipendono dal tipo di carico di lavoro (ad esempio transazione o Analytical Processing), rilasciato, RTO, la coerenza dei dati e dei requisiti di disponibilità.

![Distribuzione di area multipli](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figura 2: Distribuzione di più paesi Cassandra

### <a name="network-integration"></a>Integrazione di rete
Set di macchine virtuali distribuiti a reti private disponibile in due aree comunica con un'altra usando un tunnel VPN. Tunnel VPN si connette due gateway software viene completato il provisioning durante il processo di distribuzione di rete. Entrambe aree geografiche hanno simile architettura di rete in termini di subnet "web" e "dati"; Rete Azure consente la creazione di un numero subnet in base alle esigenze e applicare ACL in base alle esigenze per la sicurezza della rete. Durante la progettazione della topologia cluster tra è necessario essere considerate latenza di comunicazione centro dati ed economico il traffico di rete.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Coerenza dei dati per la distribuzione di più Data Center
Distribuito distribuzioni è necessario conoscere l'impatto di topologia cluster sulla velocità effettiva e una maggiore disponibilità. È necessario selezionare in modo che la base non dipende dalla disponibilità di tutti i data center di RF e a livello di coerenza.
Per un sistema che deve essere elevata coerenza, LOCAL_QUORUM di livello la coerenza (per lettura e scrittura) verrà assicurarsi che la lettura locale e scrittura sta soddisfatti dei nodi di locale mentre dati replicati asincrono centri dati remoti.  Tabella 2 vengono riepilogati i dettagli di configurazione per il cluster più aree descritto in precedenza in scrittura su.

**Configurazione di due aree Cassandra cluster**


| Parametro cluster | Valore | Note |
| ----------------- | ----- | ------- |
| Numero dei nodi (N) | 8 + 8 | Numero totale di nodi del cluster |
| Replica fattore) | 3 | Numero di repliche di una determinata riga |
| Livello di coerenza (scrittura) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] il risultato della formula viene arrotondato per difetto | 2 nodi verranno scritti il primo centro di dati in modo sincrono; i 2 nodi aggiuntivi necessari per base verranno scritti in modo asincrono al data center dei 2a. |
| Livello di coerenza (lettura) | LOCAL_QUORUM ((RF/2) + 1) = 2 il risultato della formula viene arrotondato per difetto | Richieste di lettura vengono soddisfatte dalla sola regione; 2 nodi vengono letti prima che venga inviata la risposta al client. |
| Strategia di replica | NetworkTopologyStrategy vedere [Replica dei dati](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) nella documentazione Cassandra per ulteriori informazioni | Consente di usare la topologia di distribuzione e la inserisce repliche nei nodi in modo che ogni la replica non fine lo stesso rack |
| Snitch | GossipingPropertyFileSnitch vedere [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) nella documentazione Cassandra per ulteriori informazioni | NetworkTopologyStrategy utilizza il concetto di snitch per comprendere la topologia. GossipingPropertyFileSnitch offre maggiore controllo per eseguire il mapping di ogni nodo rack e centro dati. Il cluster utilizza quindi possono propagare queste informazioni. Questo è molto più semplice in impostazione IP dinamica rispetto PropertyFileSnitch |


##<a name="the-software-configuration"></a>LA CONFIGURAZIONE DEL SOFTWARE
Le seguenti versioni software vengono utilizzate durante la distribuzione:

<table>
<tr><th>Software</th><th>Origine</th><th>Versione</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Cassandra Apache 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 RISULTATI</td></tr>
</table>

Poiché il download di JRE richiede l'approvazione manuale delle licenze Oracle, per semplificare la distribuzione, scaricare i requisiti software per il desktop per il caricamento in un secondo momento nell'immagine di modello Ubuntu che verrà creata per poter eseguire la distribuzione di cluster.

Scaricare il software indicato in una directory di download noto (ad esempio %TEMP%/downloads in Windows o ~/Downloads nella maggior parte delle distribuzioni Linux o nel Mac) nel computer locale.

### <a name="create-ubuntu-vm"></a>CREARE MACCHINE VIRTUALI UBUNTU
In questo passaggio del processo si creerà Ubuntu immagine con il software prerequisito in modo che l'immagine può essere riutilizzato per il provisioning di diversi nodi Cassandra.  
####<a name="step-1-generate-ssh-key-pair"></a>PASSAGGIO 1: Generare coppia di chiavi SSH
Azure deve un X509 codificato chiave pubblica PEM o DER al momento del provisioning. Generare una coppia di chiavi pubblica/privata seguendo le istruzioni che si trova in modalità di utilizzo SSH con Linux in Azure. Se si prevede di utilizzare putty.exe come un client SSH sul Windows o Linux, è necessario convertire PEM codificato chiave privata RSA in formato PPK utilizzando puttygen.exe; le istruzioni per l'oggetto sono disponibili nella pagina web precedente.

####<a name="step-2-create-ubuntu-template-vm"></a>PASSAGGIO 2: Creare il modello di Ubuntu macchine Virtuali
Per creare il modello macchine Virtuali, accedere al portale classico Azure e utilizzare la seguente sequenza: fare clic su Nuovo, calcolo, macchina virtuale, da raccolta, UBUNTU, Ubuntu Server 14.04 risultati e quindi fare clic sulla freccia a destra. Per un'esercitazione che illustra come creare una VM Linux, vedere creare un Linux macchina virtuale in esecuzione.

Immettere le informazioni seguenti nella schermata "configurazione macchina virtuale" #1:

<table>
<tr><th>NOME CAMPO              </td><td>       VALORE DEL CAMPO               </td><td>         NOTE                </td><tr>
<tr><td>DATA DI RILASCIO VERSIONE    </td><td> Selezione della data di drow verso il basso</td><td></td><tr>
<tr><td>NOME MACCHINA VIRTUALE    </td><td> modello di Cass                </td><td> Questo è il nome host della macchina virtuale </td><tr>
<tr><td>LIVELLO                     </td><td> STANDARD                        </td><td> Lasciare il valore predefinito              </td><tr>
<tr><td>DIMENSIONI                     </td><td> A1                              </td><td>Selezionare la macchina virtuale in base alle esigenze IO; a questo scopo lasciare il valore predefinito </td><tr>
<tr><td> NUOVO NOME UTENTE           </td><td> LocalAdmin dell'                      </td><td> "amministratore" è un nome utente riservata Ubuntu 12 xx e dopo</td><tr>
<tr><td> AUTENTICAZIONE      </td><td> Fare clic su casella di controllo                 </td><td>Verificare se si desidera proteggere con una chiave SSH </td><tr>
<tr><td> CERTIFICATO             </td><td> nome del file di certificato di chiave pubblica </td><td> Utilizzare la chiave pubblica generata in precedenza</td><tr>
<tr><td> Nuova Password   </td><td> password complessa </td><td> </td><tr>
<tr><td> Confermare la Password   </td><td> password complessa </td><td></td><tr>
</table>

Immettere le informazioni seguenti nella schermata "configurazione macchina virtuale" #2:

<table>
<tr><th>NOME CAMPO             </th><th> VALORE DEL CAMPO                       </th><th> NOTE                                 </th></tr>
<tr><td> SERVIZIO CLOUD  </td><td> Creare un nuovo servizio cloud    </td><td>Servizio cloud è una risorsa di elaborazione contenitore come macchine virtuali</td></tr>
<tr><td> NOME DNS DEL SERVIZIO CLOUD </td><td>Ubuntu template.cloudapp.net   </td><td>Assegnare un nome di bilanciamento carico indipendente computer</td></tr>
<tr><td> REGIONE/GRUPPO AFFINITÀ/VIRTUALI </td><td>    Usa ovest </td><td> Selezionare un'area in cui le applicazioni web accedono al cluster Cassandra</td></tr>
<tr><td>ACCOUNT DI ARCHIVIAZIONE </td><td>   Utilizza impostazioni predefinite </td><td>Usare l'account di archiviazione predefinito o un account di archiviazione già creati in una determinata</td></tr>
<tr><td>SET DI DISPONIBILITÀ </td><td>  Nessuno </td><td>  Lasciare vuota la casella</td></tr>
<tr><td>PUNTI FINALI   </td><td>Utilizza impostazioni predefinite </td><td>  Utilizzare la configurazione di SSH predefinita </td></tr>
</table>

Fare clic sulla freccia destra, lasciare le impostazioni predefinite nella schermata #3 e fare clic sul pulsante "controllo" per completare il processo di provisioning macchine Virtuali. Dopo alcuni minuti, la macchina virtuale con il nome "ubuntu-modello" deve essere in uno stato "esecuzione".

###<a name="install-the-necessary-software"></a>INSTALLARE IL SOFTWARE NECESSARIO
####<a name="step-1-upload-tarballs"></a>PASSAGGIO 1: Caricare tarballs
Usa scp o pscp, copiare il software precedentemente scaricato alla directory ~/downloads utilizzando il formato di comando seguente:

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Ripetere il comando riportato sopra per JRE anche per quanto riguarda i bit Cassandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>PASSAGGIO 2: Preparare la struttura di directory ed estrarre archivi
Accedere a macchina virtuale e creare la struttura di directory ed estrarre software come un utente con privilegi avanzati tramite script bash riportato di seguito:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Se si incolla questo script in una finestra vim, assicurarsi di rimuovere il ritorno a capo ('\r ") utilizzando il comando seguente:

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Passaggio 3: Modificare e così via/profilo
Aggiungere le seguenti all'estremità:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Passaggio 4: Installa JNA per sistemi di produzione
Utilizzare la sequenza di comandi seguenti: il seguente comando verrà installare jna 3.2.7.jar e jna piattaforma 3.2.7.jar alla directory /usr/share.java sudo apt-get libjna java

Creare collegamenti symbolic nella directory CASS_HOME/raccolta $ script di avvio Cassandra poterlo trovare questi vasetti da:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Passaggio 5: Configurare cassandra.yaml
Modificare cassandra.yaml in ogni macchina virtuale in modo da rispecchiare configurazione necessaria per tutte le macchine virtuali [abbiamo verrà modificare questa durante il provisioning effettivo]:

<table>
<tr><th>Nome campo   </th><th> Valore  </th><th> Note </th></tr>
<tr><td>nome_cluster </td><td>  "CustomerService"   </td><td> Usare il nome che ne suggerisca la distribuzione</td></tr>
<tr><td>listen_address  </td><td>[lasciarlo vuoto]   </td><td> Eliminare "host locale" </td></tr>
<tr><td>rpc_addres   </td><td>[lasciarlo vuoto]  </td><td> Eliminare "host locale" </td></tr>
<tr><td>semi   </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8" </td><td>Elenco di tutti gli indirizzi IP designati come semi.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Viene utilizzato per la NetworkTopologyStrateg per inferenza data center e rack di macchina virtuale</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Passaggio 6: Acquisire un'immagine macchine Virtuali
Accedere al computer virtuali utilizzando hostname (hk-CA-template.cloudapp.net) e la chiave privata SSH creato in precedenza. Vedere come utilizzare SSH con Linux su Azure per informazioni dettagliate su come effettuare l'accesso con il comando ssh o putty.exe.

Eseguire la sequenza di azioni per acquisire l'immagine seguente:
#####<a name="1-deprovision"></a>1. annullare l'implementazione
Usare il comando "sudo waagent: annullare l'implementazione + utente" per rimuovere informazioni specifiche istanza macchina virtuale. Per vedere [come acquisire una macchina virtuale Linux](virtual-machines-linux-classic-capture-image.md) da utilizzare come modello per ulteriori informazioni sul processo di acquisizione di immagini.

#####<a name="2-shutdown-the-vm"></a>2: arrestare la macchina virtuale
Verificare che la macchina virtuale è evidenziata e fare clic sul collegamento di arresto dalla barra inferiore.

#####<a name="3-capture-the-image"></a>3: acquisire un'immagine
Verificare che la macchina virtuale è evidenziata e fare clic sul collegamento di acquisizione dalla barra inferiore. Nella schermata successiva, assegnare un nome di immagine (ad esempio hk-cas-2-08-ub-14-04-2014071), a seconda delle necessità Descrizione immagine e fare clic su "spunta" per completare il processo di acquisizione.

L'operazione richiederà alcuni secondi e l'immagine deve essere disponibile nella sezione MY immagini della raccolta di immagini. L'origine macchine Virtuali saranno automaticamente delated dopo l'immagine viene acquisito correttamente.

##<a name="single-region-deployment-process"></a>Processo di distribuzione singola area
**Passaggio 1: creare la rete virtuale** Accedere al portale classico Azure e creare una rete virtuale con Mostra gli attributi della tabella. Per informazioni dettagliate del processo, vedere [configurare una rete virtuale Cloud-Only nel portale di classica Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) .      

<table>
<tr><th>Nome attributo macchine Virtuali</th><th>Valore</th><th>Note</th></tr>
<tr><td>Nome</td><td>vnet-cass-ovest-us</td><td></td></tr>
<tr><td>Area geografica</td><td>Usa ovest</td><td></td></tr>
<tr><td>Server DNS </td><td>Nessuno</td><td>Ignorare questo in quanto non si sta utilizzando un Server DNS</td></tr>
<tr><td>Configurare una connessione VPN punto al sito</td><td>Nessuno</td><td> Ignorare l'avviso</td></tr>
<tr><td>Configurare una connessione VPN da sito</td><td>Nnone</td><td> Ignorare l'avviso</td></tr>
<tr><td>Spazio di indirizzi</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>IP iniziale</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Aggiungere le subnet seguenti:

<table>
<tr><th>Nome</th><th>IP iniziale</th><th>CIDR</th><th>Note</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Subnet per web farm</td></tr>
<tr><td>dati</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Subnet dei nodi di database</td></tr>
</table>

È possibile proteggere i dati e subnet Web tramite gruppi di sicurezza di rete la copertura di cui non è compresa nell'ambito di questo articolo.  

**Passaggio 2: effettuare il provisioning di macchine virtuali** Usa l'immagine creata in precedenza, si verrà creare le seguenti macchine virtuali nel server cloud "hk-c-svc-Ovest" e associarli alle rispettive subnet, come illustrato di seguito:

<table>
<tr><th>Nome del computer    </th><th>Subnet </th><th>Indirizzo IP </th><th>Set di disponibilità</th><th>CC/Rack</th><th>Valore iniziale?</th></tr>
<tr><td>HK-c1-ovest-us   </td><td>dati   </td><td>10.1.2.4   </td><td>HK-c-aset-1    </td><td>cc = rack WESTUS = rack1 </td><td>Sì</td></tr>
<tr><td>HK-c2-ovest-us   </td><td>dati   </td><td>10.1.2.5   </td><td>HK-c-aset-1    </td><td>cc = rack WESTUS = rack1 </td><td>No </td></tr>
<tr><td>HK-c3-ovest-us   </td><td>dati   </td><td>10.1.2.6   </td><td>HK-c-aset-1    </td><td>cc = rack WESTUS = rack2 </td><td>Sì</td></tr>
<tr><td>HK-c4-ovest-us   </td><td>dati   </td><td>10.1.2.7   </td><td>HK-c-aset-1    </td><td>cc = rack WESTUS = rack2 </td><td>No </td></tr>
<tr><td>HK-c5-ovest-us   </td><td>dati   </td><td>10.1.2.8   </td><td>HK-c-aset-2    </td><td>cc = rack WESTUS = rack3 </td><td>Sì</td></tr>
<tr><td>HK-c6-ovest-us   </td><td>dati   </td><td>10.1.2.9   </td><td>HK-c-aset-2    </td><td>cc = rack WESTUS = rack3 </td><td>No </td></tr>
<tr><td>HK-c7-ovest-us   </td><td>dati   </td><td>10.1.2.10  </td><td>HK-c-aset-2    </td><td>cc = rack WESTUS = rack4 </td><td>Sì</td></tr>
<tr><td>HK-c8-ovest-us   </td><td>dati   </td><td>10.1.2.11  </td><td>HK-c-aset-2    </td><td>cc = rack WESTUS = rack4 </td><td>No </td></tr>
<tr><td>HK-w1-ovest-us   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-aset-1    </td><td>                       </td><td>N/D</td></tr>
<tr><td>HK-s2-ovest-us   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-aset-1    </td><td>                       </td><td>N/D</td></tr>
</table>

Per la creazione dell'elenco sopra di macchine virtuali sono le seguenti:

1.  Creare un servizio cloud vuota in una determinata
2.  Creare una macchina virtuale dall'immagine acquisita in precedenza e allegarlo alla rete virtuale creata in precedenza. Ripetere questo passaggio per tutte le macchine virtuali
3.  Aggiungere un servizio di bilanciamento del carico interno al servizio cloud e allegarlo alla subnet "dati"
4.  Per ogni macchina virtuale creata in precedenza, aggiungere un endpoint di bilanciamento del carico per il traffico thrift tramite un insieme di bilanciamento del carico connesso a di bilanciamento interno creato in precedenza

Può essere eseguita la procedura descritta nel portale classica Azure; un computer Windows (utilizzare una macchina virtuale in Azure se non si dispone di accesso a un computer Windows), utilizzare il seguente script di PowerShell per eseguire il provisioning di tutte le macchine 8 virtuali automaticamente.

**Elenco 1: Script di PowerShell per il provisioning di macchine virtuali**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Passaggio 3: Configurare Cassandra su ogni macchina virtuale**

Accedere a macchina virtuale ed eseguire le operazioni seguenti:

* Modificare $CASS_HOME/conf/cassandra-rackdc.properties per specificare le proprietà del centro e rack dati:

       dc =EASTUS, rack =rack1

* Modificare cassandra.yaml per configurare nodi come indicato di seguito:

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Passaggio 4: Avviarle e testare il cluster**

Accedere a uno dei nodi (ad esempio hk-c1-ovest-us) ed eseguire il comando seguente per visualizzare lo stato del cluster:

       nodetool –h 10.1.2.4 –p 7199 status

Verrà visualizzata la visualizzazione simile a quello illustrato di seguito per un cluster di 8 nodi:

<table>
<tr><th>Stato</th><th>Indirizzo  </th><th>Caricamento   </th><th>Token </th><th>Proprietario </th><th>ID host  </th><th>Rack</th></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>38.0%  </td><td>GUID (rimossi)</td><td>rack1</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>68.9%  </td><td>GUID (rimossi)</td><td>rack1</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack2</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack2</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack3</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack3</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack4</td></tr>
<tr><th>ANNULLARE  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (rimossi)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Il test del Cluster singola area
Per testare il cluster, procedere come segue:

1.    Utilizzare il cmdlet Get-AzureInternalLoadbalancer comando di Powershell, ottenere l'indirizzo IP del servizio di bilanciamento del carico interno (ad esempio  10.1.2.101). La sintassi del comando è illustrata di seguito: Get-AzureLoadbalancer-nome "hk-c-svc-ovest-us" [consente di visualizzare i dettagli del carico interno e il relativo indirizzo IP]
2.  Accedere a web farm macchine Virtuali (ad esempio hk-w1-ovest-us) utilizzando Putty o ssh
3.  Eseguire $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4.  Usare i comandi CQL seguenti per verificare se il cluster funziona:

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Verrà visualizzata una visualizzazione come quello illustrato di seguito:

<table>
  <tr><th> Customer_ID </th><th> nome </th><th> Cognome </th></tr>
  <tr><td> 1 </td><td> John </td><td> Rossi </td></tr>
  <tr><td> 2 </td><td> Olga </td><td> Rossi </td></tr>
</table>

Si noti che passando creato nel passaggio 4 utilizza SimpleStrategy con replication_factor di 3. SimpleStrategy è consigliato per aggiungere o sottrarre distribuzioni centro mentre NetworkTopologyStrategy per i dati più centrare distribuzioni. Replication_factor di 3 otterranno tolleranza per gli errori di nodo.

##<a id="tworegion"> </a>Processo di distribuzione di più paesi
Verranno sfruttare distribuzione singola area completata e ripetere la procedura per l'installazione della seconda regione. La differenza tra la distribuzione di una o più aree chiave è l'impostazione di tunnel VPN per le comunicazioni tra l'area geografica; verrà iniziare con l'installazione di rete, effettuare il provisioning di macchine virtuali e configurare Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Passaggio 1: Creare la rete virtuale all'area 2a
Accedere al portale classico Azure e creare una rete virtuale con Mostra gli attributi della tabella. Per informazioni dettagliate del processo, vedere [configurare una rete virtuale Cloud-Only nel portale di classica Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) .      

<table>
<tr><th>Nome attributo    </th><th>Valore    </th><th>Note</th></tr>
<tr><td>Nome    </td><td>vnet-cass-est-us</td><td></td></tr>
<tr><td>Area geografica  </td><td>Stati Uniti orientali</td><td></td></tr>
<tr><td>Server DNS     </td><td></td><td>Ignorare questo in quanto non si sta utilizzando un Server DNS</td></tr>
<tr><td>Configurare una connessione VPN punto al sito</td><td></td><td>     Ignorare l'avviso</td></tr>
<tr><td>Configurare una connessione VPN da sito</td><td></td><td>      Ignorare l'avviso</td></tr>
<tr><td>Spazio di indirizzi   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>IP iniziale </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Aggiungere le subnet seguenti:
<table>
<tr><th>Nome    </th><th>IP iniziale    </th><th>CIDR   </th><th>Note</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Subnet per web farm</td></tr>
<tr><td>dati    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Subnet dei nodi di database</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Passaggio 2: Creare le reti locale
Una rete locale in rete virtuale Azure è uno spazio di indirizzi proxy che esegue il mapping a un sito remoto ad esempio un cloud privato o un'altra area Azure. Questo spazio di indirizzi proxy è associato a un gateway remoto per la rete instradare le destinazioni di rete corrette. Per istruzioni sulla connessione VNET-VNET, vedere [configurare un VNet a VNet connessione](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) .

Creare due reti locali per i dettagli seguenti:

| Nome di rete | Indirizzo del Gateway VPN | Spazio di indirizzi | Note |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-us | 23.1.1.1  | 10.2.0.0/16   | Durante la creazione di un segnaposto della rete locale assegnare indirizzo gateway. L'indirizzo reale gateway viene compilato dopo aver creato il gateway. Verificare che lo spazio di indirizzi corrisponde esattamente le rispettive VNET remoto; In questo caso il VNET creata nell'area orientale degli Stati Uniti. |
| HK-lnet-Map-to-West-us | 23.2.2.2  | 10.1.0.0/16   | Durante la creazione di un segnaposto della rete locale assegnare indirizzo gateway. L'indirizzo reale gateway viene compilato dopo aver creato il gateway. Verificare che lo spazio di indirizzi corrisponde esattamente le rispettive VNET remoto; In questo caso il VNET creata nell'area occidentale degli Stati Uniti. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Passaggio 3: "Locale" rete a VNETs rispettivi
Dal portale di classica Azure, selezionare ogni vnet, fare clic su "Configura", selezionare "Connetti alla rete locale" e selezionare le reti locali per i dettagli seguenti:


| Rete virtuale | Rete locale |
| --------------- | ------------- |
| HK-vnet-ovest-us | HK-lnet-Map-to-East-us |
| HK-vnet-est-us | HK-lnet-Map-to-West-us |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Passaggio 4: Creare gateway VNET1 e VNET2
Dal dashboard di entrambe le reti virtuali, fare clic su GATEWAY creare che attiverà gateway VPN processo di provisioning. Dopo alcuni minuti il dashboard di ogni rete virtuale deve essere visualizzato l'indirizzo del gateway effettivo.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Passaggio 5: Aggiornamento "Locale" reti indirizzi rispettivi "Gateway"###
Modificare entrambe le reti locali per sostituire l'indirizzo IP del gateway segnaposto con l'indirizzo IP reale gateway solo provisioning. Utilizzare il mapping seguente:

<table>
<tr><th>Rete locale    </th><th>Gateway di rete virtuale</th></tr>
<tr><td>HK-lnet-Map-to-East-us </td><td>Gateway di hk-vnet-ovest-us</td></tr>
<tr><td>HK-lnet-Map-to-West-us </td><td>Gateway di hk-vnet-est-us</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Passaggio 6: Aggiornare la chiave condivisa
Usare il seguente script di Powershell per aggiornare la chiave IPSec di ogni gateway VPN [Usa il tasto i migliori risultati per entrambi i gateway]: Set-AzureVNetGatewayKey - VNetName hk-vnet-est-ci - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-ovest-ci - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Passaggio 7: Stabilire la connessione VNET-VNET
Dal portale di classica Azure, usare il menu "DASHBOARD" di entrambe le reti virtuali per stabilire la connessione al gateway di gateway. Utilizzare le voci di menu "CONNETTI" nella barra degli strumenti nella parte inferiore. Dopo alcuni minuti il dashboard deve visualizzare graficamente i dettagli della connessione.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Passaggio 8: Creare le macchine virtuali nell'area #2
Creare l'immagine Ubuntu come descritto nella distribuzione area #1 seguendo le stesse operazioni Copia file di immagine del disco rigido virtuale per l'account Azure dello spazio di archiviazione disponibile nell'area #2 e creare l'immagine. Utilizzare questa immagine e creare l'elenco seguente macchine virtuali in un nuovo servizio cloud hk-c-svc-est-us:


| Nome del computer | Subnet | Indirizzo IP | Set di disponibilità | CC/Rack | Valore iniziale? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-est-us | dati  | 10.2.2.4   | HK-c-aset-1      | cc = rack EASTUS = rack1 | Sì |
| HK-c2-est-us | dati  | 10.2.2.5   | HK-c-aset-1      | cc = rack EASTUS = rack1 | No  |
| HK-c3-est-us | dati  | 10.2.2.6   | HK-c-aset-1      | cc = rack EASTUS = rack2 | Sì |
| HK-c5-est-us | dati  | 10.2.2.8   | HK-c-aset-2      | cc = rack EASTUS = rack3 | Sì |
| HK-c6-est-us | dati  | 10.2.2.9   | HK-c-aset-2      | cc = rack EASTUS = rack3 | No  |
| HK-c7-est-us | dati  | 10.2.2.10  | HK-c-aset-2      | cc = rack EASTUS = rack4 | Sì |
| HK-c8-est-us | dati  | 10.2.2.11  | HK-c-aset-2      | cc = rack EASTUS = rack4 | No  |
| HK-w1-est-us | Web   | 10.2.1.4   | HK-w-aset-1      | N/D                    | N/D |
| HK-s2-est-us | Web   | 10.2.1.5   | HK-w-aset-1      | N/D                    | N/D |


Seguire le istruzioni stesso come area #1 ma usare 10.2.xxx.xxx spazio di indirizzi.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Passaggio 9: Configurare Cassandra su ogni macchina virtuale
Accedere a macchina virtuale ed eseguire le operazioni seguenti:

1. Modificare $CASS_HOME/conf/cassandra-rackdc.properties per specificare le proprietà del centro e rack dati nel formato: CC = rack EASTUS = rack1
2. Modificare cassandra.yaml per configurare nodi: semi: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###<a name="step-10-start-cassandra"></a>Passaggio 10: Avviare Cassandra
Accedere a ogni macchina virtuale e avviare Cassandra in background eseguendo il seguente comando: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Il test del Cluster di più paesi
A questo punto è stata distribuita Cassandra a 16 nodi con 8 nodi in ogni area Azure. I nodi sono dello stesso cluster per il nome del cluster comuni e la configurazione di nodo valore iniziale. Per testare il cluster, procedere come segue:

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Passaggio 1: Ottenere l'indirizzo IP bilanciamento del carico interno per entrambe le aree tramite PowerShell
- Get-AzureInternalLoadbalancer - nome "hk-c-svc-ovest-us"
- Get-AzureInternalLoadbalancer - nome "hk-c-svc-est-us"  

    Prendere nota degli indirizzi IP (ad esempio Ovest - 10.1.2.101, est - 10.2.2.101) visualizzato.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Passaggio 2: Eseguire le operazioni seguenti nell'area Ovest dopo l'accesso al hk-w1-ovest-us
1.    Eseguire $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2.  Eseguire i comandi CQL seguenti:

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Verrà visualizzata una visualizzazione come quello illustrato di seguito:

| Customer_ID | nome | Cognome |
| ----------- | --------- | -------- |
| 1           | John      | Rossi      |
| 2           | Olga      | Rossi      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Passaggio 3: Eseguire le operazioni seguenti nell'area Est dopo l'accesso in hk-w1-est-Contattaci:
1.    Eseguire $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2.  Eseguire i comandi CQL seguenti:

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Verrà visualizzata la stessa visualizzazione visto per l'area Ovest:


| Customer_ID | nome | Cognome   |
|------------ | --------- | ---------- |
| 1           | John      | Rossi        |
| 2           | Olga      | Rossi        |


Eseguire alcuni altre inserimenti e che quelle ottenere replicato a ovest-Contattaci a una parte del cluster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Test Cassandra Cluster da Node
Usando le macchine Linux creata in "web" a più livelli in precedenza, si eseguirà un semplice script node per leggere i dati inseriti in precedenza

**Passaggio 1: Installare Node e Cassandra Client**

1. Installare Node e npm
2. Installare nodo pacchetto "cassandra client" tramite npm
3. Eseguire il seguente script al prompt della shell che visualizza la stringa json dei dati recuperati:

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusioni
Microsoft Azure è una piattaforma flessibile che consente l'esecuzione di Microsoft nonché a software open source, come illustrato da questo esercizio. È possibile distribuire cluster Cassandra altamente disponibili in un unico centro dati tramite la diffusione dei nodi del cluster in più domini guasto. Cluster Cassandra possono anche essere distribuiti in più aree geografiche distante regioni Azure per sistemi di prova di emergenza. Azure e Cassandra insieme consente la costruzione di scalabilità, disponibilità e servizi di emergenza cloud recuperati necessari da internet oggi proporzioni servizi.  

##<a name="references"></a>Riferimenti##
- [http://Cassandra.Apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
