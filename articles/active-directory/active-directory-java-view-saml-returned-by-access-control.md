<properties
    pageTitle="Visualizzazione SAML restituito dal servizio controllo di accesso (Java)"
    description="Informazioni su come visualizzare SAML restituito dal servizio controllo di accesso nelle applicazioni Java ospitate in Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Come visualizzare SAML restituiti dal servizio controllo di accesso Azure

Questa guida illustra come visualizzare la sottostante protezione SAML Assertion Markup Language () restituito all'applicazione da Azure Access Control Service (ACS). La Guida si basa sull'argomento [come eseguire l'autenticazione Web gli utenti con Azure Access controllo servizio utilizzando Eclisse][] fornire codice che visualizza le informazioni di SAML. L'applicazione finita sarà simile al seguente.

![Output SAML di esempio][saml_output]

Per ulteriori informazioni su ACS, vedere la sezione [passaggi successivi](#next_steps) .

> [AZURE.NOTE]
> Il filtro di controllo di Azure Access Services è un'anteprima di tecnologia della community. Come software in versione preliminare, non formale è supportato da Microsoft.

## <a name="prerequisites"></a>Prerequisiti

Per completare le attività in questa Guida, completare l'esempio come [eseguire l'autenticazione Web gli utenti con Azure accesso controllo servizio utilizzando Eclisse][] e usarla come punto di partenza per questa esercitazione.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Aggiungere la libreria JspWriter all'assembly percorso e distribuzione di generazione

Aggiungere la raccolta che contiene la classe **javax.servlet.jsp.JspWriter** all'assembly percorso e distribuzione di compilazione. Se si utilizza Tomcat, la raccolta è **jsp api.jar**, che si trova nella cartella di **raccolta** Apache.

1. In Esplora progetti di Eclisse rapida **MyACSHelloWorld**, fare clic su **Creare percorso**, fare clic su **Configura creare percorso**, fare clic sulla scheda **raccolte** e quindi fare clic su **Aggiungi JARs esterni**.
2. Nella finestra di dialogo **Selezione JAR** esplorare VASO necessaria, selezionarlo e quindi fare clic su **Apri**.
3. Con la **proprietà per MyACSHelloWorld** finestra di dialogo ancora aperto, fare clic su **Assembly di distribuzione**.
4. Nella finestra di dialogo **Web distribuzione Assembly** , fare clic su **Aggiungi**.
5. Nella finestra di dialogo **Nuova direttiva Assembly** , fare clic su **Linguaggio creare percorso voci** e quindi fare clic su **Avanti**.
6. Selezionare la raccolta appropriata e fare clic su **Fine**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà per MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modificare il file JSP per visualizzare SAML

Modificare **index.jsp** per utilizzare il codice seguente.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Eseguire l'applicazione nell'emulatore di computer o la distribuzione di Azure, seguendo la procedura descritta in [come eseguire l'autenticazione Web gli utenti con Azure Access controllo servizio utilizzando Eclisse][].
2. Avviare un browser e aprire l'applicazione web. Dopo l'accesso all'applicazione, verrà visualizzato SAML informazioni quali l'asserzione di sicurezza fornito dal provider di identità.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori esplorare le funzionalità di ACS e per sperimentare scenari più sofisticati, vedere [2.0 Servizio controllo di accesso][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Servizio controllo di Access 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Modalità di autenticazione utenti Web con il servizio controllo di accesso Azure mediante Eclisse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 