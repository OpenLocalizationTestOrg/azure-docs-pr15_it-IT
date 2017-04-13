<properties 
   pageTitle="Creazione di un processo B2B in Azure App servizio | Microsoft Azure" 
   description="Panoramica su come creare un processo Business-to-Business" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Creazione di un processo B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Scenario aziendale 
Contoso e Northwind sono due business partner. Contoso (rivenditore) Invia ordini di Northwind (fornitore) mediante un trasporto livello di settore, ad esempio AS2. Northwind archivia tutti gli ordini in arrivo nella propria area di archiviazione Cloud. Gli ordini sono messaggi XML tra queste due partner. Dopo il messaggio è archiviato in archiviazione cloud Northwind processi interni Northwind gestiscono l'ordine di da quel momento in.
 
L'obiettivo di questa esercitazione è per stabilire come Northwind possibile stabilire un processo aziendale tramite cui può ricevere i messaggi (ordini in formato XML) dal partner Contoso tramite AS2 e quindi archiviarlo nella propria area di archiviazione Cloud.


## <a name="capabilities-demonstrated"></a>Funzionalità illustrate 
In questa esercitazione consente di mostrare le funzionalità seguenti: 

- **Trasporti messaggio**: il rivenditore e fornitore possono essere su piattaforme diverse, ma è comunque possibile ottenere le comunicazioni tra i due. In questa esercitazione si comunica su AS2 (applicabilità istruzione 2). AS2 è generalmente usati per trasporto dati tra partner commerciali in business-to-business comunicazioni.
- **Persistenza dei dati**: dopo il messaggio è stato ricevuto su AS2 Northwind desidera che in modo permanente prima ulteriori elaborazioni. È possibile utilizzare un connettore in modo permanente i messaggi nella propria area di archiviazione Cloud. In questa esercitazione BLOB Azure è vengono utilizzati come lo spazio di archiviazione cloud per Northwind.
- **Creazione di un processo aziendale**: In un flusso App API più possibile accoppiati insieme per ottenere un risultato dall'azienda, come illustrato di seguito.


## <a name="before-you-begin"></a>Prima di iniziare
In questa esercitazione si presuppone che hanno familiarità con servizi di App di Windows Azure, saper creare App API e panorama un flusso.


## <a name="steps-to-achieve-the-business-scenario"></a>Procedura per ottenere lo scenario di business
**Creare e configurare le app API necessari**

1. Creare un'istanza di **Azure lo spazio di archiviazione Blob connettore**. Questo richiede le credenziali per un account di archiviazione Azure. Assicurarsi che sia pronto prima di iniziare la creazione di questa.
2. Creare un'istanza di **Gestione Partner commerciali BizTalk**. È necessario un Database SQL vuoto alla funzione. Assicurarsi che sia pronto prima di iniziare la creazione di questa.
3. Creare un'istanza del **Connettore AS2**. È anche necessario un Database SQL vuoto alla funzione. Assicurarsi che sia pronto prima di iniziare la creazione di questa. Inoltre, se si desidera archiviare i messaggi come parte del AS2 elaborazione, è possibile fornire le credenziali per un Blob Azure durante la creazione.
4. Configurare il servizio TPM (Trading Partner Management) creato:  
    1. Passare all'istanza del servizio TPM creato durante la procedura precedente.
    2. Usare l'opzione **partner** in *componenti* per **aggiungere** un nuovo partner denominato **Contoso** e il suo profilo aggiungere l'identità AS2 necessario.
    3. Usare l'opzione **partner** in *componenti* per **aggiungere** un nuovo partner denominato **Northwind** e il suo profilo aggiungere l'identità AS2 necessario.
    4. Utilizzare l'opzione **accordi** in *componenti* per **aggiungere** un nuovo AS2 accordo Northwind e Contoso. Northwind sarà il partner di hosting e Contoso sarà il partner guest. In base alle esigenze configurare l'accesso, crittografia, compressione e riconoscimenti durante la creazione di contratto. Nel caso in cui è necessario utilizzare i certificati, è possibile caricare tramite l'opzione **i certificati** durante l'esplorazione del servizio TPM viene creato.


## <a name="create-a-flow--business-process"></a>Creare un flusso di / processi aziendali
1. Creare un nuovo flusso in cui il primo passaggio consiste AS2. Trascinare e rilasciare il **Connettore AS2** e scegliere l'istanza già creata. Scegliere trigger come la funzionalità di:  
    ![][1]  
2. Quindi trascinare e rilasciare **Azure lo spazio di archiviazione Blob connettore** e scegliere l'istanza già creata. Scegliere l'azione come la funzionalità all'interno della quale, selezionare **Carica Blob** come la funzionalità desiderata. Configurare in base alle esigenze.
3. Ora creare/distribuire il flusso.


## <a name="message-processing--troubleshooting"></a>Elaborazione dei messaggi e risoluzione dei problemi
1. È possibile testare il flusso che abbiamo installato. Inviare che XML messaggi racchiuso in AS2 (in base alle contratto AS2 creato in precedenza) all'endpoint AS2 resi disponibili tramite l'istanza di AS2Connector che è stato creato. Potrebbe essere necessario configurare l'autenticazione per l'endpoint in modo che sia accessibile al pubblico.
2. Informazioni di esecuzione del flusso sono resi disponibili tramite esplorazione al flusso e quindi eseguire l'istanza di flusso che se si ha eseguito
3. Per informazioni di elaborazione AS2, passare all'istanza AS2Connector correlata e quindi seguire eseguendo la parte di verifica. È possibile utilizzare i filtri necessari per limitare la visualizzazione di informazioni che si desiderano.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
