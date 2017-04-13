<properties 
    pageTitle="Come creare un ASE ILB uso dei modelli di gestione risorse Azure | Microsoft Azure" 
    description="Informazioni su come creare un ASE bilanciamento carico interno utilizzare i modelli di gestione di risorse Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Come creare un ASE ILB utilizzare i modelli di Azure Manager delle risorse

## <a name="overview"></a>Panoramica ##
Servizio di App ambienti possono essere creati con un indirizzo interno virtuali anziché un VIP pubblico.  Questo indirizzo viene fornito da un componente di Azure denominato il servizio di bilanciamento del carico interno (ILB).  È possibile creare un ASE ILB tramite il portale di Azure.  Può inoltre creata utilizzando l'automazione in Gestione risorse Azure modelli.  In questo articolo vengono illustrati i passaggi e sintassi necessarie per creare un ASE ILB con i modelli di gestione di risorse Azure.

Sono necessari tre passaggi in automatizzare la creazione di un ASE ILB:
1. Prima di tutto ASE base viene creata in una rete virtuale tramite un indirizzo di bilanciamento del carico interno invece che con un VIP pubblico.  Come parte di questo passaggio, viene assegnato un nome di dominio radice alla ASE ILB.
2. Una volta creato ASE ILB, viene caricato un certificato SSL.  
3. Il certificato SSL caricato in modo esplicito è assegnato a ASE ILB per il proprio certificato SSL "predefinito".  Questo certificato SSL da utilizzare per il traffico SSL alle app nel ASE ILB quando le app sono trattate usando il dominio radice comuni assegnato a ASE (ad esempio https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Creazione di Base ASE ILB ##
Un modello di gestione risorse Azure di esempio e il file di parametri associati, sono disponibili in GitHub [qui][quickstartilbasecreate].

La maggior parte dei parametri nel file *azuredeploy.parameters.json* sono comune per la creazione sia ILB ASEs, nonché ASEs associata a un pubblico VIP.  Nell'elenco seguente chiamate i parametri di attenzione o che sono univoci, quando si crea un ASE ILB:


- *interalLoadBalancingMode*: nella maggior parte dei casi impostare questa opzione per 3, vale a dire il traffico HTTP/HTTPS sulle porte 80/443 e i dati del controllo/porte channel ascolto da parte del servizio FTP su ASE, sarà associata a un ILB allocato virtuali indirizzo interno.  Se questa proprietà invece è impostata su 2, solo il servizio FTP correlato porte (controllo e dati di canali) saranno associate a un indirizzo ILB, mentre il traffico HTTP/HTTPS rimarrà sul VIP pubblico.
-  */DNSSuffix*: questo parametro definisce il dominio radice predefinito assegnati a di base.  In pubblica servizio App Azure, modificare il dominio radice predefinito per tutte le applicazioni web è *azurewebsites.net*.  Tuttavia poiché un ASE ILB interni alla rete virtuale del cliente, essa non possano essere utilizzati dominio radice predefinita del servizio pubblico.  Se, tuttavia, un ASE ILB deve avere un dominio radice predefinito significativo per l'utilizzo nella rete virtuale interna della società.  Ad esempio una società Contoso ipotetica potrebbe usare il dominio radice predefinito *interno contoso.com* App per che devono essere possibile risolvere e accessibile all'interno di rete virtuale Contoso. 
-  *ipSslAddressCount*: questo parametro ricavato automaticamente a un valore pari a 0 nel file *azuredeploy.json* perché ILB ASEs hanno solo un solo indirizzo ILB.  Non sono indirizzi IP SSL espliciti per un ASE ILB, pertanto pool di indirizzi IP SSL per un ASE ILB deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning. 

Una volta che viene completato il file *azuredeploy.parameters.json* per un ASE ILB, quindi ASE ILB possono essere create utilizzando il frammento di codice Powershell seguente.  Modificare il file di percorsi in modo che corrispondano dove si trovano i file di modello Manager delle risorse Azure nel computer in uso.  È anche necessario fornire i valori per il nome di distribuzione di gestione di risorse Azure e nome gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo la gestione risorse di Azure modello inviato che richiederà alcune ore per ASE ILB da creare.  Una volta completata la creazione, ASE ILB appariranno nel portale dell'esperienza utente nell'elenco di App servizio ambienti per l'abbonamento che lo ha attivato la distribuzione.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Durante il caricamento e la configurazione del certificato SSL "Predefinito" ##

Una volta creato ASE ILB, un certificato SSL deve essere associato il ASE certificato SSL "predefinito" utilizza per stabilire connessioni SSL alle app.  Per continuare con l'esempio Contoso Corporation ipotetica, se la ASE predefinito DNS suffisso è *interno contoso.com*, quindi una connessione a *https://some-random-app.internal-contoso.com* richiede un certificato SSL valido per **.internal contoso.com*. 

Esistono diversi modi per ottenere un certificato SSL valido incluso CA interne, l'acquisto di un certificato da un emittente esterno e utilizzando un certificato autofirmato.  Indipendentemente dall'origine del certificato SSL, è necessario essere configurati correttamente gli attributi di certificato seguenti:

- *Oggetto*: questo attributo deve essere impostato su **radice di .your-here.com di dominio*
- *Nome alternativo soggetto*: questo attributo deve includere entrambi * *.your-principale-dominio-here.com*e * *.scm.your-principale-dominio-here.com*.  Il motivo per la seconda voce è che le connessioni SSL al sito servizi/Kudu associati a ogni app saranno tramite un indirizzo di *your-app-name.scm.your-root-domain-here.com*modulo.

Con un certificato SSL valido in mano, sono necessari due passaggi preparatori aggiuntivi.  Il certificato SSL deve essere convertito/salvato come file. pfx.  Tenere presente che il file. pfx deve includere tutti intermedio e i certificati principali e anche deve essere protetto con una password.

Quindi il file. pfx risultante deve essere convertito in una stringa di base 64 perché il certificato SSL verrà caricato utilizzando un modello di gestione di risorse Azure.  Poiché i modelli di gestione di risorse Azure sono file di testo, il file. pfx deve essere convertito in una stringa di base 64 in modo che possono essere incluso come un parametro del modello.

Il frammento di codice Powershell seguente mostra un esempio di generazione di un certificato autofirmato, esportazione del certificato come file. pfx, convertire il file. pfx in una base 64 stringa con codifica e quindi salvare la base 64 in una stringa in un file distinto codificata.  Il codice di Powershell per la codifica base 64 è stato adattato dal [Blog di script di Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Dopo il certificato SSL è stato generato e convertito in una base 64 codificato correttamente stringa, il modello di Manager delle risorse Azure esempio GitHub per [configurare il certificato SSL predefinito] [ configuringDefaultSSLCertificate] può essere utilizzato.

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

- *appServiceEnvironmentName*: il nome del ASE ILB in corso la configurazione.
- *existingAseLocation*: stringa di testo contenente l'area geografica di Azure in cui è stata distribuita ASE ILB.  Ad esempio: "Sud Central US".
- *pfxBlobString*: il based64 codificato rappresentazione del file. pfx.  Il frammento di codice illustrato in precedenza sarebbe di copiare la stringa contenuta in "exportedcert.pfx.b64" e incollarlo come valore dell'attributo *pfxBlobString* .
- *password*: la password utilizzata per proteggere il file. pfx.
- *certificateThumbprint*: identificazione personale del certificato.  Se si recupera questo valore da Powershell (ad esempio *$certificate. Identificazione personale* dal frammento di codice precedente), è possibile usare il valore come-è.  Tuttavia, se si copia il valore dalla finestra di dialogo certificato Windows, ricordarsi di rimuovere gli spazi estranei.  *CertificateThumbprint* dovrebbe essere simile alla: AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: un identificatore di stringa descrittivo di propria scelta utilizzati per identificare il certificato.  Il nome viene utilizzato come parte del Manager delle risorse Azure identificatore univoco per l'entità *Microsoft.Web/certificates* che rappresenta il certificato SSL.  Nome **deve** termina con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE.  Questo suffisso viene utilizzato dal portale come un indicatore viene utilizzato il certificato per la protezione di un ASE ILB abilitato.


Di seguito è illustrato un esempio abbreviato di *azuredeploy.parameters.json* :


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Una volta compilato il file *azuredeploy.parameters.json* è possibile configurare il certificato SSL predefinito utilizzando il frammento di codice Powershell seguente.  Modificare il file di percorsi in modo che corrispondano dove si trovano i file di modello Manager delle risorse Azure nel computer in uso.  È anche necessario fornire i valori per il nome di distribuzione di gestione di risorse Azure e nome gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo il modello di gestione di risorse Azure viene inviato che saranno necessarie approssimativamente minuti 40 minuti per ASE front-end per applicare la modifica.  Ad esempio con ASE predefinito dimensione usando due front-end, il modello sarà necessaria circa un'ora e venti minuti per completare.  Mentre il modello è in esecuzione la ASE non saranno in grado di architetture.  

Al termine, il modello di App nell'ASE ILB sono accessibili tramite HTTPS e le connessioni verranno protetti tramite il certificato SSL predefinito.  Il certificato SSL predefinita verrà utilizzato quando App in ASE ILB sono trattate utilizzando una combinazione di nome dell'applicazione di più il nome host predefinito.  Ad esempio *https://mycustomapp.internal-contoso.com* utilizzerà il certificato SSL predefinito per **.internal contoso.com*.

Tuttavia, come le applicazioni in esecuzione il servizio di multi-tenant pubblico, gli sviluppatori possono inoltre configurare i nomi di host personalizzato per singole applicazioni e configurare binding a certificati SSL SNI univoci per singole applicazioni.  


## <a name="getting-started"></a>Guida introduttiva

Per iniziare a utilizzare App servizio ambienti, vedere [Introduzione all'ambiente di servizio App](app-service-app-service-environment-intro.md)

Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
