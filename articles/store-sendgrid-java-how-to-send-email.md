---
title: SendGrid e-posta hizmetini kullanma (Java) | Microsoft Docs
description: Azure 'da SendGrid e-posta hizmeti ile e-posta gönderme hakkında bilgi edinin. Java 'da yazılan kod örnekleri.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876521"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Java 'dan SendGrid kullanarak e-posta gönderme
Bu kılavuzda, Azure 'da SendGrid e-posta hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Örnekler Java dilinde yazılmıştır. Kapsanan senaryolar, **e-posta**oluşturma **, e-posta gönderme**, **ekleri ekleme**, **filtreleri kullanma**ve **Özellikleri güncelleştirme**içerir. SendGrid ve e-posta gönderme hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid e-posta hizmeti nedir?
SendGrid, özel tümleştirmeyi kolaylaştıran esnek API 'lerle birlikte güvenilir [işlem e-posta teslimi], ölçeklenebilirlik ve gerçek zamanlı çözümlemeler sağlayan [bulut tabanlı bir e-posta hizmetidir] . Ortak SendGrid kullanım senaryoları şunları içerir:

* Müşterilere alındıları otomatik olarak gönderme
* Aylık e-Fliers ve özel teklifler göndermek için dağıtım listelerini yönetme
* Engellenen e-posta ve müşteri yanıt verme gibi şeyler için gerçek zamanlı ölçümler toplama
* Eğilimleri belirlemesine yardımcı olmak için raporlar oluşturma
* Müşteri sorgularını iletme
* Uygulamanızdan e-posta bildirimleri

Daha fazla bilgi için bkz. <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>SendGrid hesabı oluşturma
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Nasıl yapılır: Javax. Mail kitaplıklarını kullanma
JavaScript gibi <https://www.oracle.com/technetwork/java/javamail> javax. Mail kitaplıklarını edinin ve kodunuza içeri aktarın. Yüksek düzeyde, JavaScript kullanarak e-posta göndermek için javax. Mail kitaplığını kullanmaya yönelik işlem şunları yapmanız gerekir:

1. SendGrid için smtp.sendgrid.net olduğu SMTP sunucusu da dahil olmak üzere SMTP değerlerini belirtin.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. *Javax. mail. Authenticator* sınıfını genişletin ve *Getpasswordauthentication* yöntemi uygulamanızda, SendGrid Kullanıcı adınızı ve parolanızı döndürün.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Bir *javax. mail. Session* nesnesi aracılığıyla kimliği doğrulanmış bir e-posta oturumu oluşturun.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. İletinizi oluşturun ve, **Kimden**, **Konu** ve içerik **değerlerini atayın.** Bu, [nasıl yapılır: E-posta](#how-to-create-an-email) bölümü oluşturun.
4. İletiyi bir *javax. mail. Transport* nesnesi ile gönderin. Bu, [Nasıl yapılır: E-posta gönder] [#how-Gönder-e-posta] bölümü.

## <a name="how-to-create-an-email"></a>Nasıl yapılır: E-posta oluştur
Aşağıda, bir e-posta için değerlerin nasıl ayarlanacağı gösterilmektedir.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Nasıl yapılır: E-posta gönder
Aşağıda, bir e-postanın nasıl gönderileceği gösterilmektedir.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Nasıl yapılır: Ek ekleme
Aşağıdaki kod, bir ekin nasıl ekleneceğini gösterir.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Nasıl yapılır: Altbilgileri, izlemeyi ve analizlerini etkinleştirmek için filtreleri kullanın
SendGrid, *filtrelerin*kullanımı aracılığıyla ek e-posta işlevselliği sağlar. Bunlar, tıklama izleme, Google Analytics, abonelik izleme gibi belirli işlevleri etkinleştirmek için bir e-posta iletisine eklenebilen ayarlardır. Filtrelerin tam listesi için bkz. [filtre ayarları][Filter Settings].

* Aşağıda, gönderilen e-postanın altında görüntülenen HTML metni ile sonuçlanan bir altbilgi filtresinin nasıl ekleneceği gösterilmektedir.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Bir filtrenin başka bir örneği de izleme ' ye tıklayın. E-posta metninizin aşağıdakiler gibi bir köprü içerdiğini ve tıklama oranını izlemek istediğinizi varsayalım:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Tıklama izlemeyi etkinleştirmek için aşağıdaki kodu kullanın:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Nasıl yapılır: E-posta özelliklerini güncelleştir
**Set özelliği** kullanılarak bazı e-posta özelliklerinin üzerine yazılabilir veya **Ekle özelliği**kullanılarak eklenmiş olabilir.

Örneğin, **ReplyTo** adreslerini belirtmek için aşağıdakileri kullanın:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Bir **bilgi** alıcısı eklemek için aşağıdakileri kullanın:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Nasıl yapılır: Ek SendGrid Hizmetleri kullanma
SendGrid, Azure uygulamanızdan ek SendGrid işlevlerinden yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [SendGrid API belgelerine][SendGrid API documentation]bakın.

## <a name="next-steps"></a>Sonraki adımlar
SendGrid e-posta hizmetinin temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

* Bir Azure dağıtımında SendGrid kullanmayı gösteren örnek: [Azure dağıtımında Java 'dan SendGrid kullanarak e-posta gönderme](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK 'Sı:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API belgeleri:<https://sendgrid.com/docs/API_Reference/index.html>
* Azure müşterileri için SendGrid özel teklifi:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[bulut tabanlı bir e-posta hizmetidir]: https://sendgrid.com/email-solutions
[işlem e-posta teslimi]: https://sendgrid.com/transactional-email
