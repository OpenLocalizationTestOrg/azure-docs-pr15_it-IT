<properties 
    pageTitle="Integrazione le identità locali con Azure Active Directory."
    description="Si tratta di Azure AD Connect che descrive che cos'è e il motivo per cui si vuole usare."
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

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Autenticazione a più fattori predefiniti in App personalizzate (SDK)

> [AZURE.IMPORTANT]  Se si desidera scaricare SDK, sarà necessario creare un Provider di autenticazione a più fattori Azure anche se si dispone di Azure MFA, AAD Premium o EMS licenze.  Se si crea un Provider di autenticazione a più fattori Azure a questo scopo e si dispongono già di licenze, è necessario creare il Provider con il modello **Per ogni utente abilitato** e collegare il Provider di directory che contiene le licenze MFA Azure, Azure Active Directory Premium o EMS.  In questo modo che non siano effettuata a meno che non si dispone di più utenti univoci utilizzando SDK rispetto al numero di licenze che si è proprietari.

Azure a più fattori autenticazione Software Development Kit (SDK) consente di creare telefonata e testo verifica messaggio direttamente in ai processi di accesso o transazione delle applicazioni nel tenant di Azure Active Directory.

SDK di autenticazione a più fattori è disponibile per c#, Visual Basic (.NET), Java, Perl, PHP e trascrizione. SDK fornisce wrapper di autenticazione a più fattori. Include tutte le informazioni che necessarie per scrivere il codice, inclusi file di codice sorgente commenti, file di esempio e un file Leggimi dettagliato. Ogni SDK include anche un certificato e chiave privata per crittografare le transazioni univoco per il Provider di autenticazione a più fattori. Come si dispone di un provider di servizi, è possibile scaricare SDK in tutte le lingue e formati in base alle esigenze.

La struttura delle API in SDK autenticazione a più fattori è molto semplice. Si esegue una singola funzione chiamata a un'API con i parametri di opzione a più fattori, ad esempio la modalità di verifica e dati utente, ad esempio il numero di telefono da chiamare o il PIN per convalidare. API di convertono la chiamata di funzione in richieste di servizi web per il servizio di autenticazione a più fattori Azure basato sul cloud. Tutte le chiamate devono includere un riferimento al certificato privato è incluso in ogni SDK.

Poiché le API non si hanno accesso a utenti registrati in Azure Active Directory, è necessario fornire le informazioni utente, ad esempio i numeri di telefono e codici PIN, in un file o database. Inoltre, le API non forniscono funzionalità di gestione di registrazione o utente, in modo che è necessario creare questi processi nell'applicazione.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Scaricare l'autenticazione a più fattori Azure SDK

Download di Azure a più fattori SDK richiede un [Provider di autenticazione a più fattori Azure](multi-factor-authentication-get-started-auth-provider.md).  È necessario un abbonamento completo Azure, anche se appartengono MFA Azure, Azure Active Directory Premium o Enterprise mobilità Suite licenze.  Per scaricare questo SDK, è necessario passare al portale di gestione a più fattori gestendo il Provider di autenticazione a più fattori direttamente o facendo clic sul collegamento **"Accedere al portale di"** nella pagina Impostazioni servizio MFA.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Per scaricare l'autenticazione a più fattori di Azure SDK dal portale di Azure


1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare Active Directory.
3. Nella pagina Active Directory, fare clic su **Provider di autenticazione a più fattori** nella parte superiore
4. Nella parte inferiore fare clic su **Gestisci**
5. Viene visualizzata una nuova pagina.  A sinistra, nella parte inferiore, fare clic su SDK.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Selezionare la lingua desiderata e fare clic su uno collegamenti per il download associato.
7. Salvare il download.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Per scaricare il SDK di autenticazione a più fattori Azure tramite le impostazioni del servizio


1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare Active Directory.
3. Fare doppio clic sull'istanza di Azure Active Directory.
4. Nella parte superiore fare clic su **Configura**
5. Selezionare **le impostazioni del servizio di gestione**autenticazione a più fattori
![Download](./media/multi-factor-authentication-sdk/download2.png)
6. Nella pagina Impostazioni servizi, nella parte inferiore dello schermo fare clic su **Vai al portale**.
![Download](./media/multi-factor-authentication-sdk/download3a.png)
7. Viene visualizzata una nuova pagina.  A sinistra, nella parte inferiore, fare clic su SDK.
8. Selezionare la lingua desiderata e fare clic su uno collegamenti per il download associato.
9. Salvare il download.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Contenuto dell'autenticazione a più fattori Azure SDK
All'interno di SDK sono disponibili gli elementi seguenti:

