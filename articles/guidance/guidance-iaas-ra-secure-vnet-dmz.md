<properties
   pageTitle="Architettura di Azure riferimento - IaaS: Implementazione di una rete Perimetrale tra Azure e Internet | Microsoft Azure"
   description="Come implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>L'implementazione di una rete Perimetrale tra Azure e Internet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per l'implementazione di una rete sicura ibrido che si estende la rete locale che accetta il traffico da internet alla rete in Azure. L'architettura di riferimento estende l'architettura descritto nell'articolo [l'implementazione di una rete Perimetrale tra Azure e la data center locale][implementing-a-secure-hybrid-network-architecture]. Si consiglia di leggere il documento e comprendere il riferimento architettura prima di leggere questo documento.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove. 

Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride in carichi di lavoro Esegui parzialmente locale e in parte in Azure.

- Infrastruttura di Azure che indirizza il traffico in ingresso da locale e internet.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura:

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Questo è nella pagina "Rete Perimetrale – pubblico".

[! [0]][0]

- **Indirizzo IP pubblico (PIP).** Questo è l'indirizzo IP dell'endpoint del pubblico. Gli utenti esterni sono connessi a Internet possono accedere al sistema tramite questo indirizzo.

- **Dispositivo virtuale rete (NVA).**  NVA è un termine generico che descrive una macchina virtuale eseguono attività, ad esempio consentire o negare l'accesso come un firewall, ottimizzazione operazioni WAN (inclusa la compressione di rete), routing personalizzata o altre funzionalità di rete.

- **Bilanciamento del carico Azure.** Tutte le richieste in arrivo da internet passano attraverso il servizio di bilanciamento del carico e distribuite a NVAs nella rete Perimetrale pubblico subnet in ingresso.

- **Rete Perimetrale pubblico in ingresso subnet.** Subnet accetta richieste dal servizio di bilanciamento del carico Azure. Le richieste in arrivo vengono passate a uno dei NVAs nella rete Perimetrale.

- **Subnet di rete Perimetrale in uscita pubblica.** Le richieste approvati tramite il NVA passano attraverso la subnet per il bilanciamento del carico interno per il livello di web.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="nva-recommendations"></a>Consigli NVA

Implementare un insieme di NVAs per il traffico originari su internet e un altro per il traffico originari locale. È un problema di protezione da utilizzare solo un set di NVAs entrambi in quanto questo schema non fornisce perimetrale sicurezza tra le due serie di traffico di rete. Si tratta dei vantaggi per usare questo schema poiché riduce la complessità della verifica regole di protezione, si rende più chiara quali regole corrispondono a ogni richiesta di rete in arrivo. Ad esempio, un set di NVAs implementazione delle regole per il traffico internet solo durante un altro set di regole di implementare NVAs locale solo per il traffico.

Includere un livello 7 NVA terminare connessioni applicazione livello NVA e la manutenzione affinità con livelli back-end. In questo modo connettività simmetrica in cui il traffico di risposta da livelli back-end restituisce tramite la NVA.  

### <a name="public-load-balancer-recommendations"></a>Suggerimenti di bilanciamento carico pubblico ###

Per mantenere la disponibilità e scalabilità, distribuire la rete Perimetrale pubblico in ingresso NVAs in un [set di disponibilità] [ availability-set] e utilizzare un [internet affiancate di bilanciamento del carico] [ load-balancer] distribuire le richieste internet in NVAs nel set di disponibilità.  

