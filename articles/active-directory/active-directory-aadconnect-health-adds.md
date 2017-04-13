
<properties
    pageTitle="Collegare mediante Azure Active Directory integrità con Active Directory | Microsoft Azure"
    description="Questa è la pagina di Azure Active Directory connettersi integrità che verrà descritto come eseguire il monitoraggio di dominio Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Collegare mediante Azure Active Directory integrità con Active Directory
La documentazione seguente è specifica di monitoraggio di servizi di dominio Active Directory con Azure Active Directory connettersi integrità. Versioni di Active Directory vengono: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

Per ulteriori informazioni sul monitoraggio ADFS con integrità di connessione di Azure Active Directory, vedere [Integrità connettersi con Azure Active Directory con ADFS](active-directory-aadconnect-health-adfs.md). Inoltre, per informazioni sul controllo Azure AD Connect (sincronizzazione) con Azure Active Directory connettersi integrità vedere [Integrità connettersi con Azure Active Directory per la sincronizzazione](active-directory-aadconnect-health-sync.md).

![Azure AD Connect integrità per Active Directory](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Avvisi per Azure Active Directory la connessione dell'integrità di dominio Active Directory
La sezione avvisi all'interno di Azure Active Directory connettersi integrità per Active Directory comprende un elenco di avvisi attivi e risolti, relativi al controller di dominio. Selezione di un avviso attivo o risolto verrà aperta una nuova pala con ulteriori informazioni, insieme a procedure di risoluzione e collegamenti a documentazione di supporto. Ogni tipo di avviso può avere una o più istanze corrispondenti a ciascuno dei controller di dominio incidono sull'avviso specifico. Nella parte inferiore della stessa e degli avvisi, fare doppio clic un controller di dominio interessato per aprire un blade aggiuntivi con ulteriori dettagli sull'istanza di avviso.

All'interno di blade, è possibile attivare le notifiche di posta elettronica per gli avvisi e modificare l'intervallo di tempo nella visualizzazione. Espansione dell'intervallo di tempo consente di visualizzare gli avvisi risolti precedenti.

![Errore di sincronizzazione Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Dashboard di controller di dominio
Questo dashboard fornisce una visualizzazione topologia dell'ambiente, oltre a rendere metriche operative chiave e lo stato di integrità di ogni controller di dominio monitorate. Metrica presentata consente di individuare velocemente qualsiasi controller di dominio che potrebbe richiedere un'ulteriore analisi. Per impostazione predefinita, viene visualizzato solo un sottoinsieme delle colonne. Tuttavia, è possibile trovare l'intero set di colonne disponibili facendo doppio clic sul comando di colonne. Selezionare le colonne che è più rilevante, posizionare il dashboard in un unico e semplice per visualizzare lo stato dell'ambiente di dominio Active Directory attiva.

![Controller di dominio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Controller di dominio possono essere raggruppati in base al dominio o il sito, è utile per comprendere la topologia di ambiente. Infine, se si fa doppio clic sull'intestazione di blade, il dashboard ingrandita per utilizzare l'immobili disponibile sullo schermo. Questa visualizzazione ingrandita è utile quando vengono visualizzate più colonne.

## <a name="replication-status-dashboard"></a>Dashboard di stato della replica
Questo dashboard fornisce una visualizzazione dello stato di replica e topologia di replica controller di dominio monitorate. Lo stato dell'ultimo tentativo di replica è elencato, insieme a documentazione utile per eventuali errori che si trova. È possibile fare doppio clic su un controller di dominio con un errore, per aprire una nuova pala con le informazioni, ad esempio: dettagli sull'errore consigliati procedure di risoluzione e collegamenti alla documentazione di risoluzione dei problemi.

![Stato della replica](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitoraggio
Questa caratteristica indica le tendenze di grafiche diversi contatori delle prestazioni, che vengono continuamente raccolti da ogni controller di dominio monitorate. Prestazioni di un controller di dominio possono essere facilmente confrontate tra tutti gli altri controller di dominio monitorate le strutture. Inoltre, è possibile visualizzare diversi contatori side-by, che è utile per la risoluzione dei problemi nel proprio ambiente.

![Monitoraggio](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Per impostazione predefinita, sono state preselezionate quattro contatori; Tuttavia, è possibile includere altri scegliendo il comando filtro e selezionando o deselezionando qualsiasi contatori desiderato. Inoltre, è possibile fare doppio clic su un grafico contatore delle prestazioni per aprire una nuova pala, che include le coordinate per ogni controller di dominio monitorate.

## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect dello stato dell'installazione dell'agente](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operazioni integrità](active-directory-aadconnect-health-operations.md)
* [Collegare mediante Azure Active Directory integrità con ADFS](active-directory-aadconnect-health-adfs.md)
* [Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect integrità domande frequenti](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
