<properties
    pageTitle="Le notifiche di Azure Active Directory identità protezione | Microsoft Azure"
    description="Informazioni su come notifiche supportano le indagini."
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory identità protezione 


Protezione di Azure Active Directory identità invia due tipi di messaggi di posta elettronica la notifica automatizzata per semplificare la gestione dei rischi utente ed eventi di rischio:

- Utente sia stato compromesso avviso tramite posta elettronica

- Email riepilogo settimanale

## <a name="user-compromised-alert-email"></a>Utente sia stato compromesso avviso tramite posta elettronica

Quando la protezione dell'identità di Azure Active Directory identifica un account come compromessa, viene generato un avviso di posta elettronica compromesso utente. Messaggio di posta elettronica include un collegamento a utenti contrassegnati per il report di rischio in dashboard di protezione dell'identità. È consigliabile esaminare immediatamente le notifiche di compromesso.


## <a name="weekly-digest-email"></a>Email riepilogo settimanale

Messaggio di posta elettronica classificata settimanale contiene un riepilogo dei nuovi eventi di rischio.<br>
Include:

- Utenti dei rischi
- Attività sospette
- Vulnerabilità rilevate
- Collegamenti ai report correlati in protezione dell'identità


<br>
![Monitoraggio e aggiornamento](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

È possibile cambiare l'invio di un messaggio di posta elettronica classificata settimanale.
<br><br>
![Rischi per gli utenti](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **Impostazioni**e il **protezione dell'identità di Azure Active Directory** .
<br><br>
![Criteri di rischio utente](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. Nella sezione **Generale** fare clic su **notifiche**.
<br><br>
![Criteri di rischio utente](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Vedere anche

- [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md) 

