<properties
    pageTitle="Sincronizzazione di Azure AD Connect: Understanding dichiarativo Provisioning | Microsoft Azure"
    description="Viene illustrato il modello di configurazione provisioning dichiarativo in Azure AD Connect."
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronizzazione di Azure AD Connect: Understanding dichiarativo Provisioning
In questo argomento viene illustrato il modello di configurazione in Azure AD Connect. Il modello è denominato Provisioning dichiarativo e consente di modificare una configurazione con facilità. Molti aspetti descritti in questo argomento sono avanzate e non è necessario maggior parte dei casi cliente.

## <a name="overview"></a>Panoramica
Provisioning dichiarativo elaborazione oggetti provenienti da una directory connessa di origine e determina come l'oggetto e attributi devono trasformati da un'origine di un valore di destinazione. Elaborazione di un oggetto in una pipeline di sincronizzazione e la pipeline è analogo a regole in entrata e in uscita. Una regola in entrata proviene da uno spazio connettore nel Metaverse e una regola in uscita è compreso tra il metaverse e uno spazio connettore.

![Pipeline di sincronizzazione](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

La pipeline dispone di più moduli diversi. Ognuna di esse è responsabile per un concetto di sincronizzazione di oggetto.

![Pipeline di sincronizzazione](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Origine, l'oggetto di origine
- [Ambito](#scope)consente di trovare tutte le regole di sincronizzazione che si trovano nell'ambito
- [Partecipare](#join), determina relazione tra spazio connettore e metaverse
- [Trasformazione](#transform), calcola il modo in cui devono essere trasformati attributi e flusso
- [Ordine di precedenza](#precedence)risolve contributi degli attributi in conflitto
- Destinazione, l'oggetto di destinazione

## <a name="scope"></a>Ambito
Il modulo ambito sta valutando un oggetto e determina le regole che si trovano nell'ambito e dovrebbero essere incluso nell'elaborazione. In base ai valori di attributi sull'oggetto, le regole di sincronizzazione diversi vengono valutate per essere nell'ambito. Ad esempio disattivati senza cassetta postale di Exchange dispone di regole diverse rispetto a un utente abilitato con una cassetta postale.  
![Ambito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

L'ambito è definito come clausole e gruppi. Le clausole sono all'interno di un gruppo. Viene utilizzato un AND logico tra tutte le clausole in un gruppo. Ad esempio (reparto = IT e paese = Danimarca). Viene utilizzato un operatore OR tra i gruppi.

![Ambito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
L'ambito in questa immagine deve essere letti come (reparto = IT e paese = Danimarca) o (paese = Svezia). Se gruppo 1 o 2 viene valutata su true, la regola è nell'ambito.

Il modulo ambito supporta le operazioni seguenti.

Operazione | Descrizione
--- | ---
UGUALE, DIVERSE | Confronto tra stringhe che restituiscono se valore è uguale al valore dell'attributo. Per gli attributi multivalore, vedere ISIN e ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Un confronto tra stringhe che restituiscono se valore è minore del valore dell'attributo.
CONTIENE, NOTCONTAINS | Confronto tra stringhe che restituiscono se valore è possibile trovare in un punto qualsiasi all'interno del valore nell'attributo.
STARTSWITH, NOTSTARTSWITH | Confronto tra stringhe che restituiscono se valore è all'inizio del valore dell'attributo.
ENDSWITH, NOTENDSWITH | Confronto tra stringhe che restituiscono se valore è alla fine del valore dell'attributo.
MAGGIORE, GREATERTHAN_OR_EQUAL | Confronto tra stringhe che valuta se valore è maggiore del valore dell'attributo.
ISNULL, ISNOTNULL | Se l'attributo è presente la dicitura dall'oggetto. Se l'attributo non è presente e pertanto null, la regola è nell'ambito.
ISIN, ISNOTIN | Viene valutato se il valore è presente nell'attributo definito. Questa operazione è multivalore variante di uguale e diverse. L'attributo deve per essere un attributo multivalore e se il valore può trovarsi in uno dei valori degli attributi, la regola è nell'ambito.
ISBITSET, ISNOTBITSET | Viene valutato se è impostato un determinato bit. Ad esempio, può essere utilizzato per valutare i bit userAccountControl per verificare se un utente è attivato o disattivato.
ISMEMBEROF, ISNOTMEMBEROF | Il valore deve contenere un nome distinto a un gruppo nello spazio connettore. Se l'oggetto è un membro del gruppo specificato, la regola è nell'ambito.

## <a name="join"></a>Join
Il modulo di join della pipeline di sincronizzazione è responsabile per trovare la relazione tra l'oggetto nell'origine e un oggetto nel database di destinazione. In una regola in entrata, questa relazione è un oggetto in un'area di connettore la ricerca di una relazione a un oggetto nel metaverse.  
![Partecipare a tra cs e mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
L'obiettivo è riscontrare che se esiste un oggetto è già presente nelle metaverse, creato da un altro connettore deve essere associato. Ad esempio, in un insieme di strutture delle risorse account all'utente di foresta account deve essere uniti con l'utente dall'insieme di strutture delle risorse.

Join vengono utilizzati principalmente nelle regole in entrata per unire oggetti spazio connettore allo stesso oggetto metaverse.

I join sono definiti come uno o più gruppi. All'interno di un gruppo, è necessario clausole. Viene utilizzato un AND logico tra tutte le clausole in un gruppo. Viene utilizzato un operatore OR tra i gruppi. I gruppi vengono elaborati nell'ordine dall'alto verso il basso. Quando un gruppo è trovato esattamente una corrispondenza con un oggetto nel database di destinazione, vengono valutate altre regole di join. Se uguali a zero o più di un oggetto viene trovato, l'elaborazione continua per il gruppo successivo di regole. Per questo motivo, le regole dovrebbero essere create nell'ordine di più esplicito prima e più sfocata alla fine.  
![Partecipare a definizione](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Linee di join in questa immagine vengono elaborate dall'alto verso il basso. Prima di tutto la pipeline di sincronizzazione vede se viene trovata una corrispondenza nel campo ID dipendente. In caso contrario, la seconda regola viene visualizzato se il nome dell'account può essere utilizzato per unire i vari oggetti. Se che non è una corrispondenza uno, la regola terza e ultima è una corrispondenza di più sfocata usando il nome dell'utente.

Se la valutazione di tutte le regole di join e è possibile individuare una corrispondenza, verrà utilizzato il **Tipo di collegamento** nella pagina **Descrizione** . Se questa opzione è impostata su **provisioning**, viene creato un nuovo oggetto nel database di destinazione.  
![Disposizione o join](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un oggetto può contenere solo una regola di sincronizzazione solo con le regole di join nell'ambito. Se sono presenti più regole di sincronizzazione in cui è definito join, si verifica un errore. Ordine di precedenza non viene usata per risolvere i conflitti di join. Un oggetto deve avere una regola di join nell'ambito per gli attributi di flusso con la stessa direzione in ingresso/uscita. Se è necessario gli attributi di flusso in ingresso e in uscita allo stesso oggetto, è necessario disporre di un in entrata e una regola di sincronizzazione in uscita con join.

Join in uscita ha un comportamento speciale quando si tenta di effettuare il provisioning di un oggetto da uno spazio connettore di destinazione. L'attributo nome distinto viene utilizzato per provare un join reverse. Se lo spazio di connettore di destinazione con il nome distinto stesso esiste già un oggetto, gli oggetti vengono uniti.

Il modulo di join viene valutato solo una volta quando una nuova regola di sincronizzazione è disponibile nell'ambito. Quando si è eseguito l'accesso di un oggetto, è non separare anche se non è più soddisfatta i criteri di join. Se si desidera disjoin un oggetto, la regola di sincronizzazione che aggiungono gli oggetti deve fuori ambito.

### <a name="metaverse-delete"></a>Elimina Metaverse
Un oggetto metaverse rimane come tempo come esiste una regola di sincronizzazione nell'ambito con **Tipo di collegamento** imposta **StickyJoin**a **disposizione** . Un StickyJoin viene utilizzato quando un connettore non è consentito effettuare il provisioning di un nuovo oggetto nel Metaverse, ma quando si è unito, devono essere eliminato nel sito di origine prima che l'oggetto metaverse viene eliminato.

Quando si elimina un oggetto metaverse, tutti gli oggetti associati a una regola in uscita sincronizzazione contrassegnata per **il provisioning** sono contrassegnati per l'eliminazione.

## <a name="transformations"></a>Trasformazioni
Le trasformazioni vengono utilizzate per definire come attributi devono flusso dal sito di origine di destinazione. I flussi di possono avere uno dei seguenti **tipi di flusso**: diretta, costante o espressione. Un flusso diretto scorra come un valore dell'attributo-è senza trasformazioni aggiuntive. Un valore costante imposta il valore specificato. Un'espressione utilizza il linguaggio delle espressioni provisioning dichiarativi per esprimere come deve essere la trasformazione. I dettagli per il linguaggio delle espressioni sono disponibili nella sezione [informazioni sulle dichiarativo provisioning linguaggio delle espressioni](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Disposizione o join](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La casella di controllo **Applica una sola volta** definisce che l'attributo deve essere impostato solo quando l'oggetto viene inizialmente creato. Ad esempio, è possibile utilizzare questa configurazione per impostare una password iniziale per un nuovo oggetto utente.

### <a name="merging-attribute-values"></a>Unione di valori degli attributi
Per i flussi di attributo è un'impostazione per determinare se devono essere uniti attributi multivalore da diversi connettori diversi. Il valore predefinito è l' **aggiornamento**, che indica che la regola di sincronizzazione con priorità hanno la precedenza.

![Unire tipi](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

È inoltre disponibile **unire** e **MergeCaseInsensitive**. Queste opzioni consentono di unire i valori da origini diverse. Ad esempio, può essere utilizzato per unire l'attributo proxyAddresses o membro di diversi insiemi di strutture diverse. Quando si utilizza questa opzione, tutte le regole di sincronizzazione nell'ambito di un oggetto necessario utilizzare lo stesso tipo di unione. Non è possibile definire l' **aggiornamento** da un connettore e **unire** da un altro. Se si tenta, viene visualizzato un errore.

La differenza tra **unire** e **MergeCaseInsensitive** è la modalità di elaborazione valori degli attributi duplicati. Motore di sincronizzazione verifica che non vengono inseriti i valori duplicati nell'attributo di destinazione. Con **MergeCaseInsensitive**, i valori duplicati con solo una differenza nel caso in cui non devono essere presenti. Ad esempio, non viene visualizzata entrambe "SMTP:bob@contoso.com" e "smtp:bob@contoso.com" nell'attributo di destinazione. **Unire** è controllare solo i valori esatti e più valori dove non c'è solo una differenza di maiuscole/minuscole potrebbero essere presente.

L'opzione **Sostituisci** è diverso da quello di **aggiornamento**, ma non viene utilizzato.

### <a name="control-the-attribute-flow-process"></a>Controllare il processo di flusso attributo
Quando più regole di sincronizzazione in ingresso vengono configurate per contribuire allo stesso attributo metaverse, la precedenza viene utilizzata per determinare il vincitore. La regola di sincronizzazione con priorità (valore numerico più basso) sarà il valore di collaborazione. Lo stesso avviene per le regole in uscita. La sincronizzazione con più alti wins la precedenza delle regole e fornire il valore alla directory connessa.

In alcuni casi, invece di fornire un valore, la regola di sincronizzazione deve determinare comportano di altre regole. Esistono alcuni valori letterali speciali utilizzati per questo caso.

Per le regole di sincronizzazione in entrata, il valore letterale **NULL** utilizzabile per indicare che il flusso non ha alcun valore di collaborazione. Un'altra regola con priorità bassa può contribuire a un valore. Se nessuna regola contribuito un valore, l'attributo metaverse viene rimosso. Per una regola in uscita, se **NULL** è il valore finale dopo l'elaborazione di tutte le regole di sincronizzazione, viene rimosso il valore nella directory connesso.

Valore letterale **AuthoritativeNull** è simile a **NULL** ma con la differenza che non regole di precedenza inferiore possono contribuire a un valore.

Un flusso di attributi è inoltre possibile utilizzare **IgnoreThisFlow**. È simile a NULL nel senso che significa niente di collaborazione. La differenza è che non rimuove un valore già esistente nel database di destinazione. È ad esempio il flusso di attributi non è mai stato sono.

Ecco un esempio:

In *a Active Directory - distribuzione ibrida di Exchange utente* sono disponibili il flusso relativo al seguente:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Questa espressione deve essere letto come: se la cassetta postale utente si trova in Azure Active Directory, quindi scorrono l'attributo da Azure Active Directory di Active Directory. In caso contrario, non vengono nulla alla Active Directory. In questo caso, preferisce mantenere il valore esistente in Active Directory.

### <a name="importedvalue"></a>ImportedValue
La funzione ImportedValue è diversa da tutte le altre funzioni poiché il nome dell'attributo deve essere racchiuso tra virgolette anziché racchiusa tra parentesi quadre:  
`ImportedValue("proxyAddresses")`.

In genere durante la sincronizzazione un attributo utilizzerà il valore previsto, anche se non è stata esportata ancora oppure è stato ricevuto un messaggio di errore durante l'esportazione ("in alto del tower"). La sincronizzazione in ingresso presuppone che un attributo che non è ancora trascorsa una directory connessa infine raggiunge. In alcuni casi, è importante sincronizzare solo un valore che è stato confermato dalla directory connessa ("ologramma e delta importare tower").

Esempio di questa funzione può trovarsi nella casella di sincronizzazione della regola *In da Active Directory: utente comuni da Exchange*. Distribuzione ibrida di Exchange, il valore aggiunto da Exchange online deve sincronizzato solo quando è stato confermato che il valore di esportazione completata:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Ordine di precedenza
Quando più regole di sincronizzazione tenta di collaborazione lo stesso valore dell'attributo di destinazione, il valore di precedenza viene utilizzato per determinare il vincitore. La regola con priorità più alta, bassa valore numerico, verrà fornire l'attributo un conflitto.

![Unire tipi](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Questo ordinamento può essere utilizzato per definire più precisi flussi attributo per un sottoinsieme di oggetti. Ad esempio, il fuori della casella-le regole dei assicurarsi che gli attributi da un account attivato (**AccountEnabled utente**) hanno la precedenza da altri account.

È possibile definire la priorità tra i connettori. Che consente ai connettori con i dati più efficaci per fornire i valori prima di tutto.

### <a name="multiple-objects-from-the-same-connector-space"></a>Più oggetti dallo stesso spazio del connettore
Se si dispone di più oggetti nello stesso spazio connettore aggiungono allo stesso oggetto metaverse, è necessario modificare la precedenza. Se diversi oggetti presenti nell'ambito della stessa regola di sincronizzazione, il motore di sincronizzazione non è possibile determinare la precedenza. È ambigui quale oggetto di origine deve fornire il valore nel Metaverse. Questa configurazione viene segnalata come ambigui anche se gli attributi nell'origine hanno lo stesso valore.  
![Allo stesso oggetto mv eseguito il join di più oggetti](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Per questo scenario, è necessario modificare l'ambito delle regole di sincronizzazione in modo che gli oggetti origine hanno regole di sincronizzazione diverso nell'ambito. Che consente di definire la priorità diversi.  
![Allo stesso oggetto mv eseguito il join di più oggetti](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul linguaggio delle espressioni nelle [Informazioni sulle espressioni di Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Vedere come dichiarativo il provisioning è utilizzato fuori della casella comprendere [la configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md).
- Informazioni su come apportare modifiche pratica utilizzando il provisioning dichiarativo in [come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md).
- Continuare a leggere come utenti e contatti collaborare [Understanding utenti e i contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)

**Argomenti della Guida di riferimento**

- [Sincronizzazione di Azure AD Connect: funzioni di riferimento](active-directory-aadconnectsync-functions-reference.md)
