<properties 
    pageTitle="Flusso Analitica: Ruotare le credenziali di log di input e output | Microsoft Azure" 
    description="Informazioni su come aggiornare le credenziali per Analitica flusso ingressi e uscite."
    keywords="credenziali di accesso"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Ruotare le credenziali di accesso per input e di output di flusso Analitica processi

##<a name="abstract"></a>Sunto
Azure flusso Analitica oggi non consente di sostituire le credenziali su un interdipendenze mentre è in esecuzione il processo.

Azure flusso Analitica supportano ripresa di un'operazione dall'ultimo output, si desidera condividere l'intero processo per ridurre al minimo il ritardo tra l'arresto e avvio del processo e rotazione le credenziali di accesso.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1: preparare il nuovo set di credenziali:
Questa parte è applicabile al input/output seguenti:

* Archiviazione BLOB
* Hub di evento
* Database SQL
* Archiviazione tabella

Per altri ingressi/uscite, procedere con parte 2.

###<a name="blob-storagetable-storage"></a>Archiviazione BLOB lo spazio di archiviazione/tabella
1.  Nel portale di gestione di Azure, passare a estensione dello spazio di archiviazione:  
![1][graphic1]
2.  Individuare la memoria utilizzata per il lavoro e passare al suo interno:  
![immagine 2][graphic2]
3.  Fare clic sul comando di gestire i tasti di scelta:  
![graphic3][graphic3]
4.  Tra la chiave primaria di accesso e il tasto di scelta secondario, **Selezionare l'effetto non utilizzata il processo**.
5.  Rigenera passaggi:  
![graphic4][graphic4]
6.  Copiare la chiave generata:  
![graphic5][graphic5]
7.  Passare alla parte 2.

###<a name="event-hubs"></a>Hub di evento
1.  Passare all'articolo estensione Bus di servizio nel portale di gestione di Azure:  
![graphic6][graphic6]
2.  Individuare il Namespace Bus servizio utilizzato per il lavoro e passare al suo interno:  
![graphic7][graphic7]
3.  Se il processo utilizza un criterio di accesso condiviso su Namespace di Bus del servizio, andare al passaggio 6  
4.  Passare alla scheda hub evento:  
![graphic8][graphic8]
5.  Individuare l'Hub di evento utilizzato per il lavoro e passare al suo interno:  
![graphic9][graphic9]
6.  Passare alla scheda Configurazione:  
![graphic10][graphic10]
7.  Nel menu a discesa Nome del criterio individuare il criterio di accesso condiviso utilizzato il processo:  
![graphic11][graphic11]
8.  Tra la chiave primaria e chiave secondaria, **Selezionare l'effetto non utilizzata il processo**.  
9.  Rigenera passaggi:  
![graphic12][graphic12]
10. Copiare la chiave generata:  
![graphic13][graphic13]
11. Passare alla parte 2.  

###<a name="sql-database"></a>Database SQL

>[AZURE.NOTE] Nota: è necessario connettersi al servizio di Database SQL. Verranno illustrano come eseguire questa operazione utilizzando l'esperienza di gestione nel portale di gestione di Azure, ma è possibile scegliere di utilizzare anche uno strumento lato client, ad esempio SQL Server Management Studio.

