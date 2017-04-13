<properties
    pageTitle="Eseguire l'aggiornamento da DirSync e sincronizzazione di Azure Active Directory | Microsoft Azure"
    description="Descrive come eseguire l'aggiornamento da DirSync e sincronizzazione di Azure Active Directory a Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Aggiornamento di Windows Azure Active Directory sincronizzazione ("DirSync") e sincronizzazione di Azure Active Directory ("Azure Active Sync")
Azure AD Connect è il modo migliore per metta Azure Active directory locale e in Office 365. Questo è il momento giusto per l'aggiornamento a Azure AD Connect da Windows Azure Active sincronizzazione della Directory (DirSync) o sincronizzazione di Azure Active Directory come questi strumenti sono obsoleti e verranno recapitate fine del supporto di 13 aprile 2017.

Gli strumenti di sincronizzazione di due identità che sono obsoleti sono stati disponibili per i clienti singola foresta (DirSync) e per più insiemi di strutture e altre avanzati per i clienti (sincronizzazione di Azure Active Directory). Questi strumenti precedenti sono stati sostituiti con una singola soluzione che è disponibile per tutti gli scenari: Azure AD Connect. Offre nuove funzionalità, miglioramenti e supporto per gli scenari di nuovi. Per poter continuare a sincronizzare i dati di identità locale di Azure Active Directory e Office 365, è consigliabile eseguire l'aggiornamento a Azure AD Connect.

L'ultima versione del DirSync è stato rilasciato nel luglio 2014 e alla versione precedente di sincronizzazione di Azure Active Directory è stata rilasciata nel maggio 2015.

## <a name="what-is-azure-ad-connect"></a>Che cos'è Azure AD Connect
Azure AD Connect è il successore DirSync e sincronizzazione di Azure Active Directory. Combina tutti gli scenari di questi due supportati. È possibile leggere altre informazioni è [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Pianificazione negativi

Data | Commento
 --- | ---
13 aprile 2016 | Sincronizzazione Windows Azure Active Directory ("DirSync") e Microsoft Azure Active Directory Sync ("Azure Active Sync") sono annunciato come obsoleto.
13 aprile 2017 | Supporta le estremità. Non saranno in grado di aprire un caso di supporto senza eseguire l'aggiornamento a Azure AD Connect prima di tutto clienti.

## <a name="how-to-transition-to-azure-ad-connect"></a>Come eseguire la transizione a Azure AD Connect
Se si esegue DirSync esistono due modi è possibile eseguire l'aggiornamento: aggiornamento e in parallela distribuzione In locale. Un aggiornamento sul posto è consigliato per la maggior parte dei clienti e se si dispone di un recente sistema operativo e minore di 50.000 oggetti. In altri casi è consigliabile eseguire una distribuzione in parallelo nel punto in cui la configurazione di DirSync viene spostata in un nuovo server Azure AD Connect.

Se si Usa sincronizzazione di Azure Active Directory è consigliabile un aggiornamento sul posto. Se si desidera, è possibile installare un server Azure AD Connect in parallelo ed eseguire una migrazione di apertura dal server di sincronizzazione di Azure Active Directory a Azure AD Connect.

Soluzione | Scenario:
----- | -----
[Eseguire l'aggiornamento da DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se si dispone di un server DirSync esistente già in esecuzione.</li>
[Eseguire l'aggiornamento da Azure sincronizzazione di Active Directory](active-directory-aadconnect-upgrade-previous-version.md)| <li>Se si sposta dalla sincronizzazione di Azure Active Directory.</li>

Se si desidera visualizzare la procedura di un aggiornamento sul posto da DirSync per Azure AD Connect, vedere questo video Channel 9:

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>Domande frequenti
**D: è stato ricevuto un messaggio di notifica da Team Azure e/o di un messaggio del centro messaggi di Office 365, ma si usa la connessione.**  
La notifica è stata inviata anche a clienti utilizzando Azure AD Connect con un numero di build 1.0. \*,0 (con una versione pre-1.1). Si consiglia ai clienti di rimanere aggiornati con le versioni di Azure AD Connect. Con la funzionalità di [aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) renderà 1.1 installato molto semplice avere sempre una versione recente di Azure AD Connect.

**Interrompere la sincronizzazione di Active Directory DirSync/Azure verrà d: lavorando 13 aprile 2017?**  
No. La data per quando questi non saranno in grado di comunicare con Azure Active Directory viene annunciata in un secondo momento. Sarà possibile trovare le informazioni in questo argomento quando disponibili.

**D: che versioni DirSync è possibile passare da?**  
Questa caratteristica è supportata per l'aggiornamento dalla versione qualsiasi DirSync attualmente in uso.

**Q: per quanto riguarda il connettore Azure Active Directory per FIM/MIM?**  
Il connettore di Azure Active Directory **per FIM/MIM è stata** dichiarati obsoleti. È in una **caratteristica bloccare**; non verrà aggiunto alcun nuove funzionalità e non riceve correzioni di bug. Si consiglia di clienti che utilizzano al piano per spostarsi da essa Azure AD Connect. Si consiglia di non avviare le nuove distribuzioni usarlo. Il connettore viene annunciato deprecato in futuro.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
