<properties
    pageTitle="Sincronizzazione di Azure AD Connect: Gestione servizi di sincronizzazione UI | Microsoft Azure"
    description="Comprendere la scheda connettori in Gestione servizi di sincronizzazione di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronizzazione di Azure AD Connect: Gestione servizi di sincronizzazione

[Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca nel Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Nella scheda connettori viene utilizzata per gestire tutti i sistemi a che il motore di sincronizzazione sia connesso.

## <a name="connector-actions"></a>Azioni di connettore

Azione | Commento
--- | ---
Creare | Non utilizzare. Per la connessione a ulteriori insiemi di strutture di Active Directory, utilizzare la procedura guidata installazione.
Proprietà | Usato per dominio e il filtraggio di unità Organizzativa.
[Elimina](#delete) | Utilizzato per eliminare i dati nell'area connettore o eliminare una connessione a un insieme di strutture.
[Configurare profili di esecuzione](#configure-run-profiles) | Eccetto i domini applicazione del filtro, elemento da configurare qui. È possibile usare questa azione per visualizzare i profili di esecuzione già configurati.
Correre | Utilizzato per avviare l'esecuzione di una variante di un profilo.
Interrompi | Arresta un connettore attualmente in esecuzione un profilo.
Connettore di esportazione | Non utilizzare.
Connettore di importazione | Non utilizzare.
Connettore di aggiornamento | Non utilizzare.
Aggiornamento dello Schema | Aggiorna lo schema memorizzati nella cache. È preferibile usare invece l'opzione della procedura guidata installazione dopo che anche gli aggiornamenti sincronizzare regole.
[Spazio di connettore di ricerca](#search-connector-space) | Usato per individuare gli oggetti e per [seguire un oggetto e i relativi dati tramite il sistema](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Elimina
L'azione di eliminazione viene usato per due diverse operazioni.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L'opzione **Elimina solo spazio connettore** rimuove tutti i dati, mantenendo la configurazione.

L'opzione **Elimina connettore e spazio connettore** rimuove i dati e la configurazione. Questa opzione viene utilizzata quando non si desidera connettersi a un insieme di strutture più.

Entrambe le opzioni sincronizzare tutti gli oggetti e aggiornare gli oggetti del metaverse. Questa azione è un'operazione di esecuzione prolungata.

### <a name="configure-run-profiles"></a>Configurare profili di esecuzione
Questa opzione consente di visualizzare i profili di esecuzione configurati per un connettore.

![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Spazio di connettore di ricerca
L'azione di spazio connettore di ricerca è utile per individuare gli oggetti e risolvere i problemi di dati.

![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Iniziare scegliendo un **ambito**. È possibile eseguire ricerche in base ai dati (nome distinto relativo, nome distinto, ancoraggio, struttura secondaria), o stato dell'oggetto (tutte le altre opzioni).  
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Se ad esempio eseguire una ricerca di struttura secondaria, viene visualizzato a tutti gli oggetti in un'unità Organizzativa.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) la griglia è possibile selezionare un oggetto, scegliere **proprietà**e [seguirlo](#follow-an-object-and-its-data-through-the-system) dallo spazio connettore origine, tramite il metaverse e per lo spazio di connettore di destinazione.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Seguire un oggetto e i relativi dati tramite il sistema
Durante la risoluzione di un problema con i dati, seguire un oggetto dallo spazio di connettore origine nel Metaverse e alla destinazione spazio connettore è una procedura chiave per comprendere il motivo per cui non è dati i valori previsti.

### <a name="connector-space-object-properties"></a>Proprietà oggetto spazio connettore
**Importazione**  
Quando si apre un oggetto cs, sono disponibili diverse schede nella parte superiore. Scheda **Importa** Mostra i dati che viene gestiti dopo l'importazione.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) il **Valore precedente** mostra cosa è memorizzata nel sistema e il **Nuovo valore** quali è stato ricevuto dal sistema di origine e non è stato ancora applicato. In questo caso, poiché non esiste un errore di sincronizzazione, non è applicata la modifica.

**Errore**  
La pagina di errore è visibile solo se si è verificato un problema con l'oggetto. Visualizzare i dettagli della pagina di operazioni per ulteriori informazioni su come [risolvere gli errori di sincronizzazione](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Derivazione**  
La scheda derivazione Mostra come oggetto spazio connettore corrisponde alla riga dell'oggetto metaverse. È possibile visualizzare il connettore importati ultima modifica dal sistema connesso e le regole applicate per compilare i dati nel metaverse.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) nella colonna **azione** , è possibile visualizzare una regola di sincronizzazione **in ingresso** con l'azione **provisioning**. Che indica come l'oggetto di spazio di connettore è presenta, l'oggetto metaverse rimane. Se l'elenco delle regole di sincronizzazione include invece una regola di sincronizzazione con **disposizione**e la direzione **in uscita** , significa che questo oggetto viene eliminato quando l'oggetto metaverse viene eliminato.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) è anche possibile visualizzare nella colonna **PasswordSync** che lo spazio del connettore in ingresso può contribuire alle modifiche apportate alla password poiché una regola di sincronizzazione ha valore **vero**. Questa password viene quindi inviata al Azure Active Directory mediante la regola in uscita.

Dalla scheda derivazione, è possibile ottenere il metaverse facendo clic su [Proprietà oggetto Metaverse](#metaverse-object-properties).

Nella parte inferiore di tutte le schede sono due pulsanti: **Anteprima** e **Registro**.

**Anteprima**  
La pagina di anteprima viene utilizzata per sincronizzare un solo oggetto. È utile se si sono di risoluzione dei problemi di alcune regole di sincronizzazione dei clienti e per visualizzare l'effetto di una modifica di un singolo oggetto. È possibile scegliere tra **Sincronizzazione completa** e **sincronizzazione Delta**. È anche possibile scegliere tra **Generare anteprima**, in modo da mantenere solo la modifica della memoria ed **Eseguire il Commit anteprima**, tutte le fasi assume la forma di spazi di connettore di destinazione.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) è possibile controllare l'oggetto e quale regola applicata per il flusso di un determinato attributo.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Log**  
La pagina Log viene utilizzata per visualizzare lo stato di sincronizzazione delle password e la cronologia, per ulteriori informazioni, vedere [risoluzione dei problemi di sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) .

### <a name="metaverse-object-properties"></a>Proprietà oggetto Metaverse
**Attributi**  
Nella scheda attributi, è possibile visualizzare i valori e il connettore autori contribuito alle diverse.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**connettori**  
Nella scheda connettori Mostra tutti gli spazi di connettore che hanno una rappresentazione dell'oggetto.
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) questa scheda è inoltre possibile passare all' [oggetto spazio connettore](#connector-space-object-properties).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
