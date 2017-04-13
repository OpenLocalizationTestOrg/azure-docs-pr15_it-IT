<properties
    pageTitle="Failover nel ripristino del sito | Microsoft Azure" 
    description="Il ripristino del sito Azure coordinate replica failover e ripristino di macchine virtuali e server fisici. Informazioni sul controllo a Azure o un Data Center secondario." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="failover-in-site-recovery"></a>Failover nel ripristino del sito

Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica

In questo articolo vengono fornite informazioni e istruzioni per il ripristino (sopra e in mancanza indietro non funziona) macchine virtuali e server fisici che sono protetti con il ripristino del sito. 

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.


## <a name="types-of-failover"></a>Tipi di failover

Dopo la protezione è abilitata per macchine virtuali e server fisici e si sta replica è possibile eseguire failover in base alle esigenze. Il ripristino del sito supporta diversi tipi di failover.

**Failover** | **Quando eseguire** | **Dettagli** | **Processo**
---|---|---|---
**Test failover** | Eseguire per convalidare la strategia di replica o eseguire un drill di ripristino di emergenza | Senza perdita di dati o il tempo di inattività.<br/><br/>Nessun impatto sulla replica<br/><br/>Nessun impatto sull'ambiente di produzione | Avviare il failover<br/><br/>Specificare come computer di test verrà connesso reti dopo il failover<br/><br/>Verificare l'avanzamento della scheda **processi** . Computer di test vengono creati e avviare in posizione secondaria<br/><br/>Azure - connettersi al computer nel portale di Azure<br/><br/>Secondario sito - accedere al computer sullo stesso host e cloud<br/><br/>Completare il test e pulire automaticamente le impostazioni di failover test.
**Failover pianificato** | Esegui per soddisfare requisiti di conformità<br/><br/>Eseguire per interventi di manutenzione pianificata<br/><br/>Eseguire per avere esito negativo sui dati per mantenere carichi di lavoro in esecuzione per le interruzioni note - ad esempio un errore di power previsto o gravi meteo<br/><br/>Eseguire failback dopo il failover da principale a secondario | Senza perdita di dati<br/><br/>Tempo di inattività è sostenuto per il tempo impiegato per arrestare la macchina virtuale sul server principale e visualizzata nella posizione secondaria.<br/><br/>Macchine virtuali sono impatto computer di destinazione diventa macchine di origine dopo il failover. | Avviare il failover<br/><br/>Verificare l'avanzamento della scheda **processi** . Computer di origine è chiuso<br/><br/>Avviare il computer duplicata in posizione secondaria<br/><br/>Azure - connettersi al computer di replica nel portale di Azure<br/><br/>Secondario sito - accedere al computer nello stesso host e nel cloud stesso<br/><br/>Eseguire il commit il failover
**Failover non pianificato** | Eseguire questo tipo di failover in modo interattivo quando un sito principale diventa accessibile a causa di un incidente imprevisto, ad esempio un risparmio energia elettrica o attacchi virus <br/><br/> È possibile eseguire un pianificato failover può essere eseguito anche se non è disponibile un sito principale. | Perdita di dati dipende dalle impostazioni di frequenza replica<br/><br/>I dati verranno aggiornati secondo l'ultima sincronizzazione | Avviare il failover<br/><br/>Verificare l'avanzamento della scheda **processi** . Se lo si desidera provare a chiudere macchine virtuali e sincronizzare i dati più recenti<br/><br/>Avviare il computer duplicata in posizione secondaria<br/><br/>Azure - connettersi al computer di replica nel portale di Azure<br/><br/>Sito secondario accedere al computer nello stesso host e nel cloud stesso<br/><br/>Eseguire il commit il failover


I tipi di failover supportate dipendono dal proprio scenario di distribuzione.

