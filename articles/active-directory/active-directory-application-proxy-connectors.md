<properties
    pageTitle="Utilizzo dei connettori di Azure Active Directory applicazione Proxy | Microsoft Azure"
    description="Viene illustrato come creare e gestire gruppi di connettori Proxy di applicazione Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Pubblicare applicazioni su reti separate e percorsi con i gruppi di connettore

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portale classica Azure](active-directory-application-proxy-connectors.md)


Connettore gruppi sono utili per diversi scenari, tra cui:

- Siti con più Data Center interconnessi. In questo caso, si desidera mantenere quante il traffico nel centro dati possibili collegamenti Data Center incrociati sono in genere costosa e lenta. È possibile distribuire i connettori in ogni Data Center per servire solo le applicazioni presenti nel centro dati. Questo approccio riduce Data Center tra collegamenti e offre un'esperienza completamente trasparente agli utenti.
- Gestire le applicazioni installate nel reti isolate che non fanno parte della rete aziendale principale. È possibile utilizzare i gruppi di connettore per installare i connettori dedicati su reti isolate isolare anche le applicazioni nella rete.
- Per le applicazioni installate nel IaaS per l'accesso cloud, i gruppi di connettore forniscono un servizio comune per proteggere l'accesso a tutte le applicazioni. Gruppi di connettore non creare dipendenza aggiuntiva nella rete aziendale oppure frammento l'esperienza di app. Connettori possono essere installati su ogni data center cloud e utilizzare solo le applicazioni presenti in questa rete. È possibile installare connettori diverse per ottenere la disponibilità elevata.
- Supporto per gli ambienti più insiemi di strutture in cui è possono distribuiti per ogni foresta connettori specifici e impostare per servire applicazioni specifiche.
- Connettore gruppi possono essere utilizzati nei siti di emergenza rilevare uno failover o come copia di backup per il sito principale.
- Connettore gruppi possono essere utilizzati anche per servire più società da un singolo tenant.

## <a name="prerequisite-create-your-connectors"></a>Prerequisito: Creare i connettori
Per raggruppare i connettori, è necessario assicurarsi che non è [installato più connettori](active-directory-application-proxy-enable.md)e tale nome è loro e poi raggrupparli. Infine, è necessario assegnarle alle applicazioni specifiche.

## <a name="step-1-create-connector-groups"></a>Passaggio 1: Creare gruppi di connettore
È possibile creare altri gruppi connettore. Il portale classico Azure mediante la creazione di gruppi di connettore.

1. Selezionare la directory e fare clic su **Configura**.  
    ![Proxy di applicazione, configurare schermata, fare clic su Gestisci gruppi di connettore](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. In applicazione Proxy, fare clic su **Gestisci gruppi di connettore** e creare un nuovo gruppo di connettore per assegnare un nome al gruppo.  
    ![Schermata di gruppi di applicazione proxy connettore - nome nuovo gruppo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Passaggio 2: Assegnare i connettori ai gruppi
Dopo aver creati i gruppi di connettore, spostare i connettori al gruppo appropriato.

1. In **Applicazione Proxy**, fare clic su **Gestisci connettori**.
2. Nel **gruppo**selezionare il gruppo desiderato per ogni connettore. I connettori può richiedere fino a 10 minuti diventi attivo nel gruppo nuovo.  
    ![Schermata di connettori proxy applicazione - gruppo dal menu a discesa](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Passaggio 3: Assegnare applicazioni ai gruppi di connettore
L'ultimo passaggio consiste nel configurare ogni applicazione al gruppo di connettore che verrà utilizzata in.

1. Nel portale di classica Azure, nella directory, selezionare l'applicazione che si desidera assegnare al gruppo e fare clic su **Configura**.
2. Nel **gruppo connettore**, selezionare il gruppo e di utilizzare. Questa modifica viene applicata immediatamente.  
    ![Applicazione proxy connettore gruppo schermata - gruppo dal menu a discesa](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Vedere anche

- [Attivare il Proxy dell'applicazione](active-directory-application-proxy-enable.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risoluzione dei problemi che si verificano con Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
