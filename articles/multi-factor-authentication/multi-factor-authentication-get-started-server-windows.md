<properties 
    pageTitle="L'autenticazione di Windows e Azure Server di autenticazione a più fattori"
    description="Questa è la pagina di autenticazione a più fattori Azure utili nella distribuzione di autenticazione di Windows e Server di autenticazione a più fattori Azure."
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
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>L'autenticazione di Windows e Azure Server di autenticazione a più fattori

La sezione autenticazione di Windows consente all'amministratore attivare e configurare l'autenticazione di Windows per una o più applicazioni.  Di seguito è un elenco di cose da tenere in considerazione prima di configurare l'autenticazione di Windows.

-  riavviare il computer è necessario prima l'autenticazione a più fattori Azure per servizi Terminal verrà attivata.
-  Se non si è nell'elenco degli utenti 'Match utente richiede l'autenticazione a più fattori di Azure' è selezionata, non sarà possibile accedere al computer dopo il riavvio.
-  IP di attendibile dipende se l'applicazione può fornire l'indirizzo IP client con l'autenticazione. Attualmente solo servizi Terminal è supportato.  







>[AZURE.NOTE]Questa caratteristica non è supportata per i servizi Terminal protetti in Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Per proteggere un'applicazione con l'autenticazione di Windows, utilizzare la procedura seguente.

1. Nel Server di autenticazione a più fattori Azure fare clic sull'icona di autenticazione di Windows.
![Autenticazione di Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Selezionare la casella di controllo di Windows di abilitare l'autenticazione. Per impostazione predefinita, questa casella sia selezionata.
3. Nella scheda applicazioni consente all'amministratore configurare uno o più applicazioni per l'autenticazione di Windows.
4. Selezionare un'applicazione o un server, specificare se l'applicazione/server è abilitato. Fare clic su OK.
5. Fare clic su Aggiungi... pulsante.
6. La scheda IP attendibili consente di ignorare le sessioni di Azure autenticazione a più fattori per Windows provenienti da specifici IP. Ad esempio i dipendenti che utilizzano l'applicazione di office e da casa, si può decidere che non si vuole telefono squilla per l'autenticazione a più fattori Azure in ufficio. A tale scopo, è necessario specificare subnet office come voce di indirizzi IP attendibili.
7. Fare clic su Aggiungi... pulsante.
8. Selezionare IP solo se si vuole ignorare un solo indirizzo IP.
9. Selezionare intervalli IP se si vuole ignorare un intero intervallo di indirizzi IP. Esempio 10.63.193.1-10.63.193.100.
10. Per specificare un intervallo di indirizzi IP utilizzando la notazione subnet, selezionare Subnet. Immettere indirizzi IP iniziale del subnet e selezionare la maschera appropriato nell'elenco a discesa.
11. Fare clic sul pulsante OK.
