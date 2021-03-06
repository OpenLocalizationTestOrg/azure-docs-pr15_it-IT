Questo articolo illustra un insieme di procedure per l'esecuzione di una Windows macchine () su Azure, prestando attenzione a sicurezza, disponibilità, gestibilità e scalabilità. 

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse di Azure] [ resource-manager-overview] e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Non è consigliabile usare una macchina virtuale singola per carichi di lavoro di produzione, poiché non esiste alcun i tempi contratto servizio () per singole macchine virtuali in Azure. Per ottenere il contratto di servizio, è necessario distribuire più macchine virtuali in un [set di disponibilità][availability-set]. Per ulteriori informazioni, vedere [esecuzione più macchine virtuali di Windows in Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Il provisioning di una macchina virtuale in Azure comporta parti più spostate rispetto a solo macchine Virtuali stesso. Sono presenti elementi di calcolo, rete e lo spazio di archiviazione.

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "elaborazione - singola pagina di macchine Virtuali.

![[0]][0]


- **Gruppo di risorse.** Un [_gruppo di risorse_] [ resource-manager-overview] è un contenitore che contiene le relative risorse. Creare un gruppo di risorse per mettere in attesa le risorse per questa macchina virtuale.

- **VM**. È possibile eseguire il provisioning di una macchina virtuale da un elenco di immagini pubblicate o da un file disco rigido virtuale (disco rigido virtuale) che si carica in archiviazione blob Azure.

- **Disco rigido del sistema operativo.** Il disco del sistema operativo è un disco rigido virtuale archiviato in [archiviazione Azure][azure-storage]. Questo significa che persiste anche se il computer host è inattivo.

- **Disco temporaneo.** La macchina virtuale viene creata con un disco temporaneo (la `D:` unità in Windows). Il disco è archiviato in un'unità fisica sul computer host. Si tratta _non_ salvati in archiviazione Azure e possono accedere al computer durante il riavvio di altri eventi del ciclo di vita macchine Virtuali. Utilizzare questo disco solo per i dati temporanei, ad esempio un file di pagina o scambiare.

- **Dischi di dati.** Un [disco dati] [ data-disk] è un disco rigido virtuale persistente utilizzato per i dati dell'applicazione. Dischi dati vengono archiviati in archiviazione Azure, ad esempio il disco del sistema operativo.

- **Rete virtuale (VNet) e subnet.** Ogni macchina virtuale in Azure viene distribuito in un VNet ulteriormente suddiviso in subnet.

- **Indirizzo IP pubblico.** È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale&mdash;, ad esempio su desktop remoto (RDP).

- **Interfaccia di rete (NIC)**. La scheda di rete consente la macchina virtuale comunicare con la rete virtuale.

- **Gruppo di sicurezza di rete (NSG)**. [NSG] [ nsg] viene utilizzata per consentire o impedire il traffico di rete alla subnet. È possibile associare un NSG con una scheda di rete singoli o con una subnet. Se si associa una subnet, le NSG regole per tutte le macchine virtuali di tale subnet.
 
- **Diagnostica.** Registrazione diagnostica è fondamentale per la gestione e risoluzione dei problemi della macchina virtuale.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="vm-recommendations"></a>Consigli macchine Virtuali

È consigliabile la serie DS e GS perché questi formati computer supportano [Lo spazio di archiviazione Premium][premium-storage]. Selezionare uno di questi formati computer a meno che non si dispone di un carico di lavoro specifico, ad esempio massime prestazioni. Per informazioni dettagliate, vedere [le dimensioni di macchina virtuale][virtual-machine-sizes]. Quando si sposta un carico di lavoro esistente in Azure, iniziare con le dimensioni di macchine Virtuali che corrisponde maggiormente ai server in locale. Quindi misura le prestazioni del carico di lavoro effettivo per quanto riguarda CPU e memoria disco interdipendenze operazioni al secondo (IOPS) e, se necessario, modificare le dimensioni. Inoltre, se sono necessarie più schede di rete, tenere presente del limite di NIC per ogni dimensioni.  

Durante il provisioning di macchine Virtuali e altre risorse, è necessario specificare un percorso. In generale, scegliere un percorso più vicino all'utenti interni o clienti. Tuttavia, non tutte le dimensioni di macchine Virtuali potrebbero essere disponibili in tutte le posizioni. Per informazioni dettagliate, vedere [servizi per area geografica][services-by-region]. Per elencare le dimensioni di macchine Virtuali disponibili in un percorso specificato, eseguire il seguente comando Azure interfaccia riga di comando (CLI):

```
    azure vm sizes --location <location>
```

Per informazioni sulla scelta di un'immagine di macchine Virtuali pubblicata, vedere [Naviga e selezionare macchina virtuale Azure immagini][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Consigli su disco e lo spazio di archiviazione

Per garantire prestazioni ottimali disco, è consigliabile [Premium archiviazione][premium-storage], che archivia i dati (SSD). Costo è basato sulla dimensione del disco provisioning. Secondo e velocità dipendono anche la dimensione del disco, pertanto durante il provisioning di un disco, valutare la possibilità di tutti i tre fattori (capacità IOPS e velocità). 

Un account di archiviazione supportino macchine virtuali da 1 a 20.

Aggiungere uno o più dischi di dati. Quando si crea un nuovo disco rigido virtuale, è formattato. Accedere a macchine Virtuali per formattare il disco.

Se si dispone di un numero elevato di dischi di dati, tenere presente i limiti dei / o totali dell'account di archiviazione. Per ulteriori informazioni, vedere [Limiti del disco macchina virtuale][vm-disk-limits].

Per ottimizzare le prestazioni, creare un account di archiviazione separata per mettere in attesa registri diagnostici. Un account di archiviazione in locale ridondanti (LRS) standard è sufficiente per registri diagnostici.

Se possibile, installare applicazioni su un disco di dati, non il sistema operativo. Tuttavia, alcune applicazioni legacy potrebbero essere necessario installare componenti sull'unità c. In questo caso, è possibile [ridimensionare il disco del sistema operativo] [ resize-os-disk] tramite PowerShell.

### <a name="network-recommendations"></a>Suggerimenti per reti

L'indirizzo IP pubblico può essere statica o dinamica. Il valore predefinito è dinamico.

- Prenotare un [indirizzo IP statico] [ static-ip] se è necessario un indirizzo IP fisso che non è possibile modificare &mdash; ad esempio, se è necessario creare un record DNS o necessario l'indirizzo IP di essere whitelisted.

- È anche possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME] [ cname-record] in DNS che indichi il FQDN. Per ulteriori informazioni, vedere [creare un nome di dominio completo nel portale di Azure][fqdn].

