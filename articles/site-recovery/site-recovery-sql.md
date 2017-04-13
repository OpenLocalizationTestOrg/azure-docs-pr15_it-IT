<properties 
    pageTitle="Proteggere SQL Server con il ripristino di emergenza di SQL Server e il ripristino del sito di Azure | Microsoft Azure" 
    description="In questo articolo viene descritto come replicare SQL Server tramite le funzionalità di emergenza Azure sito ripristino di SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteggere SQL Server con il ripristino di emergenza di SQL Server e il ripristino del sito di Azure 


Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md).

 In questo articolo viene descritto come proteggere SQL Server back-end di un'applicazione utilizzando una combinazione delle tecnologie SQL Server BCDR e il ripristino del sito di Azure. È necessario avere una buona comprensione del ripristino di emergenza di SQL Server (failover cluster, disponibilità AlwaysOn, del database che rispecchiano, la distribuzione dei log) e del ripristino del sito di Azure, prima di distribuire gli scenari descritti in questo articolo.



## <a name="overview"></a>Panoramica

Molti carichi di lavoro usare SQL Server come base. Applicazioni, ad esempio SharePoint, Dynamics e SAP usare SQL Server per implementare i servizi di dati.  SQL Server di distribuzione delle applicazioni in diversi modi diversi:

- **Server SQL autonomo**: SQL Server e tutti i database sono ospitati su un singolo computer (fisica o virtuale). Quando virtualizzato, host cluster viene utilizzato per la disponibilità elevata locale. Disponibilità livello guest non è implementata.
- **Failover cluster istanze di SQL Server (sempre sul cliente)**: due o più nodi di istanze di SQL server con dischi condivisi sono configurati in un cluster di Failover di Windows. Se nessuna delle istanze cluster è verso il basso, il cluster potrebbe non riuscire su SQL Server a un'altra istanza. Questa procedura viene generalmente utilizzata per la disponibilità elevata in un sito principale. Non proteggere contro guasti o interruzione del livello di archiviazione condivisa. Disco condiviso può essere implementata mediante ISCSI fibra o VHDx condiviso.
- **Sempre nella disponibilità gruppi SQL**: In questa configurazione, due nodi sono imposta nella condivisa nulla cluster con database di SQL Server configurati in un gruppo di disponibilità con l'icona del replica e failover automatico.

Nelle edizioni Enterprise, SQL Server vengono forniti anche emergenza nativo tecnologie di ripristino di ripristino dei database in un sito remoto. In questo articolo è verrà sfruttare e integrare con le tecnologie di ripristino di emergenza SQL native: 

- SQL sempre nella disponibilità gruppi per il ripristino di emergenza per SQL Server 2012 o 2014 Enterprise Edition.
- SQL database che rispecchiano in modalità di protezione alta per SQL Server Standard edition (qualsiasi versione) o per SQL Server 2008 R2.


Il ripristino del sito possa proteggere SQL Server, come riepilogato nella tabella.

 |**Locale a locale** | **In locale in Azure** 
---|---|---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì 
**Server fisico** | Sì | Sì


## <a name="support-and-integration"></a>Supporto e integrazione

Queste versioni di SQL Server sono supportate gli scenari in questo articolo:


- Standard e SQL Server 2014 Enterprise
- Standard ed Enterprise SQL Server 2012
- Standard e SQL Server 2008 R2 Enterprise


Il ripristino del sito può essere integrato con native tecnologie SQL Server BCDR riepilogate nella tabella seguente per offrire una soluzione di ripristino di emergenza.

