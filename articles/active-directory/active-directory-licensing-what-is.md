<properties
    pageTitle="Informazioni sulla licenza di Microsoft Azure Active Directory | Microsoft Azure"
    description="Descrizione delle licenze Microsoft Azure Active Directory, come funziona, come iniziare e procedure consigliate, inclusi Office 365, Microsoft Intune e Azure Active Directory Premium e le edizioni di base"
    services="active-directory"
      keywords="Gestione delle licenze di Azure Active Directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>Informazioni sulla licenza di Microsoft Azure Active Directory

##<a name="description"></a>Descrizione
Azure Active Directory (Azure Active Directory) è possibile eseguire identità Microsoft un servizio (IDaaS) e della piattaforma. Azure Active Directory è disponibile in un numero di versioni funzionali e tecniche che vanno dalla versione gratuita di Active Directory Azure, è disponibile con qualsiasi servizio Microsoft, ad esempio Office 365, Dynamics, Microsoft Intune e Azure (Azure Active Directory non generare le spese di consumo in questa modalità), di Azure Active Directory pagato versioni, ad esempio Enterprise mobilità famiglia di prodotti (EMS), Azure Active Directory Premium Basic, nonché l'autenticazione a più fattori Azure (MFA). Come molte di Microsoft online services, più Azure Active Directory pagato versioni vengono recapitate tramite diritti per utente, come si trovano in Office 365, Microsoft Intune e Azure Active Directory. In questi casi, l'acquisto di servizi viene rappresentato con una o più sottoscrizioni e ogni abbonamento include un numero pre-acquisto di licenze nel tenant. Diritti per utente si ottengono tramite l'assegnazione di licenze, si crea un collegamento tra un utente e sul prodotto, per consentire i componenti del servizio per l'utente e l'utilizzo di una delle licenze prepagate.

