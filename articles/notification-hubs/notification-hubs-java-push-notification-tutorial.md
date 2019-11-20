---
title: Java ile Notification Hubs kullanma
description: Azure Notification Hubs 'yi bir Java arka ucundan nasıl kullanacağınızı öğrenin.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 532ffc7a7393f016f27264b67b4ee5d3e6e5888f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213215"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Java 'dan Notification Hubs kullanma

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Bu konuda, yeni tam olarak desteklenen resmi Azure Notification Hub Java SDK 'sının temel özellikleri açıklanmaktadır.
Bu proje açık kaynaklı bir projem ve [Java SDK]'sindeki SDK kodunun tamamını görüntüleyebilirsiniz.

Genel olarak, bir Java/PHP/Python/Ruby arka ucundan tüm Notification Hubs özelliklerine, MSDN konusunda [NOTIFICATION HUBS REST API 'lerinde](https://msdn.microsoft.com/library/dn223264.aspx)açıklandığı gibi Bildirim Hub 'ı Rest arabirimini kullanarak erişebilirsiniz. Bu Java SDK 'Sı, Java 'daki bu REST arabirimleri üzerinde ince bir sarmalayıcı sağlar.

SDK Şu anda şunları destekler:

* Notification Hubs CRUD
* Kayıtlarda CRUD
* Yükleme yönetimi
* İçeri/dışarı aktarma kayıtları
* Normal gönderir
* Zamanlanan gönderimler
* Java NGÇ aracılığıyla zaman uyumsuz işlemler
* Desteklenen platformlar: APNS (iOS), FCM (Android), WNS (Windows Mağazası uygulamaları), MPNS (Windows Phone), ADM (Amazon MPR Le Fire), Baidu (Google hizmetleri olmadan Android)

## <a name="sdk-usage"></a>SDK kullanımı

### <a name="compile-and-build"></a>Derle ve derle

[Maven] kullanma

Oluşturmak için:

    mvn package

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>Notification Hub 'ı CRUDs

**NamespaceManager oluşturma:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Bildirim Hub 'ı oluştur:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 OR

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Bildirim Hub 'ı al:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Bildirim Hub 'ını güncelleştir:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Bildirim Hub 'ını Sil:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Kayıt CRUDs

**Bir Bildirim Hub 'ı istemcisi oluşturun:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Windows kaydı oluştur:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**İOS kaydı oluşturma:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Benzer şekilde, Android (FCM), Windows Phone (MPNS) ve MPR Le Fire (ADM) için kayıtlar oluşturabilirsiniz.

**Şablon kaydı oluştur:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Kayıt KIMLIĞI oluştur + upsert model kullanarak kayıt oluşturma:**

Kayıt kimliklerini cihazda depoluyorsanız kayıp yanıtları nedeniyle yinelenenleri kaldırır:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Kayıtları güncelleştir:**

    ```java
    hub.updateRegistration(reg);
    ```

**Kayıtları Sil:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Sorgu kayıtları:**

* **Tek kayıt al:**

    ```java
    hub.getRegistration(regid);
    ```

* **Hub 'daki tüm kayıtları al:**

    ```java
    hub.getRegistrations();
    ```

* **Etikete sahip kayıtları al:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Kanala göre kayıtları al:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Tüm koleksiyon sorguları $top ve devamlılık belirteçlerini destekler.

### <a name="installation-api-usage"></a>Yükleme API 'SI kullanımı

Yükleme API 'SI, kayıt yönetimi için alternatif bir mekanizmadır. Çok sayıda kaydı sürdürmek yerine, önemsiz olmayan ve kolayca yanlışlıkla veya yeterince yeterli bir şekilde gerçekleştirilebilecek, tek bir yükleme nesnesi kullanmak mümkündür.

Yükleme, ihtiyacınız olan her şeyi içerir: gönderim kanalı (cihaz belirteci), Etiketler, şablonlar, ikincil kutucuklar (WNS ve APNS için). KIMLIĞI artık almak için hizmeti çağırmanız gerekmez. yalnızca GUID veya başka bir tanımlayıcı oluşturun, cihazı cihazda tutun ve anında iletme kanalı (cihaz belirteci) ile birlikte arka uca gönderin.

Arka uçta yalnızca tek bir çağrı `CreateOrUpdateInstallation`yapmanız gerekir; tamamen ıdempotent, bu nedenle gerekirse yeniden deneyin.

Amazon Ille Fire için örnek olarak:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Güncelleştirmek istiyorsanız:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Gelişmiş senaryolar için, yükleme nesnesinin yalnızca belirli özelliklerini değiştirmeye izin veren kısmi güncelleştirme özelliğini kullanın. Kısmi güncelleştirme, yükleme nesnesine karşı çalıştırabileceğiniz JSON Patch işlemlerinin bir alt kümesidir.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Yüklemeyi Sil:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`ve `Delete` ile`Get`en sonunda tutarlıdır. İsteğiniz işlem, çağrı sırasında yalnızca sistem kuyruğuna gider ve arka planda yürütülür. Get, ana çalışma zamanı senaryosu için tasarlanmamıştır, ancak hata ayıklama ve sorun giderme amacıyla, hizmet tarafından sıkı bir şekilde kısıtlanmıştır.

Yüklemeler için gönderme akışı, kayıtlar için ile aynıdır. Belirli bir yüklemeye yönelik bildirimi hedeflemek için-yalnızca "ınstalstıd: {istenen-id}" etiketini kullanın. Bu durumda, kod şu şekilde olur:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Birçok şablondan biri için:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Zamanlama bildirimleri (Standart katmanda kullanılabilir)

Normal gönderme ile aynı, ancak bildirimin ne zaman teslim edilmesi gerektiğini belirten bir ek parametre olan scheduledTime ile aynıdır. Hizmet, şu anda + 5 dakika ile 7 gün arasında herhangi bir noktayı kabul eder.

**Bir Windows yerel bildirimi zamanlayın:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>İçeri/dışarı aktarma (Standart katmanda kullanılabilir)

Kayıtlara karşı toplu işlem yapmanız gerekebilir. Genellikle, etiketleri güncelleştirmek için başka bir sistemle veya çok büyük bir düzeltmeyle tümleştirme içindir. Binlerce kayıt varsa get/update akışını kullanmanızı önermiyoruz. Sistemin Içeri/dışarı aktarma özelliği senaryoyu kapsayacak şekilde tasarlanmıştır. Depolama hesabınızın altındaki bir blob kapsayıcısına, çıkış için gelen verilerin ve konumun bir kaynağı olarak erişim sağlayacaksınız.

**Dışarı aktarma işi gönder:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**İçeri aktarma işi gönder:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Bir iş tamamlanana kadar bekleyin:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Tüm işleri al:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**SAS imzası olan URI:**

 Bu URL, bir blob dosyasının veya blob kapsayıcısının URL 'si artı izin ve sona erme saati ile hesabın SAS anahtarı kullanılarak yapılan tüm bu işlerin imzası gibi bir dizi parametrenin URL 'sidir. Azure Storage Java SDK 'Sı, bu URI 'lerin oluşturulması dahil zengin özelliklere sahiptir. Basit alternatif olarak, imzalama algoritmasının temel ve kompakt `ImportExportE2E` uygulamasına sahip olan test sınıfına (GitHub konumundan) göz atın.

### <a name="send-notifications"></a>Bildirim gönder

Bildirim nesnesi yalnızca üst bilgileri olan bir gövdedir, bazı yardımcı yöntemler yerel ve şablon bildirimleri nesnelerini oluşturmaya yardımcı olur.

* **Windows Mağazası ve Windows Phone 8,1 (Silverlight olmayan)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8,0 ve 8,1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Lütfen yangın ateş**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Etiketlere gönder**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Etiket ifadesine gönder**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Şablon bildirimi gönder**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Java kodunuzun çalıştırılması artık hedef cihazınızda bir bildirim oluşturacak.

## <a name="next-steps"></a>Sonraki Adımlar

Bu konu, Notification Hubs için basit bir Java REST istemcisi oluşturmayı göstermiştir. Buradan şunları yapabilirsiniz:

* Tüm SDK kodunu içeren tam [Java SDK]indirin.
* Örneklerle yürütün:
  * [Notification Hubs kullanmaya başlayın]
  * [Son haberleri gönder]
  * [Yerelleştirilmiş son haberleri gönder]
  * [Kimliği doğrulanmış kullanıcılara bildirimler gönderme]
  * [Kimliği doğrulanmış kullanıcılara platformlar arası bildirimler gönderme]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Notification Hubs kullanmaya başlayın]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Son haberleri gönder]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Yerelleştirilmiş son haberleri gönder]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Kimliği doğrulanmış kullanıcılara bildirimler gönderme]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Kimliği doğrulanmış kullanıcılara platformlar arası bildirimler gönderme]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
