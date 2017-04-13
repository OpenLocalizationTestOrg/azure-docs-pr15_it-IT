<properties
    pageTitle="Report di Azure autenticazione a più fattori"
    description="Descrive come usare la caratteristica di autenticazione a più fattori Azure - report."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure autenticazione a più fattori

Autenticazione a più fattori Azure offre vari report che possono essere usate per la propria organizzazione. Questi report accessibili tramite il portale di gestione, l'autenticazione a più fattori che è necessario avere una licenza di Azure MFA Provider, o un MFA di Azure, Azure Active Directory Premium o Enterprise mobilità Suite. Di seguito è un elenco di report disponibili.

È possibile accedere report tramite il portale di gestione di Azure.

Nome| Descrizione
:------------- | :------------- |
Uso | L'utilizzo report visualizzare informazioni sull'utilizzo generale, utente riepilogo e dettagli utente.
Stato del server|Questo report consente di visualizzare lo stato dei server di autenticazione a più fattori associata al proprio account.
Cronologia utente bloccati|Questi report mostrano la cronologia delle richieste per bloccare o sbloccare gli utenti.
Cronologia utente ignorata|Visualizza la cronologia delle richieste per ignorare l'autenticazione a più fattori per numero di telefono dell'utente.
Avviso di frode|Mostra la cronologia degli avvisi di frode inviato durante l'intervallo di date specificata.
In coda|Report di elenchi in coda per l'elaborazione e il relativo stato. Una volta completato il report, viene fornito un collegamento per scaricare o visualizzare il report.

## <a name="to-view-reports"></a>Per visualizzare i report

1.  Accedere a http://azure.microsoft.com
2.  A sinistra, selezionare Active Directory.
3.  Selezionare una delle opzioni seguenti:
    - **Opzione 1**: fare clic sulla scheda a più fattori Auth provider. Selezionare il proprio provider MFA e fare clic sul pulsante Gestisci nella parte inferiore.
    - **Opzione 2**: selezionare la directory e fare clic sulla scheda Configura. Nella sezione autenticazione a più fattori, selezionare le impostazioni del servizio di gestione. Nella parte inferiore della pagina Impostazioni servizio MFA, fare clic su Vai al collegamento al portale.
4.  Nel portale di Gestione autenticazione a più fattori Azure, verranno visualizzati la visualizzazione una sezione di Report nel riquadro di spostamento sinistro. Da qui è possibile selezionare i rapporti descritti sopra.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Risorse aggiuntive**

* [Per gli utenti](./end-user/multi-factor-authentication-end-user.md)
* [Autenticazione a più fattori Azure su MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
