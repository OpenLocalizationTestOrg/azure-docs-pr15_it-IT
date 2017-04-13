<properties 
    pageTitle="Servizi e i certificati di gestione cloud | Microsoft Azure" 
    description="Informazioni su come creare e utilizzare i certificati con Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Cenni preliminari sui certificati per servizi Cloud Windows Azure
I certificati sono utilizzati in Azure per i servizi cloud ([certificati di servizio](#what-are-service-certificates)) e per l'autenticazione con l'API ([certificati di gestione](#what-are-management-certificates) quando si usa il portale di classica Azure e non ARM) di gestione. Questo argomento fornisce una panoramica generale su entrambi i tipi di certificato, come [creare](#create) e [distribuire](#deploy) per Azure.

I certificati usati in Azure sono x. 509 v3 certificati e possono essere firmati da un altro di certificazione attendibile o che possono essere autofirmati. Un certificato autofirmato è stato effettuato l'accesso dal proprio creatore e di conseguenza, non è considerato attendibile per impostazione predefinita. La maggior parte dei browser è possibile ignorare questa. Certificati devono essere utilizzati solo per se stessi quando sviluppare e testare i servizi cloud. 

I certificati utilizzati da Azure possono contenere un privato o una chiave pubblica. I certificati hanno un'identificazione personale che consente di identificare loro in modo univoco. Questa identificazione personale viene usata in Azure [file di configurazione](cloud-services-configure-ssl-certificate.md) per identificare il certificato deve utilizzare un servizio cloud. 

## <a name="what-are-service-certificates"></a>Quali sono i certificati del servizio?
Certificati di servizio vengono associati ai servizi cloud e consentono la comunicazione sicura da e verso il servizio. Ad esempio, se è stato distribuito un ruolo web, desiderate fornire un certificato che può eseguire l'autenticazione di un estremo HTTPS attualmente esposto. Certificati di servizio, definiti nella definizione del servizio, vengono installati automaticamente alla macchina virtuale che esegue un'istanza del ruolo. 

È possibile caricare i certificati di servizio al portale classica Azure tramite il portale classico Azure o tramite l'API di gestione del servizio. Certificati di servizio sono associati a un servizio cloud specifico e assegnati a una distribuzione in file di definizione del servizio.

Certificati di servizio possono essere gestiti separatamente dal provider di servizi e possono essere gestiti da persone diverse. Ad esempio uno sviluppatore può caricare un pacchetto di servizio che fa riferimento a un certificato un responsabile IT ha precedentemente caricati in Azure. Un responsabile IT possa gestire e rinnovare il certificato la modifica della configurazione del servizio senza la necessità di caricare un nuovo pacchetto di servizio. Questo è possibile perché il nome logico per il certificato e il relativo nome e posizione vengono specificati in file di definizione del servizio, mentre l'identificazione personale del certificato è specificato nel file di configurazione del servizio. Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore di identificazione personale nel file di configurazione del servizio.

## <a name="what-are-management-certificates"></a>Quali sono i certificati di gestione?
I certificati di gestione consentono di eseguire l'autenticazione con l'API di gestione del servizio fornito da Azure classica. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) utilizzerà i certificati per automatizzare configurazione e distribuzione di diversi servizi di Azure. Non si è realmente correlati a servizi cloud. 

>[AZURE.WARNING] Stai attento! Questi tipi di attestati e certificati consentono a chiunque autentica con loro per gestire l'abbonamento che sono associate. 

### <a name="limitations"></a>Limitazioni
È previsto un limite di 100 certificati di gestione per abbonamento. È inoltre disponibile un limite di 100 certificati di gestione per tutte le sottoscrizioni in proprio ID utente. dell'amministratore del servizio specifico Se è necessario per i certificati più l'ID utente per l'amministratore dell'account è già stato utilizzato per aggiungere 100 gestione certificati, è possibile aggiungere un CO-amministratore per aggiungere altri certificati. 

Prima di aggiungere più di 100 certificati, vedere se è possibile riutilizzare un certificato esistente. Utilizzo coamministratori aggiunge complessità potenzialmente non necessari per il processo di gestione dei certificati.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Creare un nuovo certificato autofirmato
È possibile utilizzare gli strumenti disponibili per creare un certificato autofirmato, purché rispettino a queste impostazioni:

* Un certificato x. 509.
* Contiene una chiave privata.
* Creato per lo scambio (file. pfx).
* Nome del soggetto deve corrispondere dominio usato per accedere al servizio cloud. 
    > Non è possibile acquistare un dominio di certificato per il cloudapp.net (o per qualsiasi Azure correlati) SSL; nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per accedere all'applicazione. Ad esempio **contoso.net**, non **contoso.cloudapp.net**.
* Minimo di crittografia a 2048 bit.
* **Servizio certificato solo**: certificato client deve trovarsi nell'archivio certificati *personali* .

Esistono due modi semplici per creare un certificato in Windows, con la `makecert.exe` utilità o IIS.

### <a name="makecertexe"></a>Makecert.exe

Questa utilità deprecata e non è più descritte di seguito. Vedere l' [articolo di MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) per ulteriori informazioni.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Se si desidera utilizzare il certificato con un indirizzo IP invece di un dominio, usare l'indirizzo IP nel parametro - DnsName.


Se si desidera utilizzare questo [certificato con il portale di gestione](../azure-api-management-certs.md), Esporta in un file **cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Sono disponibili molte pagine su internet che illustra come eseguire questa operazione con IIS. [Qui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) è trovare visualizzabile viene anche ideale. 

### <a name="java"></a>Java
È possibile usare Java per [creare un certificato](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[In questo](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) articolo viene descritto come creare i certificati con SSH.

## <a name="next-steps"></a>Passaggi successivi

[Caricare il certificato di servizio al portale di classica Azure](cloud-services-configure-ssl-certificate.md) (o il [portale di Azure](cloud-services-configure-ssl-certificate-portal.md)).

Caricare un [certificato API di gestione](../azure-api-management-certs.md) al portale di classica Azure.

>[AZURE.NOTE] Portale di Azure non consente di accedere all'API di certificati di gestione ma utilizza gli account utente.
