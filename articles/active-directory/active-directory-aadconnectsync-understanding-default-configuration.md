<properties
    pageTitle="Sincronizzazione di Azure AD Connect: informazioni sulla configurazione predefinita | Microsoft Azure"
    description="Questo articolo descrive la configurazione predefinita in Azure AD Connect sincronizzazione."
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronizzazione di Azure AD Connect: informazioni sulla configurazione predefinita
In questo articolo sono illustrate le regole di casella di configurazione. Documenti le regole e impatto della configurazione di queste regole. Inoltre illustra la configurazione predefinita di sincronizzazione di Azure AD Connect. L'obiettivo è che il lettore riconosce come funziona il modello di configurazione, denominato durante il provisioning dichiarativo in un esempio concreto. In questo articolo si presuppone che sia stato già installato e configurare la sincronizzazione di Azure AD Connect usando la procedura guidata installazione.

Per comprendere i dettagli del modello di configurazione, leggere [Il Provisioning dichiarativo comprensione](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Casella di regole locali per Azure Active Directory
Le espressioni seguenti sono disponibili nella casella di configurazione.

### <a name="user-out-of-box-rules"></a>Regole di fuori della casella utenti
Le regole vengono applicate anche al tipo di oggetto iNetOrgPerson.

Un oggetto utente deve soddisfare le operazioni seguenti per sincronizzare:

- Devono avere un sourceAnchor.
- Dopo aver creato l'oggetto in Azure Active Directory, non è possibile modificare sourceAnchor. Se il valore viene modificato in locale, l'oggetto interrompe la sincronizzazione finché non viene modificato il sourceAnchor al valore precedente.
- Dovrà avere l'attributo accountEnabled (userAccountControl) popolato. Con Active Directory locale, l'attributo è sempre presente e popolato.

Sono i seguenti oggetti utente **non verranno** sincronizzate con Azure Active Directory:

- `IsPresent([isCriticalSystemObject])`. Assicurarsi che molti casella di oggetti Active Directory, ad esempio l'account administrator predefinito, non vengono sincronizzati.
- `IsPresent([sAMAccountName]) = False`. Assicurarsi che gli oggetti utente senza l'attributo sAMAccountName non vengono sincronizzati. In questo caso solo praticamente accadrebbe in un dominio aggiornato da NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Non sincronizzare l'account di servizio utilizzato da sincronizzazione di Azure AD Connect e le relative versioni precedenti.
- Non sincronizzare gli account di Exchange che non funziona in Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Non vengono sincronizzati gli oggetti che non funziona in Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Questa maschera di bit (& H21C07000) da filtrare gli oggetti seguenti:
    - Cartelle pubbliche abilitate alla posta elettronica
    - Cassetta postale del Supervisore
    - Cassetta postale Database cassette postali (cassetta postale di sistema)
    - Gruppo di sicurezza universale (non valide per un utente, ma non è presente per motivi di legacy)
    - Gruppo non universale (non valide per un utente, ma non è presente per motivi di legacy)
    - Piano di cassette postali
    - Cassetta postale di individuazione
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Tutti gli oggetti vittima di replica non vengono sincronizzati.

Attributo presente quanto segue:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. L'attributo sourceAnchor non ha contribuito da una cassetta postale collegata. Si presuppone che se una cassetta postale collegata è stata trovata, l'account effettivo fa parte in un secondo momento.
- Exchange relativi attributi sono sincronizzati solo se l' attributo **mailNickName** presenta un valore.
- Quando sono presenti più insiemi di strutture, gli attributi vengono utilizzati nell'ordine seguente:
    1. Gli attributi relativi ai accesso (ad esempio userPrincipalName) vengono forniti dall'insieme di strutture con un account attivato.
    2. Gli attributi che possono trovarsi in un elenco indirizzi globale di Exchange (elenco indirizzi globale) vengono forniti dall'insieme di strutture con una cassetta postale di Exchange.
    3. Se non è presente alcuna cassetta postale, questi attributi possono provenire da un insieme di strutture.
    4. Exchange relativi attributi, tecnico non è visibile nell'elenco indirizzi globale, vengono forniti dall'insieme di strutture in cui `mailNickname ISNOTNULL`.
    5. Se sono presenti più insiemi di strutture in grado di soddisfare una di queste regole, l'ordine di creazione (Data/ora) dei connettori (insiemi di strutture) viene utilizzato per determinare quali foresta contribuisce gli attributi.

### <a name="contact-out-of-box-rules"></a>Regole di casella di contatti
Un oggetto contatto deve soddisfare le operazioni seguenti per sincronizzare:

- Il contatto deve essere abilitati alla posta elettronica. Viene verificato con le regole seguenti:
    - `IsPresent([proxyAddresses]) = True)`. L'attributo proxyAddresses deve essere compilato.
    - Un indirizzo di posta elettronica principale sono disponibili nell'attributo proxyAddresses o l'attributo di posta elettronica. La presenza di un @ viene utilizzato per verificare che il contenuto è un indirizzo di posta elettronica. Una di queste due regole deve essere valutata su True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Esiste una voce con "SMTP:" e se c'è possibile un @ trovato nella stringa?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. È l'attributo mail popolati e se è possibile un @ trovato nella stringa?

Sono i seguenti oggetti contatti **non verranno** sincronizzate con Azure Active Directory:

- `IsPresent([isCriticalSystemObject])`. Assicurarsi che nessuna oggetti contatto contrassegnati come critici vengono sincronizzati. Non devono essere con una configurazione predefinita.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Questi oggetti procedura non funzionano in Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Tutti gli oggetti vittima di replica non vengono sincronizzati.

### <a name="group-out-of-box-rules"></a>Regole di casella di gruppo
Un oggetto gruppo deve soddisfare le operazioni seguenti per sincronizzare:

- Devono avere meno di 50.000 membri. Questo numero è il numero di membri del gruppo locale.
    - Se è presente più membri prima che la sincronizzazione viene avviata la prima volta, il gruppo non è sincronizzato.
    - Se il numero di membri aumentare da quando è stato inizialmente creato, quindi quando viene raggiunta 50.000 membri interrompe la sincronizzazione fino a quando il numero totale di appartenenza è inferiore a 50.000 nuovamente.
    - Nota: Il numero totale di 50.000 appartenenza viene applicato anche da Azure Active Directory. il non utente è possibile sincronizzare i gruppi con altri membri, anche se si modifica o rimozione di questa regola.
- Se il gruppo è un **Gruppo di distribuzione**, deve essere abilitato alla posta. Vedere [le regole di casella di contatto](#contact-out-of-box-rules) per la regola viene applicata.

Sono i seguenti gruppo oggetti **non verranno** sincronizzate con Azure Active Directory:

- `IsPresent([isCriticalSystemObject])`. Assicurarsi che molti casella di oggetti Active Directory, ad esempio del gruppo administrators predefinito, non vengono sincronizzati.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Gruppo legacy usato da DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Gruppo di ruoli.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Tutti gli oggetti vittima di replica non vengono sincronizzati.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regole di fuori della casella ForeignSecurityPrincipal
FSP fanno parte di "uno" (\*) oggetto nel metaverse. In realtà, questo join si verifica solo per gli utenti e gruppi di sicurezza. Grazie a questa configurazione appartenenze tra insiemi di strutture vengono risolti che rappresentate correttamente in Azure Active Directory.

### <a name="computer-out-of-box-rules"></a>Regole di fuori della casella computer
Un oggetto computer deve soddisfare le operazioni seguenti per sincronizzare:

- `userCertificate ISNOTNULL`. Solo i computer Windows 10 popolare l'attributo. Tutti gli oggetti computer con un valore in questo attributo vengono sincronizzati.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Informazioni sullo scenario di casella di regole
In questo esempio si utilizza una distribuzione con un insieme di strutture di account (A), un insieme di strutture delle risorse (R) e una directory di Azure Active Directory.

![Immagine con descrizione dello scenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

In questa configurazione si presuppone che esiste un account abilitato nella foresta account e un account disattivato dell'insieme di strutture di risorse con una cassetta postale collegata.

Il nostro obiettivo con la configurazione predefinita è:

- Gli attributi relativi ai accesso vengono sincronizzati dall'insieme di strutture con l'account abilitato.
- Gli attributi presenti nell'elenco indirizzi globale (elenco indirizzi globale) vengono sincronizzati dall'insieme di strutture con la cassetta postale. Se non è presente alcuna cassetta postale, viene utilizzata qualsiasi altra foresta.
- Se viene trovata una cassetta postale collegata, l'account abilitata collegato deve essere presenti per l'oggetto da esportare in Azure Active Directory.

### <a name="synchronization-rule-editor"></a>Editor di regole di sincronizzazione
La configurazione può essere visualizzata e modificata con lo strumento di sincronizzazione regole Editor (SRE) e un collegamento a tale sono disponibili nel menu start.

![Icona di Editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Il SRE è uno strumento kit di risorse e viene installato con sincronizzazione di Azure AD Connect. Per iniziare, è necessario essere un membro del gruppo ADSyncAdmins. All'avvio, viene visualizzato simile al seguente:

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

In questo riquadro viene visualizzato tutte le regole di sincronizzazione create per la configurazione. Ogni riga della tabella è una regola di sincronizzazione. A sinistra in tipi di regole, sono elencati i due tipi diversi: in ingresso e in uscita. In ingresso e in uscita è stata inviata dalla visualizzazione del metaverse. Lo stato attivo si principalmente desidera nelle regole in entrata in questo argomento. L'elenco effettivo delle regole di sincronizzazione dipende dallo schema rilevato in Active Directory. Nella figura riportata sopra, l'insieme di strutture di account (fabrikamonline.com) non dispone di uno o più servizi, ad esempio Exchange e Lync, e non sono state create regole di sincronizzazione per i servizi. Tuttavia, l'insieme di strutture delle risorse (res.fabrikamonline.com) disponibili regole di sincronizzazione per i servizi. Il contenuto delle regole varia a seconda della versione rilevata. Ad esempio, in una distribuzione di Exchange 2013 in cui sono disponibili ulteriori flussi attributo configurati rispetto a Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regola di sincronizzazione
Una regola di sincronizzazione è un oggetto di configurazione con un insieme di attributi che scorre quando viene soddisfatta una condizione. Viene usato anche per descrivere relazione a un oggetto nel metaverse, noto come **partecipare** o **corrisponde a**un oggetto in un unico spazio connettore. Le regole di sincronizzazione è presente un valore di precedenza che indica le relazioni tra loro. Una regola di sincronizzazione con un valore numerico è prioritario e in un conflitto di flusso attributo precedenza wins la risoluzione del conflitto.

Ad esempio, osservare la regola di sincronizzazione **In da Active Directory: utente AccountEnabled**. Selezionare questa voce di SRE e scegliere **Modifica**.

Poiché si tratta una regola di fuori della casella, viene visualizzato un avviso quando si apre la regola. È necessario apportare eventuali [modifiche alle regole di casella di](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), non in modo che viene chiesto quali sono le intenzioni. In questo caso, si desidera visualizzare la regola. Selezionare **No**.

![Sincronizzazione delle regole di avviso](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Una regola di sincronizzazione con quattro sezioni di configurazione: descrizione, filtrare, le regole di Join e le trasformazioni di ambito.

#### <a name="description"></a>Descrizione
La prima sezione fornisce informazioni di base, ad esempio nome e descrizione.

![Descrizione della scheda editor regole aggiornati ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

È inoltre trovare informazioni sul sistema che connesso questa regola è correlata a quale oggetto tipo nel sistema connesso a che IT si applica e il tipo di oggetto metaverse. Il tipo di oggetto metaverse è sempre persona indipendentemente dal fatto che, quando il tipo di oggetto di origine è un utente, iNetOrgPerson o contatto. Il tipo di oggetto metaverse non è mai necessario modificare in modo che è stata creata come tipo generico. Per partecipare, StickyJoin o disposizione, è possibile impostare il tipo di collegamento. Questa impostazione interagisce con la sezione regole di Join e viene descritto in un secondo momento.

È anche possibile visualizzare la regola di sincronizzazione viene utilizzata per la sincronizzazione delle password. Se un utente nell'ambito per la regola di sincronizzazione, la password viene sincronizzata locali al cloud (presupponendo che è stata attivata la caratteristica di sincronizzazione password).

#### <a name="scoping-filter"></a>Definizione dell'ambito filtro
La sezione filtro definizione dell'ambito viene utilizzata per configurare quando applicare una regola di sincronizzazione. Dal momento che indica il nome della regola di sincronizzazione attualmente visualizzata deve essere applicata solo per gli utenti abilitati, l'ambito è configurato in modo che l' attributo di Active Directory **userAccountControl** non deve avere bit 2 impostare. Quando il motore di sincronizzazione rileva un utente in Active Directory, viene applicata la regola di sincronizzazione quando **userAccountControl** è impostato sul valore decimale 512 (abilitato normale utente). Non si applica la regola quando l'utente ha **userAccountControl** impostato su 514 (utente normale disabilitato).

![Definizione dell'ambito scheda nell'editor di regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Il filtro ambito gruppi e clausole che possono essere annidate. Tutte le clausole all'interno di un gruppo devono essere soddisfatte per applicare una regola di sincronizzazione. Quando sono definiti più gruppi, almeno un gruppo deve essere soddisfatte per la regola venga applicata. Ovvero un operatore OR viene valutato tra i gruppi e logico e viene valutato all'interno di un gruppo. Esempio di questa configurazione disponibili in sincronizzazione regola in uscita **a AAD – Group Join**. Esistono diversi gruppi di filtro della sincronizzazione, ad esempio uno per i gruppi di sicurezza (`securityEnabled EQUAL True`) e uno per i gruppi di distribuzione (`securityEnabled EQUAL False`).

![Definizione dell'ambito scheda nell'editor di regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Questa regola viene utilizzata per definire quali gruppi devono eseguire il provisioning di Azure Active Directory. Gruppi di distribuzione devono essere abilitato deve essere sincronizzata con Azure Active Directory per la posta, ma per i gruppi di sicurezza non è necessario un messaggio di posta elettronica.

#### <a name="join-rules"></a>Partecipare a regole
La terza sezione viene utilizzata per configurare la relazione tra gli oggetti nello spazio connettore e agli oggetti nel metaverse. La regola esaminata in precedenza non ha qualsiasi configurazione per partecipare a regole, pertanto si desidera esaminare **In da Active Directory: utente partecipa**.

![Scheda regole di join nell'editor di regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Il contenuto della regola di join dipende dall'opzione corrispondente è selezionata nell'installazione guidata. Per una regola in entrata, la valutazione inizia con un oggetto nello spazio di connettore di origine e ogni gruppo nelle regole di join viene valutato in sequenza. Se un oggetto di origine viene valutato in modo che corrispondano esattamente un oggetto nel metaverse usando una delle regole di join, gli oggetti vengono uniti. Se la valutazione di tutte le regole e non vi è alcuna corrispondenza, viene utilizzato il tipo di collegamento nella pagina descrizione. Se questa configurazione è impostata su **provisioning**, viene creato un nuovo oggetto nel database di destinazione, metaverse. Per eseguire il provisioning di un nuovo oggetto nel Metaverse è noto anche come **proiettare** un oggetto nel Metaverse.

Le regole di join vengono valutate una sola volta. Quando il join di un oggetto di spazio connettore e un oggetto metaverse, rimangono uniti come l'ambito della regola di sincronizzazione è ancora soddisfatto.

Durante la valutazione delle regole di sincronizzazione, deve essere solo una regola di sincronizzazione con regole di join definite nell'ambito. Se vengono trovate più regole di sincronizzazione con regole di join per un oggetto, viene generato un errore. Per questo motivo, la procedura consigliata è necessario solo una regola di sincronizzazione con join definita quando più regole di sincronizzazione si trovano nell'ambito di un oggetto. Nella casella di configurazione per la sincronizzazione di Azure AD Connect, le regole possono trovare esaminando il nome e trovare quelle ottenute con la parola **partecipare** alla fine del nome. Una regola di sincronizzazione senza tutte le regole di join definite si applica flussi attributo quando un'altra regola sincronizzazione collegate per gli oggetti o il provisioning di un nuovo oggetto nel database di destinazione.

Se osserva la figura riportata sopra, è possibile visualizzare che la regola sta tentando di partecipare **objectSID** con **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), ovvero è previsto in una topologia di strutture delle risorse di account. Si trova la stessa regola in tutti gli insiemi di strutture. Il presupposto è che ogni foresta potrebbe essere strutture di un account o delle risorse. Questa configurazione funziona anche se si dispone di account che vivono in un'unica foresta e non è necessario intervenire.

#### <a name="transformations"></a>Trasformazioni
La sezione trasformazione definisce tutti i flussi di attributo da applicare all'oggetto di destinazione quando vengono uniti gli oggetti e il filtro ambito viene soddisfatta. Tornare indietro di **In da Active Directory: utente AccountEnabled** regola di sincronizzazione si trova le trasformazioni seguenti:

![Trasformazioni scheda editor regole aggiornati ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Per inserire questa configurazione nel contesto in una distribuzione di strutture delle risorse Account normalmente per trovare un account abilitato della foresta account e un account disattivato dell'insieme di strutture di risorse con le impostazioni di Exchange e Lync. La regola di sincronizzazione attualmente visualizzata contiene gli attributi necessari per l'accesso e degli attributi seguenti devono scorrono dall'insieme di strutture dove non c'è un account attivato. Tutti questi flussi di attributo sono posizionati insieme in una regola di sincronizzazione.

Una trasformazione può avere tipi diversi: costante, pubblicitario ed espressione.

- Un flusso costante scorre sempre un valore hardcoded. Nel caso, sempre imposta il valore **vero** nel metaverse attributo denominato **accountEnabled**.
- Un flusso diretto scorra sempre il valore dell'attributo in origine per l'attributo di destinazione come-è.
- Il terzo tipo di flusso è espressione e che consente di configurazioni più avanzate.

Il linguaggio delle espressioni è VBA (Visual Basic, Applications Edition), in modo persone con esperienza di Microsoft Office o VBScript riconosce il formato. Gli attributi sono racchiusi tra parentesi quadre, [attributeName]. Nomi di funzioni e i nomi degli attributi sono maiuscole e minuscole, ma l'Editor delle regole sincronizzazione valuta le espressioni e fornire un avviso se l'espressione non è valido. Tutte le espressioni sono espressi in una singola riga con le funzioni annidate. Per visualizzare la potenza della lingua di configurazione, ecco il flusso per pwdLastSet, ma con altri commenti inseriti:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Per ulteriori informazioni sul linguaggio delle espressioni per i flussi di attributo, vedere [Informazioni sulle espressioni di Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Ordine di precedenza
Ora siano presenti alcuni singole regole di sincronizzazione, ma le regole di interagiscono con la configurazione. In alcuni casi, un valore dell'attributo è fornito da più regole di sincronizzazione allo stesso attributo di destinazione. In questo caso la precedenza degli attributi viene utilizzata per determinare quali attributi wins. Ad esempio, osservare sourceAnchor attributo. L'attributo è un importante per poter accedere a Azure Active Directory. È possibile trovare un flusso di attributi per l'attributo in due diversi regole di sincronizzazione, **In da Active Directory: utente AccountEnabled** e **In da Active Directory: utente comuni**. A causa di precedenza delle regole di sincronizzazione, l'attributo sourceAnchor è contribuito dall'insieme di strutture con un account attivato prima di tutto quando sono disponibili diversi oggetti aggiunto all'oggetto del metaverse. Se esistono account abilitato, quindi il motore di sincronizzazione viene utilizzata la regola di sincronizzazione di tutte le catture **In da Active Directory: utente comuni**. Questa configurazione garantisce che anche per gli account disattivati, c'è ancora un sourceAnchor.

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La precedenza delle regole di sincronizzazione in gruppi di impostazione dell'installazione guidata. Tutte le regole in un gruppo con lo stesso nome, ma le connessioni a varie directory connesse. L'installazione guidata offre la regola **In da Active Directory: utente partecipa** massima priorità e iterazioni sopra tutti connessi directory Active Directory. Quindi continua con i gruppi di regole in un ordine predefinito successivi. All'interno di un gruppo, le regole vengono aggiunti nell'ordine che sono stati aggiunti i connettori della procedura guidata. Se un altro connettore viene aggiunto tramite la procedura guidata, le regole di sincronizzazione vengono riordinate e le regole del connettore nuovo vengono inserite ultimo ogni gruppo.

### <a name="putting-it-all-together"></a>Combinazione degli elementi
È ora possibile sapere sufficiente sulle regole di sincronizzazione in grado di comprendere il funzionamento della configurazione con le regole della sincronizzazione. Se osserva un utente e gli attributi che vengono forniti nel Metaverse, le regole vengono applicate nell'ordine seguente:

Nome | Commento
:------------- | :-------------
In da Active Directory: utente partecipa | Regola di partecipazione a oggetti di spazio connettore con metaverse.
In da Active Directory: abilitato account | Gli attributi necessari per l'accesso a Azure Active Directory e in Office 365. Vogliamo degli attributi seguenti dall'account abilitato.
In da Active Directory: utente comuni di Exchange | Attributi presenti nell'elenco indirizzi globale. Si presuppone che la qualità dei dati più adatto nell'insieme di strutture in cui è stato trovato cassetta postale dell'utente.
In da Active Directory: utente comune | Attributi presenti nell'elenco indirizzi globale. In caso di una cassetta postale trovata, qualsiasi altro oggetto collegato tramite join può contribuire il valore dell'attributo.
In da Active Directory: utente Exchange | Esiste solo se è stato rilevato Exchange. Scorra tutti gli attributi di Exchange dell'infrastruttura.
In da Active Directory: utente Lync | Esiste solo se è stato rilevato Lync. Scorra tutti gli attributi di Lync dell'infrastruttura.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul modello di configurazione per [Il Provisioning dichiarativo comprensione](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Per ulteriori informazioni sul linguaggio delle espressioni nelle [Informazioni sulle espressioni di Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Continuare a leggere come funziona il la casella di configurazione in [Understanding utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Informazioni su come apportare modifiche pratica utilizzando il provisioning dichiarativo in [come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md).

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
