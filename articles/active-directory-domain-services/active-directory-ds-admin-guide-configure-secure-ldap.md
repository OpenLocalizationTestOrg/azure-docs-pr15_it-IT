<properties
    pageTitle="Configurare la protezione di LDAP (LDAPS) in servizi di dominio Active Directory Azure | Microsoft Azure"
    description="Configurare sicura LDAP (LDAPS) per un dominio gestito di servizi di dominio Active Directory di Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurare sicura LDAP (LDAPS) per un dominio gestito di servizi di dominio Active Directory di Azure
Questo articolo spiega come è possibile attivare Lightweight sicura di Directory Access Protocol (LDAPS) per il dominio gestito di servizi di dominio Active Directory Azure. LDAP sicuro è noto anche come "LDAP Lightweight Directory Access Protocol () su Secure Sockets Layer (SSL) / protezione TLS (Transport Layer)".

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo, è necessario:

1. Una **sottoscrizione Azure**valida.

2. Una **directory di Azure Active Directory** - uno sincronizzata con una directory locale o basata solo su cloud.

3. **Servizi di dominio Active Directory Azure** deve essere abilitata per la directory di Azure Active Directory. Se non è ancora stato fatto, seguire tutte le attività descritte nella [Guida introduttiva](./active-directory-ds-getting-started.md).

