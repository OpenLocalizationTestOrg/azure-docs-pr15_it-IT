<properties
    pageTitle="Azure AD Connect: Topologie supportate | Microsoft Azure"
    description="In questo argomento viene descritta topologie supportate e non supportate per Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Connettere topologie per Azure Active Directory
L'obiettivo di questo argomento viene descritto locali diverse e topologie di Azure Active Directory con sincronizzazione di Azure AD Connect della soluzione di integrazione principali. Descrive le configurazioni supportate e non supportate.

Legenda per le immagini nel documento:

Descrizione | Icona
-----|-----
Strutture di Active Directory locale| ![ACTIVE DIRECTORY](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory con importazione filtrato| ![ACTIVE DIRECTORY](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Server di sincronizzazione Azure AD Connect| ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect sincronizzazione "prova modalità server"| ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync con FIM2010 o MIM2016| ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Server sincronizzazione di Azure AD Connect, dettagliate| ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Directory di Azure Active Directory |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Scenario non supportato | ![Non supportate](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Singola foresta, singolo tenant di Azure Active Directory
![Insieme di strutture singolo singola Tenant](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologia più comune è una singola foresta locale, con uno o più domini e un singolo annuncio Azure tenant. Per l'autenticazione di Azure Active Directory, viene utilizzata la sincronizzazione delle password. L'installazione rapida di Azure AD Connect supporta solo questa topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Singolo insieme di strutture, più server di sincronizzazione di un tenant di Azure Active Directory
![Insieme di strutture singolo filtrata non supportate](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Non è supportata per avere più server di sincronizzazione di Azure AD Connect collegati allo stesso tenant di Azure Active Directory, ad eccezione di un [server di gestione temporanea](#staging-server). È supportata anche se questi sono configurati per la sincronizzazione si escludono a vicenda insieme di oggetti. Si potrebbe sono considerati seguente se non è raggiungibile tutti i domini della foresta da un singolo server o per distribuire carico tra diversi server.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Più insiemi di singolo tenant di Azure Active Directory
![Insieme di strutture con più singola Tenant](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Molte organizzazioni hanno ambienti con più insiemi di Active Directory locale. Esistono diversi motivi per la presenza di più strutture di Active Directory locale. Esempi tipici sono progetti con strutture delle risorse account e di conseguenza dopo una fusione o acquisizione.

Quando si dispone di più insiemi di strutture, tutti gli insiemi di strutture devono essere raggiungibili singola Azure AD Connect server di sincronizzazione. Non è necessario aggiungere il server a un dominio. Se è necessario raggiungere tutte le strutture, il server può trovarsi in una rete Perimetrale di rete.

Installazione guidata di Azure AD Connect offre diverse opzioni per consolidare gli utenti sono rappresentati in più insiemi. L'obiettivo è che un utente è rappresentato solo una volta in Azure Active Directory. Esistono alcune topologie comuni che è possibile configurare nel percorso di installazione personalizzata nell'installazione guidata. Selezionare l'opzione corrispondente che rappresenta la topologia della pagina **in modo univoco identificazione degli utenti**. Il consolidamento solo è configurato per gli utenti. Gruppi di singoli utenti non vengono consolidati con la configurazione predefinita.

Nella sezione seguente sono descritte topologie più comuni: [topologie Separate](#multiple-forests-separate-topologies), [perfettamente](#multiple-forests-full-mesh-with-optional-galsync)e [Delle risorse di Account](#multiple-forests-account-resource-forest).

Si presuppone che la configurazione predefinita in sincronizzazione di Azure AD Connect:

1. Gli utenti hanno un solo account abilitato e strutture in cui si trova l'account viene utilizzato per eseguire l'autenticazione dell'utente. Questo presupposto è per entrambi la sincronizzazione delle password e per la federazione. UserPrincipalName e sourceAnchor/immutableID accompagnato da questa struttura.
2. Gli utenti hanno solo una cassetta postale.
3. Insieme di strutture che ospita la cassetta postale di un utente ha la migliore qualità di dati per gli attributi visibili in di Exchange elenco (globale). Se non esiste nessuna cassetta postale dell'utente, un insieme di strutture può essere utilizzato per fornire i valori degli attributi.
4. Se si dispone di una cassetta postale collegata, quindi è inoltre disponibile un altro account in un'altra foresta usata per accedere.

Se l'ambiente non corrispondono a tali ipotesi, accade quanto segue:

- Se si dispone di più account attivo o più cassette postali, il motore di sincronizzazione sceglie un e ignorare l'altro.
- Una cassetta postale collegata con nessun altro account active non verrà esportata in Azure Active Directory. L'account utente non viene rappresentato come un membro di qualsiasi gruppo. Una cassetta postale collegata in DirSync viene sempre rappresentata come una cassetta postale del normale. Questa modifica è intenzionalmente un comportamento diverso per migliorare il supporto scenari più insiemi di strutture.

Sono disponibili ulteriori dettagli comprendere [la configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Più insiemi di strutture, più server di sincronizzazione di un tenant di Azure Active Directory
![Sincronizzazione con più di strutture con più non supportate](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Non è supportata per avere più di un server di sincronizzazione di connessione di Azure Active Directory connesso a un singolo tenant di Azure Active Directory. L'eccezione è l'utilizzo di un [server di gestione temporanea](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Più insiemi di strutture-topologie separate
**Gli utenti sono rappresentati solo una volta tra tutte le directory**

![Una volta agli utenti con più foresta](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Insieme di strutture con più separate topologie](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

In questo ambiente, tutti gli insiemi di strutture locale, vengono trattate come entità separate e nessun utente presenta in altra foresta.
È non presente alcun GALSync tra gli insiemi di strutture ogni foresta ha propria organizzazione di Exchange. Questa topologia potrebbe essere la situazione dopo una fusione/acquisizione o di un'organizzazione in cui ogni apparecchio business funziona isolata tra loro. Questi insiemi sono nella stessa organizzazione di Azure Active Directory e visualizzato con un elenco indirizzi globale unificato.
Nell'immagine seguente, tutti gli oggetti in ogni foresta rappresentare una sola volta nel metaverse e aggregato nel tenant di destinazione Azure Active Directory.

### <a name="multiple-forests--match-users"></a>Più insiemi di strutture: gli utenti di corrispondenza
**Identità utente sono presenti in più elenchi in linea**

Comuni per tutti gli scenari seguenti è che distribuzione e i gruppi di sicurezza possono contenere una combinazione di utenti, contatti e FSP (identità di protezione esterna)

FSP utilizzati in somma per rappresentare i membri da altri insiemi di strutture in un gruppo di sicurezza. FSP tutti vengono risolti all'oggetto reale in Azure Active Directory.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Più insiemi di strutture-perfettamente con GALSync facoltativo
**Identità utente esistono in più elenchi in linea. Utilizzando: attributo Mail**

![Posta di strutture con più utenti](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Insieme di strutture con più completo reticolo](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topologia reticolo completo consente agli utenti e risorse individuare un insieme di strutture e simboli non vi sarà bidirezionali tra gli insiemi di strutture.

Se Exchange è presente più di un insieme di strutture, è possibile se lo si desidera che la soluzione GALSync locale. Tutti gli utenti viene rappresentato come un contatto in tutti gli altri insiemi di strutture. GALSync viene generalmente implementato in Forefront Identity Manager 2010 o Microsoft Identity Manager 2016. Azure AD Connect non può essere usata per GALSync locale.

In questo scenario, gli oggetti identità vengono uniti utilizzando l'attributo di posta elettronica. Il join di un utente con una cassetta postale in un insieme di strutture con i contatti degli altri insiemi.

### <a name="multiple-forests--account-resource-forest"></a>Più insiemi di strutture-foresta Account risorse
**Identità utente esistono in più elenchi in linea. Utilizzando: attributi ObjectSID e msExchMasterAccountSID**

![Insieme di strutture con più utenti ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Insieme di strutture con più AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

In una topologia di strutture delle risorse di account, si dispone di uno o più strutture di account con un account utente attivo. È anche uno o più strutture di risorse con account disabilitati.

In questo scenario (più) **foresta risorse** trust tutte le **strutture di account**. Insieme di strutture risorsa è in genere uno schema di Active Directory "Extended" con Exchange e Lync. In questa foresta sono contenuti tutti i servizi di Exchange e Lync, nonché di altri servizi condivisi. Gli utenti hanno un account utente disabilitato nell'insieme di strutture e la cassetta postale è collegata a foresta account.

## <a name="office-365-and-topology-considerations"></a>Considerazioni sulla topologia e Office 365
Alcuni carichi di lavoro di Office 365 sono previste alcune limitazioni per topologie supportate. Se si prevede di usare uno di questi piani, quindi leggere l'argomento topologie supportate per il carico di lavoro.

Carico di lavoro |  
--------- | ---------
Exchange Online | Se presente più di Exchange dell'organizzazione locale (ovvero, Exchange è stata distribuita in più di un insieme di strutture), è necessario utilizzare Exchange 2013 SP1 o versione successiva. I dettagli sono disponibili qui: [distribuzioni ibride con più insiemi di strutture di Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype per le aziende | Se si utilizzano più insiemi di strutture locale, è supportata solo la topologia di strutture delle risorse di account. Dettagli per topologie supportate sono disponibili qui: [requisiti dell'ambiente di Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Server di gestione temporanea
![Server di gestione temporanea](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect supporta l'installazione di un secondo server in **modalità di gestione temporanea**. Un server in questa modalità legge i dati da tutte le directory connesse ma non scrivere directory connesse. Sta utilizzando il ciclo di sincronizzazione normale, pertanto è una copia aggiornata dei dati dell'identità. In emergenza in cui il server primario non si può eseguire il al server di gestione temporaneo. A tale scopo della procedura guidata Azure AD Connect. Questo secondo server può preferenza trovarsi in un Data Center diversi poiché alcuna infrastruttura non è condivise con il server primario. È necessario copiare manualmente le modifiche di configurazione apportate nel server primario per il secondo server.

Un server di gestione temporanea può anche essere utilizzato per verificare una configurazione personalizzata nuova e l'effetto che ha sui dati. È possibile visualizzare in anteprima le modifiche e regolare la configurazione. Quando si è soddisfatti con la nuova configurazione, è possibile rendere il server di gestione temporanea il server attivo e impostare il server precedente attivo in modalità di gestione temporanea.

Questo metodo può anche essere utilizzato per sostituire il server active sync. Preparare il nuovo server e impostarlo in modalità di gestione temporanea. Verificare che sia in buono stato, disabilitare (rendendo attivo), la modalità di gestione temporanea e arrestare il server attivo.

È possibile disporre di più di un server di gestione temporaneo quando si desidera dispone di più copie di backup in centri dati diversi.

## <a name="multiple-azure-ad-tenants"></a>Più tenant di Azure Active Directory
Si consiglia di si ha un tenant singolo in Azure Active Directory per un'organizzazione.
Prima che si prevede di utilizzare più tenant di Azure Active Directory, questi argomenti coprono scenari comuni che consente di utilizzare un singolo tenant.

Argomento |  
--------- | ---------
Delega utilizzando unità amministrative | [Gestione di unità amministrative in Azure Active Directory](active-directory-administrative-units-management.md)

![Con più strutture con più tenant](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Esiste una relazione 1:1 tra un server di sincronizzazione di Azure AD Connect e un tenant di Azure Active Directory. Per ogni tenant di Azure Active Directory, è necessario un'installazione di server di sincronizzazione di Azure AD Connect. Le istanze di tenant di Azure Active Directory sono in base alla progettazione isolato e gli utenti in una non è possibile visualizzare gli utenti in altro tenant. Se viene usata la separazione, quindi si tratta di una configurazione supportata, ma in caso contrario è necessario utilizzare l'unico modello tenant di Azure Active Directory.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Ogni oggetto una sola volta in un tenant di Azure Active Directory
![Insieme di strutture singolo filtrata](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

In questa topologia, un server di sincronizzazione di Azure AD Connect sia connesso a ogni tenant di Azure Active Directory. I server di sincronizzazione di Azure AD Connect devono essere configurati per i filtri in modo che ogni dispone di un insieme di oggetti operare su si escludono a vicenda. È ad esempio possibile definire l'ambito di ogni server per un determinato dominio o unità Organizzativa. Un dominio DNS può essere registrato solo in un unico tenant di Azure Active Directory. L'UPN degli utenti in locale in Active Directory è necessario utilizzare anche spazi dei nomi separati. Ad esempio nell'immagine sopra UPN distinto tre suffissi registrati in locale in Active Directory: contoso.com, fabrikam.com e wingtiptoys.com. Gli utenti in ogni dominio di Active Directory in locale usare un altro spazio dei nomi.

Non esiste alcun GALsync tra le istanze di tenant di Azure Active Directory. La Rubrica in Exchange Online e Skype for Business solo Mostra gli utenti nello stesso tenant.

Questa topologia è le seguenti restrizioni per in caso contrario scenari supportati:

- Solo uno dei tenant Azure Active Directory possibile abilitare la distribuzione ibrida di Exchange con Active Directory locale.
- Possono essere associati a un tenant di Azure Active Directory solo dispositivi Windows 10.

Writeback riguardano anche i requisiti di si escludono a vicenda insieme di oggetti. Alcune funzionalità di writeback non sono supportate con questa topologia poiché queste caratteristiche presuppongono una singola configurazione locale:

-   Writeback di gruppo con configurazione predefinita
-   Writeback di dispositivo

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Tutti gli oggetti più volte in un tenant di Azure Active Directory
![Non è supportato solo insieme di strutture con più Tenant](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Insieme di strutture singolo con più connettori non supportati](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Non è supportato per sincronizzare lo stesso account utente più tenant di Azure Active Directory.
- È supportata per modificare una configurazione per fare in modo che gli utenti in un annuncio Azure visualizzati come contatti in un altro tenant di Azure Active Directory.
- Non è supportato per modificare sincronizzazione di Azure AD Connect per connettersi a più tenant di Azure Active Directory.

### <a name="galsync-by-using-writeback"></a>GALsync con writeback
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure Active Directory tenant sono in base alla progettazione isolato.

- Non è supportato per modificare la configurazione della sincronizzazione di Azure AD Connect per leggere i dati da un altro tenant di Azure Active Directory.
- Non è supportato per esportare gli utenti come contatti in un altro locale Active Directory utilizzando sincronizzazione di Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync con server di sincronizzazione locale
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Questa caratteristica è supportata per l'utilizzo di FIM2010/MIM2016 locale agli utenti di GALsync tra due organizzazioni di Exchange. Gli utenti dell'organizzazione viene visualizzato come esterna agli utenti i contatti all'interno dell'organizzazione. È possibile sincronizzare questi annunci locali diverse quindi ai propri tenant di Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come installare Azure AD Connect per questi scenari, vedere [installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
