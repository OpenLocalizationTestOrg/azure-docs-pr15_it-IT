<properties 
   pageTitle="Visualizzazione e modifica di nomi host | Microsoft Azure"
   description="Come visualizzare e modificare nomi host per macchine virtuali di Azure, web e i ruoli di lavoro per la risoluzione dei nomi"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Visualizzazione e modifica di nome host

Per consentire le istanze del ruolo di un riferimento al nome host, è necessario impostare il valore di nome host nel file di configurazione del servizio per ogni ruolo. È aggiungendo il nome host desiderato per l'attributo **vmName** dell'elemento **ruolo** . Il valore dell'attributo **vmName** viene utilizzato come base per il nome host di ogni istanza del ruolo. Ad esempio, se **vmName** è *webrole* e sono disponibili tre istanze di tale ruolo, i nomi degli host delle istanze sarà *webrole0*, *webrole1*e *webrole2*. Non è necessario specificare un nome per macchine virtuali nel file di configurazione, perché il nome host per una macchina virtuale viene popolato in base al nome macchina virtuale. Per ulteriori informazioni sulla configurazione di un servizio di Microsoft Azure, vedere [Dello Schema di configurazione del servizio di Azure (cscfg File)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visualizzazione di nome host

È possibile visualizzare i nomi degli host di macchine virtuali e istanze del ruolo in un servizio cloud utilizzando uno degli strumenti riportata di seguito.

### <a name="azure-portal"></a>Portale di Azure

È possibile utilizzare il [portale di Azure](http://portal.azure.com) per visualizzare i nomi degli host per macchine virtuali e il Panoramica per una macchina virtuale. Tenere presente che e il Mostra un valore di **nome** e **Il nome Host**. Anche se sono inizialmente lo stesso, la modifica del nome host non cambierà il nome della macchina virtuale o istanza del ruolo.

Istanze del ruolo possono essere visualizzate anche nel portale di Azure, ma quando si elencano le istanze in un servizio cloud, non viene visualizzato il nome host. Verrà visualizzato un nome per ogni istanza, ma tale nome non rappresenta il nome host.

### <a name="service-configuration-file"></a>File di configurazione del servizio

È possibile scaricare il file di configurazione del servizio per un servizio distribuito da e il **Configura** del servizio nel portale di Azure. È quindi possibile cercare l'attributo **vmName** per l'elemento **nome ruolo** visualizzare il nome host. Tenere presente che questo nome host viene utilizzato come base per il nome host di ogni istanza del ruolo. Ad esempio, se **vmName** è *webrole* e sono disponibili tre istanze di tale ruolo, i nomi degli host delle istanze sarà *webrole0*, *webrole1*e *webrole2*.

### <a name="remote-desktop"></a>Desktop remoto

Dopo aver attivato Desktop remoto (Windows), Windows PowerShell remota (Windows) o connessioni SSH (Linux e Windows) al macchine virtuali o istanze del ruolo, è possibile visualizzare il nome host da una connessione Desktop remoto attiva in vari modi:

- Digitare il nome host il prompt dei comandi o terminal SSH.

- Digitare ipconfig/tutto il comando richiesto (solo Windows).

- Consente di visualizzare il nome del computer le impostazioni di sistema (solo Windows).

### <a name="azure-service-management-rest-api"></a>Gestione dei servizi Azure API REST

Da un client resto, seguire queste istruzioni:

1. Verificare di disporre di un certificato client per connettersi al portale di Azure. Per ottenere un certificato client, seguire i passaggi presentati in [come: informazioni sul Download e importare impostazioni di pubblicazione e abbonamento](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Impostare una voce di intestazione denominata x-ms-versione con un valore di 2013-11-01.

1. Inviare una richiesta nel formato seguente: https://management.core.windows.net/\<annullamento della sottoscrizione dell'id\>/services/hostedservices/\<nome servizio\>?embed dettaglio = true

1. Cercare l'elemento **HostName** per ogni elemento **RoleInstance** .

>[AZURE.WARNING] È anche possibile visualizzare il suffisso di dominio interno per il servizio cloud dalla risposta chiamata resto selezionando l'elemento **InternalDnsSuffix** oppure eseguendo ipconfig/completa dal prompt dei comandi in una sessione Desktop remoto (Windows) oppure in esecuzione gatto /etc/resolv.conf da un terminale SSH (Linux).

## <a name="modifying-a-hostname"></a>Modificare un nome host

È possibile modificare il nome host per qualsiasi macchina virtuale o istanza del ruolo caricando un file di configurazione del servizio modificate o rinominando il computer da una sessione Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei nomi (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schema di configurazione del servizio Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schema di configurazione virtuali Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Specificare le impostazioni DNS utilizzando il file di configurazione di rete](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
