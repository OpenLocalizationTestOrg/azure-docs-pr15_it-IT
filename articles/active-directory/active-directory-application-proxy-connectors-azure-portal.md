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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Pubblicare applicazioni su reti separate e percorsi con i gruppi di connettore - Public Preview

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portale classica Azure](active-directory-application-proxy-connectors.md)


Connettore gruppi sono utili per diversi scenari, tra cui:

- Siti con più Data Center interconnessi. In questo caso, si desidera mantenere il maggior numero il traffico nel centro dati possibili collegamenti Data Center incrociati sono costosa e lenta. È possibile distribuire i connettori in ogni Data Center per servire solo le applicazioni presenti nel centro dati. Questo approccio riduce Data Center tra collegamenti e offre un'esperienza completamente trasparente agli utenti.
- Gestire le applicazioni installate nel reti isolate che non fanno parte della rete aziendale principale. È possibile utilizzare i gruppi di connettore per installare i connettori dedicati su reti isolate isolare anche le applicazioni nella rete.
- Per le applicazioni installate nel IaaS per l'accesso cloud, i gruppi di connettore forniscono un servizio comune per proteggere l'accesso a tutte le applicazioni. Gruppi di connettore non creare dipendenza aggiuntiva nella rete aziendale oppure frammento l'esperienza di app. Connettori possono essere installati su ogni data center cloud e utilizzare solo le applicazioni presenti in questa rete. È possibile installare connettori diverse per ottenere la disponibilità elevata.
- Supporto per gli ambienti più insiemi di strutture in cui è possono distribuiti per ogni foresta connettori specifici e impostare per servire applicazioni specifiche.
- Connettore gruppi possono essere utilizzati nei siti di emergenza rilevare uno failover o come copia di backup per il sito principale.
- Connettore gruppi possono essere utilizzati anche per servire più società da un singolo tenant.

## <a name="prerequisite-create-your-connectors"></a>Prerequisito: Creare i connettori
Per raggruppare i connettori, è necessario assicurarsi di aver [installato più connettori](active-directory-application-proxy-enable.md). Durante l'installazione di un nuovo connettore unisca automaticamente il gruppo di connettore **predefinito** .

## <a name="step-1-create-connector-groups"></a>Passaggio 1: Creare gruppi di connettore
È possibile creare altri gruppi connettore. Il [portale di Azure](https://portal.azure.com)mediante la creazione di gruppi di connettore.

1. Selezionare **Azure Active Directory** per passare al dashboard di gestione della directory. A questo punto, selezionare **applicazioni aziendali** > **proxy dell'applicazione**.

2. Selezionare il pulsante di **Gruppi di connettore** . Verrà visualizzata e il nuovo gruppo di connettore.

3. Specificare un nome per il nuovo gruppo di connettore e quindi utilizzare il menu a discesa per selezionare i connettori appartengono in questo gruppo.

4. Una volta completato il connettore gruppo, selezionare **Salva** .

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Passaggio 2: Assegnare applicazioni ai gruppi di connettore
L'ultimo passaggio consiste nel configurare ogni applicazione al gruppo di connettore che verrà utilizzata in.

1. Dashboard di gestione della directory, selezionare **applicazioni aziendali** > **tutte le applicazioni** > l'applicazione che si desidera assegnare a un gruppo di connettore > **Proxy dell'applicazione**.
2. Nel **gruppo connettore**, utilizzare il menu a discesa per selezionare il gruppo e di utilizzare.
3. Selezionare **Salva** per applicare la modifica.


## <a name="see-also"></a>Vedere anche

- [Attivare il Proxy dell'applicazione](active-directory-application-proxy-enable.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risoluzione dei problemi che si verificano con Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
