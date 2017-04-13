<properties 
    pageTitle="Nota le reti | Microsoft Azure" 
    description="Configurando reti note, è possibile evitare indirizzi IP appartenenti all'organizzazione incluso nei componenti aggiuntivi di accesso da più aree geografiche e componenti aggiuntivi di accesso da indirizzi IP con i report di attività sospetto." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Reti note


È possibile utilizzare access Azure Active Directory e i report sull'utilizzo di ottenere la visibilità sull'integrità e la sicurezza della directory dell'organizzazione. Con queste informazioni, un amministratore di directory può determinare meglio nel punto in cui può stare possibili rischi di sicurezza in modo da potervi adeguatamente a ridurre i rischi.

È possibile che i report di "*componenti aggiuntivi di accesso da più aree geografiche*" e "*componenti aggiuntivi di accesso da indirizzi IP con attività sospetta*" contrassegno in modo non corretto indirizzi IP effettivamente appartenenti all'organizzazione. 

Questo problema può, ad esempio verificarsi quando: 

- Un utente il Boston office ha effettuato l'accesso remoto al centro dati San Francisco attiva il report "Accedere aggiuntivi da più aree geografiche" 

- Un utente dell'organizzazione tenta di accesso più volte con un trigger di password non corretta del report "Accedere aggiuntivi da indirizzi IP con attività sospetta" 

Per impedire la creazione di report di protezione fuorvianti questi casi, è necessario aggiungere noti intervalli di indirizzi IP all'elenco di indirizzo IP pubblico dell'organizzazione.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Per aggiungere intervalli di indirizzi IP pubblici dell'organizzazione, procedere come segue: 

1.  Accesso al [portale di gestione Azure](https://manage.windowsazure.com).

2.  Nel riquadro sinistro fare clic su **Active Directory**. <br><br>![Come funziona il Cloud App individuazione](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Nella scheda **Directory** selezionare la directory.

4.  Nel menu nella parte superiore, fare clic su **Configura**. <br><br>![Come funziona il Cloud App individuazione](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Nella scheda Configura, passare a **intervalli di indirizzi IP pubblici le organizzazioni** <br><br>![Come funziona il Cloud App individuazione](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Fare clic su **Aggiungi intervalli di indirizzi IP conosciuti**.

7.  Aggiungere gli intervalli di indirizzi nella finestra di dialogo visualizzata e quindi al termine, fare clic sul pulsante di controllo. <br><br>![Come funziona il Cloud App individuazione](./media/active-directory-known-networks/known-netwoks-04.png)


**Risorse aggiuntive**


* [Visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md)
* [Accedere aggiuntivi da indirizzi IP con attività sospetti](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Componenti aggiuntivi di accesso da più aree geografiche](active-directory-reporting-sign-ins-from-multiple-geographies.md)


