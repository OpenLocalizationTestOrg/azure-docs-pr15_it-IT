
<properties
    pageTitle="Utilizzare Azure Active Directory connettersi dello stato di sincronizzazione | Microsoft Azure"
    description="Questa è la pagina di Azure Active Directory connettersi integrità che verrà illustrate come monitorare sincronizzazione di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione
La documentazione seguente è specifica di monitoraggio Azure AD Connect (sincronizzazione) con Azure Active Directory connettersi integrità.  Per informazioni sul controllo ADFS con Azure Active Directory connettersi integrità vedere [Integrità connettersi con Azure Active Directory con ADFS](active-directory-aadconnect-health-adfs.md). Inoltre, per informazioni sul monitoraggio dei servizi di dominio Active Directory con Azure Active Directory connettersi integrità vedere [Integrità connettersi con Azure Active Directory con Active Directory](active-directory-aadconnect-health-adds.md).

![Azure AD Connect integrità per la sincronizzazione](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Avvisi per Azure Active Directory connettersi integrità per la sincronizzazione
Sezione criteri di Azure Active Directory connettersi integrità avvisi sincronizzazione fornisce l'elenco di avvisi attivi. Ogni avviso include informazioni rilevanti, procedure di risoluzione e collegamenti alla documentazione correlata. Selezionando un avviso attivo o risolto verrà visualizzata una nuova pala con informazioni aggiuntive, nonché i passaggi da che eseguire per risolvere l'avviso e collegamenti a documentazione aggiuntiva. È anche possibile visualizzare i dati cronologici sugli avvisi che sono stati risolti in passato.

Selezionando un avviso da che verrà fornito con informazioni aggiuntive, nonché i passaggi eseguire per risolvere l'avviso e collegamenti a documentazione aggiuntiva.

![Errore di sincronizzazione Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Valutazione limitata di avvisi
Se la configurazione predefinita non utilizza Azure AD Connect (ad esempio, se il filtro degli attributi viene modificato da configurazione predefinita per una configurazione personalizzata), l'agente di Azure Active Directory connettersi integrità verrà non caricare gli eventi di errore correlati Azure AD Connect.

In questo modo la valutazione di avvisi dal servizio. Verrà visualizzato un banner che indica la condizione nel portale di Azure sotto il servizio.

![Azure AD Connect integrità per la sincronizzazione](./media/active-directory-aadconnect-health-sync/banner.png)

È possibile modificare facendo clic su "Impostazioni" e consentire l'agente di Azure Active Directory connettersi integrità caricare tutti i registri di errori.

![Azure AD Connect integrità per la sincronizzazione](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Informazioni di sincronizzazione
Gli amministratori spesso è necessario conoscere il tempo necessario per le modifiche di sincronizzazione di Azure Active Directory e la quantità di modifiche in corso. Questa caratteristica offre un modo semplice per visualizzare questa situazione utilizzando i grafici seguenti:   

- Latenza delle operazioni di sincronizzazione
- Linea di tendenza Modifica oggetto

### <a name="sync-latency"></a>Latenza di sincronizzazione
Questa caratteristica offre una grafica tendenza latenza delle operazioni di sincronizzazione (importazione, esportazione, ecc.) per i connettori.  Fornisce un modo semplice e rapido per comprendere non solo la latenza delle operazioni (grande se si dispone di un elevato numero di cambiamenti), ma anche un modo per rilevare alterazioni latenza che potrebbe richiedere un'ulteriore analisi.

![Latenza di sincronizzazione](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Per impostazione predefinita, viene visualizzata solo la latenza dell'operazione di 'Esportazione' per il connettore di Azure Active Directory.  Per vedere altre operazioni sul connettore oppure per visualizzare le operazioni da altri connettori, fare clic sul grafico, selezionare Modifica grafico o fare clic sul pulsante "Modifica latenza grafico" e scegliere l'operazione specifica e connettori.

### <a name="sync-object-changes"></a>Modifiche all'oggetto sincronizzazione
Questa caratteristica consente di una linea di tendenza grafico del numero di modifiche che vengono valutate ed esportati in Azure Active Directory.  Oggi, si tenta di raccogliere le informazioni dai log di sincronizzazione è difficile.  Il grafico offre, non solo un modo più semplice di monitoraggio il numero di modifiche che si verificano nel proprio ambiente, ma anche una visualizzazione di errori che si verificano.

![Latenza di sincronizzazione](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Segnalazione errori di sincronizzazione di livello oggetto (Preview)
Questa funzionalità fornisce un report sugli errori di sincronizzazione che possono verificarsi quando i dati di identità vengono sincronizzati tra Windows Server Active Directory e Azure Active Directory utilizzando Azure AD Connect.

- Il report si riferisce errori registrati, il client di sincronizzazione (Azure AD Connect versione 1.1.281.0 o versioni successive)
- Includa gli errori che si sono verificati durante l'ultima operazione di sincronizzazione nel motore di sincronizzazione. ("Esportazione" Azure Active Directory Connector.)
- Azure agente di integrità di connettersi Active Directory per la sincronizzazione deve avere una connessione in uscita per i punti finali necessari per il report includere i dati più recenti. Vedere la [sezione requisiti](active-directory-aadconnect-health-agent-install.md#Requirements) per informazioni dettagliate.
- Il report è **aggiornato ogni 30 minuti** utilizzando i dati caricati dall'agente di Azure Active Directory connettersi integrità per la sincronizzazione.
Fornisce le seguenti funzionalità chiave

    - Categorizzazione degli errori
    - Elenco di oggetti con errore per categoria
    - Tutti i dati sugli errori in un'unica posizione
    - Sul lato da confronto di oggetti con un errore simile a causa di un conflitto
    - Scaricare il report di errore come un CV (disponibile a breve)

### <a name="categorization-of-errors"></a>Categorizzazione degli errori
Il report classifica gli errori di sincronizzazione esistente nelle categorie seguenti:

| Categoria | Descrizione |
| -------------- | ----------- |
| Attributo duplicato | Errori quando si tenta di Azure AD Connect creare o aggiornano gli oggetti con valori duplicati di uno o più attributi in Azure Active Directory che deve essere univoco in un Tenant, ad esempio proxyAddresses, UserPrincipalName. |
| Dati non corrispondenti | Errori quando non è quella sfumato in corrispondenza degli oggetti che causano errori di sincronizzazione. |
| Errore di convalida dei dati | Errori a causa di dati non validi, ad esempio caratteri non supportati in attributi importanti, ad esempio UserPrincipalName, formattare gli errori non convalidati prima di essere scritte in Azure Active Directory.|
| Attributi di grandi dimensioni | Errori quando uno o più attributi sono maggiori di dimensione consentiti, lunghezza o conteggio.|
| Altri | Tutti gli altri errori che non rientrano in categorie precedenti. In base ai suggerimenti, questa categoria verrà suddiviso in sottocategorie.

![Riepilogo Report di errore di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![categorie dei Report di errore di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Elenco di oggetti con errore per categoria
Drill-down di ogni categoria costituiranno l'elenco di oggetti con l'errore in quella categoria.
![Elenco Report errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Dettagli sull'errore
Seguire i dati è disponibile in vista di dettaglio per ogni errore

- Identificatori per l' *Oggetto Active Directory* correlate
- Identificatori per l' *Oggetto di Azure Active Directory* correlate (se applicabile)
- Descrizione dell'errore e procedura per la risoluzione
- Articoli correlati

![Dettagli segnalazioni errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Scaricare il report di errore in formato CSV
Questa funzionalità è disponibile a breve. Continuare a leggere per necessari più aggiornamenti.



## <a name="related-links"></a>Collegamenti correlati
* [Risoluzione dei problemi durante la sincronizzazione](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Attributo duplicato resilienza](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect dello stato dell'installazione dell'agente](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operazioni integrità](active-directory-aadconnect-health-operations.md)
* [Collegare mediante Azure Active Directory integrità con ADFS](active-directory-aadconnect-health-adfs.md)
* [Collegare mediante Azure Active Directory integrità con Active Directory](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integrità domande frequenti](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