**Caratteristica** |**Dettagli** | **Versione di SQL Server** 
---|---|---
**Gruppo di disponibilità AlwaysOn** | Eseguire più istanze di autonoma di SQL Server ogni in un cluster di failover contenente più nodi.<br/><br/>I database possono essere raggruppati in gruppi di failover che possono essere copiati (speculare) in istanze di SQL Server in modo che non sia necessario archiviazione condivisa.<br/><br/>Consente di emergenza tra un sito principale e una o più siti secondari. Due nodi possono essere configurati in condivisa nulla cluster con database di SQL Server configurato in un gruppo di disponibilità con l'icona del replica e failover automatico. | SQL Server 2014 & 2012 Enterprise edition
**Failover cluster (AlwaysOn FCI)** | SQL Server consente di sfruttare failover di Windows per disponibilità di carichi di lavoro di SQL Server locale.<br/><br/>In un cluster di failover sono configurati nodi istanze di SQL Server in esecuzione con dischi condivisi. Se un'istanza verso il basso negativo il cluster passa a uno diverso.<br/><br/>Il cluster non proteggersi da errori o interruzioni di archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, fibra, o condivise VHDXs. | Edizioni di SQL Server Enterprise<br/><br/>SQL Server Standard edition (limitata a solo due nodi)
**Database che rispecchiano (modalità di protezione alta)** | Consente di proteggere un solo database a una singola copia secondario. Disponibile in entrambi alta protezione (icona del) e prestazioni delle modalità di replica (asincrono). Non richiede un cluster di failover. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise tutte le edizioni
**Autonoma SQL Server** | SQL Server e database sono ospitati in un unico server (fisico o virtuale). Host cluster viene utilizzato per la disponibilità elevata se il server virtuale. Nessun livello guest disponibilità. | Enterprise o Standard

## <a name="deployment-recommendations"></a>Suggerimenti distribuzione


In questa tabella sono riepilogate le indicazioni per l'integrazione delle tecnologie SQL Server BCDR con il ripristino del sito.

**Versione** |**Edition** | **Distribuzione** | **Locale a locale** | **Locale in Azure** 
---|---|---|---|---
SQL Server 2012 o 2014 | Enterprise | Istanza del cluster failover | Disponibilità AlwaysOn | Disponibilità AlwaysOn
 | Enterprise | Disponibilità AlwaysOn per disponibilità | Disponibilità AlwaysOn | Disponibilità AlwaysOn
 | Standard | Istanza di cluster failover (cliente) | Replica di ripristino del sito con speculare locale | Replica di ripristino del sito con speculare locale
 | Enterprise o Standard | Autonomo | Replica di ripristino di siti | Replica di ripristino di siti
SQL Server 2008 R2 | Enterprise o Standard | Istanza di cluster failover (cliente) | Replica di ripristino del sito con speculare locale | Replica di ripristino del sito con speculare locale
 | Enterprise o Standard | Autonomo | Replica di ripristino di siti | Replica di ripristino di siti
SQL Server (qualsiasi versione) | Enterprise o Standard | Istanza cluster failover - applicazione DTC | Replica di ripristino di siti | Non è supportata


## <a name="deployment-prerequisites"></a>Prerequisiti per la distribuzione

Ecco cosa occorre prima di iniziare:

- Una distribuzione in locale di SQL Server che esegue una versione supportata di SQL Server. In genere è necessario anche un server Active Directory per SQL server.
- Prerequisiti per lo scenario che si desidera distribuire. Prerequisiti sono disponibili in ogni articolo di distribuzione. Vengono forniti collegamenti a queste nella [Panoramica sul ripristino di siti](site-recovery-overview.md).
- Per configurare l'esecuzione del ripristino di Azure, è necessario eseguire lo strumento di [Valutazione di Azure macchina virtuale](http://www.microsoft.com/download/details.aspx?id=40898) in macchine virtuali di SQL Server per assicurarsi che siano compatibili con Azure e il ripristino del sito.


## <a name="set-up-active-directory"></a>Configurare Active Directory

È necessario Active Directory nel sito secondario ripristino per SQL Server per il corretto funzionamento. sono disponibili due opzioni:

- **Aziende di piccole dimensioni**, ovvero se si dispone di un numero limitato di applicazioni e un singolo controller di dominio per il sito locale e si vuole avere esito negativo sull'intero sito, è consigliabile utilizzare il ripristino del sito repication replicare controller di dominio al Data Center secondario o su Azure.

- **Medie e grandi aziende**, ovvero se si dispone di un numero elevato di applicazione, si esegue un insieme di strutture di Active Directory e si desidera eseguire il nuovo applicazione o un carico di lavoro, è consigliabile impostare un controller di dominio in Data Center del secondario o in Azure. Si noti che se si usa disponibilità AlwaysOn per recuperare in un sito remoto è consigliabile che impostare un altro controller di dominio aggiuntivo nel sito secondario o Azure, da utilizzare per l'istanza di SQL Server recuperato.

Le istruzioni fornite in questo documento presuppongono che un controller di dominio sia disponibile nella posizione secondaria. [Per ulteriori](site-recovery-active-directory.md) informazioni sulla protezione di Active Directory con il ripristino del sito.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Integrare la protezione con SQL Server Always-On (in locale in Azure)


Il ripristino del sito supporta AlwaysOn SQL. Se si è creato un gruppo di disponibilità SQL con una macchina virtuale Azure impostato come 'Secondario' è quindi possibile utilizzare il ripristino del sito per gestire il failover dei gruppi di disponibilità. 