NSGs tutti contengono un set di [regole predefinito][nsg-default-rules], tra cui una regola che si blocca tutto il traffico Internet. Non è possibile eliminare le regole predefinite, ma possono sostituite da altre regole. Per consentire il traffico Internet, creare regole che consentano il traffico in ingresso a porte specifiche &mdash; , ad esempio, la porta 80 per HTTP.  

Per abilitare RDP, aggiungere una regola NSG che consente il traffico in ingresso alla porta TCP 3389.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile adattare una macchina virtuale verso l'alto o verso il basso per [modificare la dimensione di macchine Virtuali][vm-resize]. 

Per ridimensionare le orizzontalmente, inserire macchine virtuali di due o più in una disponibilità impostare dietro un bilanciamento del carico. Per informazioni dettagliate, vedere [esecuzione più macchine virtuali di Windows in Azure][multi-vm].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Come illustrato in precedenza, non esiste alcun contratto di servizio per una macchina virtuale singola. Per ottenere il contratto di servizio, è necessario distribuire più macchine virtuali in un set di disponibilità.

La macchina virtuale potrebbe incidono [manutenzione pianificata] [ planned-maintenance] [manutenzione non pianificata]o[manage-vm-availability]. È possibile utilizzare [macchine Virtuali riavviare registri] [ reboot-logs] per determinare se il riavvio macchine Virtuali è stato causato dalla manutenzione pianificata.

