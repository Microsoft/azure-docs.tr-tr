---
title: IBM MQ sunucusuna bağlan
description: Azure veya şirket içi IBM MQ Server ve Azure Logic Apps ileti gönderin ve alın
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 03/10/2021
tags: connectors
ms.openlocfilehash: a07eb6e592c68794f0e4038a7cf9a42bd396b47a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495241"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps'ten IBM MQ sunucusuna bağlanma

MQ Bağlayıcısı, şirket içinde veya Azure 'da bir MQ sunucusunda depolanan iletileri gönderir ve alır. Bu bağlayıcı bir TCP/IP ağı üzerinde uzak IBM MQ sunucusuyla iletişim kuran bir Microsoft MQ istemcisi içerir. Bu makale, MQ bağlayıcısını kullanmak için bir başlangıç kılavuzu sağlar. Bir kuyruktaki tek bir iletiye göz atarak başlayabilir ve ardından diğer eylemleri deneyebilirsiniz.

MQ Bağlayıcısı bu eylemleri içerir, ancak hiçbir tetikleyici sağlamaz:

- MQ sunucusundan iletiyi silmeden tek bir iletiye gözatın.
- MQ sunucusundan iletileri silmeden bir toplu işe gözatamazsınız.
- Tek bir ileti alın ve MQ sunucusundan iletiyi silin.
- Bir toplu ileti alın ve MQ sunucusundan iletileri silin.
- MQ sunucusuna tek bir ileti gönderin.

Resmi olarak desteklenen IBM WebSphere MQ sürümleri aşağıda verilmiştir:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0
  * MQ 9,1

## <a name="prerequisites"></a>Önkoşullar

* Şirket içi MQ sunucusu kullanıyorsanız, Şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-install.md) ağınız içindeki bir sunucuya yüklemeniz gerekir.

  > [!NOTE]
  > MQ sunucunuz herkese açık veya Azure 'da kullanılabiliyorsa, veri ağ geçidini kullanmanız gerekmez.

  * MQ bağlayıcısının çalışması için, şirket içi veri ağ geçidini yüklediğiniz sunucuda .NET Framework 4,6 yüklü olması gerekir.
  
  * Şirket içi veri ağ geçidini yükledikten sonra, MQ Bağlayıcısı 'nın şirket içi MQ sunucunuza erişmek için kullandığı [Şirket içi veri ağ geçidi için bir Azure ağ geçidi kaynağı oluşturmanız](../logic-apps/logic-apps-gateway-connection.md) da gerekir.

* MQ bağlayıcısını kullanmak istediğiniz mantıksal uygulama. MQ bağlayıcısının hiçbir tetikleyicisi yoktur, bu nedenle önce mantıksal uygulamanıza bir tetikleyici eklemeniz gerekir. Örneğin, [yinelenme tetikleyicisini](../connectors/connectors-native-recurrence.md)kullanabilirsiniz. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturmak için bu hızlı](../logic-apps/quickstart-create-first-logic-app-workflow.md)başlangıcı deneyin.

## <a name="limitations"></a>Sınırlamalar

MQ Bağlayıcısı, iletinin **Biçim** alanını desteklemez veya kullanmaz ve herhangi bir karakter kümesi dönüştürmesi gerçekleştirmez. Bağlayıcı, ileti alanında her türlü verinin bir JSON iletisine görünmesini ve iletiyi birlikte göndereceğini ekler.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ bağlantısı oluştur

Bir MQ eylemi eklediğinizde henüz MQ bağlantınız yoksa, bağlantıyı oluşturmanız istenir, örneğin:

![Bağlantı bilgilerini girin](media/connectors-create-api-mq/connection-properties.png)

1. Şirket içi MQ sunucusuna bağlanıyorsanız Şirket **içi veri ağ geçidi üzerinden Bağlan**' ı seçin.

