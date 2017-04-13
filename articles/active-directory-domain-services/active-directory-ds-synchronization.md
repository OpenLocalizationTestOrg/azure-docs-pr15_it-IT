<properties
    pageTitle="Azure Active Directory servizi di dominio: La sincronizzazione in domini gestiti | Microsoft Azure"
    description="Comprendere la sincronizzazione in un dominio gestito di servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronizzazione in un dominio gestito di servizi di dominio Active Directory di Azure
Il diagramma seguente illustra funzionamento della sincronizzazione in servizi di dominio Active Directory Azure domini gestiti.

![Topologia per la sincronizzazione in servizi di dominio Active Directory di Azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronizzazione dalla directory locale al tenant di Azure Active Directory
Sincronizzazione di Azure AD Connect viene utilizzata per sincronizzare gli account utente, appartenenza credenziali esegue l'hashing al tenant di Azure Active Directory. Gli attributi dell'utente account, ad esempio l'UPN e locale SID (identificatore di protezione) vengono sincronizzati. Se si utilizzano servizi di dominio Active Directory di Azure, hash legacy credenziali necessarie per l'autenticazione Kerberos e NTLM vengono inoltre sincronizzate al tenant di Azure Active Directory.

Se si configura writeback, le modifiche che si verificano nella directory di Azure Active Directory vengono sincronizzate di Active Directory locale. Ad esempio, se si cambia la password di utilizzo delle funzionalità di modifica password self-service di Azure Active Directory, la password modificata viene aggiornata il locale dominio di Active Directory.

> [AZURE.NOTE] Utilizzare sempre la versione più recente di Azure AD Connect avere a disposizione le correzioni per tutti gli errori noti.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronizzazione dal tenant di Azure Active Directory per il dominio gestito
Account utente, appartenenze e credenziali hash vengono sincronizzati dal tenant di Azure Active Directory al proprio dominio gestito di servizi di dominio Active Directory di Azure. Questo processo di sincronizzazione è automatico. Non è necessario configurare, monitorare o gestire il processo di sincronizzazione. Il processo di sincronizzazione è anche un-bidirezionali/unidirezionale natura. Il dominio gestito è ampiamente di sola lettura ad eccezione di tutte le unità organizzative personalizzate creato. Pertanto, è possibile apportare modifiche per gli attributi dell'utente, le password degli utenti o appartenenze all'interno del dominio gestito. Di conseguenza, non viene alcuna inverso sincronizzazione delle modifiche dal proprio dominio gestito il tenant di Azure Active Directory.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronizzazione da un ambiente locale più insiemi di strutture
Molte organizzazioni hanno un'infrastruttura di identità locale complesse composta da più insiemi di account. Azure AD Connect supporta la sincronizzazione di utenti, gruppi e hash delle credenziali da ambienti con più insiemi di strutture al tenant di Azure Active Directory.

Invece del tenant di Azure Active Directory è uno molto semplice e piatta spazio dei nomi. Per consentire agli utenti di accedere in modo affidabile applicazioni protette da Azure Active Directory, risolvere i conflitti UPN tra gli account utente in diversi insiemi di strutture. Gli ORSETTI dominio gestito di servizi di dominio Active Directory Azure chiudere somiglianza al tenant di Azure Active Directory. Di conseguenza, vengono visualizzate una struttura OU nel dominio gestito. Tutti gli utenti e gruppi vengono memorizzati all'interno del contenitore di "AADDC utenti", indipendentemente dal dominio locale o nella foresta da cui sono state sincronizzate in. Configurato un'unità Organizzativa gerarchica struttura locale. Tuttavia, il dominio gestito sono ancora presenti una struttura di unità Organizzativa flat semplice.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Esclusioni - cosa non è sincronizzato con il dominio gestito
Gli oggetti o gli attributi seguenti non sono sincronizzati al tenant di Azure Active Directory o al proprio dominio gestito:

- **Esclusi gli attributi:** È possibile impedire che alcuni attributi per la sincronizzazione con il tenant di Azure Active Directory dal proprio dominio locale con Azure AD Connect. Questi attributi esclusi non sono disponibili nel dominio gestito.

- **Criteri di gruppo:** Criteri di gruppo configurati nel dominio locale non vengono sincronizzati con il dominio gestito.

- **Condivisione SYSVOL:** Allo stesso modo, il contenuto della condivisione SYSVOL nel dominio locale non vengono sincronizzato con il dominio gestito.

- **Oggetti computer:** Oggetti computer per computer connessi al proprio dominio locale non vengono sincronizzati con il dominio gestito. Questi computer non hanno una relazione di attendibilità con il proprio dominio gestito e appartengono a solo il dominio locale. Nel dominio gestito, si individuare gli oggetti computer solo per i computer che dispone in modo esplicito dominio sul dominio gestito.

- **Attributi SidHistory per utenti e gruppi:** Utente principale e il gruppo primario SID dal proprio dominio locale vengono sincronizzati con il dominio gestito. Tuttavia, gli attributi SidHistory esistenti per utenti e gruppi non verranno sincronizzati dal proprio dominio locale nel dominio gestito.

- **Strutture di unità Organizzativa:** Unità organizzativa definite nel dominio locale non vengono sincronizzati al proprio dominio gestito. Sono disponibili due unità organizzative incorporate in un dominio gestito. Per impostazione predefinita, il dominio gestito ha una struttura di unità Organizzativa flat. Tuttavia, è possibile [creare un'unità Organizzativa nel dominio gestito](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Modalità di attributi specifici vengono sincronizzati con il dominio gestito
Nella tabella seguente sono elencati alcuni attributi comuni e descrive come vengono sincronizzati al proprio dominio gestito.

| Attributo del proprio dominio gestito | Origine | Note |
|:---|:---|:---|
|UPN|Attributo UPN dell'utente nel tenant di Azure Active Directory|L'attributo UPN dal tenant di Azure Active Directory verrà sincronizzato mentre consiste nel dominio gestito. Di conseguenza, il modo più affidabile per accedere al proprio dominio gestito utilizza l'UPN.|
|SAMAccountName|MailNickname dell'utente dell'attributo nel tenant di Azure Active Directory o generate automaticamente|L'attributo SAMAccountName è estratto l'attributo mailNickname nel tenant di Azure Active Directory. Se più account utente ha lo stesso attributo mailNickname, SAMAccountName viene generato automaticamente. Se l'utente mailNickname o prefisso UPN è più di 20 caratteri, SAMAccountName viene generato automaticamente per soddisfare il limite di 20 caratteri gli attributi SAMAccountName.|
|Password|Password dell'utente da tenant di Azure Active Directory|Hash credenziali necessarie per l'autenticazione NTLM o Kerberos (denominato anche credenziali supplementare) vengono sincronizzati dal tenant di Azure Active Directory. Se il tenant di Azure Active Directory è un tenant sincronizzato, con le credenziali sono origine dal proprio dominio locale.|
|Utente/gruppo SID primario|Generate automaticamente|SID primario per gli account utente/gruppo viene generato automaticamente nel dominio gestito. Questo attributo non corrisponde a utente/gruppo primario SID dell'oggetto all'interno del locale dominio di Active Directory. La mancata corrispondenza è il dominio gestito con uno spazio dei nomi SID diverso da quello del dominio locale.|
|Cronologia SID per utenti e gruppi|Utente primario locale e SID di gruppo|L'attributo SidHistory per utenti e gruppi nel dominio gestito è impostato in modo che corrisponda all'utente primario corrispondente o al gruppo SID nel dominio locale. Questa caratteristica consente di semplificare ascensore MAIUSC delle applicazioni in locale al dominio gestito, poiché non è necessaria per le risorse re ACL.|

> [AZURE.NOTE] **Accedere al dominio gestito utilizzando il formato UPN:** L'attributo SAMAccountName potrebbe essere generato automaticamente per alcuni account utente nel dominio gestito. Se più utenti hanno lo stesso attributo mailNickname o gli utenti hanno prefissi UPN eccessiva, potrebbe essere SAMAccountName per questi utenti generato automaticamente. Di conseguenza, il formato SAMAccountName (ad esempio, ' CONTOSO100\joeuser') non è sempre un modo affidabile per accedere al dominio. SAMAccountName generato automaticamente degli utenti può variare dai loro prefisso UPN. Utilizzare il formato UPN (ad esempio 'joeuser@contoso100.com') per accedere al dominio gestito in modo affidabile.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Oggetti che non verranno sincronizzati nel tenant di Azure Active Directory dal proprio dominio gestito
Come descritto in una sezione precedente di questo articolo, non viene alcuna sincronizzazione dal proprio dominio gestito al tenant di Azure Active Directory. È possibile scegliere di [creare un personalizzato unità Organizzativa](./active-directory-ds-admin-guide-create-ou.md) nel dominio gestito. Inoltre, è possibile creare altre unità organizzative, utenti, gruppi o account del servizio all'interno di queste unità organizzative personalizzate. Nessuno degli oggetti creati nell'unità organizzative personalizzate vengono sincronizzati al tenant di Azure Active Directory. Questi oggetti sono disponibili per l'utilizzo solo all'interno di un dominio gestito. Di conseguenza, questi oggetti non vengono utilizzando i cmdlet di Azure Active Directory PowerShell, API di Azure Active Directory grafico o l'interfaccia utente di gestione di Azure Active Directory.


## <a name="related-content"></a>Contenuti correlati
- [Caratteristiche - servizi di dominio Azure](active-directory-ds-features.md)

- [Scenari di distribuzione - servizi di dominio Active Directory di Azure](active-directory-ds-scenarios.md)

- [Considerazioni sulla rete per i servizi di dominio Active Directory di Azure](active-directory-ds-networking.md)

- [Guida introduttiva a servizi di dominio Active Directory di Azure](active-directory-ds-getting-started.md)
