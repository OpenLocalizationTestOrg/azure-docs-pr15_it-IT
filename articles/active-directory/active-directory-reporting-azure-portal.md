<properties
   pageTitle="Dichiarazione di Azure Active Directory - anteprima | Microsoft Azure"
   description="Sono elencati i diversi report disponibili per l'anteprima di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Dichiarazione di Azure Active Directory - anteprima

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-reporting-azure-portal.md)
- [Portale classica Azure](active-directory-reporting-guide.md)

*Questa documentazione fa parte di [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Con creazione di report in anteprima di Azure Active Directory, viene visualizzato tutte le informazioni che necessarie per determinare come procede l'ambiente. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md)

Esistono due aree principali di report:

- **Attività di accesso** , tra cui le informazioni sull'utilizzo di applicazioni gestite e le attività di accesso dell'utente

- **Log di controllo** : l'attività delle informazioni di sistema utenti e gestione dei gruppi, le applicazioni gestite e le attività di directory

A seconda dell'ambito di dati che si sta cercando, è possibile accedere direttamente questi report, fare clic su **utenti e gruppi** o **applicazioni aziendali** nell'elenco servizi nel [portale di Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Attività di accesso

### <a name="user-sign-in-activities"></a>Attività di accesso dell'utente

Con le informazioni fornite dalla nota accesso piè di pagina, si risposte alle domande quali:

- Che cos'è il criterio di accesso di un utente?
- Quanti utenti è utenti effettuano l'accesso più di una settimana?
- Qual è lo stato di questi componenti aggiuntivi di accesso?

Il punto di ingresso a tali dati è il grafico di accesso utente nella sezione **informazioni generali** in **utenti e gruppi**.

 ![Creazione di report] (./media/active-directory-reporting-azure-portal/05.png "Creazione di report")

Utente accesso grafico Mostra aggregazioni settimanale del segno aggiuntivi per tutti gli utenti in un determinato periodo di tempo. Il valore predefinito per il periodo di tempo è 30 giorni.

![Creazione di report] (./media/active-directory-reporting-azure-portal/02.png "Creazione di report")

Quando si fa clic in un giorno nel grafico accesso, viene visualizzato un elenco dettagliato delle attività di accesso.

![Creazione di report] (./media/active-directory-reporting-azure-portal/03.png "Creazione di report")

Ogni riga nell'elenco attività di accesso offre informazioni dettagliate sull'accesso aggiuntivo selezionato come:

- Chi ha effettuato l'accesso?

- Qual è l'UPN correlato?

- Quale applicazione in cui la destinazione di accesso?

- Che cos'è l'indirizzo IP di accesso?

- Qual è lo stato dell'accesso?

### <a name="usage-of-managed-applications"></a>Uso delle applicazioni gestite

Con un'applicazione di visualizzazione dei dati di accesso, si possono rispondere a domande quali:

- Chi sta utilizzando le applicazioni?

- Quali sono le prime 3 applicazioni nell'organizzazione?

- Ha recentemente ho implementato un'applicazione. Come procede?


Il punto di ingresso a tali dati è le prime 3 applicazioni all'interno dell'organizzazione all'interno del report di 30 giorni ultimo nella sezione **informazioni generali** in **applicazioni aziendali**.

 ![Creazione di report] (./media/active-directory-reporting-azure-portal/06.png "Creazione di report")


Aggregazioni settimanale grafico utilizzo app di accedere aggiuntivi per le applicazioni primi 3 in un determinato periodo di tempo. Il valore predefinito per il periodo di tempo è 30 giorni.

![Creazione di report] (./media/active-directory-reporting-azure-portal/78.png "Creazione di report")

Se si desidera, è possibile impostare lo stato attivo sull'applicazione.

![Creazione di report] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Creazione di report")


Quando si fa clic in un giorno del grafico l'uso di app, viene visualizzato un elenco dettagliato delle attività di accesso.


![Creazione di report] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Creazione di report")



L'opzione **componenti aggiuntivi di accesso** viene offerta una panoramica di tutti gli eventi di accesso completa alle applicazioni.

![Creazione di report] (./media/active-directory-reporting-azure-portal/85.png "Creazione di report")

Se si utilizza la selezione colonne, è possibile selezionare i campi di dati che si desidera visualizzare.

![Creazione di report] (./media/active-directory-reporting-azure-portal/column_chooser.png "Creazione di report")



### <a name="filtering-sign-ins"></a>Il filtro di componenti aggiuntivi di accesso

È possibile filtrare un intervallo di tempo per limitare la quantità di dati visualizzati componenti aggiuntivi di accesso.

![Creazione di report] (./media/active-directory-reporting-azure-portal/927.png "Creazione di report")


Un altro metodo per filtrare le voci delle attività di accesso è cercare voci specifiche.
Il metodo di ricerca consente di definire l'ambito dei aggiuntivi accesso intorno a specifici **utenti**, **gruppi** o **applicazioni**.


![Creazione di report] (./media/active-directory-reporting-azure-portal/84.png "Creazione di report")

## <a name="audit-logs"></a>Log di controllo

I registri di Azure Active Directory forniscono record attività di sistema per la conformità.

Sono disponibili tre categorie principali per il controllo delle attività correlate nel portale di Azure:

- Utenti e gruppi   

- Applicazioni

- Directory   


Per un elenco completo delle attività di report di controllo, vedere l' [elenco di eventi del report di controllo](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Il punto di ingresso a tutti i dati di controllo è **log di controllo** nella sezione **attività** di **Azure Active Directory**.


![Il controllo] (./media/active-directory-reporting-azure-portal/61.png "Il controllo")


Un log di controllo è disponibile una visualizzazione elenco che mostra i soggetti (che), le attività (quali) e le destinazioni.


![Il controllo] (./media/active-directory-reporting-azure-portal/345.png "Il controllo")


Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere ulteriori dettagli.

![Il controllo] (./media/active-directory-reporting-azure-portal/873.png "Il controllo")




### <a name="users-and-groups-audit-logs"></a>Utenti e gruppi dei registri


Con utente e i report di controllo in base a gruppo, è possibile ottenere risposte alle domande quali:

- Quali tipi di aggiornamento sono stati applicati gli utenti?

- Quanti utenti sono state modificate?

- Quante password sono state modificate?

- Cosa ha fatto un amministratore di una directory?

- Informazioni sui gruppi che sono stati aggiunti

- Esistono gruppi con le modifiche di appartenenza?

- I proprietari del gruppo sono stati modificati?

- Quali licenze sono stati assegnati a un gruppo o un utente?


Se si vuole semplicemente esaminare i dati controllo correlate a utenti e gruppi, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **attività** di **utenti e gruppi**.


![Il controllo] (./media/active-directory-reporting-azure-portal/93.png "Il controllo")


### <a name="application-audit-logs"></a>Log di controllo dell'applicazione

Con basate su applicazioni report di verifica, è possibile ottenere risposte alle domande quali:

- Quali sono le applicazioni di aggiunto o aggiornate?

- Quali sono le applicazioni che sono state rimosse?

- Un principio di servizio per un'applicazione è cambiato?

- I nomi delle applicazioni sono stati modificati?

- Consenso dell'utente che ha assegnato a un'applicazione?


Se si desidera esaminare i dati di controllo relative alle applicazioni, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **attività** di **applicazioni aziendali**.


![Il controllo] (./media/active-directory-reporting-azure-portal/134.png "Il controllo")


### <a name="filtering-audit-logs"></a>Filtro i log di controllo

È possibile filtrare un report di controllo per un intervallo di tempo per limitare la quantità di dati visualizzati.

![Il controllo] (./media/active-directory-reporting-azure-portal/324.png "Il controllo")

Un altro metodo per filtrare le voci di un file di log di controllo è cercare voci specifiche.

![Il controllo] (./media/active-directory-reporting-azure-portal/237.png "Il controllo")

## <a name="next-steps"></a>Passaggi successivi

Vedere [Azure Active Directory Reporting Guida](active-directory-reporting-guide.md).
