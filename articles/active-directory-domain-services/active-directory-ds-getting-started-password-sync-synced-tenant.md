<properties
    pageTitle="Servizi di dominio Active Directory Azure: Abilitare la sincronizzazione delle password | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Attivare la sincronizzazione delle password in servizi di dominio Active Directory di Azure
Nelle attività precedente, l'utente abilitato Servizi di dominio Active Directory di Azure per il tenant di Azure Active Directory. È necessario attivare la sincronizzazione delle password in servizi di dominio Active Directory Azure. Dopo aver configurata la sincronizzazione delle credenziali, gli utenti possono accedere al dominio gestito utilizzando le proprie credenziali aziendale.

Le fasi sono diversi in base a se l'organizzazione ha un annuncio di Azure basata solo su cloud tenant o è impostato per la sincronizzazione con la directory locali con Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Azure AD basata solo su cloud tenant](active-directory-ds-getting-started-password-sync.md)
- [Sincronizzato tenant di Azure Active Directory](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Passaggio 5: Abilitare la sincronizzazione delle password per i servizi di dominio AAD per un Azure sincronizzata tenant di Active Directory
Risposte sincronizzati Azure tenant di Active Directory è impostata per la sincronizzazione con directory locale dell'organizzazione con Azure AD Connect. Azure AD Connect non sincronizza NTLM e Kerberos credenziali hash di Azure Active Directory per impostazione predefinita. Per utilizzare i servizi di dominio Active Directory di Azure, è necessario configurare Azure AD Connect per sincronizzare gli hash delle credenziali necessari per l'autenticazione Kerberos e NTLM. La procedura seguente attiva la sincronizzazione dei valori delle credenziali necessarie al tenant di Azure Active Directory.


### <a name="install-or-update-azure-ad-connect"></a>Installare o aggiornare Azure AD Connect
Installare la versione più recente consigliata di Azure AD Connect in un computer collegato tramite join di dominio. Se si dispone di un'istanza del programma di installazione di Azure AD Connect esistente, è necessario aggiornare in modo da usare la versione più recente di Azure AD Connect. Per evitare errori problemi noti/che potrebbe già sono stati corretti, accertarsi di utilizzare sempre la versione più recente di Azure AD Connect.

**[Connettere download Azure Active Directory](http://www.microsoft.com/download/details.aspx?id=47594)**

Consigliati versione: **1.1.281.0** - pubblicati su 7 settembre 2016.

  > [AZURE.WARNING] È necessario installare la versione più recente consigliata di Azure AD Connect per abilitare le credenziali di password legacy (necessarie per l'autenticazione Kerberos e NTLM) per la sincronizzazione al tenant di Azure Active Directory. Questa funzionalità non è disponibile nelle versioni precedenti di Azure AD Connect o con lo strumento DirSync legacy.

Istruzioni di installazione per Azure AD Connect sono disponibili nell'articolo - [Guida introduttiva di Azure AD Connect](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Attivare la sincronizzazione di NTLM e Kerberos hash delle credenziali di Azure Active Directory
Eseguire il seguente script di PowerShell in ogni foresta di Active Directory, forzare la sincronizzazione delle password completo e abilitare hash delle credenziali di tutti i utenti locali sincronizzare al tenant di Azure Active Directory. Questo script consente i valori delle credenziali necessari per l'autenticazione NTLM/Kerberos da sincronizzare al tenant di Azure Active Directory.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

A seconda delle dimensioni della cartella (numero di utenti, gruppi e così via), la sincronizzazione di hash delle credenziali di Azure Active Directory richiede tempo. Le password saranno utilizzabili nel dominio gestito servizi di dominio Active Directory Azure subito dopo i valori delle credenziali sincronizzazione di Azure Active Directory.


<br>

## <a name="related-content"></a>Contenuti correlati

- [Attivare la sincronizzazione delle password per i servizi di dominio AAD per un Azure basata solo su cloud directory Active Directory](active-directory-ds-getting-started-password-sync.md)

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Aggiungere una macchina virtuale di Windows a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
