<properties pageTitle="Fra i comandi per creare un nuovo articolo o aggiornare un articolo esistente" description="Procedura per l'utilizzo di Azure tecnico contenuto archivi GitHub." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Fra i comandi per creare un nuovo articolo o aggiornare un articolo esistente


## <a name="standard-process-working-from-master"></a>Processo standard (uso dallo schema)
Seguire i passaggi descritti in questo articolo per creare una diramazione di lavoro locale nel computer in modo che è possibile creare un nuovo articolo per la sezione di documentazione tecnica di azure.microsoft.com o aggiornare un articolo esistente.

1. Avviare fra Bash (o lo strumento della riga di comando da utilizzare per fra).

 **Nota:** Se utilizza l'archivio pubblico, modificare azure-contenuto-prezzo per il contenuto di azure in tutti i comandi.

2. Modificare al prezzo di contenuto azure:

        cd azure-content-pr
3. Estrarre ramo master:

        git checkout master

4. Creare una diramazione di lavoro locale aggiornato derivata dalla diramazione master:

        git pull upstream master:<working branch>


5. Spostare nel nuovo ramo di lavoro:

        git checkout <working branch>

6. Comunicare la divisione che è stato creato il ramo di lavoro locale:

        git push origin <working branch>

7. Creare il nuovo articolo o si apportano modifiche a un articolo esistente. Usare Esplora risorse per aprire e creare nuovi file delle vendite promozionali e utilizzare Atom (http://atom.io) come editor delle vendite promozionali. Dopo aver creato o modificato l'articolo e le immagini, passare alla sezione successiva.

8. Aggiungere e salvare le modifiche apportate:

        git add .
        git commit –m "<comment>"
        
   In alternativa, è possibile aggiungere solo le specifiche del file è stata modificata:

        git add <file path>
        git commit –m "<comment>"

   Se è stato eliminato file, è necessario utilizzare questo comando:
   
        git add --all
        git commit -m "<comment>"

9. Aggiornare la diramazione di lavoro locale con le modifiche padre:

        git pull upstream master

10. Rendere disponibili le modifiche per la divisione in GitHub:

        git push origin <working branch>

12. Quando si è pronti a inviare il contenuto per il ramo master padre per di gestione temporanea, convalida, e/o la pubblicazione, in UI GitHub, creare una richiesta di pull dalla divisione per ramo master.

13. Se si è un dipendente lavorando repository privato, le modifiche per inviare automaticamente installazione e un collegamento di gestione temporaneo scritto alla richiesta di pull. Esaminare il contenuto a fasi e l'accesso i commenti di richiesta di pull aggiungendo il commento **#sign-disattivare** .  Indica che le modifiche sono pronte per essere inserito live.  Se non si vuole la richiesta di pull devono essere accettati - se si decide solo le modifiche, aggiungere la nota **#hold-off** alla richiesta di pull.

14. Accettazione di richiesta pull esamina la richiesta di pull, fornisce feedback e/o accetta la richiesta di pull. 

15. Se lo si desidera verificare l'articolo pubblicato o le modifiche

 http://Azure.microsoft.com/Documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Pubblicazione

- Articoli sono disponibili all'indirizzo circa 10:00:00 e 3:00 PM Pacifico, dal lunedì al venerdì. Può richiedere fino a 30 minuti per trovare articoli visualizzare online dopo la pubblicazione. Tenere presente la richiesta di pull da unire da un revisore richiesta pull prima di poter includere le modifiche in successiva pubblicazione eseguita. È necessario utilizzare con il revisore richiesta pull anticipo per garantire che una richiesta di pull unita per una specifica pubblicazione eseguita. In caso contrario, PRs vengono esaminate nell'ordine in cui sono stati inviati.

- Se si è un dipendente che lavora nell'archivio privato, tutte le richieste di pull sono soggetti a regole di convalida che dovranno essere risolti prima che la richiesta di pull può essere unita. 

## <a name="working-with-release-branches"></a>Utilizzo di diramazioni release

Quando si lavora con una diramazione della versione, il modo migliore per creare una diramazione di lavoro locale da ramo della versione è utilizzare la sintassi seguente comando:

    git checkout upstream/<upstream branch name> -b <local working branch name>

In questo modo si crea la filiale locale direttamente dal ramo padre, evitando qualsiasi unione locale.