>[AZURE.NOTE] Questa funzionalità è attualmente in anteprima e disponibile quando il server di host Hyper-V in Data Center del principale vengono gestito nel cloud VMM e quando il programma di installazione di VMware viene gestito da un [Server di configurazione](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Attualmente questa funzionalità non è disponibile nel portale di Azure nuovo.

#### <a name="prerequisites"></a>Prerequisiti

Ecco cosa occorre integrare SQL AlwaysOn con il ripristino del sito:

- Un Server SQL locale (server autonomo o un cluster di failover).
- Uno o più macchine virtuali Azure con SQL Server
- Configurare un gruppo di disponibilità SQL la tra un locale SQL Server e SQL Server in esecuzione in Azure
- Gestione remota di PowerShell deve essere attivato nel computer di SQL Server locale. Il server VMM o il Server di configurazione dovrebbe essere possibile effettuare chiamate remote PowerShell di SQL Server.
- Un account utente deve essere aggiunto in SQL Server locale in questi gruppi di utenti SQL con almeno le autorizzazioni:
    - Modifica gruppo di disponibilità: le autorizzazioni [qui](https://msdn.microsoft.com/library/hh231018.aspx)e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - autorizzazioni[qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Nel Server VMM è necessario creare un account RunAs o un account deve essere creato sul Server di configurazione utilizzando il CSPSConfigtool.exe per l'utente indicata nel passaggio precedente 
- Il modulo SQL PS deve essere installato su SQL Server locale e in macchine virtuali di Azure
- Agente di macchine Virtuali deve essere installate macchine virtuali in esecuzione in Azure
- NTAUTHORITY\System deve avere seguendo le autorizzazioni in SQL Server in macchine virtuali in Azure:
    - MODIFICARE il gruppo di disponibilità - autorizzazioni [qui](https://msdn.microsoft.com/library/hh231018.aspx)e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - autorizzazioni [qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Passaggio 1: Aggiungere un Server SQL


1. Fare clic su **Aggiungi SQL** per aggiungere un nuovo di SQL Server. 

    ![Aggiungere SQL](./media/site-recovery-sql/add-sql.png)

2. **Configurare**le impostazioni di SQL > **nome** specificare un nome descrittivo per fare riferimento a SQL Server.
3. **In SQL Server (FQDN)** specificare il nome di dominio completo dell'origine SQL Server che si desidera aggiungere. In caso di SQL Server è installato in tal caso, quindi fornire FQDN del cluster e non di uno dei nodi del cluster.  
4. **Nell'Istanza di SQL Server** scegliere l'istanza predefinita oppure specificare il nome dell'istanza personalizzato.
5. Selezionare un server VMM nel **Server di gestione** o configurazione Server registrato nell'archivio del ripristino del sito. Il ripristino del sito Usa questo server di gestione di comunicare con SQL Server.
6. In **esecuzione come Account** specificare il nome di un account RunAs creato nel server VMM specificato oppure con l'Account che è stato creato nel Configuraaaon Server. L'account usato per accedere a SQL Server e deve disporre delle autorizzazioni di lettura e Failover sui gruppi di disponibilità del computer SQL Server.

    ![Finestra di dialogo SQL Aggiungi](./media/site-recovery-sql/add-sql-dialog.png)

Dopo l'aggiunta di SQL Server verrà visualizzato nella scheda **SQL Server** . 

![Elenco SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Passaggio 2: Aggiungere un gruppo di disponibilità SQL

1. Dopo avere SQL Server computer viene aggiunto che il passaggio successivo consiste nell'aggiungere i gruppi di disponibilità per il ripristino del sito. A tale scopo, eseguire il drill-all'interno di SQL Server aggiunte nel passaggio precedente e fare clic su Aggiungi gruppo disponibilità SQL. 

    ![Aggiungere AG SQL](./media/site-recovery-sql/add-sqlag.png)

2. Gruppo di disponibilità SQL possibile replicare a uno o più macchine virtuali di Azure. Aggiunta quando viene chiesto di specificare il nome e un abbonamento della macchina virtuale Azure in cui si desidera il gruppo di disponibilità un errore per il ripristino del sito del gruppo di disponibilità sql.

    ![Finestra di dialogo SQL AG aggiunta](./media/site-recovery-sql/add-sqlag-dialog.png)

3. Nell'esempio precedente diventerà disponibilità gruppo DB1-AG primario su macchina virtuale SQLAGVM2 in esecuzione all'interno di abbonamento DevTesting2 in caso di errore. 

>[AZURE.NOTE] Solo i gruppi di disponibilità di principale in SQL Server aggiunte nel passaggio precedente sono disponibili da aggiungere per il ripristino del sito. Se è stata effettuata una principale gruppo di disponibilità in SQL Server o se è stato aggiunto più gruppi di disponibilità in SQL Server dopo l'aggiunta, aggiornamento utilizzando l'opzione di aggiornamento disponibile in SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Passaggio 3: Creare un piano di ripristino

Il passaggio successivo consiste nel creare un piano di ripristino utilizzando macchine virtuali e i gruppi di disponibilità. Selezionare le stesse Server VMM Server di configurazione usato nel passaggio 1 come origine e di Microsoft Azure come destinazione.

![Creare piano di ripristino](./media/site-recovery-sql/create-rp1.png)

![Creare piano di ripristino](./media/site-recovery-sql/create-rp2.png)

Nell'esempio l'applicazione di Sharepoint è costituito da 3 macchine virtuali di utilizzare un gruppo di disponibilità SQL come relativa back-end. In questo piano di ripristino che è possibile selezionare sia la categoria di disponibilità la macchina virtuale che costituiscono l'applicazione. 

È possibile personalizzare ulteriormente il piano di ripristino spostando macchine virtuali ai gruppi di failover diverso per l'ordine di failover di sequenza. Gruppo di disponibilità viene sempre terminato prima come può essere utilizzato come un back-end di qualsiasi applicazione. 

![Personalizzare il piano di ripristino](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Passaggio 4: Esito negativo su

Dopo l'aggiunta di un gruppo di disponibilità di un piano di ripristino, sono disponibili opzioni diverse failover.

Failover | Dettagli
--- | ---
**Failover pianificato** | Failover pianificato non implica dati perdita caso di errore. Per ottenere la modalità di disponibilità del gruppo di disponibilità che SQL prima di tutto è impostato su sincrono e quindi caso di errore viene attivata per rendere il gruppo di disponibilità principale al computer virtuale fornito durante l'aggiunta di gruppo della disponibilità per il ripristino del sito. Una volta completato il failover, modalità di disponibilità è impostata per lo stesso valore che venisse eseguito prima failover pianificato è stato attivato.
**Failover non pianificato** | Failover non pianificato causando in perdite di dati. Durante l'attivazione failover non pianificato non è cambiata la modalità di disponibilità del gruppo di disponibilità e di renderlo principale al computer virtuale fornito durante l'aggiunta di gruppo della disponibilità per il ripristino del sito. Una volta failover non pianificato e il server locale che esegue SQL Server sia disponibile nuovamente, invertire la replica deve essere verrà attivata per il gruppo di disponibilità. Si noti che questa azione non è disponibile nel piano di ripristino ed è possibile eseguire nel gruppo di disponibilità di SQL in base alla tabulazione SQL Server
**Test failover** | Test failover per il gruppo di disponibilità SQL non è supportato. Se si attiva Failover Test di un piano di ripristino contenente il gruppo di disponibilità di SQL, failover da ignorati per il gruppo di disponibilità.


Valutare la possibilità di queste opzioni failover.

Opzione | Dettagli
--- | ---
**Opzione 1** | 1. eseguire test caso di errore dell'applicazione e livelli front-end.<br/><br/>2. aggiornare il livello di applicazione per accedere alla copia duplicata in modalità di sola lettura ed eseguire un test di sola lettura dell'applicazione.
**Opzione 2** | 1. creare una copia dell'istanza di macchina virtuale di SQL Server replica (Usa Clona VMM per da sito o il Backup di Azure) e visualizzata in una rete di prova<br/><br/> 2. eseguire il test failover utilizzando il piano di ripristino.

Passaggio 5: Esito negativo indietro

Se si desidera rendere il gruppo di disponibilità nuovamente principale in SQL Server locale è possibile eseguire questa operazione attivazione Failover pianificato nel piano di ripristino e scegliendo la direzione da Microsoft Azure al Server VMM locale.

>[AZURE.NOTE] Dopo un failover non pianificato replica inversa deve essere attivata per il gruppo di disponibilità per riprendere la replica. Fino a quando questa operazione viene eseguita la replica rimane.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Proteggere i computer senza un Server VMM o un Server di configurazione

Per gli ambienti che non sono gestiti da un Server VMM o un Server di configurazione, è possibile utilizzare Azure automazione runbook per configurare script caso di errore di gruppi di disponibilità SQL. Di seguito sono riportati i passaggi per configurare che:

1.  Creare un file locale per lo script di esito negativo su un gruppo di disponibilità. Questo script di esempio specifica un percorso per il gruppo di disponibilità nella replica di Azure e non l'occorrenza replica sopra. Questo script verrà eseguito su SQL Server replica virtual machine passando è con l'estensione di script personalizzati.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Caricare lo script in un blob in un account di archiviazione Azure. Utilizzare questo esempio:

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Creare un runbook automazione Azure per richiamare gli script computer SQL Server replica virtuale in Azure. Usare questo script di esempio per eseguire questa operazione. [Altre informazioni](site-recovery-runbook-automation.md) sull'uso di automazione runbook nei piani di ripristino. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Quando si crea un piano di ripristino per l'applicazione di aggiungere un passaggio di script pre-"raggruppare 1 avvio" che richiama runbook automazione di gruppi di disponibilità.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Integrare la protezione con SQL AlwaysOn (in locale in locale)

Se SQL Server utilizza gruppi di disponibilità per la disponibilità elevata o un'istanza di cluster failover, è consigliabile utilizzare gruppi di disponibilità del sito di ripristino anche. Si noti che questa guida per le applicazioni che non utilizzano transazioni distribuite.

1. [Configura database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
2. Creare una nuova rete virtuale nel sito secondario.
3. Configurare una connessione VPN da sito tra la nuova rete virtuale e il sito principale.
4. Creare una macchina virtuale nel sito di ripristino e installare SQL Server su di esso.
5. Estendere la disponibilità AlwaysOn esistente al nuovo computer virtuale di SQL Server. Configurare questa istanza di SQL Server come una copia di replica asincrono.
6. Creare un comunicare ascoltatore gruppo disponibilità o aggiornare comunicare ascoltatore esistente per includere la macchina virtuale replica asincrono.
7. Verificare che la farm di applicazione sia il programma di installazione comunicare ascoltatore. Se il programma di installazione utilizzando il nome del server di database, aggiornare in modo da utilizzare comunicare ascoltatore in modo che non è necessario riconfigurare dopo il failover.

Per le applicazioni che utilizzano transazioni distribuite è recommendation si utilizza [Il ripristino del sito con replica SAN](site-recovery-vmm-san.md) o [replica da sito server VMWare/fisici](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerazioni sul piano di ripristino

1. Aggiungere questo script di esempio per la raccolta VMM nei siti principali e secondarie.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando si crea un piano di ripristino per l'applicazione di aggiungere un passaggio di script pre-"raggruppare 1 avvio" che richiama lo script di esito negativo dei gruppi di disponibilità.



## <a name="protect-a-standalone-sql-server"></a>Proteggere un Server SQL autonomo

In questa configurazione si consiglia di che utilizzare replica il ripristino del sito per proteggere il computer di SQL Server. La procedura esatta dipenderà se SQL Server è impostato come una macchina virtuale o server fisico e se si desidera replicare in Azure o secondario locale del sito. È possibile ottenere le istruzioni per tutti gli scenari di distribuzione nella [Panoramica sul ripristino di siti](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Proteggere un cluster di SQL Server (Standard o 2008 R2)

Per un cluster di SQL Server Standard edition o SQL Server 2008 R2 è consigliabile che utilizzare replica il ripristino del sito per proteggere SQL Server.

### <a name="on-premises-to-on-premises"></a>Locale a locale

- Se l'applicazione utilizza transazioni distribuite è consigliabile che si distribuisce [Il ripristino del sito con replica SAN](site-recovery-vmm-san.md) per un ambiente di Hyper-V e [VMware/fisici server a cui VMware](site-recovery-vmware-to-vmware.md) per ambiente VMware.

- Per le applicazioni non DTC, sfruttare l'approccio sopra per ripristinare il cluster come server autonomo sfruttando applet locale DB speculare.

### <a name="on-premises-to-azure"></a>In locale in Azure

Il ripristino del sito non supporta supporto cluster guest durante la replica in Azure. SQL Server non offrono una soluzione di ripristino di emergenza conveniente per Standard edition. Si consiglia di proteggere il cluster di SQL Server locale a un Server SQL autonomo e ripristinare in Azure.


1. Configurare un'istanza di SQL Server autonomo aggiuntive nel sito locale.
2. Configurare l'istanza da utilizzare come speculare per i database che necessitano di protezione. Configurare speculare in modalità di protezione alta.
3.  Configurare il ripristino del sito nel sito locale in base all'ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware/fisici server](site-recovery-vmware-to-azure-classic.md).
4.  Utilizzare la replica il ripristino del sito per replicare la nuova istanza di SQL Server Azure. Si tratta di una copia speculare protezione alta e pertanto verrà sincronizzato con cluster primario, ma verrà essere replicata in Azure replica il ripristino del sito.

Nell'immagine seguente viene illustrato il programma di installazione.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Considerazioni sulla failback

Per cluster di SQL, failback dopo un failover non pianificato verrà richiedono una copia di backup SQL e ripristinare dall'istanza speculare cluster originale e ristabilire la speculare.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](site-recovery-best-practices.md) sulle operazioni preliminari distribuire il ripristino del sito.










 