<properties
   pageTitle="Scenari ed esempi di governance abbonamento | Microsoft Azure"
   description="Vengono forniti esempi di come implementare governance abbonamento Azure per scenari comuni."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Esempi di implementazione scaffold enterprise Azure

In questo argomento sono forniti esempi di come un'organizzazione possibile implementare i consigli per un [scaffold Azure enterprise](resource-manager-subscription-governance.md). Viene utilizzata una società fittizia denominata Contoso per illustrano le procedure consigliate per gli scenari comuni. 

## <a name="background"></a>Sfondo

Contoso è una società in tutto il mondo che fornisce le soluzioni catena di fornitura per i clienti in tutti gli elementi da un modello "Software come servizio" in un modello nel pacchetto distribuito in locale.  Lo sviluppo software in tutto il mondo con centri di sviluppo significativa in India, negli Stati Uniti e Canada. 

La parte ISV della società è diviso in diverse unità aziendali indipendenti che gestiscono i prodotti in un'azienda significativa. Ogni business unit è il proprio sviluppatori, responsabili di prodotto e progettisti. 

La business unit aziendale grazie alla tecnologia servizi (ETS) fornisce centralizzata potenzialità IT e gestisce più centri di dati in business unit ospitare le applicazioni. Oltre a gestire i data center, organizzazione ETS fornisce e gestisce centralizzata collaborazione (ad esempio posta elettronica e siti Web) e i servizi di rete/telefonia. Gestisce inoltre i carichi di lavoro per i clienti per business unit più piccole che non hanno il personale. 

Di seguito vengono utilizzati in questo argomento:

- Dave è l'amministratore di Azure ETS.
- Alice è direttore di sviluppo Contoso della business unit catena di fornitura. 

Contoso è necessario creare un'applicazione line-of-business e un'app per i clienti. Ha deciso di eseguire l'App in Azure. Dave legge l'argomento di [governance abbonamento precise](resource-manager-subscription-governance.md) ed è ora possibile seguire i consigli forniti. 

## <a name="scenario-1-line-of-business-application"></a>Scenario 1: applicazione line-of-business

Contoso è la creazione di un sistema di gestione di codice sorgente (BitBucket) da utilizzare per gli sviluppatori in tutto il mondo.  L'applicazione utilizza infrastruttura come servizio (IaaS) per l'hosting e costituito da un server di database e server web. Gli sviluppatori di accedere ai server negli ambienti di sviluppo, ma senza bisogno di accesso ai server Azure. Contoso ETS auguri consentire il proprietario dell'applicazione e team di gestire l'applicazione. L'applicazione disponibile solo durante la rete aziendale di Contoso. Dave deve configurare l'abbonamento per questa applicazione. L'abbonamento anche ospitare in futuro altri software di sviluppo.  

### <a name="naming-standards--resource-groups"></a>Denominazione standard e i gruppi di risorse

