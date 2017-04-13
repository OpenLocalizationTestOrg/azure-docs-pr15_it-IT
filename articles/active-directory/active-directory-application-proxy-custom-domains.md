<properties
    pageTitle="Utilizzo di domini personalizzati in Azure Active Directory applicazione Proxy | Microsoft Azure"
    description="Viene illustrato come funziona con domini personalizzati in Azure Active Directory applicazione Proxy."
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
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Utilizzo di domini personalizzati in Proxy di applicazione Azure Active Directory

Utilizzo di un dominio predefinito consente di impostare lo stesso URL come URL interne ed esterne per l'accesso all'applicazione in modo che gli utenti abbiano solo un URL da ricordare accedere all'app, indipendentemente da dove vengono accesso da. Questo è anche possibile creare un singolo collegamento nel Pannello di accesso per l'applicazione. Se si usa il dominio predefinito fornito da Proxy di applicazione Azure Active Directory, non esiste nessuna configurazione aggiuntiva, che è necessario attivare il dominio. Nel caso in cui si usa un dominio personalizzato, esistono alcuni aspetti che occorre fare per assicurarsi che il Proxy di applicazione riconosce il dominio e convalida i certificati.

## <a name="selecting-your-custom-domain"></a>Selezione di un dominio personalizzato

1. Pubblicare l'applicazione seguendo le istruzioni nelle [applicazioni di pubblicazione con Proxy dell'applicazione](active-directory-application-proxy-publish.md).
2. Dopo l'applicazione viene visualizzata nell'elenco delle applicazioni, selezionarla e fare clic su **Configura**.
3. Nella casella **URL esterno**, immettere il proprio dominio personalizzato.
4. Se l'URL esterno è https, verrà richiesto di caricare un certificato in modo che Azure convalidare l'URL dell'applicazione. È anche possibile caricare un certificato con caratteri jolly che corrisponde all'URL esterno dell'applicazione. Questo dominio deve essere presenti nell'elenco del [Azure verificato domini](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure deve disporre di un certificato per l'URL del dominio dell'applicazione o un certificato con caratteri jolly che corrisponde all'URL esterno per l'applicazione.
5. Assicurarsi di aggiungere un record DNS che indirizza l'URL interno all'applicazione che consente di ottenere lo stesso URL per l'accesso interna ed esterna e un singolo collegamento all'applicazione nell'elenco delle applicazioni dell'utente.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Domande frequenti sull'uso dei domini personalizzati

D: è possibile selezionare un certificato già caricato senza caricarlo nuovamente?  
R: in precedenza certificati caricati vengono automaticamente associati a un'applicazione e c'è un certificato corrispondente il nome dell'applicazione host.  

Q: come aggiungere un certificato e qual è il formato a necessario caricare il file esportato?  
R: il certificato deve essere caricato dalla pagina di configurazione dell'applicazione. Il certificato deve essere un file PFX.  

D: è possibile essere utilizzati certificati ECC?  
R: non esiste alcun limite espliciti metodi di firma.  

D: è possibile essere utilizzati certificati SAN?  
R: Sì.  

D: è possibile essere usati con caratteri jolly certificati?  
R: Sì.  

D: è possibile utilizzare un certificato diverso in ogni applicazione?  
R: Sì, a meno che le due applicazioni condividano lo stesso host esterno.  

D: se è possibile registrare un nuovo dominio, è possibile usare tale dominio?  
R: Sì, l'elenco dei domini alimentazione dall'elenco di dominio verificato del tenant.  

D: cosa succede quando scade un certificato?  
R: verrà visualizzato un avviso nella sezione certificato nella pagina di configurazione dell'applicazione. Quando si tenta di accedere all'applicazione, viene visualizzato un avviso di sicurezza.  

D: cosa deve fare se desidera sostituire un certificato per un determinato app?  
R: caricare un nuovo certificato dalla pagina di configurazione dell'applicazione.  

D: è possibile eliminare un certificato e sostituirlo?  
R: quando si carica un nuovo certificato, se il certificato precedente non è in uso da un'altra applicazione, esso verrà eliminato automaticamente.  

D: cosa succede se un certificato è stato revocato?  
R: revoca controlli non vengono eseguiti i certificati. Quando si tenta di accedere all'applicazione, a seconda del browser, può essere visualizzato un avviso di sicurezza.  

D: è possibile usare un certificato autofirmato?  
R: Sì, sono consentiti certificati. Si noti che se si usa un'autorità di certificazione privato, CDP (punto di distribuzione punto di revoche di certificati) per il certificato deve essere pubblico.  

D: è presente una posizione per visualizzare tutti i certificati per tenant?  
R: non è supportato nella versione corrente.  


## <a name="see-also"></a>Vedere anche

- [Pubblicare applicazioni con Proxy dell'applicazione](active-directory-application-proxy-publish.md)
- [Attivare single sign-on](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Aggiungere il proprio nome di dominio personalizzato per Azure Active Directory](active-directory-add-domain.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
