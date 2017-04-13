<properties 
    pageTitle="Aggiungere un certificato nell'archivio CA Java | Microsoft Azure" 
    description="Informazioni su come aggiungere un certificato di certificazione (CA) certificato all'archivio certificati (cacerts) Java CA per Twilio servizio o Bus di servizio Azure." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Aggiunta di un certificato nell'archivio certificati CA Java
La procedura seguente mostra come aggiungere un certificato di certificazione (CA) certificato nell'archivio certificati (cacerts) Java CA. Nell'esempio utilizzato è per il certificato di CA necessario per il servizio Twilio. Le informazioni fornite in un secondo momento nell'argomento viene descritto come installare il certificato per il Bus di servizio Azure. 

È possibile utilizzare keytool per aggiungere il certificato CA prima di compressione il JDK e aggiungerlo alla cartella **approot** del progetto Azure oppure è possibile eseguire un'attività di avvio Azure che utilizza keytool per aggiungere il certificato. Si presuppone che verranno aggiunti un certificato prima JDK viene compresso. Inoltre, un certificato specifico da utilizzare nell'esempio, ma i passaggi della richiesta di un certificato diverso e importarli in archivio cacerts potrebbe essere simili.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Per aggiungere un certificato nell'archivio cacerts

1. Al prompt dei comandi che è impostato su cartella **jdk\jre\lib\security** del JDK, eseguire le operazioni seguenti per vedere quali certificati sono installati:

    `keytool -list -keystore cacerts`

    Verrà richiesto per l'archiviazione password. La password predefinita è **modificare**. (Se si desidera modificare la password, vedere la documentazione keytool <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>). In questo esempio si presuppone che il certificato con MD5 impronte digitali 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 non è elencato e che si desidera importarlo (è necessario questo certificato specifico dal servizio Twilio API).
2. Ottenere il certificato dall'elenco dei certificati elencati [GeoTrust certificati](http://www.geotrust.com/resources/root-certificates/). Pulsante destro del mouse sul collegamento per il certificato con numero seriale 35:DE:F4:CF e salvare la cartella **jdk\jre\lib\security** . Ai fini di questo esempio, è stato salvato in un file denominato **Equifax\_sicura\_certificato\_Authority.cer**.
3. Importare il certificato mediante il comando seguente:

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Quando viene richiesto di considerare attendibile il certificato, se il certificato prevede MD5 ID digitale 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, rispondere digitando **y**.
4. Eseguire il comando seguente per assicurarsi che il certificato è stato importato correttamente:

    `keytool -list -keystore cacerts`

5. Zip JDK e aggiungerlo alla cartella **approot** del progetto Azure.

Per informazioni su keytool, vedere <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificati Azure

Le applicazioni che utilizzano i servizi Azure (ad esempio Azure Service Bus) è necessario considerare attendibile il certificato Baltimora CyberTrust principale. (15 aprile 2013, a partire Azure è iniziata la migrazione da GTE CyberTrust Global Root a Baltimora CyberTrust Root. La migrazione ha diversi mesi completi.)

Baltimora certificato potrebbe essere già installato nel negozio cacerts così ricordarsi di eseguire il **keytool-elenco** comando prima di tutto per verificare se è già presente.

Se è necessario aggiungere Baltimora CyberTrust Root, ha numero seriale 02:00:00:b9 e SHA1 ID digitale d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Può essere scaricato dal <https://cacert.omniroot.com/bc2025.crt>, salvato in un file con estensione **cer**locale e quindi importati con **keytool** come illustrato sopra.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui certificati radice utilizzato da Azure, vedere informazioni sulla [Migrazione certificato radice di Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Per ulteriori informazioni su Java, vedere il [Centro per sviluppatori di linguaggio](/develop/java/).
