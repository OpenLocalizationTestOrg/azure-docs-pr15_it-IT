<properties
    pageTitle="Domande frequenti su macchine virtuali di Windows | Microsoft Azure"
    description="Vengono fornite le risposte ad alcune domande comuni sulle macchine virtuali di Windows create con il modello di Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Domande frequenti sulle macchine virtuali di Windows 


Questo articolo riguarda alcune domande comuni sulle macchine virtuali di Windows create in Azure utilizzando il modello di distribuzione di Manager delle risorse. Per la versione di Linux di questo argomento, vedere [domande frequenti sulle macchine virtuali Linux domanda](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire su un Virtual Machine Azure?

Tutti i sottoscrittori possono eseguire software server in una macchina virtuale Azure. Per informazioni sulla politica del supporto per l'esecuzione di software server Microsoft in Azure, vedere [software server Microsoft supporto per macchine virtuali di Azure](https://support.microsoft.com/kb/2721672)

Alcune versioni di Windows 7 e Windows 8.1 sono disponibili per gli abbonati a MSDN Azure vantaggi e gli abbonati MSDN Dev e testare uso prepagato, per le attività di sviluppo e test. Per informazioni dettagliate, incluse le istruzioni e limitazioni, vedere [le immagini di Windows Client per gli abbonati MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto spazio di archiviazione è possibile usare con una macchina virtuale?

Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [le dimensioni per macchine virtuali](virtual-machines-windows-sizes.md).

Un account di archiviazione Azure fornisce lo spazio di archiviazione per il disco di sistema operativo e qualsiasi disco dati. Ogni disco è un file con estensione vhd memorizzato come blob pagina. Per ulteriori informazioni sui prezzi, vedere [I dettagli prezzi lo spazio di archiviazione](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Come è possibile accedere a computer virtuali?

Stabilire una connessione remota utilizzando connessione Desktop remoto (RDP) per una macchina virtuale di Windows. Per ulteriori informazioni, vedere [come connettere e accedere a una macchina virtuale Azure che esegue Windows](virtual-machines-windows-connect-logon.md). Un massimo di due connessioni simultanee sono supportati, a meno che il server è configurato come host sessione Servizi Desktop remoto.  


Se si verificano problemi con Desktop remoto, vedere [risolvere i problemi di Desktop remoto connessioni a una basato su Windows Azure macchina virtuale](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Se si ha familiarità con Hyper-V, desiderati per uno strumento simile a VMConnect. Azure non offre uno strumento simile, perché non è supportato l'accesso alla console una macchina virtuale.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>È possibile utilizzare il disco temporaneo (unità d: per impostazione predefinita) per archiviare i dati?

Non usare il disco temporaneo per archiviare i dati. È solo temporanea, in modo da rischiare la perdita di dati che non possono essere recuperati. Perdita di dati può verificarsi quando si sposta la macchina virtuale su un host diverso. Ridimensionamento di una macchina virtuale, aggiornare l'host o un errore hardware nell'host sono alcuni dei motivi che potrebbe spostare una macchina virtuale.

Se si dispone di un'applicazione di cui si desidera utilizzare la lettera di unità d: è possibile riassegnare le lettere di unità in modo che il disco temporaneo utilizza un numero diverso da d:. Per ulteriori informazioni, vedere [modificare la lettera del disco temporaneo Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Come è possibile modificare la lettera del disco temporaneo?

È possibile modificare la lettera spostando il file della pagina e la riassegnazione delle lettere di unità, ma è necessario assicurarsi di eseguire la procedura descritta in un ordine specifico. Per ulteriori informazioni, vedere [modificare la lettera del disco temporaneo Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>È possibile aggiungere una macchina virtuale esistente a un set di disponibilità?

No. Se si desidera la macchina virtuale per far parte di un set di disponibilità, è necessario creare macchine Virtuali all'interno del set. Attualmente non è presente un modo per aggiungere una macchina virtuale per una disponibilità imposta dopo averlo creato.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>È possibile caricare una macchina virtuale in Azure?

Sì. Per istruzioni, vedere [caricare un'immagine macchine Virtuali di Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>È possibile ridimensionare il disco OS?

Sì. Per ulteriori informazioni, vedere [come espandere l'unità di sistema operativo di una macchina virtuale in un gruppo di risorse Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>È possibile copiare o duplicare una macchina virtuale Azure esistente?

Sì. Per ulteriori informazioni, vedere [come creare una copia di una macchina virtuale di Windows nel modello di distribuzione Manager delle risorse](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Perché non si vedono Canada centrale e delle aree geografiche Canada orientale tramite Gestione risorse di Azure?

Due nuove aree di centrale Canada e in Canada orientale non vengono registrate automaticamente per la creazione di macchina virtuale per gli abbonamenti Azure esistente. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure per area geografica Gestione risorse di Azure. Dopo una macchina virtuale viene distribuita in un'altra regione Azure, nuove aree dovrebbero essere disponibile per macchine virtuali successive.

## <a name="does-azure-support-linux-vms"></a>Azure supporta macchine virtuali Linux?

Sì. Per creare rapidamente una VM Linux a provare un prodotto, vedere [creare una macchina virtuale Linux su Azure tramite il portale](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>È possibile aggiungere una scheda di rete per la macchina virtuale dopo averlo creato?

No. Aggiunta di una scheda di rete può essere eseguita solo al momento della creazione.

## <a name="are-there-any-computer-name-requirements"></a>Ci sono i requisiti di nome computer?

Sì. Il nome del computer può contenere un massimo di 15 caratteri. Per ulteriori informazioni su denominazione delle risorse, vedere [convenzioni di denominazione dell'infrastruttura](virtual-machines-windows-infrastructure-naming-guidelines.md) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quali sono i requisiti di nome utente durante la creazione di una macchina virtuale?

I nomi utente può contenere un massimo di 20 caratteri e non può terminare con un punto ("."). 

I nomi utente seguenti non sono consentiti:

<table>
    <tr>
        <td style="text-align:center">amministratore </td><td style="text-align:center"> amministratore </td><td style="text-align:center"> utente </td><td style="text-align:center"> utente 1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> Utente2 </td><td style="text-align:center"> Test1 </td><td style="text-align:center"> User3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> un</td>
    </tr>
    <tr>
        <td style="text-align:center">ACTUser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">copia di backup </td><td style="text-align:center"> console </td><td style="text-align:center"> David </td><td style="text-align:center"> Guest</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> proprietario </td><td style="text-align:center"> radice </td><td style="text-align:center"> Server</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> supporto tecnico </td><td style="text-align:center"> Support_388945a0 </td><td style="text-align:center"> sistema</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> User4 </td><td style="text-align:center"> User5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quali sono i requisiti della password quando si crea una macchina virtuale?

Le password devono essere 123 8 caratteri e rispondere 3 fuori i requisiti di 4 complessità seguenti:

- Includere caratteri inferiori
- Includere caratteri superiori
- Contengono una cifra
- Non contengano caratteri speciali (Regex corrispondere [\W_])

Le password seguenti non sono consentite:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Word $$ PA</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