1. MQ sunucunuz için bağlantı bilgilerini sağlayın.

   * **Sunucu** için MQ sunucu adını gırebılır veya IP adresini, ardından iki nokta üst üste ve bağlantı noktası numarası ile girebilirsiniz.

   * Aktarım Katmanı Güvenliği (TLS) veya Güvenli Yuva Katmanı (SSL) kullanmak için SSL 'yi **Etkinleştir**' i seçin.

     MQ Bağlayıcısı Şu anda yalnızca sunucu kimlik doğrulamasını destekler, istemci kimlik doğrulamasını desteklememektedir. Daha fazla bilgi için bkz. [bağlantı ve kimlik doğrulama sorunları](#connection-problems).

1. **Ağ geçidi** bölümünde şu adımları izleyin:

   1. **Abonelik** listesinden Azure Gateway kaynağınız Ile ilişkili Azure aboneliğini seçin.

   1. **Bağlantı ağ geçidi** listesinden, kullanmak istediğiniz Azure ağ geçidi kaynağını seçin.

1. İşiniz bittiğinde **Oluştur**'u seçin.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Bağlantı ve kimlik doğrulama sorunları

Mantıksal uygulamanız şirket içi MQ sunucunuza bağlanmayı denediğinde şu hatayı alabilirsiniz:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* MQ bağlayıcısını doğrudan Azure 'da kullanıyorsanız, MQ sunucusunun Güvenilen bir [sertifika yetkilisi](https://www.ssl.com/faqs/what-is-a-certificate-authority/)tarafından verilen bir sertifika kullanması gerekir.

* Şirket içi veri ağ geçidini kullanıyorsanız, mümkün olduğunda güvenilir bir [sertifika yetkilisi](https://www.ssl.com/faqs/what-is-a-certificate-authority/) tarafından verilen bir sertifika kullanmayı deneyin. Ancak, bu seçenek mümkün değilse, güvenilir bir [sertifika yetkilisi](https://www.ssl.com/faqs/what-is-a-certificate-authority/) tarafından verilmemiş ve daha az güvenli olarak kabul edilen otomatik olarak imzalanan bir sertifika kullanabilirsiniz.

  Sunucunun otomatik olarak imzalanan sertifikasını yüklemek için, **Windows Sertifika Yöneticisi** (certmgr. msc) aracını kullanabilirsiniz. Bu senaryo için, şirket içi veri ağ geçidi hizmetinin çalıştığı yerel bilgisayarınızda sertifikayı, **Güvenilen kök sertifika yetkilileri** düzeyinde **Yerel bilgisayar** sertifikaları deponuza yüklemeniz gerekir.

  1. Şirket içi veri ağ geçidi hizmetinin çalıştığı bilgisayarda, Başlat menüsünü açın, **Kullanıcı sertifikalarını Yönet**' i bulun ve seçin.

  1. Windows Sertifika Yöneticisi aracı açıldıktan sonra, **Sertifikalar-Yerel bilgisayar**  >   **Güvenilen kök sertifika yetkilileri** klasörüne gidin ve sertifikayı yükler.

     > [!IMPORTANT]
     > Sertifikayı **Sertifikalar-Yerel bilgisayar**  >  **Güvenilen kök sertifika yetkilileri** deposuna yüklediğinizden emin olun.

* MQ sunucusu, TLS/SSL bağlantıları için kullanmak istediğiniz şifre belirtimini tanımlamanızı gerektirir. Ancak, .NET 'teki SslStream, şifre belirtimlerinin sırasını belirtmenize izin vermez. Bu sınırlamaya geçici bir çözüm için, MQ sunucu yapılandırmanızı, bağlayıcının TLS/SSL anlaşmasıyla gönderdiği paketteki ilk şifre belirtimiyle eşleşecek şekilde değiştirebilirsiniz.

  Bağlantıyı denediğinizde, MQ sunucusu, bağlantının başarısız olduğunu belirten bir olay iletisini günlüğe kaydeder çünkü diğer bitiş yanlış şifre belirtimini kullandı. Olay iletisi, listede ilk görüntülenen şifre belirtimini içerir. Kanal yapılandırmasındaki şifre belirtimini, olay iletisindeki şifre belirtimine uyacak şekilde güncelleştirin.

## <a name="browse-single-message"></a>Tek iletiye gözatamıyorum

1. Mantıksal uygulamanızda, tetikleyici veya başka bir eylem altında **yeni adım**' ı seçin.

1. Arama kutusuna girin `mq` ve **iletiyi görüntüle** eylemini seçin.

   !["İletiye gözatıp" eylemini seçin](media/connectors-create-api-mq/browse-message.png)

1. Henüz bir MQ bağlantısı oluşturmadıysanız [Bu bağlantıyı oluşturmanız](#create-connection)istenir.

1. Bağlantıyı oluşturduktan sonra, **Iletiye gözatatıon** eyleminin özelliklerini ayarlayın:

   | Özellik | Açıklama |
   |----------|-------------|
   | **Kuyruk** | Bağlantıda belirtilen kuyruktan farklıysa, bu kuyruğu belirtin. |
   | **MessageID**, **bağıntıkimliği**, **GroupID** ve diğer özellikler | Farklı MQ İleti özelliklerine dayalı bir iletiye gözatın |
   | **Includeınfo** | Çıkışa ek ileti bilgilerini eklemek için **doğru** öğesini seçin. Çıktıda ek ileti bilgisini atlamak için, **false**' ı seçin. |
   | **Aş** | Bir iletinin boş bir sıraya gelmesi için ne kadar bekleneceğini öğrenmek için bir değer girin. Hiçbir şey girilmişse, sıradaki ilk ileti alınır ve bir iletinin görünmesini beklerken zaman harcanması beklenmez. |
   |||

   Örnek:

   !["İletiye gözatamıyorum" eylemi için Özellikler](media/connectors-create-api-mq/browse-message-properties.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin. Uygulamanızı test etmek için **Çalıştır**' ı seçin.

   Çalıştırma bittikten sonra, tasarımcı, çıktıyı gözden geçirebilmeniz için iş akışı adımlarını ve bunların durumlarını gösterir.

1. Her adımın ayrıntılarını görüntülemek için adımın başlık çubuğuna tıklayın. Bir adımın çıktısı hakkında daha fazla bilgi için **Ham çıkışları göster**' i seçin.

   ![İleti çıktısına gözatıp](media/connectors-create-api-mq/browse-message-output.png)

   Örnek ham çıktı aşağıda verilmiştir:

   ![İletiye yönelik ham çıkışa gözatıp](media/connectors-create-api-mq/browse-message-raw-output.png)

1. **Includeınfo** **değerini true** olarak ayarlarsanız ek çıktı gösterilir:

   ![İleti ekleme bilgisini görüntüle](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Birden çok iletiye gözatın

**Iletileri gözden** geçirme eylemi, kuyruktan kaç tane ileti dönemeyeceğini göstermek Için bir **BatchSize** seçeneği içerir. **BatchSize** değeri yoksa, tüm iletiler döndürülür. Döndürülen çıktı bir ileti dizisidir.

1. Önceki adımları izleyin, ancak bunun yerine **Iletileri görüntüle** eylemini ekleyin.

1. Henüz bir MQ bağlantısı oluşturmadıysanız [Bu bağlantıyı oluşturmanız](#create-connection)istenir. Aksi takdirde, varsayılan olarak, önceden yapılandırılmış ilk bağlantı kullanılır. Yeni bir bağlantı oluşturmak için **Bağlantıyı Değiştir**' i seçin. Ya da farklı bir bağlantı seçin.

1. Eyleme ilişkin bilgileri belirtin.

1. Mantıksal uygulamayı kaydedin ve çalıştırın.

   Mantıksal uygulama çalışmayı bitirdikten sonra, **Iletilere gözatamıyorum** eyleminin bazı örnek çıktıları aşağıda verilmiştir:

   ![Örnek "iletilere gözatamıyorum" çıkışı](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Tek ileti al

**Ileti al** eylemi, **ileti görüntüle** eylemiyle aynı girişlere ve çıkışlara sahiptir. **Iletiyi al**' ı kullandığınızda ileti kuyruktan silinir.

## <a name="receive-multiple-messages"></a>Birden çok ileti alma

**Iletileri al** eylemi, **iletileri görüntüle** eylemiyle aynı giriş ve çıkışlara sahiptir. **Iletileri al**' ı kullandığınızda iletiler kuyruktan silinir.

> [!NOTE]
> Herhangi bir ileti içermeyen bir kuyrukta bir tarama veya alma eylemi çalıştırırken, eylem bu çıktıda başarısız olur:
>
> ![MQ "ileti yok" hatası](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>İleti gönder

1. Önceki adımları izleyin, ancak bunun yerine **Ileti gönder** eylemini ekleyin.

1. Henüz bir MQ bağlantısı oluşturmadıysanız [Bu bağlantıyı oluşturmanız](#create-connection)istenir. Aksi takdirde, varsayılan olarak, önceden yapılandırılmış ilk bağlantı kullanılır. Yeni bir bağlantı oluşturmak için **Bağlantıyı Değiştir**' i seçin. Ya da farklı bir bağlantı seçin.

1. Eyleme ilişkin bilgileri belirtin. **MessageType** için geçerli bir ileti türü seçin: **veri birimi**, **Yanıt** veya **istek**

   !["İleti gönder eylemi" özellikleri](media/connectors-create-api-mq/send-message-properties.png)

1. Mantıksal uygulamayı kaydedin ve çalıştırın.

   Mantıksal uygulama çalışmayı bitirdikten sonra **Ileti gönder** eyleminden alınan bazı örnek çıktıları aşağıda verilmiştir:

   ![Örnek "ileti gönder" çıkışı](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Swagger dosyasında açıklanan eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasını](/connectors/mq/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
