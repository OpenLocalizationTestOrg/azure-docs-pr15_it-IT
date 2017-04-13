<properties 
    pageTitle="Store-sendgrid-Java-How-to-Send-email-example" 
    description="Come inviare posta elettronica con SendGrid da Java in una distribuzione di Azure" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Come inviare posta elettronica con SendGrid da Java nella distribuzione di Windows Azure

Nell'esempio seguente mostra come è possibile utilizzare SendGrid per inviare messaggi di posta elettronica da una pagina web ospitato in Azure. L'applicazione risultante richiederà l'utente per i valori di posta elettronica, come illustrato nella schermata seguente.

![Modulo di posta elettronica][emailform]

Messaggio di posta elettronica risultante sarà simile alla schermata seguente.

![Messaggio di posta elettronica][emailsent]

È necessario eseguire le operazioni seguenti per utilizzare il codice in questo argomento:

1. Presso vasetti da javax. Mail, ad esempio <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Aggiungere il JAR al percorso di compilazione di linguaggio.
3. Se si utilizza Eclisse per creare l'applicazione di linguaggio, è possibile includere le librerie SendGrid nel file di distribuzione dell'applicazione (WAR) funzionalità del Eclisse distribuzione assembly. Se non si usa Eclisse per creare l'applicazione di linguaggio, assicurarsi che le raccolte vengono inclusi nello stesso ruolo Azure di applicazione Java e aggiunti al percorso della classe dell'applicazione.


Devono inoltre includere il proprio nome utente SendGrid e la password, per poter inviare il messaggio di posta elettronica. Per iniziare a utilizzare SendGrid, vedere [come inviare posta elettronica utilizzando SendGrid da Java](store-sendgrid-java-how-to-send-email.md).

Inoltre, è consigliabile avere familiarità con le informazioni di [creazione di un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690944)o con altre tecniche per l'hosting applicazioni Java in Azure, se non si usa Eclisse.

## <a name="create-a-web-form-for-sending-email"></a>Creare una maschera web per l'invio di posta elettronica

Il codice seguente viene illustrato come creare una maschera web per recuperare i dati utente per l'invio di posta elettronica. Ai fini del contenuto, il file JSP è denominato **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Creare il codice per inviare il messaggio di posta elettronica

Il codice seguente, che rappresenta una volta completato il modulo in emailform.jsp, Crea messaggio di posta elettronica e la invia. Ai fini del contenuto, il file JSP è denominato **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Oltre a inviare il messaggio di posta elettronica, emailform.jsp fornisce un risultato per l'utente. un esempio è l'immagine riportata di seguito:

![Inviare il risultato di posta elettronica][emailresult]

## <a name="next-steps"></a>Passaggi successivi

Distribuire un'applicazione all'emulatore di calcolo e all'interno di un browser eseguire emailform.jsp, immettere i valori nel modulo, fare clic su **Invia questo messaggio di posta elettronica**e quindi visualizzare i risultati in sendemail.jsp.

Questo codice è stato fornito in cui viene illustrato come utilizzare SendGrid Java in Azure. Prima di distribuire in Azure in produzione, è consigliabile aggiungere ulteriori la gestione degli errori o altre caratteristiche. Per esempio: 

* È possibile utilizzare BLOB Azure dello spazio di archiviazione o Database SQL per archiviare gli indirizzi di posta elettronica e messaggi di posta elettronica, invece di usare una maschera web. Per informazioni sull'utilizzo BLOB Azure dello spazio di archiviazione in linguaggio, vedere [come utilizzare il servizio di archiviazione Blob da Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Per informazioni sull'uso di Database SQL di linguaggio, vedere [Utilizzo di Database di SQL in linguaggio](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* È possibile utilizzare `RoleEnvironment.getConfigurationSettings` per recuperare il nome utente SendGrid e la password dalle impostazioni di configurazione della distribuzione, invece di usare il modulo web per recuperare i valori. Per informazioni sul `RoleEnvironment` per la classe, vedere [utilizzo della libreria di Runtime del servizio Azure in JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) e documentazione del pacchetto di Runtime del servizio di Azure <http://dl.windowsazure.com/javadoc>.
* Per ulteriori informazioni sull'utilizzo di SendGrid in linguaggio, vedere [come inviare posta elettronica utilizzando SendGrid da Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
