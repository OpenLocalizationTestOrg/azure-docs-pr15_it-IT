<properties title="" pageTitle="Nomi di file e le posizioni per trovare articoli tecnici Azure" description="Viene illustrata la struttura di file per articoli e le convenzioni di denominazione che è necessario seguire quando si crea un nuovo articolo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Nomi di file e le posizioni per trovare articoli tecnici Azure

Il nostro repository contenuto tecnico, verranno utilizzate una singola cartella (cartella **articoli** ) per tutti gli articoli. Non c'è alcun gerarchia delle cartelle: tutti gli articoli live nella struttura di file flat. Se si creano cartelle con loro articoli, gli articoli non possono essere pubblicati.

Invece di usare una struttura di file come un principio organizzazione, è utilizzare una convenzione di denominazione file restrittive che identifica in modo chiaro argomenti e che contribuisce a individuabilità sul web.

Ecco cosa occorre sapere:

+ [Regole]
+ [Motivo]
+ [Esempi standard]
+ [Contenuto Marketplace]
+ [Approvazione di nome di file]

##<a name="rules"></a>Regole

- Nomi di file possono contenere solo lettere, numeri e trattini. 
- Senza spazi o caratteri di punteggiatura. Utilizzare i trattini per separare le parole e numeri nel nome del file.
- Non più di 80 caratteri - si tratta di un limite di sistema di pubblicazione
- Verbi di azione usare specifici, ad esempio sviluppano, acquistano, creano, risolvere i problemi. Non ci sono parole - un'operazione.
- Non includere alcun parole small - un oggetto e, in o, e così via.
- Tutti i file devono essere in vendite promozionali e l'estensione di file MD.
- Scrivere le parole in caso contrario. evitare l'acronimo non approvato o non necessari nei nomi di file

Acronimo e initialisms nei nomi di file - linee guida specifiche:

- Non abbreviare i nomi dei servizi Azure - le prime parole del nome del file devono essere lo standard pronunciata nome del servizio o grazie alla tecnologia di Azure. 
-   Non è consentito rm o arm come acronimo in un punto qualsiasi nel nome del file
- Altri abbreviazioni standard di settore sono accettabili in base alle esigenze nei nomi di file. 

##<a name="pattern"></a>Motivo

Ecco il modello generale:

 **Service-Platform-Language-Content-Product-Version.MD**

Utilizzare le parti del modello che si applicano ed esaminare l'elenco di articoli nel repository per avere un'idea dei nomi esistenti. Nomi che non inizino con una piattaforma sviluppatore o un nome di servizio è probabilmente sospetto e slittate tramite.

##<a name="standard-examples"></a>Esempi standard

Di seguito sono riportati alcuni esempi di nomi validi che seguono lo schema. :

- cloud-servizi-dotnet-continua-delivery.md
- dispositivi mobili-servizi-ios-get-started.md
- account.md gestire documentdb
- Mobile-Services-dotnet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.MD
- Virtual-Machines-Install-Windows-Server-2008r2.MD
- cache-aspnet--provider dello stato sessione
- Azure-SDK-dotnet-Release-Notes-2-8
- storsimple-Disaster-Recovery-using-Azure-Site-Recovery

##<a name="marketplace-content"></a>Contenuto Marketplace

Per distinguere contenuto è incentrata su contributi partner Azure Marketplace, avviare i nomi di file con "marketplace". Il contenuto non deve essere troppo comune, come la maggior parte dei contenuti partner devono essere creati nei siti web dei partner.

- Marketplace-MongoDB-Virtual-Machines-Install-Windows-Server-2008r2.MD

##<a name="file-name-approval"></a>Approvazione di nome di file

È il processo del gruppo di revisori richiesta pull per controllare i nomi dei file quando si invia un nuovo file di archivio di per la prima volta. I revisori richiesta pull devono esaminare il nome del file e fornire commenti e suggerimenti tramite il flusso di commento richiesta pull se sono necessarie modifiche. Il nome del file deve essere corretti prima che venga accettato dall'utente la richiesta di pull. Collaboratori possono inserire facilmente l'aggiornamento alla richiesta di pull in sospeso.

##<a name="folder-names-in-the-repo"></a>Nomi delle cartelle di repo

Cartelle devono essere create solo per i servizi e il nome del file deve corrispondere indicazioni del servizio. Utilizzare solo lettere e trattini e lettere minuscole. Ottenere l'approvazione l'amministratore dell'archivio prima di creare una nuova cartella non associata a un servizio finale.

##<a name="changing-case-in-file-names"></a>Modifica maiuscole/minuscole nei nomi di file

Sistemi operativi Windows sono maiuscole e minuscole, pertanto se è necessario modificare un nome di file per la risoluzione di maiuscole e minuscole, si consiglia di apportare modifiche sostanziali, a meno che non è possibile apportare le modifiche in un Linux o Mac. Per esempio:

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Per rinominare un file, usare il comando seguente:
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Collegamenti di Guida dei collaboratori

- [Articolo di panoramica](./../README.md)
- [Indice degli articoli di Guida](./contributor-guide-index.md)


<!--Anchors-->
[Regole]: #rules
[Motivo]: #pattern
[Esempi standard]: #standard-examples
[Contenuto Marketplace]: #marketplace-content
[Approvazione di nome di file]: #file-name-approval
