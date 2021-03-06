<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con portale personale Mimecast | Microsoft Azure" 
    description="Informazioni su come utilizzare portale personale Mimecast con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal portale
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Mimecast portale personale.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un portale personale Mimecast single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a portale personale Mimecast sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di portale personale Mimecast (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per portale personale Mimecast
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scenario:")
##<a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Attivare l'integrazione delle applicazioni per portale personale Mimecast
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per portale personale Mimecast.

###<a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Mimecast portale personale, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Mimecast portale personale**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Mimecast portale personale**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Portale Mimecast personale] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Portale Mimecast personale")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione al portale personale Mimecast con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Mimecast portale personale** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al portale personale Mimecast** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Mimecast personali portale su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione portale personale Mimecast (ad esempio: "https://webmail-uk.mimecast.com" o "https://webmail-us.mimecast.com"), quindi scegliere **Avanti**.

    >[AZURE.NOTE] Il segno sull'URL è specifica l'area geografica.

    ![Configurare URL di App] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on Mimecast portale personale** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere il portale personale Mimecast come amministratore.

6.  Passare a **servizi \> applicazione**.

    ![Applicazioni] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applicazioni")

7.  Fare clic su **profili di autenticazione**.

    ![Profili di autenticazione] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Profili di autenticazione")

8.  Fare clic su **nuovo profilo di autenticazione**.

    ![Nuova autenticazione Profil] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Nuova autenticazione Profil")

9.  Nella sezione del **Profilo di autenticazione** , procedere come segue:

    ![Autenticazione Profil] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Autenticazione Profil")

    1.  Nella casella di testo **Descrizione** digitare un nome per la configurazione.
    2.  Selezionare **implementare SAML autenticazione per il portale di Mimecast personale**.
    3.  Come **Provider**, selezionare **Azure Active Directory**.
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on portale personale Mimecast** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **URL emittente** .
    5.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on portale personale Mimecast** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    6.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on portale personale Mimecast** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL disconnessione** .  

        >[AZURE.NOTE] Il valore di URL di accesso e il valore di disconnessione URL siano relativi a-nel portale personale Mimecast lo stesso.

    7.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    8.  Aprire il certificato codificato base 64 nel blocco note, rimuovere la prima riga ("*--*") e l'ultima riga ("*--*"), copiare negli Appunti il resto del contenuto di esso e quindi incollarlo alla casella di testo **Certificato di Provider di identità (metadati)** .
    9.  Selezionare **Consenti Single Sign in**.
    10. Fare clic su **Salva**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory accedere al portale personale Mimecast, è necessario essere effettuato il provisioning in Mimecast portale personale.  
Nel caso dei Mimecast portale personale, il provisioning è un'attività manuale.
  
È necessario registrare un dominio per poter creare gli utenti.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al **Portale personale Mimecast** come amministratore.

2.  Passare a **directory \> interno**.

    ![Directory] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directory")

3.  Fare clic su **Registra nuovo dominio**.

    ![Registrare di nuovo dominio] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Registrare di nuovo dominio")

4.  Dopo aver creato il nuovo dominio, fare clic su **Nuovo indirizzo**.

    ![Nuovo indirizzo] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Nuovo indirizzo")

5.  Nella finestra di dialogo Nuovo indirizzo, procedere come segue:

    ![Salva] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Salva")

    1.  Digitare gli attributi di **Indirizzo di posta elettronica**, **Nome globale**, **Password** e **Conferma Password** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE]È possibile usare altri strumenti di creazione di account utente portale personale Mimecast o API fornite dal portale personale Mimecast agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>Per assegnare gli utenti al portale personale Mimecast, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Mimecast portale personale **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).