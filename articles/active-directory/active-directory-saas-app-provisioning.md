<properties
    pageTitle="Automatizzato di Provisioning in Azure Active Directory dell'utente App SaaS | Microsoft Azure"
    description="Un'introduzione all'utilizzo Azure Active Directory per il provisioning automatico, deselezionare il provisioning e aggiornare continuamente gli account utente in più applicazioni SaaS di terze parti."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizzare utente il Provisioning e Deprovisioning alle applicazioni SaaS con Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>Che cos'è automatica il Provisioning dell'utente per App SaaS?

Azure Active Directory (Azure Active Directory) consente di automatizzare la creazione, manutenzione e rimozione dell'identità utente nelle applicazioni di cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ad esempio Dropbox, Salesforce, ServiceNow e altro ancora.

**Di seguito sono riportati alcuni esempi di questa caratteristica consente di eseguire operazioni:**

- Creare automaticamente nuovi account nelle App SaaS destra per nuove persone che accedono al team.
- Disattivare automaticamente gli account da SaaS apps quando persone inevitabile lasciare il team.
- Assicurarsi che le identità nelle applicazioni SaaS siano sempre aggiornate in base alle modifiche nella directory.
- Effettuare il provisioning di oggetti non di utenti, ad esempio gruppi, alle App SaaS che li supportano.

**Il provisioning automatico dell'utente include anche le funzionalità seguenti:**

- La capacità di identità esistenti tra Azure Active Directory e SaaS apps.
- Opzioni di personalizzazione assistenza Azure Active Directory per adattano le configurazioni correnti delle App SaaS che l'organizzazione è attualmente in uso.
- Avvisi di posta elettronica facoltativo per errori di provisioning.
- Creazione di relazioni e attività dei registri per agevolare la risoluzione dei problemi e il monitoraggio.

##<a name="why-use-automated-provisioning"></a>Perché usare il Provisioning automatico?

Di seguito sono riportate alcune motivazioni comuni per l'uso di questa caratteristica:

- Per evitare i costi, efficiente ed errore umano associato manuale i processi di provisioning.
- Per proteggere l'organizzazione rimuovendo istantaneamente identità degli utenti da chiave SaaS apps quando lasciano l'organizzazione.
- Per importare facilmente un numero in blocco di utenti in una specifica applicazione SaaS.
- Per sfruttare il vantaggio di consentire il provisioning soluzione eseguita di fuori degli stessi criteri di accesso di app definite per Azure Active Directory Single Sign-On.

##<a name="frequently-asked-questions"></a>Domande frequenti

**Con quale frequenza Azure Active Directory inserire le modifiche apportate directory app SaaS?**

Azure Active Directory verifica la presenza di modifiche ogni cinque ai dieci minuti. Se l'app SaaS restituisce diversi errori (ad esempio nel caso delle credenziali di amministratore non validi), Azure Active Directory gradualmente rallentano la frequenza di a una volta al giorno fino alla correzione degli errori.

**Quanto tempo sarà provisioning degli utenti?**

Le modifiche incrementali verificano quasi istantaneamente, ma se si sta tentando di provisioning la maggior parte delle directory, quindi dipende il numero di utenti e gruppi che si dispone. Directory Small solo alcuni minuti, medie directory potrebbe richiedere alcuni minuti e directory di dimensioni molto grandi può richiedere alcune ore.

**Come è possibile tenere traccia dello stato di avanzamento del processo di provisioning corrente?**

È possibile esaminare il rapporto di Provisioning Account nella sezione report della directory. È anche possibile visitare la scheda Dashboard per l'applicazione SaaS provisioning a e controllare la sezione "Integrazione stato" nella parte inferiore della pagina.

**Come si sapere se gli utenti non riuscire a ottenere il provisioning correttamente?**

Al termine della configurazione di provisioning sono è un'opzione per l'iscrizione a notifiche tramite posta elettronica per il provisioning di errori. È anche possibile verificare il Report di errori di Provisioning per visualizzare gli utenti che non è possibile effettuare il provisioning e perché.

**Possibile Azure Active Directory scrivere le modifiche da app SaaS nella directory?**

