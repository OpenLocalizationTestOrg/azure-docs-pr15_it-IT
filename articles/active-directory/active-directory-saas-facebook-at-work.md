<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Facebook in ufficio | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Facebook in ufficio."
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
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Esercitazione: Integrazione di Azure Active Directory con Facebook in ufficio

L'obiettivo di questa esercitazione è sufficiente visualizzare sull'integrazione Facebook durante il lavoro con Azure Active Directory (Azure Active Directory).

Integrazione Facebook durante il lavoro con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso a Facebook in ufficio 
- È possibile eseguire automaticamente provisioning account per gli utenti che è stato concesso l'accesso a Facebook in ufficio
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Facebook in ufficio (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale 

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con CS stelle, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Attivazione di Facebook al lavoro con single sign-on

Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Aggiunta di Facebook al lavoro dalla raccolta
Per configurare l'integrazione di Facebook in ufficio in Azure Active Directory, è necessario aggiungere Facebook in ufficio dalla raccolta per l'elenco di App SaaS gestito.

**Per aggiungere Facebook in ufficio dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Facebook in ufficio**.

7. Nel riquadro dei risultati selezionare **Facebook al lavoro**e quindi fare clic su **completa** per aggiungere l'applicazione.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Facebook durante il lavoro con il proprio account Azure Active Directory mediante la federazione in base al protocollo SAML.

**Per configurare Azure Active Directory single sign-on con Facebook in ufficio, procedere come segue:**

1.  Dopo aver aggiunto Facebook in ufficio nel portale di classica Azure, fare clic su **Configura Single Sign-On**.

2.  Nella schermata **Configura App URL** immettere l'URL in cui gli utenti verranno accedere il Facebook all'applicazione di lavoro. Si tratta del Facebook lavoro tenant URL (esempio: https://example.facebook.com/). Al termine, fare clic su **Avanti**.

3.  In una finestra del browser web diverso, accedere i Facebook nel sito della società di lavoro come amministratore.

4. Seguire le istruzioni per configurare Facebook durante il lavoro da utilizzare Azure Active Directory come provider di identità all'indirizzo seguente: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Una volta completato, tornare alla finestre del browser con il portale classico Azure, selezionare la casella di controllo per confermare di che aver completato la procedura, quindi fare clic su **Avanti** e **completamento**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Automaticamente il provisioning agli utenti di Facebook al lavoro

Azure Active Directory supporta la possibilità di Sincronizza automaticamente i dettagli del conto di utenti assegnati a Facebook in ufficio. La sincronizzazione automatica consente di Facebook al lavoro per ottenere i dati che necessari per autorizzare gli utenti per l'accesso, per le loro tentativo di accesso di per la prima volta. Anche deselezionare disposizioni agli utenti di Facebook al lavoro durante l'accesso è stato revocato in Azure Active Directory.

Preparazione automatica può essere configurata facendo clic su **Configura account provisioning** nella finestra del portale classica Azure.

Per informazioni dettagliate su come configurare il provisioning automatico, vedere [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Assegnazione agli utenti di Facebook al lavoro

Per gli utenti AAD provisioning in grado di vedere Facebook in ufficio sul pannello loro l'accesso, è necessario assegnare l'accesso all'interno del portale classico Azure.

**Per assegnare gli utenti a Facebook in ufficio:**

1.  Nella pagina iniziale di Facebook al lavoro nel portale di classica Azure, fare clic su **Assegna account**.

2.  Nel menu **Visualizza** selezionare se si desidera assegnare un utente o un gruppo a Facebook al lavoro e fare clic sul pulsante di segno di spunta.

3.  Nell'elenco risultante, selezionare gli utenti o il gruppo a cui si desidera assegnare Facebook in ufficio.

4.  Nel piè di pagina, fare clic sul pulsante **Assegna** .


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




