<properties 
    pageTitle="Autenticazione a più fattori Azure - funzionamento"
    description="Azure autenticazione a più fattori consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Fornisce rafforzare la sicurezza richiedendo un secondo metodo di autenticazione e offre autenticazione tramite un intervallo delle opzioni di verifica semplice."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Funzionamento dell'autenticazione a più fattori Azure

La protezione dell'autenticazione a più fattori si trova nel suo approccio a più livelli. Compromettere più fattori di autenticazione rappresenta un problema significativo per gli utenti malintenzionati. Anche se un pirata informatico riesce scoprire la password dell'utente, è inutile senza anche possesso del dispositivo attendibile. L'utente deve perdere il dispositivo, la persona che si trova non sarà possibile usarlo a meno che non lui anche conosce la password dell'utente.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure autenticazione a più fattori consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo.  Fornisce rafforzare la sicurezza richiedendo un secondo metodo di autenticazione e offre autenticazione tramite un intervallo delle opzioni di verifica semplice:

- telefonata
- messaggio di testo
- notifica di app per dispositivi mobili, consentendo agli utenti di scegliere il metodo più appropriato
- codice di verifica app per dispositivi mobili
- 3 ° token GIURAMENTO di terze parti

Per ulteriori informazioni come è possibile vedere come funziona vedere il video seguente.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Metodi disponibili per l'autenticazione a più fattori
Quando un utente accede, una verifica aggiuntiva viene inviata all'utente.  Di seguito sono un elenco dei metodi che possono essere usate per il suono.

Metodo di verifica  | Descrizione
------------- | ------------- |
Telefonata | Una chiamata per Smartphone dell'utente per verificare che si sta eseguendo la premendo il simbolo #.  Questo completeranno il processo di verifica.  Questa opzione è configurabile e può essere modificata con un codice specificato.
Messaggio di testo | Verrà inviato un messaggio di testo per Smartphone di un utente con un codice di 6 cifre.  Immettere il codice per completare il processo di verifica.
Notifica di App per dispositivi mobili | Verrà inviata una richiesta di verifica per Smartphone dell'utente chiedendo loro di completare la verifica selezionando verifica dall'app per dispositivi mobili. Questo problema si verifica se è selezionata l'opzione scelta della barra multifunzione come metodo di verifica principale.  Se si ricevono quando non vengono accesso, possono scegliere segnalazione di frodi.
Codice di verifica con App Mobile | Un codice di verifica verrà inviato all'app per dispositivi mobili in esecuzione su Smartphone dell'utente.  Questo problema si verifica se è selezionato un codice di verifica come metodo di verifica principale.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di autenticazione a più fattori Azure
Autenticazione a più fattori Azure è disponibile in tre diverse versioni.  Nella tabella seguente descrive ognuna di esse in modo più dettagliato.

Versione  | Descrizione
------------- | ------------- |
Autenticazione a più fattori per Office 365 | Questa versione funziona esclusivamente con le applicazioni di Office 365 e gestita dal portale di Office 365. In modo che gli amministratori ora consente di proteggere le risorse di Office 365 mediante l'autenticazione a più fattori.  Questa versione viene fornito con un abbonamento a Office 365.
Autenticazione a più fattori per gli amministratori di Azure | Lo stesso sottoinsieme di funzionalità di autenticazione a più fattori per Office 365 sarà disponibile senza costi a tutti gli amministratori di Azure. Ogni account amministrativo di un abbonamento Azure ora possibile ottenere ulteriore protezione abilitando questa funzionalità di autenticazione a più fattori core. In modo che un amministratore che desidera accedere al portale Azure per creare una macchina virtuale, un sito web, gestire lo spazio di archiviazione, servizi mobili o qualsiasi altro servizio di Azure aggiungere l'autenticazione a più fattori al proprio account di amministratore.
Autenticazione a più fattori Azure | Autenticazione a più fattori Azure offre l'insieme completo di funzionalità. <br><br>Fornisce ulteriori opzioni di configurazione tramite il portale di gestione di Azure, avanzate creazione di report e il supporto per un intervallo di locali e cloud applicazioni. Autenticazione a più fattori Azure può essere acquistato come una licenza autonoma ed è abbinato all'interno di Azure Active Directory Premium ed Enterprise mobilità Suite. <br><br>Può essere acquistato anche alla scala cronologica consumo creando un Provider di autenticazione a più fattori Azure in un abbonamento a Azure.
##<a name="feature-comparison-of-versions"></a>Confronto delle funzionalità delle versioni
Nella tabella seguente riportata di seguito comprende un elenco delle caratteristiche disponibili nelle versioni diverse di autenticazione a più fattori Azure.


Caratteristica  | Autenticazione a più fattori per Office 365 (incluso nelle SKU di Office 365)|Autenticazione a più fattori per gli amministratori di Azure (incluso in abbonamento Azure) | Autenticazione a più fattori Azure (incluso in Azure Active Directory Premium e famiglia di prodotti mobilità Enterprise)
------------- | :-------------: |:-------------: |:-------------: |
Gli amministratori possono proteggere gli account con MFA| * | * (Disponibile solo per gli account di amministratore di Azure)|*
App per dispositivi mobili come un fattore secondo|* | * | *
Telefonata come fattore secondo|* | * | *
SMS come fattore secondo|* | * | *
Password dell'App per i client che non supportano MFA|* | * | *
Controllo amministrazione i metodi di autenticazione| *| *| *
Modalità PIN| | | *
Avviso di frode| | | *
Report MFA| | | *
Ignora occasionale| | | *
Messaggio di saluto personalizzati per le chiamate telefoniche| | | *
Personalizzazione dell'ID chiamante per le chiamate telefoniche| | | *
Conferma dell'evento| | | *
IP di attendibile| | | *
Sospendere MFA per i dispositivi da ricordare (Public Preview)| | | *
SDK MFA| | | *
MFA Applications locale utilizzando MFA server| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Come ottenere l'autenticazione a più fattori di Azure

Se si vuole che tutte le funzionalità offerte da autenticazione a più fattori Azure anziché solo quelle per gli utenti di Office 365 e amministratori di Azure, sono disponibili diverse opzioni per scoprire come:

1.  Acquistare licenze di autenticazione a più fattori Azure e assegnarle agli utenti.
2.  Acquistare licenze che dispongono di autenticazione a più fattori Azure aggregati al loro interno, ad esempio Azure Active Directory Premium o Enterprise mobilità Suite e assegnarle agli utenti.
3.  Creare un Provider di autenticazione a più fattori Azure all'interno di un abbonamento a Azure. Se si dispone già di un abbonamento a Azure, è possibile iscriversi per una sottoscrizione di valutazione di Azure. Sottoscrizioni di valutazione dovranno essere convertite in normali abbonamenti prima della scadenza.

Quando si usa un Provider di autenticazione a più fattori Azure sono disponibili due modelli di utilizzo disponibili fatturate tramite l'abbonamento Azure:


- **Per ogni utente**. In genere per le aziende che desidera abilitare l'autenticazione a più fattori per un numero fisso di tutti i dipendenti che hanno l'esigenza regolarmente l'autenticazione.
- **Per l'autenticazione**. In genere per le aziende che desidera abilitare l'autenticazione a più fattori per un grande numero di utenti esterni che hanno l'esigenza raramente l'autenticazione.

Per ulteriori informazioni sui prezzi vedere [Azure prezzi MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Scegliere i postazione o il modello in base a consumo più adatta per la propria organizzazione.   Quindi per ottenere introduttiva vedere [Guida introduttiva](multi-factor-authentication-get-started.md)
