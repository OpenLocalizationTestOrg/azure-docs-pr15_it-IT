<properties 
    pageTitle="Impostazioni del Registro di sistema di individuazione di App per i servizi di Proxy cloud | Microsoft Azure" 
    description="L'obiettivo di questo argomento è necessario fornirgli i passaggi da eseguire per impostare la porta necessaria nei computer che esegue l'agente di rilevamento App Cloud." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Impostazioni di registro di sistema di individuazione App cloud per i servizi Proxy

Per impostazione predefinita, l'agente di rilevamento App Cloud è configurato per utilizzare solo le porte 80 o 443. Se si intende installare individuazione App Cloud in un ambiente con un server proxy che utilizza una porta personalizzata (80 né 443), è necessario configurare gli agenti per utilizzare questa porta. La configurazione si basa su una chiave del Registro di sistema.


L'obiettivo di questo argomento è necessario fornirgli i passaggi da eseguire per impostare la porta necessaria nei computer che esegue l'agente di rilevamento App Cloud.



**Per modificare la porta utilizzata dal computer che esegue l'agente di rilevamento App Cloud, procedere come segue:**


1. Avviare l'editor del Registro di sistema. <br> ![Correre](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Passare a o creare la chiave del Registro di sistema seguente: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 

3. Creare un nuovo valore **stringa più** **porte**chiamato. ![Nuovo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Per aprire la finestra di dialogo **Modifica stringa multipla** , fare doppio clic sul valore porte.


5. Nella casella valore di dati di testo digitare i valori seguenti e aggiungere tutte le porte personalizzate utilizzati dall'organizzazione: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Modificare più stringhe](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Fare clic su **OK** per chiudere la finestra di dialogo **Modifica stringa multiple** .



**Risorse aggiuntive**


* [Come è possibile a individuare App cloud unsanctioned utilizzati nell'organizzazione](active-directory-cloudappdiscovery-whatis.md) 