- **File Leggimi**. In questo articolo viene spiegato come utilizzare le API di autenticazione a più fattori in un'applicazione di nuova o esistente.
- **File di origine** per l'autenticazione a più fattori
- **Certificato client** che consente di comunicare con il servizio di autenticazione a più fattori
- **Chiave privata** per il certificato
- **Chiamare i risultati.** Elenco dei codici di risultato di chiamata. Per aprire il file, usare un'applicazione con la formattazione, ad esempio WordPad del testo. Utilizzare i codici di risultato chiamata per testare e risolvere i problemi di implementazione di autenticazione a più fattori dell'applicazione. Non sono i codici di stato di autenticazione.
- **Esempi.** Codice di esempio per l'implementazione di lavoro base di autenticazione a più fattori.


>[AZURE.WARNING]Il certificato client è un certificato privato univoco generati appositamente per te. Condividere o non perdere il file. È il tasto per garantire la sicurezza delle comunicazioni con il servizio di autenticazione a più fattori.

## <a name="code-sample-standard-mode-phone-verification"></a>Esempio: Verifica telefonica modalità Standard

In questo esempio viene illustrato come utilizzare le API in SDK autenticazione a più fattori Azure per aggiungere la verifica di chiamata VoIP modalità standard per l'applicazione. Modalità standard è una chiamata telefonica che l'utente risponde a premendo il tasto #.

Questo esempio si utilizza c# .NET 2.0 a più fattori autenticazione SDK in un'applicazione di ASP.NET base con una logica lato server c#, ma il processo è molto simile per semplici implementazioni in altre lingue. Perché il SDK include file di origine, non eseguibili, la creazione dei file e farvi riferimento oppure includerli direttamente nell'applicazione.

>[AZURE.NOTE]Durante l'implementazione di autenticazione a più fattori, usare altri fattori come verifica secondaria o terzo livello per integrare il metodo di autenticazione principale. Questi metodi non sono progettati per essere utilizzati come metodi di autenticazione principale.

### <a name="code-sample-overview"></a>Panoramica di esempio di codice
Questo esempio di codice per un'applicazione di demo web molto semplice utilizza una chiamata con una risposta chiave # per completare l'autenticazione utente. Questo fattore telefonata è definito in autenticazione a più fattori modalità standard.

Il codice sul lato client non include tutti gli elementi specifici di autenticazione a più fattori. Poiché i fattori di autenticazione aggiuntive sono indipendenti di autenticazione principale, è possibile aggiungere senza modificare l'interfaccia di accesso esistente. API in SDK a più fattori consentono di personalizzare l'esperienza dell'utente, ma non è necessario apportare modifiche a tutte.

Il codice sul lato server aggiunge l'autenticazione in modalità standard nel passaggio 2. Viene creato un oggetto PfAuthParams con i parametri necessari per la verifica in modalità standard: nome utente, numero e la modalità e il percorso per il certificato di client (CertFilePath), è necessario in ogni chiamata di telefono. Per una dimostrazione di tutti i parametri PfAuthParams, vedere il file di esempio in SDK.

Successivamente, l'oggetto PfAuthParams il codice passato alla funzione pf_authenticate(). Il valore restituito indica l'esito positivo o negativo dell'autenticazione. I parametri out callStatus ed errorID contengono le informazioni sul risultato chiamata aggiuntiva. I codici di risultato chiamata vengono riportati nel file di risultati di chiamata in SDK.

Questa implementazione minima può essere scritte in poche righe. Tuttavia, nel codice di produzione da includere la gestione degli errori più sofisticati, database aggiuntivi codice e un'esperienza utente avanzata.

### <a name="web-client-code"></a>Codice di Client Web

Di seguito è codice client web per una pagina di prova.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Codice lato server

Nel codice sul lato server l'autenticazione a più fattori è configurato ed eseguire nel passaggio 2. Modalità standard (MODE_STANDARD) è una chiamata telefonica a cui si risponde premendo il tasto #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