4. Un **certificato da utilizzare per consentire la protezione di LDAP**.
    - **Consigliato** - ottenere un certificato dal CA dell'organizzazione o autorità di certificazione pubblica. Questa opzione di configurazione è più sicura.
    - In alternativa, è anche possibile [creare un certificato autofirmato](#task-1---obtain-a-certificate-for-secure-ldap) come illustrato di seguito in questo articolo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisiti per il certificato LDAP protetto
Acquisire un certificato valido per le seguenti linee guida prima di attivare la protezione di LDAP. Si verifichino errori se si tenta di attivare LDAP sicuro per il dominio gestito con un certificato valido/errato.

1. **Emittente attendibili** - il certificato deve essere rilasciato da un'autorità attendibile dal computer per connettersi al dominio tramite LDAP sicuro. Questa autorità potrebbe essere autorità di certificazione dell'organizzazione o un'autorità di certificazione pubblica considerato attendibile da questi computer.

2. **Durata** - il certificato deve essere valido per almeno i mesi successivi 3 a 6. L'accesso sicuro LDAP al proprio dominio gestito non è disponibile quando scade il certificato.

3. **Nome del soggetto** - il nome del soggetto del certificato deve essere un carattere jolly per il dominio gestito. Ad esempio, se il dominio è denominato 'contoso100.com', nome del soggetto del certificato deve essere ' *. contoso100.com'. Impostare il nome DNS (nome soggetto alternativo) per questo nome con caratteri jolly.

3. **Utilizzo chiave** - il certificato deve essere configurato per di seguito viene utilizzato - firme digitali e la crittografia chiave.

4. **Scopo certificato** - il certificato deve essere valido per l'autenticazione server SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Attività 1: ottenere un certificato per LDAP protetto
Alla prima attività comporta l'acquisizione di un certificato usato per l'accesso sicuro LDAP a dominio gestito. Sono disponibili due opzioni:

- Ottenere un certificato da un'autorità di certificazione. L'autorità può essere CA globale dell'organizzazione o un'autorità di certificazione pubblica.

- Creare un certificato autofirmato.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opzione (scelta consigliata): ottenere un certificato LDAP protetto da un'autorità di certificazione
Se l'organizzazione distribuisce un enterprise infrastruttura chiave pubblica (), è necessario ottenere un certificato dall'autorità di certificazione (CA) per l'organizzazione. Se l'organizzazione Ottiene i certificati da un'autorità di certificazione pubblica, è necessario ottenere il certificato LDAP sicuro da tale autorità di certificazione pubblica.

Quando si richiede un certificato, assicurarsi di seguire i requisiti delineati da un [requisito per il certificato LDAP protetto](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Computer client per connettersi al dominio gestito uso di LDAP sicuro deve considerare attendibile l'autorità di certificazione LDAPS.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opzione B: creare un certificato autofirmato per LDAP protetto
È possibile scegliere di creare un certificato autofirmato per LDAP protetto, se:

- i certificati all'interno dell'organizzazione non vengono emessi da un'autorità di certificazione o
- non si prevede di usare un certificato emesso da un'autorità di certificazione pubblica.

**Creare un certificato autofirmato tramite PowerShell**

In un computer Windows, aprire una nuova finestra di PowerShell come **amministratore** e digitare i comandi seguenti per creare un nuovo certificato autofirmato.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

Nell'esempio precedente, sostituire 'contoso100.com' con il nome di dominio DNS del dominio gestito servizi di dominio Active Directory Azure.

![Selezionare Azure Active Directory](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Il certificato autofirmato appena creato viene inserito nell'archivio certificati del computer locale.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Attività 2 - esportazione LDAP sicuro certificato per un. File PFX
Prima di iniziare questa operazione, assicurarsi di aver ottenuto il certificato LDAP sicuro da un'autorità di certificazione pubblica o l'autorità di certificazione o creazione di un certificato autofirmato.

Eseguire i passaggi seguenti per esportare il certificato LDAPS a un. File PFX.

1. Fare clic sul pulsante **Start** e digitare **R**. Nella finestra di dialogo **Esegui** digitare **mmc** e fare clic su **OK**.

    ![Avviare la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. Quando viene richiesto di **Controllo dell'Account utente** , fare clic su **Sì** per avviare MMC (Microsoft Management Console) come amministratore.

3. Dal menu **File** fare clic su **Aggiungi/Rimuovi Snap-pollici via**.

    ![Aggiungere snap-in di MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Nella finestra di dialogo **Aggiungi o Rimuovi Snap-in** , selezionare **lo snap-in** , quindi scegliere il **Aggiungi >** pulsante.

    ![Aggiungere snap-in certificati di MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. Nella procedura guidata **snap-in certificati** selezionare **account del Computer** e fare clic su **Avanti**.

    ![Aggiungere snap-in certificati per account del computer](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. Nella pagina **Seleziona Computer** selezionare **computer locale: (il computer è in esecuzione questa console)** e fare clic su **Fine**.

    ![Aggiungere i certificati snap-in, selezionare computer](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Nella finestra di dialogo **Aggiungi o Rimuovi Snap-in** , fare clic su **OK** per aggiungere lo snap-in MMC.

    ![Aggiungi snap-in certificati di MMC - completato](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Nella finestra di MMC, fare clic per espandere **Directory radice**. Verrà visualizzato lo snap-in certificati caricato. Fare clic su **certificati (Computer locale)** per espandere. Fare clic per espandere il nodo **personali** , seguito dal nodo **certificati** .

    ![Archivio certificati personali aperto](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Verrà visualizzato il certificato autofirmato creato. È possibile esaminare le proprietà del certificato per garantire che l'identificazione personale corrisponde a quello indicato in windows PowerShell al momento della creazione del certificato.

10. Selezionare il certificato autofirmato e **scelta rapida**. Dal menu di scelta rapida, selezionare **Tutte le attività** e selezionare **Esporta...**.

    ![Certificato di esportazione](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. **Esportazione guidata certificati**fare clic su **Avanti**.

    ![Esportazione guidata di certificato](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Nella pagina **Esporta chiave privata** selezionare **Esporta la chiave privata**e fare clic su **Avanti**.

    ![Esportare la chiave privata certificato](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] È necessario esportare la chiave privata insieme il certificato. Se si fornisce un file PFX che non contiene la chiave privata per il certificato, per consentire la protezione di LDAP per il dominio gestito.

13. Nella pagina **Formato File di esportazione** , selezionare **scambio di informazioni personali - PKCS #12 (. PFX)** come formato di file per il file esportato.

    ![Formato di file di certificato di esportazione](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Solo il. Formato di file PFX è supportata. Non esportare il certificato per il. Formato di file CER.

14. Nella pagina **sicurezza** selezionare l'opzione **Password** e digitare la password per proteggere la. File PFX. Dal momento che saranno necessarie nell'attività successiva, ricordare la password. Fare clic su **Avanti** per continuare.

    ![Password per l'esportazione di certificato ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Prendere nota della password. È necessario durante l'attivazione LDAP sicuro per questo dominio gestito nel [passaggio 3 - abilitare LDAP sicuro per il dominio gestito](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Nella pagina **File da esportare** , specificare il nome del file e il percorso in cui si desidera esportare il certificato.

    ![Percorso per l'esportazione di certificato](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Nella pagina successiva, fare clic su **Fine** per esportare il certificato in un file PFX. Finestra di dialogo di conferma verrà visualizzato quando il certificato è stato esportato.

    ![Esportare certificato completato](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Attività 3 - Abilita LDAP sicuro per il dominio gestito
Per abilitare la protezione di LDAP, eseguire i passaggi di configurazione seguenti:

1. Passare al **[portale classica Azure](https://manage.windowsazure.com)**.

2. Selezionare il nodo **Active Directory** nel riquadro sinistro.

3. Selezionare la directory di Azure Active Directory (nota anche come "tenant"), per cui è stato attivato Servizi di dominio Active Directory Azure.

    ![Selezionare Azure Active Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Fare clic sulla scheda **Configura** .

    ![Configurare la scheda della directory](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Scorrere fino alla sezione intitolata **servizi di dominio**. Verrà visualizzata un'opzione intitolata **Proteggere LDAP (LDAPS)** , come illustrato nella schermata seguente:

    ![Sezione di configurazione di servizi di dominio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Fare clic sul pulsante **Configura certificato …** per visualizzare la finestra di dialogo **Configura certificato per LDAP protetto** .

    ![Configurare il certificato per LDAP protetto](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Fare clic sull'icona di cartella sotto **PFX FILE di certificato** per specificare il file PFX che contiene il certificato che si desidera utilizzare per l'accesso sicuro LDAP a dominio gestito. Inoltre, immettere la password specificata quando si esporta il certificato per il file PFX. Quindi fare clic sul pulsante Chiudi nella parte inferiore.

    ![Specificare la password e sicura LDAP PFX file](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. La sezione di **servizi di dominio** della scheda **Configura** deve visualizzato in grigio e nel **in sospeso...** stato per alcuni minuti. Durante questo periodo, il certificato LDAPS viene verificato l'accuratezza e sicura LDAP è configurato per il dominio gestito.

    ![LDAP - sicuro in sospeso](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Bastano circa 10-15 minuti per consentire la protezione di LDAP per il dominio gestito. Se il certificato LDAP sicuro fornito non corrispondono ai criteri necessari, LDAP sicuro non è abilitato per la directory e viene visualizzato un errore. Ad esempio, il nome di dominio non è corretto, il certificato è scaduto o alla scadenza breve e così via.

9. Quando LDAP sicuro è stato attivato per il dominio gestito il **in sospeso...** messaggio dovrebbe essere eliminato. Verrà visualizzata l'identificazione personale del certificato visualizzato.

    ![LDAP sicuro abilitata](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Attività 4 - abilitare l'accesso LDAP protetto tramite internet
**Attività facoltativo** - se non si prevede di accedere al dominio gestito tramite LDAPS tramite internet, ignorare questa attività di configurazione.

Prima di iniziare questa operazione, assicurarsi che è stata completata la procedura descritta nel [passaggio 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Verrà visualizzata un'opzione per **Abilitare sicura LDAP ACCESS su INTERNET** nella sezione dei **servizi di dominio** della pagina **Configura** . Questa opzione è impostata su **NO** per impostazione predefinita, poiché l'accesso a internet per il dominio gestito tramite LDAP sicuro è disattivato per impostazione predefinita.

    ![LDAP sicuro abilitata](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Attivare/disattivare **attivare l'accesso sicuro LDAP in Internet** su **Sì**. Fare clic sul pulsante **Salva** nel riquadro inferiore.
    ![LDAP sicuro abilitata](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. La sezione di **servizi di dominio** della scheda **Configura** deve visualizzato in grigio e nel **in sospeso...** stato per alcuni minuti. In un secondo momento, internet possono accedere al proprio dominio gestito su LDAP sicuro.

    ![LDAP - sicuro in sospeso](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Bastano circa 10 minuti per consentire l'accesso a internet su LDAP sicuro per il dominio gestito.

4. Al abilitando l'accesso sicuro LDAP al proprio dominio gestito tramite internet, il **in sospeso...** messaggio dovrebbe essere eliminato. Verrà visualizzato l'indirizzo IP esterno che può essere utilizzato per accedere a directory tramite LDAPS nel campo **IP indirizzo per LDAPS accesso esterno**.

    ![LDAP sicuro abilitata](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Attività 5: configurare il sistema DNS per accedere al dominio gestito da internet
**Attività facoltativo** - se non si prevede di accedere al dominio gestito tramite LDAPS tramite internet, ignorare questa attività di configurazione.

Prima di iniziare questa operazione, assicurarsi che è stata completata la procedura descritta nel [passaggio 4](#task-4---enable-secure-ldap-access-over-the-internet).

Dopo aver abilitato l'accesso sicuro LDAP tramite internet per il dominio gestito, è necessario aggiornare il DNS in modo che i computer client disponibili in questo dominio gestito. Al termine dell'attività 4, un indirizzo IP esterno viene visualizzato la scheda **Configura** **IP indirizzo per LDAPS accesso esterno**.

Configurare il proprio provider DNS esterno in modo che il nome DNS del dominio gestito (ad esempio, ' contoso100.com') fa riferimento a questo indirizzo IP esterno. In questo esempio, è necessario creare la voce DNS seguente:

    contoso100.com  -> 52.165.38.113

Ecco fatto: ora si è pronti per connettersi al dominio gestito tramite LDAP protetto tramite internet.

> [AZURE.WARNING] Tenere presente che i computer client devono attendibili autorità di certificazione LDAPS per essere in grado di connettersi correttamente al dominio gestito tramite LDAPS. Se si usa un'autorità di certificazione o un'autorità di certificazione pubblicamente attendibile, non occorre fare nulla dal momento che i computer client attendibile questi emittenti. Se si utilizza un certificato autofirmato, è necessario installare la parte pubblica del certificato autofirmato nell'archivio certificati attendibili del computer client.

<br>

## <a name="related-content"></a>Contenuti correlati

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-administer-domain.md)