Dave crea una sottoscrizione per supportare gli strumenti di sviluppo che sono comuni a tutte le business unit. Deve creare nomi significativi per i gruppi di abbonamento e delle risorse (per l'applicazione e le reti). Crea i gruppi di abbonamento e delle risorse seguenti:

| Elemento | Nome | Descrizione |
| ---- | ---- | ----------- |
| Abbonamento | Contoso ETS DeveloperTools produzione | Supporta strumenti comuni per sviluppatori |
| Gruppo di risorse | rgBitBucket | Contiene il server web dell'applicazione e il server di database |
| Gruppo di risorse | rgCoreNetworks | Contiene la connessione di gateway al sito e reti virtuali |


### <a name="role-based-access-control"></a>Controllo dell'accesso basato sui ruoli

Dopo aver creato la sottoscrizione, Dave desidera assicurarsi che il team appropriato e i proprietari di applicazione possono accedere alle risorse. Dave riconosce che ogni team ha requisiti differenti. Ha utilizza i gruppi che sono stati sincronizzati Contoso locali Active Directory (AD) a Azure Active Directory e offre il livello di accesso ai team di destra. 

Dave assegna i ruoli seguenti per l'abbonamento: 

| Ruolo | Assegnato a | Descrizione |
| ---- | ----------- | ----------- |
| [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) | Gestite ID dal Contoso Active Directory | Questo ID verrà controllato con solo in access ora (Just) tramite dello strumento di gestione delle identità di Contoso e garantisce che l'accesso proprietario abbonamento viene controllato completamente. |
| [Gestione della protezione](./active-directory/role-based-access-built-in-roles.md#security-manager) | Reparto di gestione di sicurezza e i rischi | Questo ruolo consente agli utenti di esaminare il Centro protezione di Azure e lo stato delle risorse. |
| [Collaboratori di rete](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Team di rete | Questo ruolo consente del team di rete di Contoso gestire la rete VPN da un sito e le reti virtuali. |
| *Ruolo personalizzato* | Proprietario dell'applicazione | Dave crea un ruolo che consente di modificare le risorse all'interno del gruppo di risorse. Per ulteriori informazioni, vedere [Ruoli personalizzati in RBAC Azure](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Criteri

Dave sono previsti i requisiti seguenti per la gestione delle risorse nella sottoscrizione:

- Perché gli strumenti di sviluppo supportano gli sviluppatori in tutto il mondo, ha non vuole impedire agli utenti di creazione di risorse in un'area. Tuttavia, necessita utili nel punto in cui vengono create le risorse. 
- È interessato ai costi. Pertanto si desidera per impedire ai proprietari di applicazione di creazione di macchine virtuali inutilmente costose.  
- Poiché questa applicazione svolge gli sviluppatori in numero di unità aziendali, desidera tag ogni risorsa con il proprietario di unità e applicazione business. Tramite questi tag ETS possibile distinta team appropriato.

Crea i seguenti [criteri di gestione delle risorse](resource-manager-policy.md): 

| Campo | Effetto | Descrizione |
| ----- | ------ | ----------- |
| posizione | controllo | Controllare la creazione delle risorse in qualsiasi area geografica |
| tipo | negare | Impedire la creazione di macchine virtuali di serie G |
| tag | negare | Richiedi tag proprietario dell'applicazione |
| tag | negare | Richiede tag centro di costo |
| tag | accodare | Aggiungere il nome di tag **Business Unit** e un valore di tag **ETS** a tutte le risorse |


### <a name="resource-tags"></a>Tag di risorse

Dave riconosce che ha deve disporre di informazioni specifiche nella fattura per identificare il centro di costo per l'implementazione di BitBucket. Inoltre, Dave desidera sapere tutte le risorse che possiede ETS. 

Ha aggiunto i [contrassegni](resource-group-using-tags.md) per i gruppi di risorse e le risorse. 
 
| Nome tag | Valore tag |
| -------- | --------- |
| ApplicationOwner | Il nome della persona che gestisce l'applicazione. |
| CostCenter | Centro di costo del gruppo di pagamento per il consumo Azure. |
| Business Unit | **ETS** (la business unit associato all'abbonamento) |

### <a name="core-network"></a>Rete principale

Il team di gestione Contoso ETS sicurezza e i rischi dell'informazioni rivede piano proposta di Davide e spostare un'applicazione di Azure. Si desidera impedire che l'applicazione non viene esposto su internet.  Dave è installato anche App per sviluppatori che in futuro sarà spostato Azure. Queste applicazioni richiedono interfacce pubbliche.  Per questi requisiti fornisce interne ed esterne reti virtuali e un gruppo di sicurezza di rete per limitare l'accesso.

Crea le risorse seguenti:

| Tipo di risorsa | Nome | Descrizione |
| ------------- | ---- | ----------- |
| Rete virtuale | vnInternal | Utilizzata con l'applicazione BitBucket ed è connesso tramite ExpressRoute alla rete aziendale di Contoso.  Una subnet (sbBitBucket) fornisce all'applicazione con uno spazio di indirizzi IP specifico. |
| Rete virtuale | vnExternal | Disponibile per le applicazioni future che richiedono l'endpoint di pubblico. |
| Gruppo di sicurezza di rete | nsgBitBucket | Garantisce che la possibilità di attacchi di questo carico di lavoro è ridotto a icona per consentire le connessioni solo sulla porta 443 per la subnet in cui l'applicazione risiede (sbBitBucket). |

### <a name="resource-locks"></a>Blocchi di risorse 

Dave riconosce che la connettività di rete aziendale di Contoso alla rete interna virtuale deve essere protetti da qualsiasi script e o eliminazioni accidentali. 

Crea il [blocco delle risorse](resource-group-lock-resources.md)seguenti:

| Tipo di blocco | Risorsa | Descrizione |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Impedisce agli utenti di eliminare il virtuali o subnet, ma non impedisce l'aggiunta di nuove subnet. |

### <a name="azure-automation"></a>Automazione Azure 

Dave ha nulla per automatizzare questa applicazione. Sebbene ha creato un account Azure automazione, ha non è possibile utilizzarla inizialmente. 

### <a name="azure-security-center"></a>Centro protezione di Azure 

Gestione dei servizi IT di Contoso deve rapidamente identificare e gestire i rischi. Desiderano inoltre capire quali problemi possono esistere.  

Per soddisfare questi requisiti, Dave consente di [Centro protezione di Azure](./security-center/security-center-intro.md)e consente di accedere al ruolo di Manager di sicurezza. 

## <a name="scenario-2-customer-facing-app"></a>Scenario 2: per i clienti app

Responsabili aziendali della business unit catena di fornitura sono stato individuato varie opportunità per aumentare il coinvolgimento con i clienti di Contoso tramite una carta fedeltà. Team Alice deve creare l'applicazione e decide che Azure aumenta la possibilità di soddisfare le esigenze aziendali. Alice funziona con Dave da ETS per configurare due abbonamenti per lo sviluppo e il funzionamento dell'applicazione.

### <a name="azure-subscriptions"></a>Sottoscrizioni di Azure 

Dave accede al portale aziendale di Azure e vede che il reparto catena di fornitura esiste già.  Tuttavia, quando il progetto è il primo progetto di sviluppo per il team di catena di fornitura in Azure, Dave riconosce l'esigenza di un nuovo account per il team di sviluppo di Alice.  Crea l'account "Ricerca e sviluppo" per il proprio team e assegna l'accesso ad Alice. Alice esegue l'accesso tramite il portale di Account e crea due sottoscrizioni: uno contenente i server di sviluppo e uno contenente i server di produzione.  Anna segue agli standard di denominazione stabiliti in precedenza quando si creano i seguenti abbonamenti: 

| Utilizzo di abbonamento | Nome |
| ---------------- | ---- |
| Sviluppo | Sviluppo di LoyaltyCard SupplyChain ResearchDevelopment |
| Produzione | SupplyChain operazioni LoyaltyCard produzione |

### <a name="policies"></a>Criteri

Discutere l'applicazione di Alice e Dave identificare che questa applicazione serve solo clienti nell'area Nord America.  Alice e team pianificare l'utilizzo di Azure ambiente servizio applicativo e SQL Azure per creare l'applicazione. Si potrebbe essere necessario creare macchine virtuali in fase di sviluppo.  Alice desidera assicurarsi che la sua sviluppatori abbiano le risorse che necessarie per esplorare ed esaminare i problemi senza il pull e in ETS. 

Per la **sottoscrizione di sviluppo**creano i criteri seguenti:

| Campo | Effetto | Descrizione |
| ----- | ------ | ----------- |
| posizione | controllo | Controllare la creazione delle risorse in un'area. |

Non limitare il tipo di sku che un utente può creare in fase di sviluppo e non richiedono tag per tutti i gruppi di risorse o delle risorse.

Per la **sottoscrizione di produzione**creano i criteri seguenti:

| Campo | Effetto | Descrizione |
| ----- | ------ | ----------- |
| posizione | negare | Impedire la creazione di tutte le risorse all'esterno di Stati Uniti data center. |
| tag | negare | Richiedi tag proprietario dell'applicazione |
| tag | negare | Richiede tag di reparto. | 
| tag | accodare | Aggiungere tag a ogni gruppo di risorse che indica l'ambiente di produzione. |

Non limitano il tipo di un utente può creare nell'ambiente di produzione sku.

### <a name="resource-tags"></a>Tag di risorse 

Dave riconosce che ha deve disporre di informazioni specifiche per identificare i gruppi di business corretta per la fatturazione e la proprietà. Si definisce i contrassegni delle risorse per i gruppi di risorse e le risorse. 
 
Nome tag | Valore tag |
| -------- | --------- |
| ApplicationOwner | Il nome della persona che gestisce l'applicazione. |
| Reparto | Centro di costo del gruppo di pagamento per il consumo Azure. |
| EnvironmentType | **Produzione** (Anche se l'abbonamento include **produzione** nel nome, inclusi in questo tag consente semplificarne l'identificazione quando si visualizza risorse nel portale di o sulla distinta.) |

### <a name="core-networks"></a>Reti di base

Il team di gestione Contoso ETS sicurezza e i rischi dell'informazioni rivede piano proposta di Davide e spostare un'applicazione di Azure. Si desidera verificare che l'applicazione di carta fedeltà correttamente isolata e protette in una rete di rete Perimetrale.  Per soddisfare questo requisito, Dave Alice creare una rete virtuale esterna e un gruppo di sicurezza di rete per isolare l'applicazione di carta fedeltà dalla rete aziendale di Contoso.  

Per la **sottoscrizione di sviluppo**creano:

| Tipo di risorsa | Nome | Descrizione |
| ------------- | ---- | ----------- |
| Rete virtuale | vnInternal | Viene utilizzata l'ambiente di sviluppo di carta fedeltà Contoso ed è connesso tramite ExpressRoute alla rete aziendale di Contoso. |

Per la **sottoscrizione di produzione**creano:

| Tipo di risorsa | Nome | Descrizione |
| ------------- | ---- | ----------- |
| Rete virtuale | vnExternal | Ospita l'applicazione di carta fedeltà e non è connesso direttamente a ExpressRoute Contoso. Codice viene inserito tramite il sistema di codice sorgente direttamente ai servizi PaaS. |
| Gruppo di sicurezza di rete | nsgBitBucket | Garantisce che la possibilità di attacchi di questo carico di lavoro è compresso consentendo solo comunicazioni in ingresso nel TCP 443.  Contoso è anche esaminando utilizza un Firewall di applicazione Web per la protezione aggiuntiva. |  

### <a name="resource-locks"></a>Blocchi di risorse 

Dave e Alice conferire e decidere di aggiungere blocchi di risorse su alcune delle risorse di chiave nell'ambiente per accidentali durante un push codice casuali. 

La creazione del blocco seguente:

| Tipo di blocco | Risorsa | Descrizione |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Per impedire alle persone di eliminare il virtuali o subnet. Il blocco non impedisce l'aggiunta di nuove subnet. |

### <a name="azure-automation"></a>Automazione Azure 

Alice e il team di sviluppo hanno vasta runbook per gestire l'ambiente per questa applicazione. I runbook non consentire l'aggiunta o eliminazione dei nodi per l'applicazione e altre attività di attrezzi. 

Per utilizzare questi runbook, consentono di [automazione](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Centro protezione di Azure 

Gestione dei servizi IT di Contoso deve rapidamente identificare e gestire i rischi. Desiderano inoltre capire quali problemi possono esistere.  

Per soddisfare questi requisiti, Dave consente Azure Security Center. Verificare che il Centro protezione di Azure monitoraggio le risorse che consente di accedere ai team di attrezzi e sicurezza. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di modelli di Manager delle risorse, vedere [procedure consigliate per la creazione di modelli di gestione risorse di Azure](resource-manager-template-best-practices.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuito in questo argomento.*
