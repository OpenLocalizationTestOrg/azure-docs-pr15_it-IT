<properties
    pageTitle="Glossario di Azure - dizionario Azure | Microsoft Azure"
    description="Utilizzare il glossario Azure per comprendere la terminologia cloud sulla piattaforma Azure. Questo breve dizionario Azure fornisce le definizioni dei termini cloud comuni per Azure."
    keywords="Dizionario Azure, cloud terminologia, glossario Azure, terminologia definizioni, termini cloud"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossario di Microsoft Azure: un dizionario della terminologia cloud sulla piattaforma Azure

Glossario di Microsoft Azure è un dizionario breve della terminologia cloud per la piattaforma Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Trovare le definizioni di servizio e altri termini cloud

* Per le definizioni di Azure servizi e dei relativi AWS controparti vedere [Microsoft Azure e servizi Web di Amazon](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Per settore generale cloud termini vedere [Cloud computing termini](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Glossario di Azure con sopra due riferimenti fornisce una tassonomia-to-end per Azure e settore cloud.  

## <a name="azure-glossary-list"></a>Elenco di Azure glossario


### <a name="account"></a>account  
Un lavoro o dell'istituto di istruzione o account Microsoft personale che viene utilizzato per accedere e gestire un abbonamento a Azure.  
Vedere anche [come Azure sottoscrizioni associate Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>set di disponibilità  
Insieme di macchine virtuali gestiti per fornire l'affidabilità e la ridondanza dell'applicazione. L'uso di un set di disponibilità garantisce che durante un evento di manutenzione pianificata o non pianificato sia disponibile almeno una macchina virtuale.  
Vedere anche [gestire la disponibilità di macchine virtuali di Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) e [gestire la disponibilità di macchine virtuali Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modello di distribuzione classica Azure  
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) utilizzato per la distribuzione di risorse in Azure (il nuovo modello è Gestione risorse di Azure). Alcune risorse Azure possono essere distribuiti in un modello o un altro, mentre gli altri utenti possono essere distribuiti in entrambi i modelli. Indicazioni per dettaglio singole risorse Azure modelli una risorsa possono essere distribuito con.


### <a name="cli"></a>Azure interfaccia riga di comando (CLI)  
Un' [interfaccia della riga](xplat-cli-install.md) che può essere utilizzato per gestire i servizi di Azure da Windows, OSX e PC Linux.


### <a name="powershell"></a>PowerShell Azure  
Un' [interfaccia riga di comando](powershell-install-configure.md) per gestire i servizi Azure tramite una riga di comando da PC con Windows. Alcuni servizi o funzionalità del servizio possono essere gestite solo tramite PowerShell o CLI. Indicazioni per informazioni dettagliate ogni singola risorsa Azure che modelli una risorsa possono essere distribuito con.   
Vedere anche [come installare e configurare PowerShell Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Modello di distribuzione Azure Manager delle risorse  
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) utilizzato per la distribuzione di risorse in Microsoft Azure (l'altro è il modello di distribuzione classica). Alcune risorse Azure possono essere distribuiti in un modello o un altro, mentre gli altri utenti possono essere distribuiti in entrambi i modelli. Indicazioni per dettaglio singole risorse Azure modelli una risorsa possono essere distribuito con.


### <a name="fault-domain"></a>dominio di guasto  
Insieme di macchine virtuali in un set di disponibilità che eventualmente può avere esito negativo nello stesso momento. Un esempio è un gruppo di computer in un rack che condividono un parametro di origine e di rete power comune. In Azure, macchine virtuali in un set di disponibilità sono separati automaticamente in più domini guasto.  
Vedere anche [gestire la disponibilità di macchine virtuali di Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) e [gestire la disponibilità di macchine virtuali Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>geografico  
Limite definito per residenza dati che in genere contiene due o più aree. I limiti possono essere interno o all'esterno nazionali bordi e influenzati dalle normativa fiscale. Ogni geografico ha almeno un'area. Esempi di geos sono area Asia Pacifico e Giappone. L'acronimo *geografici*.  
Vedere anche [aree Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>replica geografico  
Processo di replica automaticamente del contenuto, ad esempio BLOB, tabelle e code all'interno di una coppia internazionali.  
Vedere anche [La replica geografico attiva per Database SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>immagine  
File che contiene il sistema operativo e configurazione di applicazione che può essere utilizzata per creare un numero di macchine virtuali. In Azure sono disponibili due tipi di immagini: macchine Virtuali immagine e immagine del sistema operativo. Un'immagine di macchine Virtuali include un sistema operativo e tutti i dischi collegati a una macchina virtuale quando l'immagine viene creata. Immagine del sistema operativo contiene solo un sistema operativo GRG con configurazioni senza dati.  
Vedere anche [Naviga e selezionare immagini macchina virtuale di Windows Azure con PowerShell o CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>limiti  
Il numero di risorse che possono essere create o benchmark che è possibile ottenere le prestazioni. Limiti sono in genere associati abbonamenti, servizi e offerte.  
Vedere anche [abbonamento Azure e limiti del servizio, le quote e vincoli](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>bilanciamento del carico  
Una risorsa che distribuisce il traffico in ingresso tra computer in una rete. In Azure, un servizio di bilanciamento del carico distribuisce il traffico in macchine virtuali definiti in un set di bilanciamento del carico. Un [bilanciamento del carico](./load-balancer/load-balancer-overview.md) può essere esposto a internet o possono essere interno.  


### <a name="offer"></a>offerta  
I prezzi, crediti e termini correlati applicabili a un abbonamento a Azure.  
Vedere [Azure offrono pagina dei dettagli](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>portale  
Portale di sicuro Web utilizzato per la distribuzione e gestione dei servizi Azure.  Esistono due portali: [portale Azure](http://portal.azure.com/) e il [portale classica](http://manage.windowsazure.com/). Alcuni servizi sono disponibili in entrambi portali, mentre altre sono disponibili solo in una o l'altra. Gli elenchi di [grafico Azure disponibilità portale](https://azure.microsoft.com/features/azure-portal/availability/) a quali servizi sono disponibili in quale portale.  


### <a name="region"></a>area geografica  
Un'area all'interno di un geografico che non non tra confini e contiene uno o più Data Center. Prezzi, servizi regionali e tipi di offerta vengono esposti a livello di area geografica. Un'area in genere è associata a un'altra area, che può essere fino a diverse centinaia miglia fuori sede, in modo da formare una coppia internazionali. Coppie internazionali possono essere utilizzate come meccanismo per di emergenza e scenari di disponibilità. Anche in genere *posizione*.  
Vedere anche [aree Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>risorsa  
Un elemento che fa parte della soluzione Azure. Ogni servizio Azure consente di distribuire tipi diversi di risorse, ad esempio database o macchine virtuali.   
Vedere anche [Panoramica di gestione risorse di Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>gruppo di risorse  
In Gestione risorse che contiene le relative risorse per un'applicazione contenitore. Il gruppo di risorse è possibile includere tutte le risorse per un'applicazione o solo le risorse sono raggruppate logicamente. È possibile decidere come si desidera assegnare risorse a gruppi di risorse in base alle informazioni più appropriata per l'organizzazione.  
Vedere anche [Panoramica di gestione risorse di Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Modello di Manager delle risorse  
File JSON che definisce in modo dichiarativo una o più risorse Azure e che consente di definire le dipendenze tra le risorse distribuite. Il modello è utilizzabile per distribuire le risorse in modo coerente e più volte.  
Vedere anche [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>provider di risorse  
Servizio che fornisce le risorse è possibile distribuire e gestire tramite Gestione risorse. Ogni provider di risorse offre operazioni per l'utilizzo delle risorse che vengono distribuite. Provider di risorse accessibili tramite il portale di Azure, PowerShell Azure e SDK programmazione diverse.  
Vedere anche [Panoramica di gestione risorse di Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>ruolo  
Un modo per controllare l'accesso assegnati a utenti, gruppi e servizi. Ruoli sono in grado di eseguire azioni, ad esempio come creare, gestire e continuare a leggere risorse Azure.  
Vedere anche [RBAC: ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>contratto di servizio (contratto di servizio)  
Il contratto che descrive gli impegni di Microsoft per i tempi di attività e la connettività. Ogni servizio Azure ha un contratto di servizio specifico.  
Vedere anche [contratti di servizio](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>account di archiviazione  
Un account di archiviazione che consente di accedere ai servizi di Blob Azure, coda, tabella e File in archiviazione Azure. L'account di archiviazione offre lo spazio dei nomi univoco per gli oggetti di dati di archiviazione Azure.  
Vedere anche [gli account di archiviazione su Azure](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>abbonamento  
Contratto del cliente con Microsoft che consente di ottenere i servizi Azure. La sottoscrizione prezzi e condizioni correlate dipendono dall'offerta scelto per l'abbonamento. Vedere [il contratto di sottoscrizione Online Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Vedere anche [come Azure sottoscrizioni associate Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>tag  
Un termine di indicizzazione che consente di classificare le risorse in base ai propri requisiti per la gestione o fatturazione. È possibile utilizzare tag quando si dispone di un insieme di gruppi di risorse e risorse complesso ed è necessario visualizzare le attività in modo più significativo. Ad esempio, si potrebbero tag risorse che non hanno un ruolo simile nell'organizzazione o appartengono al reparto stesso.  
Vedere anche [tramite tag per organizzare le risorse Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>dominio di aggiornamento  
Insieme di macchine virtuali di un set di disponibilità sono aggiornati nello stesso momento. Macchine virtuali nello stesso dominio di aggiornamento sono riavviate insieme durante la manutenzione pianificata. Azure non riavvia mai più di un dominio di aggiornamento contemporaneamente. Inoltre definito un aggiornamento del dominio.  
Vedere anche [gestire la disponibilità di macchine virtuali di Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) e [gestire la disponibilità di macchine virtuali Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>macchina virtuale  
L'implementazione di software di un computer fisico che esegue un sistema operativo. Più macchine virtuali è possibile eseguire contemporaneamente sullo stesso hardware. In Azure, macchine virtuali sono disponibili in diverse dimensioni.  
Vedere anche [documentazione macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>estensione macchina virtuale  
Una risorsa che implementata comportamenti o caratteristiche utili uno altri programmi di lavoro o fornire la possibilità di interagire con un computer in esecuzione. Ad esempio, è possibile utilizzare l'estensione accesso macchine Virtuali di reimpostare o modificare i valori di accesso remoto in un computer virtuale Azure.  
Vedere anche [sulla macchina virtuale estensioni e funzionalità (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) o [su macchina virtuale estensioni e funzionalità (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>rete virtuale  
Rete che fornisce la connettività tra le risorse Azure isolato da tutti gli altri tenant Azure. Può essere collegato ad altre reti virtuale Azure attraverso un [Gateway VPN Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) e alla rete locale utilizzando [più opzioni](./vpn-gateway/vpn-gateway-cross-premises-options.md). È possibile controllare completamente blocchi di indirizzi IP, le impostazioni DNS, criteri di sicurezza e la distribuzione tabelle all'interno di questa rete.  
Vedere anche [virtuali Panoramica](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Vedere anche**  
- [Guida introduttiva di Azure](https://azure.microsoft.com/get-started/)
- [Centro risorse cloud](https://azure.microsoft.com/resources/)  
- [Azure per l'applicazione aziendale](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure nel centro dati](https://azure.microsoft.com/overview/business-apps-on-azure/) 





