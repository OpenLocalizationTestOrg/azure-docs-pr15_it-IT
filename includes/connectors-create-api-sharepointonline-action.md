Dopo avere aggiunto un trigger, l'ora di eseguire un'azione interessanti con i dati che viene generati dal trigger. Seguire questa procedura per aggiungere l'azione di **SharePoint Online - creare file** . Questa azione verrà creato un file in SharePoint Online ogni volta che viene generato il nuovo trigger di elemento. 

Per configurare questa azione, sarà necessario fornire le informazioni seguenti. Come si fornire queste informazioni, si noterà che è facile da usare dati generati dal trigger come input per alcune delle proprietà per il nuovo file:

|Creare proprietà del file|Descrizione|
|---|---|
|URL del sito|Questo è l'URL del sito di SharePoint Online in cui si vuole creare il nuovo file. Selezionare il sito dall'elenco visualizzato.|
|Percorso della cartella|Si tratta della cartella (all'URL del sito selezionata nel passaggio precedente) in cui verrà inserito il nuovo file. Cercare e selezionare la cartella.|
|Nome del file|Questo è il nome del file da creare.|
|Contenuto del file|Il contenuto che verrà scritti al file.|

1. Selezionare **+ nuova azione** per aggiungere l'azione.  
![Immagine di SharePoint online azione 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Selezionare il collegamento **Aggiungi un'azione** . Verrà visualizzata la casella di ricerca in cui è possibile cercare qualsiasi azione si desidera eseguire. In questo esempio, le azioni di SharePoint sono di interesse.    
![Immagine di SharePoint online azione 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Immettere *sharepoint* per eseguire una ricerca per le azioni correlate in SharePoint.
- Selezionare **SharePoint Online - creare file** come l'azione da eseguire.   **Nota**: verrà richiesto di autorizzare l'app logica per accedere all'account di SharePoint se non è stato creato in precedenza una connessione a SharePoint Online.    
![Immagine di SharePoint online azione 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- Si apre la **creazione di un file** .   
![Immagine di SharePoint online azione 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Selezionare **L'URL del sito** e individuare il sito in cui si desidera creare il file.     
![Immagine di SharePoint online azione 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Selezionare **percorso della cartella** e individuare la cartella in cui verrà inserito il nuovo file.  
![Immagine di SharePoint online azione 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Selezionare il controllo **del nome di File** e immettere il nome del file che si desidera creare. In questo caso, è possibile immettere il nome del file direttamente oppure è possibile utilizzare le proprietà da trigger creato in precedenza. È possibile selezionare proprietà dall'elenco di **output da quando viene creato un nuovo elemento**. Questo elenco è visualizzato solo dopo aver selezionato il controllo **del nome di File** . In questa procedura dettagliata, selezionato ID (ID della voce di elenco nuovo) come il nome del file viene creato con l'azione **SharePoint Online - creare file** .    
![Immagine di SharePoint online azione 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Selezionare il controllo **contenuto del File** e immettere il contenuto che verrà scritti il file che verrà creato. Per il contenuto del file, si noterà che è possibile utilizzare le proprietà da trigger creato in precedenza. È sufficiente selezionare le proprietà dall'elenco visualizzato. In alternativa, è possibile immettere il testo **contenuto del File** direttamente nel controllo. In questo esempio selezionata alcune proprietà e aggiungere gli spazi e inserire un trattino tra ogni proprietà.        
![Immagine di SharePoint online azione 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Salvare le modifiche al flusso di lavoro  
- Congratulazioni, è ora disponibile un'app pienamente logica che viene attivata quando viene aggiunto un nuovo elemento in un elenco di SharePoint Online. L'app quindi creerà un file con alcune delle proprietà dalla nuova voce di elenco.  È ora possibile testare mediante la creazione di un nuovo elemento nell'elenco SharePoint. 
