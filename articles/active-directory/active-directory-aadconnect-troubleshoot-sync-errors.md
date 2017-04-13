<properties
    pageTitle="Azure AD Connect: Risoluzione degli errori durante la sincronizzazione | Microsoft Azure"
    description="In questo articolo viene spiegato come risolvere gli errori rilevati durante la sincronizzazione con Azure AD Connect."
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
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Risoluzione dei problemi durante la sincronizzazione
Potrebbero verificarsi errori durante la sincronizzazione dei dati sulle identità da Windows Server Active Directory (AD DS) a Azure Active Directory (Azure Active Directory). In questo articolo viene fornita una panoramica dei diversi tipi di problemi di sincronizzazione, alcune delle possibili scenari che causano tali errori e i modi possibili per correggere gli errori. In questo articolo include i tipi di errore comuni e non può coprire i possibili errori.

 In questo articolo si presuppone che il lettore conoscano sottostante [progettare concetti di Azure Active Directory e Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Con la versione più recente di Azure AD Connect \(agosto 2016 o versioni successiva\), un report di errori di sincronizzazione è disponibile nel [Portale di Azure](https://aka.ms/aadconnecthealth) come parte di Azure Active Directory connettersi integrità per la sincronizzazione.


Avvio 1 settembre 2016 funzionalità di [Azure Active Directory duplicare attributo adattabilità](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) verrà attivata per impostazione predefinita per tutte le *nuove* Azure Active Directory tenant. Questa caratteristica verrà attivata automaticamente per tenant esistente nei prossimi mesi.

Azure AD Connect esegue 3 tipi di operazioni dalla directory conserva aggiornato: importazione, la sincronizzazione e l'esportazione. Errori possono essere eseguito in tutte le operazioni. Questo articolo è incentrato principalmente in caso di errori durante l'esportazione in Azure Active Directory.

## <a name="errors-during-export-to-azure-ad"></a>Errori durante l'esportazione in Azure Active Directory
Dopo sezione vengono descritti diversi tipi di errori di sincronizzazione che possono verificarsi durante l'operazione di esportazione di Azure Active Directory utilizzando il connettore di Azure Active Directory. Il connettore può essere identificato tramite il formato dei nomi da "contoso. *onmicrosoft.com*".
Errori durante l'esportazione in Azure Active Directory indicano che l'operazione \(aggiungere, aggiornare ed eliminare ecc\) tentato da Azure AD Connect \(motore di sincronizzazione\) su Azure Active Directory non è riuscita.

![Panoramica di errori di esportazione](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Errori di mancata corrispondenza di dati
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrizione
- Quando Azure AD Connect \(motore di sincronizzazione\) indica Azure Active Directory per aggiungere o aggiornare gli oggetti, Azure Active Directory corrisponda all'oggetto in arrivo con l'attributo **sourceAnchor** per l'attributo **immutableId** di oggetti in Azure Active Directory. La corrispondenza, si chiama un **Disco corrispondenti**.
- Quando Azure Active Directory **non trova** tutti gli oggetti che corrisponde a **immutableId** dell'attributo con l'attributo **sourceAnchor** dell'oggetto in arrivo prima il provisioning di un nuovo oggetto, essa ricorre per utilizzare gli attributi ProxyAddresses e UserPrincipalName per trovare una corrispondenza. La corrispondenza, si chiama un **Corrispondono contorni**. Corrispondono contorni è progettata in modo che corrispondano oggetti già presenti in Azure Active Directory (che sono distribuiti in Azure Active Directory) con i nuovi oggetti che viene aggiunto o aggiornato durante la sincronizzazione che rappresentano la stessa entità (utenti, gruppi) in locale.
- **InvalidSoftMatch** errore si verifica quando la corrispondenza disco rigido non trova qualsiasi oggetto corrispondente **e** contorni corrispondono viene rilevato un oggetto corrispondente per tale oggetto è un valore diverso di *immutableId* rispetto all'oggetto in arrivo *SourceAnchor*, che suggerisce che l'oggetto corrispondente è stato sincronizzato con un altro oggetto da Active Directory locale.

In altre parole, affinché la corrispondenza sfumata per l'uso, l'oggetto da sfumato trovata una corrispondenza con non deve avere qualsiasi valore per *immutableId*. Se qualsiasi oggetto con *immutableId* impostato con un valore è non funziona quella disco ma che soddisfano i criteri di corrispondenza sfumato, che l'operazione genera un errore di sincronizzazione InvalidSoftMatch.

Schema di Azure Active Directory non consente di due o più oggetti abbiano lo stesso valore degli attributi seguenti. \(Non si tratta di un elenco esaustivo.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ObjectId

>[AZURE.NOTE] Funzionalità di [attributo di Active Directory Azure duplicato attributo adattabilità](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) anche software come il comportamento predefinito di Azure Active Directory.  Questo consente di ridurre il numero di errori di sincronizzazione visualizzate Azure AD Connect (nonché altri client di sincronizzazione) rendendo più flessibile Azure Active Directory in modo che vengono gestiti i duplicati ProxyAddresses e UserPrincipalName attributi presenti in ambienti locali Active Directory. Questa caratteristica non correggere gli errori di duplicazione. Così è ancora i dati devono essere corretti. Ma consente il provisioning di nuovi oggetti che in caso contrario vengono bloccati da viene effettuato il provisioning a causa di valori duplicati in Azure Active Directory. Ciò riduce anche il numero di errori di sincronizzazione restituito al client di sincronizzazione.
Se questa caratteristica è abilitata per il Tenant, gli errori di sincronizzazione InvalidSoftMatch durante il provisioning di nuovi oggetti non verrà visualizzata.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Scenari di esempio di InvalidSoftMatch
1. Due o più oggetti con lo stesso valore dell'attributo ProxyAddresses è presente in Active Directory locale. Solo in visualizzazione provisioning Azure Active Directory.
2. Due o più oggetti con lo stesso valore userPrincipalName è presente in Active Directory locale. Solo in visualizzazione provisioning Azure Active Directory.
3. Un oggetto è stato aggiunto in locale in Active Directory con lo stesso valore dell'attributo ProxyAddresses a quello di un oggetto di Azure Active Directory esistente. L'oggetto aggiunto in locale non è visualizzato provisioning di Azure Active Directory.
4. Un oggetto è stato aggiunto locale Active Directory con lo stesso valore dell'attributo userPrincipalName come quella di un account di Azure Active Directory. L'oggetto non è visualizzato provisioning di Azure Active Directory.
5. Un account sincronizzato è stato spostato dal foresta A foresta B. Azure AD Connect (motore di sincronizzazione) utilizzava attributo ObjectGUID per calcolare il SourceAnchor. Dopo lo spostamento di strutture, il valore di SourceAnchor è diverso. Il nuovo oggetto (di foresta B) non riesce a sincronizzare con l'oggetto esistente in Azure Active Directory.
6. Un oggetto sincronizzato se si ha eliminato accidentalmente da Active Directory e un nuovo oggetto è stato creato in Active Directory per la stessa entità (ad esempio utente) senza eliminare l'account di Azure Active Directory locale. Il nuovo account ha esito negativo per la sincronizzazione con l'oggetto di Azure Active Directory esistente.
7. Azure AD Connect è stato disinstallato e reinstallare. Durante la reinstallazione, un attributo diverso è stato scelto come la SourceAnchor. Tutti gli oggetti che erano precedentemente sincronizzate arrestare la sincronizzazione con errore InvalidSoftMatch.

#### <a name="example-case"></a>Maiuscole/minuscole esempio:
1. **Davide Ruspini** è un utente sincronizzato in Azure Active Directory locale Active Directory di *contoso.com*
2. Bob Smith **UserPrincipalName** viene impostato come **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** è **SourceAnchor** calcolato da Azure AD Connect utilizzando Bob Smith **objectGUID** da Sì locali Active Directory, ovvero **immutableId** per Bob Smith di Azure Active Directory.
4. Davide è installato anche seguendo i valori per l'attributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Viene aggiunto un nuovo utente, **Roberto rossi**, in locale in Active Directory.
6. Di Bob Taylor **UserPrincipalName** viene impostato come **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** è **sourceAnchor** calcolato da Azure AD Connect utilizzando di Bob Taylor **objectGUID** da Sì locali Active Directory. Oggetto di Bob Taylor non ha ancora sincronizzato con Azure Active Directory.
8. Bob Taylor sono i seguenti valori per l'attributo proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Durante la sincronizzazione, Azure AD Connect verrà riconoscere l'aggiunta di Bob Taylor in Active Directory locale e chiedere di Azure Active Directory per rendere la stessa modifica.
10. Azure Active Directory prima di tutto eseguirà corrispondenza disco rigido. Vale a dire cercherà se qualsiasi oggetto con la immutableId è uguale a "abcdefghijkl0123456789 = =". Confronta disco rigido non viene eseguito come nessun altro oggetto in Azure Active Directory avrà tale immutableId.
11. Azure Active Directory cercherà corrispondente sfumato Bob Taylor. Vale a dire cercherà se è uguale a tre valori, inclusi gli oggetti con proxyAddressessmtp:bob@contoso.com
12. Azure Active Directory troverà oggetto Bob Smith in modo che corrispondano ai criteri di corrispondenza sfumato. Ma l'oggetto è il valore di immutableId = "abcdefghijklmnopqrstuv = =". che indica l'oggetto è stato sincronizzato da un altro oggetto da Active Directory locale. In questo modo, Azure Active Directory non è possibile sfumato-corrispondenza questi oggetti e verrà generato un errore di sincronizzazione **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Come correggere l'errore InvalidSoftMatch
La causa più comune relativa all'errore InvalidSoftMatch è due oggetti con diversi SourceAnchor \(immutableId\) hanno lo stesso valore degli attributi ProxyAddresses e/o UserPrincipalName utilizzate durante il processo di corrispondenza sfumato su Azure Active Directory. Per correggere i contorni corrispondenza non valido

1.  Identificare proxyAddresses duplicato, userPrincipalName o un altro valore dell'attributo che causa l'errore. Anche identificare le due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure Active Directory connettersi integrità per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare quale oggetto deve continuare il valore di singoli utenti e quale oggetto non dovrebbe essere.
3. Rimuovere l'oggetto che non deve avere il valore valore duplicato. Si noti che apportare le modifiche nella directory in cui l'oggetto è estratto. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se le modifiche apportate in locale in Active Directory, è consigliabile Azure AD Connect sincronizzare le modifiche.

Si noti che segnalazione errori di sincronizzazione all'interno di Azure Active Directory connettersi integrità per la sincronizzazione viene aggiornato ogni 30 minuti e include gli errori di tentativo di sincronizzazione più recente.

>[AZURE.NOTE] ImmutableId, per definizione, non modificare la durata dell'oggetto. Se Azure AD Connect non è stata configurata con alcuni scenari presente nell'elenco precedente, è possibile che in situazioni in cui Azure AD Connect viene calcolato un valore diverso di SourceAnchor per l'oggetto Active Directory che rappresenta la stessa entità (stesso utente/gruppo/contatto ecc) che contiene un oggetto di Active Directory Azure esistente che si desidera continuare a usare.

#### <a name="related-articles"></a>Articoli correlati
- [Attributi duplicati o non validi impediscono la sincronizzazione della directory in Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrizione
Quando un tentativo di Azure Active Directory sfumata corrispondono due oggetti, è possibile che due oggetti di diversi "tipo di oggetto" (ad esempio utente, gruppo, contatti e così via) hanno gli stessi valori per gli attributi utilizzati per eseguire la corrispondenza contorni. Durante la duplicazione di questi attributi non è consentita in Azure Active Directory, il risultato dell'operazione possibile errore di sincronizzazione "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Scenari di esempio per correggere l'errore ObjectTypeMismatch
- Viene creato un gruppo di sicurezza abilitato alla posta in Office 365. Amministratore aggiunge un nuovo utente o un contatto in locale Active Directory (che non è sincronizzato con Azure Active Directory ancora) con lo stesso valore dell'attributo ProxyAddresses come quella del gruppo di Office 365.

#### <a name="example-case"></a>Maiuscole/minuscole di esempio

1. Amministratore crea un nuovo gruppo di sicurezza abilitato alla posta in Office 365 per il reparto imposte e fornisce un indirizzo di posta elettronica come tax@contoso.com. Consente di assegnare l'attributo ProxyAddresses per il gruppo con il valore di**smtp:tax@contoso.com**
2. Un nuovo utente unisce Contoso.com e viene creato un account per l'utente locale con proxyAddress come**smtp:tax@contoso.com**
3. Quando la sincronizzazione il nuovo account Azure AD Connect, esso verrà visualizzato l'errore "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Come correggere l'errore ObjectTypeMismatch
La causa più comune relativa all'errore ObjectTypeMismatch è due oggetti di tipo diverso (utente, gruppo, contatti e così via) hanno lo stesso valore dell'attributo ProxyAddresses. Per risolvere il ObjectTypeMismatch:

1.  Identificare proxyAddresses singoli utenti (o altro attributo) valore che causa l'errore. Anche identificare le due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure Active Directory connettersi integrità per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare quale oggetto deve continuare il valore di singoli utenti e quale oggetto non dovrebbe essere.
3. Rimuovere l'oggetto che non deve avere il valore valore duplicato. Si noti che apportare le modifiche nella directory in cui l'oggetto è estratto. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se le modifiche apportate in locale in Active Directory, è consigliabile Azure AD Connect sincronizzare le modifiche. Segnalazione errori di sincronizzazione all'interno di Azure Active Directory connettersi integrità per la sincronizzazione viene aggiornato ogni 30 minuti e include gli errori di tentativo di sincronizzazione più recente.


## <a name="duplicate-attributes"></a>Attributi duplicati
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrizione
Schema di Azure Active Directory non consente di due o più oggetti abbiano lo stesso valore degli attributi seguenti. Questo è che tutti gli oggetti in Azure Active Directory dovrà avere un valore univoco di questi attributi in una determinata istanza.

- ProxyAddresses
- UserPrincipalName

Se Azure AD Connect tenta di aggiungere un nuovo oggetto o aggiornare un oggetto esistente con un valore per gli attributi indicati che è già stato assegnato a un altro oggetto di Azure Active Directory, l'operazione verrà generato in "AttributeValueMustBeUnique" errore di sincronizzazione.
#### <a name="possible-scenarios"></a>Possibili scenari:
1. Valore duplicato è assegnata a un oggetto già sincronizzato, che è in conflitto con un altro oggetto sincronizzato.

#### <a name="example-case"></a>Maiuscole/minuscole esempio:
1. **Davide Ruspini** è un utente sincronizzato in Azure Active Directory locale Active Directory di contoso.com
2. Bob Smith **UserPrincipalName** locale viene impostato come **bobs@contoso.com**.
3. Davide è installato anche seguendo i valori per l'attributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Viene aggiunto un nuovo utente, **Roberto rossi**, in locale in Active Directory.
5. Di Bob Taylor **UserPrincipalName** viene impostato come **bobt@contoso.com**.
6. **Davide rossi** sono i seguenti valori per l'attributo **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Oggetto di Bob Taylor è sincronizzata con Azure Active Directory correttamente.
8. Amministrazione deciso di aggiornare l'attributo **ProxyAddresses** di Bob Taylor con il valore seguente: si. **smtp:bob@contoso.com**
9. Azure Active Directory tenterà di aggiornare l'oggetto di Bob Taylor in Azure Active Directory con il valore precedente, ma tale operazione avrà esito negativo come che ProxyAddresses valore è già stata assegnata a Bob Smith, l'errore "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Come correggere l'errore AttributeValueMustBeUnique
La causa più comune relativa all'errore AttributeValueMustBeUnique è due oggetti con diversi SourceAnchor \(immutableId\) hanno lo stesso valore per gli attributi ProxyAddresses e/o UserPrincipalName. Per correggere l'errore AttributeValueMustBeUnique

1.  Identificare proxyAddresses duplicato, userPrincipalName o un altro valore dell'attributo che causa l'errore. Anche identificare le due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure Active Directory connettersi integrità per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare quale oggetto deve continuare il valore di singoli utenti e quale oggetto non dovrebbe essere.
3. Rimuovere l'oggetto che non deve avere il valore valore duplicato. Si noti che apportare le modifiche nella directory in cui l'oggetto è estratto. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se le modifiche apportate in locale in Active Directory, è consigliabile Azure AD Connect sincronizzare la modifica dell'errore da corrette.

#### <a name="related-articles"></a>Articoli correlati
-[Attributi duplicati o non validi impediscono la sincronizzazione della directory in Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Errori di convalida dei dati
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrizione
Azure Active Directory vengono applicate varie limitazioni per i dati prima di consentire a tali dati consentire la scrittura nella directory. Si tratta per assicurarsi che gli utenti finali ottenere i migliori esperienze durante l'uso di applicazioni che dipendono da questi dati.
#### <a name="scenarios"></a>Scenari
un. Il valore dell'attributo UserPrincipalName contiene caratteri non valida o non supportato.
b. L'attributo UserPrincipalName non seguire il formato richiesto.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Come correggere l'errore IdentityDataValidationFailed

un. Assicurarsi che l'attributo userPrincipalName è supportati i caratteri e formato richiesto.

#### <a name="related-articles"></a>Articoli correlati
- [Preparare il provisioning degli utenti tramite la sincronizzazione della directory con Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Descrizione
Si tratta di un caso molto specifico genera un errore di sincronizzazione **"DataValidationFailed"** quando il suffisso del UserPrincipalName dell'utente viene modificato da un dominio federato a un altro dominio federato.

#### <a name="scenarios"></a>Scenari
Per un utente sincronizzato suffisso UserPrincipalName è stato modificato da un dominio federato a un altro dominio federato in locale. Ad esempio *UserPrincipalName = bob@contoso.com * è stato cambiato in *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Esempio
1. Bob Smith, un account per Contoso.com, viene aggiunto un nuovo account utente in Active Directory con l'attributo UserPrincipalNamebob@contoso.com
2. Davide sposta a una diversa divisione Contoso.com denominato Fabrikam.com e il suo UserPrincipalName viene modificato inbob@fabrikam.com
3. Contoso.com e fabrikam.com sono i domini federati con Azure Active Directory.
4. UserPrincipalName Luca non viene aggiornato e genera un errore di sincronizzazione "DataValidationFailed".

#### <a name="how-to-fix"></a>Procedura per la risoluzione
Se suffisso UserPrincipalName dell'utente è stato aggiornato da bob@ **contoso.com** per bob@ **fabrikam.com**, **contoso.com** e **fabrikam.com** dove si trovano **i domini federati**, seguire la seguente procedura per risolvere l'errore di sincronizzazione

1. Aggiornare UserPrincipalName dell'utente in Active Directory Azure da bob@contoso.com a bob@contoso.onmicrosoft.com. È possibile utilizzare il comando PowerShell seguente con il modulo di Azure Active Directory PowerShell:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Consentire il successivo ciclo di sincronizzazione tentare di sincronizzazione. La sincronizzazione ora verrà eseguita correttamente e verrà aggiornato UserPrincipalName di Roberto a bob@fabrikam.com come previsto.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrizione
Quando un attributo supera il limite di dimensioni consentito, il limite di lunghezza o il numero massimo di impostazione dello schema di Azure Active Directory, l'operazione di sincronizzazione verrà generato l'errore di sincronizzazione **LargeObject** o **ExceededAllowedLength** . In genere questo errore si verifica per gli attributi seguenti

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Scenari possibili
1. Attributo userCertificate Luca è archiviazione dei certificati troppi assegnati a Luca. Potrebbero essere presenti i certificati di versioni precedenti, scaduti.
2. ThmubnailPhoto Luca impostato in Active Directory è troppo grande per essere sincronizzata in Azure Active Directory.
3. Durante la compilazione automatica dell'attributo ProxyAddresses in Active Directory, se si ha assegnato un oggetto > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Procedura per la risoluzione

1. Assicurarsi che l'attributo che causano l'errore entro il limite di consentiti.

## <a name="related-links"></a>Collegamenti correlati
- [Individuare gli oggetti Active Directory nell'interfaccia di amministrazione Active Directory] (https://technet.microsoft.com/library/dd560661.aspx)
- [Come eseguire query su Azure Active Directory per un oggetto mediante Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
