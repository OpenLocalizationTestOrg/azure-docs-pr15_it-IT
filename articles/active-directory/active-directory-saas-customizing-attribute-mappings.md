<properties
    pageTitle="Personalizzazione dei mapping degli attributi | Microsoft Azure"
    description="Scoprire quali mapping degli attributi per le app SaaS in Azure Active Directory come è possibile modificarli per soddisfare le esigenze aziendali."
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


# <a name="customizing-attribute-mappings"></a>Personalizzazione dei mapping degli attributi


Microsoft Azure Active Directory fornisce supporto per il provisioning dell'utente alle applicazioni di terze parti SaaS, ad esempio Salesforce, Google Apps e così via. Se si dispone di provisioning dell'utente per una terza parte applicazione SaaS attivata, il portale di gestione di Azure controlla i valori di attributo in forma di una configurazione denominata "mapping degli attributi".

Esiste un set di mapping di attributo tra gli oggetti utente di Azure Active Directory e gli oggetti utente dell'applicazione ogni SaaS preconfigurato. Alcune App gestire altri tipi di oggetti, ad esempio gruppi o contatti. <br> 
È possibile personalizzare il mapping degli attributi predefiniti in base alle esigenze aziendali. In questo modo, è possibile modificare o eliminare i mapping degli attributi esistenti o creare nuovi mapping attributo.

Nel portale di Azure Active Directory, è possibile accedere questa caratteristica, fare clic su attributi nella barra degli strumenti di un'applicazione di SaaS.

> [AZURE.NOTE] Il collegamento **attributi** è disponibile solo se è abilitata per un'applicazione di SaaS il provisioning dell'utente. 


![Forza vendita][1] 


Quando si fa clic su attributi nella barra degli strumenti, l'elenco dei mapping correnti configurati per un'applicazione di SaaS.

La schermata seguente mostra un esempio di seguente:



![Forza vendita][2]  


Nell'esempio precedente, è possibile vedere che l'attributo di **nome** di un oggetto gestito in Salesforce viene popolata con il valore di **givenName** del collegato oggetto Azure Active Directory.

Se si desidera personalizzare il mapping degli attributi o se si desidera ripristinare le impostazioni alla configurazione predefinita personalizzate, è possibile eseguire questa operazione fare clic sul pulsante correlato nella barra degli strumenti nella parte inferiore di un'applicazione.


![Forza vendita][3]  


Sono disponibili mapping degli attributi necessari per un'applicazione di SaaS a funzionare correttamente. Nella tabella attributi mapping attributo correlato avere **Sì** come valore dell'attributo **obbligatorio** . Se è necessario un mapping di attributo, è possibile eliminarlo. In questo caso, **eliminare** non è disponibile.

Per modificare un mapping attributo esistente, selezionare il mapping e quindi fare clic su **Modifica**. Verrà visualizzata una pagina di finestra di dialogo che consente di modificare il mapping degli attributi selezionato.


![Modificare il Mapping degli attributi][4]  



## <a name="understanding-attribute-mapping-types"></a>Attributo il mapping dei tipi di informazioni


Mapping di attributi, controllo come attributi vengono inseriti in una terza parte applicazione SaaS. Sono disponibili quattro diversi tipi di mapping supportati:

- **Accesso diretto** : l'attributo di destinazione viene popolata con il valore dell'attributo dell'oggetto collegato in Azure Active Directory.


- **Costante** : l'attributo di destinazione viene popolata con una stringa specifica che è stato specificato.


- **Espressione** - l'attributo di destinazione viene popolata basata sul risultato di un'espressione di tipo mi piace script. Per ulteriori informazioni, vedere [Scrittura di espressioni per il mapping degli attributi di Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Nessuno** - l'attributo di destinazione è invariato a sinistra. Tuttavia, se l'attributo di destinazione non è mai vuota, verrà specificato con il valore predefinito specificato.



Oltre a questi quattro tipi di mapping degli attributi di base, mapping degli attributi personalizzati supportano il concetto di un'assegnazione valore **predefinito** . L'assegnazione di valore predefinito garantisce che un attributo di destinazione viene popolato con un valore se di nessuno dei due valori in Azure Active Directory né sull'oggetto di destinazione.

Microsoft Azure Active Directory fornisce un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, solo gli oggetti che richiedono gli aggiornamenti vengono elaborati durante un ciclo di sincronizzazione. Aggiornamento dei mapping degli attributi ha un impatto sulle prestazioni di un ciclo di sincronizzazione. In questo modo un aggiornamento per la configurazione di mapping attributo richiede tutti gli oggetti gestiti da valutare di nuovo. Per questi motivi, è una procedura consigliata per mantenere il numero di errori consecutivi modifiche per il mapping degli attributi minimo.


##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Automatizzare utente Provisioning/Deprovisioning alle App SaaS](active-directory-saas-app-provisioning.md)
- [Scrittura di espressioni per mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Definizione dell'ambito filtri per il Provisioning dell'utente](active-directory-saas-scoping-filters.md)
- [Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni](active-directory-scim-provisioning.md)
- [Account il Provisioning di notifiche](active-directory-saas-account-provisioning-notifications.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
