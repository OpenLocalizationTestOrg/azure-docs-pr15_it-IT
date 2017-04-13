<properties
   pageTitle="Guida introduttiva a SQL dati Warehouse individuazione"
   description="Come iniziare con individuazione"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Guida introduttiva a individuazione

> [AZURE.SELECTOR]
- [Il controllo](sql-data-warehouse-auditing-overview.md)
- [Individuazione](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Panoramica

Individuazione rileva le attività di database anomala che indica rischi di sicurezza per il database. Individuazione in anteprima e supportati per SQL Data Warehouse.

Individuazione fornisce un nuovo livello di protezione, che consente agli utenti di rilevare e rispondere ai rischi quando si verificano fornendo gli avvisi di sicurezza sulle attività anomala. Gli utenti possono esplorare gli eventi sospetti utilizzando [Il controllo di Azure SQL dati Warehouse](sql-data-warehouse-auditing-overview.md) per determinare se sono il risultato di un tentativo di accesso, violare o sfruttare dati data warehouse.
Individuazione semplifica il rischi indirizzo nell'archivio di dati senza che sia necessario essere un esperto di sicurezza o gestisce la sicurezza avanzata di sistemi di controllo.

Individuazione rileva, ad esempio, alcune attività di database anomala che indica potenziali tentativi di inserimento SQL. Inserimento SQL corrisponde a uno dei problemi di sicurezza comuni dell'applicazione Web su Internet, di attacco applicazioni basate sui dati. Gli utenti malintenzionati trarre vantaggio da vulnerabilità delle applicazioni di inserire le istruzioni SQL dannose nei campi di immissione di applicazione, per violazioni o la modifica dei dati nel database.


## <a name="set-up-threat-detection-for-your-database"></a>Configurare Individuazione del database

1. Avviare il portale di Azure [all'https://portal.azure.com](https://portal.azure.com).

2. Passare a e il configurazione del Data Warehouse SQL che si desidera eseguire il monitoraggio. In e l'impostazioni selezionare **verifica e individuazione**.

    ![Riquadro di spostamento][1]

3. In e configurazione il **controllo e individuazione** trasformare **via** il controllo, che visualizza le impostazioni di rilevamento di rischio.

    ![Riquadro di spostamento][2]

4. Attivare il rilevamento di rischio **via** .

5. Configurare l'elenco dei messaggi di posta elettronica che riceverà gli avvisi di sicurezza al rilevamento di attività di warehouse dati anomala.

6. Fare clic su **Salva** e configurazione il **rilevamento di controllo e minacce** per salvare il nuovo o aggiornato il controllo e criteri di rilevamento delle minacce.

    ![Riquadro di spostamento][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Esplorare le attività di warehouse dati anomala se viene rilevato un evento sospetto

1. Si riceverà una notifica tramite posta elettronica dopo il rilevamento delle attività di database anomala. <br/>
Messaggio di posta elettronica verrà fornite informazioni sull'evento sospetti inclusi natura attività anomala, il nome del database, nome del server e l'ora dell'evento. Inoltre, verranno fornite informazioni sulle possibili cause e azioni per analizzare e mitigare il rischio potenziale al database consigliate.<br/>

    ![Riquadro di spostamento][4]

2. Nel messaggio di posta elettronica, fare clic sul collegamento **Del Registro di controllo di SQL Azure** , in modo da avviare portale classica di Azure e visualizzare i record di verifica pertinenti al momento dell'evento sospetto.

    ![Riquadro di spostamento][5]

3. Fare clic sui record di verifica per visualizzare ulteriori informazioni sulle attività database sospetti, ad esempio istruzione SQL, IP motivo e client di errore.

    ![Riquadro di spostamento][6]

4. In e il record di verifica, fare clic su **Apri in Excel** per aprire un preconfigurato modello per importare ed eseguire un'analisi più approfondita del Registro di controllo al momento dell'evento sospetto di excel.<br/>
**Nota:** In Excel 2010 o versione successiva, è necessario Power Query e l'impostazione **Combinazione rapida**

    ![Riquadro di spostamento][7]

5. Per configurare l'impostazione **Combinazione rapida** - nella scheda della barra multifunzione **POWER QUERY** , selezionare le **Opzioni** per visualizzare la finestra di dialogo Opzioni. Selezionare la sezione relativa alla Privacy e la seconda opzione - 'Ignorare i livelli di Privacy e potenzialmente migliorare le prestazioni':

    ![Riquadro di spostamento][8]

6. Per caricare i registri di controllo SQL, assicurarsi che i parametri in impostazioni scheda siano impostati correttamente e quindi selezionare la barra multifunzione 'Dati' e fare clic sul pulsante 'Aggiorna tutto.

    ![Riquadro di spostamento][9]

7. I risultati vengono visualizzati nel foglio di **Log di controllo SQL** che consente di eseguire un'analisi più approfondita delle attività anomala che sono state rilevate e ridurre l'impatto dell'evento di protezione dell'applicazione.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
