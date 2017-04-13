<properties
   pageTitle="Il backup del Database di SQL - automatica, ridondanti geografico | Microsoft Azure" 
   description="Database SQL automaticamente crea una copia di backup del database locale ogni cinque minuti e viene utilizzata di accesso in lettura geografico ridondanti di archiviazione Azure (RA GRS) per garantire la ridondanza geografico. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>Informazioni sui backup di Database SQL

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Database SQL automaticamente creata una copia di backup del database locale alcuni minuti e utilizza Azure accesso in lettura geografico ridondanti dello spazio di archiviazione per la ridondanza geografico. Il backup del database sono un elemento essenziale di qualsiasi strategia di ripristino emergenza e continuità aziendale perché sono proteggere i dati da danni accidentali o l'eliminazione. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>Che cos'è un backup del Database di SQL?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

Una copia di backup del Database SQL include il backup del database locale e l'esecuzione di backup ridondanti geografico. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario eseguire alcuna a scopo.

<!----------------- 
    Explains first component of the backup feature
------------------>

Per i backup locali, Database SQL utilizza la tecnologia SQL Server per creare backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziale](https://msdn.microsoft.com/library/ms175526.aspx )e [registro delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx) . Il backup del registro delle transazioni verificarsi ogni cinque minuti, che consente di eseguire un ripristino in un momento nello stesso server che ospita i database. Quando si ripristina un database, il servizio le cifre fuori registro completa, differenza e transazione backup dovranno essere ripristinati.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Usare una copia di backup del database locale per:

- Ripristinare un database in un punto nel tempo entro il periodo di conservazione. Con una copia di backup del database può ripristinare un database in un punto nel tempo, ripristinare un database eliminato al momento che è stato eliminato o ripristinare un database in un'altra area geografica di appartenenza. Per eseguire un ripristino, vedere [ripristinare un database da un backup del database](sql-database-recovery-using-backups.md).

- Copiare un database SQL server nell'area stessa o di un'altra. La copia è coerente a con il Database di SQL corrente. Per eseguire una copia, vedere [copia del database](sql-database-copy.md).

- Archiviare una copia di backup del database oltre il periodo di conservazione backup. Per eseguire un archivio, [esportare un database SQL di un BACPAC](sql-database-export.md) file. È quindi possibile archiviare BACPAC allo spazio di archiviazione a lungo termine e archiviarlo oltre il periodo di conservazione. In alternativa, utilizzare la BACPAC per trasferire una copia del database di SQL Server in locale o in una Azure macchine ().

<!----------------- 
    Explains first component of the backup feature
------------------>

Per i backup geografico ridondanti, Database SQL utilizza [la replica di archiviazione Azure](../storage/storage-redundancy.md). Database SQL archivia i file di backup del database locale in un account di [Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure replica della copia di backup in un [centro dati accoppiate](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Usare una copia di backup ridondanti geografico per:

- Ripristinare un database in un'area geografica diversa nel caso in cui il backup del database non è possibile accedere dalla propria area geografica database primario. 

>[AZURE.NOTE] Nel sistema di archiviazione Azure, il termine *replica* fa riferimento alla copia di file da una posizione a un'altra. SQL *replica di database* fa riferimento a tenere a più database secondari sincronizzati con un database principale. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto spazio di archiviazione backup è incluso senza costi?

Database SQL di fornisce un massimo di 200% dello spazio di archiviazione massime database provisioning come backup archiviazione senza costi aggiuntivi. Ad esempio, se si dispone di un'istanza di DB Standard con dimensioni massime DB provisioning di 250 GB, è necessario 500 GB di spazio di archiviazione backup senza costi aggiuntivi. Se il database supera fornito backup dello spazio di archiviazione, è possibile scegliere per ridurre il periodo di conservazione contattando il supporto di Azure. In alternativa è possibile acquistare spazio di archiviazione aggiuntivo backup che fatturato in base alla tariffa standard di accesso in lettura limitazioni geografiche ridondanti dello spazio di archiviazione (RA GRS). 

## <a name="how-often-do-backups-happen"></a>Con quale frequenza capitare backup?

Per i backup del database locale, il backup del database completo verificarsi settimanale differenziali si verificano ogni ora e registro delle transazioni backup verificano ogni cinque minuti. Il primo backup completo è programmato immediatamente dopo la creazione di un database. In genere viene completata entro 30 minuti, ma può impiegare più tempo quando il database è di dimensioni significative. Ad esempio, il backup iniziale può richiedere più tempo in un database ripristinato o una copia del database. Dopo il primo backup completo, tutti i backup ulteriormente vengono programmati automaticamente e gestiti automaticamente in background. L'intervallo esatto di backup del database completo e [differenziale](https://msdn.microsoft.com/library/ms175526.aspx) viene determinato come distribuisce il carico di lavoro di sistema globali. 

Per i backup geografico ridondanti, completi e differenziali vengono copiati in base alla pianificazione di replica archiviazione Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Per quanto tempo è mantenere i backup?

Ogni backup del Database di SQL dispone di un periodo di conservazione basata sul [livello di servizio](sql-database-service-tiers.md) del database. Il periodo di conservazione per un database di:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Livello di base del servizio è sette giorni.
- Livello di servizio standard è 35 giorni.
- Livello di servizio Premium è 35 giorni.


Se si eseguire il downgrade del database da livelli di servizio Standard o Premium di base, i backup vengono salvati per sette giorni. Tutti i backup esistenti più di sette giorni prima non sono più disponibili. 

Se si aggiorna il database dal livello di base del servizio come Standard o Premium, Database SQL di mantiene backup esistenti finché non assumono 35 giorni. Mantiene nuovi backup quando si verificano per 35 giorni.
 
Se si elimina un database, Database SQL di mantiene i backup nello stesso modo in che cui per un database online. Si supponga ad esempio, che si elimina un database di base contenente un periodo di conservazione di sette giorni. Una copia di backup creato da quattro giorni viene salvato per tre giorni.

>[AZURE.IMPORTANT]
    Se si elimina il server di SQL Azure che ospita i database di SQL, tutti i database che appartengono al server vengono eliminati anche e non possono essere recuperati. Non è possibile ripristinare un server eliminato.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Passaggi successivi

Il backup del database sono un elemento essenziale di qualsiasi strategia di ripristino emergenza e continuità aziendale perché sono proteggere i dati da danni accidentali o l'eliminazione. Per vedere come backup del database in una strategia più ampia, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md).


