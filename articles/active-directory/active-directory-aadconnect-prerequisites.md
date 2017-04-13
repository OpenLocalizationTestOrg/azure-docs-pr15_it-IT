<properties
   pageTitle="Azure AD Connect: Prerequisiti e hardware | Microsoft Azure"
   description="In questo argomento vengono descritti i prerequisiti e i requisiti hardware per Azure AD Connect"
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
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Connettere i prerequisiti per Azure Active Directory
Questo argomento illustra i prerequisiti e i requisiti hardware per Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, esistono alcuni dettagli desiderati.

### <a name="azure-ad"></a>Azure Active Directory
- Una sottoscrizione Azure o una [sottoscrizione di valutazione Azure](https://azure.microsoft.com/pricing/free-trial/). Questo è solo necessaria per accedere al portale di Azure e non per l'uso di Azure AD Connect. Se si utilizza PowerShell o Office 365 non è necessaria una sottoscrizione Azure usare Azure AD Connect. Se si dispone di una licenza di Office 365 è inoltre possibile utilizzare il portale di Office 365. Con una licenza di Office 365 a pagamento è inoltre possibile ottenere nel portale di Azure dal portale di Office 365.
    - È anche possibile utilizzare la funzionalità di anteprima di Azure Active Directory nel [portale di Azure](https://portal.azure.com). Questo portale non richiede una licenza di Azure.
- [Aggiungere e verificare il dominio](active-directory-add-domain.md) si intende utilizzare in Azure Active Directory. Ad esempio, se si prevede di utilizzare contoso.com per gli utenti, verificare che il dominio è stato verificato e non si utilizza solo il dominio predefinito contoso.onmicrosoft.com.
- Un tenant di Azure Active Directory consente oggetti predefiniti 50k. Quando si verifica il dominio, il limite è aumentato a 300 oggetti k. Se è necessario anche altri oggetti in Azure Active Directory, è necessario aprire un caso di supporto per il limite maggiore ulteriormente. Se sono necessari più di 500 oggetti k è necessaria una licenza, ad esempio Office 365, Azure Active Directory base, Azure Active Directory Premium o Enterprise mobilità Suite.

### <a name="prepare-your-on-premises-data"></a>Preparare i dati in locale
- Esaminare [le funzionalità di sincronizzazione facoltativo che è possibile abilitare in Azure Active Directory](active-directory-aadconnectsyncservice-features.md) e valutare le funzionalità è necessario abilitare.

### <a name="on-premises-servers-and-environment"></a>Ambiente e server locali
- Active Directory livello di schema alla versione e foresta funziona deve essere Windows Server 2003 o versione successiva. Controller di dominio è possono eseguire tutte le versioni come vengono soddisfatti i requisiti di livello schema e strutture.
- Se si prevede di usare la caratteristica **password writeback** controller di dominio deve essere in Windows Server 2008 (con Service Pack più recente) o versioni successive. Se il controller di dominio siano presenti 2008 (pre-R2) è necessario applicare [correzione KB2386717](http://support.microsoft.com/kb/2386717).
- Controller di dominio utilizzato da Azure Active Directory deve essere modificabile. Non è supportata per l'utilizzo di un controller (controller di dominio di sola lettura) e Azure AD Connect non seguire qualsiasi reindirizzamenti di scrittura.
- Azure AD Connect non è installato in Small Business Server o Windows Server Essentials. Il server è necessario utilizzare Windows Server standard o versione successiva.
- Il server Azure AD Connect deve avere un'interfaccia grafica completa installata. Non è supportata per installare su server core.
- Azure AD Connect deve essere installato in Windows Server 2008 o versione successiva. Questo server può essere un controller di dominio o un server membro se con le impostazioni express. Se si utilizzano impostazioni personalizzate, il server è inoltre possibile autonomo e non ha aggiunto a un dominio.
- Se si installa Azure AD Connect in Windows Server 2008, assicurarsi di applicare gli ultimi aggiornamenti rapidi da Windows Update. L'installazione non è possibile iniziare con un server non aggiornato.
- Se si prevede di usare la caratteristica **la sincronizzazione delle password**, il server Azure AD Connect deve essere in Windows Server 2008 R2 SP1 o versione successiva.
- Il server Azure AD Connect deve disporre di [.NET Framework 4.5.1](#component-prerequisites) o versione successiva e [Microsoft PowerShell 3.0](#component-prerequisites) o versione successiva.
- Se viene distribuito Active Directory Federation Services, i server in cui sono installati ADFS o Proxy di applicazione Web devono essere Windows Server 2012 R2 o versione successiva. [Gestione remota Windows](#windows-remote-management) deve essere abilitata su questi server per l'installazione remota.
- Se viene distribuito Active Directory Federation Services, è necessario [Certificati SSL](#ssl-certificate-requirements).
- Se viene distribuito Active Directory Federation Services, è necessario configurare [la risoluzione dei nomi](#name-resolution-for-federation-servers).
- Azure AD Connect richiede un database di SQL Server per archiviare i dati di identità. Per impostazione predefinita è installato un SQL Server 2012 Express LocalDB (versione light di SQL Server Express) e l'account di servizio per il servizio viene creato nel computer locale. SQL Server Express ha un limite di dimensioni di 10GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un volume superiore degli oggetti directory, è necessario scegliere Installazione guidata di un'altra installazione di SQL Server.
- Se si usa un Server SQL separato, questi requisiti si applicano:
    - Azure AD Connect supporta tutte le versioni di Microsoft SQL Server da SQL Server 2008 (con SP4) per SQL Server 2014. Database SQL di Microsoft Azure è **non è supportato** come database.
    - È necessario utilizzare un confronto SQL senza maiuscole/minuscole. Tali elementi sono contrassegnati con un \_CI_ nel nome della persona. È **non è supportata** per utilizzare un confronto tra maiuscole e minuscole, identificato da \_CS_ nel nome della persona.
    - È consentito solo un motore di sincronizzazione per ogni istanza di database. È **non è supportata** per condividere l'istanza del database con sincronizzazione FIM/MIM, DirSync o sincronizzazione di Azure Active Directory.

### <a name="accounts"></a>Account
- Un account di amministratore globale di Azure Active Directory per la directory di Azure Active Directory che si desidera integrare con. Questa operazione deve essere un **account organizzazione o dell'istituto di istruzione** e non può essere un **account Microsoft**.
- Se si Usa impostazioni express o eseguire l'aggiornamento da DirSync, devono avere un account di amministratore dell'organizzazione per il dominio di Active Directory locale.
- [Account in Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) se si utilizza il percorso di installazione di impostazioni personalizzate.

### <a name="azure-ad-connect-server-configuration"></a>Configurazione del server Azure AD Connect
- Nel caso gli amministratori globali MFA abilitata, l' URL **https://secure.aadcdn.microsoftonline-p.com** deve essere nell'elenco dei siti attendibili. Viene chiesto di aggiunta all'elenco siti attendibili se non viene aggiunto prima verrà chiesto di una richiesta di MFA. È possibile usare Internet Explorer per aggiungerlo all'elenco dei siti attendibili.

### <a name="connectivity"></a>Connettività
- Il server Azure AD Connect deve risoluzione DNS per intranet e internet. Il server DNS deve essere in grado di risolvere i nomi sia di Active Directory locale, nonché i punti finali di Azure Active Directory.
- Se si dispone di firewall sulla rete Intranet ed è necessario aprire porte tra i server Azure AD Connect e controller di dominio, vedere [Azure AD Connect porte](active-directory-aadconnect-ports.md) per altre informazioni.
- Se è necessario aprire il proxy o firewall limite di cui è possano accedere URL, quindi gli URL descritti in [gli intervalli di indirizzi IP e URL di Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
    - Se si usa Microsoft Cloud in Germania o nel cloud di Microsoft Azure Government, vedere [Considerazioni sull'istanze del servizio di sincronizzazione Azure AD Connect](active-directory-aadconnect-instances.md) per gli URL.
- Azure AD Connect è per impostazione predefinita utilizza TLS 1.0 per comunicare con Azure Active Directory. È possibile modificare a TLS 1.2 eseguendo la procedura descritta in [abilitare TLS 1.2 per Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
- Se si utilizza un proxy in uscita per la connessione a Internet, è necessario aggiungere l'impostazione seguente nel file **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** per l'installazione guidata e sincronizzazione di Azure AD Connect in grado di connettersi a Internet e Azure Active Directory. Questo testo deve essere immesse nella parte inferiore del file. In questo codice, &lt;PROXYADRESS&gt; rappresenta il nome host o indirizzo IP proxy effettivo.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Se il server proxy richiede l'autenticazione, l' [account del servizio](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) deve trovarsi nel dominio ed è necessario utilizzare il percorso di installazione di impostazioni personalizzate per specificare un [account di servizio personalizzato](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Occorre inoltre una modifica al file Machine. config diversa. Con questo valore cambia in Machine. config dell'installazione guidata e motore di sincronizzazione rispondono alle richieste di autenticazione dal server proxy. Nell'installazione guidata tutte le pagine, escludendo pagina **Configura** , la firma le credenziali dell'utente vengono utilizzate. Nella pagina **Configura** al termine dell'installazione guidata è cambiato il contesto per l' [account del servizio](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) è stato creato dall'utente. La sezione Machine. config dovrebbe risultare analoga alla seguente.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Per ulteriori informazioni sull' [elemento proxy predefinito](https://msdn.microsoft.com/library/kd3cf2ex.aspx), vedere MSDN.

Nel caso di problemi con le connessioni, vedere [risolvere i problemi di connettività](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Altri
- Facoltativo: Account utente di prova per verificare la sincronizzazione.

## <a name="component-prerequisites"></a>Prerequisiti per i componenti

### <a name="powershell-and-net-framework"></a>PowerShell e .net Framework
Azure AD Connect dipende da Microsoft PowerShell e .NET Framework 4.5.1. È necessario questa versione o versione successiva nel server. A seconda della versione di Windows Server, eseguire le operazioni seguenti:

- Windows Server 2012R2
  - È installato Microsoft PowerShell per impostazione predefinita, è richiesta alcuna azione.
  - .NET framework 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi che siano installati gli aggiornamenti più recenti per Windows Server nel Pannello di controllo.
- Windows Server 2008 R2 e Windows Server 2012
  - L'ultima versione di Microsoft PowerShell è disponibile in **Windows Management Framework 4.0**, disponibile [nell'Area Download Microsoft](http://www.microsoft.com/downloads).
  - Sono disponibili [nell'Area Download Microsoft](http://www.microsoft.com/downloads).NET framework 4.5.1 e versioni successive.
- Windows Server 2008
  - L'ultima versione supportata di PowerShell è disponibile in **Windows Management Framework 3.0**, disponibile [nell'Area Download Microsoft](http://www.microsoft.com/downloads).
 - Sono disponibili [nell'Area Download Microsoft](http://www.microsoft.com/downloads).NET framework 4.5.1 e versioni successive.

### <a name="enable-tls-12-for-azure-ad-connect"></a>Abilitare TLS 1.2 per Azure AD Connect
Azure AD Connect Usa TLS 1.0 per impostazione predefinita per la crittografia delle comunicazioni tra il server di motore di sincronizzazione e Azure Active Directory. È possibile modificare questa impostazione configurazione delle applicazioni .net per usare TLS 1.2 per impostazione predefinita nel server. Sono disponibili ulteriori informazioni su TLS 1.2 nella [2960358 avviso di sicurezza Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 non è abilitata in Windows Server 2008. È necessario Windows Server 2008 R2 o versione successiva. Assicurarsi che è stata applicata la correzione di .net 4.5.1 installata per il sistema operativo, vedere [2960358 avviso di sicurezza Microsoft](https://technet.microsoft.com/security/advisory/2960358). Potrebbe essere questo o versione successiva già installato nel server.
2. Se si utilizza Windows Server 2008 R2, assicurarsi che TLS 1.2 è abilitato. In server Windows Server 2012 e versioni successive, TLS 1.2 già deve essere abilitata.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Per tutti i sistemi operativi, impostare la chiave del Registro di sistema e riavviare il server.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Se si desidera anche abilitare TLS 1.2 tra il server di motore di sincronizzazione e una sessione remota di SQL Server, assicurarsi che sono le versioni di installate per [TLS 1.2 supporto per Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Prerequisiti per la federazione installazione e configurazione

### <a name="windows-remote-management"></a>Gestione remota Windows
Quando si usa Azure AD Connect per distribuire Active Directory Federation Services o il Proxy di applicazione Web, verificare i requisiti necessari per garantire una connessione e configurazione avrà esito positivo:

- Se il server di destinazione è dominio, verificare che sia abilitata gestite Remote in Windows
    - In una finestra di comando PSH privilegi elevati, comando`Enable-PSRemoting –force`
- Se il server di destinazione è una macchina WAP join non di dominio, esistono alcuni requisiti aggiuntivi
    - Nel computer di destinazione (computer WAP):
         - Assicurarsi che la winrm (gestione remota Windows / WS-Management) tramite snap-in servizi il servizio è in esecuzione
         - In una finestra di comando PSH privilegi elevati, comando`Enable-PSRemoting –force`
    - Nel computer in cui viene eseguita la procedura guidata (se quest ' ultimo non dominio unito o non attendibile dominio):
        - In una finestra di comando PSH privilegi elevati, utilizzare il comando`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - In Server Manager:
             - aggiungere host di rete Perimetrale WAP al pool di computer (Gestione server, quindi scegliere Gestisci -> Aggiungi server … usare la scheda DNS)
             - Scheda Gestione server tutti server: fare clic con il pulsante destro server WAP e scegliere Gestisci As, immettere credenziali locali (non dominio) per il computer WAP
             - Per convalidare la connettività remota di PSH, nella scheda Server Manager tutti server: fare clic con il pulsante destro server WAP e scegliere Windows PowerShell.  Per assicurarsi che sia possono stabilire sessioni remote PowerShell deve aprire una sessione remota PSH.

### <a name="ssl-certificate-requirements"></a>Requisiti dei certificati SSL
**Importante:** è consigliabile usare lo stesso certificato SSL in tutti i nodi di farm di ADFS, nonché tutti i server proxy di applicazione Web.

- Il certificato deve essere un X509 certificato.
- È possibile usare un certificato autofirmato nel server federativi in un ambiente di prova. Tuttavia, per un ambiente di produzione, è consigliabile ottenere il certificato da un'autorità di certificazione pubblica.
    - Se utilizza un certificato che non pubblicamente attendibile, assicurarsi che il certificato installato in ogni server Proxy di applicazione Web attendibile sia nel server locale e in tutti i server federativi
- L'identità del certificato deve corrispondere al nome di servizio federazione (ad esempio sts.contoso.com).
    - L'identità è un'estensione di nome alternativo (SAN) oggetto di tipo dNSName o, se non sono presenti voci SAN, il nome del soggetto specificato come un nome comune.  
    - Più voci di SAN possono essere presente nel certificato, purché uno di essi corrisponda al nome di servizio federazione.
    - Se si prevede di utilizzare Join luogo di lavoro, una SAN aggiuntive è necessaria con il valore **enterpriseregistration.** seguito dal suffisso del nome dell'entità utente (UPN) dell'organizzazione, ad esempio **enterpriseregistration.contoso.com**.
- I certificati basati sulle chiavi generazione (CNG) successive CryptoAPI e provider di archiviazione chiavi non sono supportati. Di conseguenza, che è necessario usare un certificato basato su un CSP (provider del servizio di crittografia) e non un KSP (provider di archiviazione chiavi).
- Sono supportati i certificati di caratteri jolly.

### <a name="name-resolution-for-federation-servers"></a>Risoluzione dei nomi di server federativi
- Configurare i record DNS per il nome del servizio della federazione ADFS (ad esempio sts.contoso.com) per la rete intranet (server DNS interno) e il extranet (pubblico DNS tramite il registrar). Per la rete intranet DNS record assicurarsi di utilizzare un record e non i record CNAME. Questa operazione è necessaria l'autenticazione di windows a funzionare correttamente dal computer uniti dominio.
- Se si distribuisce più di un server ADFS o server Proxy di applicazione Web, assicurarsi di aver configurato il servizio di bilanciamento del carico e che i record DNS per il nome del servizio federazione ADFS (ad esempio sts.contoso.com) scegliere il servizio di bilanciamento del carico.
- Per l'autenticazione di windows per le applicazioni browser con Internet Explorer della rete Intranet, assicurarsi che il nome del servizio federazione ADFS (ad esempio sts.contoso.com) viene aggiunto all'area intranet in Internet Explorer. Questa operazione può essere controllata tramite criteri di gruppo e distribuita a tutti i computer di dominio.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect componenti di supporto
Di seguito è un elenco dei componenti Azure AD Connect installata nel server in cui è installato Azure AD Connect. In questo elenco riguarda l'installazione di base di Express.  Se si sceglie di utilizzare una diversa di SQL Server nella pagina Servizi di sincronizzazione installa, quindi SQL Express LocalDB non è installato in locale.

- Azure AD Connect integrità
- Assistente Microsoft Online Services accesso per i professionisti IT (installato ma non dipendenza)
- Utilità della riga di comando di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Pacchetto di Microsoft Visual C++ 2013 ridistribuzione

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisiti hardware per Azure AD Connect
Nella tabella seguente sono riportati i requisiti minimi per il computer di sincronizzazione di Azure AD Connect.

| Numero di oggetti in Active Directory. | CPU | Memoria | Dimensioni del disco rigido |
| ------------------------------------- | --- | ------ | --------------- |
| Meno di 10.000 | 1.6 GHz | 4 GB | 70 GB |
| 10.000 – 50.000 | 1.6 GHz | 4 GB | 70 GB |
| 50.000-100.000 | 1.6 GHz | 16 GB | 100 GB |
| Per gli oggetti 100.000 o più è richiesta la versione completa di SQL Server|  |  |  |
| 100.000 – 300.000 | 1.6 GHz | 32 GB | 300 GB |
| 300.000 – 600.000 | 1.6 GHz | 32 GB | 450 GB |
| Più 600.000 | 1.6 GHz | 32 GB | 500 GB |

I requisiti minimi per computer che eseguono ADFS o server di applicazioni Web è il seguente:

- CPU: Doppio principali 1.6 GHz o superiore
- MEMORIA: 2GB o versioni successive
- Macchine Virtuali Azure: Configurazione A2 o versioni successive

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
