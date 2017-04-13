
<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare requisiti di controllo accesso | Microsoft Azure"
    description="Vengono descritti i pilastri di identità e identificazione dei requisiti di accesso per le risorse per gli utenti in un ambiente ibrido."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinare i requisiti di controllo di accesso per la soluzione di identità ibrido
Quando un'organizzazione di progettazione la soluzione di identità ibrido sono inoltre possibile utilizzare questa opportunità per verificare i requisiti di accesso per le risorse che si intende renderlo disponibile per gli utenti. Accesso ai dati tra tutte le quattro pilastri dell'identità, che sono:

- Amministrazione
- Autenticazione
- Autorizzazione
- Il controllo

Le sezioni che segue verranno illustrate le caratteristiche di autenticazione e autorizzazione in altri dettagli, gestione e controllo fanno parte del ciclo di vita di identità ibrida. Per ulteriori informazioni su queste funzionalità, leggere [attività di gestione delle identità ibrido determinazione](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) .

>[AZURE.NOTE]
Leggere [Il quattro pilastri di identità - gestione delle identità età di IT ibrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) per ulteriori informazioni su ognuno di tali pilastri.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
Esistono diversi scenari per l'autenticazione e l'autorizzazione, gli scenari seguenti avrà requisiti specifici che devono essere soddisfatti dalla soluzione identità ibrido che la società agirà da adottare. Scenari che comportano comunicazione Business to Business (B2B) è possono aggiungere una richiesta di aggiuntiva per gli amministratori IT poiché occorrerà assicurarsi che il metodo di autenticazione e l'autorizzazione utilizzato dall'organizzazione possa comunicare con i partner aziendali. Durante il processo di progettazione per i requisiti di autenticazione e l'autorizzazione, assicurarsi che le seguenti domande:

- Verrà organizzazione autenticare e autorizzare solo gli utenti che si trova in sistema di gestione delle identità?
 - Ci sono i piani per gli scenari B2B?
 - In caso affermativo, si già conoscono i protocolli (SAML, OAuth, Kerberos, token o certificati) da utilizzare per connettersi entrambe le aziende?
- È la soluzione di identità ibrido che si sta per l'adozione di supporto per i protocolli?

Un altro punto importante da prendere in considerazione è nel punto in cui l'archivio di autenticazione che verrà utilizzato da utenti e partner si desidera includere e il modello amministrativo da utilizzare. Considerare le seguenti opzioni di due principali:
- Centralizzata: in questo modello le credenziali dell'utente, criteri e amministrazione può essere centralizzate locale o nel cloud.
- Ibrido: in questo modello le credenziali dell'utente, criteri e amministrazione saranno centralizzata in locale e un replicato nel cloud.

Il modello di organizzazione adotterà variano in base alle esigenze aziendali, si vuole rispondere alle domande seguenti per identificare in cui verrà archiviato il sistema di gestione delle identità e la modalità di amministrazione da usare:

- L'organizzazione dispone di una gestione delle identità locali?
 - In caso affermativo, prevede per mantenerlo?
 - Ci sono i requisiti di conformità o normativa che l'organizzazione deve seguire tale stabilisce nel punto in cui devono essere inclusi il sistema di gestione di identità?
- L'organizzazione utilizza il single sign-on per App che si trova in locale o nel cloud?
 - In caso affermativo, l'adozione di un modello di identità ibrido influisce questo processo?

## <a name="access-control"></a>Controllo dell'accesso
Durante l'autenticazione e l'autorizzazione per gli elementi di base per consentire l'accesso ai dati aziendali tramite la convalida dell'utente, è importante controllare il livello di accesso che questi utenti avranno e il livello di accesso gli amministratori avranno le risorse che gestiscono. Soluzione identità ibrido deve avere la possibilità consentire l'accesso granulare alle risorse, delega e controllo dell'accesso base ruoli. Assicurarsi che le seguenti sono risposta riguardanti il controllo dell'accesso:

- La società ha più di un utente con privilegi elevati per gestire il sistema di identità?
 - In caso affermativo, ogni utente necessario allo stesso livello di accesso?
- La propria azienda necessario delegare l'accesso agli utenti di gestire le risorse specifiche
 - In caso affermativo, la frequenza si verifica questo evento?
- La propria azienda necessari per integrare funzionalità di controllo di accesso tra locali e cloud risorse?
- La propria azienda necessario limitare l'accesso alle risorse in base a determinate condizioni
- La propria azienda avrebbe qualsiasi applicazione che deve essere personalizzato controllare l'accesso alle risorse di?
 - In caso affermativo, in cui tali applicazioni si trovano (locale o nel cloud)?
 - In caso affermativo, in cui sono quelli di destinazione risorse presenti (locale o nel cloud)?

>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Definire strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) verranno esaminate le opzioni disponibili e vantaggi/svantaggi delle singole opzioni.  Rispondendo a queste domande è necessario selezionare l'opzione ottimale soddisfi le esigenze aziendali.

## <a name="next-steps"></a>Passaggi successivi

[Determinare i requisiti di risposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
