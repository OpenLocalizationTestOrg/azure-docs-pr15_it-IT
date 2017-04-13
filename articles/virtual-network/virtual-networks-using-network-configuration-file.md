<properties 
    pageTitle="Configurare una rete virtuale tramite un file di configurazione della rete" 
    description="Istruzioni per esportare e importare un file di configurazione della rete al portale di gestione di Azure per creare o modificare le reti virtuali. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurare una rete virtuale tramite un file di configurazione della rete

È possibile configurare una rete virtuale (VNet) tramite il portale di gestione di Azure o tramite un file di configurazione della rete.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Creazione e modifica di un file di configurazione di rete 
Il modo più semplice per creare un file di configurazione di rete è per esportare le impostazioni di rete da una configurazione virtuali esistente, quindi modificare il file per contenere le impostazioni che si desidera configurare per le reti virtuali.

Per modificare il file di configurazione della rete, è possibile semplicemente aprire il file, apportare le modifiche desiderate, quindi salvare il file. È possibile utilizzare qualsiasi editor *xml* di apportare modifiche al file di configurazione della rete. 

È necessario seguire da vicino le linee guida per [Impostazioni schema file di configurazione della rete](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure vengono tenuti in considerazione una subnet che contiene un elemento distribuito come **in uso**. Quando una subnet è in uso, non possono essere modificata. Prima di modificare, spostare tutto ciò che è stato distribuito alla subnet per una subnet che non venga modificata.   [Spostare una macchina virtuale o istanza del ruolo a un'altra Subnet](virtual-networks-move-vm-role-to-subnet.md), vedere.

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Esportare e importare le impostazioni di rete virtuale tramite il portale di gestione  
È possibile importare ed esportare le impostazioni di configurazione di rete contenute in un file di configurazione della rete tramite PowerShell o il portale di gestione. Le istruzioni riportate di seguito consente di esportazione e importazione tramite il portale di gestione. 

### <a name="to-export-your-network-settings"></a>Per esportare le impostazioni di rete
Quando si esporta, tutte le impostazioni per le reti virtuali nell'abbonamento verrà scritto in un file XML. 

1. Accedere al **portale di gestione**.
2. Nel portale di gestione, nella parte inferiore della pagina **reti** , fare clic su **Esporta**. 
3. Nella finestra **Esporta configurazione della rete** , verificare di aver selezionato l'abbonamento per cui si desidera esportare le impostazioni di rete. Selezionare il segno di spunta in basso a destra. 
4. Quando viene richiesto, salvare il file *NetworkConfig.xml* nella posizione desiderata.


### <a name="to-import-your-network-settings"></a>Per importare le impostazioni di rete

1. Nel **Portale di gestione**, nel riquadro di spostamento in basso a sinistra, fare clic su **Nuovo**.
2. Fare clic su **servizi di rete** -> **virtuali** -> **configurazione di importazione**.
3. Nella pagina **Importa file di configurazione della rete** , individuare il file di configurazione della rete e quindi fare clic sulla freccia **Avanti** .
4. Nella pagina **creazione alla rete aziendale** , verrà visualizzato informazioni sullo schermo con le sezioni della configurazione di rete verranno modificate o create. Se le modifiche risulta corrette per l'utente, fare clic sul segno di spunta per continuare ad aggiornare o creare la rete virtuale. 