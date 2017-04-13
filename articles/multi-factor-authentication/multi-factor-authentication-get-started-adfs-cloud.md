<properties
    pageTitle="Proteggere le risorse cloud con Azure MFA e ADFS"
    description="Questa è la pagina di autenticazione a più fattori Azure che descrive come iniziare a utilizzare MFA Azure e ADFS nel cloud."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protezione delle risorse cloud con autenticazione a più fattori Azure e ADFS

Se l'organizzazione è federato con Azure Active Directory, utilizzare l'autenticazione a più fattori Azure o Active Directory Federation Services per proteggere le risorse utilizzate dalla Azure Active Directory. Utilizzare le procedure seguenti per proteggere le risorse di Azure Active Directory con autenticazione a più fattori Azure o Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteggere le risorse di Azure Active Directory tramite ADFS

Per proteggere la risorsa cloud, abilitare un account per gli utenti e quindi impostare una regola di reclami. Eseguire questa procedura per eseguire i passaggi seguenti:

1. Usare i passaggi descritti in [autenticazione a più fattori Turn-on per gli utenti](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) per abilitare un account.
2. Avviare AD FS Management console.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Passare **all'Attendibilità componente** e fare clic su attendibile festa basarsi. Selezionare **Modifica regole attestazione...**
4. Fare clic su **Aggiungi regola...**
5. Dall'elenco a discesa, selezionare **Invia tramite una regola personalizzata reclami** e fare clic su **Avanti**.
6. Immettere un nome per la regola di richiesta di rimborso.
7. In regole personalizzate: aggiungere il testo seguente:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Indicazione corrispondente:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Fare clic su **OK** quindi scegliere **Fine**. Chiudere la finestra AD FS gestione.

Gli utenti possono immettere l'accesso utilizzando il metodo locale (ad esempio smart card).

## <a name="trusted-ips-for-federated-users"></a>Indirizzi IP attendibile per gli utenti federati
IP di attendibile consentono agli amministratori di verifica in due passaggi by-passare per indirizzi IP specifici o per gli utenti federati contenenti le richieste provenienti dalla propria rete intranet. Nelle sezioni seguenti viene descritto come configurare Azure a più fattori autenticazione attendibili IP con utenti federati e la verifica in due passaggi by-passare quando una richiesta di provenienza all'interno di una rete intranet utenti federati. Questa operazione viene eseguita mediante la configurazione di ADFS per utilizzare un pass-through o filtrare un modello di richiesta di rimborso in arrivo con il tipo di attestazione all'interno di rete aziendale.

In questo esempio Usa Office 365 per l'attendibilità componente.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurare le regole di attestazioni ADFS

È necessario eseguire innanzitutto consiste nel configurare attestazioni ADFS. Si creerà le regole due richieste, uno per il tipo di domanda all'interno di rete aziendale e una aggiuntiva per mantenere gli utenti di eseguire l'accesso.

1. Aprire Gestione ADFS Active Directory.
2. A sinistra, selezionare **Attendibilità componente**.
3. Fare clic su **Piattaforma identità di Microsoft Office 365** e selezionare **Modifica regole di richiesta di rimborso** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. In regole del rilascio di trasformazione, fare clic su **Aggiungi regola.** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Nella trasformazione Richiedi regola guidata, selezionare **Pass-Through o filtro un'attestazione in ingresso** dall'elenco a discesa e fare clic su **Avanti**.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Nella casella accanto a nome regola attestazione, assegnare alla regola un nome. Ad esempio: InsideCorpNet.
7. Dall'elenco a discesa, accanto a posta in arrivo tipo attestazione, selezionare **All'interno di rete aziendale**.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Fare clic su **Fine**.
9. In regole del rilascio di trasformazione, fare clic su **Aggiungi regola**.
10. Nella trasformazione Richiedi regola guidata, selezionare **Invia attestazioni utilizzando una regola personalizzata** dall'elenco a discesa e fare clic su **Avanti**.
11. Nella casella Nome regola attestazione: immettere *mantenere utenti effettuato l'accesso*.
12. Nella casella regola personalizzato, immettere:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Fare clic su **Fine**.
14. Fare clic su **Applica**.
15. Fare clic su **Ok**.
16. Chiudere AD FS Management.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurare Azure a più fattori autenticazione attendibili IP con utenti federati
Ora che le richieste sono attive, possiamo configurare IP attendibile.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Sul lato sinistro fare clic su **Active Directory**.
3. In Directory, selezionare la cartella in cui si desidera configurare IP attendibile.
4. Per la Directory che è stato selezionato, fare clic su **Configura**.
5. Nella sezione autenticazione a più fattori, fare clic su **Gestisci impostazioni del servizio**.
6. Nella pagina Impostazioni dei servizi in indirizzi IP attendibili, selezionare **ignorare fattori l'autenticazione a più richieste dagli utenti federati personale Intranet.** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Fare clic su **Salva**.
8. Dopo aver applicati gli aggiornamenti, fare clic su **Chiudi**.


Questo è tutto! A questo punto, gli utenti federati di Office 365 devono solo è necessario utilizzare MFA quando una richiesta di rimborso deriva da fuori rete intranet aziendale.
