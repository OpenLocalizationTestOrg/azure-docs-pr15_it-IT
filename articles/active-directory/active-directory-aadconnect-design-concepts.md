<properties
   pageTitle="Azure AD Connect: Progettare concetti | Microsoft Azure"
   description="In questo argomento dettaglio determinate aree di progettazione di implementazione"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Concetti di progettazione
Lo scopo di questo argomento è per descrivere le aree che devono essere considerate durante la progettazione dell'implementazione di Azure AD Connect. In questo argomento è un approfondimento su alcune aree e questi concetti sono descritte brevemente in anche altri argomenti.

## <a name="sourceanchor"></a>sourceAnchor
L'attributo sourceAnchor è definito come *un attributo non modificabile nel corso della durata di un oggetto*. Identifica in modo univoco un oggetto come oggetto stesso in locale e in Azure Active Directory. L'attributo è l'acronimo **immutableId** e i due nomi vengono utilizzati intercambiabile.

La parola non modificabile, che è "non può essere modificato", è importante in questo argomento. Poiché il valore dell'attributo non può essere modificato dopo averla impostata, è importante scegliere un modello che supporta lo scenario.

L'attributo viene utilizzato per gli scenari seguenti:

- Quando un nuovo server motore di sincronizzazione è integrato o ricreato dopo uno scenario di ripristino di emergenza, questo attributo collega gli oggetti esistenti in Azure Active Directory con gli oggetti in locale.
- Se si passa da un'identità basata solo su cloud a un modello di identità sincronizzata, questo attributo consente agli oggetti di "corrispondenza disco" gli oggetti esistenti in Azure Active Directory con gli oggetti in locale.
- Se si utilizza la federazione, questo attributo insieme **userPrincipalName** viene utilizzato nella richiesta per identificare un utente.

In questo argomento solo parla sourceAnchor in relazione agli utenti. Le stesse regole si applicano a tutti i tipi di oggetto, ma è solo per gli utenti in genere questo problema si verifica un problema.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selezione di un attributo sourceAnchor buona
Il valore dell'attributo deve eseguire le regole seguenti:

- Meno di 60 caratteri
    - Caratteri non essendo-z, a-Z o 0-9 codifica e conteggiata come 3 caratteri
- Non contiene un carattere speciale: & #92;! # $ % & * + / = ? ^ & #96; { } | ~ < > () '; : , [ ] " @ _
- Devono essere univoci
- Deve essere una stringa, intero o binario
- Non deve essere basato sul nome dell'utente, queste modifiche
- Non devono essere maiuscole e minuscole ed evitare di valori che possono variare di maiuscole/minuscole
- Devono essere assegnate quando viene creato l'oggetto

Se sourceAnchor selezionato non è di tipo stringa, quindi Azure Active Directory connettersi Base64Encode il valore dell'attributo per assicurarsi che nessuna caratteri speciali vengono visualizzate. Se si usa un altro server federativo di ADFS, verificare che il server può anche Base64Encode l'attributo.

L'attributo sourceAnchor è maiuscole e minuscole. Il valore "JohnDoe" non corrisponde "johndoe". Ma è consigliabile non due diversi oggetti con solo una differenza di maiuscole/minuscole.

Se si dispone di una singola foresta locale, quindi l'attributo è necessario utilizzare è **objectGUID**. È anche l'attributo utilizzato quando si utilizzano impostazioni express in Azure AD Connect e anche l'attributo utilizzato da DirSync.

Se si dispone di più insiemi di strutture e non spostare utenti tra insiemi di strutture e domini, **objectGUID** è un attributo utile usare anche in questo caso.

Se si spostano utenti tra insiemi di strutture e domini, è necessario trovare un attributo che non cambia o può essere spostato con gli utenti durante lo spostamento. Un approccio consigliato è introdurre un attributo sintetico. Attributo che può contenere un elemento che l'aspetto di un GUID, la soluzione appropriato. Durante la creazione di oggetti, un nuovo GUID viene creato e registrazione con data e all'utente. Una regola personalizzata sincronizzazione può essere creata nel server motore di sincronizzazione per creare un valore in base a **objectGUID** e aggiornare l'attributo selezionato in somma. Quando si sposta l'oggetto, assicurarsi di copiare anche il contenuto di questo valore.

Un'altra soluzione consiste nel selezionare un attributo esistente che si conosce rimarrà invariata. Attributi di uso comune includono **IDDipendente**. Se si considera un attributo contenente le lettere, assicurarsi che nessuna possibilità maiuscole/minuscole (lettere maiuscole e minuscole) può modificare per il valore dell'attributo non esiste. Gli attributi errati che non devono essere utilizzati includono gli attributi con il nome dell'utente. In un matrimonio o divorzio, il nome è prevista la modifica, non è consentito per l'attributo. Questo è un motivo per cui il motivo per cui gli attributi, ad esempio **userPrincipalName**, **posta elettronica**e **targetAddress** non sono inoltre possibili selezionare nell'installazione guidata di Azure AD Connect. Gli attributi contengono anche la @-character, non è consentito nel sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Modificare l'attributo sourceAnchor
Il valore dell'attributo sourceAnchor non può essere modificato dopo l'oggetto è stato creato in Azure Active Directory e l'identità è sincronizzato.

