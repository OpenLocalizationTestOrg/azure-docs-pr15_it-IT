<properties
   pageTitle="Esempio di applicazione per l'utilizzo con sicurezza limite ambienti | Microsoft Azure"
   description="Distribuire l'applicazione web semplice dopo la creazione di una rete Perimetrale per testare il traffico flusso scenari"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# <a name="sample-application-for-use-with-security-boundary-environments"></a>Applicazione di esempio per l'utilizzo con ambienti limite di protezione

[Tornare alla sicurezza limite Best Practices pagina][HOME]

Gli script di PowerShell possono essere eseguiti in locale nel server IIS01 e AppVM01 per installare e configurare un'applicazione web molto semplice che visualizza una pagina html dal server IIS01 front-end con il contenuto dal server AppVM01 back-end.

Questa app verranno offre un ambiente di testing semplice per molti degli esempi di rete Perimetrale e la modalità modifiche sugli endpoint, NSGs, UDR e Firewall regole possono influire flussi di traffico.

## <a name="firewall-rule-to-allow-icmp"></a>Regola firewall per consentire ICMP
Questo semplice istruzione PowerShell può essere eseguita su qualsiasi macchine Virtuali di Windows per consentire il traffico ICMP (Ping). In questo modo per semplificare la verifica e risoluzione dei problemi, consentendo il protocollo ping a passano attraverso il firewall di windows (per la maggior parte dei distros Linux che ICMP è attivata per impostazione predefinita).

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Nota:** Se si usa il seguente script, l'aggiunta di regole firewall è la prima istruzione.

## <a name="iis01---web-application-installation-script"></a>Iis01 - Script di installazione dell'applicazione Web
Questo script consente di:

1.  Aprire IMCPv4 (Ping) di windows firewall server locale per semplificare il test
2.  Installare IIS e .net Framework v4.5
3.  Creare una pagina web ASP.NET e config
4.  Modificare il pool di applicazioni predefinito per facilitare l'accesso ai file
5.  Impostare l'utente anonimo per l'account di amministratore e la password

Questo script di PowerShell deve essere eseguito in locale, mentre RDP in IIS01.

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01 - Script di installazione di File Server
Questo script consente di impostare back-end per questa semplice applicazione. Questo script consente di:

1.  Aprire IMCPv4 (Ping) del firewall per il testing più semplice
2.  Creare una nuova directory
3.  Creare un file di testo per essere in modalità remota access dalla pagina web
4.  Impostare le autorizzazioni per la directory e file per consentire l'accesso anonimo
5.  Disattivare la protezione avanzata di Internet Explorer per consentire l'esplorazione della più facile da questo server 

>[AZURE.IMPORTANT] **Procedura consigliata**: non disattivare mai la protezione avanzata di Internet Explorer in un server di produzione più è in genere una buona idea di esplorazione del web da un server di produzione. Inoltre, aprendo condivisioni file per l'accesso anonimo è una buona idea, ma fatto qui per semplicità.

Questo script di PowerShell deve essere eseguito in locale, mentre RDP in AppVM01. PowerShell è necessario eseguire come amministratore per garantire la corretta esecuzione.
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## <a name="dns01---dns-server-installation-script"></a>DNS01 - Script di installazione di Server DNS
Non esiste alcun script inclusi in questa applicazione di esempio per impostare il server DNS. Se testando le regole del firewall, NSG o UDR deve includere il traffico DNS, il server DNS01 dovranno essere manualmente il programma di installazione. Il file xml di configurazione della rete per entrambi gli esempi include DNS01 come il server DNS e il server DNS pubblico ospitato da livello 3 come backup server DNS. Il server DNS di livello 3 il server DNS effettivo utilizzato per il traffico non locale e con DNS01 non di configurazione, si verificherà alcun DNS locale.

<!--Link References-->
[HOME]: ../best-practices-network-security.md
