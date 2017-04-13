<properties
   pageTitle="Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal classica)"
   description="Come iniziare a utilizzare SQL Database dinamico dati Masking nel portale classica di Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal classica)

> [AZURE.SELECTOR]
- [Dati dinamici Masking - portale Azure](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>Panoramica

SQL Database dinamico dati Masking limita esposizione di dati riservati masking agli utenti senza privilegi. Maschera dati dinamici è supportata per la versione V12 di Database SQL Azure.

Maschera dati dinamici consente di impedire l'accesso non autorizzato ai dati riservati, consentendo ai clienti di designare la quantità di dati riservati per mostrare con un impatto minimo nel livello dell'applicazione. È una funzionalità di protezione in base a criteri che consente di nascondere i dati sensibili nel set di risultati di una query su campi database designato, mentre i dati del database non vengono modificati.

Ad esempio, un rappresentante del servizio in un call center potrebbe identificare i chiamanti da diverse cifre del loro numero di previdenza sociale o numero di carta di credito, ma gli elementi di dati non devono essere rese completamente addetto al servizio. È possibile definire una regola di maschera impostato tutte le maschere, ma le ultime quattro cifre di qualsiasi numero di previdenza sociale o numero di carta di credito nel risultato di una query. Ad esempio, è possibile definire una maschera di dati appropriato per proteggere i dati personali informazioni personali, in modo che uno sviluppatore può eseguire una query ambienti di produzione per la risoluzione dei problemi senza violare normative di conformità.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database dinamico dati Masking nozioni di base

Impostare backup dati dinamici masking criteri nel portale classica di Azure nella scheda verifica e protezione per il database.


> [AZURE.NOTE] Per impostare i dati dinamici masking nel portale di Azure, vedere [Introduzione a SQL Database dinamico dati Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md).


### <a name="dynamic-data-masking-permissions"></a>Autorizzazioni della maschera dati dinamici

Maschera dati dinamici può essere configurata per l'amministratore di Database Azure, amministrazione server o ruoli di responsabile della sicurezza.

### <a name="dynamic-data-masking-policy"></a>Criteri della maschera dati dinamici

* **Gli utenti di SQL esclusi dalla maschera** - un insieme di utenti SQL o identità AAD che riceverà i dati non mascherati nei risultati della query SQL. Si noti che gli utenti che dispongono dei privilegi di amministratore sempre essere esclusi dalla maschera e leggerà i dati originali senza qualsiasi maschera.

* **Masking regole** - un set di regole che definiscono i campi designati per essere mascherati e la funzione della maschera che verrà utilizzata. I campi designati possono essere definiti usando un nome dello schema di database, nome tabella e nome di colonna.

* **Masking funzioni** - un insieme di metodi che controllano l'esposizione dei dati per diversi scenari.

| Funzione di mascheramento | Masking logica |
|----------|---------------|
| **Impostazione predefinita**  |**Maschera completa secondo i tipi di dati dei campi designati**<br/><br/>• Utilizzare XXXX o meno x se le dimensioni del campo sono minore di 4 caratteri per i tipi di dati string (nchar, ntext, nvarchar).<br/>• Usare il valore zero per tipi di dati numerici (bigint, bit, decimali, int, denaro, valore numerico, smallint, smallmoney, tinyint, margine di flessibilità, reale).<br/>• Utilizzare 01-01-1900 per i tipi di dati Data/ora (data, datetime2, datetime, datetimeoffset, smalldatetime, ora).<br/>• Per variante SQL, il valore predefinito del tipo corrente viene utilizzato.<br/>• Per il documento XML <masked/> viene utilizzato.<br/>• Utilizzare un valore vuoto per i tipi di dati speciali (timestamp della tabella, hierarchyid, GUID, binario, immagini e i tipi di spaziale varbinary).
| **Carta di credito** |**Masking metodo che espone le ultime quattro cifre dei campi designati** e aggiunge una costante stringa come un prefisso sotto forma di una carta di credito.<br/><br/>XXXX-XXXX XXXX 1234|
| **Numero di previdenza sociale** |**Masking metodo che espone le ultime quattro cifre dei campi designati** e aggiunge una costante stringa come un prefisso sotto forma di un numero di previdenza sociale American.<br/><br/>XXX-XX-1234 |
| **Posta elettronica** | **Masking metodo espone la prima lettera che sostituisce il dominio con XXX.com** mediante un prefisso costante stringa sotto forma di un indirizzo di posta elettronica.<br/><br/>aXX@XXXX.com |
| **Numero casuale** | **Masking metodo che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione della maschera è un numero costante.<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Testo personalizzato** | **Masking metodo che espone i caratteri nome e il cognome** e aggiunge una stringa di riempimento personalizzata al centro. Se la stringa originale è minore di esposto prefisso e un suffisso, verrà utilizzata la stringa di riempimento.<br/>prefisso [spaziatura interna] suffisso<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>Configurare la maschera dati dinamici per il database tramite il portale classica di Azure

1. Avviare il portale classico Azure in [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Fare clic sul database che si desidera nascondere e quindi fare clic sulla scheda **controllo e sicurezza** .

3. In **dati dinamici masking**, fare clic su **attivato** per attivare i dati dinamici masking caratteristica.  

4. Digitare gli utenti SQL o identità AAD che devono essere esclusi dalla maschera e dispone dell'accesso ai dati riservati non mascherati. Deve trattarsi di un elenco di utenti separati da punti e virgola. Si noti che gli utenti che dispongono dei privilegi di amministratore sempre abbiano accesso ai dati non mascherati originali.

    >[AZURE.TIP] È possibile mettere in modo che il livello di applicazione può visualizzare i dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'account di SQL Server o AAD utilizzata dall'applicazione per la query. Si consiglia di questo elenco è presente un numero minimo di privilegi agli utenti di ridurre al minimo esposizione dei dati riservati.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Nella parte inferiore della pagina nella barra dei menu fare clic su **Aggiungi maschera** per aprire la maschera finestra Configurazione regola.

6. Selezionare lo **Schema**, **tabella** e **colonna** negli elenchi a discesa per definire i campi designati verranno mascherati.

7. Scegliere una **Funzione MASKING** dall'elenco di dati riservati masking categorie.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Fare clic su **OK** nella finestra dati masking finestra regola per aggiornare il set di regole nei dati dinamici masking criteri masking.

9. Fare clic su **Salva** per salvare il criterio della maschera nuovo o aggiornato.


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>Impostare i dati dinamici masking per il database tramite istruzioni Transact-SQL

Visualizzare [dati dinamici Masking](https://msdn.microsoft.com/library/mt130841.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Impostare i dati dinamici masking per il database in uso dei cmdlet di Powershell

Vedere [i cmdlet di Database SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurare la maschera dati dinamici per database mediante API REST

Vedere [operazioni per i database SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
