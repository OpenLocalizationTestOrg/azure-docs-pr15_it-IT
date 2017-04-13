<properties
   pageTitle="Connettore SQL generico | Microsoft Azure"
   description="In questo articolo viene descritto come configurare il connettore SQL generico di Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>Riferimento tecnico connettore SQL generico

Questo articolo descrive il connettore SQL generico. L'articolo si applica ai seguenti prodotti:

- Gestione identità Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario utilizzare correzione 4.1.3671.0 o versioni successive [KB3092178](https://support.microsoft.com/kb/3092178).

Per MIM2016 e FIM2010R2, il connettore è disponibile per il download dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Per visualizzare il connettore in azione, vedere l'articolo [Connettore SQL generico dettagliate](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Panoramica del connettore SQL generico

Connettore SQL generico consente di integrare il servizio di sincronizzazione con un sistema di database che offre la connettività ODBC.  

Dal punto di vista di alto livello, le caratteristiche seguenti sono supportate per la versione corrente del connettore:

Caratteristica | Supporto tecnico
--- | ---
Origine dati connessa | Il connettore è supportato con tutti i driver ODBC 64 bit. Sono stati testato con le operazioni seguenti: <li>Microsoft SQL Server e SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9</li><li>G Oracle 10 e 11</li><li>MySQL 5. x</li>
Scenari   | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione delle password</li>
Operazioni | <li>Delta importazione, esportazione e importazione completa</li><li>Per esportare: Aggiungere, eliminare, aggiornamento e sostituire</li><li>Impostare la Password, cambiare la Password</li>
Schema | <li>Individuazione automatica di oggetti e attributi</li>

### <a name="prerequisites"></a>Prerequisiti
Prima di utilizzare il connettore, accertarsi di avere le operazioni seguenti per il server di sincronizzazione:

- 4.5.2 Microsoft .NET Framework o versioni successive
- driver client ODBC a 64 bit

### <a name="permissions-in-connected-data-source"></a>Autorizzazioni in origine dati connessa
Per creare o eseguire una delle operazioni supportate in connettore SQL generico, è necessario disporre di:

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Porte e protocolli
Per le porte necessarie per il driver ODBC per l'uso, consultare la documentazione del fornitore del database.

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
Per creare un connettore SQL generico, selezionare **Agente di gestione** e **creare**nel **Servizio di sincronizzazione** . Selezionare il connettore **SQL generico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Connettività
Il connettore viene utilizzato un file DSN ODBC per la connettività. Creare il file DSN utilizzando **Le origini dati ODBC** disponibile nel menu start in **Strumenti di amministrazione**. Nello strumento di amministrazione, creare un **File DSN** in modo che possa essere fornita al connettore.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Schermata di integrazione applicativa è il primo quando si crea un nuovo connettore SQL generico. È necessario fornire le informazioni seguenti:

- Percorso del file DSN
- Autenticazione
    - Nome utente
    - Password

Il database deve supportare uno di questi metodi di autenticazione:

- **L'autenticazione di Windows**: il database di autenticazione utilizza le credenziali di Windows per verificare l'utente. Per eseguire l'autenticazione con il database viene utilizzata il nome utente e password specificata. Autorizzazioni per il database che questo account.
- **Autenticazione di SQL**: autenticazione ottimali di database il nome utente e la password definita una schermata di integrazione applicativa per connettersi al database. Se si memorizza la password o un nome utente nel file DSN, le credenziali fornite nella schermata di integrazione applicativa hanno la precedenza.
- **Autenticazione di Database SQL Azure**: per altre informazioni, vedere [connettersi al Database da con Azure Active Directory autenticazione di SQL](..\sql-database\sql-database-aad-authentication.md).

**Nome distinto è ancoraggio**: se si seleziona questa opzione, il nome distinto viene usato anche come l'attributo di ancoraggio. Può essere usata per una semplice implementazione, ma presenta anche le limitazioni seguenti:

-   Il connettore supporta solo un tipo di oggetto. Pertanto attributi di riferimento possono fare riferimento solo lo stesso tipo di oggetto.

**Tipo Esporta: oggetto Sostituisci**: durante l'esportazione, quando vengono modificati solo alcuni attributi, l'intero oggetto con tutti gli attributi viene esportato e sostituisce l'oggetto esistente.

### <a name="schema-1-detect-object-types"></a>Schema 1 (tipi di oggetto rileva)
In questa pagina, si intende configurare modalità il connettore per trovare i diversi tipi di oggetti nel database.

Ogni tipo di oggetto viene presentato come una partizione e configurato ulteriormente **Configura partizioni**e gerarchie.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Metodo di rilevamento di tipo di oggetto**: il connettore supporta i metodi di rilevamento di tipo di oggetto.

- **Valore fisso**: È fornire l'elenco dei tipi di oggetto con un elenco delimitato da virgole. Ad esempio: `User,Group,Department`.  
![Schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Tabella/visualizzazione/Stored Procedure**: specificare il nome della tabella/visualizzazione/stored procedure e quindi il nome della colonna che fornisce l'elenco dei tipi di oggetto. Se si utilizza una stored procedure, quindi anche specificare parametri per renderla nel formato **[nome]: [direzione]: [valore]**. Fornire ogni parametro in una riga separata (utilizzare Ctrl + Invio per iniziare una nuova riga).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Query SQL**: questa opzione consente di specificare una query SQL che restituisce una singola colonna con tipi di oggetto, ad esempio `SELECT [Column Name] FROM TABLENAME`. La colonna restituita deve essere di tipo stringa (varchar).

### <a name="schema-2-detect-attribute-types"></a>Schema 2 (tipi di attributo rileva)
In questa pagina, si intende configurare come i nomi degli attributi e i tipi sta per essere rilevato. Le opzioni di configurazione sono elencate per ogni tipo di oggetto rilevata nella pagina precedente.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Metodo di rilevamento di tipo attributo**: il connettore supporta i metodi di rilevamento degli attributi tipo a ogni tipo di oggetto rilevato nella schermata di Schema 1.

- **Tabella/visualizzazione/Stored Procedure**: specificare il nome della tabella/visualizzazione/stored procedure che deve essere utilizzato per trovare i nomi degli attributi. Se si utilizza una stored procedure, quindi anche specificare parametri per renderla nel formato **[nome]: [direzione]: [valore]**. Fornire ogni parametro in una riga separata (utilizzare Ctrl + Invio per iniziare una nuova riga). Per rilevare i nomi degli attributi in un attributo multivalore, specificare un elenco delimitato da virgole delle tabelle o viste. Scenari multivalore non sono supportati quando tabella padre e figlio hanno stessi nomi di colonna.
- **Query SQL**: questa opzione consente di specificare una query SQL che restituisce una singola colonna con i nomi degli attributi, ad esempio `SELECT [Column Name] FROM TABLENAME`. La colonna restituita deve essere di tipo stringa (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Schema 3 (Definisci ancoraggio e nome distinto)
Questa pagina consente di configurare ancoraggio e attributo nome distinto per ogni tipo di oggetto rilevato. È possibile selezionare più attributo in modo da ancoraggio univoco.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Non sono elencati gli attributi multivalore e Boolean.
- Attributo stesso non è possibile utilizzare per nome distinto e ancoraggio, a meno che non **nome distinto è ancoraggio** sia selezionata nella pagina connettività.
- Se **nome distinto è ancoraggio** sia selezionata nella pagina connettività, questa pagina richiede solo l'attributo nome distinto. Questo attributo sarebbe anche essere utilizzato come l'attributo di ancoraggio.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Schema 4 (tipo di attributo Definisci, Guida di riferimento e direzione)
Questa pagina consente di configurare il tipo di attributo, ad esempio numero intero, binario, o Boolean e direzione per ogni attributo. Tutti gli attributi dalla pagina **schema 2** sono elencati inclusi gli attributi multivalore.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Tipo di dati**: utilizzati per associare il tipo di attributo a tali tipi noti dal motore di sincronizzazione. Il valore predefinito è anche possibile utilizzare lo stesso tipo come rilevato nello schema di SQL, ma DateTime e materiale di riferimento non vengono rilevati facilmente. Chi, è necessario specificare **DateTime** o un **riferimento**.
- **Direzione**: È possibile impostare la direzione di attributo per importare, esportare o la soluzione del problema. La soluzione del problema è valore predefinito.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Note:

- Se un tipo di attributo non è rilevabile dal connettore, utilizzerà il tipo di dati String.
- **Le tabelle annidate** possono essere considerati tabelle di database di una colonna. Oracle archivia le righe della tabella annidata in alcun ordine specifico. Quando si recupera tabella annidata in una variabile PL/SQL, le righe hanno pedici consecutivi a partire da 1. Che offre accesso di tipo matrice alle singole righe.
- **VARRYS** non sono supportate in connector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Schema 5 (partizione Definisci per gli attributi di riferimento)
In questa pagina, viene configurato per tutti gli attributi di riferimento quale partizione (tipo di oggetto) a cui si riferisce a un attributo.

![Schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se si usa **nome distinto è ancoraggio**, è necessario utilizzare lo stesso tipo di oggetto come quello che da cui si fa riferimento. È possibile fare riferimento a un altro tipo di oggetto.

### <a name="global-parameters"></a>Parametri globali
La pagina parametri globale viene utilizzata per configurare importazione Delta, il formato di data/ora e metodo Password.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Connettore SQL generico supporta i seguenti metodi per l'importazione Delta:

- **Trigger**: vedere [generare visualizzazioni Delta utilizzando i trigger](https://technet.microsoft.com/library/cc708665.aspx).
- **Filigrana**: un approccio generico che può essere utilizzato con qualsiasi database. La query filigrana è già presenti in base al fornitore del database. Una colonna di filigrana deve essere presenta in ogni tabella/visualizzazione utilizzato. Questa colonna deve traccia gli inserimenti e aggiornamenti per i dipendenti e le tabelle come (multivalore o figlio) tabelle. Risolvere i servizi di sincronizzazione e il server di database devono essere sincronizzati. In caso contrario, alcune voci nell'importazione delta potrebbero essere stati omessi.  
Limitazione:
    - Strategia di filigrana non supporto eliminato gli oggetti.
- **Snapshot**: (funziona solo con Microsoft SQL Server), [la generazione di viste Delta tramite snapshot](https://technet.microsoft.com/library/cc720640.aspx)
- **Il rilevamento delle modifiche**: (funziona solo con Microsoft SQL Server) [sul rilevamento delle modifiche](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitazioni:
    - Ancora & attributo nome distinto deve fare parte della chiave primaria per l'oggetto selezionato nella tabella.
    - Query SQL non è supportata durante l'importazione / esportazione con il rilevamento delle modifiche.

**Altri parametri**: specificare il fuso orario del Server Database che indica che il server di Database in cui si trova. Questo valore viene utilizzato per supportare i diversi formati di data e ora attributi.

Il connettore archivia sempre data e l'ora di data in formato UTC. Per essere in grado di convertire correttamente la data e ora, il fuso orario del server di database e il formato utilizzato deve essere specificato. Il formato dovrebbe essere espressa nel formato di .net.

Durante l'esportazione è necessario specificare tutti gli attributi di ora data per il connettore nel formato di ora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configurazione delle password**: il connettore offre funzionalità di sincronizzazione delle password e supporta imposta e modifica la password.

Il connettore fornisce due metodi per supportare la sincronizzazione delle password:

- **Stored Procedure**: questo metodo richiede due stored procedure per supportare Set e Cambia password. Digitare tutti i parametri per aggiungere e modificare l'operazione di password in **Impostare Password SP** e **Modificare la Password SP** parametri rispettivamente come per esempio seguente.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Estensione password**: questo metodo richiede la Password estensione DLL (è necessario fornire il nome della DLL estensione che è implementazione dell'interfaccia [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ). Deve trovarsi nella cartella estensione assembly dell'estensione di password in modo che il connettore può caricare la DLL in fase di esecuzione.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Inoltre, è necessario attivare la gestione delle Password nella pagina **Configura estensione** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurare le partizioni e gerarchie
Nella pagina partizioni e gerarchie, selezionare tutti i tipi di oggetto. Ogni tipo di oggetto è una partizione dedicata.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

È anche possibile ignorare i valori definiti nella pagina **connettività** o **Parametri globali** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurare gli ancoraggi
Questa pagina è di sola lettura dopo il punto di ancoraggio è già stato definito. L'attributo di ancoraggio selezionati verrà sempre aggiunti con il tipo di oggetto in modo che rimangano univoca per i tipi di oggetto.

![ancoraggi](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurare il parametro passaggio Esegui
La procedura seguente vengono configurata nei profili di esecuzione del connettore. Queste configurazioni svolgono il lavoro effettivo di importazione ed esportazione di dati.

### <a name="full-and-delta-import"></a>Completo e di importazione Delta
Generico connettore SQL supporto completo e importazione Delta utilizzo di questi metodi:

- tavolo
- Visualizzazione
- Stored Procedure
- Query SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabella/visualizzazione**  
Per importare gli attributi multivalore per un oggetto, è necessario fornire il nome della tabella/visualizzazione separati da virgola nelle **visualizzazioni per nome di multivalore** tabella/e condizioni di join rispettivi in **condizione di Join** con la tabella padre.

Esempio: Si desidera importare l'oggetto dipendente e tutti i relativi attributi multivalore. Sono disponibili due tabelle, denominate dipendente (tabella principale) e reparto (multivalore).
Eseguire le operazioni seguenti:

- Tipo **dipendente** **Tabella/visualizzazione/SP**.
- Digitare reparto nome di multivalore/nelle **visualizzazioni**per tabella.
- Digitare la condizione di join tra dipendenti e reparto **Condizione di Join**, ad esempio `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Stored procedure**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Se si dispone di quantità di dati, è consigliabile per implementare l'impaginazione con le Stored procedure.
- Per le Stored Procedure supportare l'impaginazione, è necessario specificare avviare indice e l'indice di fine. Vedere: [in modo efficiente lo scorrimento grandi quantità di dati](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexe @EndIndex vengono sostituiti in fase di esecuzione con il valore di dimensioni pagina corrispondente configurato nella pagina **Configura passaggio** . Ad esempio, quando il connettore recupera prima pagina e le dimensioni della pagina è impostato 500, in tale situazione @StartIndex 1 e @EndIndex 500. Questi valori aumentano quando connettore recupera pagine successive e modificare il @StartIndex & @EndIndex valore.
- Per eseguire parametri Stored Procedure, specificare i parametri in `[Name]:[Direction]:[Value]` formato. Immettere ogni parametro in una riga separata (Usa Ctrl + Invio per iniziare una nuova riga).
- Connettore SQL generico supporta anche l'operazione di importazione dei server collegati in Microsoft SQL Server. Se devono essere recuperati da una tabella in server collegato, tabella deve essere fornita nel formato:`[ServerName].[Database].[Schema].[TableName]`
- Connettore SQL generico supporta solo gli oggetti con struttura analoga (sia alias nome e tipo di dati) tra eseguire i passaggi il rilevamento di informazioni e lo schema. Se l'oggetto selezionato da schema e informazioni fornite in fase di esecuzione è diverso, connettore di SQL in grado di supportare questo tipo di scenari.

**Query SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Set di risultati di più query non è supportata.
- Query SQL supporta l'impaginazione e fornire start indice e l'indice di fine come una variabile per supportare l'impaginazione.

### <a name="delta-import"></a>Importazione delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configurazione dell'importazione delta richiede alcune configurazione più complessa rispetto all'importazione completo.

- Se si sceglie l'approccio Trigger o uno Snapshot per tenere traccia delle modifiche delta, quindi fornire tabella Cronologia o uno Snapshot database nella casella **tabella Cronologia o Snapshot del database** .
- È inoltre necessario fornire condizione di join tra le tabelle di cronologia e padre, ad esempio`Employee.ID=History.EmployeeID`
- Per tenere traccia della transazione nella tabella padre dalla tabella della cronologia, è necessario specificare il nome della colonna che contiene le informazioni di operazione (Aggiungi/aggiornamento/eliminazione).
- Se si sceglie filigrana per tenere traccia delle modifiche delta, quindi specificare il nome della colonna che contiene le informazioni di operazione nel **Nome di colonna Segna acqua**.
- La colonna **Modifica degli attributi di tipo** è necessaria per il tipo di modifica. Questa colonna è associata una modifica che si verifica la tabella primaria o della tabella multivalore a un tipo di modifica nella visualizzazione delta. Questa colonna può contenere il tipo di modifica Modify_Attribute per le modifiche a livello di attributo o un'aggiunta, modifica o Elimina modificare tipo per un tipo di modifiche a livello di oggetto. Se si tratta di un numero diverso da quello predefinito Aggiungi, modifica o Elimina, quindi è possibile definire tali valori usando questa opzione.

### <a name="export"></a>Esportazione
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Connettore SQL generico supporta l'esportazione usando quattro metodi supportati come:

- tavolo
- Visualizzazione
- Stored Procedure
- Query SQL

**Tabella/visualizzazione**  
Se si sceglie l'opzione di visualizzazione per tabella /, il connettore genera le rispettive query per eseguire l'esportazione.

**Stored procedure**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se si sceglie l'opzione Stored Procedure, esportazione richiede le diverse tre per Stored procedure per eseguire le operazioni di inserimento/aggiornamento/eliminazione.

- **Aggiungere il nome del provider**: SP questo viene eseguito se qualsiasi oggetto proviene al connettore per l'inserimento nella tabella corrispondente.
- **Aggiornare il nome del provider**: SP questo viene eseguito se qualsiasi oggetto proviene al connettore per l'aggiornamento nella tabella corrispondente.
- **Eliminare il nome del provider**: SP questo viene eseguito se qualsiasi oggetto proviene al connettore per l'eliminazione nella tabella corrispondente.
- Attributo selezionato dallo schema utilizzato come valore di parametro per stored procedure. Ad esempio `EmployeeName: INPUT: @EmployeeName` (EmployeeName sia selezionata nello schema di connettore e il connettore sostituisce il rispettivo valore mentre si esegue l'esportazione)
- Per eseguire stored procedure con parametri, fornire i parametri in `[Name]:[Direction]:[Value]` formato. Immettere ogni parametro in una riga separata (Usa Ctrl + Invio per iniziare una nuova riga).

**Query SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se si sceglie l'opzione della query SQL, esportazione richiede tre query per eseguire le operazioni di inserimento/aggiornamento/eliminazione.

- **Inserimento di Query**: questa query viene eseguita se qualsiasi oggetto proviene al connettore per l'inserimento nella tabella corrispondente.
- **Query di aggiornamento**: questa query viene eseguita se qualsiasi oggetto proviene al connettore per l'aggiornamento nella tabella corrispondente.
- **Query di eliminazione**: questa query viene eseguita se qualsiasi oggetto proviene al connettore per l'eliminazione nella tabella corrispondente.
- Attributo selezionato dallo schema utilizzato come valore di parametro alla query, ad esempio`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per informazioni su come attivare la registrazione risolvere i problemi di connessione, informazioni su [come abilitare ETW Tracing per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).
