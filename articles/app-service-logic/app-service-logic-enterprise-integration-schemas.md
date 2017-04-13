<properties
    pageTitle="Panoramica di schemi e il Language Pack integrazione Enterprise | Microsoft Azure"
    description="Informazioni su come usare schemi con le app Enterprise Integration Pack e logica"
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
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Informazioni sugli schemi e il Language Pack integrazione Enterprise  

## <a name="why-use-a-schema"></a>Perché usare uno schema?
Usare schemi per verificare che i documenti XML che viene visualizzato sono validi, con i dati previsti in un formato predefinito. Schemi vengono utilizzati per convalidare i messaggi scambiati in uno scenario B2B.

## <a name="add-a-schema"></a>Aggiungere uno schema
Dal portale di Azure:  

1. Selezionare **altri servizi**.  
![Portale schermata di Azure con "Altri servizi" evidenziato](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Nella casella di ricerca filtro immettere **integrazione**e selezionare **Account integrazione** dall'elenco dei risultati.     
![Schermata della casella di ricerca di filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Selezionare l' **account di integrazione** a cui aggiungere lo schema.    
![Schermata dell'elenco degli account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Selezionare il riquadro di **schemi** .  
![Schermata di IEP integrazione Account, con "Schemi" evidenziati](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Aggiungere un file di schema minore di 2 MB  

1. Nella finestra e **schemi** che viene visualizzata (mediante la procedura precedente), selezionare **Aggiungi**.  
![Blade schermata di schemi, con il pulsante "Aggiungi" evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Immettere un nome per lo schema. Per caricare il file di schema, quindi selezionare l'icona di cartella accanto alla casella di testo **Schema** . Al termine il processo di caricamento, scegliere **OK**.    
![Schermata di "Aggiungi Schema", "File Small" evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Aggiungere un file di schema superiore a 2 MB (fino a un massimo di 8 MB)  

Questo processo dipende dal livello di accesso contenitore blob: **pubblico** o **non consentire l'accesso anonimo**. Per determinare il livello di accesso, in **Esplora archivi Azure**in **Contenitori Blob**, selezionare il contenitore blob desiderato. Selezionare **protezione**e selezionare la scheda **Livello di accesso** .

1. Se il livello di accesso di sicurezza blob è **pubblico**, seguire questa procedura.  
  ![Schermata di Azure Esplora archivi con "Contenitori Blob", "Sicurezza" e "Pubblico" evidenziato](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    un. Caricare lo schema allo spazio di archiviazione e copiare l'URI.  
    ![Creazione di account di archiviazione di URI evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. In **Aggiungi Schema**, selezionare **file di grandi dimensioni**e fornire URI nella casella di testo **URI del contenuto** .  
    ![Schermata degli schemi, con il pulsante "Aggiungi" e "File di grandi dimensioni" evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Se il livello di accesso di sicurezza blob **non consentire l'accesso anonimo**, seguire questa procedura.  
  ![Schermata di Azure Esplora archivi con "Contenitori Blob", "Sicurezza" e "Nessun accesso anonimo" evidenziato](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    un. Caricare lo schema di spazio di archiviazione.  
    ![Schermata dell'account di archiviazione](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Generare una firma di accesso condiviso per lo schema.  
    ![Schermata della sccount di spazio di archiviazione, con la scheda firme accesso condiviso evidenziata](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. In **Aggiungi Schema**, selezionare **file di grandi dimensioni**e fornire la firma di accesso condiviso URI nella casella di testo **URI del contenuto** .  
    ![Schermata degli schemi, con il pulsante "Aggiungi" e "File di grandi dimensioni" evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. In e lo **schemi** dell'Account di integrazione EIP, viene visualizzato lo schema appena aggiunto.  
![Schermata di EIP integrazione Account "Schemi" e il nuovo schema evidenziato](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Modificare gli schemi
1. Selezionare il riquadro di **schemi** .  
2. Selezionare lo schema che si desidera modificare da e lo **schemi** visualizzata.
3. Scegliere **Modifica**e lo **schemi** .  
![Blade schermata di schemi](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Selezionare il file di schema che si desidera modificare utilizzando la finestra di dialogo per la selezione file che verrà visualizzato.
5. Selezionare **Apri** in selezione dei file.  
![Schermata di selezione dei file](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Si riceve una notifica che indica che il caricamento ha avuto esito positivo.  

## <a name="delete-schemas"></a>Eliminare gli schemi
1. Selezionare il riquadro di **schemi** .  
2. Selezionare lo schema che si desidera eliminare da e lo **schemi** visualizzata.  
3. Scegliere **Elimina**e lo **schemi** .
![Blade schermata di schemi](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Per confermare la scelta, selezionare **Sì**.  
![Schermata del messaggio di conferma "Elimina schema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Infine, si noterà che consente di aggiornare l'elenco degli schemi in e lo **schemi** e lo schema che è stato eliminato non sia più presente.  
![Schermata di EIP integrazione Account, con "Schemi" evidenziati](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sull'organizzazione Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Informazioni sul language pack integrazione enterprise").  
