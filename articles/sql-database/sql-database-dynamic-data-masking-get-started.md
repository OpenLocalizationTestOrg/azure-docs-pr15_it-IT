<properties
   pageTitle="Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal)"
   description="Come iniziare a utilizzare SQL Database dinamico dati Masking nel portale di Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal)

> [AZURE.SELECTOR]
- [Dati dinamici Masking - Azure portale classica](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Panoramica

SQL Database dinamico dati Masking limita esposizione di dati riservati masking agli utenti senza privilegi. Maschera dati dinamici è supportata per la versione V12 di Database SQL Azure.

Maschera dati dinamici consente di impedire l'accesso non autorizzato ai dati riservati, consentendo ai clienti di designare la quantità di dati riservati per mostrare con un impatto minimo nel livello dell'applicazione. È una funzionalità di protezione in base a criteri che consente di nascondere i dati sensibili nel set di risultati di una query su campi database designato, mentre i dati del database non vengono modificati.

Ad esempio, un rappresentante del servizio in un call center potrebbe identificare i chiamanti da diverse cifre del loro numero di previdenza sociale o numero di carta di credito, ma gli elementi di dati non devono essere rese completamente addetto al servizio. È possibile definire una regola di maschera impostato tutte le maschere, ma le ultime quattro cifre di qualsiasi numero di previdenza sociale o numero di carta di credito nel risultato di una query. Ad esempio, è possibile definire una maschera di dati appropriato per proteggere i dati personali informazioni personali, in modo che uno sviluppatore può eseguire una query ambienti di produzione per la risoluzione dei problemi senza violare normative di conformità.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database dinamico dati Masking nozioni di base

Impostare dati dinamici masking criteri nel portale di Azure selezionando l'operazione Masking dati dinamici nel proprio blade di configurazione di Database SQL o blade impostazioni.


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
| **Numero casuale** | **Masking metodo che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione della maschera è un numero costante.<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Testo personalizzato** | **Masking metodo che espone i caratteri nome e il cognome** e aggiunge una stringa di riempimento personalizzata al centro. Se la stringa originale è minore di esposto prefisso e un suffisso, verrà utilizzata la stringa di riempimento. <br/>prefisso [spaziatura interna] suffisso<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Campi consigliati per creare una maschera

Motore di consigli DDM contrassegna determinati campi dal database come potenzialmente riservati, i campi sono buoni candidati per maschera. In e il Masking dati dinamici nel portale, verrà visualizzato colonne consigliate per il database. È sufficiente è fare clic su **Aggiungi maschera** per una o più colonne e quindi **Salva** per applicare una maschera per tali campi.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurare la definizione della maschera dati dinamici per il database tramite il portale di Azure

1. Avviare il portale di Azure [all'https://portal.azure.com](https://portal.azure.com).

2. Passare a e l'impostazioni del database contenente i dati sensibili da per creare una maschera.

3. Fare clic sul riquadro **Masking dati dinamici** che avvia e configurazione il **Masking dati dinamici** .

    * In alternativa, è possibile scorrere fino alla sezione **operazioni** e fare clic su **Masking dati dinamici**.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. In e il configurazione **Masking dati dinamici** è possibile visualizzare alcune colonne di database che il motore di consigli è contrassegnato per maschera. Per accettare i suggerimenti, basta fare clic su **Aggiungi maschera** per una o più colonne e verrà creata una maschera in base al tipo predefinito per la colonna. È possibile modificare la funzione della maschera facendo clic sulla regola della maschera e la maschera di modifica formato del campo in un formato diverso di propria scelta. Assicurarsi di fare clic su **Salva** per salvare le impostazioni.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Per aggiungere una maschera per tutte le colonne nel database, fare clic su **Aggiungi maschera** per aprire e il configurazione **Aggiungi regola Masking** nella parte superiore della stessa e di configurazione **Masking dati dinamici**

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Selezionare **Schema**, **tabelle** e **colonne** per definire il campo scelto verrà mascherato.

7. Scegliere un **Formato del campo Masking** dall'elenco di dati riservati masking categorie.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Fare clic su **Salva** i dati masking blade regola per aggiornare il set di regole nei dati dinamici masking criteri masking.

9. Digitare gli utenti SQL o identità AAD che devono essere esclusi dalla maschera e dispone dell'accesso ai dati riservati non mascherati. Deve trattarsi di un elenco di utenti separati da punti e virgola. Si noti che gli utenti che dispongono dei privilegi di amministratore sempre abbiano accesso ai dati non mascherati originali.

    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] È possibile mettere in modo che il livello di applicazione può visualizzare i dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'account di SQL Server o AAD utilizzata dall'applicazione per la query. Si consiglia di questo elenco è presente un numero minimo di privilegi agli utenti di ridurre al minimo esposizione dei dati riservati.

10. Fare clic su **Salva** i dati masking blade di configurazione per salvare il criterio della maschera nuovo o aggiornato.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Impostare i dati dinamici masking per il database in uso dei cmdlet di Powershell

Vedere [i cmdlet di Database SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurare la maschera dati dinamici per database mediante API REST

Vedere [operazioni per i database SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
