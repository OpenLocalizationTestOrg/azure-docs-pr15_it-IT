<properties
   pageTitle="Generico connettore SQL passo-passo | Microsoft Azure"
   description="In questo articolo si verifica tramite un sistema HR semplice dettagliato usando il connettore SQL generico."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>Connettore SQL generico dettagliate
In questo argomento è una Guida dettagliata. Crea un database di esempio semplice HR e usarlo per l'importazione di alcuni utenti e appartenenza al gruppo.

## <a name="prepare-the-sample-database"></a>Preparare il database di esempio
In un server che esegue SQL Server, eseguire lo script SQL [dell'Appendice](#appendix-a). Questo script crea un database di esempio con il nome GSQLDEMO. Il modello a oggetti di database creato l'aspetto dell'immagine:  
![Modello a oggetti](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Anche creare un utente che si desidera utilizzare per connettersi al database. In questa procedura dettagliata, l'utente è chiamato FABRIKAM\SQLUser e si trova nel dominio.

## <a name="create-the-odbc-connection-file"></a>Creare il file di connessione ODBC
Connettore SQL generico utilizza ODBC per connettersi al server remoto. Prima di tutto è necessario creare un file con le informazioni di connessione ODBC.

1. Avviare l'utilità di gestione ODBC nel server:  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Selezionare la scheda **File DSN**. Fare clic su **Aggiungi**.
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Funzionamento dei driver perfezionarlo, pertanto selezionare l'immagine e fare clic su **Avanti >**.  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Assegnare un nome, ad esempio **GenericSQL**al file.  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Fare clic su **Fine**.  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Ora di configurare la connessione. Assegnare una buona descrizione dell'origine dati e specificare il nome del server che esegue SQL Server.  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Selezionare la modalità di autenticazione con SQL. In questo caso, viene utilizzato l'autenticazione di Windows.  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Specificare il nome del database di esempio, **GSQLDEMO**.  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Mantieni tutti gli elementi predefiniti in questa schermata. Fare clic su **Fine**.  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Per verificare che il funzionamento come previsto, fare clic su **Verifica origine dati**.  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Verificare che il test ha esito positivo.  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. File di configurazione ODBC dovrebbe ora essere visibile nel File DSN.  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

È ora disponibile il file è necessario e iniziare a creare il connettore.

## <a name="create-the-generic-sql-connector"></a>Creare il connettore SQL generico

1. In UI gestore servizio di sincronizzazione, selezionare **i connettori** e **creare**. Selezionare **SQL generico (Microsoft)** e assegnare un nome descrittivo.  
![Connettore1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Individuare il file DSN creato nella sezione precedente e caricarlo nel server. Specificare le credenziali per connettersi al database.  
![Connettore2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. In questa procedura dettagliata abbiamo sono rendendo più semplice per noi e segnala che sono disponibili due tipi di oggetto, **utenti** e **gruppi**.
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Per trovare gli attributi, ci teniamo il connettore per rilevare tali attributi esaminando la stessa tabella. Dal momento che **gli utenti** è una parola riservata in SQL, è necessario fornire racchiusa tra parentesi quadre [].  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Ora per definire gli attributi di ancoraggio e il nome distinto. Per **gli utenti**, è utilizzare la combinazione dei due attributi nome utente e campo ID dipendente. Per **gruppo**serve nomegruppo (non realistici in reali, ma per questa procedura dettagliata funziona).
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Non tutti i tipi di attributo possono essere rilevati in un database SQL. Il tipo di riferimento dell'attributo non può in particolare. Per il tipo di oggetto di gruppo, è necessario modificare i valori OwnerID e MemberID per fare riferimento.  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Gli attributi che è selezionati come attributi di riferimento nel passaggio precedente richiedono il tipo di oggetto questi valori sono un riferimento a. In questo caso, il tipo di oggetto utente.  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Nella pagina parametri globali selezionare **filigrana** come strategia di delta. Digitare anche il formato di data/ora **gg / MM / aaaa hh**.
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Nella pagina **Configura partizioni e gerarchie** selezionare entrambi i tipi di oggetto.
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. **Selezionare i tipi di oggetto** e **Selezionare gli attributi**, selezionare tipi di oggetto e tutti gli attributi. Nella pagina **Configura gli ancoraggi** fare clic su **Fine**.

## <a name="create-run-profiles"></a>Creare profili di esecuzione

1. In UI gestore servizio di sincronizzazione, selezionare i **connettori**e **Configurare profili eseguire**. Fare clic su **nuovo profilo**. Iniziamo con **Importazione completa**.  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Selezionare il tipo di **Importazione completa (solo passaggio)**.  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Selezionare la partizione **oggetto = utente**.  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Selezionare **tabella** e digitare **[utente]**. Scorrere fino alla sezione tipo di oggetto multivalore e immettere i dati come illustrato nella figura seguente. Selezionare **Fine** per salvare il passaggio.
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Selezionare **nuova azione**. Questa volta, selezionare **oggetto = gruppo**. Nell'ultima pagina, usare la configurazione come illustrato nella figura seguente. Fare clic su **Fine**.  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Facoltativo: Se si desidera, è possibile configurare ulteriori profili di esecuzione. Per questa procedura dettagliata, viene utilizzato solo l'importazione completa.
7. Fare clic su **OK** per completare la modifica di profili di esecuzione.

## <a name="add-some-test-data-and-test-the-import"></a>Aggiungere alcuni dati e testare l'importazione
Compilare alcuni dati di test nel database di esempio. Quando si è pronti, selezionare **Esegui** e **importazione completa**.

Ecco un utente con due numeri di telefono e un gruppo con alcuni membri.  
![CS1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![CS2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>Appendice
**Script SQL per creare il database di esempio**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
