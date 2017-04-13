<properties
   pageTitle="Come configurare gli avvisi di sicurezza | Microsoft Azure"
   description="Informazioni su come configurare gli avvisi di sicurezza per l'estensione di Azure privilegi di gestione delle identità."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Come configurare gli avvisi di sicurezza in Azure Active Directory privilegi di gestione delle identità

## <a name="security-alerts"></a>Avvisi di sicurezza
Azure privilegi identità PIM (Management) genera avvisi quando attività sospette o non sicuro è nel proprio ambiente. Quando viene generato un avviso, questo verrà visualizzato nel dashboard di PIM. Selezionare l'avviso per visualizzare un report in cui sono elencati gli utenti o ruoli che ha attivato l'avviso.

![PIM dashboard gli avvisi di sicurezza - schermata][1]



| Invia avviso | Trigger | Suggerimento |
| ----- | ------- | -------------- |
| **Ruoli assegnati all'esterno di PIM** | Un amministratore in modo permanente è stato assegnato a un ruolo, all'esterno di interfaccia PIM. | Esaminare la nuova assegnazione di ruolo. Poiché altri servizi è possono assegnare solo gli amministratori permanenti, modificarla impostando un'assegnazione idoneo se necessario. |
| **Ruoli attivati troppo frequentemente** | Si sono verificati troppi riattivazione del ruolo stesso tempestivamente le impostazioni. | Contattare l'utente per vedere perché è stato attivato il ruolo quante volte. Forse il limite di tempo è troppo breve per essi per completare le attività oppure forse che usa script di attivare automaticamente un ruolo. |
| **Ruoli non richiedono l'autenticazione a più fattori per l'attivazione** | Sono disponibili ruoli senza MFA attivato nelle impostazioni. | È necessario MFA per i ruoli privilegi più elevati, ma consiglia abilitare MFA all'attivazione di tutti i ruoli. |
| **Gli amministratori non si usa il proprio ruolo privilegi** | Sono presenti amministratori idoneità dell'utente che non hanno attivato i ruoli di recente. | Avviare una revisione di access per determinare gli utenti che non più necessarie access. |
| **Sono presenti troppi amministratori globali** | Sono presenti più globali amministratori quella consigliata. | Se si dispone di un numero elevato di amministratori globali, è probabile che gli utenti vengono selezionate più autorizzazioni di hanno bisogno. Spostare gli utenti ai ruoli privilegi inferiori oppure per effettuare alcuni di essi idoneo per il ruolo anziché assegnati in modo permanente. |

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

È possibile personalizzare alcuni degli avvisi di sicurezza in PIM a gestire l'ambiente e obiettivi per la sicurezza. Seguire questa procedura per raggiungere e l'impostazioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il riquadro di **Azure Active Directory privilegi di gestione delle identità** nel dashboard.
2. Selezionare **gestite ruoli privilegi** > **Impostazioni** > **impostazioni degli avvisi**.

    ![Passare a impostazioni degli avvisi di sicurezza][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Avviso "Ruoli attivati troppo frequentemente"

Questo avviso attiva se un utente attiva lo stesso ruolo privilegiato più volte all'interno di un periodo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni.

- **Periodo di tempo di attivazione il rinnovo**: specificare in giorni, ore, minuti e il secondo il periodo di tempo che si desidera utilizzare per tenere traccia di rinnovo sospetto.

- **Numero di rinnovo di attivazione**: specificare il numero di attivazioni da 2 a 100, che è prendere in considerazione degni di avviso dell'intervallo di tempo scelto. È possibile modificare questa impostazione, spostare il dispositivo di scorrimento o digitare un numero nella casella di testo.


### <a name="there-are-too-many-global-administrators-alert"></a>Avviso "Sono presenti troppi amministratori globali"

PIM attiva l'avviso due diversi criteri vengono soddisfatti, se è possibile configurare entrambi gli elementi. Prima di tutto, è necessario raggiungere una determinata soglia di amministratori globali. In secondo luogo, percentuale del totale delle assegnazioni di ruolo deve essere amministratori globali. Se vengono soddisfatte solo uno di questi unità di misura, non viene visualizzato l'avviso.  

- **Numero minimo di amministratori globali**: specificare il numero di amministratori globali, da 2 a 100, prendere in considerazione un importo non sicure.

- **Percentuale di amministratori globali**: specificare la percentuale di amministratori che hanno gli amministratori globali, compreso tra 0% al 100%, che non sicuro nel proprio ambiente.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"Gli amministratori non si usa il proprio ruolo privilegi" avviso

Questo avviso attivato se un utente passa a un determinato periodo di tempo senza l'attivazione di un ruolo.

- **Numero di giorni che vanno**: specificare il numero di giorni compreso tra 0 e 100, che un utente può passare senza l'attivazione di un ruolo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
