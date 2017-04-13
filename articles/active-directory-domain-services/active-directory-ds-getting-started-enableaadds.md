<properties
    pageTitle="Servizi di dominio Active Directory Azure: Attivare servizi di dominio Active Directory Azure | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Attivare servizi di dominio Active Directory Azure

## <a name="task-3-enable-azure-ad-domain-services"></a>Attività 3: Abilitare Servizi di dominio Active Directory Azure
In questa attività è attivare servizi di dominio Active Directory di Azure per la directory. Effettuare le seguenti operazioni di configurazione per attivare servizi di dominio Active Directory di Azure per la directory.

1. Passare al **portale classica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selezionare il nodo **Active Directory** nel riquadro sinistro.

3. Selezionare il tenant di Azure Active Directory (directory) per il quale si desidera attivare servizi di dominio Active Directory Azure.

    ![Selezionare Azure Active Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Fare clic sulla scheda **Configura** .

    ![Configurare la scheda della directory](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Scorrere fino a una sezione intitolata **servizi di dominio**.

    ![Sezione di configurazione di servizi di dominio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Attivare o disattivare l'opzione intitolato **attivare servizi di dominio per la directory** su **Sì**. È possibile alcune altre opzioni di configurazione per i servizi di dominio Active Directory Azure visualizzate nella pagina.

    ![Attivare servizi di dominio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Quando si attivano servizi di dominio Active Directory di Azure per il tenant, Azure Active Directory e archiviato i valori delle credenziali Kerberos e NTLM necessari per l'autenticazione degli utenti.

7. Specificare il **nome di dominio DNS dei servizi di dominio**.

   - Il nome di dominio predefinito della directory (vale a dire che terminano con la **. onmicrosoft.com** suffisso) sia selezionata per impostazione predefinita.

   - L'elenco contiene tutti i domini che sono stati configurati per la directory di Azure Active Directory, tra cui verificato, nonché domini non verificati che consentono di configurare nella scheda "Domains".

   - Inoltre, è inoltre possibile digitare un nome di dominio personalizzato. In questo esempio abbiamo digitato un nome di dominio personalizzato 'contoso100.com'.

     > [AZURE.WARNING] Assicurarsi che il prefisso dominio del nome di dominio che specificare (ad esempio, 'contoso100' nel nome di dominio 'contoso100.com') è meno di 15 caratteri. È possibile creare un dominio di servizi di dominio Active Directory Azure con un prefisso dominio più di 15 caratteri.

8. Assicurarsi che il nome di dominio DNS che scelto per il dominio gestito non esiste già nella rete virtuale. In particolare, verificare se:

   - si dispone già di un dominio con lo stesso nome di dominio DNS sulla rete virtuale.

   - la rete virtuale selezionato ha una connessione VPN alla rete locale e si dispone di un dominio con lo stesso nome di dominio DNS nella rete locale.

   - si dispone di un servizio cloud esistente con lo stesso nome della rete virtuale.

9. Il passaggio successivo consiste nel selezionare una rete virtuale in cui si desidera servizi di dominio Active Directory di Azure sia disponibile. Selezionare la rete virtuale e subnet dedicata creata nell'elenco a discesa intitolato **servizi di dominio di connettersi a questa rete virtuale**.

   - Assicurarsi che la rete virtuale che è stato specificato appartiene a un'area di Azure supportata da servizi di dominio Active Directory Azure. Fare riferimento alla pagina [servizi Azure dall'area](https://azure.microsoft.com/regions/#services/) fonti di informazioni sulle aree di Azure in cui è disponibili Azure servizi di dominio Active Directory.

   - Reti virtuali appartenenti a un'area che non supportano i servizi di dominio di Azure Active Directory non vengono visualizzati nell'elenco a discesa.
   
   - Utilizzare una subnet dedicata all'interno della rete virtuale per i servizi di dominio di Azure Active Directory. Assicurarsi che non si seleziona subnet gateway. Vedere [Considerazioni sulla rete](active-directory-ds-networking.md). 

   - Analogamente, le reti virtuali che sono state create usando Gestione risorse di Azure non vengono visualizzati nell'elenco a discesa. Reti virtuali Manager delle risorse non sono attualmente supportate da servizi di dominio Active Directory Azure.

10. Per attivare servizi di dominio Active Directory di Azure, scegliere **Salva** dal riquadro attività nella parte inferiore della pagina.

11. Nella pagina viene visualizzato un ' in sospeso... " stato, mentre i servizi di dominio Active Directory Azure viene attivato per la directory.

    ![Attivare servizi di dominio, in attesa di stato](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Azure servizi di dominio Active Directory fornisce disponibilità per il dominio gestito. Dopo aver attivato Servizi di dominio Active Directory di Azure, si noterà gli indirizzi IP in cui sono disponibili sul Mostra virtuali su uno dei servizi di dominio. Il secondo indirizzo IP verrà visualizzato più avanti, non appena il servizio consente disponibilità per il dominio. Quando la disponibilità elevata è configurato e attivo per il dominio, è necessario vedere due indirizzi IP nella sezione dei **servizi di dominio** della scheda **Configura** .

12. Dopo circa 20-30 minuti, viene visualizzato il primo indirizzo IP in cui è disponibili nella rete virtuale nel campo **indirizzo IP** nella pagina **Configura** servizi di dominio.

    ![IP prima effettuato il provisioning di servizi di dominio attivati:](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Quando la disponibilità è attivata per il dominio, viene visualizzato due indirizzi IP visualizzati nella pagina. Il dominio gestito è disponibile nella rete virtuale selezionata in queste due indirizzi IP. Nota verso il basso gli indirizzi IP in modo che è possibile aggiornare le impostazioni di DNS per la rete virtuale. Questo passaggio consente macchine virtuali sulla rete virtuale per connettere il dominio per le operazioni, ad esempio aggiunta al dominio.

    ![Servizi di dominio attivato - viene completato il provisioning di entrambi IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] A seconda delle dimensioni del tenant di Azure Active Directory (numero di utenti, gruppi e così via), la sincronizzazione al proprio dominio gestito accetta un po' di tempo. Questo processo di sincronizzazione viene eseguita in background. Per tenant di grandi dimensioni con decine di migliaia di oggetti, potrebbe richiedere un o due giorni per tutti gli utenti, appartenenza ai gruppi e le credenziali da sincronizzare.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Attività 4 - aggiornare le impostazioni DNS per la rete virtuale Azure
La configurazione è necessario [aggiornare le impostazioni di DNS per la rete virtuale Azure](active-directory-ds-getting-started-dns.md).
