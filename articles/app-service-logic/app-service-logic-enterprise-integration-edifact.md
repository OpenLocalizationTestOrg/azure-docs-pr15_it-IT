<properties 
    pageTitle="Integrazione di Enterprise con EDIFACT | Microsoft Azure" 
    description="Informazioni su come usare accordi EDIFACT per creare le app logica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Integrazione di Enterprise con EDIFACT 

> [AZURE.NOTE] Questa pagina illustra le caratteristiche EDIFACT di logica di App. Per informazioni su X12 fare clic [qui](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Creare un contratto EDIFACT 
È possibile scambiare messaggi EDIFACT, è necessario creare un contratto EDIFACT e archiviarlo nel proprio account di integrazione. I passaggi seguenti illustrano le fasi del processo di creazione di un contratto EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Ecco cosa occorre prima di iniziare
- Un [account di integrazione](./app-service-logic-enterprise-integration-accounts.md) definito nell'abbonamento Azure  
- Almeno due [partner](./app-service-logic-enterprise-integration-partners.md) già definiti nell'account di integrazione  

>[AZURE.NOTE]Quando si crea un contratto, il contenuto nei messaggi verrà visualizzato/invia da e verso il partner deve corrispondere al tipo di contratto.    


Dopo aver [creato un account di integrazione](./app-service-logic-enterprise-integration-accounts.md) e [aggiunto partner](./app-service-logic-enterprise-integration-partners.md), è possibile creare un contratto EDIFACT procedendo come segue:  

### <a name="from-the-azure-portal-home-page"></a>Nella home page del portale Azure

Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "Azure portale"):  
1. Selezionare **Sfoglia** dal menu a sinistra.  

