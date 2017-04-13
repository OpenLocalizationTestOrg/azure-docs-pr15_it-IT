<properties
    pageTitle="Connettersi al Database SQL con SQL Server Management Studio in Azure RemoteApp | Microsoft Azure"
    description="Utilizzare questa esercitazione per informazioni su come usare SQL Server Management Studio in Azure RemoteApp per protezione e le prestazioni quando ci si connette al Database SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>In SQL Server Management Studio RemoteApp di Azure consente di connettersi al Database SQL

## <a name="introduction"></a>Introduzione  
In questa esercitazione viene illustrato come usare SQL Server Management Studio (Express) in Azure RemoteApp per connettersi al Database SQL. Viene illustrato il processo di installazione di SQL Server Management Studio in Azure RemoteApp, vengono illustrati i vantaggi e Mostra le funzionalità di sicurezza che è possibile usare in Azure Active Directory.

**Tempo stimato per completare:** 45 minuti

## <a name="ssms-in-azure-remoteapp"></a>SQL Server Management Studio in RemoteApp Azure

Azure RemoteApp è un servizio di licenze in Azure che offre le applicazioni. Sono disponibili ulteriori informazioni qui: [Novità RemoteApp?](../remoteapp/remoteapp-whatis.md)

SQL Server Management Studio in esecuzione in Azure RemoteApp fornisce gli stessi risultati di in esecuzione in locale di SQL Server Management Studio.

![Schermata che mostra SQL Server Management Studio in esecuzione in Azure RemoteApp][1]



## <a name="benefits"></a>Vantaggi

Esistono diversi vantaggi all'uso di SQL Server Management Studio in Azure RemoteApp, tra cui:

- Porta 1433 in SQL Azure non è necessario essere esposto esternamente (all'esterno di Azure).
- Non è necessario mantenere aggiunta e rimozione di indirizzi IP nel firewall Azure SQL Server.
- Tutte le connessioni RemoteApp di Azure verificano su HTTPS tramite la porta 443 crittografati protocollo Desktop remoto
- Si tratta di più utenti, possono scalare.
- Esiste un aumento delle prestazioni di evitare di SQL Server Management Studio nella stessa regione il Database di SQL.
- È possibile controllare l'utilizzo di Azure RemoteApp con la versione Premium di Azure Active Directory che contiene dei report di attività utente.
- È possibile abilitare l'autenticazione a più fattori (MFA).
- Accesso SQL Server Management Studio in un punto qualsiasi quando si utilizza uno dei client di Azure RemoteApp supportati che include iOS, Android, Mac, Windows Phone e Windows PC.


## <a name="create-the-azure-remoteapp-collection"></a>Creare la raccolta RemoteApp di Azure

Ecco la procedura per creare la raccolta RemoteApp di Azure con SQL Server Management Studio:


### <a name="1-create-a-new-windows-vm-from-image"></a>1. creare una nuova macchina virtuale Windows dall'immagine
Usare l'immagine "Windows Server Remote Desktop sessione Host Windows Server 2012 R2" dalla raccolta per semplificare la nuova macchina virtuale.


### <a name="2-install-ssms-from-sql-express"></a>2. installare SQL Server Management Studio da SQL Express

Passare nella nuova macchina virtuale e passare alla pagina di download: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

C'è un'opzione per scaricare solo SQL Server Management Studio. Dopo il download, eseguire il programma di installazione di SQL Server Management Studio nella directory di installazione.

Inoltre, è necessario installare SQL Server 2014 Service Pack 1. È possibile scaricare qui: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 include funzionalità essenziali per l'utilizzo di Database SQL Azure.


### <a name="3-run-validate-script-and-sysprep"></a>3. eseguire script di convalida e Sysprep

Sul desktop di macchina virtuale è chiamato convalida uno script di PowerShell. Eseguire questo strumento facendo doppio clic su. Verrà verificare che la macchina virtuale sia pronta per essere usato per l'hosting remoto delle applicazioni. Una volta completata la verifica, verrà chiesto per eseguirlo - scelta per eseguirlo.

Dopo avere completato sysprep, viene chiuso la macchina virtuale.

Per ulteriori informazioni sulla creazione di un'immagine di Azure RemoteApp, vedere: [come creare un'immagine di modello RemoteApp in Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. acquisire l'immagine

Quando la macchina virtuale ha smesso di esecuzione, individuarlo nel portale di corrente e acquisita.

Per ulteriori informazioni sull'acquisizione di un'immagine, vedere [acquisire un'immagine di una macchina virtuale di Windows Azure creata con il modello di distribuzione classica](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. aggiungere le immagini dei modelli di RemoteApp Azure

Nella sezione Azure RemoteApp del portale di corrente, fare clic sulla scheda le immagini dei modelli e fare clic su Aggiungi. Nella casella popup, selezionare "Importa un'immagine dalla libreria dei macchine virtuali" e quindi scegliere l'immagine appena creata.



### <a name="6-create-cloud-collection"></a>6. creare cloud insieme

Nel portale corrente, creare una nuova raccolta Cloud RemoteApp di Azure. Scegliere immagine del modello appena importata con SQL Server Management Studio in cui è installato.

![Crea nuova raccolta cloud][2]


### <a name="7-publish-ssms"></a>7. pubblicare SQL Server Management Studio

Nella pubblicazione scheda della raccolta cloud nuovo, seleziona pubblica un'applicazione dal Menu Start e quindi scegliere SQL Server Management Studio dall'elenco.

![Pubblicare App][5]

### <a name="8-add-users"></a>8. aggiungere utenti

Nella scheda l'accesso degli utenti è possibile selezionare gli utenti che avranno accesso alla raccolta RemoteApp di Azure che include solo SQL Server Management Studio.

![Aggiunta utente][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. installare l'applicazione client RemoteApp di Azure

È possibile scaricare e installare un client di Azure RemoteApp: [Download | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurare il Server SQL Azure

La configurazione sola necessaria consiste nel verificare che i servizi di Windows Azure sia abilitato per il firewall. Se si utilizza questa soluzione, non è necessario aggiungere gli indirizzi IP per aprire il firewall. Il traffico di rete che può essere SQL Server è stata inviata da altri servizi di Azure.


![Consenti Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Autenticazione a più fattori (MFA)

MFA è possibile abilitare specificamente per questa applicazione. Passare alla scheda applicazioni di Azure Active Directory. Si noterà una voce per RemoteApp di Microsoft Azure. Se si sceglie l'applicazione e quindi configurare, si verrà visualizzata la pagina riportata di seguito nel punto in cui è possibile abilitare MFA dell'applicazione.

![Attivare MFA][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Controllare l'attività con Azure Active Directory Premium

Se non si dispone di Azure Active Directory Premium, è necessario attivare nella sezione licenze della directory. Con Premium abilitato, è possibile assegnare gli utenti a livello Premium.

Quando si passa a un utente in Azure Active Directory, quindi passare alla scheda attività per visualizzare le informazioni di accesso a RemoteApp di Azure.



## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la procedura sopra indicata, sarà possibile eseguire il client di Azure RemoteApp log-in e con un utente assegnato. Verrà visualizzata con SQL Server Management Studio come una delle applicazioni ed è possibile eseguire come se sono stati installato nel computer con l'accesso al Server di SQL Azure.

Per ulteriori informazioni su come effettuare la connessione al Database SQL, vedere [Connetti a Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md).


È tutto ciò che per il momento. Buona!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
