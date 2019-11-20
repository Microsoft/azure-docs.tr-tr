---
title: Twilio 'dan telefon görüşmesi yapma (Java) | Microsoft Docs
description: Azure 'da Java uygulamasında Twilio kullanarak bir Web sayfasından telefon araması yapmayı öğrenin.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838547"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Azure 'da Java uygulamasında Twilio kullanarak telefon araması yapma
Aşağıdaki örnekte, Azure 'da barındırılan bir Web sayfasından çağrı yapmak için Twilio nasıl kullanabileceğiniz gösterilmektedir. Elde edilen uygulama, aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcıdan telefon araması değerlerini ister.

![Twilio ve Java kullanan Azure çağrı formu][twilio_java]

Bu konudaki kodu kullanmak için aşağıdakileri yapmanız gerekir:

1. Twilio hesabı ve kimlik doğrulama belirteci alın. Twilio kullanmaya başlamak için [https://www.twilio.com/pricing][twilio_pricing]sırasındaki fiyatlandırmayı değerlendirin. [https://www.twilio.com/try-twilio][try_twilio]' de kaydolabilirsiniz. Twilio tarafından sunulan API hakkında daha fazla bilgi için bkz. [https://www.twilio.com/api][twilio_api].
2. Twilio JAR 'i edinin. [https://github.com/twilio/twilio-java][twilio_java_github], GitHub kaynaklarını indirebilir ve kendi jar 'nizi oluşturabilir veya önceden oluşturulmuş bir jar indirebilirsiniz (bağımlılıkları olan veya olmayan).
   Bu konudaki kod önceden oluşturulmuş TwilioJava-3.3.8-Dependencies JAR kullanılarak yazılmıştır.
3. JAR 'yi Java derleme yolunuza ekleyin.
4. Bu Java uygulamasını oluşturmak için çakışan küreler kullanıyorsanız, Twilio JAR 'yi, tutulma 'in dağıtım derleme özelliğini kullanarak uygulama dağıtım dosyanıza (WAR) dahil edin. Bu Java uygulamasını oluşturmak için çakışan küreler kullanmıyorsanız, Twilio JAR 'in Java uygulamanızla aynı Azure rolüne eklendiğinden ve uygulamanızın sınıf yoluna eklendiğinden emin olun.
5. CAcert anahtar deposu 'larınızın MD5 parmak izi 67 olan Equifax güvenli sertifika yetkilisi sertifikasını içerdiğinden emin olun: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1e: D1:1B: EC: D4 (seri numarası 35: de: F4: CF ve SHA1 parmak izi D2:32:09: ad: 23: D 3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3A). Bu, Twilio API 'Lerini kullandığınızda çağrılan [https://api.twilio.com][twilio_api_service] hizmeti için sertifika YETKILISI (CA) sertifikasıdır. Bu CA sertifikasını JDK 'nin CAcert deposuna ekleme hakkında daha fazla bilgi için bkz. [Java CA sertifika deposuna sertifika ekleme][add_ca_cert].

Ayrıca, [Azure Toolkit for Eclipse kullanarak Merhaba Dünya uygulama oluşturma][azure_java_eclipse_hello_world]hakkında bilgi sahibi veya Azure 'Da, çakışan küreler kullanmıyorsanız Java uygulamalarını barındırmak için kullanabileceğiniz diğer teknikler hakkında bilgi sahibi olmanız önerilir.

## <a name="create-a-web-form-for-making-a-call"></a>Çağrı yapmak için bir Web formu oluşturma
Aşağıdaki kod, bir çağrı yapmak için Kullanıcı verilerini almak üzere bir Web formu oluşturmayı gösterir. Bu örneğin amaçları doğrultusunda, **Twıocyüksek**adlı yeni bir dinamik Web projesi oluşturulmuştur ve **callform. JSP** bir JSP dosyası olarak eklenmiştir.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Çağrıyı yapmak için kodu oluşturma
Kullanıcı callform. jsp tarafından görünen formu tamamladığında çağrılan aşağıdaki kod, çağrı iletisini oluşturur ve çağrıyı oluşturur. Bu örneğin amaçları doğrultusunda, JSP dosyası **MakeCall. jsp** olarak adlandırılır ve **twıocı** projesine eklenmiştir. (Aşağıdaki kodda **Accountsıd** ve **authToken** 'a atanan yer tutucu değerleri yerine Twilio hesabınızı ve kimlik doğrulama belirtecinizi kullanın.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

MakeCall. jsp çağrısı yapmanın yanı sıra Twilio uç noktasını, API sürümünü ve çağrı durumunu görüntüler. Aşağıdaki ekran görüntüsünde örnek bir örnektir:

![Twilio ve Java kullanarak Azure çağrı yanıtı][twilio_java_response]

## <a name="run-the-application"></a>Uygulamayı çalıştırma
Uygulamanızı çalıştırmak için üst düzey adımlar aşağıda verilmiştir; Bu adımların ayrıntıları [Azure Toolkit for Eclipse kullanılarak Merhaba Dünya uygulama oluşturma][azure_java_eclipse_hello_world]konusunda bulunabilir.

1. Twıocyüksek WAR dosyanızı Azure **AppRoot** klasörüne aktarın. 
2. Twıocyüksek WAR 'nizi açmak için **Startup. cmd** dosyasını değiştirin.
3. Uygulamanızı işlem öykünücüsü için derleyin.
4. İşlem öykünücüsünde dağıtımınızı başlatın.
5. Bir tarayıcı açın ve `http://localhost:8080/TwilioCloud/callform.jsp`çalıştırın.
6. Forma değer girin, **Bu çağrıyı yap**' a tıklayın ve ardından MakeCall. jsp içindeki sonuçları görüntüleyin.

Azure 'a dağıtmaya hazırsanız, buluta dağıtım için yeniden derleyin, Azure 'a dağıtın ve tarayıcıda http://*your_hosted_name*. cloudapp.net/TwilioCloud/callform.jsp ' yi çalıştırın ( *your_hosted_name*için değerini değiştirin).

## <a name="next-steps"></a>Sonraki adımlar
Bu kod, Azure 'da Java 'da Twilio kullanarak temel işlevselliği göstermek için verilmiştir. Üretim sırasında Azure 'a dağıtım yapmadan önce, daha fazla hata işleme veya diğer özellik eklemek isteyebilirsiniz. Örneğin:

* Bir Web formu kullanmak yerine, telefon numaralarını depolamak ve metin çağırmak için Azure Storage bloblarını veya SQL veritabanını kullanabilirsiniz. Java 'da Azure Storage bloblarını kullanma hakkında daha fazla bilgi için bkz. [Java 'Dan blob Storage hizmetini kullanma][howto_blob_storage_java]. 
* Twilio hesap KIMLIĞI ve kimlik doğrulama belirtecini, dağıtımınızın yapılandırma ayarlarından almak için, MakeCall. jsp içindeki değerleri sabit kodlamak yerine **Roleenvironment. getConfigurationSettings** ' i kullanabilirsiniz. **Roleenvironment** sınıfı hakkında daha fazla bilgi için bkz. [JSP 'de Azure hizmeti çalışma zamanı kitaplığını kullanma][azure_runtime_jsp].
* MakeCall. jsp kodu, **URL** değişkenine Twilio tarafından SAĞLANMıŞ bir URL [https://twimlets.com/message][twimlet_message_url]atar. Bu URL, Twilio biçimlendirme dili (TwiML) yanıtını sağlar ve bu çağrı ile nasıl devam edeceğine Twilio. Örneğin, döndürülen TwiML, metnin çağrı alıcısına konuşulmasına neden olan **&gt;fiil&lt;** içerebilir. Twilio tarafından sağlanmış URL 'yi kullanmak yerine, Twilio 'ın isteğine yanıt vermek için kendi hizmetinizi oluşturabilirsiniz; daha fazla bilgi için bkz. [Java 'Daki Voice ve SMS özellikleri Için Twilio kullanma][howto_twilio_voice_sms_java]. TwiML hakkında daha fazla bilgi [https://www.twilio.com/docs/api/twiml][twiml]' de bulunabilir ve&lt;hakkında daha fazla bilgi **&gt;** ve diğer Twilio fiiller [https://www.twilio.com/docs/api/twiml/say][twilio_say]adresinde bulunabilir.
* [https://www.twilio.com/docs/security][twilio_docs_security]adresindeki Twilio güvenlik yönergelerini okuyun.

Twilio hakkında daha fazla bilgi için bkz. [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Ayrıca Bkz.
* [Java 'daki Voice ve SMS özellikleri için Twilio kullanma][howto_twilio_voice_sms_java]
* [Java CA sertifika deposuna sertifika ekleme][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
