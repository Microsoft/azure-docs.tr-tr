---
title: Store-SendGrid-Java-nasıl yapılır-Gönder-e-posta-örnek
description: Azure dağıtımında Java 'dan SendGrid kullanarak e-posta gönderme
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876476"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Azure dağıtımında Java 'dan SendGrid kullanarak e-posta gönderme
Aşağıdaki örnek, Azure 'da barındırılan bir Web sayfasından e-posta göndermek için SendGrid 'i nasıl kullanabileceğinizi gösterir. Ortaya çıkan uygulama, aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcıdan e-posta değerlerini ister.

![E-posta formu][emailform]

Elde edilen e-posta aşağıdaki ekran görüntüsüne benzer şekilde görünür.

![E-posta iletisi][emailsent]

Bu konudaki kodu kullanmak için aşağıdakileri yapmanız gerekir:

1. Örneğin, javax. Mail JARs 'ı edinin <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. JARs 'ı Java derleme yolunuza ekleyin.
3. Bu Java uygulamasını oluşturmak için tutulma kullanıyorsanız, tutulma dağıtım derleme özelliğini kullanarak uygulama dağıtım dosyanıza (WAR) SendGrid kitaplıklarını dahil edebilirsiniz. Bu Java uygulamasını oluşturmak için tutulma kullanmıyorsanız, kitaplıkların Java uygulamanızla aynı Azure rolüne eklendiğinden ve uygulamanızın sınıf yoluna eklendiğinden emin olun.

E-postayı gönderebilmeniz için kendi SendGrid Kullanıcı adınız ve parolanız de olmalıdır. SendGrid 'i kullanmaya başlamak için bkz. [Java 'Dan SendGrid kullanarak e-posta gönderme](store-sendgrid-java-how-to-send-email.md).

Ayrıca, [Azure için Merhaba Dünya uygulama oluşturma](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)konusunda bilgi sahibi olan veya Azure 'da Azure 'da Java uygulamalarının barındırılmasına yönelik diğer tekniklerin yanı sıra, çakışan küreler kullanılması önerilir.

## <a name="create-a-web-form-for-sending-email"></a>E-posta göndermek için bir Web formu oluşturun
Aşağıdaki kod, e-posta göndermek için Kullanıcı verilerini almak üzere bir Web formu oluşturmayı gösterir. Bu içeriğin amaçları doğrultusunda, JSP dosyası **emailform. jsp**olarak adlandırılır.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-send-the-email"></a>E-postayı göndermek için kod oluşturma
Aşağıdaki kod, emailform. jsp içindeki formu tamamladığınızda çağrılır, e-posta iletisini oluşturur ve gönderir. Bu içeriğin amaçları doğrultusunda, JSP dosyası **SendEmail. jsp**olarak adlandırılır.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

Emailform. jsp, e-posta göndermeye ek olarak Kullanıcı için bir sonuç sağlar; Aşağıdaki ekran görüntüsünde örnek bir örnektir:

![Posta sonucu gönder][emailresult]

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı işlem öykünücüsüne dağıtın ve bir tarayıcı içinde emailform. JSP çalıştırın, forma değer girin, **Bu e-postayı Gönder ' e**tıklayın ve ardından SendEmail. jsp içindeki sonuçlara bakın.

Bu kod, Azure 'da Java 'da SendGrid 'in nasıl kullanılacağını göstermek için verilmiştir. Üretim sırasında Azure 'a dağıtım yapmadan önce, daha fazla hata işleme veya diğer özellik eklemek isteyebilirsiniz. Örneğin: 

* Web formu kullanmak yerine e-posta adreslerini ve e-posta iletilerini depolamak için Azure Storage bloblarını veya SQL veritabanını kullanabilirsiniz. Java 'da Azure Storage bloblarını kullanma hakkında daha fazla bilgi için bkz. [Java 'Dan blob Storage hizmetini kullanma](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Java 'da SQL veritabanı kullanma hakkında daha fazla bilgi için bkz. [Java 'DA SQL veritabanı kullanma](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Java 'da SendGrid kullanma hakkında daha fazla bilgi için bkz. [Java 'Dan SendGrid kullanarak e-posta gönderme](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
