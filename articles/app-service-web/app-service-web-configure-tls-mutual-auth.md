<properties 
    pageTitle="Come configurare l'autenticazione reciproca TLS per Web App" 
    description="Informazioni su come configurare un'applicazione web per l'utilizzo di autenticazione certificato client in TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Come configurare l'autenticazione reciproca TLS per Web App

## <a name="overview"></a>Panoramica ##
È possibile limitare l'accesso all'app web Azure abilitando diversi tipi di autenticazione per renderla. Per effettuare questa operazione è possibile eseguire l'autenticazione tramite un certificato client quando la richiesta sia su SSL/TLS. Questo meccanismo è denominato autenticazione reciproca TLS o certificato di autenticazione e in questo articolo verranno illustrano come configurare un'applicazione web per l'utilizzo di autenticazione certificato client.

> **Nota:** Se si accede al sito su HTTP e non HTTPS, non si riceveranno i certificati client. Pertanto se l'applicazione richiede certificati client è consigliabile non consentire le richieste per l'applicazione su HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurare Web App per l'autenticazione certificato Client ##
Per configurare un'applicazione web per richiedere certificati client è necessario aggiungere l'impostazione di siti clientCertEnabled per un'app web e impostato su true. Questa impostazione non è attualmente disponibile tramite l'esperienza di gestione del portale e API REST dovrà essere usata per eseguire questa operazione.

È possibile utilizzare lo [strumento ARMClient](https://github.com/projectkudu/ARMClient) per rendere più semplice creare la chiamata API REST. Dopo l'accesso con lo strumento sarà necessario eseguire il comando seguente:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
tutto {} sostituendo con informazioni per un'app web e la creazione di un file denominato contenuto enableclientcert.json con JSON seguenti:

> {"posizione": "Personale App percorso Web"   
>   "proprietà": {  
>     "clientCertEnabled": true}}  

Assicurarsi di modificare il valore di "posizione" in qualsiasi percorso un'app web si trova ad esempio North Central US o ovest US ecc.

> **Nota:** Se si esegue ARMClient da Powershell, sarà necessario interrompere l'esecuzione di @ simbolo per il file JSON con un segno di graduazione indietro '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Accedere al certificato Client da un'applicazione Web ##
Se si utilizza ASP.NET e configura l'app per l'utilizzo di autenticazione certificato client, il certificato sarà disponibile tramite la proprietà **HttpRequest.ClientCertificate** . Per altri stack di applicazioni, il certificato client sarà disponibile nell'applicazione di un valore di base 64 codificato nell'intestazione della richiesta "X-ARR-ClientCert". L'applicazione è possibile creare un certificato da questo valore e quindi utilizzarla per scopi di autenticazione e l'autorizzazione dell'applicazione.

## <a name="special-considerations-for-certificate-validation"></a>Considerazioni speciali per la convalida dei certificati ##
Il certificato client che viene inviato all'applicazione non eseguire alcuna operazione di convalida della piattaforma Azure Web Apps. Convalidare il certificato è responsabilità dell'applicazione web. Ecco il codice ASP.NET di esempio che convalida le proprietà dei certificati per l'autenticazione.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
