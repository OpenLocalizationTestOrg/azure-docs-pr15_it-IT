<properties
    pageTitle="Azure Active Directory per servizio Auth utilizzando OAuth2.0 | Microsoft Azure"
    description="In questo articolo viene descritto come utilizzare messaggi HTTP per implementare l'autenticazione al servizio tramite il flusso di concessione di OAuth2.0 client credenziali."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="service-to-service-calls-using-client-credentials"></a>Servizio alle chiamate del servizio mediante credenziali

Il flusso di OAuth 2.0 Client credenziali Grant consente a un servizio web ( *client riservate*) usare le proprie credenziali per l'autenticazione durante la chiamata a un altro servizio web, invece di rappresentazione di un utente. In questo scenario, il client è in genere a un servizio web intermedio, un servizio daemon o un sito web.

## <a name="client-credentials-grant-flow-diagram"></a>Credenziali client concedono diagramma di flusso

Nel diagramma seguente viene descritta come le credenziali del client concedono works del flusso di Azure Active Directory (Azure Active Directory).

![OAuth2.0 Client credenziali Grant flusso](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L'applicazione client autentica all'endpoint del rilascio token Azure Active Directory e le richieste di token di accesso.
2. L'endpoint del rilascio token di Azure Active Directory problemi token di accesso.
3. Il token di accesso viene utilizzato per eseguire l'autenticazione per la risorsa protetta.
4. Dati dalla risorsa protetta vengono restituiti all'applicazione web.

## <a name="register-the-services-in-azure-ad"></a>Registrare i servizi in Azure Active Directory

Registrare il servizio di chiamata e il servizio di ricezione di Azure Active Directory (Azure Active Directory). Per informazioni dettagliate, vedere [aggiunta, aggiornamento e la rimozione di un'App](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Richiedere un Token di accesso

Per richiedere un token di accesso, utilizzare un HTTP POST al tenant specifici endpoint di Azure Active Directory.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Richiesta di token di accesso al servizio

Una richiesta di token di accesso al servizio contiene i parametri seguenti.

| Parametro | | Descrizione |
|-----------|------|------------|
| response_type | Obbligatorio | Specifica il tipo di risposta richiesta. In un flusso di concessione di credenziali Client, il valore deve essere **client_credentials**.|
| client_id | Obbligatorio | Specifica l'id client di Azure Active Directory del servizio web chiamante. Per trovare ID client dell'applicazione chiamante, nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura**.|
| client_secret | Obbligatorio |  Immettere una chiave registrata per il servizio web chiamante Azure Active Directory. Per creare un tasto, nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura**. |
| risorsa | Obbligatorio | Immettere l'URI ID applicazione del servizio web di destinazione. Per trovare l'URI ID App, nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura**. |

## <a name="example"></a>Esempio

Il POST HTTP seguente richiede un token di accesso per il servizio web https://service.contoso.com/. Il `client_id` identifica il servizio web che richiede il token di accesso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Accesso al servizio Token risposta

Una risposta di successo contiene una risposta JSON OAuth 2.0 con i parametri seguenti.

| Parametro   | Descrizione |
|-------------|-------------|
|Access |Token di accesso. Il servizio web chiamante possa utilizzare questo token per eseguire l'autenticazione al servizio web ricezione. |
|access_type  | Indica il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è **titolare**. Per ulteriori informazioni sui token del titolare, vedere il [OAuth 2.0 autorizzazione Framework: utilizzo di Token del titolare (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | Per quanto tempo il token di accesso è valido (secondi).|
|expires_on   |La data di scadenza token di accesso. La data viene rappresentata come il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene utilizzato per determinare la durata del token memorizzati nella cache. |
|risorsa     | App ID URI del servizio web di destinazione. |

## <a name="example"></a>Esempio

Nell'esempio seguente mostra una risposta di successo a una richiesta di un token di accesso a un servizio web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Vedere anche

* [OAuth 2.0 in Azure Active Directory](active-directory-protocols-oauth-code.md)
