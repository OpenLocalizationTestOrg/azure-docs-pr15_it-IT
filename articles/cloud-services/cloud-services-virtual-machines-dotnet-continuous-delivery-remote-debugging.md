<properties
    pageTitle="Attivare il debug remoto con recapito continuo | Microsoft Azure"
    description="Informazioni su come abilitare il debug remoto quando si usa recapito continuo per distribuire in Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Attivare il debug remoto quando si usa recapito continuo per pubblicare in Azure

È possibile attivare il debug remoto in Azure, per servizi cloud o macchine virtuali, quando si utilizza [recapito continuo](cloud-services-dotnet-continuous-delivery.md) per pubblicare in Azure attenendosi alla procedura seguente.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Attivazione del debug remoto per servizi cloud

1. Agente di compilazione, configurare l'ambiente iniziale per Azure come descritto in [Compilazione della riga di comando per Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Poiché il runtime di debug remoto (msvsmon.exe) è richiesto per il pacchetto, installare [Remote Tools per Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (o [Remote Tools per Microsoft Visual Studio 2013 aggiornamento 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) se si sta utilizzando Visual Studio 2013). In alternativa, è possibile copiare i file binari di debug remoto da un sistema che ha installato Visual Studio.
3. Creare un certificato come descritto in [Cenni preliminari sui certificati per servizi Cloud Windows Azure](cloud-services-certs-create.md). Mantenere la PFX e identificazione personale del certificato RDP e caricare il certificato per il servizio cloud di destinazione.
4. Utilizzare le opzioni seguenti nella riga di comando MSBuild per compilare e creare un pacchetto con remote debug attivato. (Sostituire i percorsi effettivi per i file di progetto e di sistema per gli elementi racchiusi tra parentesi quadre angolo).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`è il percorso della cartella contenente msvsmon.exe remoto strumenti per Visual Studio.
    `RemoteDebuggerConnectorVersion`è la versione di Azure SDK nel servizio cloud. Inoltre deve corrispondere alla versione installata con Visual Studio.

5. Pubblicare il servizio cloud di destinazione mediante il file di pacchetto e cscfg generato nel passaggio precedente.
6. Importare il certificato (file. pfx) nel computer in cui è Visual Studio con Azure SDK per .NET installato. Assicurarsi di importare la `CurrentUser\My` archivio certificati, in caso contrario inserirlo come allegato per il debug in Visual Studio avrà esito negativo.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Attivazione del debug remoto per macchine virtuali

1. Creare una macchina virtuale Azure. Vedere [creare una macchina virtuale che eseguono Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [creare e gestire Azure macchine virtuali di Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Nella [pagina del portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851)consente di visualizzare dashboard della macchina virtuale per vedere della macchina virtuale **Identificazione personale del certificato RDP**. Questo valore viene utilizzato per la `ServerThumbprint` valore di configurazione dell'estensione.
3. Creare un certificato client, come indicato in [Cenni preliminari sui certificati per i servizi Cloud Azure](cloud-services-certs-create.md) (mantenere la PFX e identificazione personale del certificato RDP).
4. Installare Powershell Azure (versione 0.7.4 o versioni successive) come descritto in [come installare e configurare Azure PowerShell](../powershell-install-configure.md).
5. Eseguire il seguente script per abilitare l'estensione RemoteDebug. Sostituire i percorsi e dati personali con uno personalizzato, ad esempio il nome dell'abbonamento, il nome del servizio e identificazione personale.

    >[AZURE.NOTE] Questo script è configurato per Visual Studio 2015. Se si sta utilizzando Visual Studio 2013, modificare il `$referenceName` e `$extensionName` assegnazioni per utilizzare i `RemoteDebugVS2013` (invece di `RemoteDebugVS2015`).

    <pre>
   Aggiungere AzureAccount

    Selezionare-AzureSubscription "L'abbonamento Microsoft"

    $vm = "mytestvm1" AzureVM get - nome-nome "mytestvm1"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint in $endpoints) {Aggiungi AzureEndpoint - macchine Virtuali $vm-nome $endpoint. Name - protocollo tcp - PublicPort $endpoint. Porta-PublicPort locale $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = 1. "*" $publicConfiguration = "<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -versione $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension in $vm. MACCHINE VIRTUALI. ResourceExtensionReferences) {se (($extension. Nomeriferimento - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - e ($extension. Assegnare un nome - eq $extensionName) '- e ($extension. Versione - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Chiave = 'txt' interruzione}}

    $vm | Aggiornamento AzureVM </pre>

6. Importare il certificato (PFX) nel computer in cui è Visual Studio con Azure SDK per .NET installato.
