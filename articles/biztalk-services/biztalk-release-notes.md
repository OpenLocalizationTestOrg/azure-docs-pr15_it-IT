<properties
    pageTitle="Note sulla versione per i servizi BizTalk Azure | Servizi di BizTalk Microsoft Azure"
    description="Vengono descritti i problemi noti per i servizi BizTalk Azure" 
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

# <a name="release-notes-for-azure-biztalk-services"></a>Note sulla versione per i servizi BizTalk Azure

Note sulla versione di Microsoft Azure BizTalk Services contengono problemi noti di questa versione.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Quali sono le novità nell'aggiornamento di novembre di BizTalk Services
* È possibile attivare la crittografia inattivi nel portale servizi BizTalk. Vedere [abilitare la crittografia inattivi nel portale di servizi BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Cronologia aggiornamenti

### <a name="october-update"></a>Aggiornamento di ottobre

* Account dell'organizzazione sono supportati:  
 * **Scenario**: la registrazione di una distribuzione BizTalk Service usando un account Microsoft (ad esempio user@live.com). In questo scenario, solo gli utenti di Account Microsoft possono gestire BizTalk Service tramite il portale di servizi BizTalk. Non è possibile usare un account dell'organizzazione.  
 * **Scenario**: la registrazione di una distribuzione BizTalk Service utilizzando un account dell'organizzazione in Azure Active Directory (ad esempio user@fabrikam.com o user@contoso.com). In questo scenario, solo gli utenti di Azure Active Directory nella stessa organizzazione possono gestire BizTalk Service tramite il portale di servizi BizTalk. Non è possibile usare un account Microsoft.  
* Quando si crea un BizTalk Service nel portale di classica Azure, vengono registrati automaticamente nel portale servizi BizTalk.
 * **Scenario**: accedere al portale di classica Azure, creare BizTalk Service e quindi scegliere **Gestione** per la prima volta. Quando si apre il portale di servizi BizTalk, BizTalk Service automaticamente registra ed è pronta per le distribuzioni.  
 Vedere [la registrazione e aggiornamento di una distribuzione di servizio BizTalk nel portale di servizi di BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Aggiornamento di agosto 14
* Contratto e bridge di separazione: commerciali accordi partner e ponti sono ora disaccoppiati nel portale servizi BizTalk. È ora creare contratti e ponti separatamente e in fase di esecuzione ponti risolvono un accordo in base ai valori nel messaggio EDI. Vedere [Creare accordi in servizi di Windows Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689908.aspx), [creare un bridge EDI nel portale servizi BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Crea un bridge AS2 nel portale servizi BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx)e [come ponti risolvono i contratti in fase di esecuzione?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* L'opzione per creare modelli per i contratti viene interrotta.  
* Per il contratto di lato Invia, è ora possibile specificare gli insiemi di delimitatore diversi per ogni schema. Questa configurazione viene specificata nelle impostazioni di protocollo per Invia lato contratto. Per ulteriori informazioni, vedere [creazione di un' X12 contratto di servizi di Windows Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [creare un accordo EDIFACT in servizi di Windows Azure BizTalk](https://msdn.microsoft.com/library/azure/dn606267.aspx). Due nuove entità aggiunti anche all'API OM TPM per lo stesso scopo. Vedere [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) ed [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Costrutti XSD standard, inclusi i tipi di derivato, sono ora supportati. Vedere [costrutti XSD standard di usare le mappe](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [Usare i tipi di derivato in scenari Mapping ed esempi](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 supporta nuovi algoritmi del microfono per firmare i messaggi e nuovi algoritmi di crittografia. Vedere [creare un accordo AS2 in servizi BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Problemi noti

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemi di connettività dopo BizTalk Services aggiornamento portale

  Se è aperto il portale di servizi BizTalk durante l'aggiornamento di BizTalk Services per le trasmissioni le modifiche apportate al servizio, si potrebbero venire problemi di connettività con il portale di servizi BizTalk.  
  In alternativa, si può riavviare il browser, eliminare la cache del browser oppure ad avviare il portale in modalità privata.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE Visual Studio non è possibile individuare l'elemento se si fa clic su un errore o un avviso in un progetto BizTalk Services
Installare Visual Studio 2012 Update 3 RC 1 per risolvere il problema.  

### <a name="custom-binding-project-reference"></a>Guida di riferimento project associazione personalizzata
Valutare la possibilità di un progetto BizTalk Services in una soluzione Visual Studio situazioni seguenti:  
* Nella stessa soluzione Visual Studio, è un progetto BizTalk Services e un progetto di associazione personalizzata. Il progetto BizTalk Service contiene un riferimento a questo file di progetto di associazione personalizzata.
* Il progetto BizTalk Service contiene un riferimento a un'associazione/comportamento personalizzato DLL.

Si 'genera' la soluzione in Visual Studio correttamente. Successivamente, 'Ricostruisci' o pulire la soluzione. Successivamente, quando si rigenerare o pulire nuovamente, si verifica l'errore seguente:  
  Impossibile copiare il file <Path to DLL> a "bin\Debug\FileName.dll". Il processo non è possibile accedere al file 'bin\Debug\FileName.dll' perché utilizzato da un altro processo.  

#### <a name="workaround"></a>Soluzione alternativa
* Se è installato [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) , sono disponibili due opzioni:

  * Riavviare Visual Studio, o

  * Riavviare la soluzione. Eseguire quindi solo una generazione sulla soluzione.  

* Se non è installato [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) , aprire Task Manager, fare clic sulla scheda processi, fare clic su processo MSBuild.exe e quindi fare clic sul pulsante Termina processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routing alle BasicHttpRelay endpoint non è supportato dal portale di servizi BizTalk e ponti se caratteri non stampabili vengono trasferiti come intestazioni HTTP

Se si usa caratteri non stampabili come parte delle proprietà alzati di livello per i messaggi, i messaggi non possono essere instradati a destinazioni inoltro che utilizzano l'associazione BasicHttpRelay. Inoltre, le proprietà alzati di livello che sono disponibili come parte della verifica siano con codifica URL per BLOB e non codificati per le destinazioni.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN viene inviato in modo asincrono anche se è selezionata l'opzione MDN asincrono invia  
Valutare la possibilità di questo scenario: se si seleziona la casella di controllo **Invia MDN asincrono** e specificare un URL per l'invio asincrono MDN a e quindi deselezionare la casella di controllo **Invia MDN asincrono** nuovamente il MDN viene comunque inviato all'URL specificato anche se non è selezionata l'opzione per inviare MDNs asincrono.  
In alternativa, è necessario deselezionare l'URL specificato prima deselezionando la casella di controllo **Invia MDN asincrono** e quindi distribuire il contratto AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Spazi oltre un interscambio valido generare un messaggio vuoto devono essere inviati l'endpoint di sospensione  
Se sono presenti spazi oltre un segmento IEA, il disassembler considerato come fine corrente di interscambio ed esamina il set di spazi come messaggio successivo successivo. Poiché non si tratta di interscambio valido, è possibile osservare che una corretta del messaggio viene inviato alla destinazione route e un messaggio vuoto viene inviato l'endpoint di sospensione.  
### <a name="tracking-in-biztalk-services-portal"></a>Verifica nel portale di servizi BizTalk  
Eventi di rilevamento vengono acquisiti massimo l'elaborazione del messaggio EDI e qualsiasi correlazione. Se un messaggio non riesce di fuori fase protocollo, verifica verrà visualizzato come completato. In questo caso, vedere la sezione LOG nella colonna **Dettagli** per **tenere traccia** per dettagli sull'errore.
Il X12 impostazioni di invio e la ricezione ([creazione di un' X12 contratto di servizi di Windows Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornire informazioni sul passaggio protocollo.  

### <a name="update-agreement"></a>Contratto di aggiornamento  
Il portale di servizi BizTalk consente di modificare il qualificatore di un'identità quando è configurato un contratto. Questo può comportare proprietà incoerente. È ad esempio un contratto utilizzando ZZ:1234567 e ZZ:7654321 qualificatore. Impostazioni del profilo portale servizi BizTalk è modificare ZZ:1234567 per essere 01:ChangedValue. Si apre il contratto e viene visualizzato 01:ChangedValue anziché ZZ:1234567.
Per modificare il qualificatore di un'identità, eliminare il contratto, aggiornare **identità** nel profilo partner e quindi ricreare il contratto.  
> AZURE. AVVISO ciò influisce su X12 e AS2.  

### <a name="as2-attachments"></a>Allegati AS2  
Gli allegati dei messaggi non sono supportati in AS2 inviare o ricevano. In particolare, gli allegati vengono ignorati e il corpo del messaggio viene elaborato come messaggio AS2 normale.  
### <a name="resources-remembering-path"></a>Risorse: Percorso di memorizzazione  
Quando si aggiungono **risorse**, la finestra di dialogo potrebbe non ricordare il percorso usato in precedenza per aggiungere una risorsa. Per ricordare il percorso usato in precedenza, provare ad aggiungere il sito web portale servizi BizTalk **Siti attendibili** in Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se si rinomina il nome dell'entità di un bridge e chiudere il progetto senza salvare le modifiche, aprire nuovamente l'entità genera un errore
Valutare la possibilità di uno scenario nell'ordine seguente:  
* Aggiungere un bridge (ad esempio un Bridge unidirezionale XML) a un progetto BizTalk Service  

* Rinominare il bridge specificando un valore per la proprietà Name entità. Consente di rinominare il file .bridgeconfig associato con il nome specificato.  

* Chiudere il file .bcs (in base alla chiusura della scheda in Visual Studio) senza salvare le modifiche.  

* Aprire nuovamente il file .bcs da Esplora soluzioni.  
Si noterà che il file .bridgeconfig associata include il nuovo nome specificato, il nome dell'entità nella finestra di progettazione sia ancora il nome precedente. Se si tenta di aprire la configurazione del Bridge facendo doppio clic sul componente bridge, viene visualizzato l'errore seguente:  
  '<old name>'Entità associato del file'<old name>.bridgeconfig' non esiste  
Per evitare l'esecuzione in questo scenario, accertarsi di salvare le modifiche dopo la ridenominazione di entità in un progetto BizTalk Service.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Progetto BizTalk Service venga compilato correttamente anche se un elemento è stato escluso da un progetto di Visual Studio
Valutare la possibilità di uno scenario in cui si aggiunge un elemento (ad esempio, un file XSD) a un progetto BizTalk Service, includere l'elemento in questione la configurazione del Bridge (ad esempio possibile specificare l'account come un tipo di messaggio di richiesta) e quindi escludere dal progetto di Visual Studio. In questo caso, la compilazione del progetto non otterranno gli errori come elemento eliminato è disponibile su disco nella stessa posizione in cui è stato incluso nel progetto di Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk Service per il progetto non verifica la disponibilità di schema durante la configurazione di ponti
In un progetto BizTalk Service se uno schema che viene aggiunto al progetto importato in un altro schema, il progetto BizTalk Service non controlla se lo schema importato viene aggiunta al progetto. Se si tenta di compilazione di un progetto, viene visualizzato non gli errori di generazione.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Il messaggio di risposta per un Bridge di richiesta di risposta XML è sempre di set di caratteri UTF-8
In questa versione, il set di caratteri del messaggio di risposta da un Bridge di richiesta di risposta XML è sempre impostata su UTF-8.
### <a name="user-defined-datatypes"></a>Tipi di dati definito dall'utente
Le schede BizTalk scheda Pack all'interno della feature servizio scheda BizTalk possono utilizzare tipi di dati definito dall'utente per le operazioni di scheda.
Quando si usano tipi di dati definito dall'utente, copiare i file (. dll) a unità: \Programmi\Microsoft BizTalk scheda Service\BAServiceRuntime\bin\ o per la Cache (assembly globale) del server che ospita BizTalk scheda servizio. In caso contrario, il seguente errore può verificarsi sul client:  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Si consiglia di utilizzare GACUtil.exe per installare un file nella Cache Assembly globale. GACUtil.exe viene illustrato come utilizzare questo strumento e le opzioni della riga di comando Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Riavviare il sito Web di BizTalk scheda Service
Installazione del **Runtime del servizio scheda BizTalk** * Crea la * *Servizio scheda BizTalk* * sito web IIS che contiene il * *BAService* * dell'applicazione.* *BAService** applicazione internamente utilizza l'associazione di inoltro per estendere la portata dell'endpoint del servizio di locale nel cloud. Per un servizio ospitato in locale, l'endpoint di inoltro corrispondente verrà registrato bus servizio solo all'avvio del servizio locale.  

Se si interrompe e avviare un'applicazione, la configurazione per l'avvio automatico di un'applicazione non è valido. Pertanto quando viene interrotto **BAService** , è necessario sempre riavviare il sito web **Servizio scheda BizTalk** invece. Non avviare o arrestare l'applicazione **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Caratteri speciali non dovrebbero essere usati per indirizzo ed entità i nomi dei componenti line
Utilizzare i caratteri speciali non per i nomi di indirizzo ed entità dei componenti line. In tal caso, verrà visualizzato un messaggio di errore durante la distribuzione del progetto BizTalk Service. Per alcuni caratteri, ad esempio "%", il sito Web servizio scheda BizTalk potrebbe passare a un'arrestato e sarà necessario avviarlo manualmente.
### <a name="test-map-with-get-context-property"></a>Mappa di test con proprietà di contesto Get
Se una trasformazione contiene un'operazione di mapping di **Proprietà di contesto Get** , **Test mappa** avrà esito negativo. Per ovviare al temporaneo, sostituire l'operazione di mappa **Proprietà contesto Get** con operazione mappa concatenare una stringa contenente dati fittizi. Per popolare lo schema di destinazione e che testare altre funzionalità di trasformazione.
### <a name="test-map-property-does-not-display"></a>Test mapping proprietà non vengono visualizzati
Le proprietà di **Test mappa** non sono visualizzati in Visual Studio. Questo può verificarsi se la finestra **proprietà** e la finestra **Esplora** non sono ancorate contemporaneamente. Per risolvere questo problema, ancorare le **proprietà** e le finestre di **Esplora soluzioni** .  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Riepilogo DateTime riformattare è disattivato
Quando un'operazione di mapping riformattare DateTime viene aggiunto all'area di progettazione e configurata, l'elenco a discesa formato potrebbe essere visualizzato in grigio. Questo problema può verificarsi se il computer visualizzazione è impostato su **Media-125%** o **grande-150%**. Per risolvere il problema, impostare la visualizzazione **più piccolo-100% (impostazione predefinita)** eseguendo la procedura seguente:  
1. Aprire il **Pannello di controllo** e fare clic su **aspetto e personalizzazione**.
2. Fare clic su **Visualizza**.
3. Fare clic su **più piccolo-100% (impostazione predefinita)** e fare clic su **Applica**.

Casella di riepilogo a discesa **formato** dovrebbe ora funzionare come previsto.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicare contratti nel portale servizi BizTalk
Considerare le seguenti condizioni:
1. Creare un contratto utilizzando Trading API OM di gestione dei Partner.
2. Aprire il contratto nel portale di servizi BizTalk in due schede diverse.
3. Distribuire il contratto da entrambe le schede.
4. Di conseguenza, entrambi i contratti vengono distribuiti risultante in voci duplicate nel portale servizi BizTalk

**Soluzione alternativa**. Aprire uno qualsiasi dei contratti duplicati nel portale servizi BizTalk e annullare la distribuzione.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Ponti non utilizzano certificato aggiornato anche dopo l'aggiornamento di un certificato nell'archivio di elementi
Considerare gli scenari seguenti:  

**Scenario 1: Utilizzo dei certificati basato su identificazione personale per proteggere il trasferimento di messaggi da un bridge a un endpoint del servizio**  
Valutare la possibilità di uno scenario in cui si usa certificati basati su identificazione personale di un progetto BizTalk Service. Aggiornare il certificato nel portale servizi BizTalk con lo stesso nome ma un'identificazione personale diversa, ma non si aggiorna il progetto BizTalk Service conseguenza. In questo caso, il bridge può continuare a elaborare i messaggi di dati del certificato precedenti potrebbero essere ancora nella cache del canale. Successivamente, si verifica un errore di elaborazione dei messaggi.  

**Soluzione**: aggiornare il certificato nel progetto BizTalk Service e ridistribuire il progetto.  

**Scenario 2: Uso dei comportamenti basato sul nome per identificare i certificati per la protezione di trasferimento di messaggi da un bridge a un endpoint del servizio**

Valutare la possibilità di uno scenario in cui si usa il comportamento del basato sul nome per identificare i certificati di un progetto BizTalk Service. Aggiornare il certificato nel portale servizi BizTalk, ma non vengono aggiornate progetto BizTalk Service conseguenza. In questo caso, il bridge può continuare a elaborare i messaggi di dati del certificato precedenti potrebbero essere ancora nella cache del canale. Successivamente, si verifica un errore di elaborazione dei messaggi.  

**Soluzione**: aggiornare il certificato nel progetto BizTalk Service e ridistribuire il progetto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Continuano a ponti elaborare i messaggi anche quando il database SQL è offline
Ponti servizi BizTalk continuano elaborare i messaggi per un po' di tempo, anche se il Database di SQL Azure Microsoft (che archivia le informazioni in esecuzione come elementi distribuiti e pipeline), è offline. In questo modo servizi BizTalk utilizza gli elementi memorizzati nella cache e configurazione del bridge.
Se non si desidera ponti per elaborare tutti i messaggi quando il Database di SQL è offline, è possibile utilizzare i cmdlet di PowerShell servizi BizTalk interrompere o sospendere BizTalk Service. Vedere [Azure BizTalk di esempio di un codice di gestione](http://go.microsoft.com/fwlink/p/?LinkID=329019) per i cmdlet di Windows PowerShell per gestire le operazioni.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Leggere il messaggio XML nel componente di codice personalizzato del bridge include un carattere DB aggiuntivo
Valutare la possibilità di uno scenario in cui si vuole leggere un messaggio XML all'interno di codice personalizzato del bridge. Se si usa il System.Text.Encoding.UTF8.GetString(bytes) API .NET un carattere DB è inclusa nell'output all'inizio del messaggio. Pertanto, se non si desidera l'output per includere il carattere DB aggiuntivo, è necessario utilizzare ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Invio di messaggi da un bridge utilizzando WCF non adatta
I messaggi inviati a un bridge utilizzando WCF non è adatta. Se si desidera che un client scalable, è necessario utilizzare invece HttpWebRequest.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>AGGIORNAMENTO: Errore del Provider di Token dopo l'aggiornamento da BizTalk Services Preview alla disponibilità generale (GA)
Esiste un contratto di AS2 o EDI con batch attivo. Quando BizTalk Service viene aggiornata dalla versione di anteprima a GA, possono verificarsi le operazioni seguenti:
* Errore: Il provider di token non è in grado di fornire un token di sicurezza. Provider di token restituito messaggio: non è possibile risolvere il nome remoto.

* Attività batch vengono annullate.

**Soluzione alternativa**: dopo il servizio BizTalk viene aggiornato alla disponibilità generale (GA), ridistribuire il contratto.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>AGGIORNAMENTO: Casella degli strumenti sono illustrate le icone bridge precedente dopo l'aggiornamento di BizTalk Services SDK
Dopo l'aggiornamento di una versione precedente di BizTalk Services SDK, che aveva vecchie icone che rappresentano i ponti, la casella degli strumenti continua a visualizzare le icone precedente per i Bridge. Tuttavia, se si aggiunge un collegamento all'area di progettazione progetto BizTalk Service, nell'area Mostra la nuova icona.  

**Soluzione alternativa**. Per risolvere il problema eliminando i file .tbd in <system drive>: \Users\<utente > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>AGGIORNAMENTO: Aggiornare portale BizTalk dalla versione di anteprima per GA risulti incluso un messaggio di errore che indica che la funzionalità EDI non è disponibile
Se si è connessi al portale di servizi BizTalk mentre i servizi BizTalk è effettuato l'aggiornamento dalla versione di anteprima a GA, potrebbe essere visualizzato l'errore seguente nel portale:  

Questa funzionalità non è disponibile come parte di questa edizione di Microsoft Azure BizTalk Services. Per usare queste funzionalità passare a un'edizione di appropriata.  

**Risoluzione**: disconnessione dal portale, Chiudi e Apri il browser, quindi accedere al portale.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>AGGIORNAMENTO: Verifica nuovi dati non viene visualizzato dopo i servizi BizTalk viene aggiornato a GA
Si supponga uno scenario in cui si dispone di un bridge di XML distribuito nell'abbonamento a servizi BizTalk Preview. Inviare messaggi al bridge e corrispondente la traccia dei dati è disponibile nel portale di servizi di BizTalk. A questo punto, se i componenti di runtime portale servizi BizTalk e BizTalk Services vengono convertiti in GA e si invia un messaggio allo stesso endpoint bridge distribuito in precedenza, i dati di verifica non viene visualizzato per i messaggi inviati dopo l'aggiornamento.  

### <a name="pipelines-vs-bridges"></a>Pipeline v/s ponti
In questo documento, il termine "pipeline" e 'ponti' vengono utilizzati in modo intercambiabile. Entrambe essenzialmente lo stesso significato, ossia, un'unità di elaborazione messaggio distribuita sui servizi BizTalk.  

### <a name="concepts"></a>Concetti  

[Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