Configurare il bilanciamento del carico per accettare le richieste solo sulle porte necessarie per il traffico internet. Ad esempio, limitare le richieste HTTP in ingresso alla porta 80 e le richieste HTTPS in ingresso alla porta 443.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Progettare l'infrastruttura con un sistema di bilanciamento del carico che precede la subnet di rete Perimetrale pubblica in ingresso dall'inizio è connessa a internet. Anche se il momento di architettura richiede un singolo NVA, sarà facile adatta a più NVAs in futuro, se è già stato distribuito internet affiancate di bilanciamento del carico.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Internet affiancate di bilanciamento del carico richiede ogni NVA nella rete Perimetrale pubblico in ingresso subnet per implementare una [verifica dell'integrità][lb-probe]. Una verifica dell'integrità che cessa di rispondere per questo endpoint viene considerata non è disponibile e il servizio di bilanciamento del carico darà le richieste di altri NVAs nello stesso set di disponibilità. Si noti che se NVAs tutti non riesce a rispondere, l'applicazione non viene eseguito in modo che è importante avere monitoraggio configurato per attrezzi degli avvisi quando il numero di istanze NVA integro supera una determinata soglia.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Limitare la funzionalità di monitoraggio e gestione per di gli in ingresso pubblico DMZ NVA a rispondere a richieste dalla casella ponticello nella subnet gestione solo. Come descritto di [implementazione di una rete Perimetrale tra Azure e la data center locale] [ implementing-a-secure-hybrid-network-architecture] del documento, definire una route di rete singola dalla rete locale attraverso il gateway alla casella ponticello subnet management per limitare l'accesso.

Se la connettività di gateway tra la rete locale e Azure verso il basso, è comunque possibile contattare casella passa distribuendo un PIP, aggiungerlo alla casella passa e servizi remoti in da internet.

## <a name="security-considerations"></a>Considerazioni sulla protezione

L'architettura di riferimento consente a più livelli di sicurezza:

- Internet affiancate di bilanciamento del carico indirizza richieste per il NVAs in ingresso pubblico rete Perimetrale subnet solo, scegliere solo le porte necessarie per l'applicazione.

- Le regole NSG per la subnet rete Perimetrale pubblica in ingresso e in uscita impediscono la NVAs di violazione bloccando richieste che non rientrano le regole NSG.

- La configurazione di routing NAT per il NVAs indirizza le richieste in arrivo su porte 80 e 443 di bilanciamento del carico di livello web, ma ignora le richieste in tutte le altre porte.

Si noti che è possibile accedere con tutte le richieste in arrivo su tutte le porte. Controllare regolarmente i log, prestando attenzione alle richieste che rientrano all'esterno di parametri previsti come questi può indicare tentativi di intrusione.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Utilizzo di NSGs per il traffico in blocco/passaggio tra livelli dell'applicazione

Ognuno dei livelli web, business e dati limitare il traffico tra di esse utilizzando NSGs. Vale a dire il livello di business utilizza un NSG per bloccare tutto il traffico che non hanno origine nel livello web e il livello di dati utilizza un NSG per bloccare tutto il traffico che non hanno origine nel livello business. Se si dispone di un requisito per espandere le regole NSG per consentire l'accesso più ampio a questi livelli, valutare questi requisiti contro i rischi di sicurezza. Ogni nuovo percorso in ingresso rappresenta un'opportunità per i dati di danni accidentali o intenzionali perdita o dell'applicazione danni.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni è disponibile in Github. L'architettura di riferimento include una rete virtuale (VNet), gruppo di sicurezza di rete (NSG), bilanciamento del carico e due macchine ().

È possibile distribuire l'architettura di riferimento con Windows o macchine virtuali Linux seguendo le indicazioni seguenti: 

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-public-dmz-network-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Selezionare il **Tipo di sistema operativo** dall'elenco a discesa casella, **windows** o **linux**.
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-public-dmz-wl-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

6. Attendere che la distribuzione completare.

7. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, pertanto selezionare **Usa esistente** e immettere `ra-public-dmz-network-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

9. Attendere che la distribuzione completare.

10. Si consiglia di immediatamente modificare entrambi i file di parametro includono hardcoded nome utente e password per tutte le macchine virtuali. Per ogni macchina virtuale nella distribuzione, selezionarlo nel portale di Azure e quindi fare clic su **Reimposta password** e il **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per mantenere.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Architettura della rete ibrido protetta"
