<properties
    pageTitle="Sincronizzazione di Azure AD Connect: come apportare modifiche alla configurazione predefinita | Microsoft Azure"
    description="Viene illustrato come apportare modifiche alla configurazione di sincronizzazione di Azure AD Connect."
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronizzazione di Azure AD Connect: come apportare modifiche alla configurazione predefinita
Lo scopo di questo argomento è per esaminare come apportare modifiche alla configurazione predefinita di sincronizzazione di Azure AD Connect. Fornisce i passaggi per alcuni scenari comuni. Con questa conoscenza dovrebbe essere possibile semplicemente apportare alcune modifiche semplice per la configurazione in base alle regole aziendali.

## <a name="synchronization-rules-editor"></a>Editor delle regole di sincronizzazione
L'Editor delle regole sincronizzazione viene utilizzato per visualizzare e modificare la configurazione predefinita. È possibile trovarlo nel Menu Start del gruppo di **Azure AD Connect** .  
![Menu Start con Editor di regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando lo si apre, vengono visualizzate le regole di fuori della casella predefinito.

![Editor di regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Spostarsi nell'editor
Elenchi a discesa nella parte superiore dell'editor consente di trovare rapidamente una determinata regola. Ad esempio, se si desidera visualizzare le regole in cui è incluso proxyAddresses attributo, modificate a discesa per le operazioni seguenti:  
![Filtro SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Per reimpostare il filtro e caricare una configurazione aggiornata, premere **F5** sulla tastiera.

Nella parte superiore destra è presente un pulsante **Aggiungi nuova regola**. Questo pulsante viene utilizzato per creare una regola personalizzata.

Nella parte inferiore, si dispone pulsanti per agire su una regola di sincronizzazione selezionata. **Modificare** ed **eliminare** eseguire operazioni desiderato. **Esportare** produce uno script di PowerShell per ricreare la regola di sincronizzazione. Questa procedura consente di spostare una regola di sincronizzazione da un server a un'altra.

## <a name="create-your-first-custom-rule"></a>Creare la prima regola personalizzata
La modifica più comune è modifiche ai flussi di attributo. I dati nella directory di origine potrebbero non essere come Azure Active Directory. Nell'esempio in questa sezione, si desidera verificare che il nome dell'utente è sempre **con l'iniziale**maiuscola.

### <a name="disable-the-scheduler"></a>Disabilitare l'utilità di pianificazione
L' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) viene eseguita ogni 30 minuti per impostazione predefinita. Si desidera assicurarsi che non viene avviato mentre si apportano modifiche e risolvere i problemi delle nuove regole. Per disattivare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Disabilitare l'utilità di pianificazione](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Creare la regola

1. Fare clic su **Aggiungi nuova regola**.
2. Nella pagina **Descrizione** immettere quanto segue:  
![In ingresso regola filtro](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nome: Assegnare alla regola un nome descrittivo.
    - Descrizione: Spiegazioni in modo che un altro utente comprendere novità per la regola.
    - Connessione di sistema: il sistema possibile trovare l'oggetto in. In questo caso, si seleziona Active Directory Connector.
    - Tipo di oggetto sistema/Metaverse connesso: Selezionare **utenti** e **persona** .
    - Tipo di collegamento: Cambiare questo valore per **partecipare**.
    - Ordine di precedenza: Specificare un valore univoco nel sistema. Un valore numerico più basso indica precedenza.
    - Tag: Lasciare vuoto. Solo le regole di casella di Microsoft devono avere questa casella contenente un valore.
3. Nella pagina **ambito filtro** immettere **givenName ISNOTNULL**.  
![Definizione dell'ambito filtro regola in entrata](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
In questa sezione viene utilizzata per definire quali oggetti che dovrà essere applicata la regola. Se lasciato vuoto, la regola viene applicato a tutti gli oggetti utente. Ma che includerà delle sale riunioni, gli account di servizio e altri oggetti utente non persone.
4. In **regole di partecipare**, lasciare vuoto il campo.
5. Nella pagina **trasformazioni** modificare l'ExchangeRate di FlowType **all'espressione**. Selezionare l' attributo Target **givenName**e nell'origine immettere `PCase([givenName])`.
![Regola in entrata trasformazioni](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
Motore di sincronizzazione è distinzione tra maiuscole e sia il nome della funzione e il nome dell'attributo. Se si digita un problema, viene visualizzato un avviso quando si aggiunge la regola. L'editor consente di salvare e continuare, è necessario riaprire la regola e correggere la regola.
6. Fare clic su **Aggiungi** per salvare la regola.

La nuova regola personalizzata deve essere visibile con altre regole di sincronizzazione nel sistema.

### <a name="verify-the-change"></a>Verificare la modifica
Con questa modifica nuova, si vuole che funziona come previsto e non viene generare errori. A seconda del numero di oggetti che presenti, sono disponibili due diversi modi per eseguire questo passaggio.

1. Eseguire una sincronizzazione completa su tutti gli oggetti
2. Eseguire un'anteprima e una sincronizzazione completa in un singolo oggetto

Avviare il **Servizio di sincronizzazione** dal menu start. La procedura descritta in questa sezione è tutti questo strumento.

1. **Sincronizzazione completa su tutti gli oggetti**  
Selezionare **i connettori** nella parte superiore. Identificare il connettore è stata modificata nella sezione precedente, in questo caso i servizi di dominio Active Directory, e selezionarlo. Selezionare **Esegui** da azioni e selezionare **OK**e **Sincronizzazione completa** .
![Sincronizzazione completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Gli oggetti sono stati aggiornati nel metaverse. Ora si desidera visualizzare l'oggetto nel metaverse.

2. **Anteprima e sincronizzazione completa in un singolo oggetto**  
Selezionare **i connettori** nella parte superiore. Identificare il connettore è stata modificata nella sezione precedente, in questo caso i servizi di dominio Active Directory, e selezionarlo. Selezionare **spazio connettore di ricerca**. Consente di trovare un oggetto che si desidera utilizzare per testare la modifica dell'ambito. Selezionare l'oggetto e fare clic su **Anteprima**. Nella schermata Nuovo selezionare **Anteprima eseguire il Commit**.
![Eseguire il commit anteprima](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
La modifica a questo punto si impegna a metaverse.

**Visualizzare l'oggetto nel metaverse**  
Ora si desidera selezionare alcuni oggetti di esempio per assicurarsi che il valore previsto e che la regola applicata. Selezionare **La ricerca nel Metaverse** nella parte superiore. Aggiungere un filtro per trovare gli oggetti rilevanti. Dai risultati della ricerca, aprire un oggetto. Esaminare i valori degli attributi e anch'esso nella colonna **Regole di sincronizzazione** che la regola applicata come previsto.  
![Ricerca nel Metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Attivare l'utilità di pianificazione
Se tutti gli elementi come previsto, è possibile abilitare nuovamente l'utilità di pianificazione. Da PowerShell, eseguire `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Altre modifiche di flusso comuni degli attributi
La sezione precedente descritte le procedure apportare modifiche a un flusso di attributo. In questa sezione vengono forniti alcuni esempi. La procedura per la procedura creare la regola di sincronizzazione è abbreviato, ma è possibile trovare la procedura completa nella sezione precedente.

### <a name="use-another-attribute-than-the-default"></a>Utilizzare un altro attributo da quello predefinito
Fabrikam, non esiste un insieme di strutture in alfabeto locale viene usato per nome, cognome e nome visualizzato. La rappresentazione caratteri latini degli attributi seguenti sono disponibili negli attributi di estensione. Durante la creazione dell'elenco indirizzi globale in Azure Active Directory e Office 365, l'organizzazione desidera questi attributi utilizzato.

Con una configurazione predefinita, un oggetto dall'insieme di strutture locale è simile alla seguente:  
![Attributo flusso 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Per creare una regola con altri flussi di attributo, eseguire le operazioni seguenti:

- Avviare **La sincronizzazione della regola Editor** dal menu start.
- Con **entrata** ancora selezionato a sinistra, fare clic sul pulsante **Aggiungi nuova regola**.
- Assegnare alla regola un nome e una descrizione. Selezionare il servizio Active Directory locale e i tipi di oggetto desiderato.  In **Tipo di collegamento**, selezionare **Partecipa**. Per la precedenza, selezionare un numero che non viene utilizzato da un'altra regola. Le regole di casella di iniziano a 100, in modo che il valore 50 possa essere utilizzato in questo esempio.
![Attributo flusso 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Lasciare vuota l'ambito (ovvero, applicare a tutti gli oggetti utente della foresta).
- Lasciare vuote le regole di join (ovvero, consentire la regola di casella di gestire i join).
- In trasformazioni, creare flussi di seguito:  
![Attributo flusso 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Fare clic su **Aggiungi** per salvare la regola.
- Passare al **Gestore del servizio di sincronizzazione**. I **connettori**, selezionare il connettore in cui è stata aggiunta la regola. Selezionare **Esegui**e **sincronizzazione completa**. Una sincronizzazione completa ricalcola tutti gli oggetti utilizzando le nuove regole corrente.

Questo è il risultato per lo stesso oggetto con questa regola:  
![Attributo flusso 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Lunghezza degli attributi
Per impostazione predefinita impostato come indicizzabile sono gli attributi della stringa e la lunghezza massima è 448 caratteri. Se si lavora con gli attributi della stringa che possono contenere più, quindi assicurarsi di includere le operazioni seguenti nel flusso attributo:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Modificare il userPrincipalSuffix
L'attributo userPrincipalName in Active Directory non è sempre noto dagli utenti e potrebbe non essere adatto come l'ID di accesso. Azure AD Connect Sincronizza l'installazione guidata di se si seleziona un attributo diverso, ad esempio posta. Ma in alcuni casi l'attributo deve essere calcolato. Ad esempio, la società Contoso contiene due directory di Azure Active Directory, uno per produzione e uno per la verifica. Si desidera che gli utenti nel tenant di test per l'utilizzo di un altro suffisso ID di accesso.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

In questa espressione, scrivere tutto a sinistra del primo @-sign (Word) e concatena con una stringa fissa.

### <a name="convert-a-multi-value-to-a-single-value"></a>Convertire un valore di multiple in un valore singolo
Alcuni attributi di Active Directory sono multivalore nello schema anche se soddisfino singole con valori in Active Directory utenti e computer. Un esempio è l'attributo description.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

In questa espressione nel caso in cui l'attributo è un valore è eseguire il primo elemento (voce) nell'attributo, rimuovere (Trim) spazi iniziali e finali e quindi mantenere i caratteri prima di tutto 448 (a sinistra) nella stringa.

### <a name="do-not-flow-an-attribute"></a>Non vengono un attributo
Per informazioni generali sullo scenario per questa sezione, vedere [controllare il processo del flusso di attributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Esistono due metodi per non trasmettere un attributo. Il primo è disponibile nell'installazione guidata e consente di [rimuovere gli attributi selezionati](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Questa opzione è disponibile se sono state sincronizzate l'attributo prima. Tuttavia, se è stato avviato e sincronizzare l'attributo in un secondo momento rimuoverla con questa caratteristica, le interruzioni di motore di sincronizzazione gestione l'attributo e i valori esistenti vengono lasciate in Azure Active Directory.

Se si desidera rimuovere il valore di un attributo e assicurarsi che non si trasmette in futuro, è necessario creare una regola personalizzata.

Fabrikam, sono state realizzate che alcuni degli attributi che è sincronizzare nel cloud non deve essere presente. Si desidera verificare che questi attributi vengono rimossi da Azure Active Directory.  
![Attributi di estensione errata](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Creare una nuova regola di sincronizzazione in ingresso e popolare la descrizione ![descrizioni](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Creare flussi di attributo del tipo di **espressione** e con l'origine **AuthoritativeNull**. Valore letterale **AuthoritativeNull** indica che il valore deve essere vuoto nel MV anche se una regola di sincronizzazione di precedenza inferiore tenta di inserire il valore.
![Trasformazione per gli attributi di estensione](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Salvare la regola di sincronizzazione. Avviare il **Servizio di sincronizzazione**, trovare il connettore, selezionare **Esegui**e **Sincronizzazione completa**. Questo passaggio ricalcola tutti i flussi di attributo.
- Verificare che le modifiche richieste per esportare lo spazio connettore la ricerca.
![Elimina a fasi](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul modello di configurazione per [Il Provisioning dichiarativo comprensione](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Per ulteriori informazioni sul linguaggio delle espressioni nelle [Informazioni sulle espressioni di Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
