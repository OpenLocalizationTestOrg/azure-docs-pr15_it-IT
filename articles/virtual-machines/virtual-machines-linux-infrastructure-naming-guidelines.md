<properties
    pageTitle="Infrastruttura indicazioni per la denominazione | Microsoft Azure"
    description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la denominazione di servizi di infrastruttura Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Convenzioni di denominazione dell'infrastruttura

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

In questo articolo è incentrata su comprendere come approccio convenzioni di denominazione per tutte le risorse di Azure diverse creare un insieme di risorse logico e facilmente identificabile tra l'ambiente.

## <a name="implementation-guidelines-for-naming-conventions"></a>Linee guida implementazione convenzioni di denominazione

Decisioni:

- Quali sono le convenzioni di denominazione per le risorse Azure?

Attività:

- Definire affissi usare tra le risorse per garantire la coerenza.
- Definire i nomi degli account di archiviazione assegnati il requisito per questi utenti siano univoci.
- La convenzione di denominazione da utilizzare e distribuire a tutte le parti interessate garantire la coerenza tra distribuzioni del documento.

## <a name="naming-conventions"></a>Convenzioni di denominazione

È necessario avere una buona convenzione di denominazione in posizione prima di creare nulla in Azure. Una convenzione di denominazione assicurarsi che tutte le risorse abbiano un nome prevedibile, quali contribuisce a ridurre il carico amministrativo associato alla gestione delle risorse.

È possibile scegliere di seguire un determinato set di convenzioni di denominazione definiti per l'intera organizzazione oppure per un account o la sottoscrizione di Azure specifica. Per quanto sia semplice per le persone all'interno delle organizzazioni stabilire regole implicite quando si utilizzano le risorse Azure, è necessario essere in grado di ridurre per team che collaborano in Azure.

Implica l'accettazione su un set di anticipo convenzioni di denominazione. Esistono alcune considerazioni relative alle convenzioni di denominazione per tagliare tra che set di regole.

## <a name="affixes"></a>Appone

Come si osserva per definire una convenzione di denominazione, una decisione è che affisso sia presente:

- Inizio del nome (prefisso)
- Alla fine del nome (suffisso)

Ad esempio, di seguito sono due possibili nomi per un gruppo di risorse tramite il `rg` appone:

- RG-Web App (prefisso)
- Web App-Rg (suffisso)

Affissi possono fare riferimento ai diversi aspetti che descrivono le risorse specifiche. Nella tabella seguente sono illustrati alcuni esempi in genere utilizzati.

| Aspetto                               | Esempi                                                               | Note                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Ambiente                          | DEV, stg, produzione                                                         | A seconda di fine di ogni ambiente.                                                     |
| Posizione                             | usw (Ovest Stati Uniti), utilizzare (USA orientale 2)                                         | A seconda del tipo di area del Data Center o area geografica dell'organizzazione.                               |
| Componente Azure, servizio o prodotto | RG per gruppo di risorse, VNet per la rete virtuale                        | A seconda del prodotto per cui la risorsa fornisce supporto.                                          |
| Ruolo                                 | DB, app, web                                                           | In base al ruolo della macchina virtuale.                                                              |
| Istanza                             | 01, 02, 03, e così via.                                                       | Per le risorse che dispone di più istanze. Ad esempio bilanciato server web in un servizio cloud. |


Quando si stabilisce le convenzioni di denominazione, assicurarsi che è stato chiaramente quali affissi da usare per ogni tipo di risorsa e in quale posizione (suffisso vs prefisso).

## <a name="dates"></a>Date

È importante determinare la data di creazione dal nome di una risorsa. È consigliabile il formato di data aaaammgg. In questo formato garantisce che non solo è la versione completa di data viene registrata, ma anche che due risorse i cui nomi si differenziano solo per la data vengono ordinati in ordine alfabetico e in ordine cronologico.

## <a name="naming-resources"></a>Nomi delle risorse

Definire ogni tipo di risorsa nella convenzione di denominazione, che deve avere regole che definiscono come assegnare nomi a ogni risorsa che viene creato. Queste regole applicare a tutti i tipi di risorse, ad esempio:

- Sottoscrizioni
- Account
- Account di archiviazione
- Reti virtuali
- Subnet
- Set di disponibilità
- Gruppi di risorse
- Macchine virtuali
- Punti finali
- Gruppi di sicurezza di rete
- Ruoli

Per assicurarsi che il nome fornisce le informazioni necessarie per determinare per la risorsa che fa riferimento, è necessario utilizzare nomi descrittivi.

## <a name="computer-names"></a>Nomi dei computer

Quando si crea una macchine (), Azure richiede un nome di macchine Virtuali di fino a 64 caratteri utilizzato per il nome della risorsa. Azure Usa lo stesso nome per il sistema operativo installato nella macchina virtuale. Tuttavia, questi nomi potrebbero non essere sempre lo stesso.

Se una macchina virtuale viene creata da un file di immagine VHD che contiene già un sistema operativo, il nome di macchine Virtuali di Azure può essere diverso dal nome del computer del sistema operativo della macchina virtuale. Questa situazione è possibile aggiungere un livello di difficoltà di gestione delle macchine Virtuali, pertanto non è consigliabile. Assegnare la risorsa macchine Virtuali di Azure lo stesso nome il nome del computer da assegnare al sistema operativo di quella macchina virtuale.

È consigliabile che il nome di macchine Virtuali di Azure corrisponde al nome del computer del sistema operativo sottostante.

## <a name="storage-account-names"></a>Nomi degli account di archiviazione

Gli account di archiviazione sono regole particolari relativi nomi. È possibile utilizzare solo numeri e lettere minuscole. Per ulteriori informazioni, vedere [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) . Inoltre, il nome dell'account di archiviazione, con core.windows.net, deve essere un nome DNS globalmente valido e univoco. Ad esempio, se l'account di archiviazione è chiamato mystorageaccount, i nomi DNS risultanti seguenti devono essere univoci:

- mystorageaccount.BLOB.Core.Windows.NET
- mystorageaccount.Table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 