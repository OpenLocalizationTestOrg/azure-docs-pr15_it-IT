<properties
    pageTitle="Gruppi di computer nel Log Analitica accedere ricerche | Microsoft Azure"
    description="Gruppi di computer nel Log Analitica consentono di ambito delle ricerche log a un gruppo specifico di computer.  In questo articolo descrive i vari metodi che è possibile utilizzare per creare gruppi di computer e sul loro utilizzo di una ricerca di log."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>

# <a name="computer-groups-in-log-analytics-log-searches"></a>Gruppi di computer nel Log Analitica accedere ricerche
Gruppi di computer nel Log Analitica consentono di ambito [accedere ricerche](log-analytics-log-searches.md) a un gruppo specifico di computer.  Ogni gruppo viene popolata con computer tramite una query che è possibile definire o importando gruppi provenienti da origini diverse.  Quando il gruppo è incluso in una ricerca di log, i risultati sono limitati ai record che soddisfano i computer nel gruppo.

## <a name="creating-a-computer-group"></a>Creazione di un gruppo di computer
È possibile creare un gruppo di computer nel Log Analitica utilizzando uno dei metodi descritti nella tabella seguente.  Nelle sezioni seguenti vengono fornite informazioni dettagliate su ogni metodo. 

| Metodo | Descrizione |
|:---|:---|
| Ricerca dei registri       | Creare un log di ricerca che restituisce un elenco di computer e salvare i risultati in un gruppo di computer. |
| Ricerca dei registri API   | Utilizzare l'API di ricerca Log per creare un gruppo in base ai risultati di una ricerca di log. |
| Active Directory | Analizza automaticamente l'appartenenza al gruppo di qualsiasi computer agente che fanno parte di un dominio di Active Directory e creare un gruppo in Analitica Log per ogni gruppo di sicurezza.
| WINDOWS SERVER UPDATE SERVICES              | Cercare l'assegnazione del gruppi di Windows Server Update Services server o client e creare un gruppo in Analitica Log per ogni automaticamente. |


### <a name="log-search"></a>Ricerca dei registri

Gruppi di computer creati da una ricerca di Log conterrà tutti i computer restituiti da una query di ricerca definite dall'utente.  Questa query viene eseguita ogni volta che viene utilizzato il gruppo di computer in modo che vengano riflesse le modifiche dal momento che è stato creato il gruppo.

Utilizzare la procedura seguente per creare un gruppo di computer da una ricerca di log.

1. [Creare una ricerca di log](log-analytics-log-searches.md) che restituisce un elenco di computer.  La ricerca deve restituire un set di valori univoci di computer utilizzando uno strumento come **Computer distinti** o **Count misura dal Computer** nella query.  
2. Fare clic sul pulsante **Salva** nella parte superiore dello schermo.
3. Selezionare **Sì** per **salvare questa query come un gruppo di computer:**.
4. Digitare un **nome** e una **categoria** per il gruppo.  Se esiste già una ricerca con lo stesso nome e categoria, verrà richiesto di sovrascrivere il file.  È possibile impostare più ricerche con lo stesso nome in categorie diverse. 

Ecco le ricerche di esempio che è possibile salvare in un gruppo di computer.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>Accedere all'API di ricerca

Gruppi di computer creati con l'API di ricerca Log sono le stesse ricerche create con una ricerca di Log.

