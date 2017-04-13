<properties
   pageTitle="Sincronizzazione di Azure AD Connect: considerazioni e attività operative | Microsoft Azure"
   description="In questo argomento descrive attività operative per la sincronizzazione di Azure AD Connect e come preparare per il funzionamento di questo componente."
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
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronizzazione di Azure AD Connect: esame e attività operative
L'obiettivo di questo argomento è descrivere attività operative per la sincronizzazione di Azure AD Connect.

## <a name="staging-mode"></a>Modalità di gestione temporanea
Modalità di gestione temporanea può essere usata per diversi scenari, tra cui:

-   Disponibilità.
-   Verificare e distribuire nuove modifiche di configurazione.
-   Presentare un nuovo server ed eliminare il vecchio.

Con un server in modalità di gestione temporanea, è possibile apportare modifiche alla configurazione e anteprima delle modifiche apportate nel server active. È anche possibile eseguire importazione completa e sincronizzazione completa per verificare che tutte le revisioni sono previsto prima di apportare le modifiche nel proprio ambiente di produzione.

Durante l'installazione, è possibile selezionare il server in **modalità di gestione temporanea**. Questa azione attiva il server per l'importazione e sincronizzazione, ma non viene eseguito alle esportazioni. Un server in modalità di gestione temporanea non è in esecuzione la sincronizzazione delle password o writeback di password, anche se è selezionato queste caratteristiche durante l'installazione. Quando si disattiva modalità di gestione temporanea, il server avvia l'esportazione consente, la sincronizzazione delle password ed effettuare writeback di password.

È comunque possibile forzare un'esportazione tramite la gestione di servizi di sincronizzazione.

Un server in modalità di gestione temporanea continuerà a ricevere modifiche da Active Directory e Azure Active Directory. Ha sempre una copia delle modifiche più recenti e può creare rapidamente sulle responsabilità di un altro server. Se si apportano modifiche di configurazione per il server primario, è necessario apportare le stesse modifiche nel server in modalità di gestione temporanea.

Per coloro che conosca precedenti tecnologie di sincronizzazione, la modalità di gestione temporanea è diversa dal momento che il server dispone di un database SQL. Questa architettura consente server la modalità di gestione temporanea deve trovarsi in un centro di dati diversi.

### <a name="verify-the-configuration-of-a-server"></a>Verificare la configurazione di un server
Per applicare questo metodo, procedere come segue:

