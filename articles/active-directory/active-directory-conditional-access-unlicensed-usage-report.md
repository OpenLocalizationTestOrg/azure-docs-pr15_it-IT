<properties
    pageTitle="Report di utilizzo senza licenza | Microsoft Azure"
    description="Relazione consente di utilizzo senza licenza che identificare gli utenti senza licenza che usano pagato caratteristiche di Azure Active Directory."
    services="active-directory"
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

# <a name="unlicensed-usage-report"></a>Report di utilizzo senza licenza

I più facile report di utilizzo senza licenza che identificare utenti senza licenza che utilizzano pagato caratteristiche di Azure Active Directory. In questo modo è possibile apportare migliore utilizzo delle licenze acquistate e per identificare si sa quando è possibile acquistare altre licenze. 

Il report mostra attivo utilizzo delle caratteristiche a pagamento negli ultimi 30 giorni. 

## <a name="report-structure"></a>Struttura del report
 
| Nome di colonna          |    Descrizione |
| :--                  | :--         |
| Utenti senza licenza      |    Nome dell'utente |
| Caratteristica              | Il nome della caratteristica. Ad esempio: accesso condizionato |
| Applicazione accessibili | Il nome dell'applicazione accessibile con la caratteristica. Ad esempio: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Se è stato eliminato un account utente nella colonna "Utenti senza licenza" verrà visualizzato un ID, ad esempio 1003000090D8B285


## <a name="conditional-access-feature"></a>Funzionalità di accesso condizionale

Utenti senza licenza verranno contrassegnati quando accedono a un servizio in cui il criterio di accesso condizionale applicata se non hanno una licenza di Azure Active Directory Premium. 

Si applica ad MFA / criteri posizione, nonché dispositivo criteri che usano Intune.
 

## <a name="see-also"></a>Vedere anche

- [Utilizzo di Access condizionale con Office 365 e altri Azure Active Directory connessi App](active-directory-conditional-access.md)
- [Introduzione a access condizionali per Azure Active Directory](active-directory-conditional-access-azuread-connected-apps.md) 


