<properties
   pageTitle="Abilitare o disabilitare il monitoraggio macchine Virtuali di Azure"
   description="Descrive come abilitare o disabilitare il monitoraggio macchine Virtuali di Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Abilitare o disabilitare il monitoraggio macchine Virtuali di Azure

In questa sezione viene descritto come abilitare o disabilitare il monitoraggio in macchine virtuali in esecuzione in Azure. Per impostazione predefinita monitoraggio è attivato in macchine virtuali di Azure se distribuito dal [portale di Azure](https://portal.azure.com) e monitoraggio grafici sono disponibili con un periodo di 1 minuti per impostazione predefinita. È possibile attivare o disattivare il monitoraggio tramite il portale e dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows (CLI Azure). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Attivare / disattivare il monitoraggio tramite il portale di Azure
 
È possibile attivare il monitoraggio di Azure una macchina virtuale, che fornisce i dati relativi all'istanza in periodi di 1 minuto. (spazio di archiviazione modifiche si applicano). Dati di diagnostica dettagliate sono disponibili per la macchina virtuale nei grafici portale o tramite l'API. Per impostazione predefinita, il portale di Azure consente il monitoraggio, ma è possibile disattivare la come descritto di seguito. È possibile attivare il monitoraggio mentre la macchina virtuale è in esecuzione o in stato di arresto.

- Aprire il portale in **Azure [https://portal.azure.com](https://portal.azure.com)**

- Nel riquadro di spostamento sinistro fare clic su macchine virtuali.

- In macchine virtuali elenco, selezionare un'istanza in esecuzione o meno. Verrà aperto blad macchina virtuale.

- Fare clic su "Tutte le impostazioni".

- Fare clic su "Diagnostica".

- Modificare lo stato su Sì o No. È anche possibile scegliere in questo blade il livello di monitoraggio dei dettagli che si desidera attivare per la macchina virtuale.

[Azure.Note] L'opzione diagnostica su è il valore predefinito quando si crea una nuova macchina virtuale

![Attivare / disattivare il monitoraggio tramite il portale di Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Attivare / disattivare il monitoraggio con CLI Azure
 
Per abilitare il monitoraggio per una macchina virtuale Azure.

- Creare un file denominato, ad esempio PrivateConfig.json con il contenuto seguente.
        {"storageAccountName": "l'account di archiviazione per ricevere dati", "storageAccountKey": "la chiave dell'account"}
- Eseguire il seguente comando CLI Azure.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] È possibile modificare dalla versione 2.0 a una versione successiva, se disponibile. 

Per ulteriori informazioni sulla configurazione di monitoraggio metriche ed esempi, visitare il documento - * *[Utilizzando Linux diagnostica estensione Monitor Linux Virtual Machine dati sulle prestazioni e diagnostici](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