Dischi rigidi virtuali sono supportati da [Archiviazione Azure][azure-storage], replica per la durata e la disponibilità.

Per evitare perdite di dati accidentali durante il normale funzionamento (ad esempio a causa di errore utente), è necessario implementare anche i backup in un momento, tramite [snapshot blob] [ blob-snapshot] o un altro strumento.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

**Gruppi di risorse.** Inserire risorse strettamente che condividono lo stesso ciclo di vita in un [gruppo di risorse]stesso[resource-manager-overview]. Gruppi di risorse consentono di distribuire e monitorare le risorse a livello di gruppo e rollup fatturazione costi del gruppo di risorse. È inoltre possibile eliminare le risorse come un insieme, è molto utile per distribuzioni di prova. Assegnare risorse nomi significativi. Che rende più facile individuare una risorsa specifica e comprendere il proprio ruolo. Vedere [consigliate convenzioni di denominazione per le risorse Azure][naming conventions].

**Diagnostica macchine Virtuali.** Abilita il monitoraggio e diagnostica, inclusi metriche di integrità di base, i registri dell'infrastruttura di diagnostica e [Avvio diagnostica][boot-diagnostics]. Avvio diagnostica consentono di diagnosticare un errore di avvio, se la macchina virtuale ottiene in uno stato non avvio. Per ulteriori informazioni, vedere [abilitare il monitoraggio e diagnostica][enable-monitoring]. Utilizzare l' [Insieme di Azure Log] [ log-collector] estensione per raccogliere i registri di piattaforma Azure e caricarli allo spazio di archiviazione Azure.   

Il comando CLI seguente consente di diagnostica:

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Interrompere una macchina virtuale.** Azure viene fatta distinzione tra stati "Arrestato" e "Deallocated". Addebitate quando lo stato di macchine Virtuali è "interrotta". Addebitate non quando la macchina virtuale rilasciato.

Usare il seguente comando CLI rilasciare una macchina virtuale:

```
    azure vm deallocate <resource-group> <vm-name>
```

Pulsante **Interrompi** nel portale di Azure rilascia anche la macchina virtuale. Tuttavia, se si arresta tramite il sistema operativo mentre è connesso, la macchina virtuale viene interrotto ma _non_ rilasciato, pertanto verrà ancora addebitata.

**Eliminare una macchina virtuale.** Se si elimina una macchina virtuale, i dischi rigidi virtuali non vengono eliminati. Questo significa che è possibile eliminare la macchina virtuale senza perdere dati. Tuttavia, verrà comunque addebitata per lo spazio di archiviazione. Per eliminare il disco rigido virtuale, eliminare il file dallo [spazio di archiviazione blob][blob-storage].

Per evitare eliminazioni accidentali, utilizzare un [blocco di risorsa] [ resource-lock] bloccare l'intera risorsa gruppo o un blocco singole risorse, ad esempio la macchina virtuale. 

## <a name="security-considerations"></a>Considerazioni sulla protezione

Utilizzare [Il Centro protezione di Azure] [ security-center] per ottenere una visualizzazione dello stato di protezione delle risorse di Azure centrale. Centro protezione esegue il monitoraggio potenziali problemi di sicurezza come sistema aggiorna, antimalware e offre una panoramica completa dell'integrità di protezione della distribuzione. 

- Centro protezione è configurato per abbonamento Azure. Attivare la raccolta dei dati di sicurezza come descritto in [Utilizzare il Centro protezione].

