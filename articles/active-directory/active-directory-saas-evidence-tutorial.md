<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Evidence.com | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory ed Evidence.com."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Esercitazione: Integrazione di Azure Active Directory con Evidence.com

L'obiettivo di questa esercitazione è sufficiente visualizzare come configurare il single sign-on tra Azure Active Directory (AAD) ed Evidence.com. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:
    
* Abbonamento valido a Microsoft Azure
* Attivazione di un abbonamento a Evidence.com con single sign-On (posta elettronica earlyaccess@evidence.com se basata su SAML single sign-on non è attivata)

Al termine di questa esercitazione, gli utenti AAD alle quali sono state assegnate Evidence.com access sarà possibile accesso singolo nell'applicazione tramite il comando AAD.

## <a name="add-evidencecom-to-your-directory"></a>Aggiungere Evidence.com alla directory

In questa sezione viene descritto come aggiungere Evidence.com come un'applicazione integrata di Azure Active Directory.

**Per attivare l'integrazione di applicazione per la prova:**

1.  Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

4.  Per aprire la raccolta di applicazione, fare clic su **Aggiungi**e quindi fare clic su **Aggiungi un'applicazione dalla raccolta**.

5.  Nella casella Cerca digitare **Evidence.com**.

6.  Nel riquadro dei risultati, selezionare **Evidence.com**e quindi fare clic su **completa** per aggiungere l'applicazione.


## <a name="configuring-single-sign-on"></a>Configurare il single sign-on

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Evidence.com con il proprio account Azure Active Directory mediante la federazione in base al protocollo SAML.

**Per configurare il single sign-on, procedere come segue:**

1.  Dopo aver aggiunto Evidence.com nel portale di classica Azure, fare clic su **Configura Single Sign-On**. 
 
2.  Nella schermata successiva selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

3.  Nella schermata Configura App URL immettere l'URL in cui gli utenti verranno accedere all'URL di tenant Evidence.com (esempio: https://yourtenant.evidence.com e quindi fare clic su **Avanti**. 

4.  Fare clic sul collegamento **Scarica certificato** e salvarlo nell'unità locale. Il certificato e gli URL dei metadati (ID entità, Single Sign-on URL di accesso e accedere all'URL) verranno utilizzati per configurare Single Sign-On nel sito Evidence.com. 

5.  In una finestra del browser web separato, eseguire l'accesso per il Evidence.com come amministratore del tenant e passare alla scheda **amministratore**
      
6.  Fare clic su **Agenzia Single Sign-On**
 
7.  Selezionare **SAML in base a Single Sign**
 
8.  Copiare l' **URL emittente** **Single Sign-On** e **Disconnetti singola** valori riportati nel portale di classica Azure e ai campi corrispondenti Evidence.com.

9.  Aprire il certificato scaricata nel passaggio 4 con un editor di testo come Notepad.exe e copiare e incollare il contenuto nella casella **Certificato di sicurezza** . 

10. Salvare la configurazione in Evidence.com.
 
11. Nel portale di Azure classico selezionare **Conferma configurati il single sign-on come descritto in precedenza**. Questo controllo consente il certificato corrente iniziare a lavorare per questa casella di controllo dell'applicazione.
 
12. Nella pagina di conferma Single sign-on, fare clic su **completa**.  


## <a name="creating-an-evidencecom-test-user"></a>Creazione di un utente di test Evidence.com

Per gli utenti di Azure Active Directory per poter accedere, è necessario essere effettuato il provisioning per l'accesso all'interno dell'applicazione Evidence.com. In questa sezione viene descritto come creare gli account utente di Azure Active Directory all'interno di Evidence.com

**Effettuare il provisioning di un account utente in Evidence.com:**

1.  In una finestra del browser accedere sito della società Evidence.com come amministratore.

2.  Passare alla scheda **amministratore** .

3.  Fare clic su **Aggiungi utente**.

4.  Fare clic sul pulsante **Aggiungi** .

5.  L' **Indirizzo di posta elettronica** dell'utente aggiunto deve corrispondere il nome utente degli utenti in Azure Active Directory che si desidera concedere l'accesso. Se l'indirizzo di posta elettronica e il nome utente non lo stesso valore all'interno dell'organizzazione, è possibile utilizzare il **Evidence.com > attributi > Single Sign-On** sezione del portale di classica Azure modificare nameidenitifer inviate a Evidence.com per l'indirizzo di posta elettronica.


## <a name="assigning-users-to-evidencecom"></a>Assegnazione di utenti a Evidence.com

Per gli utenti AAD provisioning in grado di vedere Evidence.com nel pannello loro l'accesso, è necessario assegnare l'accesso all'interno del portale classico Azure.

**Per assegnare gli utenti a Evidence.com:**

1.  Nella pagina Guida introduttiva relativa Evidence.com nel portale di classica Azure, fare clic su **assegna agli utenti di Evidence.com**.
 
2.  Nel menu **Visualizza** selezionare se si desidera assegnare un utente o un gruppo Evidence.com e fare clic sul pulsante di segno di spunta.
 
3.  Nell'elenco **utenti** selezionare gli utenti al gruppo a cui si desidera assegnare Evidence.com.
 
4.  Nel piè di pagina, fare clic sul pulsante **Assegna** .

