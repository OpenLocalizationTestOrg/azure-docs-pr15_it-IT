<properties
    pageTitle="Sincronizzazione di Azure AD Connect: configurare il filtro | Microsoft Azure"
    description="In questo articolo viene spiegato come configurare il filtro di sincronizzazione di Azure AD Connect."
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
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronizzazione di Azure AD Connect: configurazione del filtro
Uso dei filtri, è possibile controllare gli oggetti che devono essere visualizzati in Azure Active Directory dalla directory locale. La configurazione predefinita consente di accedere a tutti gli oggetti in tutti i domini strutture configurati. Si tratta in genere, la configurazione consigliata. Gli utenti finali utilizzando carichi di lavoro di Office 365, ad esempio Exchange Online e in Skype for Business, trarre vantaggio da un elenco indirizzi globale completa in modo che possano inviare posta elettronica e tutti gli utenti di chiamata. Con la configurazione predefinita ricevono l'esperienza stesso che avviene per con un'implementazione locale di Exchange o Lync.

In alcuni casi, è necessario apportare alcune modifiche alla configurazione predefinita. Ecco alcuni esempi:

- Si prevede di utilizzare [più Azure topologia di Active directory](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). È necessario applicare un filtro per controllare l'oggetto che deve essere sincronizzato per un determinato directory di Azure Active Directory.
- Si esegue un progetto pilota per Azure o Office 365 e si vuole che solo un sottoinsieme di utenti in Azure Active Directory. Nel progetto pilota small, non è importante avere un elenco indirizzi globale completo per dimostrare la funzionalità.
- Si dispone di più account del servizio e altri non personali che non si desidera in Azure Active Directory.
- Per motivi di conformità non eliminare qualsiasi utente account locale. È solo disattivarli. Ma in Azure Active Directory solo account attivi deve essere presente.

In questo articolo illustra come configurare i diversi metodi di filtri.

> [AZURE.IMPORTANT]Microsoft non supporta la modifica o un'operazione di sincronizzazione di Azure AD Connect all'esterno di tali azioni descritte formale. Le seguenti azioni potrebbe causare in uno stato incoerente o non supportato di sincronizzazione di Azure AD Connect e di conseguenza, Microsoft non fornisce supporto tecnico per questi tipi di distribuzione.

## <a name="basics-and-important-notes"></a>Nozioni di base e note importanti
Sincronizzazione di Azure AD Connect, è possibile attivare il filtro in qualsiasi momento. Se si inizia con una configurazione predefinita di sincronizzazione della directory e quindi configurare il filtro, gli oggetti che vengono filtrati non vengono sincronizzati con Azure Active Directory. In seguito a questa modifica, vengono eliminati tutti gli oggetti in Azure Active Directory che in precedenza sono stati sincronizzati ma quindi sono stati filtrati in Azure Active Directory.

