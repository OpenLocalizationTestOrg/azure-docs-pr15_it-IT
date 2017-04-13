
<properties
    pageTitle="Azure autenticatore per Android | Microsoft Azure"
    description="Effettuare l'accesso per accedere alle risorse lavoro, è possibile utilizzare Microsoft Azure autenticatore app. L'app di Azure autenticatore notifica di una richiesta di verifica a due fattori in sospeso tramite un avviso per un dispositivo mobile."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="azure-authenticator-for-android"></a>Azure autenticatore per Android

L'amministratore IT può hanno consigliati è possibile utilizzare Microsoft Azure autenticatore effettuare l'accesso per accedere alle risorse di lavoro. Questa applicazione fornisce queste due opzioni:

* Autenticazione a più fattori consente di proteggere gli account aziendale o dell'istituto di istruzione con la verifica in due passaggi. L'accesso in utilizzo di un elemento (ad esempio, la password) che ben conosci e proteggere l'account ulteriormente con un avere (una chiave di protezione da questa app). L'app di Azure autenticatore notifica di una richiesta di verifica a due fattori in sospeso tramite un avviso per un dispositivo mobile. È necessario semplicemente visualizzare la richiesta dell'app e toccare verificare o Annulla. In alternativa, potrebbe essere richiesto di immettere il passcode visualizzato nell'app.

* Account di lavoro consente di trasformare il telefono o tablet Android in un dispositivo attendibile e offrire Single Sign-On (SSO) per applicazioni aziendali. L'amministratore IT potrebbe essere necessario aggiungere un account di lavoro per accedere alle risorse della società. Single Sign-on consente di eseguire l'accesso una sola volta e automaticamente Calc accedere a tutte le applicazioni che la propria azienda ha reso disponibile per l'utente.

## <a name="installing-the-azure-authenticator-app"></a>Installazione dell'app autenticatore Azure

È possibile installare l'app di Azure autenticatore dal Google Play Store.
Le istruzioni per aggiungere l'account di lavoro da Samsung Android vs Samsung Android dispositivo sono leggermente diverse. Le istruzioni per entrambi sono elencate di seguito.

<a name="adding-the-work-account-from-samsung-android-device"></a>Aggiunta dell'account di lavoro da dispositivo Samsung Android
----------------------------------------------------------------------------------------------------------------
###<a name="adding-the-work-account-through-the-app-home-screen"></a>Aggiunta dell'account di lavoro tramite schermata iniziale dell'app

Le istruzioni seguenti sono applicabili a Samsung GS3 e versioni successive telefoni o la nota 2 e sopra Tablet.

1. Nella schermata iniziale dell'app, accettare il contratto di licenza utente finale (contratto di licenza).
2. Nella schermata di attivazione di Account, fare clic sul menu di scelta rapida a destra e selezionare **account aziendale**.
3. Nella schermata Aggiungi Account selezionare** l'Account aziendale**.
4. Nella schermata di amministrazione di dispositivo attiva, fare clic su **Attiva**.
5. Nella schermata informativa sulla privacy selezionare la casella di controllo e fare clic su **Conferma**.
6. Nella schermata di partecipare a luogo di lavoro, immettere l'ID utente fornito dall'organizzazione e fare clic su **Partecipa**.
7. Per accedere all'app autenticatore Azure, immettere dell'organizzazione un * * * account e la password e fare clic su **Accedi**.
8. La schermata successiva vengono visualizzate le informazioni di autenticazione a più fattori (MFA) è per maggiore sicurezza ed è facoltativa. Questa schermata verrà visualizzato se il lavoro o dell'istituto di istruzione richiede l'autenticazione secondo fattore per la creazione di account di lavoro. Fornisce istruzioni per verificare ulteriormente l'account.
9. Schermata di partecipare a luogo di lavoro viene visualizzato il messaggio, "**partecipare luogo di lavoro**". L'app autenticatore Azure sta tentando di partecipare a un dispositivo alla rete aziendale.
10. Verrà visualizzato il messaggio il join luogo di lavoro nella schermata successiva.

>[AZURE.NOTE]
> È consentito un account di lavoro singola nel dispositivo.

### <a name="adding-the-work-account-from-the-settings-menu"></a>Aggiunta dell'account di lavoro dal menu impostazioni
Dopo aver installato l'app autenticatore Azure, è anche possibile creare un account di lavoro da Android Account Manager.

1. Dal menu impostazioni, passare ad **account** e fare clic su **Aggiungi Account**.
2. Seguire i passaggi da 3 a 10 della procedura, aggiungere l'account di lavoro tramite schermata iniziale dell'app, per aggiungere un account di lavoro.

<a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Aggiunta dell'account di lavoro da un dispositivo Android non Samsung
------------------------------------------------------------------------------------------------------------------
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Aggiunta dell'account di lavoro tramite schermata iniziale dell'app

