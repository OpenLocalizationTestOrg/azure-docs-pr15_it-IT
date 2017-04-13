<properties
    pageTitle="Risoluzione dei problemi di errore di allocazione servizio Cloud | Microsoft Azure"
    description="Risoluzione dei problemi di errore di allocazione quando si distribuisce servizi Cloud di Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Risoluzione dei problemi di errore di allocazione quando si distribuisce servizi Cloud di Azure

## <a name="summary"></a>Riepilogo
Quando si distribuiscono le istanze in un servizio Cloud o aggiungere istanze del ruolo di lavoro o un nuovo sito web, Microsoft Azure Assegna risorse di elaborazione. In alcuni casi, possono verificarsi errori durante l'esecuzione di queste operazioni anche prima che vengano raggiunti i limiti di Azure abbonamento. In questo articolo vengono illustrate le cause di alcuni degli errori di allocazione comuni e vengono suggerite possibili rimedi. Le informazioni potrebbero essere utili quando si pianifica la distribuzione dei servizi.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Sfondo-funzionamento dell'assegnazione
I server Azure Data Center sono suddivisi in cluster. Si tenta di una nuova richiesta di allocazione servizio cloud in più cluster. Quando la prima istanza viene distribuita in un servizio cloud (in gestione temporanea o produzione), che servizio cloud Ottiene aggiunte a un cluster. Qualsiasi distribuzioni ulteriormente per il servizio cloud verranno visualizzato nello stesso cluster. In questo articolo verrà e viene indicato come "bloccati a un cluster". Diagramma 1 seguito è illustrata nel caso di un'assegnazione normale che tentato più cluster; Diagramma 2 illustra nel caso di un'assegnazione che ha bloccato a Cluster 2 perché che ospita la CS_1 servizio Cloud esistente.

![Diagramma di assegnazione](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Perché si verifica errore di allocazione
Quando una richiesta di allocazione viene aggiunta a un cluster, è presente una più possibilità di errori trovare risorse gratuite poiché il pool di risorse disponibili è limitato a un cluster. Inoltre, se la richiesta di assegnazione viene aggiunta a un cluster ma il tipo di risorsa richiesto non è supportato da tale cluster, la richiesta avrà esito negativo anche se il cluster dispone risorsa gratuita. Diagramma 3 seguito illustra il caso in cui un'allocazione bloccata non riesce perché il cluster solo candidati non dispone di risorse gratuite. Diagramma 4 illustra il caso in cui una bloccata allocazione ha esito negativo perché il cluster candidati solo non supporta la dimensione macchine Virtuali richiesta, anche se il cluster dispone di risorse gratuite.

![Errore di allocazione bloccata](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Errore di allocazione sulla risoluzione dei problemi per i servizi cloud
### <a name="error-message"></a>Messaggio di errore
È possibile visualizzato il messaggio di errore seguente:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemi comuni
Ecco gli scenari comuni di allocazione che causano una richiesta di allocazione aggiunte a un singolo cluster.

- La distribuzione in intervallo aperto di gestione temporanea - se un servizio cloud dispone di una distribuzione in uno dei due intervallo aperto, quindi il servizio cloud intero viene aggiunta a un cluster specifico.  Ciò significa che se una distribuzione già presente nell'intervallo di aperto produzione, quindi una nuova distribuzione di gestione temporanea può essere allocata solo nello stesso cluster l'intervallo di aperto di produzione. Se il cluster si avvicina capacità, potrebbe non riuscire la richiesta.

- Ridimensionamento: aggiunta di nuove istanze a un servizio cloud esistente necessario allocare nello stesso cluster.  Piccolo ridimensionamento le richieste in genere può essere assegnato, ma non sempre. Se il cluster si avvicina capacità, potrebbe non riuscire la richiesta.

- Gruppo affinità - una nuova distribuzione a un servizio cloud vuota da allocare per tessuti in un cluster di tale area, a meno che il servizio cloud viene aggiunta a un gruppo di affinità. Tentativo di distribuzioni allo stesso gruppo affinità cluster stesso. Se il cluster si avvicina capacità, potrebbe non riuscire la richiesta.

- Gruppo affinità vNet - reti virtuali precedenti sono stati collegati ai gruppi di affinità anziché aree e servizi cloud in queste reti virtuali da aggiunte al gruppo affinità cluster. Tentativo di distribuzioni a questo tipo di rete virtuale cluster bloccata. Se il cluster si avvicina capacità, potrebbe non riuscire la richiesta.

## <a name="solutions"></a>Soluzioni

1. Ridistribuire a un nuovo servizio cloud: questa soluzione è probabile che sia più efficace poiché consente la piattaforma tra cui scegliere tutti i cluster di tale area.

    - Distribuire il carico di lavoro in un nuovo servizio cloud  

    - Aggiornare il record CNAME o un record per indirizzare il traffico al nuovo servizio cloud

    - Una volta zero il traffico verrà il vecchio sito, è possibile eliminare il vecchio servizio cloud. Questa soluzione deve sostenere alcun periodo di inattività.

2. Eliminazione di produzione e di gestione temporanea bande orarie: questa soluzione manterrà o meno il nome DNS esistente, ma determinerà il tempo di inattività all'applicazione.

    - Eliminare la produzione e gestione temporanea bande orarie di un servizio cloud esistente in modo che il servizio cloud è vuoto, quindi

    - Creare una nuova distribuzione nel servizio cloud esistente. Questo nuovamente tenterà di assegnazione su tutti i cluster nell'area. Assicurarsi che il servizio cloud non è collegato a un gruppo di affinità.

3. IP riservati - questa soluzione manterrà o meno l'indirizzo IP esistente di indirizzi, ma determinano il tempo di inattività all'applicazione.  

    - Creare un IP riservati per la distribuzione esistente tramite Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Seguire #2 dall'alto, assicurandosi di specificare il nuovo IP riservati in CSCFG del servizio.

4. Rimuovere il gruppo di affinità per nuove distribuzioni - affinità gruppi non è più consigliati. Attenersi alla procedura indicata sopra per distribuire un nuovo servizio cloud di #1. Assicurarsi che servizio cloud non in un gruppo di affinità.

5. Convertire in una rete virtuale internazionali, vedere [come eseguire la migrazione da gruppi di affinità a una rete locale (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
