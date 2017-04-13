<properties
    pageTitle="Preparare un disco rigido virtuale di Windows per caricare in Azure | Microsoft Azure"
    description="Procedure consigliate per la preparazione di un disco rigido virtuale di Windows prima di caricare in Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparare un disco rigido virtuale di Windows per caricare in Azure
Per caricare una macchina virtuale Windows locali per Azure, è necessario preparare correttamente il disco rigido virtuale (disco rigido virtuale). Esistono diverse procedure consigliate per poter completare prima di caricare un disco rigido virtuale in Azure. In questo articolo viene illustrato come preparare un disco rigido virtuale di Windows per caricare in Microsoft Azure e viene inoltre [quando e come utilizzare Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparare il disco virtuale

>[AZURE.NOTE] 
> Azure supporta solo [macchine virtuali di generazione 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) che sono in formato di file disco rigido virtuale. Nel nuovo formato VHDX non è supportato in Azure. 
>
> Il disco rigido virtuale deve essere dimensioni fisse, non dinamico. Se necessario, le istruzioni seguenti dettagli conversione da dischi VHDX o dinamico. Le dimensioni massime consentite per il disco rigido virtuale sono 1.023 GB.


Verificare il corretto funzionamento il disco rigido virtuale di Windows nel server locale. Risolvere gli eventuali errori nella macchina virtuale stesso prima di provare a convertire o caricare in Azure.

Se è necessario convertire il disco virtuale in formato richiesto per Azure, usare uno dei metodi indicati nelle sezioni seguenti. Eseguire il backup della macchina virtuale prima di eseguire qualsiasi processo di conversione disco virtuale o Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Convertire l'utilizzo di gestione di Hyper-V
- Aprire Gestione di Hyper-V e selezionare computer locale a sinistra. Nel menu sopra di essa, fare clic su **azione** > **Modificare disco**.
    - Nella schermata di **individuare virtuale sul disco rigido** , individuare e selezionare il disco virtuale.
    - Selezionare **Converti** nella schermata successiva
        - Se è necessario convertire l'abbonamento da VHDX, selezionare **disco rigido virtuale** e fare clic su **Avanti**
        - Se è necessario convertire da disco, selezionare **dimensioni fisse** e fare clic su **Avanti**

    - Individuare e selezionare **il percorso per il nuovo file disco rigido virtuale**.
    - Fare clic su **Fine** per chiudere.

### <a name="convert-using-powershell"></a>Convertire tramite PowerShell
È possibile convertire un disco virtuale utilizzando il [cmdlet di PowerShell disco rigido virtuale Converti](http://technet.microsoft.com/library/hh848454.aspx). Nell'esempio seguente, stiamo la conversione da un VHDX disco rigido virtuale e la conversione da un dinamico tipo fisso:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertire dal formato disco VMware VMDK
Se si dispone di un'immagine di macchine Virtuali di Windows nel [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK)convertirla in un disco rigido virtuale utilizzando il [Convertitore macchina virtuale Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Leggere il blog [come convertire una VMDK VMware a disco rigido virtuale Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) per altre informazioni.

## <a name="prepare-windows-configuration-for-upload"></a>Preparare la configurazione di Windows per il caricamento

> [AZURE.NOTE] Eseguire tutti i comandi con [privilegi di amministratore](https://technet.microsoft.com/library/cc947813.aspx).

1. Rimuovere eventuali route persistente statica nella tabella di routing:

    - Per visualizzare la tabella route, eseguire `route print`.
    - Consultare le sezioni della **Persistenza route** . Se c'è una route permanente, utilizzare [la distribuzione delete](https://technet.microsoft.com/library/cc739598.apx) per rimuoverla.

2. Rimuovere il proxy WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configurare il disco criteri SAN a [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Usare l'ora UTC (Coordinated Universal Time) per Windows e impostare il tipo di avvio del servizio ora di Windows (w32time) **automaticamente**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurare i servizi di Windows
5. Assicurarsi che tutti i servizi di Windows seguenti sia impostato sui **valori predefiniti di Windows**. Sono configurati con le impostazioni di avvio indicate nell'elenco seguente. È possibile eseguire questi comandi per reimpostare le impostazioni di avvio:

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Configurazione Desktop remoto
6. Se sono presenti qualsiasi certificato autofirmato collegati a comunicare ascoltatore remoto protocollo RDP (Desktop), rimuoverli:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Per ulteriori informazioni sulla configurazione dei certificati per comunicare ascoltatore RDP, vedere [Configurazione dei certificati di comunicare ascoltatore in Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configurare i valori [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) per RDP servizio:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configurare la modalità di autenticazione per il servizio RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Attivare il servizio RDP aggiungendo le sottochiavi del Registro di sistema:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurare le regole di Windows Firewall
10. Consentire a WinRM con tre profili di firewall (dominio, privato e pubblico) e attivare assistenza remota di PowerShell:

    ```
    Enable-PSRemoting -force
    ```

11. Verificare che siano regole firewall del sistema operativo guest seguenti:

    - In ingresso

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - In ingresso e in uscita

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - In uscita

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Ulteriori passaggi di configurazione per Windows
12. Eseguire `winmgmt /verifyrepository` per verificare che l'archivio di Windows Management strumentazione è coerenza. Se l'archivio è danneggiato, vedere [questo post di blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Assicurarsi che le impostazioni di configurazione dei dati di avvio corrispondano a quanto segue:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Rimuovere i filtri Transport Driver Interface aggiuntivi, ad esempio un software per l'analisi pacchetti TCP.
15. Per assicurarsi che il disco è corretti e coerenti, eseguire la `CHKDSK /f` comando.
16. Disinstallare qualsiasi altro software di terze parti e driver relativi a componenti fisici o qualsiasi altra tecnologia di virtualizzazione.
17. Assicurarsi che un'applicazione di terze parti non usa porta 3389. Questa porta viene usata per il servizio RDP in Azure. È possibile utilizzare il `netstat -anob` comando per controllare le porte che usano dalle applicazioni.
18. Se il disco rigido virtuale di Windows che si desidera caricare un controller di dominio, seguire [questi passaggi aggiuntivi](https://support.microsoft.com/kb/2904015) per preparare il disco.
19. Riavviare il computer macchine Virtuali per assicurarsi che Windows sia ancora integro raggiungibili mediante la connessione RDP.
20. Reimpostare la password di amministratore locale corrente e assicurarsi che è possibile utilizzare questo account per accedere a Windows tramite la connessione RDP.  Questa autorizzazione di accesso viene controllata dall'oggetto Criteri di "Consenti accesso tramite Servizi Desktop remoto". L'oggetto è disponibile in "Computer configurazione Windows\Impostazioni protezione\Criteri Assegnazione diritti utente."


## <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
22. Installare gli aggiornamenti più recenti per Windows. Se non è possibile, assicurarsi che siano installati gli aggiornamenti seguenti:

    - [KB3137061](https://support.microsoft.com/kb/3137061) Macchine virtuali di Microsoft Azure non il ripristino di un'interruzione di rete e si verificano problemi di danneggiamento dei dati

    - [KB3115224](https://support.microsoft.com/kb/3115224) Maggiore affidabilità per macchine virtuali in esecuzione su un host Windows Server 2012 R2 o Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Aggiornamento della sicurezza per Microsoft Windows possibile elevazione indirizzo dei privilegi: 8 marzo 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Numero di eventi ID 129 connessi quando si esegue una macchina virtuale di Windows Server 2012 R2 in Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Macchine virtuali di Microsoft Azure non il ripristino di un'interruzione di rete e si verificano problemi di danneggiamento dei dati

    - [KB3114025](https://support.microsoft.com/kb/3114025) Prestazioni ridotte quando si accede lo spazio di archiviazione file Azure da Windows 8.1 o Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Aggiornamento rapido aumenta il limite di 64 KB dei buffer RIO ogni processo di Azure servizio in Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) Non è possibile accedere macchine virtuali ospitate in servizi di hosting Azure tramite una connessione VPN in Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) La connettività VPN tra locale viene persa quando Azure i tunnel VPN da sito utilizzano Windows Server 2012 R2 configurazione

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Aggiornamento della sicurezza per Microsoft Windows possibile elevazione indirizzo dei privilegi: 8 marzo 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descrizione dell'aggiornamento della sicurezza per CSRSS: 12 aprile 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Sistema si blocca durante disco in Windows<a id="step23"></a>
23. Se si desidera creare un'immagine per distribuire più computer da essa, è necessario generalizzare l'immagine eseguendo `sysprep` prima di caricare il disco rigido virtuale in Azure. Non è necessario eseguire `sysprep` per l'uso di un disco rigido virtuale specializzato. Per ulteriori informazioni su come creare un'immagine GRG, vedere gli articoli seguenti:

    - [Creare un'immagine di macchine Virtuali da una macchina virtuale Azure esistenti utilizzando il modello di distribuzione di Manager delle risorse](virtual-machines-windows-create-vm-generalized.md)
    - [Creare un'immagine di macchine Virtuali da una macchina virtuale Azure esistente usando il modem distribuzione classica](virtual-machines-windows-classic-capture-image.md)
    - [Supporto di Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurazioni aggiuntive suggerite

Le seguenti impostazioni non influiscono disco rigido virtuale durante il caricamento. Tuttavia, è consigliabile che sono state configurate.

- Installare l' [agente di Azure macchine virtuali](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Dopo aver installato l'agente, è possibile abilitare estensioni macchine Virtuali. Estensioni macchine Virtuali implementano la maggior parte delle funzionalità critiche che si desidera utilizzare con le macchine virtuali come reimpostazione della password, la configurazione RDP e molti altri.

- Registro dettagli può essere utile per la risoluzione dei problemi di arresto anomalo di Windows. Attivare la raccolta di log dettagli:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Dopo avere creata la macchina virtuale in Azure, configurare il file di dimensioni definito dal sistema paging sull'unità d:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Passaggi successivi

- [Caricare un'immagine di macchine Virtuali di Windows Azure per le distribuzioni di Manager delle risorse](virtual-machines-windows-upload-image.md)
