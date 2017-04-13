<properties
    pageTitle="Aggiornare la soluzione di gestione in OMS | Microsoft Azure"
    description="In questo articolo consentono di imparare a usare questa soluzione per gestire gli aggiornamenti per i computer Windows e Linux."
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![Soluzione di gestione degli aggiornamenti in OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Soluzione di gestione degli aggiornamenti in OMS

La soluzione di gestione degli aggiornamenti in OMS consente di gestire gli aggiornamenti per i computer Windows e Linux.  Valutare lo stato degli aggiornamenti disponibili in tutti i computer agente e avviare il processo di installazione degli aggiornamenti necessari per i server rapidamente. 

## <a name="prerequisites"></a>Prerequisiti

-   Gli agenti di Windows devono essere configurati per comunicare con un server di Windows Server Update Services (WSUS) o avere accesso a Microsoft Update.  

    >[AZURE.NOTE] Agente di Windows non è possibile gestire contemporaneamente da System Center Configuration Manager.  
  
-   Agenti Linux devono avere accesso a un repository di aggiornamento.  L'agente OMS per Linux può essere scaricato dal [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Configurazione

Eseguire la procedura seguente per aggiungere la soluzione di gestione degli aggiornamenti nell'area di lavoro OMS e aggiungere gli agenti Linux.  Gli agenti di Windows vengono automaticamente aggiunti senza altre configurazioni.

1.  Aggiungere la soluzione di gestione degli aggiornamenti nell'area di lavoro OMS usando la procedura descritta nella sezione [aggiungere OMS soluzioni](../log-analytics/log-analytics-add-solutions.md) dalla raccolta soluzioni.  
2.  Nel portale OMS, selezionare **Impostazioni** e quindi **Origini connesse**.  Nota l' **ID dell'area di lavoro** e la **chiave primaria** o **chiave secondaria**.
3.  Eseguire i passaggi seguenti per ogni computer Linux.

    un.  Installare la versione più recente dell'agente OMS per Linux eseguendo i comandi seguenti.  Sostituire <Workspace ID> con l'ID dell'area di lavoro e <Key> con la chiave primaria o secondaria.

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b. Per rimuovere l'agente, eseguire il comando seguente.

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Management Pack

Se il gruppo di gestione System Center Operations Manager è collegato all'area di lavoro OMS, quindi i seguenti management pack verrà installato in Operations Manager quando si aggiunge questa soluzione. Non esiste alcun configurazione o manutenzione di questi management pack necessari. 

-   Microsoft System Center Advisor valutazione Intelligence pacchetto di aggiornamento delle (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   Aggiornare la distribuzione Pannello di gestione

Per ulteriori informazioni sulla modalità di aggiornamento dei pacchetti di gestione di soluzione, vedere [Connettere Operations Manager per Log Analitica](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Raccolta di dati

### <a name="supported-agents"></a>Agenti supportati

Nella tabella seguente vengono descritte le origini connesse supportate da questa soluzione.

Origine connesso | Supportati | Descrizione|
----------|----------|----------|
Agenti di Windows | Sì | La soluzione raccoglie informazioni sugli aggiornamenti di sistema da agenti di Windows e avvia l'installazione degli aggiornamenti necessari.|
Agenti Linux | Sì | La soluzione raccoglie informazioni sugli aggiornamenti di sistema da parte di agenti Linux.|
Gruppo di gestione di Operations Manager | Sì | La soluzione raccoglie informazioni sugli aggiornamenti di sistema da parte di agenti in un gruppo di gestione connesso.<br>Non è necessaria una connessione diretta da agente di Operations Manager a Log Analitica. Dati vengono inoltrati dal gruppo di gestione archivio di OMS.|
Account di archiviazione Azure | No | Spazio di archiviazione Azure non include informazioni sugli aggiornamenti di sistema.|  

### <a name="collection-frequency"></a>Frequenza di raccolta

Per ogni computer Windows gestito, viene eseguita un'analisi due volte al giorno.  Quando si installa un aggiornamento, viene aggiornate le informazioni relative all'interno di 15 minuti.  

Per ogni computer Linux gestito, viene eseguita un'analisi ogni tre ore.  

## <a name="using-the-solution"></a>Utilizzo della soluzione

Quando si aggiunge la soluzione di gestione degli aggiornamenti nell'area di lavoro OMS, il riquadro di **Gestione degli aggiornamenti** verrà aggiunti al dashboard OMS. Questa sezione consente di visualizzare un conteggio e rappresentazione grafica del numero di computer nel proprio ambiente che richiedono gli aggiornamenti del sistema.<br><br>
![Riquadro Riepilogo gestione dell'aggiornamento](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Valutazioni di aggiornamento di una visualizzazione

Fare clic sul riquadro **Gestione degli aggiornamenti** per aprire il dashboard di **Gestione degli aggiornamenti** . Il dashboard include le colonne nella tabella seguente. Ogni colonna sono elencati fino a 10 elementi corrispondenti a criteri della colonna per l'intervallo di tempo e l'ambito specificato. È possibile eseguire una ricerca di log che restituisce tutti i record, fare clic su **tutti** nella parte inferiore della colonna o facendo clic sull'intestazione della colonna.

Colonna | Descrizione|
----------|----------|
**Computer aggiornamenti mancanti** ||
Importanti o aggiornamenti della sicurezza | Elenchi ordinata nella parte superiore dieci computer che non sono presenti gli aggiornamenti per il numero di aggiornamenti che siano presenti. Fare clic su un nome del computer per eseguire una ricerca Registro restituzione di che tutti i record per il computer di aggiornamento.|
Critica o aggiornamenti della sicurezza anteriori a 30 giorni| Identifica il numero del computer in cui sono mancanti importanti o aggiornamenti della sicurezza raggruppati per il periodo di tempo dopo l'aggiornamento è stato pubblicato. Fare clic su una delle voci per eseguire una ricerca Registro restituzione di tutti gli aggiornamenti critici e mancanti.|
**Obbligatorio aggiornamenti mancanti**||
Importanti o aggiornamenti della sicurezza | Elenchi di classificazioni degli aggiornamenti di computer mancano ordinata in base al numero di computer aggiornamenti nella categoria mancanti. Fare clic su una classificazione per eseguire una ricerca Registro restituzione di che tutti i record per quella classificazione di aggiornamento.|
**Distribuzioni di aggiornamento**||
Distribuzioni di aggiornamento | Numero di aggiornamento pianificato attualmente distribuzioni e la durata fino alla prossima pianificata.  Fare clic sul riquadro Visualizza programmazioni, attualmente in esecuzione e aggiornamenti completati o per pianificare una nuova distribuzione.|  
<br>  
![Dashboard di riepilogo di gestione di aggiornamento](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Aggiornare visualizzazione Computer di gestione del Dashboard](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Aggiornare visualizzazione pacchetto di gestione del Dashboard](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Installazione degli aggiornamenti

Dopo che sono stati valutati gli aggiornamenti per tutti i computer Windows, nell'ambiente, è possibile aggiornamenti installati mediante la creazione di una *Distribuzione di aggiornamenti*necessari.  Una distribuzione di aggiornamento è un'installazione pianificata degli aggiornamenti necessari per uno o più computer Windows.  Specificare la data e l'ora per la distribuzione oltre a un computer o un gruppo di computer che devono essere inclusi.  

Installare gli aggiornamenti dal runbook in Azure automazione.  Attualmente non è possibile visualizzare tali runbook e non richiede alcuna configurazione.  Creazione di una distribuzione aggiornare, viene creata una programmazione nel che inizia runbook un aggiornamento a master al momento specificato per i computer.  Questo runbook master inizi un runbook figlio su ogni agente di Windows che esegue l'installazione degli aggiornamenti necessari.  

### <a name="viewing-update-deployments"></a>Distribuzioni di aggiornamento di una visualizzazione

Fare clic sul riquadro di **Distribuzione di aggiornamenti** per visualizzare l'elenco esistente della distribuzione degli aggiornamenti.  Sono raggruppate per stato- **pianificato**, **l'esecuzione**e **completato**.<br><br> ![Pagina di pianificazione di aggiornamento distribuzioni](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

Nella tabella seguente sono descritte le proprietà di visualizzazione per la distribuzione di ogni aggiornamento.

Proprietà | Descrizione|
----------|----------|
Nome | Nome della distribuzione degli aggiornamenti.|
Programmazione | Tipo di programmazione.  *OneTime* è l'unico valore possibile.|
Ora di inizio|Data e ora in cui è programmata la distribuzione degli aggiornamenti per iniziare.|
Durata | Numero di minuti che la distribuzione degli aggiornamenti è possibile eseguire.  Se tutti gli aggiornamenti non sono installati all'interno di questa durata, gli aggiornamenti rimanenti necessario attendere che la distribuzione degli aggiornamenti successivi.|
Server | Numero di computer incidono la distribuzione degli aggiornamenti.|
Stato | Stato corrente di distribuzione degli aggiornamenti.<br><br>Valori possibili sono:<br>-Non avviata<br>-Esecuzione<br>-Completato|  

Fare clic su una distribuzione di aggiornamento per visualizzare la schermata di dettaglio che include le colonne nella tabella seguente.  Queste colonne non verranno popolate se la distribuzione di aggiornamento non è ancora iniziata.<br>

Colonna | Descrizione|
----------|----------|
**Risultati di computer**||
È stato completato correttamente | Elenca il numero di computer nella distribuzione degli aggiornamenti in base allo stato.  Fare clic su uno stato per eseguire una ricerca Registro restituzione che tutti aggiornare i record con lo stato per la distribuzione di aggiornamento.|
Stato di installazione di computer| Elenca i computer coinvolti nella distribuzione degli aggiornamenti e la percentuale degli aggiornamenti installati correttamente. Fare clic su una delle voci per eseguire una ricerca Registro restituzione di tutti gli aggiornamenti critici e mancanti.|
**Aggiornare i risultati istanza**||
Stato di installazione di istanza | Elenchi di classificazioni degli aggiornamenti di computer mancano ordinata in base al numero di computer aggiornamenti nella categoria mancanti. Fare clic su un computer per eseguire una ricerca Registro restituzione di che tutti i record per il computer di aggiornamento.|  
<br><br> ![Panoramica dei risultati della distribuzione di aggiornamento](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Creazione di una distribuzione di aggiornamento

Creare una nuova distribuzione di aggiornamento facendo clic sul pulsante **Aggiungi** nella parte superiore della schermata per aprire la pagina **Nuova distribuzione di aggiornamento** .  È necessario specificare i valori per le proprietà nella tabella seguente.

Proprietà | Descrizione|
----------|----------|
Nome | Nome univoco per identificare la distribuzione degli aggiornamenti.|
Fuso orario | Fuso orario da usare per l'ora di inizio.|
Ora di inizio | Data e ora per avviare la distribuzione degli aggiornamenti.|
Durata | Numero di minuti che la distribuzione degli aggiornamenti è possibile eseguire.  Se tutti gli aggiornamenti non sono installati all'interno di questa durata, gli aggiornamenti rimanenti necessario attendere che la distribuzione degli aggiornamenti successivi.|
Computer | Nomi di computer o gruppi di computer da includere nella distribuzione degli aggiornamenti.  Selezionare una o più voci dall'elenco a discesa.|
<br><br> ![Nuova pagina di distribuzione di aggiornamento](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervallo di tempo

Per impostazione predefinita, l'ambito dei dati di analisi della soluzione di gestione degli aggiornamenti è stata inviata da tutti i gruppi di gestione connesso generati all'interno dell'ultimo giorno 1. 

Per modificare l'intervallo di tempo dei dati, selezionare **i dati basati** nella parte superiore del dashboard. È possibile selezionare i record creato o aggiornato negli ultimi sette giorni, 1 giorno o 6 ore. Oppure è possibile scegliere **personalizzato** e specificare un intervallo di date personalizzato.<br><br> ![Opzione intervallo di tempo personalizzato](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Registro Analitica record

La soluzione di gestione degli aggiornamenti crea due tipi di record nell'archivio OMS.

### <a name="update-records"></a>Aggiornare i record

Per ogni aggiornamento è installato o necessario in ogni computer viene creato un record a un tipo di **aggiornamento** . Aggiornare i record sono le proprietà nella tabella seguente.

Proprietà | Descrizione|
----------|----------|
Tipo | *Aggiornamento*|
SourceSystem | Origine che approvato installazione dell'aggiornamento.<br>Valori possibili sono:<br>-Microsoft Update<br>-Windows Update<br>-SCCM<br>-Server Linux (recuperati dal pacchetto responsabili)|
Approvazione | Specifica se l'aggiornamento è stata approvata per l'installazione.<br> Per i server Linux è attualmente facoltativo come patch non viene utilizzato OMS.|
Classificazione per Windows | Classificazione dell'aggiornamento.<br>Valori possibili sono:<br>-Applicazioni<br>-Aggiornamenti critici<br>-Aggiornamenti definizione<br>-Feature pack<br>-Aggiornamenti sicurezza<br>-Service Pack<br>-Aggiornamenti cumulativi<br>-Aggiornamenti|
Classificazione per Linux | Cassification dell'aggiornamento.<br>Valori possibili sono:<br>-Aggiornamenti critici<br>-Aggiornamenti sicurezza<br>-Altri aggiornamenti|
Computer | Nome del computer.|
InstallTimeAvailable | Specifica se la fase di installazione è disponibile da altri agenti che installazione dell'aggiornamento stesso.|
InstallTimePredictionSeconds | Tempo di installazione stimata in secondi in base a altri agenti che installazione dell'aggiornamento stesso.|
KBID | ID dell'articolo della Knowledge base che descrive l'aggiornamento.|
ManagementGroupName | Nome del gruppo di gestione per gli agenti SCOM.  Per altri agenti, si tratta AOI -<workspace ID>.|
MSRCBulletinID | ID del bollettino che descrive l'aggiornamento.|
MSRCSeverity | Gravità del bollettino Microsoft.<br>Valori possibili sono:<br>-Critico<br>-Importante<br>-Moderato|
Facoltativo | Specifica se l'aggiornamento è facoltativo.|
Prodotto | Nome del prodotto che riguarda l'aggiornamento.  Fare clic su **Visualizza** per aprire l'articolo in un browser.|
PackageSeverity | Gravità della vulnerabilità fissata in questo aggiornamento, come indicato dai fornitori distro Linux. | 
PublishDate | Data e ora in cui è stato installato l'aggiornamento.|
RebootBehavior | Specifica se l'aggiornamento forza riavviare il computer.<br>Valori possibili sono:<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | Numero di revisione dell'aggiornamento.|
SourceComputerId | GUID per identificare il computer.|
TimeGenerated | Data e ora che il record è stato aggiornato.|
Titolo | Titolo dell'aggiornamento.|
Codice UpdateID | GUID per identificare l'aggiornamento.|
UpdateState | Specifica se l'aggiornamento è installato nel computer in uso.<br>Valori possibili sono:<br>-- L'aggiornamento è stato installato nel computer in uso.<br>-Se necessario, l'aggiornamento non è installato ed è necessaria nel computer in uso.|  

<br>
Quando si esegue una ricerca di log che restituisce record con un tipo di **aggiornamento** è possibile selezionare la visualizzazione **degli aggiornamenti** che visualizza una serie di riquadri riepilogare gli aggiornamenti restituiti dalla ricerca. È possibile fare clic sulle voci di **mancante e aggiornamenti applicati** e **aggiornamenti obbligatori e facoltativi** riquadri per definire l'ambito della visualizzazione per il set di aggiornamenti. Selezionare la visualizzazione **elenco** o della **tabella** per restituire i singoli record.<br> 

![Visualizzazione di aggiornamento del Registro ricerca con l'aggiornamento di tipo di Record](./media/oms-solution-update-management/update-la-view-updates.png)  

Nella visualizzazione **tabella** , è possibile fare clic su **KBID** per qualsiasi record per aprire un browser con l'articolo della Knowledge Base. In questo modo è possibile consultare rapidamente i dettagli dell'aggiornamento specifico.<br> 

![Visualizzazione per tabella Registro ricerca con gli aggiornamenti di tipo di Record di riquadri](./media/oms-solution-update-management/update-la-view-table.png)

In visualizzazione **elenco** , fare clic su collegamento della **visualizzazione** accanto KBID per aprire l'articolo della Knowledge Base.<br>

![Visualizzazione elenco di ricerca log con gli aggiornamenti di tipo di Record di riquadri](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>Record UpdateSummary

Viene creato un record a un tipo di **UpdateSummary** per ogni computer agente di Windows. Questo record viene aggiornato ogni volta che il computer viene analizzato per gli aggiornamenti. Record **UpdateSummary** hanno le proprietà nella tabella seguente.

Proprietà | Descrizione|
----------|----------|
Tipo | UpdateSummary|
SourceSystem | OpsManager |
Computer | Nome del computer.|
CriticalUpdatesMissing | Numero di aggiornamenti critici mancanti nel computer.|
ManagementGroupName | Nome del gruppo di gestione per gli agenti SCOM. Per altri agenti, si tratta AOI -<workspace ID>.|
NETRuntimeVersion | Versione del runtime .NET installata nel computer.|
OldestMissingSecurityUpdateBucket | Intervallo per categorizzare il tempo dopo la sicurezza mancante dal meno recente nel computer in uso è stato pubblicato.<br>Valori possibili sono:<br>-Meno recenti<br>-180 giorni<br>-150 giorni<br>-120 giorni<br>-90 giorni<br>-60 giorni<br>-passare 30 giorni<br>-Recenti|
OldestMissingSecurityUpdateInDays | Numero di giorni che vanno dall'aggiornamento della sicurezza mancante dal meno recente nel computer in uso è stato pubblicato.|
OsVersion | Versione del sistema operativo installato nel computer.|
OtherUpdatesMissing | Numero di altri aggiornamenti mancanti nel computer.|
SecurityUpdatesMissing | Numero di aggiornamenti di protezione mancanti nel computer.|
SourceComputerId | GUID per identificare il computer.|
TimeGenerated | Data e ora che il record è stato aggiornato.|
TotalUpdatesMissing |Numero totale di aggiornamenti mancanti nel computer.|
WindowsUpdateAgentVersion | Numero di versione dell'agente di Windows Update nel computer.|
WindowsUpdateSetting | Impostazione per la modalità in aggiornamenti importanti verrà installati nel computer.<br>Valori possibili sono:<br>-Disattivato<br>-Notifica prima dell'installazione<br>-Installazione pianificata|
WSUSServer | Server di Windows Server Update URL di Services se il computer è configurato per usare uno.|  

## <a name="sample-log-searches"></a>Ricerche dei registri di esempio

La tabella seguente contiene ricerche dei registri di esempio per aggiornare i record raccolte da questa soluzione. 

Query | Descrizione|
----------|----------|
Tutti i computer con gli aggiornamenti mancanti | Tipo = UpdateState aggiornamento = necessari facoltativo = false & #124; selezionare Computer, titolo, KBID, classificazione, UpdateSeverity, PublishedDate|
Aggiornamenti mancanti per computer "COMPUTER01.contoso.com" (Sostituisci con il proprio nome del computer) | Tipo = UpdateState aggiornamento = necessari facoltativo = false Computer = "COMPUTER01.contoso.com" & #124; selezionare Computer, titolo, KBID, prodotto, UpdateSeverity, PublishedDate|
Tutti i computer con mancanti importanti o aggiornamenti della sicurezza | Tipo = UpdateState aggiornamento = necessari facoltativo = false (classificazione = classificazione o "Aggiornamenti della sicurezza" = "Aggiornamenti critici")|
Aggiornamenti critici o di sicurezza necessari per i computer in cui vengono applicati manualmente gli aggiornamenti | Tipo = UpdateState aggiornamento = necessari facoltativo = false (classificazione = classificazione o "Aggiornamenti della sicurezza" = "Aggiornamenti critici") IN Computer {tipo = UpdateSummary WindowsUpdateSetting = #124; & manuale Computer distinti} & #124; KBID distinti|
Eventi di errore per computer in cui Manca critiche o sicurezza necessari aggiornamenti | Tipo = evento EventLevelName = errore IN Computer {tipo = aggiornamento (classificazione = classificazione o "Aggiornamenti della sicurezza" = "Aggiornamenti critici") UpdateState = necessari facoltativo = false & #124; Computer distinti}|
Tutti i computer con aggiornamenti cumulativi mancanti | Tipo = facoltativo aggiornamento = falsa classificazione = "Aggiornamenti cumulativi" UpdateState = necessario & #124; selezionare Computer, titolo, KBID, classificazione, UpdateSeverity, PublishedDate|
Aggiornamenti mancanti distinti in tutti i computer | Tipo = UpdateState aggiornamento = necessari facoltativo = false & #124; Titolo distinto|
Appartenenza a un computer Windows Server Update Services | Tipo = UpdateSummary & #124; misurare Count da WSUSServer|
Configurazione di aggiornamento automatico | Tipo = UpdateSummary & #124; misurare Count da WindowsUpdateSetting|
Computer con l'aggiornamento automatico disattivato | Tipo = UpdateSummary WindowsUpdateSetting = manuale|  
Elenco di tutti i computer Linux che hanno un aggiornamento del pacchetto disponibile | Tipo = aggiornamento e OSType = Linux e UpdateState! = "Non è necessario" & #124; misurare Count dal Computer|
Elenco di tutti i computer Linux che hanno disponibile un aggiornamento pacchetto cui vulnerabilità critiche o sicurezza | Tipo = aggiornamento e OSType = Linux e UpdateState! = "Non è necessario" e (classificazione = classificazione o "Aggiornamenti critici" = "Aggiornamenti della sicurezza") & #124; misurare Count dal Computer|
Elenco di tutti i pacchetti è disponibile un aggiornamento | Tipo = aggiornamento e OSType = Linux e UpdateState! = "Non è necessario"|
Elenco di tutti i pacchetti è disponibile un aggiornamento che risolve vulnerabilità critiche o sicurezza | Tipo = aggiornamento e OSType = Linux e UpdateState! = "Non è necessario" e (classificazione = classificazione o "Aggiornamenti critici" = "Aggiornamenti della sicurezza")|
Elenco di tutti i computer "Ubuntu" con gli aggiornamenti disponibili | Tipo = aggiornamento e OSType = Linux e OSName = Ubuntu & #124; misurare Count dal Computer|

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare ricerche dei registri [Analitica Log](../log-analytics/log-analytics-log-searches.md) per visualizzare informazioni dettagliate aggiornamento dei dati.

- [Creare dashboard personalizzati](../log-analytics/log-analytics-dashboards.md) con conformità degli aggiornamenti per i computer gestiti.

- [Creare avvisi](../log-analytics/log-analytics-alerts.md) quando vengono rilevati aggiornamenti critici come mancanti dal computer o un computer sono previsti gli aggiornamenti automatici disabilitati.  