1.  Passare all'articolo estensione database SQL nel portale di gestione di Azure:  
![graphic14][graphic14]
2.  Individuare il Database di SQL utilizzata per il collegamento processo e **fare clic sul server** nella stessa riga:  
![graphic15][graphic15]
3.  Fare clic sul comando Gestisci:  
![graphic16][graphic16]
4.  Schema di Database di tipo:  
![graphic17][graphic17]
5.  Digitare il proprio nome utente, Password e fare clic su Log in:  
![graphic18][graphic18]
6.  Fare clic su Nuova Query:  
![graphic19][graphic19]
7.  Digitare la query seguente sostituendo < nome_accesso > con il proprio nome utente e la sostituzione <enterStrongPasswordHere> con la nuova password:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Fare clic su Esegui:  
![graphic20][graphic20]
9.  Tornare al passaggio 2 e questa volta fare clic il database:  
![graphic21][graphic21]
10. Fare clic sul comando Gestisci:  
![graphic22][graphic22]
11. digitare il proprio nome utente, Password e fare clic su accesso:  
![graphic23][graphic23]
12. Fare clic su Nuova Query:  
![graphic24][graphic24]
13. Digitare la query seguente sostituendo < nome_utente > con un nome per il quale si desidera individuare l'account di accesso nel contesto di questo database (è possibile fornire lo stesso valore assegnato per < nome_accesso >) e la sostituzione < nome_accesso > con il nuovo nome utente:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Fare clic su Esegui:  
![graphic25][graphic25]
15. È ora necessario fornire il nuovo utente con lo stesso ruoli e privilegi assegnati all'utente originale.
16. Passare alla parte 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: Arrestare il processo di Analitica flusso
1.  Passare all'articolo estensione Analitica flusso nel portale di gestione di Azure:  
![graphic26][graphic26]
2.  Individuare il processo e passare al suo interno:  
![graphic27][graphic27]
3.  Passare alla scheda input o output in base a ruotate se le credenziali in Input o in un Output.  
![graphic28][graphic28]
4.  Fare clic sul comando Interrompi e confermare che il processo è stata interrotta:  
![graphic29][graphic29] attendere che il processo interrompere.
5.  Individuare l'input/output che si desidera ruotare le credenziali e passare al suo interno:  
![graphic30][graphic30]
6.  Passare alla parte 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: Modificare le credenziali per il processo di Analitica flusso

###<a name="blob-storagetable-storage"></a>Archiviazione BLOB lo spazio di archiviazione/tabella
1.  Trovare il campo chiave Account lo spazio di archiviazione e incollare la chiave appena generata:  
![graphic31][graphic31]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:  
![graphic32][graphic32]
3.  Una prova di connessione verrà avviato automaticamente quando si salvano le modifiche, assicurarsi che è ha superato.
4.  Passare alla parte 4.

###<a name="event-hubs"></a>Hub di evento
1.  Trovare il campo chiave dei criteri di evento Hub e incollare la chiave appena generata:  
![graphic33][graphic33]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:  
![graphic34][graphic34]
3.  Una prova di connessione verrà avviato automaticamente quando si salvano le modifiche, assicurarsi che ha superato.
4.  Passare alla parte 4.

###<a name="power-bi"></a>Power BI
1.  Fare clic su autorizzazione rinnovare:  
* ![graphic35][graphic35]
* Si otterrà la conferma seguente:  
* ![graphic36][graphic36]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:  
![graphic37][graphic37]
3.  Verificare che ha superato una prova di connessione verrà avviato automaticamente quando si salvano le modifiche.
4.  Passare alla parte 4.

###<a name="sql-database"></a>Database SQL
1.  Trovare i campi nome utente e Password e incollare l'insieme di credenziali appena creato:  
![graphic38][graphic38]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:  
![graphic39][graphic39]
3.  Una prova di connessione verrà avviato automaticamente quando si salvano le modifiche, assicurarsi che ha superato.  
4.  Passare alla parte 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: Avviare il processo dall'ultimo ora ha smesso di
1.  Passa a un'altra Input/Output:  
![graphic40][graphic40]
2.  Fare clic sul comando Start:  
![graphic41][graphic41]
3.  Selezionare l'ultima volta arrestato e fare clic su OK:  
 ![graphic42][graphic42]
4.  Passare alla parte 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Rimuovere il vecchio set di credenziali
Questa parte è applicabile al input/output seguenti:
* Archiviazione BLOB
* Hub di evento
* Database SQL
* Archiviazione tabella

###<a name="blob-storagetable-storage"></a>Archiviazione BLOB lo spazio di archiviazione/tabella
Ripetere parte 1 per il tasto di scelta utilizzato in precedenza, il lavoro per rinnovare un tasto di scelta rapida al ora inutilizzato.

###<a name="event-hubs"></a>Hub di evento
Ripetere parte 1 per la chiave utilizzata in precedenza tramite il processo di rinnovo chiave ora inutilizzata.

###<a name="sql-database"></a>Database SQL
1.  Tornare alla finestra della query da parte 1 passaggio 7 e digitare la query seguente sostituendo < previous_login_name > con il nome utente utilizzato per il lavoro:  
`DROP LOGIN <previous_login_name>`  
2.  Fare clic su Esegui:  
    ![graphic43][graphic43]  

È necessario ottenere la conferma seguente: 

    Command(s) completed successfully.

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