Prima di iniziare ad apportare viene modificato in filtro, verificare che si [Disabilita l'attività pianificata](#disable-scheduled-task) in modo che non vengono esportati accidentalmente le modifiche che non è ancora verificato per essere corretta.

Poiché il filtro è possibile rimuovere molti oggetti contemporaneamente, si desidera verificare che i nuovi filtri siano corretti prima di iniziare l'esportazione di tutte le modifiche in Azure Active Directory. Dopo avere completato la procedura di configurazione, si consiglia di seguire i [passaggi di verifica](#apply-and-verify-changes) prima di esportare e apportare modifiche a Azure Active Directory.

Per proteggere gli utenti di eliminare molti oggetti accidentalmente, la caratteristica [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) è per impostazione predefinita. Se si eliminano molti oggetti a causa di filtro (500 per impostazione predefinita), è necessario seguire la procedura descritta in questo articolo per consentire le eliminazioni elaborata di Azure Active Directory.

Se si usa una compilazione prima 2015 novembre ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), apportare modifiche alla configurazione di filtro e utilizzare la sincronizzazione delle password, è necessario attivare una sincronizzazione completa di tutte le password dopo aver completato la configurazione. Per informazioni sulle procedure avviare una sincronizzazione completa password vedere [Trigger una sincronizzazione completa di tutte le password](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se si è in 1.0.9125 o versioni successive, quindi l'azione regolare **la sincronizzazione completa** anche calcola se devono essere sincronizzate le password e questa operazione aggiuntiva non è più necessaria.

Se gli oggetti **utente** sono stati eliminati inavvertitamente in Azure Active Directory a causa di un errore di filtro, è possibile ricreare gli oggetti utente in Active Directory Azure rimuovendo le configurazioni di filtro e quindi sincronizzare nuovamente la directory. Questa azione consente di ripristinare gli utenti dal Cestino in Azure Active Directory. Tuttavia, non è possibile annullare l'eliminazione dei tipi di oggetto. Ad esempio, se si elimina per sbaglio un gruppo di sicurezza e che è stato usato per ACL una risorsa, il gruppo e il relativo ACL non può essere recuperato.

Azure AD Connect Elimina solo gli oggetti che dispone di una sola volta considerato nell'ambito. Se esistono oggetti in Azure Active Directory che sono stati creati da un altro motore di sincronizzazione e gli oggetti non sono presenti nell'ambito, aggiunta di filtri non rimuoverli. Ad esempio, se si inizia con un server DirSync e creata una copia completa dell'intera directory in Azure Active Directory e si installa un nuovo server di sincronizzazione di Azure AD Connect in parallelo con il filtro attivato dall'inizio, non rimuove gli oggetti aggiuntivi creati da DirSync.

Configurazione del filtro viene mantenuta quando si installa o eseguire l'aggiornamento a una versione più recente di Azure AD Connect. È sempre consigliabile verificare che la configurazione non è stato inavvertitamente modificata dopo l'aggiornamento a una versione più recente prima di eseguire la sincronizzazione prima ciclo.

Se si dispone di più di un insieme di strutture, le configurazioni di filtro descritte in questo argomento devono applicate a ogni foresta (presupponendo che si desidera che la stessa configurazione per tutti gli elementi).

### <a name="disable-scheduled-task"></a>Disattivare l'attività pianificate
Per disattivare l'utilità di pianificazione predefiniti che attiva un ciclo di sincronizzazione ogni 30 minuti, procedere come segue:

1. Passare a un PowerShell prompt.
2. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $False` per disattivare l'utilità di pianificazione.
3. Apportare le modifiche come descritto in questo argomento.
4. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $True` per attivare nuovamente l'utilità di pianificazione.

**Se si utilizza una compilazione di Azure AD Connect prima 1.1.105.0**  
Per disattivare l'attività pianificata che attiva un ciclo di sincronizzazione ogni tre ore, procedere come segue:

1. Avviare **l'Utilità di pianificazione** dal menu start.
2. Direttamente in **Libreria Utilità di pianificazione**, individuare l'attività denominata **utilità di pianificazione di sincronizzazione di Azure Active Directory**, pulsante destro del mouse e selezionare **Disattiva**.  
![Utilità di pianificazione](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. È ora possibile apportare modifiche alla configurazione ed eseguire manualmente il motore di sincronizzazione dalla console di **gestione di servizi di sincronizzazione** .

Dopo avere completato tutte le modifiche filtro, non dimenticare di provenire indietro e **abilitare** l'attività.

## <a name="filtering-options"></a>Opzioni di filtro
I tipi di configurazione di filtro seguenti possono essere applicati allo strumento di sincronizzazione della Directory:

- [**In base a gruppo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtro in base a un singolo gruppo può essere configurato solo durante l'installazione iniziale usando la procedura guidata installazione. Non è ulteriormente coperta in questo argomento.

- [**Dominio**](#domain-based-filtering): questa opzione consente di selezionare quali domini per la sincronizzazione di Azure Active Directory. È anche possibile aggiungere e rimuovere i domini dalla configurazione del motore di sincronizzazione se si apportano modifiche all'infrastruttura locale dopo l'installazione di sincronizzazione di Azure AD Connect.

- [**Basato su unità di organizzativo**](#organizational-unitbased-filtering): questa opzione di filtro consente di selezionare le unità organizzative la sincronizzazione di Azure Active Directory. Questa opzione è per tutti i tipi di oggetto selezionate unità organizzative.

- [**Basata su attributo**](#attribute-based-filtering): questa opzione consente di filtrare gli oggetti in base ai valori attributo sugli oggetti. È anche possibile filtri diversi per diversi tipi di oggetto.

È possibile utilizzare le opzioni di filtro più nello stesso momento. Ad esempio, è possibile utilizzare il filtro basato su OU per includere solo gli oggetti in un'unità Organizzativa e lo stesso tempo basato su attributi filtro per filtrare ulteriormente i vari oggetti. Quando si utilizzano più metodi di filtri, i filtri di utilizzano un logico AND tra i filtri.

## <a name="domain-based-filtering"></a>Il filtro basato sul dominio
In questa sezione vengono descritte le procedure per configurare il filtro di dominio. Se sono stati aggiunti o rimossi domini della foresta dopo l'installazione di Azure AD Connect, è necessario aggiornare la configurazione del filtro.

Il modo migliore per modificare il filtro basato sul dominio è eseguendo l'installazione guidata e modificare [dominio e unità organizzative filtro](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata è automazione tutte le attività descritte in questo argomento.

È necessario seguire questa procedura solo se per qualche motivo si riesce a eseguire l'installazione guidata.

Configurazione del filtro basato su dominio è costituito da questa procedura:

- [Selezionare i domini](#select-domains-to-be-synchronized) da includere nel processo di sincronizzazione.
- Per ogni dominio aggiunto ed è stato rimosso, regolare [eseguire profili](#update-run-profiles).
- [Applica e verificare le modifiche](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Selezionare i domini da sincronizzare
**Per impostare il filtro di dominio, eseguire la procedura seguente:**

1. Accedere a un server che esegue sincronizzazione di Azure AD Connect utilizzando un account è un membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare il **Servizio di sincronizzazione** dal menu start.
3. Selezionare **i connettori** e nell'elenco **connettori** , selezionare il connettore con il tipo di **Servizi di dominio Active Directory**. **Azioni**, selezionare **proprietà**.  
![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Fare clic su **Configura partizioni**.
5. Nell'elenco **Selezionare partizioni** selezionare e deselezionare i domini in base alle esigenze. Verificare che siano selezionate solo le partizioni da sincronizzare.  
![Partizioni](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Se è stato modificato il locale dell'infrastruttura di Active Directory e domini aggiunti o rimossi dall'insieme di strutture, quindi fare clic sul pulsante **Aggiorna** per ottenere un elenco aggiornato. Quando si aggiornano, viene chiesto di credenziali. Fornire credenziali di accesso in lettura di Active Directory locale. Se non è obbligatoria l'utente che è già presenti nella finestra di dialogo.  
![Aggiornamenti necessari](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Al termine, chiudere la finestra di dialogo **proprietà** , fare clic su **OK**. Se è stato rimosso domini dall'insieme di strutture, un menu a comparsa messaggio che indica che un dominio è stato rimosso e la configurazione verranno rimosse.
7. Continuare a modificare [eseguire profili](#update-run-profiles).

### <a name="update-run-profiles"></a>Aggiornare i profili di esecuzione
Se è stato aggiornato il filtro di dominio, è necessario aggiornare i profili di esecuzione.

1. Nell'elenco **connettori** assicurarsi che sia selezionato il connettore modificato nel passaggio precedente. Selezionare **Configurare profili di eseguire** **Azioni**.  
![Connettore eseguire profili](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

È necessario modificare i profili seguenti:

- Importazione completa
- Sincronizzazione completa
- Importazione delta
- Sincronizzazione delta
- Esportazione

Per ognuna delle cinque profili, eseguire le operazioni seguenti per ogni dominio **aggiunto** :

1. Selezionare il profilo di esecuzione e fare clic su **Nuova azione**.
2. Nella pagina **Configura passaggio** nell'elenco a discesa **tipo** selezionare il tipo di passaggio con lo stesso nome del profilo che si sta configurando. Fare clic su **Avanti**.  
![Connettore eseguire profili](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Nella pagina **Configurazione del connettore** nell'elenco a discesa **partizione** selezionare il nome del dominio che aggiunto al filtro di dominio.  
![Connettore eseguire profili](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Per chiudere la finestra di dialogo **Configura profilo di esecuzione** , fare clic su **Fine**.

Per ognuna delle cinque profili, eseguire le operazioni seguenti per ogni dominio **rimosso** :

1. Selezionare il profilo di esecuzione.
2. Se il **valore** dell'attributo **partizione** GUID, selezionare il passaggio Esegui e fare clic su **Elimina passaggio**.  
![Connettore eseguire profili](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Il risultato dovrebbe essere che ogni dominio che si desidera sincronizzare deve essere elencato come un passaggio per ogni profilo di esecuzione.

Per chiudere la finestra di dialogo **Configurare profili di esecuzione** , fare clic su **OK**.

- Per completare la configurazione [Applica e verificare le modifiche](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtro basato su unità di organizzativo
Il modo migliore per modificare il filtro basato su unità Organizzativa è eseguendo l'installazione guidata e modificare [dominio e unità organizzative filtro](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata è automazione tutte le attività descritte in questo argomento.

È necessario seguire questa procedura solo se per qualche motivo si riesce a eseguire l'installazione guidata.

**Per configurare il filtro aziendale basato su unità –, eseguire la procedura seguente:**

1. Accedere a un server che esegue sincronizzazione di Azure AD Connect utilizzando un account è un membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare il **Servizio di sincronizzazione** dal menu start.
3. Selezionare **i connettori** e nell'elenco **connettori** , selezionare il connettore con il tipo di **Servizi di dominio Active Directory**. **Azioni**, selezionare **proprietà**.  
![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Fare clic su **Configura partizioni**, selezionare il dominio che si desidera configurare e quindi fare clic su **contenitori**.
5. Quando richiesto, immettere le eventuali credenziali di accesso in lettura di Active Directory locale. Se non è obbligatoria l'utente che è già presenti nella finestra di dialogo.
6. Nella finestra di dialogo **Selezionare i contenitori** , deselezionare le unità organizzative che non si desidera sincronizzare con il servizio directory cloud e quindi fare clic su **OK**.  
![UNITÀ ORGANIZZATIVA](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - Il contenitore **computer** dovrebbe essere selezionato per i computer Windows 10 correttamente la sincronizzazione di Azure Active Directory. Se il computer di dominio risiede in altre unità organizzative, assicurarsi che quelli selezionati.
  - Se si dispone di più insiemi di strutture con relazioni di trust, il contenitore **ForeignSecurityPrincipals** dovrebbe essere selezionato. In questo contenitore consente l'appartenenza ai gruppi di sicurezza tra insiemi di strutture da risolvere.
  - Se è stata attivata la caratteristica di writeback di dispositivo, l'unità Organizzativa **RegisteredDevices** dovrebbe essere selezionata. Se si usa un'altra funzione writeback, ad esempio writeback di gruppo, verificare che siano selezionati questi percorsi.
  - Selezionare l'unità Organizzativa dove si trovano gli utenti, InetOrgPerson, gruppi, i contatti e computer. Nella figura, tutti si trovano in OU ManagedObjects.
7. Al termine, chiudere la finestra di dialogo **proprietà** , fare clic su **OK**.
8. Per completare la configurazione [Applica e verificare le modifiche](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Il filtro basato su attributo
Verificare che siano presenti le 2015 novembre ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) o in un secondo momento creare per questa procedura per l'uso.

Filtro in base a attributo è il modo più flessibile per filtrare gli oggetti. È possibile utilizzare la potenza di [provisioning dichiarativi](active-directory-aadconnectsync-understanding-declarative-provisioning.md) per definire quasi ogni aspetto di quando un oggetto da sincronizzare con Azure Active Directory.

È possibile applicare filtri entrambe le [in ingresso](#inbound-filtering) da Active Directory nel Metaverse e [in uscita](#outbound-filtering) da metaverse di Azure Active Directory. Si consiglia di applicare il filtro in entrata, poiché è più semplice da gestire. Il filtro in uscita deve essere utilizzato solo se è necessario per partecipare a oggetti da più di un insieme di strutture affinché la valutazione abbiano effetto.

### <a name="inbound-filtering"></a>In ingresso filtro
Filtro di base in ingresso è tramite le impostazioni predefinite nel punto in cui gli oggetti di Azure Active Directory devono avere la cloudFiltered attributo metaverse non è impostata su un valore da sincronizzare. Se il valore dell'attributo è impostato su **True**, l'oggetto non è sincronizzato. Dovrebbe essere impostato su **False** in base alla progettazione. Per assicurarsi che altre regole sono in grado di fornire un valore, questo attributo solo deve per contenere valori **vero** o **NULL** (assenti).

In filtro in ingresso, utilizzare la potenza di **ambito** per stabilire quali oggetti deve essere o non devono essere sincronizzati. Verrà visualizzata nel punto in cui è apportare modifiche in modo da adattarlo ai requisiti dell'organizzazione. Il modulo dell'ambito include **gruppo** e **clausola** per determinare se una regola di sincronizzazione deve essere nell'ambito. Un **gruppo** contiene uno o più **clausola**. Esiste un AND logico tra più clausole e logico o tra più gruppi.

Analizzate un esempio:  
![Ambito](./media/active-directory-aadconnectsync-configure-filtering/scope.png) mentre dovrebbe essere come **(reparto = IT) o (reparto = vendite e c = US)**.

Negli esempi e procedura indicata di seguito si utilizza l'oggetto utente ad esempio, ma è possibile utilizzare questo per tutti i tipi di oggetto.

Negli esempi seguenti il valore di precedenza iniziare con 500. Questo valore garantisce che le regole vengono valutate dopo le regole di fuori della casella (precedenza minore; maggiore valore numerico).

#### <a name="negative-filtering-do-not-sync-these"></a>Applicazione di filtri, "non vengono sincronizzati questi" negativi
Nell'esempio seguente escludere (non sincronizzate) tutti gli utenti in cui **extensionAttribute15** hanno valore **NoSync**.

1. Accedere a un server che esegue sincronizzazione di Azure AD Connect utilizzando un account è un membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare **La sincronizzazione regole Editor** dal menu start.
3. Verificare che sia selezionata **in ingresso** e fare clic su **Aggiungi nuova regola**.
4. Assegnare alla regola un nome descrittivo, ad esempio "*In da Active Directory: utente DoNotSyncFilter*". Selezionare l'insieme di strutture corretto, **utente** come **tipo di oggetto CS**e **persona** come **tipo di oggetto MV**. Come **Tipo di collegamento**, selezionare **Partecipa** e nell'ordine di precedenza digitare un valore non viene utilizzato da un'altra regola di sincronizzazione (ad esempio 500) e quindi fare clic su **Avanti**.  
![In ingresso 1 Descrizione](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. In **filtro ambito**, fare clic su **Aggiungi gruppo**, fare clic su **Aggiungi clausola**e nell'attributo selezionare **ExtensionAttribute15**. Assicurarsi che l'operatore è **uguale** a e quindi digitare il valore **NoSync** nella casella valore. Fare clic su **Avanti**.  
![In ingresso ambito 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Lasciare vuote le regole di **partecipare** e quindi fare clic su **Avanti**.
7. Fare clic su **Aggiungi trasformazione**, selezionare **ExchangeRate di FlowType** a **costante**, selezionare l' attributo Target **cloudFiltered** e nella casella di testo di origine, digitare **True**. Fare clic su **Aggiungi** per salvare la regola.  
![In ingresso trasformazione 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Per completare la configurazione [Applica e verificare le modifiche](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Applicazione di filtri, "solo sincronizzare questi" positivo
Espressione di filtro positivo può essere più complessa poiché è necessario anche prendere in considerazione gli oggetti che non sono più evidenti da sincronizzare, ad esempio delle sale riunioni.

L'opzione di filtro positivo richiede due regole di sincronizzazione. Una (o più) con l'ambito corretto di oggetti da sincronizzare e un secondo sincronizzazione completa catture di regole che filtrare tutti gli oggetti che non sono ancora stati identificati come oggetto che deve essere sincronizzato.

Nell'esempio seguente si sincronizzano solo gli oggetti utente nel punto in cui l'attributo di reparto è il valore **vendite**.

1. Accedere a un server che esegue sincronizzazione di Azure AD Connect utilizzando un account è un membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare **La sincronizzazione regole Editor** dal menu start.
3. Verificare che sia selezionata **in ingresso** e fare clic su **Aggiungi nuova regola**.
4. Assegnare un nome descrittivo, ad esempio "*da Active Directory: utente vendite sincronizzati*" alla regola. Selezionare l'insieme di strutture corretto, **utente** come **tipo di oggetto CS**e **persona** come **tipo di oggetto MV**. Come **Tipo di collegamento**, selezionare **Partecipa** e nell'ordine di precedenza digitare un valore non viene utilizzato da un'altra regola di sincronizzazione (ad esempio 501) e quindi fare clic su **Avanti**.  
![In ingresso descrizione 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. In **filtro ambito**, fare clic su **Aggiungi gruppo**, fare clic su **Aggiungi clausola**e nell'attributo selezionare **reparto**. Assicurarsi che l'operatore è **uguale** a e quindi digitare il valore **vendite** nella casella valore. Fare clic su **Avanti**.  
![In ingresso ambito 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Lasciare vuote le regole di **partecipare** e quindi fare clic su **Avanti**.
7. Fare clic su **Aggiungi trasformazione**, selezionare **ExchangeRate di FlowType** a **costante**, selezionare l' attributo Target **cloudFiltered** e nella casella di testo di origine, digitare **False**. Fare clic su **Aggiungi** per salvare la regola.  
![In ingresso trasformazione 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Si tratta di un caso particolare in cui è impostato cloudFiltered in modo esplicito su False.

    A questo punto è necessario creare la regola di sincronizzazione di tutte le catture.

8. Assegnare alla regola un nome descrittivo, ad esempio "*In da Active Directory: filtro utente catture completa*". Selezionare l'insieme di strutture corretto, **utente** come **tipo di oggetto CS**e **persona** come **tipo di oggetto MV**. Come **Tipo di collegamento**, selezionare **Partecipa** e nell'ordine di precedenza digitare un valore non viene utilizzato da un'altra regola di sincronizzazione (ad esempio 600). Si è selezionato un'ordine di precedenza valore superiore (precedenza inferiore) rispetto alla regola di sincronizzazione precedente ma anche a sinistra di spazio in modo che è possibile aggiungere altre regole filtro di sincronizzazione in un secondo momento, quando si desidera iniziare la sincronizzazione di altri reparti. Fare clic su **Avanti**.  
![In ingresso descrizione 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Lasciare vuota **l'ambito filtro** e fare clic su **Avanti**. Un filtro vuoto indica che la regola deve essere applicata a tutti gli oggetti.
10. Lasciare vuote le regole di **partecipare** e quindi fare clic su **Avanti**.
11. Fare clic su **Aggiungi trasformazione**, selezionare **ExchangeRate di FlowType** a **costante**, selezionare l' attributo Target **cloudFiltered** e nella casella di testo di origine, digitare **True**. Fare clic su **Aggiungi** per salvare la regola.  
![In ingresso trasformazione 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Per completare la configurazione [Applica e verificare le modifiche](#apply-and-verify-changes).

Se necessario, è possibile creare altre regole del primo tipo in cui includere più oggetti la sincronizzazione.

### <a name="outbound-filtering"></a>Il filtro in uscita
In alcuni casi, è necessario eseguire il filtro solo dopo che si aggiungono gli oggetti nel metaverse. Impossibile, ad esempio essere necessario esaminare l'attributo di posta elettronica da strutture delle risorse e l'attributo userPrincipalName dalla foresta di account per determinare se è necessario sincronizzare un oggetto. In questi casi, viene creato il filtro per la regola in uscita.

In questo esempio, si cambiare i filtri utenti pertanto solo in posta e userPrincipalName terminare con @contoso.com vengono sincronizzati:

1. Accedere a un server che esegue sincronizzazione di Azure AD Connect utilizzando un account è un membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare **La sincronizzazione regole Editor** dal menu start.
3. In **Tipo di regole**, fare clic su **in uscita**.
4. Individuare la regola denominata **a AAD-SOAInAD utente partecipa**. Fare clic su **Modifica**.
5. Nella casella popup, rispondere **Sì** per creare una copia della regola.
6. Nella pagina **Descrizione** modificare la precedenza su un valore inutilizzato, ad esempio 50.
7. Fare clic su **filtro ambito** nel riquadro di spostamento sinistro. Fare clic su **Aggiungi clausola**, attributo selezionare **posta elettronica**, selezionare operatore **ENDSWITH**e tipo di valore **@contoso.com**. Fare clic su **Aggiungi clausola**, selezionare attributo **userPrincipalName**, selezionare operatore **ENDSWITH**e tipo di valore **@contoso.com**.
8. Fare clic su **Salva**.
9. Per completare la configurazione [Applica e verificare le modifiche](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Applicare e verificare le modifiche
Dopo avere apportato le modifiche alla configurazione, questi deve applicati a oggetti già presenti nel sistema. È possibile anche che devono essere elaborati gli oggetti non sono attualmente nel motore di sincronizzazione e il motore di sincronizzazione deve essere letto in sistema di origine per verificare il relativo contenuto.

Se è stata modificata la configurazione utilizzando **dominio** o **unità organizzativa** filtro, è necessario eseguire l' **importazione completa** seguita da **sincronizzazione Delta**.

Se è stata modificata configurazione utilizzando **attributo** filtro, è necessario eseguire **la sincronizzazione completa**.

Eseguire le operazioni seguenti:

1. Avviare il **Servizio di sincronizzazione** dal menu start.
2. Selezionare **i connettori** e nell'elenco **connettori** , selezionare il connettore utilizzato per eseguire una configurazione modificare in precedenza. **Azioni**, selezionare **Esegui**.  
![Connettore eseguire](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. In **Esegui profili**, selezionare l'operazione indicata nella sezione precedente. Se è necessario eseguire due operazioni, eseguire la seconda volta completato il primo (nella colonna **stato** è **inattivo** per il connettore selezionato).

Dopo la sincronizzazione, tutte le modifiche vengono inserite per essere esportato. Rendere effettive le modifiche in Azure Active Directory, si desidera verificare che tutte le modifiche siano corrette.

1. Avviare un prompt cmd e passare alla sezione`%Program Files%\Microsoft Azure AD Sync\bin`
2. Correre:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Nel servizio di sincronizzazione, è possibile trovare il nome del connettore. Ha un nome simile a "contoso.com-AAD" per Azure Active Directory.
3. Correre:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. A questo punto si dispone di un file in % temp % denominato export.csv che possono essere esaminate in Microsoft Excel. Il file contiene tutte le modifiche che si desidera esportare.
5. Apportare le modifiche necessarie per la configurazione o di dati ed eseguire questa procedura nuovamente (importazione, sincronizza e verifica) fino a quando non sono previste modifiche che si desidera esportare.

Quando si è soddisfatti, è possibile esportare le modifiche in Azure Active Directory.

1. Selezionare **i connettori** e nell'elenco **connettori** , selezionare il connettore di Azure Active Directory. **Azioni**, selezionare **Esegui**.
2. Nella finestra **Esegui profili**, selezionare **Esporta**.
3. Se le modifiche alla configurazione Elimina molti oggetti, quindi viene segnalato un errore relativo all'esportazione il numero è maggiore della soglia (per impostazione predefinita 500). Se viene visualizzato questo errore, è necessario disattivare temporaneamente la caratteristica [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

A questo punto è possibile abilitare nuovamente l'utilità di pianificazione.

1. Avviare **l'Utilità di pianificazione** dal menu start.
2. Direttamente in **Libreria Utilità di pianificazione**, individuare l'attività denominata **utilità di pianificazione di sincronizzazione di Azure Active Directory**, pulsante destro del mouse e selezionare **Attiva**.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
