<properties
    pageTitle="Azure AD Connect: Abilitazione dispositivo writeback | Microsoft Azure"
    description="In questo documento dettaglio come abilitare writeback di dispositivo con Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Abilitazione writeback di dispositivo

>[AZURE.NOTE] Una sottoscrizione a Azure Active Directory Premium è necessaria per il writeback di dispositivo.

La documentazione seguente vengono fornite informazioni su come abilitare la funzionalità di writeback di dispositivo in Azure AD Connect. Writeback di dispositivo viene utilizzato negli scenari seguenti:

- Abilitare l'accesso condizionale in base ai dispositivi di ADFS (2012 R2 o versioni successive) protetta applicazioni (componente trust di terze parti).

In questo modo rafforzare la sicurezza e la certezza che viene concesso l'accesso alle applicazioni solo ai dispositivi attendibili. Per ulteriori informazioni sull'accesso condizionato, vedere [Gestione dei rischi con accesso condizionale](active-directory-conditional-access.md) e [impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Dispositivi devono trovarsi nella stessa foresta gli utenti. Dal momento che i dispositivi devono essere scritto nuovamente a una singola foresta, questa caratteristica non supporta attualmente una distribuzione con più insiemi di utente.</li>
<li>Oggetto di configurazione di un solo dispositivo registrazione può essere aggiunti alla foresta di Active Directory locale. Questa caratteristica non è compatibile con una topologia in cui il servizio Active Directory locale viene sincronizzato con più directory di Azure Active Directory.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Installa Azure AD connettersi
1. Installare Azure AD Connect utilizzando personalizzati o Express impostazioni. Si consiglia di iniziare tutti gli utenti e gruppi sincronizzato correttamente prima di attivare writeback di dispositivo.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparare Active Directory
Utilizzare la procedura seguente per preparare l'ambiente per l'uso di writeback di dispositivo.

1.  Dal computer in cui è installato Azure AD Connect, avviare PowerShell in modalità elevata.

2.  Se non è installato il modulo di Active Directory PowerShell, installarlo utilizzando il comando seguente:

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Se il modulo di Azure Active Directory PowerShell non è installato, scaricare e installare dal [Modulo Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297). Questo componente dipende l'Assistente per l'accesso, che viene installato con Azure AD Connect.

4.  Con credenziali di amministratore dell'organizzazione, eseguire i comandi seguenti e quindi uscire da PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Le credenziali di amministratore dell'organizzazione sono necessari poiché le modifiche apportate ai nomi di configurazione sono necessari. Un amministratore di dominio non dispone delle autorizzazioni sufficienti.

![PowerShell per l'attivazione writeback di dispositivo](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Descrizione:

- Se non esiste già, crea e configura nuovi contenitori e oggetti in CN = Configurazione registrazione dispositivo, CN = Servizi, CN = Configurazione, [nome distinto foresta].
- Se non esiste già, crea e configura nuovi contenitori e oggetti in CN = RegisteredDevices, [nome distinto dominio]. Gli oggetti dispositivo vengono creati in questo contenitore.
- Imposta le autorizzazioni necessarie nell'account Azure Active Directory Connector per gestire dispositivi di Active Directory.
- Solo deve essere eseguito in un insieme di strutture, anche se Azure AD Connect viene installato sul più insiemi di strutture.

Parametri:

- NomeDominio: Dominio Active Directory in cui verranno creati oggetti dispositivo. Nota: Tutti i dispositivi per un determinato insieme di strutture di Active Directory verranno creati in un unico dominio.
- AdConnectorAccount: Account Active Directory utilizzato da Azure AD Connect per gestire gli oggetti nella directory. Si tratta di account utilizzato dal servizio di sincronizzazione Azure AD Connect per connettersi a Active Directory. Se è stato installato con le impostazioni express, è l'account con MSOL_ preceduta.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Abilita dispositivo writeback di Azure AD Connect
Utilizzare la procedura seguente per abilitare writeback di dispositivo in Azure AD Connect.

1.  Eseguire di nuovo l'installazione guidata. Selezionare **personalizzare le opzioni di sincronizzazione** dalla pagina attività aggiuntive e fare clic su **Avanti**.
![Installazione personalizzata personalizzare le opzioni di sincronizzazione](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  Nella pagina caratteristiche facoltative writeback dispositivo non sarà non è più disponibile. Sarà disponibile in uscita nella pagina caratteristiche facoltative nota che se Azure AD Connect Prepara passaggi non sono writeback dispositivo completata. Selezionare la casella relativa writeback di dispositivo e fare clic su **Avanti**. Se la casella di controllo è ancora disattivato, vedere [risoluzione dei problemi di sezione](#the-writeback-checkbox-is-still-disabled).
![Caratteristiche facoltative dispositivo Writeback di installazione personalizzata](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  Nella pagina writeback verrà visualizzato il dominio specificato come foresta writeback dispositivo predefinito.
![Insieme di destinazione writeback installazione dispositivo personalizzato](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Completare l'installazione della procedura guidata senza apportare modifiche aggiuntive. Se necessario, fare riferimento alla [installazione personalizzata di Azure AD Connect.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Abilitare l'accesso condizionale
Istruzioni dettagliate per abilitare questo scenario sono disponibili all'interno di [impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verificare che i dispositivi vengono sincronizzati con Active Directory
Dispositivo writeback dovrebbe ora funzionare correttamente. Tenere presente che può richiedere fino a 3 ore per gli oggetti dispositivo sia indietro scritto in Active Directory.  Per verificare che i dispositivi sono da sincronizzare correttamente, eseguire le operazioni seguenti dopo aver completato le regole di sincronizzazione:

1.  Avviare Centro amministrativo di Active Directory.
2.  Espandere RegisteredDevices all'interno del dominio è federato.
![Il centro di amministrazione di Active Directory registrato dispositivi](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Verranno elencati sono dispositivi registrati correnti.
![Il centro di amministrazione di Active Directory registrato elenco dispositivi](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="the-writeback-checkbox-is-still-disabled"></a>La casella di controllo writeback è ancora disattivato
Se la casella di controllo per il writeback di dispositivo non è attivato, anche se aver seguito i passaggi precedenti, la procedura seguente mostrerà tutti i passaggi che cos'è verificare l'installazione guidata prima che la casella è abilitata.

Innanzitutto prima:

- Assicurarsi che almeno un insieme di strutture abbia 2012R2 Windows Server. Il tipo di oggetto dispositivo deve essere presenta.
- Se l'installazione guidata è già in esecuzione, le modifiche non vengano rilevate. In questo caso, completare l'installazione guidata ed eseguire nuovamente.
- Verificare che l'account che forniscono dello script di inizializzazione sia effettivamente all'utente corretto utilizzato da Active Directory Connector. Per verificare questa impostazione, procedere come segue:
    - Dal menu di avvio, aprire **Il servizio di sincronizzazione**.
    - Aprire la scheda **connettori** .
    - Trovare il connettore con il tipo di servizi di dominio Active Directory e selezionarlo.
    - Nel riquadro **Azioni**, scegliere **proprietà**.
    - Passare a cui **connettersi foresta di Active Directory**. Verificare che il dominio e il nome utente specificato in questo schermo l'account specificato per lo script.
![Account Connector nella gestione di servizi di sincronizzazione](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Verificare la configurazione di Active Directory:
- Verificare che il servizio di registrazione del dispositivo si trova nel percorso seguente (CN = DeviceRegistrationService, CN = dispositivo registrazione servizi, CN = Configurazione registrazione dispositivo, CN = Servizi, CN = Configuration) nel contesto dei nomi di configurazione.

![Risoluzione dei problemi, DeviceRegistrationService configurazione dello spazio dei nomi](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Verificare un solo oggetto di configurazione della ricerca lo spazio dei nomi di configurazione. Se esistono più monitor, eliminare i duplicati.

![Risoluzione dei problemi, cercare gli oggetti duplicati](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- Dispositivo registrazione dell'oggetto del servizio, assicurarsi che l'attributo msDS-DeviceLocation è presente e presenta un valore. Ricerca questo percorso e verificare che è presente con tipooggetto msDS-DeviceContainer.

![Risoluzione dei problemi, msDS DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Risoluzione dei problemi, RegisteredDevices classe di oggetti](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Verificare l'account utilizzato da Active Directory Connector disponga delle autorizzazioni necessarie per il contenitore di dispositivi registrati rilevato dal passaggio precedente. Si tratta delle autorizzazioni previste nel contenitore:

![Risoluzione dei problemi, verificare le autorizzazioni sul contenitore](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Verificare l'account Active Directory disponga delle autorizzazioni in CN = Configurazione registrazione dispositivo, CN = Servizi, CN = oggetto di configurazione.

![Risoluzione dei problemi, verificare le autorizzazioni per la configurazione di registrazione dispositivo](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ulteriori informazioni
- [Gestione dei rischi con accesso condizionale](active-directory-conditional-access.md)
- [Impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
