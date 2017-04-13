<properties
    pageTitle="Impostazioni di criteri e MDM gruppo | Microsoft Azure"
    description="Informazioni sui criteri di gruppo e dispositivi mobili le impostazioni di gestione (MDM) che devono essere utilizzate in dispositivi di proprietà dell'azienda. I criteri vengono applicati al dispositivo intero dell'utente."
    services="active-directory"
    keywords="quali sono i criteri di gruppo e le impostazioni di MDM per Enterprise stato Roaming, Roaming stato Enterprise, cloud windows"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Impostazioni di criteri di gruppo e MDM

Utilizzare questi criteri di gruppo e dispositivi mobili (MDM) le impostazioni solo nei dispositivi aziendali perché questi criteri vengono applicati al dispositivo intero dell'utente. L'applicazione di criteri di MDM per disabilitare la sincronizzazione di impostazioni per un uso personale, proprietà dell'utente dispositivo influire negativamente sull'uso di tale dispositivo. Inoltre, è avrà effetto altri account utente sul dispositivo anche dai criteri.

Aziende che desidera gestire comuni per i dispositivi (non gestiti) personali è possono utilizzare il portale di Azure per abilitare o disabilitare il roaming, anziché utilizzare criteri di gruppo o MDM.
Nelle tabelle seguenti vengono descrivono le impostazioni dei criteri disponibili.

## <a name="mdm-settings"></a>Impostazioni di MDM
Le impostazioni dei criteri MDM applicano a Windows 10 e Windows 10 Mobile.  Supporto per Windows 10 Mobile è disponibile solo per account Microsoft in base a roaming tramite OneDrive account utente.  Fare riferimento alla sezione "Dispositivi e i punti finali" per informazioni dettagliate su quali dispositivi sono supportati per la sincronizzazione di Azure Active Directory in base a.

| Nome                               | Descrizione                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Consente la connessione Account Microsoft | Consente agli utenti di eseguire l'autenticazione tramite un account Microsoft nel dispositivo |
| Consentire le impostazioni di sincronizzazione             | Consente agli utenti di utilizzare le impostazioni di Windows e dati di app. Se si disattiva questo criterio consente di disattivare sincronizzazione, nonché l'esecuzione di backup su dispositivi mobili                  |

## <a name="group-policy-settings"></a>Criteri di gruppo
Le impostazioni dei criteri di gruppo applicano ai dispositivi Windows 10 che fanno parte di un dominio Active Directory. La tabella include anche impostazioni legacy visualizzate per gestire le impostazioni di sincronizzazione, ma non funzionano per Enterprise stato Roaming per Windows 10, che sono contrassegnate con "non USA" nella descrizione.

| Nome                                | Descrizione |
|-------------------------------------|-------------|
| Account: Account di Microsoft blocco  |Questa impostazione impedisce agli utenti di aggiunta nuovo account di Microsoft nel computer in uso|
| Si sincronizzano                         |Impedisce agli utenti per il roaming app dati e le impostazioni di Windows|
| Si sincronizzano personalizzare             |Disattiva la sincronizzazione del gruppo temi|
| Si sincronizzano le impostazioni del browser        |Disattiva la sincronizzazione del gruppo di Internet Explorer|
| Si sincronizzano le password               |Disattiva la sincronizzazione del gruppo di password|
| Si sincronizzano altre impostazioni di Windows  |Disattiva la sincronizzazione del gruppo di impostazioni di altre finestre|
| Si sincronizzano personalizzazione del desktop |Non utilizzare; non ha alcun effetto|
| Non vengono sincronizzati sulle connessioni a consumo  |Disabilita il roaming su connessioni, ad esempio cellulari 3G a consumo|
| Si sincronizzano App                    |Non utilizzare; non ha alcun effetto|
|Si sincronizzano le impostazioni dell'app             |Disattiva roaming dei dati app|
|Si sincronizzano le impostazioni di avvio           |Non utilizzare; non ha alcun effetto|


## <a name="related-topics"></a>Argomenti correlati
- [Panoramica di Roaming di stato dell'organizzazione](active-directory-windows-enterprise-state-roaming-overview.md)
- [Abilitare lo stato dell'organizzazione roaming di Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Dati roaming domande frequenti e le impostazioni](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Informazioni di riferimento impostazioni comuni di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
