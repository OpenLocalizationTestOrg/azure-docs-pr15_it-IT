<properties
    pageTitle="Come generare e trasferire protetta HSM chiavi per Azure chiave archivio | Microsoft Azure"
    description="Usare questo articolo consentono di pianificare, genera e quindi trasferire il proprio chiavi HSM protetto da utilizzare con Azure chiave archivio."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Come generare e trasferire protetta HSM tasti per Azure chiave archivio

##<a name="introduction"></a>Introduzione

Per garanzia aggiunta, quando si utilizza Azure chiave archivio, è possibile importare o generare chiavi nei moduli di sicurezza hardware (HSM) che non è mai lasciare il limite di modulo di sicurezza hardware. Questo scenario viene spesso come *visualizzare la chiave*o BYOK. La HSM sono FIPS 140-2 livello 2 convalidati. Archivio chiave Azure utilizza HSM familiari nShield Thales per proteggere le chiavi.

Usare le informazioni in questo argomento che consentono di pianificare, genera e quindi trasferire il proprio chiavi HSM protetto da utilizzare con Azure chiave archivio.

Questa funzionalità non è disponibile per la Cina Azure. 

>[AZURE.NOTE] Per ulteriori informazioni su Azure chiave archivio, vedere [Novità Azure chiave archivio?](key-vault-whatis.md)  
>
>Per un'esercitazione introduttiva recupero, che include la creazione di un archivio chiave per chiavi HSM protetto, vedere [Guida introduttiva di Azure chiave archivio](key-vault-get-started.md).

Ulteriori informazioni sulla generazione e il trasferimento di una chiave HSM protetto tramite Internet:

- Generare la chiave da una workstation non in linea, in modo da ridurre i rischi.

- La chiave è crittografata con una chiave Exchange chiave (KEK), che rimane crittografato finché non viene trasferita la HSM archivio chiave Azure. La versione crittografata della chiave lascia workstation originale.

- Il set di strumenti impostate nella chiave tenant che associa la chiave nel mondo di sicurezza di Azure chiave archivio. Pertanto dopo la HSM archivio chiave Azure visualizzato e decrittografare la chiave, è possibile usarlo con solo queste HSM. Non è possibile esportare la chiave. Questa associazione viene applicata dal HSM Thales.

- Chiave Exchange chiave (KEK), viene utilizzata per crittografare il tasto viene generato all'interno di HSM archivio chiave Azure e non esportabile. La HSM imporre non può essere alcuna Cancella versione KEK di fuori di HSM. Inoltre, il set di strumenti include attestato da Thales che il KEK non è esportabile e generato all'interno di un modulo di sicurezza hardware autentico produttrice da Thales.

- Il set di strumenti include attestato da Thales che tutto il mondo di sicurezza di Azure chiave archivio viene generato anche in un modulo di sicurezza hardware genuine prodotto da Thales. Questo attestato dimostra all'utente che utilizza Microsoft genuine hardware.

- Microsoft utilizza KEKs distinto e separare al mondo di protezione in ogni area geografica di appartenenza. Questa separazione assicura la chiave può essere utilizzata solo nei data center nell'area in cui è crittografato. Ad esempio, non è utilizzata una chiave da un cliente europeo nei data center in Nord America o Asia.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Ulteriori informazioni sui servizi Thales HSM e Microsoft

Protezione e Thales è un provider globale iniziale di crittografia dei dati e le soluzioni di sicurezza informatica ai servizi finanziari, alta tecnologia, produzione, per enti pubblici e sectors tecnologia. Con un anno di 40 traccia record di protezione aziendale e informazioni per enti pubblici, le soluzioni Thales vengono utilizzate per quattro delle cinque società di energia e aerospace più grande. Le soluzioni utilizzate anche da parte dei paesi NATO 22 e proteggere più di 80 per cento delle transazioni di pagamento in tutto il mondo.

