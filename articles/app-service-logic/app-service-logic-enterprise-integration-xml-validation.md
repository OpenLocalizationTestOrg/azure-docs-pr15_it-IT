<properties 
    pageTitle="Panoramica della convalida XML nel pacchetto di integrazione Enterprise | Servizio di Microsoft Azure App | Microsoft Azure" 
    description="Informazioni sul funzionamento di convalida nelle App Enterprise Integration Pack e logica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-validation"></a>Integrazione di Enterprise con convalida XML

## <a name="overview"></a>Panoramica
Spesso in scenari B2B, i partner per un contratto necessario per convalidare messaggi scambiati tra loro validi prima di poter iniziare l'elaborazione dei dati. Nel pacchetto di integrazione dell'organizzazione, è possibile utilizzare il connettore convalida XML per convalidare documenti rispetto a uno schema predefinito.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Come eseguire la convalida di un documento con il connettore convalida XML
1. Creare una logica app e [collegarlo al proprio account di integrazione](./app-service-logic-enterprise-integration-accounts.md "su come collegare un account di integrazione per un'app di logica") che contiene lo schema che verrà utilizzato per convalidare i dati XML.
2. Aggiungere un trigger **richiesta - richiesta HTTP un quando si riceve** l'app logica  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Aggiungere l'azione **Convalida XML** dal primo selezione **per aggiungere un'azione**  
4. Immettere *xml* nella casella di ricerca per filtrare tutte le azioni a quello che si desidera utilizzare 
5. Selezionare **la convalida XML**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Selezionare la casella di testo **del contenuto**  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Selezionare il tag corpo come il contenuto che verrà convalidato.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Selezionare la casella di riepilogo **Nome dello SCHEMA** e scegliere lo schema che si desidera utilizzare per convalidare l' input *contenuto* precedente     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Salvare il lavoro  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

A questo punto, non si desidera configurare il connettore di convalida. In un'applicazione di scenari reali, si desidera memorizzare i dati convalidati in un'applicazione line, ad esempio SalesForce. È possibile aggiungere facilmente un'azione per inviare l'output della convalida in Salesforce. 

È ora possibile verificare l'azione convalida effettuando una richiesta all'endpoint HTTP.  

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'organizzazione Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Informazioni sui Service Pack integrazione Enterprise")   