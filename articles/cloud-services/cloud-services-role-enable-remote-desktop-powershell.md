<properties
pageTitle="Abilitare connessione Desktop remoto per un ruolo di servizi Cloud Windows Azure tramite PowerShell"
description="Come configurare l'applicazione di servizio cloud azure tramite PowerShell per consentire connessioni desktop remoto"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Abilitare connessione Desktop remoto per un ruolo di servizi Cloud Windows Azure tramite PowerShell

>[AZURE.SELECTOR]
- [Portale classica Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile utilizzare una connessione Desktop remoto per risolvere i problemi e diagnosticare i problemi con l'applicazione mentre è in esecuzione.

In questo articolo viene descritto come abilitare desktop remoto i ruoli di servizio Cloud tramite PowerShell. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per i prerequisiti necessari per questo articolo. PowerShell utilizza l'estensione Desktop remoto in modo che è possibile attivare Desktop remoto dopo l'applicazione distribuita.


## <a name="configure-remote-desktop-from-powershell"></a>Configurare Desktop remoto da PowerShell

Il cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) consente di attivare Desktop remoto sui ruoli specificati o tutti i ruoli della distribuzione di servizio cloud. Il cmdlet consente di specificare il nome utente e la Password per l'utente desktop remoto tramite il parametro *delle credenziali* che accetta un oggetto PSCredential.

Se si usa PowerShell in modo interattivo, è possibile impostare facilmente l'oggetto PSCredential chiamando il cmdlet [Get-credenziali](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Questo comando consente di visualizzare una finestra di dialogo che consente di immettere il nome utente e la password per l'utente remoto in modo sicuro.

Dal momento che consente di PowerShell in scenari di automazione, è anche possibile impostare l'oggetto **PSCredential** in modo che non richieda l'interazione utente. Prima di tutto, è necessario impostare una password di protezione. Iniziare con specificando una password di testo normale convertirla in una stringa protetta utilizzando [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx). Successivamente è necessario convertire questa stringa protetta in una stringa standard crittografata utilizzando [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Ora è possibile salvare la stringa standard crittografata a un file di utilizzo di [Contenuto Set](https://technet.microsoft.com/library/ee176959.aspx).

È anche possibile creare un file di password di protezione in modo che non è necessario digitare la password ogni volta. Inoltre, un file di password di protezione è migliore di un file di testo normale. Consente di creare un file di password di protezione di PowerShell seguente:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Quando si imposta la password, assicurarsi che soddisfano i [requisiti di complessità](https://technet.microsoft.com/library/cc786468.aspx).

Per creare l'oggetto credenziali dal file di password di protezione, è necessario leggere il contenuto del file e convertirli in una stringa protetta utilizzando [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Il cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) accetta anche un parametro di *scadenza* , che specifica un **DateTime** in cui scadenza l'account utente. Ad esempio, è possibile impostare pochi giorni che vanno dalla data corrente e l'ora di scadenza dell'account.

In questo esempio PowerShell viene illustrato come impostare l'estensione Desktop remoto in un servizio cloud:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
È anche possibile specificare l'intervallo aperto di distribuzione e i ruoli che si desidera attivare desktop remoto. Se non vengono specificati i parametri, il cmdlet consente di desktop remoto su tutti i ruoli di intervallo aperto distribuzione di **produzione** .

L'estensione Desktop remoto è associato a una distribuzione. Se si crea una nuova distribuzione per il servizio, è necessario attivare desktop remoto in tale distribuzione. Se si vuole sempre avere attivato desktop remoto, quindi è consigliabile integrare gli script di PowerShell il flusso di lavoro di distribuzione.


## <a name="remote-desktop-into-a-role-instance"></a>Desktop remoto in un'istanza del ruolo
Il cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) viene utilizzato per desktop remoto in un'istanza di ruolo specifico del servizio cloud. È possibile utilizzare il parametro *LocalPath* di scaricare il file RDP in locale. In alternativa, è possibile utilizzare il parametro *di avvio* per avviare direttamente la finestra di dialogo connessione Desktop remoto per accedere all'istanza di ruolo di servizio cloud.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Controllare se estensione Desktop remoto è attivato in un servizio
Consente di visualizzare il cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) che è attivato o disattivato in una distribuzione di servizi desktop remoto. Il cmdlet restituisce il nome utente per l'utente desktop remoto e i ruoli che l'estensione desktop remoto è abilitato per. Per impostazione predefinita, si verifica questo evento in intervallo aperto la distribuzione ed è possibile scegliere di utilizzare invece l'intervallo di gestione temporanea aperto.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Rimuovere estensione Desktop remoto da un servizio
Se già stato abilitato l'estensione desktop remoto in una distribuzione ed è necessario aggiornare le impostazioni del desktop remoti, rimuovere l'estensione. E riattivarlo con le nuove impostazioni. Ad esempio, se si desidera impostare una nuova password per l'account utente remoto o l'account scaduto. Questa operazione è necessaria nelle distribuzioni esistenti che hanno l'estensione desktop remoto attivato. Per le nuove distribuzioni, è possibile applicare l'estensione semplicemente direttamente.

Per rimuovere l'estensione desktop remoto di distribuzione, è possibile utilizzare il cmdlet [AzureServiceRemoteDesktopExtension Rimuovi](https://msdn.microsoft.com/library/azure/dn495280.aspx) . È anche possibile specificare l'intervallo aperto di distribuzione e il ruolo da cui si desidera rimuovere l'estensione desktop remoto.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Per rimuovere completamente la configurazione di estensione, è necessario chiamare il cmdlet *rimuovere* con il parametro **UninstallConfiguration** .
>
>Il parametro **UninstallConfiguration** disinstalla tutte le configurazioni di estensione che viene applicata al servizio. Tutte le configurazioni di estensione sono associata alla configurazione del servizio. Chiamare il cmdlet *rimuovere* senza **UninstallConfiguration** Annulla l'associazione di <mark>distribuzione</mark> dalla configurazione dell'estensione, in modo efficiente rimuovendo l'estensione. Tuttavia, la configurazione dell'estensione rimane associata al servizio.



## <a name="additional-resources"></a>Risorse aggiuntive

[Come configurare i servizi Cloud](cloud-services-how-to-configure.md)
