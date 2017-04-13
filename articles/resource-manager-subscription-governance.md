<properties
   pageTitle="Procedure consigliate per le aziende spostamento Azure | Microsoft Azure"
   description="Descrive una struttura aziende è possono utilizzare per garantire un ambiente protetto e gestibile."
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

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Scaffold Azure enterprise - governance precise abbonamento

Aziende sono sempre più adozione di cloud flessibilità e flessibilità. Si utilizzano resistenze del cloud per generare ricavi o ottimizzare le risorse per le aziende. Microsoft Azure offre una vasta gamma di servizi che è possono assemblare aziende come blocchi predefiniti da un'ampia gamma di applicazioni e carichi di lavoro di indirizzi. 

Tuttavia, conoscere il punto di inizio è spesso difficile. Dopo aver deciso l'utilizzo di Azure, alcuni simboli domande:

- "Come soddisfare i requisiti legali per sovranità dei dati in alcuni paesi?"
- "Come garantire che un utente non inavvertitamente modificato un sistema critico?"
- "Come sapere quali sono supportate tutte le risorse in modo che è possibile includere le it e fattura nuovamente in modo accurato?"

La prospettiva di un abbonamento a vuoto con guide senza protezione è complessa. Questo spazio vuoto di compromettere lo spostamento in Azure.

Questo articolo fornisce un punto di partenza per tecnici soddisfare le esigenze di governance e saldo con l'esigenza di flessibilità. Introduce il concetto di scaffold un'organizzazione che forniscono alle organizzazioni che per l'implementazione e la gestione delle loro sottoscrizioni Azure. 

## <a name="need-for-governance"></a>Necessità di governance

Quando si sposta in Azure, è necessario risolvere l'argomento di governance anticipata per garantire il corretto utilizzo della cloud all'interno dell'azienda. Purtroppo, la creazione di un sistema di governance esaustivo burocrazia e l'ora di conseguenza, alcuni gruppi business passare direttamente ai fornitori senza IT aziendali. Questo approccio può essere lasciata dell'azienda aperta a vulnerabilità se le risorse non sono gestite in modo corretto. Caratteristiche del cloud pubblico flessibilità, flessibilità e basato su consumo prezzi - sono importanti ai gruppi di aziende che devono soddisfare rapidamente le esigenze dei clienti (interne ed esterne). Ma IT aziendali è necessario assicurarsi che i sistemi e dati siano protetti in modo efficace.

In realtà, pagine di supporto temporaneo viene usata per creare le basi della struttura. Il scaffold Guide la struttura generale e fornisce punti di ancoraggio per sistemi più permanenti per essere installato. Un scaffold enterprise corrisponde: un set di controlli flessibili e Azure funzionalità fornite da struttura per l'ambiente e gli ancoraggi per i servizi basati su cloud pubblico. Fornisce i generatori (IT e business gruppi) una base per creare e collegare nuovi servizi.

Il scaffold si basa su consigliate che è stata raccolte da molti impegni con i clienti di varie dimensioni. I client intervallo compreso tra organizzazioni di piccole dimensioni lo sviluppo di soluzioni nel cloud per le aziende Fortune 500 e fornitori di software indipendenti che la migrazione e lo sviluppo di soluzioni nel cloud. Scaffold enterprise è "realizzati" per la massima flessibilità per supportare carichi di lavoro IT tradizionale e carichi di lavoro agile; ad esempio gli sviluppatori la creazione di applicazioni software-come-a-servizio basato sulle funzionalità Azure.

Scaffold enterprise deve essere la base di ogni nuovo abbonamento all'interno di Azure. Consente agli amministratori di garantire carichi di lavoro di soddisfare i requisiti di governance minima di un'organizzazione senza impedisce rapidamente propri obiettivi di business e gli sviluppatori.

> [AZURE.IMPORTANT] Governance è fondamentale per il successo di Azure. In questo articolo è destinato all'implementazione tecnica di un scaffold enterprise ma solo tocca sul processo e le relazioni tra i componenti più ampio. Criteri governance scorra dall'alto verso il basso e dipende da cosa l'azienda desidera realizzare. Naturalmente, la creazione di un modello di governance per Azure include rappresentanti dall'IT, ma soprattutto deve avere rappresentazione sicuro dai responsabili di gruppo business e sicurezza e gestione dei rischi. In conclusione, un scaffold enterprise è su come limitare i rischi aziendali per semplificare l'importanza e obiettivi dell'organizzazione.

