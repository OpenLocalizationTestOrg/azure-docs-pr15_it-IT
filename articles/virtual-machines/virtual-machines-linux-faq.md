<properties
    pageTitle="Domande frequenti su macchine virtuali Linux | Microsoft Azure"
    description="Vengono fornite le risposte ad alcune domande comuni sulle macchine virtuali Linux create con il modello di Manager delle risorse."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Domande frequenti sulle macchine virtuali Linux

Questo articolo riguarda alcune domande comuni sulle macchine virtuali Linux create in Azure utilizzando il modello di distribuzione di Manager delle risorse. Per la versione di Windows di questo argomento, vedere [domande frequenti domanda sulle macchine virtuali di Windows](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire su un Virtual Machine Azure?

Tutti i sottoscrittori possono eseguire software server in una macchina virtuale Azure. Per ulteriori informazioni, vedere [Linux nelle distribuzioni Azure-Endorsed](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto spazio di archiviazione è possibile usare con una macchina virtuale?

Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [le dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).

Un account di archiviazione Azure fornisce lo spazio di archiviazione per il disco di sistema operativo e qualsiasi disco dati. Ogni disco è un file con estensione vhd memorizzato come blob pagina. Per ulteriori informazioni sui prezzi, vedere [I dettagli prezzi lo spazio di archiviazione](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Come è possibile accedere a computer virtuali?

Stabilire una connessione remota per accedere al computer virtuale, utilizzando Secure Shell (SSH). Vedere le istruzioni su come connettersi [da Windows](virtual-machines-linux-ssh-from-windows.md) o [da Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md). Per impostazione predefinita, SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare il numero modificando il file di configurazione.


Se si verificano problemi, vedere [risolvere i problemi di Secure Shell (SSH) connessioni](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>È possibile utilizzare il disco temporaneo (dev/sdb1) per archiviare i dati?

Non usare il disco temporaneo (dev/sdb1) per archiviare i dati. È presente solo per l'archiviazione temporanea. Potrebbe causare la perdita di dati che non possono essere recuperati.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>È possibile copiare o duplicare una macchina virtuale Azure esistente?

Sì. Per ulteriori informazioni, vedere [come creare una copia di una macchina virtuale Linux nel modello di distribuzione Manager delle risorse](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Perché non si vedono Canada centrale e delle aree geografiche Canada orientale tramite Gestione risorse di Azure?

Due nuove aree di centrale Canada e in Canada orientale non vengono registrate automaticamente per la creazione di macchina virtuale per gli abbonamenti Azure esistente. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure per area geografica Gestione risorse di Azure. Dopo una macchina virtuale viene distribuita in un'altra regione Azure, nuove aree dovrebbero essere disponibile per macchine virtuali successive.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>È possibile aggiungere una scheda di rete per la macchina virtuale dopo averlo creato?

No. Aggiunta di una scheda di rete può essere eseguita solo al momento della creazione.


## <a name="are-there-any-computer-name-requirements"></a>Ci sono i requisiti di nome computer?

Sì. Il nome del computer può contenere un massimo di 64 caratteri. Per ulteriori informazioni su denominazione delle risorse, vedere [convenzioni di denominazione dell'infrastruttura](virtual-machines-linux-infrastructure-naming-guidelines.md) .


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quali sono i requisiti di nome utente durante la creazione di una macchina virtuale?

I nomi utente devono essere 1-64 caratteri.

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

Le password devono essere 72 6 caratteri e rispondere 3 fuori i requisiti di 4 complessità seguenti:

- Includere caratteri inferiori
- Includere caratteri superiori
- Contengono una cifra
- Non contengano caratteri speciali (Regex corrispondere [\W_])

Le password seguenti non sono consentite:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Word $$ PA</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