- Quando è abilitata la raccolta di dati, nel Centro sicurezza digitalizza automaticamente macchine virtuali creati in tale sottoscrizione.

**Gestione delle patch.** Se abilitata, Security Center controlla se protezione e aggiornamenti critici mancano. Usare [le impostazioni di criteri di gruppo] [ group-policy] in macchine Virtuali per abilitare gli aggiornamenti automatici del sistema.

**Antimalware.** Se è attivato, Centro protezione verifica se è installato software antimalware. Utilizzare Security Center per installare il software antimalware all'interno del portale Azure.

Utilizzare [il controllo dell'accesso basato sui ruoli] [ rbac] (RBAC) per controllare l'accesso alle risorse Azure che si distribuiscono. RBAC consente di assegnare i ruoli di autorizzazione per i membri del team di attrezzi. Ad esempio, il ruolo di lettore possa visualizzare le risorse di Azure ma non creare, gestire o eliminarli. Alcuni ruoli sono specifici per i tipi di risorse Azure specifico. Ad esempio, il ruolo di collaboratore macchina virtuale possibile riavviare o rilasciare una macchina virtuale, reimpostare la password di amministratore, creare una nuova macchina virtuale e così via. Altri [ruoli RBAC incorporati] [ rbac-roles] che possono essere utili per l'architettura di riferimento includono [DevTest esercitazioni utente] [ rbac-devtest] e [Collaboratori di rete][rbac-network]. Un utente può essere assegnato ai ruoli più ed è possibile creare ruoli personalizzati per ulteriormente diffusamente autorizzazioni.

> [AZURE.NOTE] RBAC non limita le azioni che è possibile eseguire un utente ha eseguito l'accesso in una macchina virtuale. Le autorizzazioni dipendono dal tipo di account nel sistema operativo guest.   

Per reimpostare la password di amministratore locale, eseguire la `vm reset-access` comando CLI Azure.

```
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
```

Usare i [registri di controllo] [ audit-logs] per visualizzare il provisioning di azioni e altri eventi macchine Virtuali.

Valutare la possibilità di [Azure disco crittografia] [ disk-encryption] se è necessario crittografare il disco del sistema operativo e dati. 

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una [distribuzione] [ github-folder] per un riferimento è disponibile l'architettura che illustra le procedure consigliate. L'architettura di riferimento include una rete virtuale (VNet), gruppo di sicurezza di rete (NSG) e una singola macchine ().

Esistono diversi modi per distribuire l'architettura di riferimento. Il modo più semplice consiste nell'eseguire la procedura seguente: 

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra".  
[![Distribuire Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-single-vm-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Selezionare il **Tipo di sistema operativo** dall'elenco a discesa, di **windows**.
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Si consiglia di immediatamente modificare entrambi i file di parametro includono un nome utente amministratore hardcoded e la password. Fare clic su macchine Virtuali denominata `ra-single-vm0 `nel portale di Azure. Quindi fare clic su **reimpostare la password** nel e **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per mantenere il nuovo nome utente e la password.

Per informazioni su altri modi per distribuire l'architettura di riferimento, vedere il file Leggimi in [macchine virtuali di singola indicazioni][github-folder]] Github cartella. 

## <a name="customize-the-deployment"></a>Personalizzare la distribuzione

Se è necessario modificare la distribuzione in base alle proprie esigenze, seguire le istruzioni in [file Leggimi][github-folder]. 

## <a name="next-steps"></a>Passaggi successivi

Affinché il [contratto di servizio per macchine virtuali] [ vm-sla] per applicare, è necessario distribuire due o più istanze di un set di disponibilità. Per ulteriori informazioni, vedere [esecuzione macchine virtuali di più su Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/en-us/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[security-center]: https://azure.microsoft.com/en-us/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Utilizzare il Centro protezione]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Singolo architettura macchine Virtuali di Windows Azure"
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[blocks]: https://github.com/mspnp/template-building-blocks