**Direzione failover** | **Test failover** | **Failover pianificato** | **Failover non pianificato**
---|---|---|---
Sito VMM primario al sito secondario VMM | Supportati | Supportati | Supportati 
Sito secondario di VMM al sito principale VMM | Supportati | Supportati | Supportati
Cloud nel Cloud (singola VMM server) |  Supportati | Supportati | Supportati
Sito VMM Azure | Supportati | Supportati | Supportati 
Azure al sito VMM | Non supportate | Supportati | Non supportate 
Sito di Hyper-V in Azure | Supportati | Supportati | Supportati
Azure al sito di Hyper-V | Non supportate | Supportati | Non supportate
Sito VMware in Azure | Supportati (scenario avanzato)<br/><br/> Scenario non supportato (legacy) |Non supportate | Supportati
Server fisico in Azure | Supportati (scenario avanzato)<br/><br/> Scenario non supportato (legacy) | Non supportate | Supportati

## <a name="failover-and-failback"></a>Failover e failback

Non si riesce su macchine virtuali a un sito secondario in locale o su Azure, a seconda della distribuzione. Un computer che ha esito negativo su in Azure viene creato come una macchina virtuale Azure. Potrebbe non riuscire su una sola macchina virtuale o server fisico o un piano di ripristino. Piani di ripristino è costituito da uno o più ordinate gruppi contenenti protette macchine virtuali o server fisici. Vengono utilizzati per progettare failover di più computer che non su in base al gruppo si trovano. [Per ulteriori](site-recovery-create-recovery-plans.md) informazioni sui piani di ripristino. 

Dopo aver completato il failover e i computer siano in esecuzione in una posizione secondaria si noti che:

- Se Impossibile su Azure, dopo i computer failover non protetta o replica nel percorso primario o secondario. In Azure macchine virtuali vengono archiviate in archiviazione replicato geografico che fornisce resilienza, ma non replica.
- Se si ha un non pianificato il controllo a un sito secondario, dopo che il computer failover nel percorso secondario non protetto o la replica.
- Se è stato fatto un pianificato il controllo a un sito secondario, dopo che sono protetti i computer failover nel percorso secondario.
 

### <a name="failback-from-secondary-site"></a>Failback dal sito secondario

Failback da un sito secondario impostare come primario utilizza lo stesso processo come il controllo dall'oggetto primario a secondario. Dopo avere inserito l'approvata e completare il failover da primaria al Data Center secondario, è possibile avviare replica inversa quando il sito principale diventa disponibile. Inversa della replica avvia la replica tra il sito secondario e principale sincronizzando i dati delta. Replica inversa porta macchine virtuali in uno stato protetto ma Data Center del secondario è ancora presente nella posizione attiva. Per rendere il sito principale nella posizione attiva si avvia pianificato in caso di errore da secondaria a primaria, seguito da un'altra replica inversa.

### <a name="failback-from-azure"></a>Failback da Azure

Se non riuscita su Azure macchine virtuali vengono protetti tramite le caratteristiche di adattabilità Azure per macchine virtuali. Per rendere il sito principale originale nella posizione attiva è eseguire pianificato in caso di errore. Se il sito originale non è disponibile, potrebbe non riuscire posizione originale o in un'altra posizione. Per avviare la replica dopo aver failback nella posizione principale si avvia una replica inversa.

### <a name="failover-considerations"></a>Considerazioni sul failover

- **Indirizzo IP dopo il failover**, per impostazione predefinita, un'operazione su computer avrà un indirizzo IP diverso da quello del computer di origine. Se si desidera mantenere la stessa vedere indirizzi IP: 
    - **Sito secondario**, ovvero se sta la mancata su un sito secondario e si desidera mantenere un indirizzo IP [leggere](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) questo articolo. Si noti che è possibile mantenere un indirizzo IP pubblico se il provider di servizi Internet supportato.
    - **Azure**, ovvero se sta la mancata su Azure è possibile specificare l'indirizzo IP che si desidera assegnare la scheda **Configura** le proprietà di macchina virtuale. Non è possibile mantenere un indirizzo IP pubblico dopo il controllo a Azure. È possibile mantenere gli spazi indirizzo non RFC 1918 utilizzati come indirizzi interni.
