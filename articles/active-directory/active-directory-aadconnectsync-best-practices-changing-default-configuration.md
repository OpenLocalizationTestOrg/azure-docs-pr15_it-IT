<properties
    pageTitle="Sincronizzazione di Azure AD Connect: procedure consigliate per la modifica della configurazione predefinita | Microsoft Azure"
    description="Procedure ottimali per la modifica della configurazione predefinita di sincronizzazione di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronizzazione di Azure AD Connect: procedure consigliate per la modifica della configurazione predefinita
Lo scopo di questo argomento è per descrivere le modifiche supportate e non supportate per la sincronizzazione di Azure AD Connect.

La configurazione creata da Azure AD Connect funziona "così com'è" per la maggior parte delle ambienti per la sincronizzazione di Active Directory locali con Azure Active Directory. Tuttavia, in alcuni casi, è necessario applicare alcune modifiche a una configurazione per soddisfare una particolare esigenza o requisito.

## <a name="changes-to-the-service-account"></a>Modifiche apportate all'account del servizio
Sincronizzazione di Azure AD Connect è in esecuzione con un account di servizio creato dall'installazione guidata. Questo account del servizio contiene le chiavi di crittografia per il database utilizzato dal servizio di sincronizzazione. Viene creato con una password lunga 127 caratteri e la password è impostata su non scada.

- È **supportata** per modificare o reimpostare la password dell'account del servizio. In questo modo Elimina le chiavi di crittografia e il servizio non è in grado di accedere al database e non si riesce ad avviare.

## <a name="changes-to-the-scheduler"></a>Modifiche apportate all'utilità di pianificazione
Iniziare con le versioni da compilazione 1.1 (febbraio 2016) è possibile configurare l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) di un ciclo di sincronizzazione diverso da quello predefinito 30 minuti.

## <a name="changes-to-synchronization-rules"></a>Modifiche alle regole di sincronizzazione
L'installazione guidata fornisce una configurazione che deve per usare per i motivi più comuni. Nel caso in cui è necessario apportare modifiche alla configurazione, è necessario seguire queste regole per essere una configurazione supportata.

- È possibile [modificare l'attributo flussi](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se i flussi di attributo diretto predefinito non sono adatti per l'organizzazione.
- Se si desidera [non flusso un attributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e rimuovere gli eventuali valori attributo esistente in Azure Active Directory, è necessario creare una regola per questo scenario.
- [Disattivare una regola di sincronizzazione indesiderate](#disable-an-unwanted-sync-rule) anziché eliminarlo. Una regola eliminata verrà ricreata durante l'aggiornamento.
- Per [modificare una regola di fuori della casella](#change-an-out-of-box-rule), si deve eseguire una copia della regola originale e disabilitare la regola di casella di. L'Editor delle regole sincronizzazione viene visualizzato un prompt e consente di.
- Esportare le regole di sincronizzazione personalizzati utilizzando l'Editor di regole di sincronizzazione. L'editor fornisce con uno script di PowerShell che è possibile utilizzare per ricreare facilmente in uno scenario di ripristino di emergenza.

>[AZURE.WARNING] Le regole di sincronizzazione di casella di avere un'identificazione personale. Se si apporta una modifica di queste regole, l'identificazione personale non è più la corrispondenza. Potrebbero essersi verificati problemi in futuro quando si tenta di applicare una nuova versione di Azure AD Connect. Apportare modifiche solo il modo in cui che viene descritto in questo articolo.

### <a name="disable-an-unwanted-sync-rule"></a>Disattivare una regola di sincronizzazione indesiderate
Non eliminare una regola della casella di sincronizzazione. Verrà ricreato durante l'aggiornamento successivo.

In alcuni casi, l'installazione guidata produce una configurazione non funziona per la topologia. Ad esempio, se si dispone di una topologia di strutture delle risorse di account, ma è stata estesa schema della foresta account con lo schema di Exchange, le regole per Exchange vengono create per l'account di strutture e delle risorse. In questo caso, è necessario disattivare la regola di sincronizzazione per Exchange.

![Regola di sincronizzazione disabilitata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Nella figura riportata sopra, l'installazione guidata ha rilevato uno schema di Exchange 2003 precedente nel foresta account. Prima della foresta della risorsa è stata introdotta in ambiente Fabrikam è stato aggiunto l'estensione di schema. Affinché che non attributi dall'implementazione di Exchange precedente sono sincronizzati, è consigliabile disattivare la regola di sincronizzazione come illustrato.

### <a name="change-an-out-of-box-rule"></a>Modificare una regola di fuori della casella
Se è necessario apportare modifiche a una regola di fuori della casella, è necessario crearne una copia della regola di casella di e disabilitare la regola originale. Apportare le modifiche alla regola duplicata. L'Editor delle regole sincronizzazione vi aiuta a questi passaggi. Quando si apre una regola di fuori della casella, viene visualizzata la finestra di dialogo:  
![Avviso dalla regola casella](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selezionare **Sì** per creare una copia della regola. La regola duplicata viene aperto.  
![Regola duplicato](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Questa regola duplicato, apportare le modifiche necessarie all'ambito, join e le trasformazioni.

## <a name="next-steps"></a>Passaggi successivi

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
