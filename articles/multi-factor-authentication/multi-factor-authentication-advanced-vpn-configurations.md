<properties
    pageTitle="Scenari avanzati con Azure autenticazione a più fattori e 3 ° VPN festa"
    description="Questa pagina vengono fornite informazioni sulla configurazione della procedura di configurazione per MFA Azure con 3 ° prodcuts di terze parti."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Scenari avanzati con Azure autenticazione a più fattori e 3 ° VPN festa
Autenticazione a più fattori Azure utilizzabili per la connessione diretta con una serie di 3 ° soluzioni VPN di terze parti.  Sono inclusi accessorio Cisco® ASA VPN, accessorio Citrix NetScaler SSL VPN e accessorio Juniper reti sicura Access/Pulse sicura connessione sicura SSL VPN.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Accessorio ASA VPN Cisco e l'autenticazione a più fattori di Azure
Autenticazione a più fattori Azure integrazione completa con dispositivo Cisco® ASA VPN per rafforzare la sicurezza per gli account di accesso Cisco AnyConnect® VPN e accesso al portale.  Questa operazione può essere eseguita tramite protocollo LDAP o RADIUS.  Selezionare una delle operazioni seguenti per scaricare le guide dettagliate dettagliate per la configurazione.

Guida alla configurazione  | Descrizione
------------- | ------------- |
[Cisco ASA con la configurazione di MFA Anyconnect VPN e Azure per LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrazione dispositivo VPN Cisco ASA con MFA Azure mediante LDAP|
[Cisco ASA con la configurazione di MFA Anyconnect VPN e Azure per raggio](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrazione dispositivo VPN Cisco ASA con Azure MFA utilizzando raggio

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Autenticazione a più fattori Citrix NetScaler SSL VPN e Azure
Autenticazione a più fattori Azure integrazione completa con dispositivo Citrix NetScaler SSL VPN per rafforzare la sicurezza per gli account di accesso di Citrix NetScaler SSL VPN e accesso al portale.  Questa operazione può essere eseguita tramite protocollo LDAP o RADIUS.  Selezionare una delle operazioni seguenti per scaricare le guide dettagliate dettagliate per la configurazione.

Guida alla configurazione  | Descrizione
------------- | ------------- |
[Configurazione di MFA Citrix NetScaler SSL VPN e Azure per LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrazione la rete VPN SSL NetScaler Citrix con accessorio MFA Azure tramite LDAP|
[Configurazione di MFA Citrix NetScaler SSL VPN e Azure per raggio](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrazione dispositivo Citrix NetScaler SSL VPN con Azure MFA utilizzando raggio

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Accessorio Juniper/Pulse sicura SSL VPN e l'autenticazione a più fattori di Azure
Autenticazione a più fattori Azure integrazione completa con dispositivo Juniper/Pulse sicura SSL VPN per rafforzare la sicurezza per gli account di accesso Juniper/Pulse sicura SSL VPN e accesso al portale.  Questa operazione può essere eseguita tramite protocollo LDAP o RADIUS.  Selezionare una delle operazioni seguenti per scaricare le guide dettagliate dettagliate per la configurazione.

Guida alla configurazione  | Descrizione
------------- | ------------- |
[Configurazione di MFA Juniper/Pulse sicuro SSL VPN e Azure per LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Integrazione la rete VPN SSL sicura Juniper/Pulse con accessorio MFA Azure tramite LDAP|
[Configurazione di MFA Juniper/Pulse sicuro SSL VPN e Azure per raggio](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrazione dispositivo VPN SSL sicura Juniper/Pulse con Azure MFA utilizzando raggio