1. Nella schermata iniziale dell'app, accettare il contratto di licenza utente finale (contratto di licenza).
2. Nella schermata di attivazione di Account, fare clic sul menu di scelta rapida a destra e selezionare **account aziendale**.
3. Nella schermata account fare clic su **Aggiungi Account**.
4. Se viene visualizzata la schermata di account, fare clic su **Aggiungi account**. Se un account di lavoro è già stato creato in precedenza, si verrà visualizzata una schermata di sincronizzazione che mostra le esistenti account aziendale. È possibile mantenere l'account di lavoro, è sufficiente toccare la freccia indietro alla schermata iniziale. In alternativa, è possibile selezionare l'account da rimuovere e ricreare una schermata nel luogo di lavoro partecipare di nuovo lavoro account, immettere l'ID utente fornito dall'organizzazione e fare clic su partecipa.
5. Per accedere all'app autenticatore Azure, immettere l'account dell'organizzazione e la password e fare clic su **Accedi**.
7. La schermata successiva vengono visualizzate le informazioni di autenticazione a più fattori (MFA) è per maggiore sicurezza ed è facoltativa. Questa schermata verrà visualizzato se il lavoro o dell'istituto di istruzione richiede l'autenticazione secondo fattore per la creazione di account di lavoro. Fornisce istruzioni per verificare ulteriormente l'account.
8. Nella schermata successiva, fare clic su **OK** . Non modificare il nome del certificato.
il messaggio, "Partecipare a luogo di lavoro". L'app autenticatore Azure sta tentando di partecipare a un dispositivo alla rete aziendale.
Verrà visualizzato il messaggio il join luogo di lavoro nella schermata successiva.

>[AZURE.NOTE]
> È consentito un account di lavoro singola nel dispositivo.

Dopo aver installato l'app autenticatore Azure, è anche possibile creare un account di lavoro da Android Account Manager.

1. Dal menu **Impostazioni** , passare ad account e fare clic su **Aggiungi Account**.
2. Seguire i passaggi 2 e 7 della procedura, aggiungere l'account di lavoro tramite l'app privati schermo * *, per aggiungere un account di lavoro.

### <a name="how-to-find-out-which-version-is-installed"></a>Come individuare la versione installata

1. È possibile scoprire quale versione dell'app autenticatore Azure e sono installate versioni servizio associato nel dispositivo.
2. Dal menu a comparsa, fare clic **su**.
3. In cui sono visualizzati i servizi di app e le versioni installate nel dispositivo.
 
### <a name="sending-log-files-to-report-issues"></a>Invio di file di log per segnalare problemi

1. Seguire le indicazioni sul supporto Microsoft per segnalare un problema con l'app autenticatore Azure, ottenere un numero incidente e inviare i file di log in base al numero di operazioni non consentito assegnato come indicato di seguito:
2. Dal menu a comparsa, fare clic su **registrazione**.
3. Se si dispone di un incidente aperto con il supporto Microsoft, prendere nota del numero ai problemi (sarà necessario per una fase successiva). Se si desidera ricevere assistenza non è già stato creato un intervento del supporto, seguire le indicazioni al [Supporto tecnico clienti Microsoft](https://support.microsoft.com/en-us/contactus) per aprire un nuovo evento imprevisto.
4. Nella schermata di registrazione, fare clic su **Invia adesso**.
5. Selezionare il provider di posta elettronica che si desidera utilizzare.
7. Se si dispone già di un incidente aperto supporto Microsoft, contattare il supporto tecnico del problema per scoprire come inviare dati del registro e che vengano associati il problema. Supporto tecnico poter fornire informazioni per l'indirizzo di posta elettronica o riga oggetto. Se non si dispone già di un intervento del supporto, seguire le indicazioni al supporto Microsoft per aprire un nuovo evento imprevisto.
9. Modificare la riga **a** e riga **dell'oggetto** con le informazioni ricevute dal supporto Microsoft.
10. App Azure autenticatore allegherà il file di log al messaggio di posta elettronica che si intende inviare. Descrivere il problema, aggiornare l'elenco dei destinatari (facoltativo) e inviare il messaggio di posta elettronica.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Eliminare l'account di lavoro e lasciare il luogo di lavoro

È possibile rimuovere l'account di lavoro creato in qualsiasi momento come indicato di seguito:

**Per eliminare l'account di lavoro dal menu impostazioni**

1. Gestione di account, selezionare **account aziendale**.
2. Nella schermata Account di lavoro in **Impostazioni generali**, selezionare **impostazioni dell'Account, lasciare la rete aziendale**.
3. Selezionare **abbandona** nella schermata di **Partecipare a luogo di lavoro** .
4. Quando viene visualizzato il messaggio "Sono sicuro di voler lasciare luogo di lavoro", fare clic su **OK** .
5. In questo modo che l'account di lavoro ha eliminato dal luogo di lavoro.

>[AZURE.NOTE]
>È consigliabile non utilizzare l'opzione Rimuovi per eliminare un account di lavoro come questa opzione potrebbe non funzionare in alcune versioni precedenti di Android.

##<a name="uninstalling-the-app"></a>La disinstallazione dell'app

In un dispositivo Samsung Android privilegi di amministratore per dispositivo deve essere rimosso come indicato di seguito prima di disinstallare il 
1. **Impostazioni di** **sistema**, selezionare **protezione**.
2. All'interno di D**periferica amministrazione**, fare clic su **amministratori dispositivo**. Verificare che la casella di controllo accanto a **Azure autenticatore** sia deselezionata.

##<a name="troubleshooting"></a>Risoluzione dei problemi

Se viene visualizzato l' **Errore Keystore**, potrebbe essere non si dispone del blocco dello schermo di set di configurazione con un PIN. Per risolvere questo problema, disinstallare l'app autenticatore Azure, configurare un PIN per la schermata di blocco e reinstallare l'app.