Per la maggior parte delle applicazioni SaaS, il provisioning è solo in uscita, il che significa che gli utenti vengono scritte dalla directory all'applicazione e le modifiche apportate dall'applicazione non è possibile scritte alla directory. Per [giorno lavorativo](https://msdn.microsoft.com/library/azure/dn762434.aspx), tuttavia, il provisioning è solo in ingresso, vale a dire che gli utenti vengono importati nella directory da lavorativo e allo stesso modo, le modifiche nella directory non ottenere scritti nel giorno lavorativo.

**Come è possibile inviare commenti e suggerimenti al team di progettazione?**

Contatta Microsoft tramite l' [area feedback Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Come funziona automatizzato Provisioning?

Azure Active Directory disposizioni agli utenti di App SaaS connettendosi a provisioning endpoint fornito da ogni fornitore dell'applicazione. Questi endpoint consentono di Azure Active Directory creare, aggiornare e rimuovere utenti a livello di programmazione. Di seguito è riportata una breve panoramica dei diversi passaggi che entrerà in Azure Active Directory per automatizzare il provisioning.

1. Se si abilita il provisioning di un'applicazione per la prima volta, vengono eseguite le operazioni seguenti:
 - Azure Active Directory tenterà in modo che corrispondano degli utenti nell'app SaaS con la propria identità corrispondente nella directory. Quando un utente viene trovata una corrispondenza, vengono *non* automaticamente l'abilitazione per single sign-on. Affinché un utente abbia accesso all'applicazione, si deve essere in modo esplicito assegnate all'app in Azure Active Directory, direttamente o tramite l'appartenenza ai gruppi.
 - Se è già stato specificato gli utenti che devono essere assegnati all'applicazione e Azure Active Directory non riesce a trovare gli account esistenti per tali utenti, Azure Active Directory verrà provisioning di nuovi account per poterli nell'applicazione.
2. Dopo aver completata la sincronizzazione iniziale come descritto in precedenza, Azure Active Directory controllerà 10 minuti per le modifiche seguenti:
 - Se nuovi utenti sono stati assegnati all'applicazione (direttamente o tramite l'appartenenza ai gruppi), quindi saranno viene completato il provisioning di un nuovo account nell'app SaaS.
 - Se è stata rimossa accesso dell'utente, quindi il proprio account nell'app SaaS verrà contrassegnato come disabilitato (gli utenti sono mai completamente rimossi, che impedisce la perdita di dati in caso di una configurazione errata).
 - Se un utente di recente è stato assegnato all'applicazione e disponevano già un account nell'app SaaS, tale account verrà contrassegnato come abilitato e alcune proprietà utente potrebbero essere aggiornate se sono scadute rispetto alla directory.
 - Se le informazioni dell'utente (ad esempio il numero di telefono, ubicazione dell'ufficio, e così via) sono state modificate nella directory, tali informazioni anche essere aggiornate nell'applicazione SaaS.

Per ulteriori informazioni su come gli attributi sono mappati tra Azure Active Directory e l'app SaaS, vedere l'articolo sulla [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Elenco di applicazioni che supportano il Provisioning automatico dell'utente

Fare clic su un'App per vedere un'esercitazione su come configurare il provisioning automatico per renderla:

- [Casella](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Contribuire](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Forza vendita](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Giorno lavorativo](http://go.microsoft.com/fwlink/?LinkId=690250) (provisioning in ingresso)

Consentire a un'applicazione per supportare il provisioning automatico dell'utente, offre endpoint necessari che consentono ai programmi esterni automatizzare la creazione, manutenzione e la rimozione degli utenti. Di conseguenza, non tutte le app SaaS sono compatibili con questa caratteristica. Per le applicazioni che supportano questa caratteristica, il team di progettazione di Azure Active Directory sarà in grado di compilazione di un connettore provisioning a tali applicazioni e il corretto funzionamento sia assegnata la priorità per le esigenze dei clienti e potenziali.

Per contattare il team di progettazione di Azure Active Directory per richiedere supporto provisioning per domande, inviare un messaggio tramite l' [area feedback Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Personalizzazione dei mapping degli attributi per il Provisioning dell'utente](active-directory-saas-customizing-attribute-mappings.md)
- [Scrittura di espressioni per mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Definizione dell'ambito filtri per il Provisioning dell'utente](active-directory-saas-scoping-filters.md)
- [Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni](active-directory-scim-provisioning.md)
- [Account il Provisioning di notifiche](active-directory-saas-account-provisioning-notifications.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)
