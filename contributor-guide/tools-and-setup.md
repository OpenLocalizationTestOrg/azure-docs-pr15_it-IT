<properties
pageTitle="Installare e configurare gli strumenti per la creazione di GitHub"
description="Strumenti e i passaggi di impostazione per la creazione di Azure contenuto in GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Installare e configurare gli strumenti per la creazione di GitHub

Seguire i passaggi descritti in questo articolo per impostare gli utensili per contribuire alla documentazione tecnica di Azure. Collaboratori informali e occasionali probabile che sia possono usare GitHub descritto nel passaggio 2 dell'interfaccia utente.

Se si ha familiarità con fra, è necessario verificare la terminologia fra: [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). Inoltre, questo thread StackOverflow contiene un glossario dei termini di operazioni che si incontrano in questa serie di passaggi: [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Contenuto

- [Creare un account GitHub e configurare il proprio profilo](#create-a-github-account-and-set-up-your-profile)
- [Iscriversi a Disqus](#sign-up-for-disqus)
- [Determinare se è davvero necessario seguire questi passaggi](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Autorizzazioni in GitHub](#permissions-in-github)
- [Installare fra per Windows](#install-git-for-windows)
- [Abilitare l'autenticazione a due fattori](#enable-two-factor-authentication)
- [Installare un editor delle vendite promozionali](#install-a-markdown-editor)
- [Configurare Atom](#configure-atom)
- [Divisione repository e copiarla nel computer in uso](#fork-the-repository-and-copy-it-to-your-computer)
- [Configurare il nome utente e la posta elettronica in locale](#configure-your-user-name-and-email-locally)
- [Passaggi successivi](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Creare un account GitHub e configurare il proprio profilo

Per contribuire al contenuto tecnico Azure, è necessario un account [GitHub](http://www.github.com) .

Se si è un collaboratore Microsoft, è necessario configurare l'account GitHub in modo che sta chiaramente come un dipendente Microsoft. Configurare il proprio profilo come indicato di seguito:

- **Immagine del profilo**: un'immagine della (obbligatorio)
- **Nome**: il nome e cognome (obbligatorio)
- **Messaggio di posta elettronica**: l'indirizzo di posta elettronica di Microsoft (facoltativo)
- **Società**: Microsoft Corporation (obbligatorio)
- **Posizione**: elenco la posizione dell'utente (facoltativa)

Il profilo dovrebbe essere simile a questo profilo:

<p align="center">
 ![Esempio di profilo GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Iscriversi a Disqus

Ogni articolo tecnico Azure pubblicato ha un flusso di commento fornito dal servizio Disqus.

 ![Logo di disco](./media/tools-and-setup/discus.png)

Se ci si trova un dipendente Microsoft e si è l'autore o un collaboratore a un articolo, è necessario iscriversi a Disqus in modo che è possibile partecipare lo Stream del commento per l'articolo.

1. Iscriversi a un account presso [http://www.disqus.com/](http://www.disqus.com/)
2. Compilare il profilo come indicato di seguito:

 - **Nome completo**: il nome completo, come visualizzato l'elenco di libro indirizzo Microsoft, nonché informazioni tra parentesi quadre, che rappresenta l'alias più @MSFT. Formato: *primo ultimo [alias@MSFT] *
 - **Posizione**: la posizione dell'utente
 - **La breve**: il titolo

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Determinare se è davvero necessario seguire questi passaggi

Potrebbe non essere necessario eseguire tutti i passaggi descritti in questo articolo. Dipende dal tipo di contenuto contributo utile o necessario rendere.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Inviare una modifica solo il testo a un articolo esistente

Se è necessario o solo per apportare gli aggiornamenti di testuali a un articolo esistente, probabilmente non è necessario seguire i passaggi rimanenti. È possibile utilizzare editor basato sul web delle vendite promozionali di GitHub per inviare le modifiche apportate. Fare clic sul collegamento GitHub vedere l'articolo che si desidera modificare:

 ![Esempio di profilo GitHub](./media/tools-and-setup/contributetogit.png)

 Quindi fare clic sull'icona edit nella versione GitHub dell'articolo

 ![Esempio di profilo GitHub](./media/tools-and-setup/editicon.PNG)

 Che apre l'editor di semplice utilizzo web che semplifica l'individuazione di inviare le modifiche. Non è necessario eseguire altri passaggi in questo articolo.

###<a name="all-other-changes"></a>Tutte le altre modifiche
UI GitHub supporta la creazione di nuovi file e trascinamento della selezione immagini. Tuttavia, quando si lavora nell'interfaccia utente, gestione dei rami può essere confusione in modo che in genere consigliabile installare gli strumenti e i comandi per creare e gestire gli articoli di informazioni. Se si desidera utilizzare l'interfaccia utente, vedere:

- [Creazione di file in Github](https://github.com/blog/1327-creating-files-on-github)
- [Caricare file in elenco repository](https://github.com/blog/2105-upload-files-to-your-repositories)

Per gli ordinamenti seguenti del lavoro, è consigliabile installare e informazioni su come usare gli strumenti:

 - Apportare modifiche significative a un articolo
 - Creare e pubblicare un nuovo articolo
 - Aggiunta di nuove immagini o aggiornamento di immagini
 - Aggiornamento di un articolo per un periodo di giorni che vanno senza pubblicazione modifiche ogni di tali giorni
 - Creazione di contenuto per una versione che deve attivare un determinato giorno di una determinata ora

##<a name="permissions-in-github"></a>Autorizzazioni in GitHub

Chiunque disponga di un account GitHub può contribuire al contenuto tecnico Azure attraverso il nostro archivio pubblico in [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Nessuna autorizzazione speciale sono necessaria.

Se ci si trova un PM Microsoft o writer che stanno lavorando Azure di contenuto, è necessario il nostro privato repository del contenuto, prezzo di contenuto azure. Visitare [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) per richiedere le autorizzazioni di lettura che consentiranno di contributi tramite repo privato - accedere a GitHub con il pulsante > fare clic su Azure > fare clic su **Partecipa a un team** o **partecipare a un altro team**e quindi cercare e partecipare al gruppo di **azure contenuto lettura** .

## <a name="install-git-for-windows"></a>Installare fra per Windows

Installare fra per Windows da [http://git-scm.com/download/win](http://git-scm.com/download/win). Il download installa il sistema di controllo delle versioni fra e viene installato fra Bash, dell'applicazione della riga di comando che verrà utilizzato per interagire con l'archivio fra locale.

È possibile accettare le impostazioni predefinite. Se si vuole i comandi disponibili all'interno della riga di comando di Windows, selezionare l'opzione che consente a tale.

<p align="center">
 ![Esempio di profilo GitHub](./media/tools-and-setup/gitbashinstall.png)

(Nota: questa non è diverso da quello di "Github per Windows". "Github per Windows" è uno strumento basato su grafica diversi che funziona anche se si desidera documentarsi se stessi. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Abilitare l'autenticazione a due fattori

È necessario abilitare l'autenticazione a due fattori (2FA) nel proprio account GitHub se si utilizza l'archivio di contenuto privato. È necessario nell'archivio privato.

A tale scopo, seguire le istruzioni in entrambi gli GitHub argomenti seguenti:

- [Informazioni su autenticazione a due fattori](https://help.github.com/articles/about-two-factor-authentication/)
- [Creazione di un token di accesso per l'utilizzo della riga di comando](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Quando si crea il token, selezionare tutti gli ambiti disponibili nell'interfaccia utente per la creazione di token ([informazioni dettagliate su ciascun ambito](https://developer.github.com/v3/oauth/#scopes))

Dopo aver attivato 2FA, è necessario immettere il token di accesso anziché la password GitHub al prompt dei comandi quando si tenta di accedere a un repository GitHub dalla riga di comando. Il token di accesso non è il codice di autenticazione che viene visualizzato in un messaggio di testo quando si configura 2FA. Si tratta di una stringa lunga che ha un aspetto simile al seguente: fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Alcune note:

- Quando si creano i token di accesso, salvarlo in un file di testo per renderla accessibile quando è necessario.

- In un secondo momento, quando si desidera incollare il token, è utile sapere esistono due modi per incollare nella riga di comando:

 - Fare clic sull'icona nell'angolo superiore sinistro della finestra della riga di comando > Modifica > Incolla.
 - Pulsante destro del mouse sull'icona nell'angolo superiore sinistro della finestra e fare clic su proprietà > Opzioni > modalità Modifica rapida. In questo modo la riga di comando in modo che è possibile incollare facendo clic nella finestra della riga di comando.

## <a name="install-a-markdown-editor"></a>Installare un editor delle vendite promozionali

Si modifica il contenuto utilizzando la notazione di semplice "delle vendite promozionali" nel file, anziché più complesso "margine di profitto" (HTML, XML e così via). Pertanto, è necessario installare un editor delle vendite promozionali.

- **Atom**: la maggior parte delle Aiutaci a utilizzare l'editor di vendite promozionali Atom del GitHub: [http://atom.io](http://atom.io). Non richiede una licenza per l'utilizzo di business. Ha il controllo ortografico.

- **Il blocco note**: È possibile utilizzare il blocco note di una soluzione molto leggera.

- **Riportato**: si tratta di un editor delle vendite promozionali leggera, eleganti, in linea e Apri origine che offre un'anteprima. Visitare [http://prose.io](http://prose.io) e autorizzare nel testo del repository.

- **[Codice di visual Studio](https://www.visualstudio.com/products/code-vs.aspx)** - voce Microsoft in questa casella.

## <a name="configure-atom"></a>Configurare Atom

Se si usa Atom, è necessario configurare alcuni aspetti.

- Atom è per impostazione predefinita 2 spazi per le schede, ma delle vendite promozionali prevede 4 spazi. Se si mantiene l'impostazione predefinita di due, l'articolo sarà l'aspetto accattivante nell'anteprima locale, ma non quando viene importato in Azure. Configurare e quindi Atom per utilizzare gli 4 spazi - è possibile trovare questa impostazione in File > Impostazioni > impostazioni dell'Editor > la lunghezza delle tabulazioni.
- È anche da attivare a capo contorni in questa sezione, che indica lo stesso "capo" nel blocco note.
- Per attivare l'anteprima delle vendite promozionali, fare clic su pacchetti > Anteprima delle vendite promozionali > Mostra/Nascondi anteprima. È possibile utilizzare Ctrl-MAIUSC-M per attivare o disattivare l'anteprima visualizzazione HTML.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Divisione repository e copiarla nel computer in uso

1. Creare una divisione del repository in GitHub: passare alla parte superiore destra della pagina e fare clic sul pulsante di divisione. Se richiesto, selezionare l'account come la posizione in cui deve essere creata la divisione. Viene creata una copia dell'archivio all'interno di account fra Hub. In generale, technical writer e program manager necessario divisione azure prezzo di contenuto repo privato. Collaboratori community necessario contenuto azure di divisione, repo pubblico. È sufficiente divisione una volta; Dopo l'installazione e configurazione prima, se si vuole copiare la divisione in un altro computer, è sufficiente eseguire i comandi che seguono in questa sezione per copiare la repo nel computer in uso.  Se si sceglie di creare biforcazioni di entrambi i repository, sarà necessario creare una divisione per ogni repository.

2. Copiare il personale accesso Token ottenuto dal [https://github.com/settings/tokens](https://github.com/settings/tokens). È possibile accettare le autorizzazioni predefinite per il token.  Salvare il Token di accesso personali in un file di testo per poterle riutilizzare in futuro.

3. Copiare l'archivio nel computer in uso con le credenziali incorporate nella stringa di comando.  A tale scopo, aprire fra Bash ed Esegui come amministratore. Al prompt dei comandi immettere il comando seguente.  Questo comando crea una directory azure-content(-pr) nel computer in uso.  Se si sta utilizzando il percorso predefinito, sarà in c:\users<your Windows user name>\azure-content(-pr).

Repo pubblico:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Privato repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Questo comando duplicato, ad esempio, potrebbe essere simile al seguente:

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Connessione repository remoto e configurare le credenziali

Creare un riferimento all'archivio radice tramite l'immissione di questi comandi. Imposta le connessioni all'archivio in GitHub in modo che è possibile ottenere le ultime modifiche apportate nel computer locale e reinserire le modifiche apportate a GitHub. Questo comando consente di configurare anche i token in locale in modo che non è necessario immettere il nome e la password ogni volta che si tenta di accedere a repo padre e la divisione in GitHub.

Repo pubblico:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Privato repo:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Di solito sono necessari un po' di tempo. Al termine dell'operazione, non sarà necessario divisione nuovamente o immettere nuovamente le credenziali. È solo copiare biforcazioni nuovamente in un computer locale se gli strumenti è stato configurato in un altro computer.


## <a name="configure-your-user-name-and-email-locally"></a>Configurare il nome utente e la posta elettronica in locale

Per assicurarsi che l'utente è elencato correttamente come collaboratore, è necessario configurare il nome utente e la posta elettronica locale in fra.

1. Avviare fra Bash e alternare azure contenuto o prezzo di contenuto azure:

   ````
   cd azure-content
   ````

 o

   ````
   cd azure-content-pr
   ````

2. Configurare il proprio nome utente in modo che corrisponda il nome come è configurato nel profilo GitHub:

    ````
    git config --global user.name "John Doe"
    ````
3. Configurare la posta elettronica in modo che corrisponda alle dimensioni messaggio di posta elettronica primario designato nel profilo GitHub; Se si è un dipendente MSFT, deve essere un indirizzo di posta elettronica MSFT:

    ````
    git config --global user.email "alias@example.com"
    ````
4. Tipo di `git config -l` e rivedere le impostazioni locali per assicurarsi che il nome utente e posta elettronica nella configurazione siano corrette.

##<a name="next-steps"></a>Passaggi successivi

- Comprendere il tipo di contenuto che appartenenza a repo di contenuto tecnico e sapere cosa non appartiene. Vedere le [indicazioni canale contenuto](./content-channel-guidance.md)!
- Seguire [questa procedura per creare o modificare un articolo e quindi inviarla per la pubblicazione](./git-commands-for-master.md).
- Copiare [il modello delle vendite promozionali](../markdown templates/markdown-template-for-new-articles.md) come base per un nuovo articolo.
- Usare [questo elenco di controllo per verificare la richiesta di pull grado di soddisfare i criteri di qualità](./contributor-guide-pr-criteria.md) per l'unione.


###<a name="contributors-guide-navigation"></a>Spostamento manuale dei collaboratori

- [Articolo di panoramica](./../README.md)
- [Indice degli articoli di Guida](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