Per informazioni dettagliate sulla creazione di un gruppo di computer tramite l'API di ricerca Log vedere [gruppi di Computer nel Log Analitica accedere all'API REST di ricerca](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory

Quando si configura Log Analitica importare appartenenze ai gruppi di Active Directory, esso verrà analizzare l'appartenenza al gruppo di tutti i computer con l'agente OMS di dominio.  Viene creato un gruppo di computer in Analitica Log per ogni gruppo di sicurezza di Active Directory e ogni computer viene aggiunta ai gruppi di computer corrispondente ai gruppi di sicurezza che cui sono membri.  Appartenenza a questo verrà costantemente aggiornata ogni 4 ore.  

Configurare Analitica Log per importare i gruppi di sicurezza Active Directory dal menu a **Gruppi di Computer** nel Log Analitica **Impostazioni**.  Selezionare **l'automazione** e quindi **appartenenze ai gruppi di Active Directory di importazione da computer**.  Non esiste alcun ulteriori operazioni di configurazione necessari.

![Gruppi di computer da Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Dopo aver importati i gruppi, i menu verrà elencati il numero di computer con l'appartenenza ai gruppi rilevato e il numero di gruppi importati.  È possibile fare clic su uno di questi collegamenti per restituire i record **ComputerGroup** con queste informazioni.

### <a name="windows-server-update-service"></a>Servizio di aggiornamento di Windows Server

Quando si configura Analitica Log per importare appartenenze Windows Server Update Services, esso verrà analizzare l'appartenenza al gruppo di destinazione di qualsiasi computer con l'agente OMS.  Se si utilizza lato client destinazione, tutti i computer sia connesso a OMS che fa parte di qualsiasi Windows Server Update Services l'assegnazione del gruppi avrà l'appartenenza al gruppo importato Analitica Log. Se si utilizza sul lato server destinazione, OMS agente dovrebbe essere installata nel server Windows Server Update Services affinché le informazioni sull'appartenenza di gruppo essere importato in OMS.  Appartenenza a questo verrà costantemente aggiornata ogni 4 ore. 

Configurare Analitica Log per importare i gruppi di sicurezza Active Directory dal menu a **Gruppi di Computer** nel Log Analitica **Impostazioni**.  Selezionare **Active Directory** e quindi **appartenenze ai gruppi di Active Directory di importazione da computer**.  Non esiste alcun ulteriori operazioni di configurazione necessari.

![Gruppi di computer da Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Dopo aver importati i gruppi, i menu verrà elencati il numero di computer con l'appartenenza ai gruppi rilevato e il numero di gruppi importati.  È possibile fare clic su uno di questi collegamenti per restituire i record **ComputerGroup** con queste informazioni.

## <a name="managing-computer-groups"></a>Gestione dei gruppi di computer

È possibile visualizzare gruppi di computer che sono stati creati da una ricerca Registro o l'API di ricerca Registro dal menu a **Gruppi di Computer** nel Log Analitica **Impostazioni**.  Fare clic sulla **x** nella colonna **Rimuovi** per eliminare il gruppo di computer.  Fare clic sull'icona di **visualizzare i membri** per un gruppo per l'esecuzione di ricerca dei registri del gruppo che restituisce i membri. 

![Gruppi di computer salvato](media/log-analytics-computer-groups/configure-saved.png)

Per modificare il gruppo, creare un nuovo gruppo con la **categoria** e **nome** stesso per sovrascrivere il gruppo originale.

## <a name="using-a-computer-group-in-a-log-search"></a>Utilizzo di un gruppo di computer di una ricerca di log
Utilizzare la sintassi seguente per fare riferimento a un gruppo di una ricerca di log.  Richiesta la specifica che la **categoria** è facoltativo, utile se si dispone di gruppi di computer con lo stesso nome in categorie diverse. 

    $ComputerGroups[Category: Name]

Quando si esegue una ricerca, vengono risolti prima di tutto i membri di gruppi di computer inclusi nella ricerca.  Se il gruppo è basato su una ricerca di log, la ricerca viene eseguita per restituire i membri del gruppo prima di eseguire la ricerca di log di livello superiore.

Gruppi di computer vengono in genere utilizzati con la clausola **** nella ricerca del registro come illustrato nell'esempio seguente.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Record dei gruppi di computer

Viene creato un record nel repository OMS per ogni appartenenza ai gruppi di computer creato da Active Directory o Windows Server Update Services.  Questi record con un tipo di **ComputerGroup** e dispongono le proprietà nella tabella seguente.  Record non vengono creati per i gruppi di computer in base a ricerche dei registri.

| Proprietà | Descrizione |
|:--|:--|
| Tipo                | *ComputerGroup* |
| SourceSystem        | *SourceSystem*  |
| Computer            | Nome del computer membro. |
| Gruppo               | Nome del gruppo. |
| GroupFullName       | Percorso completo al gruppo inclusi di origine e il nome di origine.
| GroupSource         | Il gruppo di origine è stato raccolti da. <br><br>Active Directory<br>WINDOWS SERVER UPDATE SERVICES<br>WSUSClientTargeting |
| GroupSourceName     | Nome dell'origine raccolti dai gruppi.  Per Active Directory, questo è il nome di dominio. |
| ManagementGroupName | Nome del gruppo di gestione per gli agenti SCOM.  Per altri agenti, si tratta AOI -\<ID area di lavoro\> |
| TimeGenerated       | Data e ora di creazione o aggiornamento del gruppo. |



## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [ricerche dei registri](log-analytics-log-searches.md) analizzare i dati raccolti mediante le soluzioni e le origini dati.  