- **Failover parziale**, ovvero se si vuole avere esito negativo su parte di un sito anziché di un intero sito si noti che: 
    - **Sito secondario**, ovvero se non si trova su una parte di un sito principale a un sito secondario e si desidera connettersi al sito principale, usare una connessione VPN da sito per la connessione non riuscito tramite le applicazioni del sito secondario a componenti dell'infrastruttura in esecuzione nel sito principale. Se si verifica un errore di un'intera subnet su è possibile mantenere l'indirizzo IP macchina virtuale. Se non si riesce su una subnet parziale non è possibile mantenere l'indirizzo IP macchina virtuale perché non è possibile dividere subnet tra siti.
    - **Azure**, ovvero se si desidera connettersi al sito principale esito negativo su un sito parziale in Azure, è possibile utilizzare una connessione VPN da sito per la connessione non riuscito tramite applicazione in Azure a componenti dell'infrastruttura in esecuzione nel sito principale. Si noti che se si verifica un errore intera subnet su è possono mantenere l'indirizzo IP macchina virtuale. Se non si riesce su una subnet parziale non è possibile mantenere l'indirizzo IP macchina virtuale perché non è possibile dividere subnet tra siti.
 
- **Lettera**, ovvero se si desidera conservare la lettera in macchine virtuali dopo il failover è possibile impostare i criteri SAN per la macchina virtuale su **On**. Macchina virtuale dischi vengono automaticamente. [Per ulteriori](https://technet.microsoft.com/library/gg252636.aspx).
- **Indirizzare le richieste di client**, ovvero il ripristino del sito funziona con Azure il traffico Manager per indirizzare le richieste di client per l'applicazione dopo il failover.




## <a name="run-a-test-failover"></a>Eseguire test caso di errore

Quando si esegue caso di errore test verrà chiesto per selezionare impostazioni di rete del computer di replica di test. Si dispone di un numero di opzioni.  

**Opzione di failover test** | **Descrizione** | **Controllo failover** | **Dettagli**
---|---|---|---
**Impossibile su Azure, senza rete** | Non selezionare una destinazione rete Azure | Controlli failover testare macchina virtuale viene avviato come previsto in Azure | Tutte le macchine virtuali di test in un piano di recupero vengono aggiunti in un servizio cloud singola e possono connettersi tra loro<br/><br/>Computer non sono connessi a una rete Azure dopo il failover.<br/><br/>Gli utenti possono connettersi al computer di test con un indirizzo IP pubblico
**Impossibile su Azure, ovvero alla rete aziendale con** | Selezionare una destinazione rete Azure | Failover verifica la presenza di test computer connessi alla rete | Creare una rete Azure isolata dalla rete di produzione Azure e configurare l'infrastruttura per la macchina virtuale replicata a funzionare come previsto.<br/><br/>Alla subnet del computer virtuale di test è basata sulla subnet in cui il volume su macchina virtuale è previsto per connettersi a in caso di errore pianificato o non pianificato caso.
**Impossibile su un sito secondario di VMM, senza rete** | Non selezionare una rete macchine Virtuali | Failover verifica che i computer di test vengono creati.<br/><br/>Computer virtuale di test verrà creato nello stesso host host in cui si trova la macchina virtuale duplicata. Non verrà aggiunta al cloud in cui si trova la macchina virtuale duplicata. | <p>Il volume su computer non connesso a una rete.<br/><br/>Il computer possa essere connessi a una rete macchine Virtuali dopo che è stato creato
**Impossibile su un sito secondario di VMM, ovvero alla rete aziendale con** | Selezionare una rete esistente macchine Virtuali una | Failover verifica che vengano create macchine virtuali | Computer virtuale di test verrà creato nello stesso host host in cui si trova la macchina virtuale duplicata. Non verrà aggiunta al cloud in cui si trova la macchina virtuale duplicata.<br/><br/>Creare una rete macchine Virtuali che è isolati dalla rete di produzione<br/><br/>Se si sta utilizzando una rete basata su VLAN è consigliabile che si crea una rete logica separata (non utilizzata in produzione) in VMM a tale scopo. Questa rete logica viene utilizzata per creare le reti macchine Virtuali allo scopo di test failover.<br/><br/>La rete logica deve essere associata almeno una delle schede di rete di tutti i server di Hyper-V ospita macchine virtuali.<br/><br/>Per reti logiche VLAN, i siti di rete che aggiungere alla rete logica devono essere isolati.<br/><br/>Se si sta utilizzando una rete logica basata su virtualizzazione di rete di Windows, il ripristino del sito di Azure crea automaticamente reti macchine Virtuali isolate.
**Impossibile su un sito secondario di VMM, creare una rete** | Una rete di test temporanea verrà creata automaticamente in base all'impostazione che specificare nella **Rete logica** e i relativi siti rete correlati | Failover verifica che vengano create macchine virtuali | Usare questa opzione se il piano di ripristino Usa più di una rete macchine Virtuali. Se si sta utilizzando reti di virtualizzazione di rete di Windows, questa opzione può creare automaticamente reti macchine Virtuali con le stesse impostazioni (subnet e pool di indirizzi IP) nella rete del computer virtuale duplicata. Queste reti macchine Virtuali vengono cancellate automaticamente dopo il failover di test è stato completo.</p><p>Computer virtuale di test verrà creato nello stesso host host in cui si trova la macchina virtuale duplicata. Non verrà aggiunta al cloud in cui si trova la macchina virtuale duplicata.

>[AZURE.NOTE] L'indirizzo IP assegnato a una macchina virtuale durante il failover di test è uguale all'indirizzo IP da ricevere quando eseguendo caso di errore pianificato o non pianificato (partendo che l'indirizzo IP è disponibile nella rete failover test. Se lo stesso indirizzo IP non è disponibile nella rete failover test macchina virtuale verrà visualizzato un altro indirizzo IP è disponibile nella rete failover test.



### <a name="run-a-test-failover-from-on-premises-to-azure"></a>Eseguire test caso di errore da locale in Azure

Questa procedura viene descritto come eseguire test caso di errore per un piano di ripristino. In alternativa è possibile eseguire il failover per un singolo computer nella scheda **macchine virtuali** .

1. Scegliere **Il piano di ripristino** > *recoveryplan_name*. Fare clic su **Failover** > **Failover Test**.
2. Nella pagina **Confermare Test Failover** specificare come macchine duplicata verranno connesso a una rete Azure dopo il failover.
3. Se sta la mancata su Azure e la crittografia dei dati è abilitata per il cloud, nella **Chiave di crittografia** selezionare il certificato emesso quando è abilitata la crittografia dei dati durante l'installazione del Provider. 
4. Avanzamento failover nella scheda **processi** . Dovrebbe essere possibile vedere computer di replica di test nel portale di Azure.
5. È possibile accedere macchine duplicata in Azure dal sito locale avviano una connessione RDP alla macchina virtuale. porta 3389 dovranno essere aperto per l'endpoint per la macchina virtuale.
5. Al termine, quando il failover raggiunge la fase di **test completata** , fare clic su **Test completo** alla fine.
5. Nelle **note per** registrare e salvare le osservazioni associate failover test.
8. Fare clic su Pulisci automaticamente l'ambiente di testing **failover test è stata completata** . Termine di questa failover test verrà visualizzato lo stato di**omplete** C.

> [AZURE.NOTE] Se continua in caso di errore di test per più di due settimane verrà completata obbligatoriamente. Verranno eliminate eventuali elementi o macchine virtuali create automaticamente durante il failover di test.
  

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>Eseguire test caso di errore da un sito principale in locale in un sito secondario in locale

È necessario eseguire numerose operazioni per l'esecuzione di test caso di errore, incluse l'esecuzione di una copia del controller di dominio e posizionamento dei server DNS e DHCP test nel proprio ambiente di test. È possibile farlo in due modi:

- Se si desidera eseguire test caso di errore tramite una rete esistente, preparare Active Directory, DHCP e DNS in rete.
- Se si desidera eseguire test caso di errore utilizzando l'opzione per creare automaticamente le reti macchine Virtuali, aggiungere il passaggio manuale prima di gruppo 1 nel piano del ripristino che si desidera utilizzare per il failover test e aggiungere le risorse dell'infrastruttura di rete creata automaticamente prima di eseguire il test failover.

#### <a name="things-to-note"></a>Notare quanto

- Durante la replica a un sito secondario, il tipo di rete usato dal computer replica non deve corrispondere al tipo di rete logico utilizzata per failover test, ma alcune combinazioni potrebbero non funzionare. Se la replica utilizza DHCP e basate su VLAN isolamento, la rete macchina virtuale per la replica non è necessario un pool di indirizzi IP statico. In modo che mediante la virtualizzazione di rete di Windows per failover test procedura non funziona in quanto non pool di indirizzi sono disponibili. Test failover non funzionano anche se la rete replica è isolamento n e la rete di test è virtualizzazione di rete di Windows. In questo modo la rete di isolamento n non è disponibile subnet necessari per creare una rete di virtualizzazione di rete di Windows.
- Il modo in cui replica macchine virtuali connessi mappati reti macchine Virtuali dopo failover dipende dalla configurazione della rete macchine Virtuali nella console di VMM:
    - **Rete di macchine Virtuali configurato senza isolamento o isolamento VLAN**, ovvero se DHCP è definito per la rete macchine Virtuali, la macchina virtuale duplicata verrà connesso ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logico associato. La macchina virtuale verrà visualizzato il relativo indirizzo IP dal server DHCP disponibili. Non è necessario un pool di indirizzi IP statico definito per la rete macchine Virtuali di destinazione. Se un pool di indirizzi IP statico viene utilizzato per la rete macchine Virtuali la macchina virtuale duplicata verrà connesso ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logico associato. La macchina virtuale verrà visualizzato il relativo indirizzo IP del pool definito per la rete macchine Virtuali. Se un pool di indirizzi IP statico non è stato definito nella rete macchine Virtuali di destinazione, l'allocazione degli indirizzi IP non riuscirà. Server di origine e destinazione VMM che si desidera utilizzare per la protezione e ripristino, è necessario creare il pool di indirizzi IP.
    - **Rete di macchine Virtuali con Windows virtualizzazione di rete**, ovvero se una rete macchine Virtuali è configurata con questa impostazione per la rete macchine Virtuali di destinazione, è necessario definire un pool statico, indipendentemente dal fatto che sia stata configurata la rete macchine Virtuali di origine da utilizzare DHCP o un indirizzo IP statico pool indirizzi. Se si definisce DHCP, il server VMM destinazione fungere da server DHCP e fornire un indirizzo IP del pool definito per la rete macchine Virtuali di destinazione. Se per il server di origine è definita l'utilizzo di un pool di indirizzi IP statico, il server VMM destinazione allocazione di un indirizzo IP del pool di. In entrambi i casi, allocazione degli indirizzi IP non riuscirà se non è definito un pool di indirizzi IP statico.

#### <a name="run-test"></a>Eseguire test

Questa procedura viene descritto come eseguire test caso di errore per un piano di ripristino. In alternativa è possibile eseguire il failover per un singolo computer virtuali o server fisico nella scheda **macchine virtuali** .

1. Scegliere **Il piano di ripristino** > *recoveryplan_name*. Fare clic su **Failover** > **Failover Test**.
2. Nella pagina **Confermare Failover testare** specificare come deve essere connesso a reti macchine virtuali dopo il failover di test.
3. Avanzamento failover nella scheda **processi** . Quando il failover raggiunge la fase di** test completata** , fare clic su **Test completo** per completare il processo failover test.
4. Fare clic su **note** per registrare e salvare le osservazioni associate failover test.
4. Termine dell'operazione verificare che le macchine virtuali avviato correttamente.
5. Dopo aver verificato che macchine virtuali avviato correttamente, eseguire il failover test per pulire l'ambiente di isolamento. Se si è scelto di creare automaticamente le reti macchine Virtuali, pulizia Elimina tutte le macchine virtuali di test e testare le reti.

> [AZURE.NOTE] Se continua in caso di errore di test per più di due settimane verrà completata obbligatoriamente. Verranno eliminate eventuali elementi o macchine virtuali create automaticamente durante il failover di test.


#### <a name="prepare-dhcp"></a>Preparare DHCP

Se le persone coinvolte macchine virtuali un server DHCP test deve essere creato all'interno della rete sono isolata creato allo scopo di test failover failover test utilizzare DHCP.


### <a name="prepare-active-directory"></a>Preparazione di Active Directory
Per eseguire test caso di errore per la verifica delle applicazioni, sarà necessario una copia dell'ambiente di Active Directory di produzione nel proprio ambiente di test. Verranno illustrate sezione [test considerazioni sul failover per active directory](site-recovery-active-directory.md#considerations-for-test-failover) per altri dettagli. 


### <a name="prepare-dns"></a>Preparare il DNS

Preparare un server DNS per il failover test come indicato di seguito:

- **DHCP**, ovvero se macchine virtuali di utilizzare DHCP, aggiornare l'indirizzo IP del test DNS server DHCP test. Se si usa un tipo di rete di virtualizzazione di rete di Windows, il server VMM funge da server DHCP. Di conseguenza, l'indirizzo IP del DNS deve essere aggiornato in rete failover test. In questo caso, macchine virtuali verranno registrarsi al Server DNS pertinenti.
- **Indirizzo statico**, ovvero se macchine virtuali di usare un indirizzo IP statico, l'indirizzo IP del server DNS test deve essere aggiornato in rete failover di prova. Potrebbe essere necessario aggiornare il DNS con l'indirizzo IP macchine virtuali test. A questo scopo è possibile utilizzare lo script di esempio seguente: 

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-planned-failover-primary-to-secondary"></a>Eseguire pianificato in caso di errore (primario a quello secondario)

 Questa procedura viene descritto come eseguire pianificato in caso di errore per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale nella scheda **macchine virtuali** .

1. Prima di iniziare a verificare che tutte le macchine virtuali di cui su che si vuole avere esito negativo stata completata la replica iniziale.
2. Scegliere **Il piano di ripristino** > *recoveryplan_name*. Fare clic su **Failover** > **pianificato Failover**. 
3. Nella pagina **Confermare Failover pianificato **scegliere il percorso di origine e destinazione. Nota la direzione di failover.

    - Se tutti i server macchine virtuali si trovano nella posizione di origine o di destinazione precedente failover funzionano come previsto, i dettagli della direzione failover sono informativo. 
    - Se macchine virtuali sono attive in posizioni di origine e di destinazione, viene visualizzato il pulsante **Cambia orientamento** . Utilizzare questo pulsante per modificare e specificare la direzione in cui il failover dovrebbe essere eseguita.

5. Se sta la mancata su Azure e la crittografia dei dati è abilitata per il cloud, selezionare il certificato emesso quando è abilitata la crittografia dei dati durante l'installazione di Provider sul server VMM **Chiave di crittografia** . 
6. Quando si inizia a pianificati in caso di errore il primo passaggio consiste arrestare macchine virtuali di perdita di dati. È possibile eseguire l'avanzamento failover nella scheda **processi** . Se si verifica un errore failover (o in una macchina virtuale in uno script che è incluso nel piano del ripristino), si arresta failover pianificato di un piano di ripristino. È possibile avviare nuovamente il failover.
8. Dopo la creazione di macchine virtuali di replica siano disposte in un commit in sospeso. Fare clic su **Commit** per eseguire il commit il failover. 
9. Al termine della replica completare il macchine virtuali di avvio in corrispondenza della posizione secondario. 

## <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato

Questa procedura viene descritto come eseguire un failover non pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per un singolo computer virtuali o server fisico nella scheda **macchine virtuali** .

1. Scegliere **Il piano di ripristino** > *recoveryplan_name*. Fare clic su **Failover** > **Failover non pianificato**. 
3. Nella pagina **Confermare Failover non pianificato **, scegliere il percorso di origine e destinazione. Nota la direzione di failover.

    - Se tutti i server macchine virtuali si trovano nella posizione di origine o di destinazione precedente failover funzionano come previsto, i dettagli della direzione failover sono informativo. 
    - Se macchine virtuali sono attive in posizioni di origine e di destinazione, viene visualizzato il pulsante **Cambia orientamento** . Utilizzare questo pulsante per modificare e specificare la direzione in cui il failover dovrebbe essere eseguita.

4. Se sta la mancata su Azure e la crittografia dei dati è abilitata per il cloud, selezionare il certificato emesso quando è abilitata la crittografia dei dati durante l'installazione di Provider sul server VMM **Chiave di crittografia** . 
5. Selezionare **arrestare macchine virtuali e sincronizzare i dati più recenti** per specificare che il ripristino del sito arrestare la macchine virtuali protette e sincronizzare i dati in modo che la versione più recente dei dati verrà eseguita su. Se non si seleziona questa opzione o non è riuscito il failover sarà dal punto di ripristino disponibili più recente per la macchina virtuale.
6. È possibile eseguire l'avanzamento failover nella scheda **processi** . Si noti che, anche se si verificano errori durante un failover non pianificato, il piano di ripristino esegue fino al completamento.
7. Dopo il failover macchine virtuali sono in uno stato **eseguito il commit in sospeso** . Fare clic su **Commit** per eseguire il commit il failover.
8. Se si imposta la replica di utilizzare più punti di ripristino, in un punto di ripristino modifica è possibile scegliere di utilizzare un punto di ripristino che non è più tardi (ultime viene utilizzata per impostazione predefinita). Dopo aver eseguito il commit ulteriori punti di ripristino verranno rimossi.
9. Al termine della replica macchine virtuali di avvio e l'esecuzione in corrispondenza della posizione secondaria. Tuttavia non sono protetti o replica. Quando il sito principale è disponibile con la stessa infrastruttura sottostante, fare clic su **Replicare inverso** per avviare la replica inversa. In questo modo che tutti i dati viene replicato nel sito principale e che la macchina virtuale è pronta per failover nuovamente. Annullare la replica dopo un failover non pianificato riduzione del trasferimento di dati. Il trasferimento utilizzerà lo stesso metodo è configurato per accedere a impostazioni replica iniziale per il cloud.

## <a name="failback-from-secondary-to-primary"></a>Failback da secondaria a primaria

 Dopo aver failover dall'oggetto primario in posizione secondaria, replicate macchine virtuali non è protetto tramite il ripristino del sito e posizione secondaria ora funge da principale. Seguire queste procedure per avere esito negativo al sito principale originale. Questa procedura viene descritto come eseguire pianificato in caso di errore per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale nella scheda **macchine virtuali** .

1. Scegliere **Il piano di ripristino** > *recoveryplan_name*. Fare clic su **Failover** > **pianificato Failover**.
2. Nella pagina **Confermare Failover pianificato **scegliere il percorso di origine e destinazione. Nota la direzione di failover. Se il failover da primario usato come previsto e tutte le macchine virtuali sono in posizione secondaria che questo riguarda solo le informazioni.
3. Se sta non funziona proveniente da Azure selezionare impostazioni di sincronizzazione dei **Dati**:

    - **Sincronizzare i dati prima di failover (solo modifiche delta Sincronizza)**: questa opzione di ridurre al minimo il tempo di inattività per macchine virtuali Sincronizza senza arrestare loro. Esegue le operazioni seguenti:
        - Fase 1: Accetta snapshot della macchina virtuale in Azure e copiarlo negli host di Hyper-V in locale. Il computer continua in esecuzione in Azure.
        - Fase 2: Chiude la macchina virtuale in Azure in modo che nessun nuove modifiche che vi. Il set finale delle modifiche apportate vengono trasferite al server in locale e viene avviata la macchina virtuale locale.
    

    - **Sincronizzazione dati durante il failover solo (download completo)**, usare questa opzione se è in esecuzione su Azure da molto tempo. Questa opzione è più veloce poiché si prevede che la maggior parte del disco è stato modificato e non si desidera dedicare troppo tempo nel calcolo. Consente di eseguire il download del disco. È utile anche quando la macchina virtuale locale è stata eliminata.
    
    > [AZURE.NOTE] È consigliabile utilizzare questa opzione se si è in esecuzione Azure da un po' di tempo (o un mese) o la macchina virtuale locale è stata eliminata. Questa opzione non esegue calcoli checksum.
    
5. Se sta la mancata su Azure e la crittografia dei dati è abilitata per il cloud, selezionare il certificato emesso quando è abilitata la crittografia dei dati durante l'installazione di Provider sul server VMM **Chiave di crittografia** . 
5. Per impostazione predefinita è utilizzato l'ultimo punto di ripristino, ma in **Un punto di ripristino modifica** è possibile specificare un punto di ripristino diverso. 
6. Fare clic sul segno di spunta per avviare il failback.  È possibile eseguire l'avanzamento failover nella scheda **processi** . 
7. f è selezionata l'opzione per sincronizzare i dati prima del failover, una volta completato, la sincronizzazione dei dati iniziale e si è pronti per arrestare macchine virtuali di Azure, fare clic su **processi**  >  <planned failover job name> **Failover completo**. Arrestato machine Azure, trasferisca le ultime modifiche alla macchina virtuale locale e lo avvia.
8. È possibile registro nella macchina virtuale di convalida sarà disponibile come previsto. 
9. La macchina virtuale è in un commit in sospeso. Fare clic su **Commit** per eseguire il commit il failover.
10. Per completare il failback scegliere **Invertire replicare** allo scopo di proteggere il computer virtuale nel sito principale.



## <a name="failback-to-an-alternate-location"></a>Failback in un percorso alternativo

Se è stata distribuita protezione tra un [sito di Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md) è necessario possibilità di failback comuni per un'alternativa locale posizione. Ciò è utile se è necessario configurare di nuovo hardware locale. Ecco come si farebbe.

1. Se si sta configurando nuovo hardware installare Windows Server 2012 R2 e il ruolo Hyper-V nel server.
2. Creare un parametro di rete virtuale con lo stesso nome che erano presenti nel server originale.
3. Selezionare **Gli elementi protetti** -> **Gruppo protezione**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> si vuole avere esito negativo indietro e selezionare **Failover pianificato**.
4. In **Confermare Failover pianificato** selezionare **Crea locale virtuali computer se non esiste**. 
5. Nella **Casella Nome Host** selezionare il nuovo server di host Hyper-V in cui si desidera inserire la macchina virtuale.
6. Sincronizzazione dei dati è consigliabile che selezionare l'opzione **Sincronizza i dati prima di failover**. Ciò contribuisce a ridurre i tempi di inattività per macchine virtuali poiché Sincronizza senza arrestare loro. Esegue le operazioni seguenti:

    - Fase 1: Accetta snapshot della macchina virtuale in Azure e copiarlo negli host di Hyper-V in locale. Il computer continua in esecuzione in Azure.
    - Fase 2: Chiude la macchina virtuale in Azure in modo che nessun nuove modifiche che vi. Il set finale delle modifiche apportate vengono trasferite al server in locale e viene avviata la macchina virtuale locale.
    
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Al termine della sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale in Azure, fare clic su **processi** > <planned failover job> > **Failover completo**. Arrestato machine Azure, trasferisca le ultime modifiche alla macchina virtuale locale e lo avvia.
9. È possibile accedere al computer virtuale locale per verificare che il funzionamento come previsto. Fare clic su **Commit** per completare il failover.
10. Fare clic su **Cambia replicare** per iniziare a proteggere la macchina virtuale locale.

    >[AZURE.NOTE] Se si annulla il processo di failback durante il passaggio della sincronizzazione di dati, la macchina virtuale locale saranno in uno stato danneggiati. Ciò avviene perché le copie di sincronizzazione di dati i dati più recenti da macchine Virtuali di Azure dischi sul disco dati locale e configurata fino a quando la sincronizzazione completa, il disco dati potrebbero non essere in uno stato coerente. Se la macchina virtuale via locale viene avviata dopo la sincronizzazione di dati è stata annullata, potrebbe non avviato. Attivare nuovamente failover per completare la sincronizzazione di dati.
 
