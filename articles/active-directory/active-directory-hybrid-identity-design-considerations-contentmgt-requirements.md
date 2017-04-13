<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare i requisiti di gestione dei contenuti | Microsoft Azure"
    description="Fornisce informazioni su come determinare i requisiti di gestione dei contenuti dell'azienda. In genere quando l'utente dispone il proprio dispositivo potrebbe essere anche più credenziali che verranno alternato in base all'applicazione che utilizza. È importante distinguere tra il tipo di contenuto è stata creata utilizzando le credenziali personali rispetto a quelli creati con le credenziali aziendale. Soluzione identità dovrebbe essere possibile interagire con cloud services per assicurare un'esperienza ottimale per l'utente finale durante assicurarsi che il suo informativa sulla privacy e aumentare la protezione dalla perdita di dati."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Requisiti di gestione del contenuto per la soluzione di identità ibrido

Comprendere i requisiti di gestione del contenuto per l'azienda direttamente può influire sulla scelta la soluzione di identità ibrido da usare. Con la proliferazione di più dispositivi e la possibilità degli utenti di visualizzare i propri dispositivi ([BYOD](http://aka.ms/byodcg)), la società necessario proteggere i propri dati ma anche necessario intatto privacy dell'utente. In genere quando l'utente dispone il proprio dispositivo potrebbe essere anche più credenziali che verranno alternati in base all'applicazione che utilizza. È importante distinguere tra il tipo di contenuto è stata creata utilizzando le credenziali personali rispetto a quelli creati con le credenziali aziendale. Soluzione identità dovrebbe essere possibile interagire con cloud services per assicurare un'esperienza ottimale per l'utente finale durante assicurarsi che il suo informativa sulla privacy e aumentare la protezione dalla perdita di dati. 

Soluzione identità verrà utilizzata dai diversi controlli tecnici per fornire la gestione dei contenuti, come illustrato nella figura seguente:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controlli di sicurezza che utilizzo di sistema di gestione dell'identità**

In generale, requisiti di gestione dei contenuti verranno utilizzati il sistema di gestione delle identità nelle aree seguenti:

- Informativa sulla privacy: identificare utente a cui appartiene una risorsa e applicare i controlli appropriati per mantenere l'integrità.
- Classificazione di dati: identificare l'utente o gruppo e livello di accesso a un oggetto in base alla classificazione. 
- Protezione dalla perdita di dati: controlli di protezione responsabili per la protezione dei dati per evitare la perdita saranno necessario interagire con il sistema di identità per convalidare l'identità dell'utente. È anche importante per il controllo trail scopo.

>[AZURE.NOTE]
Leggere [la classificazione di dati per la preparazione cloud](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) per ulteriori informazioni sulle procedure consigliate e linee guida per la classificazione di dati.

Pianificazione della soluzione di identità ibrido quando assicurarsi che le seguenti domande in base alle esigenze dell'organizzazione:

- La propria azienda dispone dei controlli di protezione nel punto in cui imporre la privacy dei dati?
 - In caso affermativo, i controlli di sicurezza saranno in grado di integrare con la soluzione di identità ibrido che si intende adottare?
- La propria azienda utilizza classificazione di dati?
 - In caso affermativo, è possibile integrare con la soluzione di identità ibrido che si intende adottare la soluzione corrente?
- La propria azienda attualmente disponibile una soluzione per perdita di dati? 
 - In caso affermativo, è possibile integrare con la soluzione di identità ibrido che si intende adottare la soluzione corrente?
- La propria azienda è necessario per controllare l'accesso alle risorse?
 - In caso affermativo, il tipo di risorse?
 - In caso affermativo, il livello di informazioni è necessario?
 - In caso affermativo, nel punto in cui deve trovarsi nel log di controllo? Locale o nel cloud?
- La propria azienda necessario crittografare tutti i messaggi che contengono dati sensibili (SSNs, i numeri di carta di credito e così via)?
- La propria azienda necessario crittografare tutti i documenti/contenuto condiviso con i partner commerciali esterni?
- La propria azienda è necessario applicare criteri aziendali in determinati tipi di messaggi di posta elettronica (non Rispondi a tutti, non inoltrare)?
 
>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Definire strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) verranno esaminate le opzioni disponibili e vantaggi/svantaggi delle singole opzioni.  Da con domande tali che verranno selezionati opzione migliore si adatta l'azienda deve.


## <a name="next-steps"></a>Passaggi successivi
[Requisiti di controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
