<properties
    pageTitle="MFA Server con Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="In questo articolo viene descritto come iniziare a utilizzare l'autenticazione a più fattori Azure e ADFS in Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Proteggere le risorse locali e cloud utilizzando Server di autenticazione a più fattori Azure con ADFS in Windows Server 2012 R2

Se si usa Active Directory Federation Services (ADFS) e si desidera cloud sicura o risorse locale, è possibile configurare Server di autenticazione a più fattori Azure per lavorare con ADFS. Questa configurazione attiva verifica in due passaggi per gli endpoint alto valore.

In questo articolo verranno trattati gli argomenti con Server di autenticazione a più fattori Azure con ADFS in Windows Server 2012 R2. Per ulteriori informazioni, informazioni su come [proteggere le risorse locali e cloud con Server di autenticazione a più fattori Azure con ADFS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Proteggere Windows Server 2012 R2 ADFS con Server Azure autenticazione a più fattori

Quando si installa Server di autenticazione a più fattori Azure, sono disponibili le opzioni seguenti:

- Installare Server di autenticazione a più fattori Azure localmente nello stesso server ADFS
- Installare la scheda autenticazione a più fattori Azure in locale nel server ADFS e quindi installare il Server di autenticazione a più fattori in un altro computer

Prima di iniziare, tenere presente le seguenti informazioni:

- Non sono necessari per installare Server di autenticazione a più fattori Azure nel server ADFS. Tuttavia, è necessario installare la scheda autenticazione a più fattori per AD FS in un Windows Server 2012 R2 che esegue ADFS. Se si tratta di una versione supportata e si installa la scheda ADFS separatamente nel server federativo ADFS, è possibile installare il server in un altro computer. Vedere le procedure seguenti per imparare a installare la scheda separatamente.

- Quando è stata progettata la scheda MFA Server AD FS, è stato stimato che ADFS potrebbe passare il nome del componente di alla scheda. Quindi, il nome dell'entità componente Impossibile entità_spost nome di un'applicazione. Tuttavia, cui questa situazione non deve essere le maiuscole/minuscole. Se l'organizzazione Usa messaggio di testo o i metodi di verifica app per dispositivi mobili, le stringhe definite nelle impostazioni di società contengono un segnaposto, <$*Nome_applicazione*$>. Questo segnaposto non viene sostituito automaticamente quando si utilizza la scheda ADFS. È consigliabile rimuovere il segnaposto da stringhe appropriate per proteggere ADFS.

- L'account da usare per accedere è necessario disporre di diritti per creare gruppi di sicurezza del servizio Active Directory.

- Installazione guidata di autenticazione a più fattori AD FS scheda Crea un gruppo di sicurezza denominato PhoneFactor Admins nell'istanza di Active Directory. Viene quindi aggiunto l'account di servizio ADFS del servizio federativo a questo gruppo. È consigliabile verificare sul controller di dominio che il gruppo PhoneFactor Admins viene effettivamente creato e che AD FS account del servizio è un membro del gruppo. Se necessario, aggiungere manualmente l'account di servizio ADFS al gruppo PhoneFactor Admins sul controller di dominio.

- Per informazioni sull'installazione di SDK di servizio Web con il portale di utente, fare riferimento [distribuzione portale utente per Server di autenticazione a più fattori Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installare Server di autenticazione a più fattori Azure in locale nel server ADFS

1. Scaricare e installare Server di autenticazione a più fattori Azure nel server ADFS. Per informazioni sull'installazione, fare riferimento a [Guida introduttiva di Azure a più fattori autenticazione Server](multi-factor-authentication-get-started-server.md).
2. Nella console di Server di autenticazione a più fattori Azure, fare clic sull'icona di **ADFS** e quindi selezionare le opzioni di **registrazione utente Consenti** e **Consenti agli utenti di selezionare metodo**.
3. Selezionare le altre opzioni che si desidera specificare per l'organizzazione.
4. Fare clic su **Installa AD FS scheda**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se viene visualizzata la finestra di Active Directory, significa che due cose. Il computer fa parte di un dominio e configurazione di Active Directory per rendere sicure le comunicazioni tra la scheda ADFS e il servizio di autenticazione a più fattori è incompleta. Fare clic su **Avanti** per completare questa configurazione, automaticamente o selezionare il **ignorare la configurazione automatica di Active Directory e configurare le impostazioni manualmente** casella di controllo e quindi fare clic su **Avanti**.
6. Se le finestre di gruppo locale è visualizzata, significa che due cose. Il computer non fa parte di un dominio e la configurazione del gruppo locale per rendere sicure le comunicazioni tra la scheda ADFS e il servizio di autenticazione a più fattori è incompleta. Fare clic su **Avanti** per completare questa configurazione, automaticamente o selezionare il **ignorare la configurazione automatica di gruppo locale e configurare le impostazioni manualmente** casella di controllo e quindi fare clic su **Avanti**.
7. Nell'installazione guidata, fare clic su **Avanti**. Server di autenticazione a più fattori Azure Crea gruppo PhoneFactor Admins e aggiunge l'account di servizio ADFS al gruppo PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Nella pagina **Programma di installazione di avvio veloce** fare clic su **Avanti**.
9. Autenticazione a più fattori AD FS scheda il programma di installazione, fare clic su **Avanti**.
10. Al termine dell'installazione, fare clic su **Chiudi** .
11. Quando la scheda è stata installata, è necessario registrare con ADFS. Aprire Windows PowerShell ed eseguire il comando seguente:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Per usare la scheda appena registrata, modificare i criteri di autenticazione globali in ADFS. Nella console di ADFS, passare al livello di nodo **Criteri di autenticazione** . Nella sezione **Autenticazione a più fattori** fare clic sul collegamento **Modifica** accanto alla sezione **Impostazioni globali** . Nella finestra **Modifica globale dei criteri di autenticazione** selezionare **Autenticazione a più fattori** come metodo di autenticazione aggiuntive e quindi fare clic su **OK**. La scheda registrata come WindowsAzureMultiFactorAuthentication. Riavviare il servizio di ADFS per la registrazione rendere effettive.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

A questo punto, Server di autenticazione a più fattori è configurato per essere un provider di autenticazione aggiuntive da utilizzare con ADFS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installare un'istanza autonoma della scheda ADFS tramite il servizio Web SDK
1. Installare SDK servizio Web nel server che esegue il Server di autenticazione a più fattori.
2. Copiare i file seguenti il \Program Files\Multi-directory fattore autenticazione Server per il server in cui si intende installare la scheda ADFS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Registro MultiFactorAuthenticationAdfsAdapter.ps1
  - Annullare la registrazione MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Eseguire il file di installazione MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Autenticazione a più fattori ADFS scheda il programma di installazione, fare clic su **Avanti** per avviare l'installazione.
5. Al termine dell'installazione, fare clic su **Chiudi** .

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Modificare il file MultiFactorAuthenticationAdfsAdapter.config

Seguire questa procedura per modificare il file MultiFactorAuthenticationAdfsAdapter.config:

1. Impostare il nodo **UseWebServiceSdk** su **true**.  
2. Impostare il valore per **WebServiceSdkUrl** all'URL di SDK servizio Web autenticazione a più fattori. Ad esempio: *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, dove certificatename è il nome del certificato.  
3. Modificare lo script di registro MultiFactorAuthenticationAdfsAdapter.ps1 aggiungendo *- ConfigurationFilePath &lt;percorso&gt; * alla fine della `Register-AdfsAuthenticationProvider` comando, in cui * &lt;percorso&gt; * è il percorso completo del file MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurare il servizio Web SDK con un nome utente e password

Sono disponibili due opzioni per la configurazione del servizio Web SDK. Il primo con un nome utente e la password, il secondo con un certificato client. Seguire questi passaggi per la prima opzione o andare per il secondo.  

1. Impostare il valore per **WebServiceSdkUsername** a un account è un membro del gruppo di sicurezza PhoneFactor Admins. Utilizzare la &lt;dominio&gt;& #92; &lt;nome utente&gt; formato.  
2. Impostare il valore per **WebServiceSdkPassword** la password dell'account appropriato.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurare il servizio Web SDK con un certificato client

Se non si desidera utilizzare un nome utente e password, seguire questa procedura per configurare il servizio Web SDK con un certificato client.

1. Ottenere un certificato client da un'autorità di certificazione per il server che esegue SDK servizio Web. Informazioni su come [ottenere i certificati client](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importare il certificato client archivio certificati personali del computer locale nel server che esegue SDK servizio Web. Verificare che di certificazione pubblico il certificato dell'autorità sia nell'archivio certificati certificati radice attendibili.  
3. Esportare le chiavi pubbliche e privatehttp del certificato client a un file. pfx.  
4. Esportare la chiave pubblica in formato base 64 in un file CER.  
5. In Server Manager, verificare che la caratteristica di autenticazione Mapping certificati Client \Web Server\Security\IIS Server Web (IIS) sia installata. Se non è installato, selezionare **Aggiungi ruoli e le funzionalità** per aggiungere questa caratteristica.  
6. In Gestione IIS, fare doppio clic su **Editor di configurazione** del sito Web che contiene la directory virtuale SDK servizio Web. È importante eseguire questa operazione al livello del sito Web e non a livello di directory virtuale.  
7. Passare alla sezione **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Impostare enabled su **true**.  
9. Impostare oneToOneCertificateMappingsEnabled su **true**.  
10. Fare clic sul pulsante **…** accanto a oneToOneMappings e quindi fare clic su **Aggiungi** collegamento.  
11. Aprire il file. cer base 64 esportati in precedenza. Rimuovere *---iniziare certificato---* *---TERMINARE certificato---*e le interruzioni di riga. Copiare la stringa risultante.  
12. Certificato imposta la stringa copiata nel passaggio precedente.  
13. Impostare enabled su **true**.  
14. Impostare il nome utente a un account è un membro del gruppo di sicurezza PhoneFactor Admins. Utilizzare la &lt;dominio&gt;& #92; &lt;nome utente&gt; formato.  
15. Impostare la password per la password dell'account appropriato e quindi chiudere l'Editor di configurazione.  
16. Fare clic sul collegamento **Applica** .  
17. Nella directory virtuale SDK servizio Web, fare doppio clic su **autenticazione**.  
18. Verificare che la rappresentazione ASP.NET e l'autenticazione di base sono impostate su **attivato**e che tutti gli altri elementi siano impostati su **disabilitato**.  
19. Nella directory virtuale SDK servizio Web, fare doppio clic su **Impostazioni SSL**.  
20. Impostare i certificati Client a **accetta**e quindi fare clic su **Applica**.  
21. Copiare il file. PFX esportato in precedenza nel server che esegue la scheda ADFS.  
22. Importare il file. pfx archivio certificati personali del computer locale.  
23. Pulsante destro del mouse e selezionare **Gestisci chiavi Private**e quindi l'accesso in lettura per l'account usato per accedere al servizio di ADFS.  
24. Aprire il certificato client e copiare l'identificazione personale dalla scheda **Dettagli** .  
25. Nel file MultiFactorAuthenticationAdfsAdapter.config impostare **WebServiceSdkCertificateThumbprint** stringa copiata nel passaggio precedente.  


Infine, per registrare la scheda, eseguire la \Program Files\Multi-MultiFactorAuthenticationAdfsAdapter.ps1 con fattore autenticazione Server\Register script di PowerShell. La scheda registrata come WindowsAzureMultiFactorAuthentication. Riavviare il servizio di ADFS per la registrazione rendere effettive.

## <a name="related-topics"></a>Argomenti correlati

Risoluzione dei problemi, consultare le [Domande frequenti di autenticazione a più fattori Azure](multi-factor-authentication-faq.md)