Per questo motivo, le restrizioni seguenti si applicano a Azure AD Connect:

- L'attributo sourceAnchor può essere impostato solo durante l'installazione iniziale. Se si esegue nuovamente l'installazione guidata, questa opzione è di sola lettura. Se è necessario modificare questa impostazione, è necessario disinstallare e reinstallare.
- Se si installa un altro server di Azure AD Connect, è necessario selezionare lo stesso attributo sourceAnchor utilizzato in precedenza. Se si sposta Azure AD Connect in precedenza mediante DirSync, è necessario utilizzare **objectGUID** perché è l'attributo utilizzato da DirSync.
- Se il valore di sourceAnchor viene modificato dopo l'oggetto è stata esportata in Azure Active Directory, quindi Azure AD Connect genera un errore di sincronizzazione e non consentire ulteriori modifiche che oggetto prima che è stato risolto il problema e il sourceAnchor viene modificata indietro nella directory di origine.

## <a name="azure-ad-sign-in"></a>Accesso aggiuntivo di Azure Active Directory
Durante l'integrazione con Azure Active directory locale, è importante conoscere le impostazioni di sincronizzazione possono influenza l'utente modo autentica. Azure Active Directory utilizza userPrincipalName (UPN) per eseguire l'autenticazione dell'utente. Tuttavia, quando si sincronizzano gli utenti, è necessario scegliere l'attributo da utilizzare per il valore dell'attributo userPrincipalName con attenzione.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Scegliere l'attributo userPrincipalName
Quando si seleziona l'attributo per fornire il valore di UPN da utilizzare in una Azure necessario

- I valori dell'attributo è conforme alla sintassi UPN (RFC822), che deve essere del formatousername@domain
- Il suffisso i valori di corrisponde a uno dei domini personalizzati verificati Azure Active Directory

In impostazioni express, la scelta verrà utilizzata per l'attributo è userPrincipalName. Se l'attributo userPrincipalName non contiene il valore desiderato agli utenti di accedere a Azure, quindi è necessario scegliere **Installazione personalizzata**.

### <a name="custom-domain-state-and-upn"></a>Lo stato di dominio personalizzato e UPN
È importante verificare che sia presente un dominio verificato per il suffisso UPN.

John è un utente in contoso.com. Si desidera John usare l'UPN locale john@contoso.com effettuare l'accesso a Azure dopo avere sincronizzato gli utenti con le contoso.onmicrosoft.com di Azure Active directory. A tale scopo, è necessario aggiungere e verificare contoso.com come un dominio personalizzato in Azure Active Directory prima di iniziare la sincronizzazione di utenti. Se il suffisso UPN di Luca, ad esempio contoso.com, non corrisponde a un dominio verificato in Azure Active Directory, Azure Active Directory sostituirà il suffisso UPN con contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Non routing locale domini e UPN per Azure Active Directory
In alcune organizzazioni hanno domini non routing, ad esempio contoso.local o domini semplice singola etichetta come contoso. il non utente è possibile verificare un dominio non supporta in Azure Active Directory. Azure AD Connect possibile sincronizzare solo un dominio verificato in Azure Active Directory. Quando si crea una directory di Azure Active Directory, viene creato un dominio di routing che diventa dominio predefinito per la tua promozione Azure ad esempio contoso.onmicrosoft.com. Pertanto, diventa necessario verificare qualsiasi altro dominio di routing in questo caso, nel caso in cui non si desidera sincronizzare con il dominio onmicrosoft.com predefinito.

Per altre informazioni sull'aggiunta e la verifica domini, vedere [aggiungere il proprio nome di dominio personalizzato in Azure Active Directory](active-directory-add-domain.md) .

Azure AD Connect rileva se in esecuzione in un ambiente di dominio non routing e da in modo appropriato un messaggio di avviso da se si prosegue con impostazioni express. Se si opera in un dominio non routing, è probabile che l'UPN degli utenti non Routing suffissi troppo. Ad esempio, se si esegue in contoso.local, Azure AD Connect suggerito è necessario utilizzare le impostazioni personalizzate anziché impostazioni express. Utilizza impostazioni personalizzate, comunque in grado di specificare l'attributo che deve essere utilizzato come UPN per accedere a Azure dopo che gli utenti vengono sincronizzati in Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
