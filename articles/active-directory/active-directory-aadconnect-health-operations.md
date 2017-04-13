<properties
    pageTitle="Azure AD Connect operazioni di integrità."
    description="Questo articolo descrive le operazioni aggiuntive che possono essere eseguite dopo aver distribuito Azure Active Directory connettersi integrità."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect operazioni integrità

L'argomento seguente descrive le varie operazioni che è possono eseguire con Azure Active Directory connettersi integrità.

## <a name="enable-email-notifications"></a>Attivare le notifiche di posta elettronica
È possibile configurare il Azure Active Directory connettersi integrità servizio per l'invio di notifiche tramite posta elettronica quando vengono generati avvisi che indica che l'infrastruttura di identità non è integro. Questo problema si verifica quando viene generato un avviso, come e quando è contrassegnato come risolto. Seguire le istruzioni seguenti per configurare le notifiche di posta elettronica.

![Azure AD Connect dell'integrità della posta elettronica alla scoperta di notifica](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notifiche tramite posta elettronica sono disabilitate per impostazione predefinita.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Per abilitare Azure Active Directory connettersi integrità notifiche tramite posta elettronica

1. Aprire e l'avvisi per il servizio per il quale si desidera ricevere notifica tramite posta elettronica.
2. Fare clic sul pulsante "Impostazioni di notifica" della barra delle azioni.
3. Attivare l'opzione di notifica tramite posta elettronica su ON.
4. Selezionare la casella di controllo per configurare tutti gli amministratori globali per ricevere notifiche tramite posta elettronica.
5. Se si desidera ricevere notifiche tramite posta elettronica su altri indirizzi di posta elettronica, è possibile specificare le nella casella altro destinatario di posta elettronica. Per rimuovere un indirizzo di posta elettronica da questo elenco, fare clic con il pulsante destro del movimento e selezionare Elimina.
6. Per rendere effettive le modifiche fare clic su "Salva". Le modifiche abbiano effetti solo dopo aver selezionato "Salva".

## <a name="delete-a-server-or-service-instance"></a>Eliminare un'istanza di server o il servizio

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Eliminare un server dal servizio di integrità a connettersi Azure Active Directory
In alcuni casi, è consigliabile rimuovere un server di monitoraggio. Seguire le istruzioni seguenti per rimuovere un server dal servizio di integrità a connettersi Azure Active Directory.

Quando si elimina un server, tenere presente quanto segue:

- Questa azione INTERROMPE la raccolta di ulteriori dati dal server. Questo server verrà rimossi dal servizio di monitoraggio. Dopo l'azione, non sarà possibile visualizzare gli avvisi di nuovi, monitoraggio o uso dati analitica per questo server.
- Questa azione verrà non disinstallare o rimuovere l'Agente integrità dal server. Se non si è disinstallato l'Agente integrità prima di eseguire questo passaggio, potrebbe apparire gli eventi di errore sul server correlati all'agente di integrità.
- Questa azione non eliminerà dati già raccolti dal server. Verranno eliminati i dati per i criteri di conservazione dei dati di Microsoft Azure.
- Dopo aver completato questa azione, se si desidera avviare il monitoraggio di nuovo lo stesso server è necessario disinstallare e reinstallare l'agente di integrità su questo server.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Per eliminare un server dal servizio di integrità a connettersi Azure Active Directory

Azure AD Connect integrità per ADFS e Azure Active Directory la connessione (sincronizzazione):

1. Aprire e il Server da e elenco il Server, selezionare il nome del server da rimuovere.
2. Scegliere il pulsante "Elimina" sulla barra delle azioni e il Server.
3. Confermare l'operazione per eliminare il server, digitare il nome del server nella finestra di conferma.
4. Fare clic sul pulsante "Elimina".

Azure AD Connect integrità per Active Directory:

1. Aprire il dashboard di controller di dominio.
2. Selezionare il controller di dominio da rimuovere.
3. Fare clic sul pulsante "Selezionata l'opzione Elimina" della barra delle azioni.
4. Confermare l'operazione per eliminare il server.
5. Fare clic sul pulsante "Elimina".

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminare un'istanza del servizio dal servizio di integrità a connettersi Azure Active Directory

In alcuni casi, è consigliabile rimuovere un'istanza del servizio. Seguire le istruzioni seguenti per rimuovere un'istanza del servizio da Azure Active Directory connettersi integrità dei servizi.

Quando si elimina un'istanza del servizio, tenere presente quanto segue:

- L'istanza corrente del servizio verranno rimosse dal servizio di monitoraggio.
- Questa azione verrà non disinstallare o rimuovere l'Agente integrità da uno qualsiasi dei server che sono state monitorate come parte di questa istanza del servizio. Se non si è disinstallato l'Agente integrità prima di eseguire questo passaggio, potrebbe apparire gli errori nei server correlati all'agente di integrità.
- Verranno eliminati tutti i dati da questa istanza del servizio per i criteri di conservazione dei dati di Microsoft Azure.
- Dopo l'esecuzione di questa azione, se si desidera iniziare il monitoraggio del servizio, disinstallare e reinstallare l'agente di integrità nei server che verranno monitorate. Dopo aver completato questa azione, se si desidera avviare il monitoraggio di nuovo lo stesso server è necessario disinstallare e reinstallare l'agente di integrità su questo server.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Per eliminare un'istanza del servizio dal servizio di sicurezza a connettersi Azure Active Directory

1. Aprire e il servizio da e elenco il servizio selezionando l'identificatore di servizio (nome della farm) che si desidera rimuovere.
2. Scegliere il pulsante "Elimina" sulla barra delle azioni e il Server.
3. Confermare il nome del servizio, digitarlo nella casella di conferma. (ad esempio: sts.contoso.com)
4. Fare clic sul pulsante "Elimina".
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gestire l'accesso con basato sui ruoli controllo dell'accesso
### <a name="overview"></a>Panoramica
[Controllo dell'accesso basato su ruoli](role-based-access-control-configure.md) per Azure Active Directory connettersi integrità fornisce l'accesso del servizio di Azure Active Directory connettersi integrità a utenti e/o gruppi all'esterno di amministratori globali. Questa operazione viene eseguita mediante l'assegnazione di ruoli per i gruppi di utenti previsti e/o e consente di limitare gli amministratori globali all'interno della directory.

#### <a name="roles"></a>Ruoli
Azure Active Directory connettersi integrità supporta i ruoli predefiniti seguenti.

| Ruolo | Autorizzazioni |
| ----------- | ---------- |
| Proprietario | I proprietari possono ***gestire l'accesso*** (ad esempio, Assegna ruolo a un utente/gruppo), ***visualizzare tutte le informazioni*** (ad esempio visualizzare avvisi) dal portale e ***modificare le impostazioni*** (ad esempio le notifiche tramite posta elettronica) all'interno di Azure Active Directory connettersi integrità. <br>Per impostazione predefinita, gli amministratori globali di Azure Active Directory vengono assegnati questo ruolo e non può essere modificato.  |
|Per i collaboratori|  Collaboratori è possibile ***visualizzare tutte le informazioni*** (ad esempio visualizzare avvisi) dal portale e ***modificare le impostazioni*** (ad esempio le notifiche tramite posta elettronica) all'interno di Azure Active Directory connettersi integrità.|
|Utilità per la lettura| Utilità per la lettura è possibile ***visualizzare tutte le informazioni*** (ad esempio visualizzare avvisi) dal portale di all'interno di Azure Active Directory connettersi integrità.|

Tutti gli altri ruoli (ad esempio, "Amministratori accesso utente" o "DevTest esercitazioni utenti"), anche se è disponibile in esperienza portale non hanno Nessun impatto per accedere all'interno di Azure Active Directory connettersi integrità.

#### <a name="access-scope"></a>Ambito di accesso

Azure AD Connect supporta che gestisce l'accesso a due livelli:

- ***Tutte le istanze di servizio***: questa è la procedura consigliata per la maggior parte dei clienti e si controlla l'accesso per tutte le istanze di servizio (ad esempio una farm ADFS) per tutti i tipi di ruolo sono gestiti dall'Azure Active Directory connettersi integrità.

- ***Istanza del servizio***: In alcuni casi, potrebbe essere necessario separare accesso basato sui tipi di ruolo o da un'istanza del servizio. In questo caso, è possibile gestire l'accesso al livello di istanza del servizio.  

Autorizzazione se un utente finale è autorizzato ad accedere a livello Directory o istanza del servizio.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Come consentire a utenti o gruppi di accesso per la salute la connessione di Azure Active Directory
#### <a name="steps-1-select-the-appropriate-access-scope"></a>I passaggi da 1: Selezionare l'ambito di accesso appropriato
Per consentire a un utente l'accesso al livello di *tutte le istanze di servizio* Azure Active Directory connettersi integrità, aprire e il principale in Azure Active Directory connettersi integrità.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Passaggio 2: Aggiungere utenti, gruppi e assegnare i ruoli
1. Fare clic sulla parte "Utenti" nella sezione Configura.<br>
![Azure AD Connect integrità RBAC principale Blade](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selezionare "Aggiungere"
3. Selezionare il ruolo"", ad esempio "Proprietario"<br>
![Azure AD Connect integrità RBAC aggiunta utente](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Digitare il nome o l'identificatore di destinazione utente o gruppo. È possibile selezionare uno o più utenti o gruppi nello stesso momento. Fare clic su "select".
![Azure AD Connect integrità RBAC Seleziona utente](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selezionare "Ok".<br>

6. Una volta completata l'assegnazione di ruolo, gli utenti e/o gruppi verranno visualizzato nell'elenco.<br>
![Azure AD Connect integrità RBAC utente elenco](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Questa procedura consentirà nell'elenco di utenti e autorizzazioni di accesso gruppo relativi ruoli.
>[AZURE.NOTE]
- Gli amministratori globali sempre abbiano accesso completo a tutte le operazioni, ma gli account di amministratore globale non vengono visualizzati nell'elenco precedente.
- "Invitare utenti" caratteristica non è supportata all'interno di Azure Active Directory connettersi integrità.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passaggio 3: Condividere il percorso blade con gli utenti o gruppi
1. Dopo l'assegnazione di autorizzazioni, un utente può accedere Azure Active Directory connettersi integrità facendo clic su [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Una volta nella e la è possibile bloccare le diverse parti al dashboard di blade semplicemente facendo clic su "Aggiungi a dashboard"<br>
![Blade di pin Azure Active Directory connettersi integrità RBAC](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un utente con assegnato il ruolo "Lettore" non sarà possibile eseguire l'operazione "creare" per ottenere l'estensione di Azure Active Directory connettersi integrità da Azure Marketplace. L'utente ancora accessibile per e il facendo clic su collegamento riportato sopra. Per uso successivo, l'utente può aggiungere e al dashboard.

### <a name="remove-users-andor-groups"></a>Rimuovere utenti e/o gruppi
È possibile rimuovere un utente o un gruppo aggiunto alla parte di Azure Active Directory connettersi integrità in base a controllo dell'accesso ruoli destra facendo clic su e selezionare Rimuovi.<br>
![Azure AD Connect integrità RBAC Rimuovi utente](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect dello stato dell'installazione dell'agente](active-directory-aadconnect-health-agent-install.md)
* [Collegare mediante Azure Active Directory integrità con ADFS](active-directory-aadconnect-health-adfs.md)
* [Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Collegare mediante Azure Active Directory integrità con Active Directory](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integrità domande frequenti](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
