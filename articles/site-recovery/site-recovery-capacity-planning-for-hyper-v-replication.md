<properties
    pageTitle="Eseguire lo strumento di pianificazione delle capacità di Hyper-V per il ripristino del sito | Microsoft Azure"
    description="In questo articolo è contenute istruzioni per l'uso dello strumento di pianificazione capacità Hyper-V per il ripristino del sito di Azure"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Eseguire lo strumento di pianificazione delle capacità di Hyper-V per il ripristino del sito

Come parte della distribuzione il ripristino del sito di Azure, è necessario stabilire della replica e dei requisiti della larghezza di banda. Lo strumento di pianificazione delle capacità di Hyper-V per il ripristino del sito consente di stabilire i requisiti di replica e della larghezza di banda per la replica macchina virtuale Hyper-V.


In questo articolo viene descritto come eseguire lo strumento di pianificazione delle capacità di Hyper-V. Questo strumento dovrebbe essere usato insieme ad altri strumenti di pianificazione della capacità e informazioni descritte nella [pianificazione della capacità per il ripristino del sito](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Prima di iniziare

Eseguire lo strumento su un nodo di server o cluster Hyper-V nel sito principale. Per eseguire lo strumento è necessario selezionare il server di host Hyper-V:

- Sistema operativo: Windows Server® 2012 o Windows Server® 2012 R2
- Memoria: 20 MB (minimo)
- CPU: il sovraccarico di 5 percentuale (minimo)
- Spazio su disco: 5 MB (minimo)

Prima di eseguire lo strumento è necessario preparare il sito principale. Se si sta replicare tra due locale siti e si vuole controllare la larghezza di banda, è necessario preparare un server di replica anche.


## <a name="step-1-prepare-the-primary-site"></a>Passaggio 1: Preparare il sito principale
1. Creare un elenco di tutte le macchine virtuali di Hyper-V da replicare e hosts/cluster Hyper-V in cui si trovano nel sito principale. Lo strumento eseguire ogni volta che per più host di prodotto autonomo o per un singolo cluster ma non entrambe contemporaneamente. È anche necessario eseguire separatamente per ogni sistema operativo, in modo che è necessario raccogliere e prendere nota dei server di Hyper-V come indicato di seguito:

  - Server autonomi di Windows Server® 2012
  - Cluster di Windows Server® 2012
  - Server autonomi di Windows Server® 2012 R2
  - Cluster di Windows Server® 2012 R2

3. Abilitare l'accesso remoto a WMI host Hyper-V e cluster. Eseguire questo comando su ogni cluster o un server per assicurarsi che l'opzione regole del firewall e le autorizzazioni degli utenti siano impostate:

        netsh firewall set service RemoteAdmin enable

5. Attivare monitoraggio delle prestazioni sul server e cluster, come indicato di seguito:

  - Aprire Windows Firewall con snap-in **Protezione avanzata** e quindi attivare le seguenti regole in entrata: **Accesso alla rete COM + (DCOM-IN)** e tutte le regole **gruppo di gestione remota registro eventi**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Passaggio 2: Preparare un server di replica (in locale per la replica locale)

Non è necessario eseguire questa operazione se sta replicate in Azure.

È consigliabile che si è impostato un singolo host Hyper-V come un server di ripristino in modo che una macchina virtuale fittizia può essere replicata in modo da controllare la larghezza di banda.  È possibile ignorare questa operazione, ma non sarà possibile misurare la larghezza di banda, a meno che non si farebbe.

1. Se si desidera utilizzare un nodo cluster come replica di configurare il gestore di Replica Hyper-V:

    - In **Server Manager**, aprire **Gestione Cluster di Failover**.
    - Connettersi al cluster, evidenziare il nome del cluster e fare clic su **Azioni** > **Configurare ruolo** per aprire la configurazione guidata disponibilità elevata.
    - In **Seleziona ruolo** fare clic su **Gestore di Replica Hyper-V**. Nella procedura guidata specificare un **nome NetBIOS** e **indirizzo IP** da utilizzare come punto di connessione a cluster (denominato un punto di accesso client). Saranno configurati **Hyper-V Replica gestore** risultante il nome di un punto di accesso client che è importante conoscere.
    - Verificare che il ruolo di Hyper-V Replica gestore include online correttamente e potrebbe non riuscire su tra tutti i nodi del cluster. Per eseguire questa operazione, fare clic con il pulsante destro del ruolo, **spostare**e quindi fare clic su **Seleziona nodo**. Selezionare un nodo > **OK**.
    - Se si usa l'autenticazione basata su certificato, verificare che ogni nodo del cluster e il client al punto che tutto è installato il certificato di accesso.
2.  Abilitare un server di replica:

    - Per un cluster di aprire Gestione Cluster di errore, connettersi al cluster e fare clic su **ruoli** > Scegli ruolo > **Impostazione replica**s > **Abilita il cluster come un server di Replica**. Si noti che se si usa un cluster della replica è necessario avere il ruolo di Hyper-V Replica gestore presenta nel cluster nel sito principale.
    - Per un server autonomo aprire Hyper-V Manager. Nel riquadro **Azioni** , fare clic su **Impostazioni Hyper-V** per il server a cui che si desidera attivare e **Configurazione della replica** fare clic su **Abilita questo computer come un server di Replica**.
3. Configurare l'autenticazione:

    - In **porte di autenticazione e** selezionare la modalità di autenticazione del server primario e le porte di autenticazione. Se si usa certificato fare clic su **Seleziona certificato** per selezionarne uno. Utilizzare Kerberos se host di Hyper-V principale e il ripristino stesso dominio o domini attendibili. Utilizzare i certificati per diversi domini o la distribuzione di un gruppo di lavoro.
    - Nella sezione **autorizzazioni e lo spazio di archiviazione** consente a **qualsiasi** server autenticato (principale) inviare dati replica al server di replica. Fare clic su **OK** o **Applica**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Eseguire **netsh http Mostra servicestate** per verificare che comunicare ascoltatore sia in esecuzione per il protocollo/porta specificato:  
4. Configurare Firewall. Durante l'installazione di Hyper-V vengono create le regole del firewall per consentire il traffico come impostazione predefinita porte (HTTPS su 443, Kerberos su 80). Abilitare le regole come indicato di seguito:

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Passaggio 3: Eseguire lo strumento di pianificazione delle capacità

Dopo aver preparato il sito principale e configurare un server di ripristino è possibile eseguire lo strumento.

1. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39057) lo strumento da Microsoft Download Center.
2. Eseguire lo strumento da uno dei server primario (o uno dei nodi dal cluster principale). Fare clic sul file .exe e quindi scegliere **Esegui come amministratore**.
3. Nella **prima di iniziare** specificare quanto tempo desiderato per la raccolta di dati. È consigliabile che eseguire lo strumento durante le ore di produzione per verificare che i dati siano rappresentante. Se si sta tentando solo convalidare la connettività di rete, è possibile raccogliere per un solo minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Dettagli **del sito principale** specificare il nome del server o il nome di dominio completo per un host autonoma, o per un cluster di specificare il nome di dominio completo del client accettare punto, il nome del cluster o qualsiasi nodo del cluster e quindi fare clic su **Avanti**. Lo strumento rileva automaticamente il nome del server in cui viene eseguito. Lo strumento sceglie macchine virtuali che è possono monitorare per il server specificato.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. In **Dettagli sito duplicata** se sta replicate in Azure o se si sta replicate in un Data Center secondario e già configurato un server di replica, selezionare **Ignora prove di sito di replica**. Se esegue la replica in un Data Center secondario ed è stato configurato un tipo di replica nel nome di dominio completo del server autonomo o il punto di accesso client per il cluster nel **nome del Server (o) Hyper-V Replica gestore CAP**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. In **Dettagli Replica estesa** consente **ignorare il test di sito estesa duplicata**. Non sono supportati per il ripristino del sito.
7. In **Macchine virtuali scegliere da replicare** gli strumenti di connettersi al server o cluster e di visualizzare macchine virtuali e dischi in esecuzione nel server primario, secondo le impostazioni specificate nella pagina **Dettagli sito principale** . Si noti che non eseguono macchine virtuali già abilitati per la replica o che non viene visualizzato. Selezionare le macchine virtuali per il quale si desidera raccogliere metriche. Selezionare i dischi rigidi virtuali automaticamente raccoglie i dati per le macchine virtuali troppo.
9. Se è stato configurato un server di replica o cluster, in **informazioni di rete** specificare la larghezza di banda WAN approssimativa si ritiene che verrà utilizzato tra i siti principale e replica e selezionare i certificati se è stata configurata autenticazione certificato.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. In **Riepilogo** controllare le impostazioni e fare clic su **Avanti** per iniziare la raccolta metriche. Lo stato e avanzamento strumento viene visualizzato nella pagina **Calcolare capacità** . Quando termina strumento esecuzione fare clic su **Visualizza Report** per passare l'output. Per impostazione predefinita i report e registri sono archiviati in **%systemdrive%\Users\Public\Documents\Capacity pianificazione**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Passaggio 4: Interpretare i risultati
Ecco le metriche importanti. È possibile ignorare metriche che non sono elencate di seguito. Che non siano pertinenti per il ripristino del sito.

