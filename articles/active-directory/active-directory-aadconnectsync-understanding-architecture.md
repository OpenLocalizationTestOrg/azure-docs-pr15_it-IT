<properties
   pageTitle="Sincronizzazione di Azure AD Connect: Introduzione all'architettura | Microsoft Azure"
   description="In questo argomento vengono descritti l'architettura di sincronizzazione di Azure AD Connect e i termini usati."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronizzazione di Azure AD Connect: Introduzione all'architettura
In questo argomento è descritta l'architettura di base per la sincronizzazione di Azure AD Connect. In molti aspetti è simile ai predecessori MIIS 2003, ILM 2007 e 2010 FIM. Sincronizzazione di Azure AD Connect è l'evoluzione di queste tecnologie. Se si ha familiarità con una di queste tecnologie precedenti, il contenuto di questo argomento sarà familiare anche. Se ha familiarità con la sincronizzazione, in questo argomento è automaticamente. Tuttavia non è necessario conoscere i dettagli di questo argomento per essere eseguita correttamente in esecuzione di personalizzazioni per la sincronizzazione di Azure AD Connect (denominato motore di sincronizzazione in questo argomento).

## <a name="architecture"></a>Architettura
Motore di sincronizzazione consente di creare una visualizzazione integrata di oggetti che sono archiviate in più origini dati connesse e si gestisce le informazioni sull'identità in tali origini dati. Questa visualizzazione integrata dipende dalle informazioni sull'identità recuperati da origini dati connesse e un set di regole che determinano come elaborare queste informazioni.

### <a name="connected-data-sources-and-connectors"></a>Origini dati connesse e connettori
Il motore di sincronizzazione elabora le informazioni sull'identità da archivi dati diversi, ad esempio Active Directory o un database SQL Server. Ogni archivio di dati che consente di organizzare i dati in un formato di database e che fornisce i metodi di accesso ai dati standard è un potenziale candidato di origine dati per il motore di sincronizzazione. Archivi i dati che vengono sincronizzati dal motore di sincronizzazione sono denominate **connessi origini dati** o **connessi directory** (CD).

Motore di sincronizzazione incapsula interazione con un'origine dati connessi all'interno di un modulo denominato un **connettore**. Ogni tipo di origine dati connessa dispone di un connettore specifico. Il connettore viene convertita un'operazione necessaria nel formato che consente di usare l'origine dati connessa.

Connettori effettuare chiamate API per lo scambio di informazioni sulle identità (leggere e scrivere) con un'origine dati connessa. È anche possibile aggiungere un connettore personalizzato utilizzando il framework di connettività extensible. L'illustrazione seguente mostra come un connettore connette un'origine dati connessa al motore di sincronizzazione.

