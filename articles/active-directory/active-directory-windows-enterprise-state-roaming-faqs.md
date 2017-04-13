<properties
    pageTitle="Dati roaming domande frequenti e le impostazioni | Microsoft Azure"
    description="Fornisce le risposte ad alcune domande agli amministratori IT che potrebbero essere sulle impostazioni e sincronizzazione dei dati app."
    services="active-directory"
    keywords="Enterprise impostazioni di stato comune, cloud di windows, domande frequenti roaming dello stato dell'organizzazione"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="settings-and-data-roaming-faq"></a>Dati roaming domande frequenti e le impostazioni

In questo argomento fornisce le risposte ad alcune domande agli amministratori IT che potrebbe essere sulle impostazioni e sincronizzazione dei dati app.

## <a name="what-data-roams"></a>Si sposta i dati?
**Impostazioni di Windows**: le impostazioni PC incorporate nel sistema operativo Windows. In genere, si tratta di impostazioni che personalizzare il PC e includono le seguenti categorie generali:

- *Tema*, che include funzionalità, ad esempio le impostazioni del tema e barra delle applicazioni desktop.
- *Impostazioni di Internet Explorer*, tra cui recente aperto schede e Preferiti.
- *Impostazioni del browser bordo*, ad esempio Preferiti e l'elenco di lettura.
- *Le password*, incluse le password Internet, i profili di Wi-Fi e gli altri utenti.
- *Preferenze di lingua*, che include le impostazioni per i layout di tastiera, lingua del sistema, data e ora e altro ancora.
- *Accessibilità delle funzionalità di access*, ad esempio tema con contrasto elevato, l'Assistente vocale e lente di ingrandimento.
- *Impostazioni di altre finestre*, ad esempio le impostazioni del prompt ed elenco di applicazioni.