Nella figura seguente vengono descritti i componenti del scaffold. La base si basa su un piano a tinta unita per i reparti, account e abbonamenti. I pilastri è costituito da criteri di gestione delle risorse e standard di denominazione sicuro. Il resto della scaffold proviene da principali funzionalità di Azure e le caratteristiche che consentono un ambiente protetto e gestibile.

![componenti Scaffold](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure è diventato rapidamente dalla sua introduzione nel 2008. Questa crescita obbligatorio Microsoft ingegneri di riprogettare approccio per la gestione e la distribuzione di servizi. Il modello di gestione di risorse Azure è stata introdotta in 2014 e sostituisce il modello di distribuzione classica. Manager delle risorse consente alle organizzazioni di distribuire, organizzare e controllare le risorse Azure più facilmente. Gestione risorse include parallelizzazione durante la creazione di risorse per la distribuzione più veloce delle soluzioni complesse, indipendenti. Include inoltre il controllo dell'accesso granulare e la possibilità di risorse tag con i metadati. Si consiglia di creare tutte le risorse tramite il modello di Manager delle risorse. Scaffold enterprise in modo esplicito è progettato per il modello di Manager delle risorse.

## <a name="define-your-hierarchy"></a>Definire la gerarchia

Si basa sul scaffold la registrazione di Enterprise Azure (e il portale aziendale). Registrazione enterprise definisce la forma e utilizzare i servizi Azure all'interno di una società e la struttura di governance core. All'interno di contratto enterprise, clienti sono è possibile suddividere ulteriormente l'ambiente in reparti, account e infine abbonamenti. Una sottoscrizione Azure è l'unità di base in cui sono contenute tutte le risorse. Definisce inoltre diversi limiti all'interno di Azure, ad esempio il numero di core, risorse e così via.

![gerarchia](./media/resource-manager-subscription-governance/agreement.png)

Ogni enterprise è la differenza e la gerarchia nell'immagine precedente consente di notevole flessibilità nell'organizzazione Azure all'interno della società. Prima di implementare le indicazioni contenute in questo documento, è necessario modellare la gerarchia e si conoscono del relativo impatto sulla fatturazione, accesso alle risorse e complessità.

Tre modelli comuni per Azure iscrizioni sono:

- Il modello **funzionale**

    ![funzionali](./media/resource-manager-subscription-governance/functional.png)

- Il modello di **divisione** 

    ![Business](./media/resource-manager-subscription-governance/business.png)

- Motivo **geografici**

    ![geografici](./media/resource-manager-subscription-governance/geographic.png)

Si applica scaffold livello di abbonamento per estendere la governance requisiti dell'organizzazione nella sottoscrizione.

## <a name="naming-standards"></a>Standard di denominazione

Il primo elemento chiave del scaffold è standard di denominazione. Standard di denominazione ben progettato consente di identificare le risorse nel portale di in una fattura e all'interno di script. È probabile che si dispone già di standard di denominazione per un'infrastruttura locale. Quando si aggiunge all'ambiente di Azure, è necessario estendere tali standard di denominazione per le risorse Azure. Assegnare un nome standard facilitare la gestione più efficiente dell'ambiente in tutti i livelli.

> [AZURE.TIP]
>
> - Rivedere e adottare dove possibile le [indicazioni di modelli e procedure consigliate](./guidance/guidance-naming-conventions.md). Questa guida è utile per decidere in uno standard di denominazione significativo.
> - Utilizzare camel per i nomi delle risorse (ad esempio myResourceGroup e vnetNetworkName). Nota: Sono disponibili alcune risorse, ad esempio gli account di archiviazione, in cui l'unica opzione disponibile consiste nell'utilizzare lettere minuscole (e non altri caratteri speciali).
> - Valutare la possibilità di utilizzo dei criteri di gestione risorse Azure (descritti nella sezione seguente) per applicare gli standard di denominazione.
 
## <a name="policies-and-auditing"></a>Criteri e controllo

Il secondo elemento chiave del scaffold consiste nel creare [criteri di gestione risorse di Azure](resource-manager-policy.md) e [il registro attività di controllo](resource-group-audit.md). Criteri di gestione risorse offrono la possibilità di gestire i rischi in Azure. È possibile definire i criteri che garantire sovranità dei dati da limitazione, imposte o il controllo a determinate azioni. 

- Criteri sono un sistema di **consentire** l'impostazione predefinita. Controllare le azioni definendo e assegnazione di criteri alle risorse che negano o controllare le azioni sulle risorse.
- Criteri sono descritti dalla definizione dei criteri in una lingua di definizione di criteri (condizioni if then).
- Creare criteri con JSON (Javascript Object Notation) file formattati. Dopo aver definito un criterio, viene assegnato a un particolare ambito: abbonamento, gruppo di risorse. o una risorsa.

Criteri hanno più azioni che consentono di un approccio diffusamente per gli scenari. Le azioni disponibili sono:

-   **Nega**: blocca richiesta della risorsa
-   **Controllo**: consente la richiesta ma aggiunge una riga per il registro attività (che può essere utilizzato per fornire avvisi o per impostare un trigger runbook)
-   **Accoda**: aggiunge le informazioni specificate per la risorsa. Ad esempio, se in una risorsa non è presente un tag "CostCenter", aggiungere tale tag con un valore predefinito.

### <a name="common-uses-of-resource-manager-policies"></a>Comuni di utilizzo dei criteri di gestione risorse

Criteri di gestione risorse Azure sono un potente strumento il toolkit di Azure. Consentono di evitare i costi di imprevisti, per identificare un centro di costo per le risorse tramite tagging e assicurarsi che siano soddisfatti i requisiti della compatibilità. Quando i criteri vengono combinati con le caratteristiche di controllo predefinite, è possibile personalizzare le soluzioni complesse e flessibile. Criteri consentono alle aziende di fornire i controlli per carichi di lavoro "IT tradizionale" e "Agile" carichi di lavoro; ad esempio, lo sviluppo di applicazioni del cliente. I motivi più comuni che vediamo per i criteri sono:

- **Sovranità geografico conformità dei dati** - Azure fornisce aree in tutto il mondo. Aziende spesso desiderano controllare nel punto in cui vengono create le risorse (se si desidera verificare sovranità dei dati o semplicemente per assicurarsi che le risorse vengono create vicino utenti finali delle risorse).
- **Gestione dei costi** - abbonamento di Azure può contenere risorse di molti tipi e scala. Le organizzazioni spesso fare in modo che le sottoscrizioni standard evitare l'utilizzo di risorse inutilmente grandi, che possono richiedere centinaia di dollari un mese o più.
- **Predefinito governance tramite tag richiesti** - che richiedono tag è una delle caratteristiche più comuni e altamente desiderate. Utilizzo dei criteri di gestione risorse Azure aziende sono in grado garantire che una risorsa viene contrassegnata in modo appropriato. Tag più comuni sono: reparto, proprietario della risorsa e ambiente di tipo (ad esempio - produzione, test e sviluppo)

**Esempi**

"Tradizionale IT" abbonamento per le applicazioni line-of-business

-   Applicare contrassegni reparto e proprietario in tutte le risorse
-   Limitare la creazione di risorse all'area del Nord America
-   Limitare la possibilità di creare macchine virtuali di serie G e HDInsight cluster

"Agile" ambiente per una business unit la creazione di applicazioni cloud

- Consentire la creazione delle risorse per soddisfare requisiti sovranità dei dati, solo in un'area specifica.
- Applicare il contrassegno di ambiente in tutte le risorse. Se si crea una risorsa senza un tag, aggiungere il **ambiente: sconosciuto** tag per la risorsa.
- Controllo quando le risorse vengono create fuori del Nord America ma non impedisce il.
- Controllo quando vengono create le risorse costi alta.

> [AZURE.TIP]
>
> L'uso più comune di criteri di gestione delle risorse tra più organizzazioni è a controllo *nel punto in cui* le risorse possono essere create e *tipi di risorse* è possono creare. Oltre a controlli nella *posizione in cui* e *quali*, molte organizzazioni utilizzano criteri per assicurarsi che le risorse sono i metadati appropriati per la fatturazione indietro consumo. È consigliabile l'applicazione di criteri a livello di abbonamento per:
>
> - Sovranità geografico conformità dei dati
> - Gestione dei costi
> - Tag richiesti (in base esigenza aziendale, ad esempio BillTo, proprietario dell'applicazione)
>
> È possibile applicare criteri aggiuntivi livello più basso dell'ambito.
 
### <a name="audit---what-happened"></a>Controllo - cosa è successo?

Per visualizzare funzionamento dell'ambiente, è necessario controllare l'attività. La maggior parte dei tipi di risorse all'interno di Azure creano registri diagnostici che è possibile analizzare tramite uno strumento log o nella famiglia di prodotti di Azure operazioni di gestione. È possibile raccogliere i registri delle attività in più abbonamenti per fornire un reparto o visualizzazione dell'organizzazione. Record di controllo sono uno strumento diagnostico importante e un meccanismo fondamentale per avviare eventi nell'ambiente di Azure.

Registri delle attività di gestione risorse distribuzioni consentono di determinare le **operazioni** che ha posizionare e chi ha eseguito. Registri delle attività possono essere raccolti e aggregati utilizzando strumenti come Log Analitica.

## <a name="resource-tags"></a>Tag di risorse

Man mano che gli utenti dell'organizzazione aggiungono risorse per la sottoscrizione, è sempre più importante da associare risorse reparto appropriato, clienti e ambiente. È possibile allegare metadati alle risorse tramite [tag](resource-group-using-tags.md). Utilizzare tag per fornire informazioni sulla risorsa o il proprietario. I contrassegni consentono di non solo aggregare e raggruppare le risorse in vari modi, ma utilizzare tali dati ai fini del rifiuto. È possibile contrassegnare le risorse con fino a 15 coppie di parole chiave: valore. 

Tag di risorse sono flessibili e da collegare alla maggior parte delle risorse. Esempi di tag di risorse comuni sono:

- BillTo
- Reparto (o Business Unit)
- Ambiente (sviluppo di produzione, fase)
- Livello (livello Web, livello applicazione)
- Proprietario dell'applicazione
- NomeProgetto

![tag](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Per altri esempi di tag, vedere [consigliate per le risorse Azure convenzioni di denominazione](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Creare una strategia di tagging che identifica nelle proprie sottoscrizioni quali metadati sono necessari per la business, finanze, sicurezza, gestione dei rischi e la gestione dell'ambiente. Si consiglia di eseguire un criterio che impone il tagging per:
>
> - Gruppi di risorse
> - Spazio di archiviazione
> - Macchine virtuali
> - Server servizio ambienti/web delle applicazioni

## <a name="resource-group"></a>Gruppo di risorse 

Manager delle risorse consente di inserire le risorse nei gruppi significativi per affinità fatturazione o naturale di gestione. Come detto in precedenza, Azure include due modelli di distribuzione. Nel modello di classica precedente, l'unità di base della gestione dei durante la sottoscrizione. Era difficile suddividere le risorse all'interno di una sottoscrizione, portato alla creazione di un numero elevato di sottoscrizioni. Con il modello di Manager delle risorse, visto l'introduzione di gruppi di risorse. Gruppi di risorse sono contenitori di risorse che dispone di un ciclo di vita comune o condividono un attributo, ad esempio "tutti i server SQL" o "Applicazione A".

Gruppi di risorse non possono essere contenuti all'interno di altra e le risorse possono appartenere solo a un gruppo di risorse. È possibile applicare determinate azioni in tutte le risorse in un gruppo di risorse. Eliminazione di un gruppo di risorse, ad esempio, consente di rimuovere tutte le risorse all'interno del gruppo di risorse. In genere, inserire un'intera applicazione o un sistema correlato nello stesso gruppo di risorse. Ad esempio, un'applicazione di tre livelli denominata Contoso Web applicazione contiene il server web, server applicazioni e SQL server nello stesso gruppo di risorse.

> [AZURE.TIP]
>
> Come si organizzano i gruppi di risorse può variare da carichi di lavoro "IT tradizionale" per carichi di lavoro "Agile IT"
>
> - "Tradizionale IT" carichi di lavoro più comunemente raggruppate per gli elementi all'interno di ciclo di vita stesso, ad esempio un'applicazione. Raggruppamento dall'applicazione consente la gestione di singole applicazioni.
> - "Agile IT" carichi di lavoro di comandi sono particolarmente utili per concentrarsi su applicazioni cloud per i clienti esterni. I gruppi di risorse rispecchiare i livelli di distribuzione (ad esempio Web livello, livello App) e gestione.

## <a name="role-based-access-control"></a>Controllo dell'accesso basato sui ruoli

Probabile che vengono chiedersi "che devono avere accesso alle risorse?" e "come controllare l'accesso?" Se si consente o impedisce l'accesso al portale di Azure e il controllo dell'accesso alle risorse nel portale di è fondamentale. 

Quando è stata rilasciata Azure, accedere ai controlli a una sottoscrizione erano di base: amministratore o condivisa. Accedere a un abbonamento l'accesso al modello implicito classica a tutte le risorse nel portale. Questa mancanza di un controllo preciso condotto proliferazione degli abbonamenti per fornire un livello di controllo l'accesso per un'iscrizione Azure.

Questo proliferazione delle sottoscrizioni non è più necessaria. Controllo dell'accesso basato sui ruoli, è possibile assegnare utenti ai ruoli standard (ad esempio "lettore" e "writer" ai tipi comuni dei ruoli). È inoltre possibile definire ruoli personalizzati.

> [AZURE.TIP]
>  
> - Connettere Azure Active Directory utilizzando lo strumento AD Connect archivio di identità aziendale (in genere Active Directory).
> - Controllare l'amministrazione/Co-amministratore di una sottoscrizione tramite un'identità gestita. **Non** assegnare amministrazione/Co-amministratore a un nuovo proprietario della sottoscrizione. Utilizzare invece ruoli RBAC per fornire diritti **proprietario** a un gruppo o singole.
> - Aggiungere utenti Azure a un gruppo (ad esempio, i proprietari X applicazione) in Active Directory. Utilizzare il gruppo sincronizzato per fornire i membri del gruppo dei diritti appropriati per gestire il gruppo di risorse contenente l'applicazione.
> - Seguire il principio dei **privilegi minimi** necessari per svolgere il lavoro previsto concessione. Per esempio:
>     - Gruppo di distribuzione: Un gruppo di cui è possibile distribuire le risorse.
>     - Gestione delle macchine virtuali: Un gruppo a cui è in grado di riavviare macchine virtuali (per le operazioni)

## <a name="azure-resource-locks"></a>Blocchi di risorse Azure

Come organizzazione aggiunge servizi di base per l'abbonamento, è sempre più importante per assicurarsi che i servizi disponibili evitare interruzioni delle attività aziendali. [Blocchi di risorse](resource-group-lock-resources.md) consente di limitare le operazioni sulle risorse alto valore in cui vengano modificati o eliminati hanno un impatto significativo su applicazioni o infrastruttura cloud. È possibile applicare blocchi a un abbonamento, gruppo di risorse o delle risorse. In genere, vengono applicati blocchi alle risorse di base, ad esempio reti virtuali, gateway e gli account di archiviazione. 

Blocchi di risorse supportano attualmente due valori: CanNotDelete e sola lettura. CanNotDelete significa che gli utenti (i diritti appropriati) possano quindi leggere o modifica una risorsa, ma non è possibile eliminarlo. Sola lettura indica che gli utenti autorizzati non è possibile eliminare o modificare una risorsa.

Per creare o eliminare blocchi management, è necessario avere accesso a `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*` azioni.
Ruoli predefiniti solo proprietario e l'amministratore di accesso utente disponga queste azioni.

> [AZURE.TIP] Opzioni di rete di base devono essere protetto con blocchi. Eliminazione accidentale di un gateway, VPN da sito saranno gravi a un abbonamento a Azure. Azure non consente di eliminare una rete virtuale in uso, ma l'applicazione di più restrizioni è una misura utile. Criteri sono inoltre fondamentali per la manutenzione dei controlli appropriati. Si consiglia di applicare un blocco di **CanNotDelete** di criteri in uso.
>
> - Virtuali: CanNotDelete
> - Gruppo di sicurezza di rete: CanNotDelete
> - Criteri: CanNotDelete

## <a name="core-networking-resources"></a>Risorse di rete di base

Accesso alle risorse può essere interno (all'interno della rete aziendale) o esterno (tramite internet). È facile per gli utenti dell'organizzazione inavvertitamente inserire risorse nel posto sbagliato e potenzialmente aprirle per l'accesso non autorizzato. Come con i dispositivi in locale, aziende necessario aggiungere controlli appropriati per assicurarsi che gli utenti Azure prendere le decisioni destra. Per la supervisione abbonamento, vengono illustrate le risorse di base che forniscono semplice per i controlli di access. Le risorse principali è costituito da:

- **Reti virtuali** sono oggetti contenitore per subnet. Sebbene non sia strettamente necessario viene spesso utilizzato quando ci si connette applicazioni alle risorse aziendali interne.
- **Gruppi di sicurezza di rete** sono simili a un firewall e fornire le regole per come una risorsa può "comunicare" in rete. Consentono di controllare granulare come / se una subnet o macchina virtuale, è possibile connettersi a Internet o altre subnet nella stessa rete virtuale.

![rete principale](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Creare reti virtuali impegna costantemente per carichi di lavoro esterna e interna carichi di lavoro. Questo approccio riduce la possibilità di immissione inavvertitamente macchine virtuali che devono essere interni carichi di lavoro in un unico spazio esposto esterno.
> - Gruppi di sicurezza di rete sono fondamentali per la configurazione. Almeno, bloccare l'accesso a internet da reti interne virtuale e bloccare l'accesso alla rete aziendale da reti esterne virtuale.

### <a name="automation"></a>Automazione

Gestire le risorse singolarmente è lunga e laboriosa e potenzialmente errori per determinate operazioni. Azure offre diverse funzionalità di automazione inclusi automazione Azure, logica App e le funzioni di Azure. [Automazione di Azure](./automation/automation-intro.md) consente agli amministratori di creare e definire runbook per gestire attività comuni di gestione delle risorse. Creare runbook usando un editor di codice PowerShell o un editor grafico. È possibile creare flussi di lavoro in più fasi complessi. Automazione Azure viene spesso utilizzato per gestire attività comuni, ad esempio arrestare risorse non utilizzate o la creazione di risorse in risposta a un trigger specifico senza la necessità di intervento.

> [AZURE.TIP]
>
> - Creare un account Azure automazione e rivedere i runbook disponibile (riga grafica e comando) disponibili nella [Raccolta Runbook](./automation/automation-runbook-gallery.md).
> - Importare e personalizzare chiave runbook per uso personale.
>
> Scenari comuni sono la possibilità di macchine virtuali di inizio/arresto in base alla pianificazione. Esistono runbook di esempio sono disponibili nella raccolta che consentono di gestire questo scenario e istruzioni per espanderlo.


## <a name="azure-security-center"></a>Centro protezione di Azure

Ad esempio uno dei principali blocchi nel cloud adozione è stata le preoccupazioni sulla sicurezza. È necessario assicurarsi che le risorse in Azure sono protette responsabili IT e reparti di sicurezza. 

[Azure Security Center](./security-center/security-center-intro.md) fornisce una visualizzazione centrale dello stato di protezione delle risorse nelle sottoscrizioni e fornisce indicazioni che consentono di evitare che le risorse manomesso. È possibile abilitare criteri più specifici (ad esempio, applicazione di criteri a gruppi di risorse specifici che consentono la enterprise adattare le condizioni per il rischio che si indirizzamento). Infine, Centro protezione di Azure è una piattaforma aperta che consente a partner Microsoft e fornitori di software indipendenti di software che si inserisce nel Centro protezione di Azure per migliorarne le funzionalità di creazione. 

> [AZURE.TIP]
>
> Centro protezione di Azure è attivato per impostazione predefinita in ciascuna sottoscrizione. Tuttavia, è necessario abilitare la raccolta di dati da macchine virtuali per consentire di Azure Security Center installare l'agente e iniziare la raccolta di dati.
>
> ![raccolta di dati](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Passaggi successivi

- Dopo avere appreso sulla governance sottoscrizione, è possibile vedere questi suggerimenti nell'esercitazione. Vedere [esempi di implementazione di governance Azure abbonamento](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuito in questo argomento.*