1. [Preparare l'ambiente](#prepare)
2. [Importare e sincronizzare](#import-and-synchronize)
3. [Verificare](#verify)
4. [ASP parametro](#switch-active-server)

#### <a name="prepare"></a>Preparare l'ambiente

1. Installare Azure AD Connect, selezionare **la modalità di gestione temporanea**e deselezionare l'opzione **Avvia sincronizzazione** nell'ultima pagina della procedura guidata installazione. Questa modalità consente di eseguire manualmente il motore di sincronizzazione.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Segno di disattivare/effettuare l'accesso e dal menu di avvio seleziona **Servizio di sincronizzazione**.

#### <a name="import-and-synchronize"></a>Importare e sincronizzare

1. Selezionare **i connettori**e selezionare il primo connettore con il tipo di **Servizi di dominio Active Directory**. Fare clic su **Esegui**, selezionare **importazione completa**, quindi **su OK**. Eseguire la procedura seguente per tutti i connettori di questo tipo.
2. Selezionare il connettore con il tipo di **Azure Active Directory (Microsoft)**. Fare clic su **Esegui**, selezionare **importazione completa**, quindi **su OK**.
3. Assicurarsi che la scheda connettori è ancora selezionata. Per ogni connettore con il tipo di **Servizi di dominio Active Directory**, fare clic su **Esegui**, selezionare **Sincronizzazione Delta**, quindi **su OK**.
4. Selezionare il connettore con il tipo di **Azure Active Directory (Microsoft)**. Fare clic su **Esegui**, selezionare **Sincronizzazione Delta**, quindi **su OK**.

Ora in più fasi esportazione assume la forma di Azure Active Directory e locale di Active Directory (se si sta utilizzando una distribuzione ibrida di Exchange). Passaggi successivi consentono di controllare quali consiste nel modificare prima di iniziare effettivamente l'esportazione alle directory.

#### <a name="verify"></a>Verificare

1. Avviare un prompt cmd e passare alla sezione`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Correre:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Nel servizio di sincronizzazione, è possibile trovare il nome del connettore. Ha un nome simile a "contoso.com-AAD" per Azure Active Directory.
3. Correre:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Si dispone di un file in % temp % denominato export.csv che possono essere esaminate in Microsoft Excel. Il file contiene tutte le modifiche che si desidera esportare.
5. Apportare le modifiche necessarie per la configurazione o di dati ed eseguire questa procedura nuovamente (importazione e Sincronizza e verifica) fino a quando non sono previste modifiche che si desidera esportare.

**Informazioni sul file export.csv**

La maggior parte del file è di facile comprensione. Alcune abbreviazioni per comprendere il contenuto:

- OMODT-tipo di modifica oggetto. Indica se l'operazione di livello di un oggetto è un aggiunta, aggiornamento o eliminazione.
- AMODT-tipo di modifica degli attributi. Indica se l'operazione di un livello di attributo è un'aggiunta, aggiornamento o eliminazione.

Se il valore dell'attributo multivalore, non ogni modifica viene visualizzato. Solo il numero di valori aggiunti o rimossi è visibile.

#### <a name="switch-active-server"></a>ASP parametro

1. Sul server attualmente attivo, disattivare il server (DirSync/FIM/Azure Active Sync) in modo che non è esportato di Azure Active Directory o impostarlo in modalità (Azure AD Connect) di gestione temporanea.
2. Eseguire l'installazione guidata nel server in **modalità di gestione temporanea** e disattivare **la modalità di gestione temporanea**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Ripristino di emergenza
Parte della progettazione implementazione consiste nel piano sulle operazioni da eseguire nel caso di emergenza nel punto in cui si perde il server di sincronizzazione. Sono disponibili diversi modelli da utilizzare e quella da utilizzare dipende da diversi fattori tra cui:

-   Che cos'è la tolleranza della non è possibile apportare le modifiche agli oggetti in Azure Active Directory durante il periodo di inattività?
-   Se si usa la sincronizzazione delle password, gli utenti intendesse gli oggetti devono utilizzare la vecchia password in Azure Active Directory in caso di cambiarla locale?
-   Si dispone di una dipendenza sulle operazioni in tempo reale, ad esempio writeback password?

A seconda delle risposte a queste domande e i criteri dell'organizzazione, è possibile implementare uno dei seguenti strategie:

-   Ricreare quando necessario.
-   Disporre di un server standby riserva, noto come **la modalità di gestione temporanea**.
-   Utilizzare macchine virtuali.

Se non si utilizza il database di SQL Express incorporati, si deve inoltre esaminare la sezione [Disponibilità SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Rigenerare quando necessario
Una strategia valida consiste nel pianificare la ricostruzione server quando necessario. In genere, installare il motore di sincronizzazione ed eseguire che l'importazione iniziale e sincronizzazione può essere completate entro alcune ore. Se esiste un server di riserva non è disponibile, è possibile usare temporaneamente un controller di dominio per ospitare il motore di sincronizzazione.

Il server di motore di sincronizzazione non archiviare uno stato sugli oggetti in modo che il database può ricreato dai dati di Active Directory e Azure Active Directory. L'attributo **sourceAnchor** viene utilizzato per unire gli oggetti da locale e nel cloud. Se si rigenera il server con esistente oggetti locale e nel cloud, quindi il motore di sincronizzazione corrisponde a tali oggetti insieme nuovamente su reinstallazione. I cambiamenti che è necessario del documento e salvare sono le modifiche apportate al server, ad esempio le regole di filtro e la sincronizzazione della configurazione. Prima di iniziare la sincronizzazione, è necessario riapplicare queste configurazioni personalizzate.

### <a name="have-a-spare-standby-server---staging-mode"></a>Disporre di un server standby riserva - modalità di gestione temporanea
Se si dispone di un ambiente più complesso, è consigliabile fare in modo che uno o più server standby. Durante l'installazione, è possibile abilitare un server in **modalità di gestione temporanea**.

Per ulteriori informazioni, vedere [modalità di gestione temporanea](#staging-mode).

### <a name="use-virtual-machines"></a>Utilizzare macchine virtuali
Un metodo comune e supportato consiste nell'eseguire il motore di sincronizzazione in un computer virtuale. Nel caso in cui l'host dispone di un problema, è possibile eseguire la migrazione di immagine con il server di motore di sincronizzazione in un altro server.

### <a name="sql-high-availability"></a>Disponibilità SQL
Se non si utilizza il SQL Server Express con Azure AD Connect, devono ritenute disponibilità per SQL Server. Soluzione solo disponibilità supportata è SQL cluster. Soluzioni non supportate includono che rispecchiano e sempre attiva.

## <a name="next-steps"></a>Passaggi successivi

**Argomenti della panoramica**  

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)  
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)  
