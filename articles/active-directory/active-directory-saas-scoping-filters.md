<properties
    pageTitle="Il provisioning di app basata su attributi con ambito filtri | Microsoft Azure"
    description="Informazioni su come utilizzare i filtri dell'ambito per evitare che gli oggetti nelle applicazioni che supportano il provisioning utente automatico da provisioning effettivamente se un oggetto non soddisfa le esigenze aziendali."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Il provisioning di app basata su attributi con i filtri di ambito

L'obiettivo di questa sezione è viene illustrato come utilizzare i filtri dell'ambito per definire regole basate su attributo che consente di verificare quali utenti cui eseguire il provisioning all'applicazione.





## <a name="clauses-and-scope-groups"></a>Clausole e gruppi di ambito


![Definizione dell'ambito filtro][1] 




Filtri dell'ambito sono definiti da uno o più **gruppi di ambiti**, ognuno dei quali tenere una o più **clausole**. Per visualizzare le clausole per un gruppo particolare ambito, espanderlo facendo clic sulla freccia a sinistra del nome del gruppo.

Una **clausola** determina quali utenti è consentito passano attraverso il filtro ambito valutando gli attributi di ogni utente. Ad esempio, potrebbe essere una clausola che è necessario che sia uguale a New York, vale a dire che solo gli utenti di New York eseguire il provisioning nell'applicazione '' attributo di un utente.

![Definizione dell'ambito nome del gruppo][2] 



Ogni **gruppo ambito** inizia con obbligatoria una **clausola**, come illustrato nella figura riportata sopra. Per questa clausola semplicemente indica che l'utente deve essere assegnato all'applicazione prima di essere valutato per i filtri dell'ambito. Per questa clausola non è possibile eliminare o modificare.

È possibile aggiungere nuove clausole o nuovi gruppi di ambito facendo clic sul pulsante appropriato. È possibile assegnare un nome di ogni gruppo ambito modificando la proprietà **Nome del gruppo ambito** .





## <a name="how-scoping-filters-are-evaluated"></a>Come vengono valutati filtri definizione dell'ambito

Durante il provisioning, viene eseguito il test tutti gli utenti assegnati con i filtri dell'ambito per determinare se tale utente merita l'accesso all'applicazione. È possibile pensare di ogni clausola come un test che deve essere superato per consentire all'utente di evitare la visualizzazione filtrata. 

Se si dispone di più gruppi di ambito definiti, ogni utente deve passare almeno uno di essi per accedere all'applicazione. All'interno di ogni gruppo ambito, tuttavia, l'utente deve superare ogni singola clausola per passare a tale gruppo ambito specifico. 

In altre parole, è possibile pensare di gruppi di ambito come o congiunti e le clausole al loro interno come è possibile pensare e congiunti. Si consideri ad esempio il filtro ambito riportata di seguito:


![Definizione dell'ambito nome del gruppo][2]  


In base a questo filtro ambito, gli utenti devono soddisfare i criteri seguenti per eseguire il provisioning:

1. Devono essere assegnate all'applicazione.

2. Funzionamento del reparto di progettazione

3. Deve trattarsi di lavoro in San Francisco o in Canada.


##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Automatizzare utente il Provisioning e Deprovisioning alle applicazioni SaaS](active-directory-saas-app-provisioning.md)
- [Personalizzazione dei mapping degli attributi per il Provisioning dell'utente](active-directory-saas-customizing-attribute-mappings.md)
- [Scrittura di espressioni per mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Account il Provisioning di notifiche](active-directory-saas-account-provisioning-notifications.md)
- [Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni](active-directory-scim-provisioning.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