### <a name="on-premises-to-on-premises-replication"></a>Locale per la replica locale
  - Impatto della replica di elaborazione dell'host principale, di memoria
  - Impatto della replica nel server primario, spazio di archiviazione dell'host di ripristino IOPS
  - Totale della larghezza di banda necessario per la replica delta (Mbps)
  - Larghezza di banda di rete osservati tra l'host principale e l'host del ripristino (Mbps)
  - Suggerimento per il numero ideale di attivo in parallelo bonifici tra i due host/cluster

### <a name="on-premises-to-azure-replication"></a>Locale a replica Azure
  - Impatto della replica di elaborazione dell'host principale, di memoria
  - Impatto della replica dello spazio di archiviazione spazio dell'host principale su disco, IOPS
  - Totale della larghezza di banda necessario per la replica delta (Mbps)

## <a name="more-resources"></a>Altre risorse

- Per informazioni dettagliate sullo strumento leggere il documento associato il download dello strumento.
- Guardare una procedura dettagliata dello strumento su di Keith Mayer [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Ottenere i risultati](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) delle prestazioni verifica degli locale per la replica di Hyper-V in locale



## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la pianificazione della capacità è possibile avviare la distribuzione il ripristino del sito:

- [Replicare macchine virtuali di Hyper-V nel cloud VMM Azure](site-recovery-vmm-to-azure.md)
- [Replicare macchine virtuali di Hyper-V (senza VMM) Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicare macchine virtuali di Hyper-V tra siti VMM](site-recovery-vmm-to-vmm.md)
- [Replicare macchine virtuali di Hyper-V tra siti VMM con SAN](site-recovery-vmm-san.md)
- [Replicare hyper-V macchine virtuali nel server VMM singola](site-recovery-single-vmm.md)