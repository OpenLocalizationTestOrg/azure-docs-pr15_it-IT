<properties
    pageTitle="Caratteristiche del servizio di Azure AD Connect sincronizzazione e configurazione | Microsoft Azure"
    description="Descrive le caratteristiche di lato del servizio di sincronizzazione di Azure AD Connect."
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Caratteristiche di Azure AD Connect sincronizzazione del servizio

La funzionalità di sincronizzazione di Azure AD Connect include due componenti:

- Il componente locale denominato **sincronizzazione di Azure AD Connect**, detto anche **motore di sincronizzazione**.
- Il servizio che si trovano in Azure Active Directory noto anche come **servizio di sincronizzazione di Azure AD Connect**

Questo argomento viene illustrato come utilizzare le seguenti funzionalità del **servizio di sincronizzazione di Azure AD Connect** e come è possibile configurare mediante Windows PowerShell.

Queste impostazioni vengono configurate per il [Modulo Azure Active Directory per Windows PowerShell](http://aka.ms/aadposh). Scaricare e installare separatamente da Azure AD Connect. I cmdlet descritti in questo argomento sono stati introdotti [2016 marzo rilasciare (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se non si dispone i cmdlet descritti in questo argomento o non producono lo stesso risultato, verificare che si esegue la versione più recente.

Per visualizzare la configurazione nella directory di Azure Active Directory, eseguire `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures risultato](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Molte di queste impostazioni possono essere modificate da Azure AD Connect.

Per configurare le impostazioni seguenti `Set-MsolDirSyncFeature`:

DirSyncFeature | Commento
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Consente di un attributo in quarantena quando è un duplicato di un altro oggetto anziché l'intero oggetto non funziona durante l'esportazione.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Consente agli oggetti per il join userPrincipalName oltre indirizzo SMTP principale.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Consente al motore di sincronizzazione aggiornare l'attributo userPrincipalName per gestite/concesso in licenza utenti (non federato).

Dopo aver attivato una caratteristica, non può essere disabilitata nuovamente.

>[AZURE.NOTE] Da 24 agosto 2016 *l'adattabilità attributo duplicato* è attivata per impostazione predefinita per nuovi Azure directory Active Directory. Questa caratteristica verrà inoltre distribuita e abilitata nella directory create prima di tale data. Quando la directory consiste nell'eseguire questa funzione attivata, si riceverà una notifica tramite posta elettronica.

Le seguenti impostazioni sono configurate per Azure AD Connect e non possono essere modificate da `Set-MsolDirSyncFeature`:

DirSyncFeature | Commento
--- | ---
DeviceWriteback | [Azure AD Connect: Abilitazione writeback di dispositivo](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Sincronizzazione di Azure AD Connect: estensioni Directory](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementare la sincronizzazione delle password con sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Anteprima: Writeback di gruppo](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Non sono attualmente supportati.

## <a name="duplicate-attribute-resiliency"></a>Adattabilità attributo duplicato
Invece di mancata provisioning gli oggetti con UPN duplicati / proxyAddresses, l'attributo duplicato è "messo in quarantena" e viene assegnato un valore temporaneo. Quando viene risolto il conflitto, l'UPN temporaneo viene modificato automaticamente il valore corretto. Questo problema può essere abilitato per UPN e proxyAddress separatamente. Per ulteriori informazioni, vedere [la sincronizzazione delle identità e adattabilità attributo duplicato](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Confronta contorni UserPrincipalName
Quando è attivata, sfumato Confronta è abilitato per UPN oltre all' [indirizzo SMTP principale](https://support.microsoft.com/kb/2641663), è sempre abilitata. Corrispondenza sfumato viene utilizzato in modo che corrispondano agli utenti di cloud in Azure Active Directory con utenti locali.

Se è necessario corrispondenza locale account Active Directory con un account esistente creato nel cloud e non si usa Exchange Online, quindi questa funzionalità è utile. In questo scenario, in genere non è necessario impostare l'attributo SMTP nel cloud.

Questa caratteristica è in per impostazione predefinita per nuovo directory di Azure Active Directory. È possibile vedere se questa funzionalità è abilitata per l'utente eseguendo:  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se questa caratteristica non è abilitata per la directory di Azure Active Directory, è possibile abilitarla eseguendo:  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizzare gli aggiornamenti userPrincipalName
Storicamente, gli aggiornamenti per l'attributo UserPrincipalName utilizzo del servizio di sincronizzazione da locale è stato bloccato, a meno che entrambe le condizioni sono vere:

- L'utente è gestita (non federato).
- L'utente non è stata assegnata una licenza.

Per ulteriori informazioni, vedere [i nomi utente in Office 365, Azure o Intune non corrispondono l'UPN locale o l'ID di accesso alternativo](https://support.microsoft.com/kb/2523192).

Questa funzionalità consente al motore di sincronizzazione di aggiornare l'attributo userPrincipalName quando è modificato in locale e si utilizza la sincronizzazione delle password. Se si utilizza la federazione, questa caratteristica non è supportata.

Questa caratteristica è in per impostazione predefinita per nuovo directory di Azure Active Directory. È possibile vedere se questa funzionalità è abilitata per l'utente eseguendo:  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se questa caratteristica non è abilitata per la directory di Azure Active Directory, è possibile abilitarla eseguendo:  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Dopo aver attivato questa caratteristica, valori userPrincipalName esistenti rimangono come-è. Al prossimo cambio l'attributo di userPrincipalName in locale, sincronizzazione delta normale sugli utenti aggiornerà l'UPN.  

## <a name="see-also"></a>Vedere anche

- [Sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
