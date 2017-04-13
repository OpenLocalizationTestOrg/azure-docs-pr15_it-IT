## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Se si specifica una definizione di struttura per rettangolare set di dati
La sezione struttura nel set di dati JSON sezione **facoltativa** per le tabelle rettangolare (con righe e colonne) e contiene un insieme di colonne della tabella. Utilizzare la sezione struttura per uno dei due che fornisce informazioni sul tipo per le conversioni o eseguire il mapping di colonna. Le sezioni seguenti descrivono in dettaglio queste funzionalità. 

Ogni colonna che contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| nome | Nome della colonna. | Sì |
| tipo | Tipo di dati della colonna. Vedere sezione le conversioni di tipo sotto per ulteriori informazioni relative alle quando devono specificare informazioni sul tipo di | No |
| impostazioni locali | .NET in base alle impostazioni cultura da utilizzare quando tipo specificato e .NET Datetime o Datetimeoffset. Il valore predefinito è "en-us".  | No |
| formato | Stringa di formato da utilizzare quando tipo specificata e .NET digitare Datetime o Datetimeoffset. | No |

Nell'esempio seguente viene illustrata la sezione struttura JSON per una tabella con tre colonne ID utente, nome e lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilizzare le seguenti linee guida relative ai tempi includere le informazioni sulla "struttura" e gli elementi da includere nella sezione **struttura** .

- **Per le origini dati strutturati** che memorizzare le informazioni dello schema e tipo di dati, insieme ai dati stesso (origini come tabelle Azure SQL Server, Oracle, e così via), è necessario specificare la sezione "struttura" solo se si desidera eseguire il mapping di colonne specifiche delle colonne di origine per colonne specifiche di sink e i nomi non sono uguali (vedere Dettagli nella sezione mapping colonna riportata di seguito). 

    Come detto in precedenza, le informazioni sul tipo è facoltativi nella sezione "struttura". Per le origini strutturate, informazioni sul tipo è già disponibile come parte della definizione di set di dati nell'archivio dati, pertanto non includere informazioni sul tipo di quando si include la sezione "struttura".
- **Per schema sulle origini dati letti (specificamente blob Azure)** è possibile scegliere di archiviare dati senza archiviare informazioni tipo o schema con i dati. Per questi tipi di origini dati è necessario includere "struttura" nei seguenti 2 casi:
    - Si desidera eseguire il mapping di colonna.
    - Quando il set di dati di origine in un'attività di copia, è possibile fornire informazioni sul tipo di "struttura" e factory dati utilizzerà questo tipo di informazioni per la conversione di tipi nativi per il sink. Vedere l'articolo [spostare i dati da e verso Blob Azure](../articles/data-factory/data-factory-azure-blob-connector.md) per altre informazioni.

### <a name="supported-net-based-types"></a>È supportata. Tipi di rete 
Dati factory supporta CLS conforme .NET in base a digitare i valori seguenti per fornire informazioni sul tipo di "struttura" per schema sulle origini dati lettura come blob Azure.

- Int16
- Int32 
- Int64
- Singolo
- Effettuare un doppio
- Decimale
- Byte
- Bool
- Stringa 
- GUID
- DateTime
- DateTimeOffset
- TimeSpan 

Per Datetime e Datetimeoffset è anche possibile specificare stringa "lingua" e "formato" per semplificare l'analisi di stringa Datetime personalizzata. Per vedere l'esempio seguente la conversione di tipi.

