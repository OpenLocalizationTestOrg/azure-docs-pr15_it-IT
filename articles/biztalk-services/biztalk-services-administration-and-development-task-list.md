<properties
    pageTitle="Elenco di servizi BizTalk attività di amministrazione e lo sviluppo | Microsoft Azure"
    description="Pianificazione e processo aiuto per la distribuzione di Azure BizTalk Services."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Amministrazione e l'elenco di attività di sviluppo in servizi BizTalk  

## <a name="getting-started"></a>Guida introduttiva
Quando si lavora con i servizi BizTalk di Microsoft Azure, sono disponibili diversi locale e basato su cloud componenti da prendere in considerazione. Per iniziare, considerare la procedura seguente:  

|Passaggio|Chi è responsabile|Attività|Collegamenti correlati|
|----|----|----|----|
|1.|Amministratore|Creare l'abbonamento a Microsoft Azure con un account Microsoft o un account aziendale|[Portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Amministratore|Creare o eseguire il provisioning BizTalk Service.|[Creare un BizTalk Service Azure nel portale classica](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Amministratore|Eseguire la registrazione è o distribuzione servizi BizTalk della società|[La registrazione e aggiornamento di una distribuzione di servizio BizTalk nel portale di servizi di BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Amministratore|Applica se l'applicazione utilizza Servizio scheda BizTalk per connettersi a un sistema Line-of-Business (Line) locale o utilizza una coda o un argomento destinazione.  Creare il Namespace Bus servizio Azure. Assegnare questo spazio dei nomi, nome autorità che emette Bus di servizio e i valori di chiave Bus servizio per gli sviluppatori.|[Procedura: creare o modificare un Namespace di servizio servizio Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [ottenere nome autorità che emette e i valori di chiave](biztalk-issuer-name-issuer-key.md)|
|5.|Per sviluppatori|Installare SDK e creare il progetto BizTalk Service in Visual Studio.|[Installare servizi BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [creare l'endpoint di messaggistica RTF in Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Per sviluppatori|Distribuire il BizTalk Service project per il BizTalk Service ospitati in Azure.|[La distribuzione e l'aggiornamento del progetto di servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Amministratore|Si applica se si utilizza EDI.  È possibile aggiungere partner e creare contratti nel portale di Microsoft Azure BizTalk Services. Quando si crea un contratto, è possibile aggiungere il bridge e/o le trasformazioni create dallo sviluppatore per le impostazioni del contratto.|[Configurazione EDI e AS2 EDIFACT nel portale di servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Amministratore|Tramite il portale classico Azure, monitorare lo stato del BizTalk Service, inclusi dati sulle prestazioni.|[BizTalk Services: Schede del Dashboard, monitorare e scala](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Amministratore|Portale di Microsoft Azure BizTalk Services, gestire gli elementi utilizzati da BizTalk Services e tenere traccia dei messaggi che vengono elaborati da file bridge.|[Tramite il portale di servizi BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Amministratore|Creare un piano di backup per eseguire il backup BizTalk Service.|[Continuità aziendale e in BizTalk servizi di emergenza](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Passaggi successivi
[Esercitazioni ed esempi](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Creare il progetto in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installare servizi BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Concetti
[Creare il progetto in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 ed EDIFACT messaggistica (per aziende)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Altre risorse  
[Aggiunta di origine e destinazione Bridge messaggistica i punti finali](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Informazioni e creare mappe messaggi e le trasformazioni](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Tramite il servizio scheda BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Servizi BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)
