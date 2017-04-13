<properties
    pageTitle="Algoritmo della firma cambia per protezione festa di risposta di Office 365 | Microsoft Azure"
    description="Questa pagina vengono fornite le linee per la modifica algoritmo SHA per protezione federazione con Office 365"
    keywords="SHA1, SHA256, Office 365, federazione, aadconnect, adfs, ADFS, Cambia sha, protezione federazione, basarsi protezione di terze parti"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Modificare algoritmo di firma per Office 365 la risposta di protezione di terze parti

## <a name="overview"></a>Panoramica

Azure Active Directory Federation Services (ADFS) firma i token a Microsoft Azure Active Directory per garantire che non può essere modificati con. Questa firma può essere basata sulle SHA1 o SHA256. Azure Active Directory supporta ora i token firmati con un algoritmo SHA256 ed è consigliabile impostare l'algoritmo di firma di token SHA256 per il massimo livello di sicurezza. In questo articolo vengono illustrati i passaggi necessari per impostare l'algoritmo di firma di token SHA256 più sicura livello.

## <a name="change-the-token-signing-algorithm"></a>Modificare l'algoritmo di firma di token

Dopo aver impostato algoritmo di firma con uno dei due processi riportata di seguito, ADFS firma di token per Office 365 basarsi protezione di terze parti con SHA256. Non è necessario apportare le modifiche di configurazione aggiuntivi e la modifica non influisce sulla possibilità di accedere a Office 365 o altre applicazioni di Azure Active Directory.

### <a name="ad-fs-management-console"></a>Console di gestione di Active Directory ADFS

1. Aprire la console di gestione di ADFS nel server ADFS primario.
2. Espandere il nodo ADFS e fare clic su **Attendibilità componente**.
3. Pulsante destro del mouse la protezione di terze parti componente di Office 365/Azure e scegliere **proprietà**.
4. Selezionare la scheda **Avanzate** e quindi algoritmo sicura SHA256.
5. Fare clic su **OK**.

![Algoritmo di firma SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlet di PowerShell di ADFS Active Directory

1. In qualsiasi server ADFS aprire PowerShell in privilegi di amministratore.
2. Impostare l'algoritmo protetta utilizzando il cmdlet **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leggere inoltre

* [Ripristino di protezione di Office 365 con Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
