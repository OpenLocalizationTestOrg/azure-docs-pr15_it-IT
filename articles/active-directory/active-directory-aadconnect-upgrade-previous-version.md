<properties
   pageTitle="Azure AD Connect: Eseguire l'aggiornamento da una versione precedente | Microsoft Azure"
   description="Illustra i diversi metodi per l'aggiornamento alla versione più recente di Azure Active Directory di connettersi, compresi aggiornamento sul posto e la migrazione di apertura."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alle ultime
In questo argomento descrive i vari metodi che consente di aggiornare l'installazione di Azure AD Connect alla versione più recente. È consigliabile mantenere personalmente corrente con le versioni di Azure AD Connect. I passaggi descritti in [ruotare migrazione](#swing-migration) utilizzati anche quando si effettua una configurazione sostanziale modificare.

Se si vuole eseguire l'aggiornamento da DirSync, vedere [eseguire l'aggiornamento da strumento di sincronizzazione di Azure Active Directory (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Esistono diverse strategie di eseguire l'aggiornamento di Azure AD Connect.

Metodo | Descrizione
--- | ---
[Aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) | Per i clienti con un'installazione rapida, questo è il metodo più semplice.
[Aggiornamento sul posto](#in-place-upgrade) | Se si dispone di un singolo server, aggiornare l'installazione in posizione nello stesso server.
[Migrazione di apertura](#swing-migration) | Con due server, è possibile preparare uno dei server con la nuova versione o configurazione e modificare ASP quando si è pronti.

Per le autorizzazioni necessarie, vedere [autorizzazioni necessarie per l'aggiornamento](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Aggiornamento sul posto
Un aggiornamento sul posto funziona per lo spostamento di sincronizzazione di Azure Active Directory o Azure AD Connect. Non funzionerà per DirSync o per una soluzione con FIM + Azure Active Directory Connector.

Questo metodo è preferibile quando si dispone di un singolo server e minore di circa 100.000 oggetti. Se sono state apportate modifiche alle regole di casella di sincronizzazione, un'importazione completa e sincronizzazione completa verificarsi dopo l'aggiornamento. In questo modo che la nuova configurazione viene applicata a tutti gli oggetti esistenti nel sistema. Questa operazione può richiedere alcune ore a seconda del numero di oggetti nell'ambito del motore di sincronizzazione. L'utilità di pianificazione della sincronizzazione delta normale, per impostazione predefinita ogni 30 minuti è sospesa ma continua la sincronizzazione delle password. È possibile eseguire l'aggiornamento sul posto durante un fine settimana. Se sono state apportate modifiche alla configurazione della casella di con la nuova versione di Azure AD Connect, verrà avviato invece una normale delta importazione/sincronizzazione.  
![Aggiornamento sul posto](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se sono state apportate modifiche alle regole di casella di sincronizzazione, questi verrà impostati tornare alla configurazione predefinita durante l'aggiornamento. Per assicurarsi che la configurazione viene mantenuta tra gli aggiornamenti, assicurarsi che le modifiche apportate come descritto nelle [procedure consigliate per la modifica della configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migrazione di apertura
Se si dispone di una distribuzione complessa o molto molti oggetti, potrebbe risultare poco per eseguire un aggiornamento sul posto sul sistema attivo. L'operazione per alcuni clienti richiederà più giorni e durante questo periodo non verrà elaborata alcuna modifica delta. Questo metodo viene usato anche quando si prevede di apportare modifiche significative alla configurazione e si vuole provare la prima che questi vengono inseriti nel cloud.

Il metodo consigliato per gli scenari seguenti è usare una migrazione di apertura. È necessario (almeno) due server, uno attivo e un server di gestione temporaneo. Il server attivo (linee blu continue nella figura seguente) è responsabile per il carico di produzione attivo. Il server di gestione temporanea (viola tratteggiate nella figura seguente) preparato con la nuova versione o configurazione e quando si è pronti completamente, il server viene attivato. Server active precedente, ora con la versione precedente o installata, configurazione apportate server di gestione temporanea aggiornato

I due server è possono usare versioni diverse. Ad esempio, ASP che si prevede di rimuovere le autorizzazioni consentono di sincronizzazione di Azure Active Directory e il server di gestione temporaneo nuovo può utilizzare Azure AD Connect. Se si utilizza la migrazione di apertura per lo sviluppo di una nuova configurazione è consigliabile avere le stesse versioni su due server.  
![Server di gestione temporanea](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: Si è notato che alcuni utenti preferiscono disporre di tre o quattro server per questo scenario. Quando si aggiorna il server di gestione temporanea, non è un server di backup in caso di [emergenza](active-directory-aadconnectsync-operations.md#disaster-recovery). Con tre o quattro server possibile preparare un gruppo di server principale/standby con la nuova versione garantisce vi siano sempre un server di gestione temporanea pronti per subentrare.

Questa procedura anche per passare dalla sincronizzazione di Azure Active Directory o una soluzione con FIM + Azure Active Directory Connector. Questa procedura non funziona per DirSync, ma lo stessa apertura (denominato anche distribuzione in parallelo) metodo di migrazione con i passaggi per il DirSync può trovarsi [nell'Aggiornamento Azure Active Directory (DirSync) di sincronizzazione](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Passaggi di migrazione di apertura

1. Se si utilizza Azure AD Connect su entrambi i server e si prevede di eseguire solo una modifica della configurazione, verificare che il server attivo e server di gestione temporanea sono entrambe usando la stessa versione. Che rende più facile confrontare le differenze in un secondo momento. Se si sta aggiornando sincronizzazione di Azure Active Directory, questi server dispongono di versioni diverse. Se esegue l'aggiornamento da una versione precedente di Azure AD Connect, è consigliabile iniziare con due server usando la stessa versione, ma non è invece necessario.
2. Se si sono apportate alcune attività di configurazione personalizzato e non ha il server di gestione temporanea, seguire i passaggi in [spostare configurazione personalizzata da attivo a server di gestione temporanea](#move-custom-configuration-from-active-to-staging-server).
3. Se esegue l'aggiornamento da una versione precedente di Azure AD Connect, aggiornare il server di gestione temporanea per la versione più recente. Se si spostano dalla sincronizzazione di Azure Active Directory, installare Azure AD Connect nel server di gestione temporanea.
4. Consentire al motore di sincronizzazione eseguire importazione completa e sincronizzazione completa sui server di gestione temporanea.
5. Verificare che la nuova configurazione non è stato causano modifiche impreviste mediante la procedura in **verifica** in [verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se non è sincronizzazione e come previsto, corrette, eseguire importazione e verificare fino a quando non sono soddisfacente i dati. La procedura seguente sono disponibili nella sezione collegate.
6. Passare a altro server di gestione temporanea per il server attivo. Questo è il passaggio finale **passare ASP** in [verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se si sta aggiornando Azure AD Connect, aggiornare il server in modalità per la versione più recente di gestione temporanea. Seguire la stessa procedura descritta in precedenza per ottenere i dati e aggiornare la configurazione. Se è stato aggiornato dalla sincronizzazione di Azure Active Directory, è possibile disattivare e rimuovere il vecchio server.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Spostare configurazione personalizzata da attivo al server di gestione temporanea
Se sono state apportate modifiche di configurazione per il server attivo, è necessario assicurarsi che le stesse modifiche vengono applicate al server di gestione temporaneo.

È possibile spostare le regole di sincronizzazione personalizzato creato con PowerShell. Altre modifiche devono essere applicati allo stesso modo in entrambi i sistemi e non possono essere migrati.

Cosa che è necessario assicurarsi che sia configurato in modo stesso su entrambi i server:

- Connessione a insiemi stesso.
- Qualsiasi dominio e unità Organizzativa di filtro.
- Caratteristiche facoltative stesso, ad esempio la sincronizzazione delle password e writeback di password.

**Sposta le regole di sincronizzazione**  
Per spostare una regola di sincronizzazione personalizzato, eseguire le operazioni seguenti:

1. Aprire **L'Editor delle regole sincronizzazione** nel server active.
2. Selezionare la regola personalizzata. Fare clic su **Esporta**. Verrà visualizzata una finestra del blocco note. Salvare il file temporaneo con estensione PS1. In questo modo è uno script di PowerShell. Copiare il file ps1 al server di gestione temporaneo.  
![Esportazione delle regole di sincronizzazione](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. GUID connettore è diverso sui server di gestione temporanea e devono essere modificati. Per ottenere il GUID, avviare **l'Editor di regole di sincronizzazione**, selezionare una delle regole di pronte che rappresenta lo stesso sistema connesso e fare clic su **Esporta**. Sostituire il GUID nel file PS1 con GUID dal server di gestione temporaneo.
4. Prompt dei comandi di PowerShell eseguire il file PS1. La regola di sincronizzazione personalizzato verrà creato nel server di gestione temporanea.
5. Se si dispone di più regole personalizzate, ripetere per tutte le regole personalizzate.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