Microsoft ha collabora con Thales per migliorare lo stato dell'immagine per HSM. Questi miglioramenti consentono di ottenere vantaggi di servizi ospitati senza rinunciato alla controllare le chiavi. In particolare, questi miglioramenti consentono Microsoft di gestire la HSM in modo che non è necessario. Come un servizio cloud, Azure chiave archivio scalabilità brevi per soddisfare picchi di utilizzo dell'organizzazione. Nello stesso momento la chiave è protetta all'interno di HSM Microsoft: mantenere il controllo al ciclo di vita chiave perché generare la chiave e trasferirla HSM Microsoft.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementazione di portare la chiave (BYOK) per Azure chiave archivio

Utilizzare le seguenti informazioni e procedure se si genererà la chiave protetta HSM e trasferirla in Azure chiave archivio, ovvero la porta proprio scenario chiave (BYOK).


##<a name="prerequisites-for-byok"></a>Prerequisiti per BYOK

Vedere la tabella seguente per un elenco di prerequisiti per visualizzare la chiave (BYOK) per Azure chiave archivio.

|Requisito|Ulteriori informazioni|
|---|---|
|Una sottoscrizione di Azure|Per creare un archivio di chiave di Azure, è necessario un abbonamento a Azure: [iscrizione per versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)|
|Il livello di servizio Azure chiave archivio Premium per supportare le chiavi protetta modulo di sicurezza hardware|Per ulteriori informazioni sui livelli di servizio e le funzionalità di Azure chiave archivio, visitare il sito Web [Azure chiave archivio prezzi](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Modulo di sicurezza hardware Thales, smart card e software di supporto|È necessario avere accesso a un modulo di sicurezza Hardware Thales e base informazioni operative relative Thales HSM. Vedere [Thales Hardware sicurezza modulo](https://www.thales-esecurity.com/msrms/buy) per l'elenco dei modelli compatibili o da cui acquistare un modulo di sicurezza hardware se non si dispone di uno.|
|Hardware e software seguenti:<ol><li>Un x64 offline lavoro utilizzando un sistema operativo Windows minimo di Windows 7 e Thales nShield software almeno versione 11.50.<br/><br/>Se questa workstation è in esecuzione Windows 7, è necessario [installare Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Workstation che sia connesso a Internet e che dispone di un sistema operativo Windows minimo di Windows 7.</li><li>Un'unità USB o altri dispositivi di archiviazione portatile con almeno 16 MB di spazio libero.</li></ol>|Per motivi di sicurezza, è consigliabile che prima workstation non è connesso a una rete. Tuttavia, questa indicazione non viene applicata a livello di programmazione.<br/><br/>Si noti che nelle istruzioni che seguono, questa workstation viene considerata workstation disconnessa.</p></blockquote><br/>Inoltre, se la chiave tenant per una rete di produzione, è consigliabile utilizzare una workstation seconda, separata per scaricare il set di strumenti e caricare il tasto tenant. Ma a scopo di testing, è possibile utilizzare la stessa workstation come il primo.<br/><br/>Si noti che nelle istruzioni che seguono, la seconda workstation viene considerata workstation connesso a Internet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generare e trasferire la chiave a modulo di sicurezza hardware archivio chiave Azure

Utilizzare i cinque passaggi seguenti per generare e trasferire la chiave a un modulo di sicurezza hardware archivio di Azure chiave:

- [Passaggio 1: Preparare il workstation connesso a Internet](#step-1-prepare-your-internet-connected-workstation)
- [Passaggio 2: Preparare il workstation disconnessa](#step-2-prepare-your-disconnected-workstation)
- [Passaggio 3: Generare il codice key](#step-3-generate-your-key)
- [Passaggio 4: Preparare il tasto per il trasferimento](#step-4-prepare-your-key-for-transfer)
- [Passaggio 5: Trasferire la chiave in Azure chiave archivio](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passaggio 1: Preparare il workstation connesso a Internet
Per questo primo passaggio, eseguire le procedure seguenti sulla workstation che sia connesso a Internet.


###<a name="step-11-install-azure-powershell"></a>Passaggio 1.1: Installare PowerShell Azure

Workstation connesso a Internet, scaricare e installare il modulo di Azure PowerShell che include i cmdlet per la gestione di Azure chiave archivio. È necessaria la versione minima di 0.8.13.

Per istruzioni sull'installazione, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Passaggio 1,2: Ottenere il proprio ID sottoscrizione Azure

Avviare una sessione di PowerShell Azure e accedere al proprio account Azure tramite il comando seguente:

        Add-AzureAccount
Nella finestra del browser a comparsa, immettere il nome utente dell'account Azure e la password. Quindi, utilizzare il comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
Dall'output, individuare l'ID per l'abbonamento che verrà utilizzato per Azure chiave archivio. Sarà necessario per l'ID di abbonamento in un secondo momento.

Chiudere la finestra di PowerShell Azure.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passaggio 1.3: Scaricare il set di strumenti BYOK per Azure chiave archivio

Passare a Microsoft Download Center di [scaricare il set di strumenti di Azure chiave archivio BYOK](http://www.microsoft.com/download/details.aspx?id=45345) per la propria area geografica o l'istanza di Azure. Utilizzare le informazioni seguenti per identificare il nome del pacchetto per il download e il relativo hash pacchetto SHA-256 corrispondente:

---

**America del Nord:**

KeyVault BYOK-strumenti United States.zip

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europa:**

KeyVault-BYOK-strumenti-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Area Asia:**

KeyVault-BYOK-strumenti-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**America Latina:**

KeyVault-BYOK-strumenti-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Giappone:**

KeyVault-BYOK-strumenti-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Australia:**

KeyVault-BYOK-strumenti-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Azure per enti pubblici:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-strumenti-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canada:**

KeyVault-BYOK-strumenti-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Germania:**

KeyVault-BYOK-strumenti-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**India:**

KeyVault-BYOK-strumenti-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Per verificare l'integrità dello scaricati BYOK set di strumenti, dalla sessione di PowerShell di Azure, utilizzare il cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

Il set di strumenti include le operazioni seguenti:

- Un pacchetto della chiave Exchange chiave (KEK) il cui nome inizia con **BYOK-KEK - pkg-.**
- Un pacchetto di protezione del mondo il cui nome inizia con **BYOK-SecurityWorld - pkg-.**
- Uno script python denominato v**erifykeypackage.py.**
- Un file eseguibile della riga di comando file denominato **KeyTransferRemote.exe** e DLL associate.
- Un Visual C++ Redistributable Package, denominato **vcredist_x64.exe.**

Copiare il pacchetto di un'unità USB o altri archiviazione portatile.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Passaggio 2: Preparare il workstation disconnessa

Per il secondo passaggio, eseguire le procedure seguenti sulla workstation che non è connesso a una rete (Internet o alla rete interna).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Passaggio 2.1: Preparare la workstation disconnessa con modulo di sicurezza hardware Thales

Installare il software di supporto nCipher (Thales) in un computer Windows e quindi allegare un modulo di sicurezza hardware Thales al computer.

Assicurarsi che gli strumenti di Thales nel percorso (**%nfast_home%\bin** e **%nfast_home%\python\bin**). Ad esempio, digitare quanto segue:

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Per ulteriori informazioni, vedere la Guida utente inclusa con il modulo di sicurezza hardware Thales.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passaggio 2.2: Installare il set di strumenti BYOK workstation disconnesso

Copiare il pacchetto di set di strumenti BYOK unità USB o un altro spazio di archiviazione portatile e quindi eseguire le operazioni seguenti:

1. Estrarre i file del pacchetto scaricati in qualsiasi cartella.
2. Da una cartella, eseguire vcredist_x64.exe.
3. Seguire le istruzioni per l'installazione di componenti di runtime Visual C++ per Visual Studio 2013.

##<a name="step-3-generate-your-key"></a>Passaggio 3: Generare il codice key

Per il terzo passaggio, eseguire le procedure seguenti workstation disconnessi.

###<a name="step-31-create-a-security-world"></a>Passaggio 3.1: Creare un mondo di sicurezza

Avviare un prompt dei comandi ed eseguire il programma di nuovo mondo Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Questo programma viene creato un file di **Sicurezza World** % NFAST_KMDATA%\local\world, che corrisponde alla cartella C:\ProgramData\nCipher\Key gestione applicazioni\Impostazioni. È possibile usare valori diversi per la base ma in questo esempio viene richiesto di immettere tre schede vuote e PIN per ognuno di essi. Quindi qualsiasi due schede concedere l'accesso completo a tutto il mondo di sicurezza. Queste schede diventano l' **Amministratore scheda impostato** per il nuovo mondo di sicurezza.

Eseguire quindi le operazioni seguenti:

- Eseguire il backup del file del mondo. Proteggere e proteggere il file del mondo, le schede di amministratore e il PIN e assicurarsi che nessuna singola persona abbia accesso a più di una scheda.

###<a name="step-32-validate-the-downloaded-package"></a>Passaggio 3.2: Convalidare il pacchetto scaricato

Questo passaggio è facoltativo ma consigliato, in modo che è possibile convalidare le operazioni seguenti:

- Chiave di Exchange incluse nel set di strumenti è stata creata da un modulo di sicurezza hardware Thales originale.
- Hash del mondo sicurezza incluse nel set di strumenti è stata creata in un modulo di sicurezza hardware Thales autentico.
- Chiave di Exchange è non esportabile.

>[AZURE.NOTE]Per convalidare il pacchetto scaricato, il modulo di sicurezza hardware deve essere connesso, acceso e deve avere un mondo di sicurezza su di esso (ad esempio quello che appena creato).

Per convalidare il pacchetto scaricato:

1.  Eseguire lo script verifykeypackage.py collegando una delle operazioni seguenti, a seconda area geografica o istanza di Azure:
    - Per il Nord America:

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - Per l'Europa:

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - Per Asia:

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - America Latina:

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Per il Giappone:

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - Per l'Australia:

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Per [Enti Azure](https://azure.microsoft.com/features/gov/), che utilizza l'istanza di governo degli Stati Uniti di Azure:

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Per il Canada:

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - Per la Germania:

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - Per l'India:

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]Il software Thales include python in %NFAST_HOME%\python\bin

2.  Confermare vedere le seguenti, che indica la convalida ha esito positivo: **risultato: successo**

Questo script di convalida catena firmatario fino alla chiave principale Thales. Hash di questa chiave radice è incorporato nello script e il relativo valore deve essere **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. È inoltre possibile verificare questo valore separatamente visitando il [sito Web Thales](http://www.thalesesec.com/).

A questo punto si è pronti creare una nuova chiave.

###<a name="step-33-create-a-new-key"></a>Passaggio 3.3: Creare una nuova chiave

Generare una chiave utilizzando il programma di **generatekey** Thales.

Eseguire il seguente comando per generare la chiave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando si esegue questo comando, utilizzare queste istruzioni:

- Il parametro *proteggere* deve essere impostata su valore **modulo**, come illustrato. In questo modo si crea una chiave protetta modulo. Il set di strumenti BYOK non supporta i tasti protetto da OCS.

- Sostituire il valore di *contosokey* **ident** e **plainname** con un valore stringa. Per ridurre al minimo costi generali amministrative e ridurre il rischio di errori, è consigliabile utilizzare lo stesso valore per entrambi. Il valore **ident** deve contenere solo numeri, trattini e lettere minuscole.

- Il pubexp viene lasciato vuoto (impostazione predefinita) in questo esempio, ma è possibile specificare valori specifici. Per ulteriori informazioni, vedere la documentazione Thales.

Questo comando crea un file di chiave suddiviso in token nella cartella %NFAST_KMDATA%\local il cui nome inizia con **key_simple_**, seguito da **ident** specificata nel comando. Ad esempio: **key_simple_contosokey**. Il file contiene una chiave crittografata.

Eseguire il backup questo suddiviso in token chiave in un percorso sicuro.

>[AZURE.IMPORTANT] Quando si trasferisce in un secondo momento la chiave in Azure chiave archivio, Microsoft non è possibile esportare questo tasto all'utente in modo che diventi fondamentale di eseguire il backup del mondo chiave e la sicurezza in modo sicuro. Per istruzioni e procedure consigliate per il backup della chiave, contattare Thales.

A questo punto si è pronti trasferire la chiave all'archivio chiave Azure.

##<a name="step-4-prepare-your-key-for-transfer"></a>Passaggio 4: Preparare il tasto per il trasferimento

Per questo passaggio quarto, eseguire le procedure seguenti workstation disconnessi.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passaggio 4.1: Creare una copia della chiave con autorizzazioni ridotte

Per ridurre le autorizzazioni per la chiave da un prompt dei comandi, eseguire una delle operazioni seguenti, a seconda area geografica o istanza di Azure:

- Per il Nord America:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Per l'Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Per Asia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- America Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Per il Giappone:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Per l'Australia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Per [Enti Azure](https://azure.microsoft.com/features/gov/), che utilizza l'istanza di governo degli Stati Uniti di Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Per il Canada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- Per la Germania:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- Per l'India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Quando si esegue questo comando, sostituire *contosokey* con lo stesso valore specificato nel **3.3 passaggio: creare una nuova chiave** dal passaggio [Genera codice](#step-3-generate-your-key) .

Viene chiesto di inserire carte di amministrazione di sicurezza del mondo.

Al termine, il comando viene visualizzato **risultato: successo** e la copia della chiave con autorizzazioni ridotte siano presenti nel file denominato key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Passaggio 4.2: Esaminare la nuova copia della chiave

Facoltativamente, è possibile eseguire il Thales utilità per confermare le autorizzazioni minime per la nuova chiave:

- aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Quando si eseguono questi comandi, sostituire contosokey con lo stesso valore specificato nel **3.3 passaggio: creare una nuova chiave** dal passaggio [Genera codice](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passaggio 4.3: Crittografare il tasto con la chiave di Microsoft Exchange

Eseguire uno dei comandi seguenti, a seconda area geografica o istanza di Azure:

- Per il Nord America:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per l'Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per Asia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- America Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per il Giappone:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per l'Australia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per [Enti Azure](https://azure.microsoft.com/features/gov/), che utilizza l'istanza di governo degli Stati Uniti di Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per il Canada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per la Germania:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Per l'India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Quando si esegue questo comando, utilizzare queste istruzioni:

- Sostituire *contosokey* con l'identificatore utilizzate per generare il tasto in **3.3 passaggio: creare una nuova chiave** dal passaggio [Genera codice](#step-3-generate-your-key) .

- Sostituire *SubscriptionID* con l'ID dell'abbonamento Azure che contiene l'archivio chiave. Recuperata questo valore in precedenza, in **1.2 passaggio: ottenere il proprio ID sottoscrizione Azure** dal passaggio [Prepara workstation connesso a Internet](#step-1-prepare-your-internet-connected-workstation) .

- Sostituire *ContosoFirstHSMKey* con un'etichetta che viene usata il nome del file output.

Quando viene completata correttamente, viene visualizzato **risultato: successo** e di un nuovo file nella cartella corrente che contiene il nome seguente: .byok TransferPackage -*ContosoFirstHSMkey*

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passaggio 4.4: Copiare il pacchetto di trasferimento chiave workstation connesso a Internet

Utilizzare un'unità USB o altri archiviazione portatile per copiare il file di output ottenuto nel passaggio precedente (KeyTransferPackage ContosoFirstHSMkey.byok) alla workstation connesso a Internet.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passaggio 5: Trasferire la chiave in Azure chiave archivio

Per il passaggio finale su workstation connesso a Internet, utilizzare il cmdlet [AzureKeyVaultKey Aggiungi](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) per caricare il pacchetto di trasferimento chiave copiato da workstation disconnessa per il modulo di sicurezza hardware archivio di Azure chiave:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se il caricamento ha esito positivo, vedere visualizzare le proprietà della chiave appena aggiunto.


##<a name="next-steps"></a>Passaggi successivi

È ora possibile usare questo tasto protetta HSM nell'archivio di chiave. Per ulteriori informazioni, vedere la sezione **Se si desidera utilizzare un modulo sicurezza hardware ()** nell'esercitazione [Guida introduttiva a Azure chiave archivio](key-vault-get-started.md) .