>[AZURE.TIP]Se non è visibile il collegamento **Sfoglia** , potrebbe essere necessario espandere il menu prima di tutto. Eseguire questa operazione, fare clic sul collegamento **Mostra menu** che si trova in alto a sinistra del menu di scelta compresso.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Digitare *l'integrazione* nella casella di ricerca di filtro, quindi selezionare **Gli account di integrazione** dall'elenco dei risultati.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. In e il **Account integrazione** aperta, selezionare l'account di integrazione che consente di creare il contratto. Se non viene visualizzato l'account di qualsiasi integrazione elenchi, [crearne uno prima](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Selezionare il riquadro **contratti** . Se non viene visualizzato il riquadro accordi aggiungerla prima.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. Selezionare il pulsante **Aggiungi** nella e accordi visualizzata.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Immettere un **nome** per il contratto, quindi selezionare il **tipo di contratto** per EDIFACT, **Host Partner**, **Identità Host**, **Partner Guest**, **Guest identità**nell'e accordi visualizzata.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Dopo aver impostato le proprietà del contratto, selezionare **Impostazioni di ricezione** per configurare come messaggi ricevuti tramite questo contratto devono essere gestiti.  
8. Controllo impostazioni di ricezione è diviso in sezioni, inclusi gli identificatori, messaggio di conferma, schemi, numeri di controllo, convalida, impostazioni interne ed elaborazione Batch. Configurare le proprietà in base al contratto con il partner che è verrà scambiare messaggi con. Ecco una visualizzazione di questi controlli, configurarli in base a come si desidera il presente contratto per identificare e gestire i messaggi in arrivo:  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Selezionare il pulsante **OK** per salvare le impostazioni.  

### <a name="identifiers"></a>Identificatori

|Proprietà|Descrizione |
|---|---|
|UNB6.1 (riferimento destinatario Password)|Immettere un valore alfanumerico compreso tra 1 e 14 caratteri.|
|UNB6.2 (riferimento destinatario qualificatore)|Immettere un valore alfanumerico con almeno un carattere e un massimo di due caratteri.|

### <a name="acknowledgments"></a>Riconoscimenti 

|Proprietà|Descrizione |
|----|----|
|Ricezione di messaggio (CONTRL)|Selezionare questa casella di controllo per restituire una tecnica conferma (CONTRL) al mittente di interscambio. Il riconoscimento viene inviato al mittente di interscambio in base alle impostazioni per il contratto di inviare.|
|Riconoscimento (CONTRL)|Selezionare questa casella di controllo per restituire un riconoscimento funzionale (CONTRL) al mittente di interscambio il riconoscimento viene inviato al mittente di interscambio in base alle impostazioni per il contratto di inviare.|

### <a name="schemas"></a>Schemi

|Proprietà|Descrizione |
|----|----|
|UNH2.1 (TIPO)|Selezionare un tipo di set di transazione.|
|UNH2.2 (VERSIONE)|Immettere il numero di versione di messaggio. (Minimo, un carattere; massimo, tre caratteri).|
|UNH2.3 (VERSIONE FINALE)|Immettere il numero di versione finale del messaggio. (Minimo, un carattere; massimo, tre caratteri).|
|UNH2.5 (CODICE ASSEGNATO ASSOCIATO)|Immettere il codice assegnato. (Massimo, sei caratteri. Deve essere alfanumerico).|
|UNG2.1 (ID MITTENTE APP)|Immettere un valore alfanumerico con almeno un carattere e un massimo di 35 caratteri.|
|UNG2.2 (APP MITTENTE CODICE QUALIFICATORE)|Immettere un valore alfanumerico, con un massimo di quattro caratteri.|
|SCHEMA|Selezionare lo schema precedentemente caricato da usare dall'Account integrazione associato.|

### <a name="control-numbers"></a>Numeri di controllo

|Proprietà|Descrizione |
|----|----|
|Non consentire i duplicati Interchange il numero di controllo|Selezionare questa casella di controllo per bloccare svincoli duplicati. Se selezionata, l'azione decodificare EDIFACT controlla che il numero di controllo di interscambio (UNB5) per l'interscambio ricevuto non corrisponde a un numero di controllo di interscambio elaborati in precedenza. Se viene rilevata una corrispondenza, l'oggetto non viene elaborato l'interscambio.
|Verificare la presenza di duplicati UNB5 ogni (giorni)|Se si è scelto di non consentire i numeri di controllo di interscambio duplicati, è possibile specificare il numero di giorni in cui il controllo viene eseguito assegnando il valore appropriato per l'opzione **Controlla duplicati UNB5 ogni (giorni)** .|
|Non consentire i duplicati numerici controllo di gruppo|Selezionare questa casella di controllo per bloccare svincoli con numeri di controllo gruppo duplicato (UNG5).|
|Non consentire transazione set di controllo numerici duplicati|Selezionare questa casella di controllo per bloccare svincoli con numeri di controllo imposta transazione duplicata (UNH1).|
|Numero di controllo conferma EDIFACT|Per impostare i numeri di riferimento transazione set da utilizzare in un messaggio di conferma, immettere un valore per il prefisso, un intervallo di numeri di riferimento e un suffisso.|

### <a name="validations"></a>Convalida

|Proprietà|Descrizione |
|----|----|
|Tipo di messaggio|Specificare il tipo di messaggio. Di al termine, ogni riga di convalida un altro verrà aggiunti automaticamente. Se non siano presenti regole, la riga contrassegnata come predefinita viene utilizzato per la convalida.|
|Convalida EDI|Selezionare questa casella di controllo per la convalida EDI sui tipi di dati come definito dalle proprietà EDI dello schema, restrizioni di lunghezza, elementi di dati vuota e separatori finali.|
|Convalida "Extended"|Selezionare questa casella di controllo per consentire la convalida (XSD) "Extended" di svincoli ricevuto dal mittente interscambio. Convalida della lunghezza del campo, facoltativo e numero di ripetizioni oltre la convalida del tipo di dati XSD inclusi.|
|Consenti gli zeri iniziali o finali|Selezionare **Consenti** per consentire iniziali o finali zeri iniziali; **Per QueuedDeliveryRequirements** per non consentire iniziali o finali zeri iniziali o **tagliare** tagliare iniziali e finali zeri.|
|Tagliare gli zeri iniziali o finali|Selezionare questa casella di controllo per rimuovere tutti gli zero iniziali o finali|
|Criteri separatore finali|Selezionare **Non consentito** se non si desidera consentire finali delimitatori e separatori un interscambio ricevuto dal mittente interscambio. Se il servizio di interscambio contiene finali delimitatori e separatori, vengono dichiarato non validi. Selezionare **facoltativo** per accettare svincoli con o senza separatori e delimitatori finali. Selezionare **obbligatorio** se interscambio ricevuto deve contenere finali delimitatori e separatori.|

### <a name="internal-settings"></a>Impostazioni interne

|Proprietà|Descrizione |
|----|----|
|Creare tag XML vuoti se sono consentiti i separatori finali|Selezionare questa casella di controllo per includere tag XML vuoti per i separatori finali al mittente di interscambio.|
|Elaborazione batch in ingresso|Le opzioni includono:</br></br>**Dividi interscambio come transazione set - sospendere set di transazioni in caso di errore**: analizza ogni transazione impostare un interscambio in un documento XML separato applicando la busta appropriata per il set di transazione. Con questa opzione se uno o più gruppi di transazione dell'interscambio non vengono convalidati, quindi solo i set di transazioni vengono sospesi. Dividi interscambio come transazione set - sospendere interscambio errore: analizza ogni transazione impostare un interscambio in un documento XML separato applicando la busta appropriata. Con questa opzione se transazione uno o più set di interscambio la convalida non riesce, quindi l'intero interscambio verrà sospeso.</br></br>**Mantenere interscambio - sospendere set di transazioni in caso di errore**: si lascia intatto, creazione di un documento XML per l'intero interscambio in batch l'interscambio. Con questa opzione se uno o più gruppi di transazione dell'interscambio non vengono convalidati, quindi solo i set di transazioni vengono sospesi, durante l'elaborazione di tutti gli altri set di transazione.</br></br>**Mantenere interscambio - sospendere interscambio errore**: si lascia intatto, creazione di un documento XML per l'intero interscambio in batch l'interscambio. Con questa opzione se transazione uno o più set di interscambio la convalida non riesce, quindi l'intero interscambio è sospesa.|

Il presente contratto è pronto per gestire i messaggi in arrivo conformi alle impostazioni selezionate.

Per configurare le impostazioni di gestiscono i messaggi inviati per i partner:  
10. Selezionare **Le impostazioni di invio** per configurare come i messaggi inviati tramite il presente contratto devono essere gestiti.  

Controllo Impostazioni invio è diviso in sezioni, inclusi gli identificatori, riconoscimento, schemi, buste, set di caratteri separatori, numeri di controllo e convalida. 

Ecco una visualizzazione di questi controlli. Effettuare le selezioni in base a come si desidera gestire i messaggi inviati per i partner tramite il presente contratto:   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Selezionare il pulsante **OK** per salvare le impostazioni.  

### <a name="identifiers"></a>Identificatori
|Proprietà|Descrizione |
|----|----|
|UNB1.2 (versione di sintassi)|Selezionare un valore compreso tra **1** e **4**.|
|UNB2.3 (indirizzo del mittente inverso Routing)|Immettere un valore alfanumerico con un massimo di 14 caratteri e almeno un carattere.|
|UNB3.3 (Routing inverso destinatario indirizzo)|Immettere un valore alfanumerico con un massimo di 14 caratteri e almeno un carattere.|
|UNB6.1 (riferimento destinatario Password)|Immettere un valore alfanumerico con almeno una di e un massimo di 14 caratteri.|
|UNB6.2 (riferimento destinatario qualificatore)|Immettere un valore alfanumerico con almeno un carattere e un massimo di due caratteri.|
|UNB7 (ID applicazione di riferimento)|Immettere un valore con un massimo di 14 caratteri e almeno un carattere alfanumerico|

### <a name="acknowledgment"></a>Messaggio di conferma
|Proprietà|Descrizione |
|----|----|
|Ricezione di messaggio (CONTRL)|Selezionare questa casella di controllo se il partner ospitato prevede la ricezione per ricevere una tecnica conferma (CONTRL). Questa impostazione specifica che il partner di hosting, che invia il messaggio, le richieste di una risposta dal partner guest.|
|Riconoscimento (CONTRL)|Selezionare questa casella di controllo se il partner ospitato prevede la ricezione di un riconoscimento funzionale (CONTRL). Questa impostazione specifica che il partner di hosting, che invia il messaggio, le richieste di una risposta dal partner guest.|
|Generare ciclo di gruppo di archiviazione 1/SG4 per set di transazioni accettato|Se si sceglie di richiedere una conferma funzionale, selezionare questa casella di controllo per forzare la generazione di gruppo di archiviazione 1/SG4 cicli di riconoscimenti CONTRL funzionali per set di transazioni accettato.|

### <a name="schemas"></a>Schemi
|Proprietà|Descrizione |
|----|----|
|UNH2.1 (TIPO)|Selezionare un tipo di set di transazione.|
|UNH2.2 (VERSIONE)|Immettere il numero di versione di messaggio.|
|UNH2.3 (VERSIONE FINALE)|Immettere il numero di versione finale del messaggio.|
|SCHEMA|Selezionare lo schema da utilizzare. Schemi si trovano nell'account integrazione. Per accedere agli schemi, collegare l'account di integrazione logica all'applicazione in uso.|

### <a name="envelopes"></a>Buste
|Proprietà|Descrizione |
|----|----|
|UNB8 (Processing codice priorità)|Immettere un valore in ordine alfabetico che non è più di un carattere.|
|UNB10 (comunicazioni accordo)|Immettere un valore con almeno un carattere e un massimo di 40 caratteri alfanumerico.|
|UNB11 (Test indicatore)|Selezionare questa casella di controllo per indicare che il servizio di interscambio generato dati di test|
|Applicare UNA segmento (servizio stringa suggerimenti)|Selezionare questa casella di controllo per generare un segmento di dati per l'interscambio da inviare.|
|Applicare segmenti UNG (funzione gruppo intestazione)|Selezionare questa casella di controllo per creare segmenti di raggruppamento nell'intestazione del gruppo funzionale nei messaggi inviati al partner guest. Per creare segmenti UNG vengono utilizzati i valori seguenti:</br></br>Per **UNG1**, immettere un valore alfanumerico con almeno un carattere e un massimo di sei caratteri.</br></br>Per **UNG2.1**, immettere un valore alfanumerico con almeno un carattere e un massimo di 35 caratteri.</br></br>Per **UNG2.2**, immettere un valore alfanumerico, con un massimo di quattro caratteri.</br></br>Per **UNG3.1**, immettere un valore alfanumerico con almeno un carattere e un massimo di 35 caratteri.</br></br>Per **UNG3.2**, immettere un valore alfanumerico, con un massimo di quattro caratteri.</br></br>Per **UNG6**, immettere un valore alfanumerico con almeno una di e un massimo di tre caratteri.</br></br>Per **UNG7.1**, immettere un valore alfanumerico con almeno un carattere e un massimo di tre caratteri.</br></br>Per **UNG7.2**, immettere un valore alfanumerico con almeno un carattere e un massimo di tre caratteri.</br></br>Per **UNG7.3**, immettere un valore alfanumerico con almeno 1 carattere e un massimo di 6 caratteri.</br></br>Per **UNG8**, immettere un valore alfanumerico con un massimo di 14 caratteri e almeno un carattere.|

### <a name="character-sets-and-separators"></a>Set di caratteri e separatori
Diverso dal set di caratteri, è possibile immettere un set di delimitatori da utilizzare per ogni tipo di messaggio diverso. Se per uno schema di un determinato messaggio non viene specificato un set di caratteri, verrà usato il set di caratteri predefinito.

|Proprietà|Descrizione |
|----|----|
|UNB1.1 (identificatore di sistema)|Selezionare il carattere EDIFACT impostato da applicare a interscambio in uscita.|
|Schema|Selezionare uno schema dall'elenco a discesa. Di al termine ogni riga una nuova riga verrà aggiunto. Selezionare i separatori di essere utilizzato per lo schema selezionato:</br></br>**Separatore di elementi componente** : immettere un unico carattere per separare gli elementi di dati composto.</br></br>**Separatore di elementi di dati** : immettere un unico carattere per separare semplici elementi di dati all'interno degli elementi di dati composto.</br></br></br></br>**Carattere di sostituzione** : selezionare questa casella di controllo se payload dati contengono i caratteri che possono essere utilizzati anche di dati, segmento o componenti separatori. È quindi possibile immettere un carattere di sostituzione. Quando si genera il messaggio in uscita EDIFACT, tutte le istanze di caratteri separatori nei dati payload vengono sostituite con il carattere specificato.</br></br>**Terminatore segmento** : immettere un unico carattere per indicare la fine di un segmento EDI.</br></br>**Suffisso** : selezionare il carattere usato con l'identificatore di segmento. Se è possibile designare un suffisso, l'elemento di dati terminatore segmento può essere vuoto. Se il terminatore segmento è vuota, è necessario designare un suffisso.|

### <a name="control-numbers"></a>Numeri di controllo
|Proprietà|Descrizione |
|----|----|
|UNB5 (numero di controllo di interscambio)|Immettere un prefisso, un intervallo di valori per il numero di controllo di interscambio e un suffisso. Questi valori vengono utilizzati per generare un interscambio in uscita. Il prefisso e suffisso sono facoltativi. il numero di controllo è necessario. Il numero di controllo viene incrementato per ogni nuovo messaggio; il prefisso e suffisso rimangono invariati.|
|UNG5 (gruppo numero di controllo)|Immettere un prefisso, un intervallo di valori per il numero di controllo di interscambio e un suffisso. Questi valori vengono utilizzati per generare il numero di controllo di gruppo. Il prefisso e suffisso sono facoltativi. il numero di controllo è necessario. Il numero di controllo viene incrementato per ogni nuovo messaggio fino a raggiunta il valore massimo; il prefisso e suffisso rimangono invariati.|
|UNH1 (numero di riferimento intestazione del messaggio)|Immettere un prefisso, un intervallo di valori per il numero di controllo di interscambio e un suffisso. Questi valori vengono utilizzati per generare il numero di riferimento intestazione del messaggio. Il prefisso e suffisso sono facoltativi. il numero di riferimento è necessario. Viene incrementato il numero di riferimento per ogni nuovo messaggio; il prefisso e suffisso rimangono invariati.|

### <a name="validations"></a>Convalida
|Proprietà|Descrizione |
|----|----|
|Tipo di messaggio|Se si seleziona questa opzione consente la convalida del ricevitore di interscambio. Questa convalida esegue la convalida EDI sugli elementi di dati di set di transazioni, la convalida dei tipi di dati, lunghezza restrizioni e gli elementi di dati vuota e separatori di formazione.|
|Convalida EDI|Selezionare questa casella di controllo per la convalida EDI sui tipi di dati come definito dalle proprietà EDI dello schema, restrizioni di lunghezza, elementi di dati vuota e separatori finali.|
|Convalida "Extended"|Se si seleziona questa opzione consente la convalida "Extended" di svincoli ricevuto dal mittente interscambio. Convalida della lunghezza del campo, facoltativo e numero di ripetizioni oltre la convalida del tipo di dati XSD inclusi. È possibile abilitare la convalida estensione senza abilitare la convalida EDI, o viceversa.|
|Consenti gli zeri iniziali o finali|Se si seleziona questa opzione specifica un interscambio EDI ricevuto dalla parte non esito negativo convalida se un elemento di dati in un interscambio EDI non è conforme al relativo requisito a causa di lunghezza o gli spazi finali, ma è conforme i loro requisiti di lunghezza quando vengono rimossi.|
|Tagliare gli zeri iniziali o finali|Se si seleziona questa opzione verrà tagliare gli zeri iniziali e finali.|
|Separatore finali|Se si seleziona questa opzione consente di specificare un interscambio EDI ricevuto le parti non vengono convalidati se un elemento di dati in un interscambio EDI non è conforme i loro requisiti di lunghezza a causa di zeri iniziali (o finali) o spazi finali, ma è conforme i loro requisiti di lunghezza quando vengono rimossi.</br></br>Selezionare **Non consentito** se non si desidera consentire finali delimitatori e separatori un interscambio ricevuto dal mittente interscambio. Se il servizio di interscambio contiene finali delimitatori e separatori, vengono dichiarato non validi.</br></br>Selezionare **facoltativo** per accettare svincoli con o senza separatori e delimitatori finali.</br></br>Selezionare **obbligatorio** se interscambio ricevuto deve contenere finali delimitatori e separatori.|

Dopo aver selezionate **OK** e l'aperto:  
12. Selezionare il riquadro **accordi** nella e conto integrazione e verrà visualizzato il contratto appena aggiunto nell'elenco.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Ulteriori informazioni
- [Altre informazioni sull'organizzazione Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Informazioni sui Service Pack integrazione Enterprise")  