[Provare ora premium Azure Active Directory.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portale di amministrazione Azure Active Directory è una parte del portale di classica Azure. Durante l'utilizzo di Azure Active Directory non richiede alcun acquisto Azure, accesso al portale questo richiede un abbonamento attivo a Azure o una [sottoscrizione di valutazione Azure](https://azure.microsoft.com/pricing/free-trial/).

Per un'ampia panoramica delle funzionalità di servizi di Azure Active Directory, vedere [Novità di Azure Active Directory](active-directory-whatis.md).
[Maggiori informazioni sui livelli di servizio di Azure Active Directory](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Sottoscrizioni di Azure pagare in base all'uso sono diverse: mentre rappresentata anche nella directory, tali sottoscrizioni abilitazione della creazione di risorse Azure e mapparli al metodo di pagamento. Sono non disponibili in questo caso totali licenze associati all'abbonamento. Associazione degli utenti con l'abbonamento, l'accesso degli utenti per la gestione delle risorse di sottoscrizione, è possibile concedere loro le autorizzazioni per eseguire operazioni su Azure risorse associate all'abbonamento.


##<a name="how-does-azure-ad-licensing-work"></a>Come funziona la gestione delle licenze di Azure Active Directory?

In base a licenza (basato sul diritto) Azure Active Directory di servizi attivando una sottoscrizione nel tenant / servizio directory di Azure Active Directory. Dopo la sottoscrizione è attiva la funzionalità di servizio può gestite da amministratori/servizio directory e utilizzata da utenti autorizzati.

Quando si acquistare o attivare Enterprise mobilità Suite, Azure Active Directory Premium o base di Azure Active Directory, la directory viene aggiornata con l'abbonamento, incluso il periodo di validità e licenze prepagate. Le informazioni sull'abbonamento, tra cui lo stato, evento successivo del ciclo di vita e il numero di licenze assegnate o disponibile è disponibile tramite il portale classico Azure nella scheda licenze per la directory specifica. Questo è migliore per gestire le assegnazioni delle licenze.

Ciascuna sottoscrizione è costituito da uno o più piani di servizio, ogni mapping il livello di funzionalità incluso del tipo di servizio. ad esempio Azure AD MFA Azure, Microsoft Intune, Exchange Online o SharePoint Online. Gestione delle licenze Azure Active Directory non richiede di gestione dei livelli di servizio piano. Questa è la differenza tra Office 365 che si basa su questa modalità di configurazione avanzate per gestire l'accesso ai servizi inclusi. Azure Active Directory si basa sulla configurazione del servizio, per attivare le caratteristiche e gestire le singole autorizzazioni.

In generale, le informazioni di sottoscrizione di Azure Active Directory viene gestite tramite il portale classico Azure, nella scheda licenze per la pagina specifica. Sottoscrizioni di Azure Active Directory, ad eccezione di Azure Active Directory Premium, non vengono visualizzati nel portale di Office.

> [AZURE.IMPORTANT] Azure Active Directory Premium e Basic, oltre al tipo abbonamenti Enterprise mobilità Suite sono limitate a tenant di directory provisioning. Le sottoscrizioni non è possibile dividere tra directory o utilizzate per autorizzare gli utenti di altre directory. Spostamento di una sottoscrizione tra directory è possibile ma richiede l'invio di un ticket di supporto o annullamenti ed nuovamente acquistare nel caso di acquisti diretti.

> Al momento dell'acquisto di Azure Active Directory o Enterprise mobilità Suite tramite contratti multilicenza attivazione della sottoscrizione verrà eseguito automaticamente quando il contratto include altri servizi Online Microsoft, ad esempio Office 365.

Pagato Azure caratteristiche di Active Directory estesi l'ampiezza della directory. Alcuni esempi:
- Gruppo basato assegnazione alle applicazioni, che è attivato in applicazione specifica che vengono gestiti.
- Avanzate e le funzionalità di gestione di gruppo modalità self-service sono disponibili in configurazione di directory o all'interno del gruppo specifico.
- Report sulla protezione Premium sono nella scheda report
- Individuazione di applicazioni cloud viene visualizzata nel portale di Azure in identità.

###<a name="assigning-licenses"></a>Assegnazione di licenze
Mentre ottenere una sottoscrizione è costituita da tutti che è necessario configurare le funzionalità di pagamento, utilizzando il Azure Active Directory pagato funzionalità richiede la distribuzione di licenze per gli utenti di destra. In generale, tutti gli utenti che devono avere accesso a o che viene gestito tramite un annuncio Azure pagato caratteristica devono essere assegnato una licenza. Un'assegnazione di licenze di un mapping tra un utente e un servizio acquistato, ad esempio Azure Active Directory Premium, Basic o Enterprise mobilità Suite.

Gestire gli utenti che la directory devono avere una licenza è semplice. Può essere eseguita mediante l'assegnazione a un gruppo di creare regole di assegnazione tramite il portale di amministrazione di Azure Active Directory o mediante l'assegnazione di licenze direttamente a destra individui tramite un portale, PowerShell o API. Quando si assegna licenze a un gruppo, tutti i membri del gruppo vengano assegnati una licenza. Se gli utenti vengono aggiunti o rimossi dal gruppo verranno assegnate o rimuovere la licenza appropriata. Assegnazione al gruppo può utilizzare Gestione qualsiasi gruppo disponibili e coerente con l'assegnazione di gruppo in base alle applicazioni. Questo approccio, è possibile impostare le regole in modo che tutti gli utenti nella directory vengono assegnati automaticamente, assicurarsi che tutti gli utenti con il titolo appropriato disponga di una licenza o persino delegare la decisione di altri responsabili dell'organizzazione.

Assegnazione di licenze basato su gruppo, qualsiasi utente mancano una località di utilizzo ereditano il percorso della directory durante l'assegnazione. Questo percorso può essere modificato dall'amministratore in qualsiasi momento. Nei casi in cui l'assegnazione automatica non riuscita a causa di errore, le informazioni utente in quel tipo di licenza rifletteranno tale stato.

##<a name="getting-started-with-azure-ad-licensing"></a>Guida introduttiva di gestione delle licenze di Azure Active Directory

Guida introduttiva di Azure Active Directory è facile; è anche possibile creare directory come parte di iscrizione a una versione di valutazione di Azure gratuito. [Ulteriori informazioni sull'iscrizione come un'organizzazione](sign-up-organization.md). Di seguito consentono di verificare che la directory meglio sia allineata con altri servizi Microsoft si potrebbe essere costituita o si prevede di utilizzare e i propri obiettivi di ottenere il servizio.

Ecco un paio di procedure consigliate:
- Se si utilizza già i servizi dell'organizzazione di Microsoft, si dispone già di una directory di Azure Active Directory. In questo caso, è necessario continuare a usare la stessa directory per altri servizi, in modo da Gestione delle identità di base, inclusi il provisioning e ibrido Single Sign-on, è possibile utilizzare i servizi. Gli utenti avranno un'esperienza di accesso singolo e verranno vantaggi offerti dai funzionalità più complesse tra i servizi. Di conseguenza, se si decide di acquistare un annuncio Azure pagato servizio per i dipendenti, è consigliabile utilizzare la stessa directory per eseguire questa operazione.
- Se si prevede di utilizzare Azure Active Directory per un set di utenti (partner, clienti e così via) o se si desidera esaminare i servizi di Azure Active Directory e si desidera eseguire tale operazione di isolamento del servizio di produzione o se sono necessarie per configurare un ambiente sandbox per i servizi, è consigliabile creare prima di tutto una nuova directory tramite il portale di classica Azure Azure diverso. [Ulteriori informazioni sulla creazione di una nuova directory di Azure Active Directory nel portale di classica Azure](active-directory-licensing-directory-independence.md). La nuova directory verrà creata con l'account come un utente esterno con autorizzazioni di amministratore globale. Quando accede al portale di classica Azure con l'account, sarà possibile vedere questa directory e accedere a tutte le attività amministrative directory. È consigliabile creare un account locale con privilegi appropriati per gestire altri servizi Microsoft (quelli non è accessibile tramite il portale classico Azure). [Altre informazioni sulla creazione di account utente in Active Directory Azure](active-directory-create-users.md).

> [AZURE.NOTE] Azure Active Directory supporta esterni "utenti", ovvero account utente in un'istanza di Azure Active Directory che sono stati creati usando un Account Microsoft (MSA) o un'identità di Azure Active Directory da un'altra directory. Mentre si è occupati estensione questa funzionalità in tutti i servizi dell'organizzazione di Microsoft, immediatamente di tali account non sono supportati in alcune delle esperienze i servizi; ad esempio, il portale di amministrazione di Office 365 non supporta attualmente questi utenti. Di conseguenza, gli utenti esterni con un account Microsoft non sarà possibile accedere, il portale di amministrazione di Office 365 mentre gli utenti esterni da altre directory di Azure Active Directory verranno ignorati. In questo caso, solo l'account dell'utente locale, Azure Active Directory o directory di Office 365 in cui l'utente è stato creato originariamente, possono essere accessibili tramite i contenuti.

Come indicato, Azure Active Directory include diverse versioni di pagamento. Queste versioni sono alcune differenze nella disponibilità di acquisto:


| Prodotto   | EA/CONTRATTO MULTILICENZA     | Apri  |   CSP |   Diritti di utilizzo MPN  |   Acquisto diretto | Versione di valutazione |
|---|---|---|---|---|---|---|
| Famiglia di prodotti mobilità Enterprise |   X | X | X | X |  |      X |
| Azure Active Directory Premium  | X | X | X |   | X | X |
| Azure Active Directory Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Selezionare uno o più versioni di valutazione di licenza
 In tutti i casi, è possibile attivare un abbonamento di valutazione di Azure Active Directory Premium o Enterprise mobilità Suite selezionando la versione di valutazione specifico desiderato nella scheda licenze nella directory. Una versione di valutazione contiene una sottoscrizione di 30 giorni con 100 licenze.

![Piani di licenza di valutazione di Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Piani Enterprise mobilità Suite licenza di valutazione](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Piani di licenza di valutazione attivo](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Assegnare licenze
Dopo la sottoscrizione è attiva, è necessario assegnare una licenza a se stessi e aggiornare il browser per assicurarsi che si visualizzano tutte le caratteristiche. Il passaggio successivo consiste per assegnare licenze agli utenti che saranno necessario accedere o da includere nel pagato caratteristiche di Azure Active Directory. Come indicato in precedenza in "Assegnazione licenze", il modo migliore per eseguire questa operazione è per identificare il gruppo che rappresenta il gruppo di destinatari desiderato e assegnare alla licenza; In questo modo, gli utenti che vengono aggiunti o rimossi dal gruppo sul ciclo di vita assegnati a o rimossi dalla licenza.

Per assegnare una licenza a un gruppo o singoli utenti, selezionare il piano di licenza che si desidera assegnare e fare clic su **Assegna** della barra dei comandi.

![Piani di licenza di valutazione attivo](./media/active-directory-licensing-what-is/assign_licenses.png)

Una volta nella finestra di dialogo assegnazione per il piano selezionato, è possibile selezionare gli utenti e aggiungerli alla colonna **assegnare** a destra. È possibile sfogliare l'elenco utenti o cercare persone specifiche utilizzando la ricerca vetro nell'angolo superiore destro della griglia utente. Per assegnare gruppi, selezionare "Gruppi" dal menu **Visualizza** e quindi fare clic sul pulsante di controllo sulla destra per aggiornare le assegnazioni che vengono visualizzate.

![Assegnazione di licenze ai gruppi](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

A questo punto è possibile cercare o spostarsi tra i gruppi e aggiungerli alla colonna **assegnare** nello stesso modo. È possibile usare questi per assegnare una combinazione di utenti e gruppi in un'unica operazione. Per completare il processo di assegnazione, fare clic sul pulsante di controllo nell'angolo inferiore destro della pagina.

![Messaggio di stato delle assegnazioni di licenze](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Quando viene assegnato un gruppo, i membri ereditano le licenze entro 30 minuti, ma in genere entro 1-2 minuti.

Errori assegnazione possono verificarsi durante l'assegnazione di licenze di Azure Active Directory, ma sono relativamente rari. Potenziali errori di assegnazione sono limitati a:
- Conflitto di assegnazione - quando un utente in precedenza è stato assegnato una licenza che non è compatibile con la licenza corrente. In questo caso, assegna la licenza nuova sarà necessario rimuovere quella precedente.
- Ha superato le licenze disponibili - quando il numero di utenti in gruppi assegnati supera le licenze disponibili, lo stato delle assegnazioni degli utenti verrà applicato a un errore di assegnare a causa di licenze mancante.

###<a name="view-assigned-licenses"></a>Visualizzare assegnate le licenze

Una visualizzazione di riepilogo di licenze assegnate incluso abbonamento disponibili, assegnate e successiva del ciclo di vita evento vengono visualizzati nella scheda **licenze** .

![Visualizzare il numero di licenze assegnate](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Elenco dettagliato dei assegnato agli utenti e gruppi, tra cui lo stato dell'assegnazione e il percorso (diretto o ereditato da uno o più gruppi) è disponibile in caso di spostamento in un piano di licenza.

![Nella visualizzazione dei dettagli di licenze assegnate per un piano di licenza](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

La rimozione di licenze è semplice come l'assegnazione. Se l'utente viene assegnata direttamente o per un gruppo assegnato, è possibile rimuovere la licenza selezionando il tipo di licenza, selezionare **Rimuovi**, aggiunta all'utente o gruppo all'elenco di installazione e per confermare l'azione. In alternativa, è possibile aprire un tipo di licenza, selezionare l'utente o gruppo specifico e toccare **Rimuovi** sulla barra dei comandi. Per terminare l'ereditarietà dell'utente di una licenza da un gruppo, è sufficiente rimuovere l'utente dal gruppo.

###<a name="extending-trials"></a>Estensione prove

Versione di valutazione estensioni per i clienti sono disponibili in modalità self-service tramite il portale di Office 365. Un amministratore cliente può passare al [portale di Office](https://portal.office.com/#Billing) (accesso dipende dalle autorizzazioni per il portale di Office) e selezionare la versione di valutazione di Azure Active Directory Premium. Fare clic sul collegamento **prolunga versione di valutazione** e seguire le istruzioni. È necessario immettere una carta di credito, ma non addebitata.

![Estensione di una versione di valutazione di licenza nel portale di Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

I clienti possono richiedere un'estensione di valutazione per l'invio di una richiesta di assistenza. Un amministratore cliente è possibile passare al portale di Office 365 [pagina del supporto tecnico](http://aka.ms/extendAADtrial) (accesso dipende dalle autorizzazioni per la pagina di supporto di Office). In questa pagina selezionare "Sottoscrizioni e prove" in funzionalità e "Domande per la versione di valutazione" in sintomo. Infine, immettere le informazioni nelle circostanze

![Estensione di una versione di valutazione di licenza con una richiesta di assistenza](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto potrebbe essere possibile configurare e utilizzare alcune caratteristiche Premium di Azure Active Directory.

- [Reimpostazione della password self-service](active-directory-manage-passwords.md)
- [Gestione dei gruppi modalità self-service](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD Connect integrità](active-directory-aadconnect-health.md)
- [Assegnazione al gruppo alle applicazioni](active-directory-manage-groups.md)
- [Autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication.md)
- [Acquisto diretto di licenze di Azure Active Directory Premium](http://aka.ms/buyaadp)