![Arc1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Possano flusso di dati in entrambe le direzioni, ma non è possibile scorrere in entrambe le direzioni contemporaneamente. In altre parole, un connettore, questo può essere configurato per consentire il flusso dall'origine dati connessa al motore di sincronizzazione o dal motore di sincronizzazione per l'origine dati connessa di dati, ma solo una di queste operazioni può verificarsi in qualsiasi momento per un oggetto e attributo. La direzione può essere diversa per diversi oggetti e attributi diversi.

Per configurare un connettore, specificare i tipi di oggetto che si desidera sincronizzare. Specificare i tipi di oggetto consente di definire l'ambito degli oggetti inclusi nel processo di sincronizzazione. Il passaggio successivo consiste nel selezionare gli attributi da sincronizzare, che è noto come un elenco di inclusione attributo. Queste impostazioni possono essere modificate qualsiasi momento in risposta a modifiche alle regole aziendali. Quando si utilizza la procedura guidata installazione di Azure AD Connect, questi vengono configurate automaticamente.

Per esportare gli oggetti in un'origine dati connessa, l'elenco di inclusione attributo deve includere almeno gli attributi minimi necessari per creare un tipo di oggetto specifico in un'origine dati connessa. Ad esempio, l'attributo **sAMAccountName** da includere nell'elenco di inclusione attributo per esportare un oggetto utente in Active Directory perché tutti gli oggetti utente in Active Directory devono avere un attributo **sAMAccountName** definito. Di nuovo l'installazione guidata indica questa configurazione dell'utente.

Se l'origine dati connessa utilizza componenti strutturali, ad esempio le partizioni o contenitori per organizzare gli oggetti, è possibile limitare le aree di origine dati connessa utilizzati per una determinata soluzione.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Struttura interna dello spazio dei nomi di motore di sincronizzazione
Lo spazio dei nomi motore di sincronizzazione intera è costituito da due spazi dei nomi che memorizzano le informazioni sull'identità. Due spazi dei nomi sono:

- Lo spazio di connettore (CS)
- Metaverse (MV)

Lo **spazio connettore** è un'area di gestione temporanea contenente le rappresentazioni di oggetti designati da un'origine dati connessa e gli attributi specificati nell'elenco di inclusione attributo. Motore di sincronizzazione utilizza lo spazio del connettore per individuare le modifiche apportate nell'origine dati connessa e per le modifiche in ingresso sul passaggio. Motore di sincronizzazione Usa anche lo spazio del connettore in fasi modifiche in uscita per l'esportazione all'origine dati connessa. Motore di sincronizzazione mantiene uno spazio connettore distinti come un'area di gestione temporanea per ogni connettore.

Utilizzando un'area di gestione temporanea, il motore di sincronizzazione resta indipendente delle origini dati connesse e non viene influenzato dal loro disponibilità e l'accessibilità. Di conseguenza, è possibile elaborare le informazioni sull'identità in qualsiasi momento utilizzando i dati nell'area di gestione temporanea. Motore di sincronizzazione può richiedere solo le modifiche apportate all'interno dell'origine dati connessa dopo l'ultimo sessione di comunicazione terminate o push solo le modifiche alle informazioni di identità l'origine dati connessa non ha ancora ricevuto, in modo da ridurre il traffico di rete tra il motore di sincronizzazione e l'origine dati connessa.

Motore di sincronizzazione archivia inoltre informazioni sullo stato relative a tutti gli oggetti fasi nell'area connettore. Quando si riceve nuovi dati, motore di sincronizzazione valuta sempre se i dati sono già stati sincronizzati.

**Metaverse** è un'area di archiviazione che contiene le informazioni sull'identità aggregati da più origini dati connessi, offrendo un'unica visualizzazione globale e integrata di tutti gli oggetti combinati. Oggetti del Metaverse vengono creati in base alle informazioni di identità recuperati da origini dati connesse e un set di regole che consentono di personalizzare il processo di sincronizzazione.

L'illustrazione seguente mostra lo spazio dei nomi spazio connettore e lo spazio dei nomi di metaverse all'interno del motore di sincronizzazione.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Oggetti identità motore di sincronizzazione
Gli oggetti nel motore di sincronizzazione sono rappresentazioni di oggetti nell'origine dati connessa o della visualizzazione integrata che motore di sincronizzazione di tali oggetti. Ogni oggetto motore di sincronizzazione deve avere un identificatore univoco globale (GUID). GUID forniscono express relazioni tra gli oggetti e l'integrità dei dati.

### <a name="connector-space-objects"></a>Oggetti spazio connettore
Quando il motore di sincronizzazione comunica con un'origine dati connessa, legge le informazioni sull'identità dell'origine dati connessa e le informazioni vengono utilizzate per creare una rappresentazione dell'oggetto identity nello spazio connettore. È possibile creare o eliminare gli oggetti singolarmente. Tuttavia, è possibile eliminare manualmente tutti gli oggetti in uno spazio connettore.

Tutti gli oggetti nello spazio connettore è due attributi:

- Un identificatore univoco globale (GUID)
- Un nome distinto (noto anche come nome distinto)

Se l'origine dati connessa assegna un attributo univoco all'oggetto, gli oggetti nello spazio connettore possono inoltre includere un attributo di ancoraggio. L'attributo di ancoraggio identifica un oggetto nell'origine dati connessa. Motore di sincronizzazione utilizzato l'ancoraggio per individuare la rappresentazione dell'oggetto corrispondente nell'origine dati connessa. Motore di sincronizzazione si presuppone che l'ancoraggio di un oggetto non cambia mai per tutta la durata dell'oggetto.

Molti dei connettori utilizzare un identificatore univoco noto per generare un ancoraggio automaticamente per tutti gli oggetti durante l'importazione. Ad esempio, Active Directory Connector utilizza l'attributo **objectGUID** per un ancoraggio. Per le origini dati connessi che non dispongono di un identificatore univoco precisi, è possibile specificare generazione ancoraggio durante la configurazione del connettore.

In caso, l'ancoraggio integrato da uno o più attributi specifici di un oggetto digitare, né delle modifiche apportate e che in modo univoco identifica l'oggetto nell'area connettore (ad esempio un dipendente o un ID utente).

Un oggetto di spazio connettore può essere una delle operazioni seguenti:

- Un oggetto di gestione temporaneo
- Un segnaposto

### <a name="staging-objects"></a>Oggetti di gestione temporanea
Un oggetto di gestione temporaneo rappresenta un'istanza dei tipi di oggetto designato dall'origine dati connessa. Oltre alle GUID e il nome distinto, un oggetto di gestione temporaneo ha sempre un valore che indica il tipo di oggetto.

Gli oggetti di gestione temporanea che sono stati importati sempre è un valore per l'attributo di ancoraggio. Oggetti di gestione temporanea che sono state appena viene completato il provisioning dal motore di sincronizzazione e sono in corso la creazione dell'origine dati connessa non dispone di un valore per l'attributo di ancoraggio.

Oggetti di gestione temporanea eseguono anche i valori correnti degli attributi business e informazioni operative necessarie dal motore di sincronizzazione per eseguire il processo di sincronizzazione. Informazioni operative includono contrassegni che indicano il tipo di aggiornamento in più fasi nell'oggetto di gestione temporanea. Se un oggetto di gestione temporaneo ha ricevuto informazioni sulla nuova identità dall'origine dati connessa che non è ancora stata elaborata, l'oggetto è contrassegnata come **importazione in sospeso**. Se un oggetto di gestione temporaneo include nuove informazioni di identità che non sono ancora state esportate all'origine dati connessa, viene contrassegnato come **in attesa di esportazione**.

Un oggetto di gestione temporaneo può essere un oggetto di importazione o esportazione. Motore di sincronizzazione crea un oggetto di importazione utilizzando le informazioni sull'oggetto ricevute dall'origine dati connessa. Quando il motore di sincronizzazione riceve le informazioni sull'esistenza di un nuovo oggetto che corrisponde a uno dei tipi di oggetto selezionati in Connector, viene creato un oggetto di importazione nello spazio connettore come rappresentazione dell'oggetto all'interno dell'origine dati connessa.

L'illustrazione seguente mostra un oggetto di importazione che rappresenta un oggetto nell'origine dati connessa.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Motore di sincronizzazione crea un oggetto di esportazione utilizzando le informazioni sull'oggetto nel metaverse. Esportare gli oggetti vengono esportati in origine dati connessa durante la sessione di comunicazione successiva. Dal punto di vista del motore di sincronizzazione, esportare gli oggetti non esistono nell'origine dati connessa ancora. L'attributo di ancoraggio per un oggetto di esportazione di conseguenza, non è disponibile. Dopo aver ricevuto l'oggetto del motore di sincronizzazione, l'origine dati connessa crea un valore univoco per l'attributo di ancoraggio dell'oggetto.

L'illustrazione seguente mostra la modalità di creazione di un oggetto di esportazione utilizzando le informazioni sull'identità nel metaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Motore di sincronizzazione reimportare oggetto dall'origine dati connessa confermata l'esportazione dell'oggetto. Esportare gli oggetti diventano Importa oggetti quando li riceve motore di sincronizzazione durante l'importazione successivo dall'origine dati connessa.

### <a name="placeholders"></a>Segnaposto
Il motore di sincronizzazione utilizza uno spazio dei nomi flat per archiviare oggetti. Tuttavia, alcune origini dati connessi, ad esempio Active Directory utilizzano uno spazio dei nomi gerarchico. Per trasformare le informazioni provenienti da uno spazio dei nomi gerarchico in uno spazio dei nomi piatto, motore di sincronizzazione utilizza i segnaposto per mantenere la gerarchia.

Ogni segnaposto rappresenta un componente (ad esempio un'unità organizzativa) di nome gerarchico dell'oggetto che non è stato importato nel motore di sincronizzazione, ma è necessario per costruire il nome gerarchico. La compilazione di spazi vuoti creati da riferimenti nell'origine dati connessa a oggetti che non sono di gestione temporanea oggetti nello spazio connettore.

Motore di sincronizzazione utilizza anche i segnaposto per archiviare gli oggetti con riferimenti che non sono ancora stati importati. Ad esempio, se sincronizzazione è configurata in modo da includere l'attributo responsabile per l'oggetto *Abbie verdi* e il valore ricevuto è un oggetto che non è stato importato ancora, ad esempio *CN = Lee Sperry, CN = utenti, Cc = fabrikam, Cc = com*, le informazioni di gestione vengono archiviate come segnaposto nell'area connettore. Se l'oggetto manager viene importato in un secondo momento, segnaposto oggetto viene sovrascritta dall'oggetto di gestione temporanea che rappresenta il responsabile.

### <a name="metaverse-objects"></a>Oggetti del Metaverse
Un oggetto metaverse contiene la visualizzazione aggregata tale motore di sincronizzazione sono previsti gli oggetti di gestione temporanea nello spazio connettore. Motore di sincronizzazione crea metaverse oggetti utilizzando le informazioni in Importa oggetti. Diversi oggetti spazio connettore possono essere collegati a un oggetto singolo metaverse, ma non è possibile collegare un oggetto di spazio connettore a più di un oggetto metaverse.

Oggetti del Metaverse non è possibile manualmente creati o eliminati. Motore di sincronizzazione elimina automaticamente gli oggetti metaverse che non dispongono di un collegamento a qualsiasi oggetto spazio connettore nello spazio connettore.

Per mappare gli oggetti all'interno di un'origine dati connessa a un tipo di oggetto corrispondente all'interno di metaverse, motore di sincronizzazione fornisce uno schema extensible con un set predefinito di tipi di oggetti e attributi associati. È possibile creare nuovi tipi di oggetti e attributi per gli oggetti metaverse. Gli attributi possono essere a valore singolo o multivalore e i tipi di attributi possono essere stringhe, riferimenti, numeri e valori booleani.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relazioni tra gli oggetti di gestione temporaneo e metaverse
All'interno di nomi del motore di sincronizzazione, il flusso di dati è abilitato per la relazione di collegamento tra gli oggetti di gestione temporaneo e metaverse. Un oggetto di gestione temporaneo è collegato a un oggetto metaverse si chiama un **oggetto il join** (o un **connettore oggetto**). Un oggetto di gestione temporaneo che non è collegato a un oggetto metaverse è denominato **separato oggetto** (o **oggetto sezionatore**). I termini il join e separato Preferiti per non confondere con i connettori responsabili per l'importazione ed esportazione di dati di una directory connessa.

Segnaposto mai sono collegati a un oggetto metaverse

Un oggetto collegato tramite join è costituito da un oggetto di gestione temporaneo e la relazione collegata a un oggetto singolo metaverse. Oggetti collegati vengono utilizzati per sincronizzare i valori di attributo tra un oggetto di spazio connettore e un oggetto metaverse.

Quando un oggetto di gestione temporaneo diventa un oggetto collegato tramite join durante la sincronizzazione, gli attributi possono passare tra l'oggetto di gestione temporanea e l'oggetto metaverse. Il flusso di attributi è bidirezionale e viene configurato tramite regole attributo di importazione e attributo Esporta.

Un oggetto di spazio singolo connettore può essere collegato a un solo oggetto metaverse. Tuttavia, ogni oggetto metaverse può essere collegato a più oggetti di spazio di connettore nello stesso o in spazi connettore diverso, come illustrato nella figura seguente.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

La relazione tra l'oggetto di gestione temporanea e un oggetto metaverse collegata permanente e può essere rimossa ma solo in base alle regole specificate.

Un oggetto separato è un oggetto di gestione temporaneo che non è collegato a qualsiasi oggetto metaverse. L'attributo i valori di un oggetto separato non sono elaborate qualsiasi ulteriormente all'interno di metaverse. I valori degli attributi dell'oggetto corrispondente nell'origine dati connessa non vengono aggiornati dal motore di sincronizzazione.

L'utilizzo di oggetti separati, è possibile memorizzare le informazioni sull'identità motore di sincronizzazione ed elaborare in un secondo momento. Mantenere un oggetto di gestione temporaneo come oggetto separato nello spazio connettore offre molti vantaggi. Perché il sistema ha già disponibili temporaneamente le informazioni richieste sull'oggetto, non è necessario creare una rappresentazione di questo oggetto nuovamente durante l'importazione successivo dall'origine dati connessa. In questo modo, motore di sincronizzazione ha sempre uno snapshot completo dell'origine dati connessa, anche se non vi è alcuna connessione all'origine dati connessa. Oggetti separati possono essere convertiti in oggetti collegati e viceversa, in base alle regole specificate.

Viene creato un oggetto di importazione come oggetto separato. Un oggetto di esportazione deve essere un oggetto collegato tramite join. La logica del sistema applica questa regola ed Elimina tutti gli oggetti di esportazione che non sono un oggetto collegato tramite join.

## <a name="sync-engine-identity-management-process"></a>Processo di gestione delle identità del motore di sincronizzazione
Il processo di gestione delle identità consente di controllare la modalità di aggiornamento di informazioni sulle identità tra le origini dati connessi diversi. Gestione delle identità si verifica in tre processi:

- Importazione
- Sincronizzazione
- Esportazione

Durante il processo di importazione, motore di sincronizzazione valuta le informazioni sull'identità in ingresso da un'origine dati connessa. Quando vengono rilevate modifiche, crea nuovi oggetti di gestione temporaneo o aggiorna gli oggetti di gestione temporaneo esistenti nell'area connettore per la sincronizzazione.

Durante il processo di sincronizzazione, motore di sincronizzazione aggiorna metaverse per riflettere le modifiche apportate nello spazio connettore e aggiorna lo spazio del connettore in base alle modifiche apportate nel metaverse.

Durante il processo di esportazione, motore di sincronizzazione inserisce le modifiche che sono gestiti in oggetti di gestione temporanea e che vengono contrassegnate come in sospeso di esportazione.

In ciascuno dei processi si presenta come flussi di informazioni di identità da un'origine dati connessa a un'altra illustrazione seguente.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processo di importazione
Durante il processo di importazione, motore di sincronizzazione valuta aggiornamenti apportati alle informazioni sulle identità. Motore di sincronizzazione confronta le informazioni sull'identità ricevute dall'origine dati connessa con le informazioni di identità relative a un oggetto di gestione temporanea e determina se l'oggetto di gestione temporanea richiede aggiornamenti. Se è necessario aggiornare l'oggetto di gestione temporanea con i nuovi dati, l'oggetto di gestione temporanea viene contrassegnata come in sospeso importazione.

Per gli oggetti nello spazio connettore prima della sincronizzazione di gestione temporanea, motore di sincronizzazione può elaborare solo le informazioni sull'identità che sono state modificate. Questo processo fornisce i seguenti vantaggi:

- **Sincronizzazione efficiente**. La quantità di dati elaborati durante la sincronizzazione è ridotta a icona.
- **Risincronizzazione efficiente**. È possibile modificare le modalità di elaborazione delle informazioni sull'identità senza riconnessione il motore di sincronizzazione all'origine dati motore di sincronizzazione.
- **Possibilità di visualizzare in anteprima la sincronizzazione**. È possibile visualizzare l'anteprima di sincronizzazione per verificare che le ipotesi di informazioni sul processo di gestione delle identità siano corrette.

Per ogni oggetto specificato nel connettore, il motore di sincronizzazione prima tenta di individuare una rappresentazione dell'oggetto nello spazio connettore del connettore. Motore di sincronizzazione vengono esaminati tutti gli oggetti di gestione temporaneo nell'area connettore e tenta di individuare un oggetto di gestione temporaneo corrispondente che ha un attributo ancoraggio corrispondente. Se nessun oggetto di gestione temporanea esistente ha un attributo ancoraggio corrispondente, motore di sincronizzazione tenta di individuare un oggetto di gestione temporaneo corrispondente con lo stesso nome distinto.

Quando il motore di sincronizzazione rileva un oggetto di gestione temporaneo corrispondente dal nome distinto ma non dall'ancoraggio, si verifica il comportamento speciale seguente:

- Se l'oggetto posizionato nell'area connettore non ha alcun ancoraggio, motore di sincronizzazione rimuove l'oggetto dallo spazio connettore e contrassegna l'oggetto metaverse che collegarla a come **riprovare a eseguire il provisioning di sincronizzazione successiva**. Quindi, viene creato il nuovo oggetto di importazione.
- Se l'oggetto posizionato nell'area connettore ha un ancoraggio, motore di sincronizzazione si presuppone che l'oggetto è stato rinominato o eliminato nelle directory connesse. Assegna un nome distinto temporaneo, nuovo per l'oggetto di spazio connettore in modo che è possibile inserire la l'oggetto in arrivo. L'oggetto precedente diventa quindi **temporanee**, in attesa per il connettore importare la ridenominazione o eliminazione per risolvere il problema.

Motore di sincronizzazione individua un oggetto di gestione temporaneo che corrisponde all'oggetto specificato nel connettore, determina quali tipi di modifiche da applicare. Ad esempio, motore di sincronizzazione può rinominare o eliminare l'oggetto nell'origine dati connessa o potrebbe aggiornare solo i valori degli attributi dell'oggetto.

Gli oggetti di gestione temporanea con dati aggiornati sono contrassegnati come in sospeso importazione. Sono disponibili diversi tipi di in attesa di importazione. In base al risultato del processo di importazione, un oggetto di gestione temporaneo nell'area connettore è una delle operazioni seguenti in attesa di tipi di importazione:

- **Nessuno**. Nessuna modifica per gli attributi dell'oggetto gestione temporanea è disponibile. Motore di sincronizzazione non indicatore di questo tipo come in sospeso importazione.
- **Aggiungere**. L'oggetto di gestione temporanea è un nuovo oggetto di importazione nello spazio connettore. Motore di sincronizzazione Contrassegna questo tipo come in sospeso Importa per un'ulteriore elaborazione nel metaverse.
- **Aggiornamento**. Motore di sincronizzazione viene rilevato un oggetto di gestione temporaneo corrispondente nell'area connettore e contrassegna questo tipo come in attesa di importazione in modo che gli aggiornamenti per gli attributi possono essere elaborati nel metaverse. Gli aggiornamenti comprendono ridenominazione di oggetto.
- **Eliminare**. Motore di sincronizzazione viene rilevato un oggetto di gestione temporaneo corrispondente nell'area connettore e contrassegna questo tipo come in attesa di importazione, in modo che l'oggetto collegato tramite join può essere eliminato.
- **Aggiungi/Elimina**. Motore di sincronizzazione viene rilevato un oggetto di gestione temporaneo corrispondente nell'area connettore, ma non corrispondono a tipi di oggetto. In questo caso un aggiungere Elimina viene gestita la modifica. A Elimina aggiungere modifica indica al motore di sincronizzazione che una risincronizzazione completa dell'oggetto deve essere compresa poiché set di regole diversi vengono applicate a questo oggetto quando l'oggetto digitare le modifiche.

Se si imposta lo stato di importazione di un oggetto di gestione temporaneo, è possibile ridurre sensibilmente la quantità di dati elaborati durante la sincronizzazione perché consente al sistema di elaborare solo gli oggetti che sono state aggiornate dati.

### <a name="synchronization-process"></a>Processo di sincronizzazione
La sincronizzazione è costituito da due processi correlati:

- Sincronizzazione in ingresso, quando il contenuto del metaverse viene aggiornato con i dati nell'area connettore.
- Sincronizzazione in uscita, quando il contenuto dell'area connettore viene aggiornato utilizzando i dati nel metaverse.

In base alle informazioni inserite nello spazio connettore, il processo di sincronizzazione in ingresso creato nel metaverse visualizzazione integrata dei dati memorizzati nelle origini dati connessi. Tutti gli oggetti di gestione temporaneo o solo quelle ottenute con informazioni un'importazione in sospeso sono aggregati in base alla configurazione delle regole.

Gli aggiornamenti di processo di sincronizzazione in uscita esportare gli oggetti quando gli oggetti metaverse vengono modificati.

Sincronizzazione in ingresso crea la visualizzazione integrata nel metaverse le informazioni di identità ricevuto da origini dati connessi. Motore di sincronizzazione può elaborare le informazioni sull'identità in qualsiasi momento usando le informazioni più recenti di identità che ha dall'origine dati connessa.

**Sincronizzazione in ingresso**

Sincronizzazione in ingresso include i processi seguenti:

- **Provisioning** (denominato anche **proiezione** se è importante distinguere questo processo da il provisioning di sincronizzazione in uscita). Motore di sincronizzazione crea un nuovo oggetto metaverse in base a un oggetto di gestione temporaneo e li collega. Provisioning è un'operazione di livello di oggetti.
- **Partecipare**. Motore di sincronizzazione si collega un oggetto di gestione temporaneo a un oggetto metaverse esistente. Un join è un'operazione di livello di oggetti.
- **Il flusso di attributi di importazione**. Motore di sincronizzazione aggiorna i valori degli attributi, denominati flusso di attributi dell'oggetto nel metaverse. Importa flusso di attributi è un'operazione di livello attributo che richiede un collegamento tra un oggetto di gestione temporaneo e un oggetto metaverse.

Disposizione è il solo processo che crea oggetti nel metaverse. Provisioning interessa solo gli oggetti di importazione che sono oggetti separati. Durante la disposizione, motore di sincronizzazione crea un oggetto metaverse corrispondente al tipo di oggetto dell'oggetto importato e consente di stabilire un collegamento tra gli oggetti, creando così un oggetto collegato tramite join.

Il processo di aggiunta stabilisce anche un collegamento tra Importa oggetti e un oggetto metaverse. La differenza tra partecipare e disposizione è che il processo di aggiunta richiede l'oggetto di importazione sono collegate a un oggetto metaverse esistente, in cui il processo di provisioning crea un nuovo oggetto metaverse.

Motore di sincronizzazione tenta di partecipare a un oggetto di importazione a un oggetto metaverse tramite i criteri specificati nella configurazione di sincronizzazione della regola.

Durante i processi di provisioning e partecipare a un oggetto metaverse rendendole aggiungono motore di sincronizzazione i collegamenti a un oggetto separato. Al termine di queste operazioni a livello di oggetto, motore di sincronizzazione possibile aggiornare i valori degli attributi dell'oggetto metaverse associato. Questo processo viene definito il flusso di attributi di importazione.

Importa flusso di attributi che si verifica su tutti gli oggetti di importazione che contengono dati nuovi e sono collegati a un oggetto metaverse.

**Sincronizzazione in uscita**

Gli aggiornamenti di sincronizzazione in uscita esportare gli oggetti quando un oggetto metaverse modificare ma non viene eliminato. L'obiettivo della sincronizzazione in uscita consiste nel valutare se le modifiche agli oggetti presenti in richiedono gli aggiornamenti a oggetti di gestione temporaneo negli spazi connettore. In alcuni casi, le modifiche possono richiedere che gestione temporanea oggetti in tutti gli spazi connettore aggiornati. Oggetti di gestione temporanea che vengono modificati vengono contrassegnati come in sospeso di esportazione, assumono l'esportazione di oggetti. Questi Esporta gli oggetti vengono inseriti in un secondo momento all'origine dati connessa durante il processo di esportazione.

Sincronizzazione in uscita sono previsti tre processi:

- **Il provisioning**
- **Annullamento dell'implementazione**
- **Esportare il flusso di attributi**

Il provisioning e deprovisioning sono entrambe le operazioni a livello di oggetto. Il deprovisioning dipende dal tipo di provisioning perché solo il provisioning può avviare l'operazione. Annullamento dell'implementazione viene attivata quando il provisioning rimuove il collegamento tra un oggetto metaverse e un oggetto di esportazione.

Provisioning sempre viene attivato quando le modifiche vengono applicate agli oggetti nel metaverse. Quando vengono apportate modifiche agli oggetti presenti in, motore di sincronizzazione possibile eseguire una delle seguenti operazioni come parte del processo di provisioning:

- Creare gli oggetti uniti, in cui un oggetto metaverse è collegato a un oggetto di esportazione appena creato.
- Rinominare un oggetto collegato tramite join.
- Disjoin i collegamenti tra un oggetto metaverse e oggetti, creazione di un oggetto separato di gestione temporanea.

Se il provisioning richiede il motore di sincronizzazione per creare un nuovo oggetto connettore, l'oggetto di gestione temporanea a cui è collegato l'oggetto metaverse è sempre un oggetto di esportazione, in quanto l'oggetto non esiste ancora nell'origine dati connessa.

Se il provisioning richiede motore di sincronizzazione di un oggetto collegato tramite join, la creazione di un oggetto separato, disjoin deprovisioning viene attivato. Il processo di deprovisioning Elimina l'oggetto.

Durante l'annullamento dell'implementazione, eliminazione di un oggetto di esportazione non fisica eliminare l'oggetto. L'oggetto viene contrassegnato come **eliminato**, il che significa che l'operazione di eliminazione viene gestita sull'oggetto.

Esporta il flusso di attributi si verifica anche durante il processo di sincronizzazione in uscita, simile a quello che si verifica il flusso di attributi di importazione durante la sincronizzazione in ingresso. Esportazione attributo flusso si verifica solo tra gli oggetti metaverse ed Esporta che fanno parte.

### <a name="export-process"></a>Processo di esportazione
Durante il processo di esportazione, motore di sincronizzazione esamina tutti gli oggetti di esportazione vengono contrassegnati come in sospeso esportazione nello spazio connettore e quindi invia gli aggiornamenti all'origine dati connessa.

Motore di sincronizzazione possa determinare il successo dell'esportazione, ma non sufficientemente può determinare il completamento del processo di gestione delle identità. Oggetti origine dati connessa sempre possono essere modificati da altri processi. Perché il motore di sincronizzazione non ha una connessione permanente all'origine dati connessa, non è sufficiente per rendere ipotesi con le proprietà di un oggetto nell'origine dati connessi basata solo su una notifica di esportazione corretta.

Ad esempio, un processo nell'origine dati connessa Impossibile modificare gli attributi dell'oggetto tornare ai valori originali (ovvero l'origine dati connessa Impossibile sovrascrivere i valori subito dopo i dati vengono inseriti dal motore di sincronizzazione e con successo nell'origine dati connessa).

Gli archivi motore di sincronizzazione esportare e importano informazioni di stato su ogni oggetto di gestione temporanea. Se i valori degli attributi che siano specificati nell'elenco di inclusione attributo sono stati modificati dopo l'ultima esportazione, l'archiviazione di importare ed esportare motore di sincronizzazione per consente di stato per rispondere in modo appropriato. Motore di sincronizzazione utilizza il processo di importazione per verificare i valori degli attributi esportati in origine dati connessa. Confronto tra le informazioni importate ed esportate, come illustrato nella figura seguente consente motore di sincronizzazione determinare se è stata eseguita correttamente l'esportazione o se dovrà essere ripetute.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Ad esempio, se il motore di sincronizzazione Esporta attributo C, che presenta un valore di 5, a un'origine dati connessa, memorizza C = 5 in memoria stato esportazione. Ogni esportazione aggiuntive sui risultati oggetto durante un tentativo di esportare C = 5 l'origine dati connessa nuovamente perché il motore di sincronizzazione si presuppone che il valore non è stato in modo permanente applicato all'oggetto (ovvero, a meno che un valore diverso importato recente dal sito di origine dati connessa). La memoria esportazione viene svuotata quando C = 5 viene ricevuto durante un'operazione di importazione sull'oggetto.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