**Dati delle applicazioni**: app universale Windows è possibile scrivere i dati delle impostazioni per una cartella comune e vengono sincronizzati automaticamente i dati scritti in questa cartella. Dipende da sviluppatore dell'app singoli per progettare un'app per sfruttare questa funzionalità. Per ulteriori informazioni su come sviluppare un Windows universale app che usa comune, vedere [archiviazione appdata API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e [Windows 8 appdata comune sviluppo blog](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>L'account viene utilizzato per la sincronizzazione di impostazioni?
In Windows 8 e Windows 8.1, sincronizzazione impostazioni usate sempre account di Microsoft consumer. Gli utenti aziendali ha la possibilità di connettersi a un account Microsoft all'account di dominio Active Directory per accedere alla sincronizzazione impostazioni. In Windows 10, questo account connesso a Microsoft funzionalità deve essere sostituita con un quadro account primario e secondario.

L'account principale è definito come l'account usato per accedere a Windows. Può trattarsi di un account Microsoft, un account Azure Active Directory (Azure Active Directory), un account di Active Directory locale o un account locale. Oltre all'account principale, gli utenti di Windows 10 possono aggiungere uno o più account cloud secondario per i dispositivi. Un account secondario è in genere un account Microsoft, un account Azure Active Directory o altro account, ad esempio Gmail o Facebook. Questi account secondari forniscono l'accesso a Windows Store e altri servizi, ad esempio il single sign-on, ma non sono in grado di supportare sincronizzazione impostazioni.

In Windows 10, solo l'account principale per il dispositivo può essere usata per sincronizzazione impostazioni (vedere [come si esegue l'aggiornamento dalla sincronizzazione impostazioni dell'account Microsoft in Windows 8 di Azure Active Directory sincronizza le impostazioni di Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)).

Dati non è mai misto tra gli account utente diverso nel dispositivo. Esistono due regole per la sincronizzazione di impostazioni:

- Impostazioni di Windows diventa sempre comune con l'account principale.
- Dati App verranno contrassegnati con l'account usato per acquistare l'app. Solo le applicazioni contrassegnate con l'account principale verranno sincronizzata. La proprietà di App tagging viene determinato quando un'app viene caricamento lato e gestione di dispositivi mobili (MDM) di Windows Store.

Se non è possibile identificare il proprietario dell'app, verranno spostati con l'account principale. Se un dispositivo viene aggiornato da Windows 8 o Windows 8.1 a Windows 10, tutte le applicazioni verranno contrassegnate come acquisita dall'account Microsoft. In questo modo maggior parte degli utenti acquisiscono App tramite Windows Store e non è disponibile alcun supporto di Windows Store per gli account Azure Active Directory prima di Windows 10. Se è installata un'app tramite una licenza non in linea, l'app verrà contrassegnato con l'account principale nel dispositivo.

>[AZURE.NOTE]  
> Dispositivi Windows 10 sono di proprietà dell'organizzazione e si è connessi alla Azure Active Directory non possono più connettersi il proprio account Microsoft a un account di dominio. La possibilità di connettersi a un account Microsoft a un account di dominio e disporre sincronizzazione dei dati dell'utente per l'account Microsoft (ovvero, l'account di Microsoft roaming tramite l'account Microsoft connesso e la funzionalità di Active Directory) viene rimosso da dispositivi Windows 10 che fanno parte di un ambiente di Active Directory o Azure Active Directory connesso.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Come si esegue l'aggiornamento dalla sincronizzazione impostazioni dell'account Microsoft in Windows 8 di Azure Active Directory sincronizza le impostazioni di Windows 10?
Se si è connessi al dominio Active Directory che esegue Windows 8 o Windows 8.1 con un account Microsoft connesso, eseguire la sincronizzazione impostazioni tramite il tuo account Microsoft. Dopo l'aggiornamento a Windows 10, si continuerà a sincronizzare le impostazioni utente tramite account Microsoft, purché ci si trova un utente di dominio e il dominio di Active Directory non si connette con Azure Active Directory.

Se il dominio di Active Directory locale connettersi con Azure Active Directory, il dispositivo tenterà di sincronizzare le impostazioni utilizzando connesso account Azure Active Directory. Se l'amministratore di Azure Active Directory non attiva Enterprise stato Roaming il connesso account Azure Active Directory verrà interrotto la sincronizzazione delle impostazioni. Se si è un utente di Windows 10 e accedere con un'identità di Azure Active Directory, si verrà avviata la sincronizzazione di impostazioni di windows come amministratore consente di sincronizzazione di impostazioni tramite Azure Active Directory.

Se è archiviato qualsiasi tipo di dati personali sul dispositivo aziendale, tenere presente che il sistema operativo Windows e dati dell'applicazione verranno avviata la sincronizzazione di Azure Active Directory. Questo implica quanto segue:

- Le impostazioni dell'account Microsoft personale verranno lasciano tranne le impostazioni del lavoro o dell'istituto di istruzione account Azure Active Directory. In questo modo le impostazioni di Azure Active Directory e l'account Microsoft sincronizzare utilizzano account distinti.
- Dati personali, ad esempio le password di rete Wi-Fi, le credenziali web e Preferiti di Internet Explorer precedentemente sincronizzate tramite un account Microsoft connesso verranno sincronizzati mediante Azure Active Directory.


## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Eseguire come account Microsoft e Azure Active Directory aziendale stato Roaming interoperabilità lavoro?
Nelle versioni di novembre 2015 o versioni successive di Windows 10, Roaming dello stato dell'organizzazione è supportato solo per un singolo account alla volta. Se si accedere a Windows tramite un lavoro o dell'istituto di istruzione account Azure Active Directory, tutti i dati verranno sincronizzate tramite Azure Active Directory. Se non si accedere a Windows tramite un account Microsoft personale, tutti i dati verranno sincronizzate tramite l'account Microsoft. Appdata universale effettuerà il roaming usando solo l'accesso account principale sul dispositivo e diventa comune solo se la licenza dell'app è di proprietà di account principale. Appdata universale per le app di proprietà di un account secondario non essere sincronizzato.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Impostazioni si sincronizzano per gli account Azure Active Directory del tenant più?
Quando più Azure AD account da diversi tenant di Azure Active Directory sono nello stesso dispositivo, è necessario aggiornare del Registro di sistema del dispositivo per comunicare con Azure Rights Management (RMS Azure) per ogni tenant di Azure Active Directory.  

1. Trovare il GUID per ogni tenant di Azure Active Directory. Aprire il portale classico Azure e selezionare un tenant di Azure Active Directory. GUID per il tenant è l'URL nella barra degli indirizzi del browser. Per esempio: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Dopo aver ottenuto il GUID, sarà necessario aggiungere la chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<ID GUID del tenant >**.
Creare un nuovo valore stringa multiple (MULTI-REG-SZ) denominato **AllowedRMSServerUrls**dalla chiave **tenant ID GUID** . Per i propri dati, specificare l'URL di punto di distribuzione delle licenze di altro tenant Azure che accede al dispositivo.
3. È possibile trovare l'URL del punto di distribuzione delle licenze eseguendo il cmdlet **Get-AadrmConfiguration** . Se i valori per la **LicensingIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** sono diversi, specificare entrambi i valori. Se i valori sono uguali, specificare il valore di una sola volta.


## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quali sono le opzioni di impostazione comune per le applicazioni desktop di Windows esistente?
Roaming funziona solo per le applicazioni di Windows universale. Sono disponibili due opzioni per l'attivazione di roaming su un'applicazione desktop di Windows esistente:

- [Desktop Bridge](http://aka.ms/desktopbridge) consente di trasferire le applicazioni desktop di Windows esistente per la piattaforma Windows universale. Da qui le modifiche al codice minimo devono avvenire in modo da sfruttare il roaming dei dati di Azure Active Directory app. Il Desktop Bridge offre le app con un'identità di app, è necessario per abilitare dati app roaming per le applicazioni desktop esistente.
- [Virtualizzazione di esperienza utente (UE + V)](https://technet.microsoft.com/library/dn458947.aspx) consente di creare un modello di impostazioni personalizzate per le applicazioni desktop di Windows esistente e abilitare il roaming per le applicazioni di Win32. Questa opzione non richiede dello sviluppatore dell'app modificare codice dell'app. V UE è limitata ai locale Active Directory comuni per i clienti che hanno acquistato Microsoft Desktop ottimizzazione Pack.

Gli amministratori possono configurare UE + V per il roaming dati app desktop di Windows modificando roaming delle impostazioni del sistema operativo Windows e app universale dati tramite i [criteri di gruppo V UE](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), tra cui:

- Il roaming criteri di gruppo impostazioni di Windows
- Non vengono sincronizzati i criteri di gruppo app di Windows
- Internet Explorer roaming nella sezione delle applicazioni

Microsoft potrebbe in futuro, provare a utilizzare modi per rendere UE-V integrato in Windows ed estendere i UE + V per il roaming impostazioni tramite il cloud di Azure Active Directory.


## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso archiviare dati e le impostazioni sincronizzate in locale?
Roaming stato Enterprise archivia tutti i dati sincronizzati nel cloud Azure. V UE offre un locale roaming soluzione.

## <a name="who-owns-the-data-thats-being-roamed"></a>A chi appartengono i dati che sono vengono spostati?
Aziende proprie i dati spostate tramite Roaming dello stato dell'organizzazione. Dati vengono archiviati in un Data Center di Azure. Tutti i dati utente vengono crittografati durante il transito e inattivi nel cloud usando Azure RMS. Si tratta di un miglioramento rispetto alla sincronizzazione di impostazioni basate su account Microsoft, che solo alcuni dati riservati, ad esempio le credenziali dell'utente vengono crittografati prima che il dispositivo.

Microsoft si impegna a protezione dati del cliente. Dati delle impostazioni di un utente dell'organizzazione vengono crittografati automaticamente da Azure RMS prima dell'invio di un dispositivo con Windows 10, in modo che nessun altro utente può leggere questi dati. Se l'organizzazione ha un abbonamento a pagamento per Azure RMS, è possibile usare altre caratteristiche di Azure RMS, ad esempio rilevamento e revocare i documenti, automaticamente proteggere i messaggi di posta elettronica che contengono informazioni riservate e gestire chiavi personalizzate ("anticipare la chiave" soluzione, noto anche come BYOK). Per ulteriori informazioni su queste funzioni e funzionamento di Azure RMS, vedere [Novità di Azure Rights Management](https://technet.microsoft.com/jj585026.aspx).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>È possibile gestire sincronizzazione per un'app specifica impostazione?
In Windows 10, non esiste nessuna impostazione MDM o criteri di gruppo per disattivare il roaming per una singola applicazione. Amministratori tenant possono disabilitare la sincronizzazione appdata per tutte le App in un dispositivo gestito, ma non è un controllo più preciso livello per app o all'interno di app.

## <a name="how-can-i-enable-or-disable-roaming"></a>Come è possibile abilitare o disabilitare roaming?
Nell'applicazione di **Impostazioni** , passare ad **account** > **le impostazioni di sincronizzazione**. In questa pagina, è possibile visualizzare l'account viene utilizzato il roaming impostazioni, ed è possibile abilitare o disabilitare i singoli gruppi di impostazioni che spostate.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Che cos'è consigliato per l'attivazione comuni in Windows 10?
Microsoft ha impostazioni diverse roaming soluzioni disponibili, inclusi i profili utente, UE V ed Enterprise stato comune.  Microsoft si impegna a sviluppare un investimento Enterprise provincia Roaming in futuro le versioni di Windows. Se l'organizzazione non è pronta o familiarità con spostamento dei dati nel cloud, è consigliabile utilizzare UE V come la tecnologia comune principale. Se l'organizzazione richiede roaming supporto per le applicazioni desktop di Windows esistenti, ma si vuole passare al cloud, è consigliabile utilizzare sia Enterprise stato comune e UE V. Sebbene UE V ed Enterprise stato Roaming sono molto simili tecnologie, non sono si escludono a vicenda. Complementari per garantire che l'organizzazione fornisce ai servizi comuni che gli utenti.  

Quando si utilizza Enterprise stato Roaming e UE V, sono valide le regole seguenti:

- Roaming dello stato dell'organizzazione è l'agente comune primaria sul dispositivo. UE V viene utilizzata per completare la "distanza Win32".
- UE-V roaming per le impostazioni di Windows e dati di una versione recente UWP app dovrebbe essere disabilitati quando criteri di gruppo V UE. Questi sono già contemplati dal Roaming dello stato dell'organizzazione.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>In che modo Enterprise stato Roaming supporta un'infrastruttura VDI (VDI)?
Roaming dello stato dell'organizzazione è supportato nelle SKU client di Windows 10, ma non dal server gli SKU. Se un client macchine Virtuali è ospitato su un computer hypervisor e quando in modalità remota Accedi a macchina virtuale, verranno spostati i dati. Se il sistema operativo stesso condiviso tra più utenti e gli utenti in modalità remota accedere a un server per un'esperienza desktop completa, roaming potrebbe non funzionare. Lo scenario basato sulla sessione quest'ultimo non è supportato ufficiale.


## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>Cosa accade quando l'organizzazione Compra Azure RMS dopo l'utilizzo di roaming?
Se l'organizzazione utilizza già comuni in Windows 10 con la sottoscrizione gratuita limitato di Azure RMS, un abbonamento a pagamento Azure RMS non avrà alcun effetto sulla funzionalità di roaming e non le modifiche alla configurazione saranno richiesto dall'amministratore IT.

## <a name="known-issues"></a>Problemi noti

- Se si tenta di accedere al dispositivo Windows con una smart card o virtuale della smart card, sincronizzazione impostazioni verrà interrotte. Aggiornamenti a Windows 10 potrebbero risolvere il problema.
- È necessario l'aggiornamento cumulativo di luglio per Windows 10 (build 10586.494 o versioni successive) per la sincronizzazione per usare Preferiti di Internet Explorer.
- Dati protetti con la protezione delle informazioni di Windows non verranno sincronizzate tramite Roaming stato dell'organizzazione. Inoltre, computer con Windows informazioni protetto non verificherà sincronizzazione tema. 
- In determinate condizioni Roaming dello stato dell'organizzazione possono non essere sincronizzati dati se è configurato l'autenticazione a più fattori Azure.
    - Se il dispositivo è configurato per richiedere [l'Autenticazione a più fattori](multi-factor-authentication.md) nel portale di Azure Active Directory, si potrebbe non riuscire a impostazioni di sincronizzazione durante l'accesso a un dispositivo con Windows 10 con una password. Questo tipo di autenticazione a più fattori configurazione deve proteggere con un account di amministratore Azure. Gli utenti amministratori essere comunque possibile eseguire la sincronizzazione effettuando l'accesso ai dispositivi Windows 10 con il proprio [Account Microsoft Passport per il lavoro](active-directory-azureadjoin-passport.md) PIN oppure eseguendo l'autenticazione a più fattori durante l'accesso ad altri servizi Azure ad esempio Office 365.
    - Sincronizzazione potrebbe non riuscire se l'amministratore configura il criterio di accesso condizionale autenticazione a più fattori di Active Directory Federation Services e scade il token di accesso sul dispositivo.  Verificare che l'accesso e disconnessione utilizzando il PIN [Microsoft Passport per il lavoro](active-directory-azureadjoin-passport.md) o completare l'autenticazione a più fattori durante l'accesso ad altri servizi Azure ad esempio Office 365.

- Se un computer è dominio con la registrazione automatica per i dispositivi di Azure Active Directory, potrebbero verificarsi problemi di sincronizzazione se il computer è fuori sede per lunghi periodi di tempo e non è possibile completare l'autenticazione di dominio. Per risolvere il problema, connettere la macchina a una rete aziendale in modo che può riprendere a sincronizzazione.


## <a name="related-topics"></a>Argomenti correlati
- [Cenni preliminari sui comune stato Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
- [Abilitare lo stato dell'organizzazione roaming di Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Impostazioni di MDM per la sincronizzazione di impostazioni e dei criteri di gruppo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Informazioni di riferimento impostazioni comuni di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
