<properties
   pageTitle="Protezione a livello di riga con Power BI incorporata"
   description="Ulteriori informazioni sulla sicurezza a livello di riga con Power BI incorporata"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Sicurezza a livello di riga con Power BI incorporata

Sicurezza a livello di riga (RLS) può essere utilizzato per limitare l'accesso degli utenti ai dati specifici all'interno di un report o un set di dati, che consente a più utenti diversi di utilizzare lo stesso report durante tutti i dati di diversi vedere. Power BI incorporato supporta ora configurato con RLS set di dati.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Per sfruttare RLS, è importante che capire tre concetti principali; Gli utenti, ruoli e le regole. Di seguito è illustrato in dettaglio ogni:

**Gli utenti** , questi sono gli utenti finali effettivi la visualizzazione dei report. In Power BI incorporati, gli utenti sono identificati dalla proprietà nome utente in un Token di App.

**Ruoli** : gli utenti appartengono ai ruoli. Un ruolo è un contenitore per le regole e può essere denominato qualcosa come "Direttore" o "Agente di vendita". In Power BI incorporati, gli utenti sono identificati dalla proprietà ruoli in un Token di App.

**Regole** : ruoli regole e queste regole sono i filtri effettivi stanno per essere applicato ai dati. Può trattarsi di semplice come "paese = Italia" o qualcosa di molto più dinamico.

### <a name="example"></a>Esempio

Per il resto di questo articolo verrà fornire un esempio di modifica RLS e che quindi che all'interno di un'applicazione incorporata. Questo esempio viene utilizzato il file PBIX [Esempio analisi al dettaglio](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Esempio analisi Retail Mostra le vendite per tutti i punti in una determinata catena. Senza RLS, indipendentemente da quale distretto responsabile accede e visualizza il report, vedranno gli stessi dati. Dirigenti ha stabilito ogni direttore dovrebbe essere visualizzato solo le vendite per gli archivi che gestiscono e a tale scopo, possiamo usare RLS.

RLS vengono creati in Power BI Desktop. Quando il set di dati e report vengono aperti, è possibile passare alla visualizzazione Diagramma per visualizzare lo schema:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Ecco alcuni aspetti da considerare con questo schema:

-   Tutte le misure, ad esempio **Total Sales**, sono archiviate nella tabella dei fatti **Sales** .
-   Esistono quattro tabelle delle dimensioni correlate aggiuntive: **elemento**, **ora**, **archiviare**e **distretto**.
-   Le frecce sulle linee di relazione indicano modi filtri possono passare da una tabella a un'altra. Ad esempio, se viene inserito un filtro data **[Data]**, nello schema corrente da filtrare solo verso il basso i valori nella tabella **Sales** . Senza altre tabelle da incidono questo filtro poiché tutte le frecce sulle linee di relazione scegliere la tabella delle vendite e non al computer.
-   La tabella **distretto** indica chi è il responsabile per ciascun:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

In base a questo schema, se si applica un filtro a colonna **Direttore** descritti nella tabella distretto e che filtra corrispondenze all'utente la visualizzazione del report, il filtro anche filtrerà verso il basso le tabelle di **archiviare** e **vendite** per mostrare solo i dati per tale particolare distretto manager.

Ecco come:

1.  Nella scheda modello, fare clic su **Gestione ruoli**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Creare un nuovo ruolo denominato **Gestione**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Nella tabella **distretto** immettere la seguente espressione DAX: **[direttore] = username ()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Per assicurarsi che utilizza le regole, nella scheda **modellazione** fare clic su **Visualizza i ruoli**e quindi immettere le operazioni seguenti:  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    I report ora verranno visualizzati i dati come se fosse effettuato l'accesso come **Andrea vendite**.

Applicazione del filtro, il modo in cui è stato fatto, filtrerà verso il basso tutti i record delle tabelle **distretto**, **archiviare**e **vendite** . Tuttavia, a causa la direzione del filtro sulle relazioni tra **le vendite** e **l'ora**, tabelle **Sales** ed **elemento**ed **elemento** e **l'ora** non verrà filtrate verso il basso.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Che può essere adatta per questo requisito, tuttavia, se non vogliamo responsabili per visualizzare gli elementi per cui non hanno tutte le vendite, è possibile attivare bidirezionale cross-filtro per la relazione e flusso il filtro di protezione in entrambe le direzioni. A tale scopo modificando la relazione tra **vendite** e un **elemento**, come segue:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

A questo punto, i filtri possono inoltre passare dalla tabella Sales nella tabella **articoli** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Nota** Se si Usa modalità DirectQuery per i dati, sarà necessario abilitare bidirezionale tra il filtro selezionando queste due opzioni:

1.  **File** -> **Opzioni e le impostazioni** -> **Caratteristiche Preview** -> **Abilita cross applicazione di filtri in entrambe le direzioni per DirectQuery**.
2.  **File** -> **Opzioni e le impostazioni** -> **DirectQuery** -> **consentire misura senza restrizioni in modalità DirectQuery**.


Per ulteriori informazioni sui filtri incrociati bidirezionale, scaricare il white paper [bidirezionale funzionalità di filtri incrociati in SQL Server Analysis Services 2016 e Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Questa racchiude tutte le operazioni da effettuare in Power BI Desktop, ma è disponibile un altro elemento di lavoro che deve essere eseguita per rendere il RLS regole definito lavoro in Power BI incorporato. Gli utenti di autenticazione e autorizzazione dall'applicazione e App token vengono utilizzati per concedere l'utente potrà accedere a un determinato report di Power BI incorporato. Power BI incorporato non contiene informazioni specifiche chi è l'utente. Per RLS funzionino, è necessario passare alcune informazioni di contesto aggiuntive come parte del token app:
-   **nome utente** (facoltativo): utilizzata con RLS si tratta di una stringa che può essere utilizzata per identificare l'utente quando l'applicazione di regole RLS. Vedere Utilizzare la protezione a livello con Power BI incorporata della riga
-   **ruoli** : una stringa contenente i ruoli per selezionare quando l'applicazione di regole di sicurezza a livello di riga. Se si passa a più di un ruolo, devono essere passati come matrice di stringhe.

Se la proprietà di nome utente è presenta, è anche necessario passare almeno un valore in ruoli.

Token app completo un aspetto simile al seguente:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

A questo punto, con tutti gli elementi insieme, quando un utente accede all'applicazione per visualizzare il report solo potrà visualizzare i dati che possono visualizzare, come definito per la protezione a livello di riga.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Vedere anche
[Protezione a livello di riga (RLS) con Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
