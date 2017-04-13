<properties 
   pageTitle="Risolvere i problemi di un dispositivo StorSimple distribuito | Microsoft Azure"
   description="Descrive come individuare e correggere gli errori che si verificano su un dispositivo StorSimple è attualmente distribuito e funzionante."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Risolvere i problemi di dispositivi StorSimple operativi

## <a name="overview"></a>Panoramica

In questo articolo fornisce indicazioni per la risoluzione dei problemi utile per la risoluzione dei problemi di configurazione che possono verificarsi dopo il dispositivo StorSimple è distribuito e funzionante. Descrive i problemi più comuni, le possibili cause e procedure consigliate per risolvere problemi che potrebbero verificarsi durante l'esecuzione di Microsoft Azure StorSimple. Queste informazioni si applicano al dispositivo fisico StorSimple locale e il dispositivo virtuale StorSimple.

Alla fine di questo articolo che è disponibile un elenco dei codici di errore che possono verificarsi durante l'operazione di Microsoft Azure StorSimple, nonché i passaggi da eseguire per risolvere gli errori. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo di creazione guidata configurazione per i dispositivi operativi

Utilizzare la configurazione guidata ([Richiama HcsSetupWizard][1]) per controllare la configurazione del dispositivo e adottare misure correttive se necessario.

Quando si esegue la configurazione guidata in un dispositivo configurato in precedenza e operativo, il flusso del processo è diverso. È possibile modificare solo le voci seguenti:

- Indirizzo IP, maschera subnet e gateway
- Server DNS primario
- Server NTP primario
- Configurazione del proxy web facoltativo

La configurazione guidata non esegue operazioni correlate alla registrazione della raccolta e dispositivo di password.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Errori che si verificano durante esecuzioni successive dell'installazione guidata

Nella tabella seguente vengono descritti gli errori che possono verificarsi quando si esegue la configurazione guidata in un dispositivo operativo, le possibili cause degli errori e consigliato azioni a risolverli. 

| No. | Messaggio di errore o condizione | Le possibili cause | Azione consigliata |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Errore 350032: Il dispositivo è già stato disattivato. | Se si esegue la configurazione guidata in un dispositivo che viene disattivato, verrà visualizzato questo messaggio di errore. | [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per la procedura successiva. Non è possibile attivare un dispositivo disattivato servizio. La reimpostazione factory potrebbe essere necessaria prima di poter attivare nuovamente il dispositivo. |
|  2  | Richiamare HcsSetupWizard: ERROR_INVALID_FUNCTION (eccezione da HRESULT: 0x80070001) | Aggiornare il server DNS non riesce. Le impostazioni DNS sono impostazioni globali e vengono applicate in tutte le interfacce di rete. | Abilitare l'interfaccia e applicare nuovamente le impostazioni DNS. Ciò potrebbe interferire con la rete per le altre interfacce abilitati in quanto tali impostazioni sono valide. |
|  3  | Il dispositivo viene visualizzato in linea nel portale di servizio StorSimple Manager, ma quando si tenta di completare la configurazione minima e salvare la configurazione, l'operazione non riesce. | Durante l'installazione e configurazione iniziali, il proxy web non è stato configurato, anche se si è verificato un server proxy effettivo in posizione. | Utilizzare il [cmdlet Test HcsmConnection] [ 2] per individuare l'errore. [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) se non si riesce a risolvere il problema. |
|  4  | Richiamare HcsSetupWizard: Valore non è compreso nell'intervallo previsto. | Una maschera di subnet non corretti produce questo errore. Le possibili cause sono: <ul><li> Maschera subnet è vuota o mancante.</li><li>Il formato di prefisso Ipv6 non è corretto.</li><li>L'interfaccia è abilitato cloud, ma il gateway è mancanti o non corretti.</li></ul>Si noti che dati 0 automaticamente cloud abilitati se configurato la configurazione guidata. | Per risolvere il problema, usare subnet 0.0.0.0 o 256.256.256.256 e quindi osservare l'output. Immettere i valori corretti per la maschera di subnet, gateway e il prefisso Ipv6, in base alle esigenze. |
 
## <a name="error-codes"></a>Codici di errore

Gli errori sono elencati in ordine numerico.

|Numero errore|Testo di errore o la descrizione|Azione consigliata dell'utente|
|:---|:---|:---|
|10502|Si è verificato un errore durante l'accesso al proprio account di archiviazione.|Attendere alcuni minuti e riprovare. Se l'errore persiste, contattare il supporto tecnico Microsoft per la procedura successiva.|
|40017|L'operazione di backup non riuscita come un volume specificato nei criteri di backup non è stato trovato nel dispositivo.|Riprovare a eseguire il backup operazione, se l'errore persiste, contattare il supporto Microsoft. per la procedura successiva.|
|40018|L'operazione di backup non riuscita come nessuno dei volumi specificati nei criteri di backup rilevati sul dispositivo. |Riprovare a eseguire il backup operazione, se l'errore persiste, contattare il supporto Microsoft. per la procedura successiva.|
|390061|Il sistema è occupato o non è disponibile.|Attendere alcuni minuti e riprovare. Se l'errore persiste, contattare il supporto tecnico Microsoft per la procedura successiva.|
|390143|Si è verificato un errore con codice errore 390143. (Errore sconosciuto).|Se l'errore persiste, contattare il supporto Microsoft per la procedura successiva.|

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a risolvere il problema, [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per assistenza. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
