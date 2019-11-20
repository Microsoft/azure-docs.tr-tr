---
title: Azure etkinlik günlüklerini Azure kiracılarının tamamında bir Log Analytics çalışma alanında toplayın | Microsoft Docs
description: Event Hubs ve Logic Apps kullanarak Azure etkinlik günlüğünden veri toplayın ve farklı bir kiracıda Azure Izleyici 'de bir Log Analytics çalışma alanına gönderin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 02/06/2019
ms.openlocfilehash: b11671f20a7e3e6053f90a884777b31196232a38
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048301"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants"></a>Azure etkinlik günlüklerini Azure Active Directory kiracılar genelinde Azure Izleyici 'ye toplayın

> [!NOTE]
> Artık, kaynak günlüklerini topladığınıza benzer bir tanılama ayarı kullanarak, etkinlik günlüğünü bir Log Analytics çalışma alanında toplayabilirsiniz. Bkz. Azure [izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplayın ve çözümleyin](activity-log-collect.md).

Bu makalede, Logic Apps için Azure Log Analytics veri toplayıcı Bağlayıcısı 'nı kullanarak Azure Izleyici 'deki bir Log Analytics çalışma alanında Azure etkinlik günlüklerini toplamak için bir yöntem adımları açıklanır. Farklı bir Azure Active Directory kiracısındaki bir çalışma alanına Günlükler göndermeniz gerektiğinde bu makaledeki işlemi kullanın. Örneğin yönetilen bir servis sağlayıcısıysanız, müşterinin aboneliğinden etkinlik günlüklerini toplamak ve bunları kendi aboneliğinizdeki bir Log Analytics çalışma alanında depolamak isteyebilirsiniz.

Log Analytics çalışma alanı aynı Azure aboneliğinde veya farklı bir abonelikte ancak aynı Azure Active Directory, Azure etkinlik günlüklerini toplamak için Azure [izleyici 'de Azure etkinlik Log Analytics günlüklerini toplayın ve çözümleyin](activity-log-collect.md) bölümündeki adımları kullanın.

## <a name="overview"></a>Genel Bakış

Bu senaryoda kullanılan strateji, Azure Etkinlik Günlüğü'nün olayları bir [Olay Hub'ına](../../event-hubs/event-hubs-about.md) göndermesini sağlamaktır ve burada bir [Mantıksal Uygulama](../../logic-apps/logic-apps-overview.md) bunları Log Analytics çalışma alanınıza gönderir. 

![Etkinlik günlüğünden Log Analytics çalışma alanına veri akışı görüntüsü](media/collect-activity-logs-subscriptions/data-flow-overview.png)

Bu yaklaşımın avantajları şunlardır:
- Azure Etkinlik Günlüğü Olay Hub'ına akış yaptığından gecikme süresi düşüktür.  Daha sonra mantıksal uygulama tetiklenir ve çalışma alanına verileri gönderir. 
- Çok az kod gerekir ve dağıtılması gereken sunucu altyapısı yoktur.

Bu makalede şu işlemleri yapmanız için size yol gösterilir:
1. Olay Hub'ı oluşturma. 
2. Azure Etkinlik Günlüğü dışarı aktarma profilini kullanarak etkinlik günlüklerini Olay Hub'ına aktarma.
3. Olay Hub 'ından okumak ve Log Analytics çalışma alanına olay göndermek için bir mantıksal uygulama oluşturun.

## <a name="requirements"></a>Gereksinimler
Aşağıda bu senaryoda kullanılan Azure kaynaklarıyla ilgili gereksinimler verilmiştir.

- Olay Hub'ı ad alanının, günlükleri yayan abonelikle aynı abonelikte yer alması gerekmez. Ayarı yapılandıran kullanıcının her iki aboneliğe de uygun erişim izinleri olmalıdır. Aynı Azure Active Directory'de birden çok aboneliğiniz varsa, tüm aboneliklerin etkinlik günlüklerini tek bir olay hub'ına gönderebilirsiniz.
- Mantıksal Uygulama olay hub'ından farklı bir abonelikte yer alabilir ve aynı Azure Active Directory'de yer alması gerekmez. Mantıksal Uygulama, Olay Hub'ından okumak için Olay Hub'ının paylaşılan erişim anahtarını kullanır.
- Log Analytics çalışma alanı Mantıksal Uygulama'dan farklı bir abonelikte ve Azure Active Directory'de yer alabilir, ama işlemi basitleştirmek için bunların aynı abonelikte yer almasını öneririz. Mantıksal uygulama, Log Analytics çalışma alanı KIMLIĞINI ve anahtarını kullanarak çalışma alanına gönderilir.



## <a name="step-1---create-an-event-hub"></a>1\. Adım - Olay Hub’ı oluşturma

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Azure Portal'da **Kaynak oluştur** > **Nesnelerin İnterneti** > **Olay Hub'ları** öğesini seçin.

   ![Market yeni olay hub'ı](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. **Ad alanı oluştur**'un altında, yeni ad alanı girin veya mevcut bir ad alanını seçin. Adın kullanılabilirliği sistem tarafından hemen kontrol edilir.

   ![olay hub'ı oluştur iletişim kutusunun resmi](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Yeni kaynak için fiyatlandırma katmanını (Temel veya Standart), Azure aboneliğini, kaynak grubunu ve konumu seçin.  Ad alanını oluşturmak için **Oluştur**’a tıklayın. Sistemin kaynakları tam olarak sağlaması için birkaç dakika beklemeniz gerekebilir.
6. Listede yeni oluşturduğunuz ad alanına tıklayın.
7. **Paylaşılan erişim ilkeleri**'ni seçin ve **RootManageSharedAccessKey** öğesine tıklayın.

   ![olay hub'ı paylaşılan erişim ilkelerinin resmi](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. **RootManageSharedAccessKey** bağlantı dizesini panoya kopyalamak için kopyala düğmesine tıklayın. 

   ![olay hub'ı paylaşılan erişim anahtarının resmi](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Not Defteri gibi geçici bir konumda, Olay Hub'ı adının bir kopyasını ve birincil veya ikincil Olay Hub'ı bağlantı dizesini saklayın. Bu değerler Mantıksal Uygulama için gereklidir.  Olay Hub'ı bağlantı dizesi olarak **RootManageSharedAccessKey** bağlantı dizesini kullanabilir veya ayrı bir bağlantı dizesi oluşturabilirsiniz.  Kullandığınız bağlantı dizesinin `Endpoint=sb://` ile başlaması ve **Yönet** erişim ilkesi olan bir ilkeye yönelik olması gerekir.


## <a name="step-2---export-activity-logs-to-event-hub"></a>2\. Adım - Etkinlik Günlüklerini Event Hub’a aktarma

Etkinlik Günlüğü akışını etkinleştirmek için bir Olay Hub'ı Ad Alanını ve bu ad alanı için paylaşılan erişim ilkesini seçmelisiniz. İlk yeni Etkinlik Günlüğü olayı olduğunda bu ad alanında bir Olay Hub'ı oluşturulur. 

Günlükleri yayan abonelikten farklı bir abonelikte yer alan olay hub'ı ad alanını kullanabilirsiniz; ancak aboneliklerin aynı Azure Active Directory'de olması gerekir. Ayarı yapılandıran kullanıcının her iki aboneliğe erişmek için uygun RBAC'si olmalıdır. 

1. Azure Portal'da **İzle** > **Etkinlik Günlüğü**'nü seçin.
3. Sayfanın üst kısmındaki **Dışarı Aktar** düğmesine tıklayın.

   ![azure izlemenin gezinti resmi](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. **Abonelik** alanında dışarı aktarma işleminin hangi abonelikten yapılacağını seçin ve ardından **Bölgeler** açılan listesinde **Tümünü seç**'e tıklayarak tüm bölgelerdeki kaynaklarla ilgili olayları seçin. **Bir olay hub'ına aktarın** onay kutusuna tıklayın.
7. **Service bus ad alanı**'na tıklayın ve ardından olay hub'ını içeren **Abonelik**'i, **olay hub'ı ad alanı**'nı ve **olay hub'ı ilke adı**'nı seçin.

    ![olay hub'ına aktar sayfasının resmi](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. **Tamam**'a tıklayın ve ardından **Kaydet**'e tıklayarak bu ayarları kaydedin. Ayarlar aboneliğinize hemen uygulanır.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>3\. Adım - Mantıksal Uygulama'yı oluşturma

Etkinlik günlükleri Olay Hub 'ına yazıldıktan sonra, günlükleri Olay Hub 'ından toplayıp Log Analytics çalışma alanına yazacak bir mantıksal uygulama oluşturursunuz.

Mantıksal Uygulama şunları içerir:
- Olay Hub'ından okumak için [Olay Hub'ı bağlayıcı](https://docs.microsoft.com/connectors/eventhubs/) tetikleyicisi.
- JSON olaylarını ayıklamak için [JSON Ayrıştır eylemi](../../logic-apps/logic-apps-content-type.md).
- JSON'u bir nesneye dönüştürmek için [Oluştur eylemi](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).
- Log Analytics verileri Log Analytics çalışma alanına [göndermek için veri Bağlayıcısı gönder](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) .

   ![mantıksal uygulamalarda olay hub'ı tetikleyicisi ekleme resmi](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Mantıksal Uygulama Gereksinimleri
Mantıksal Uygulamanızı oluşturmadan önce, önceki adımdan aşağıdaki bilgilerin elinizde olduğundan emin olun:
- Olay Hub'ı adı
- Olay Hub'ı ad alanı için Olay Hub'ı bağlantı dizesi (birincil veya ikincil).
- Log Analytics çalışma alanı kimliği
- Log Analytics paylaşılan anahtarı

Olay Hub'ı adını ve bağlantı dizesini almak için, [Event Hubs ad alanı izinlerini denetleme ve bağlantı dizesini bulma](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string) başlığı altında verilen adımları izleyin.


### <a name="create-a-new-blank-logic-app"></a>Yeni boş bir Mantıksal Uygulama oluşturma

1. Azure Portal'da **Kaynak oluştur** > **Kurumsal Tümleştirme** > **Mantıksal Uygulama**'yı seçin.

    ![Yeni mantıksal uygulama Market](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Aşağıdaki tabloya ayarları girin.

    ![Mantıksal uygulama oluşturma](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Ayar | Açıklama  |
   |:---|:---|
   | Ad           | Mantıksal uygulamanın benzersiz adı. |
   | Abonelik   | Mantıksal uygulamayı içerecek olan Azure aboneliğini seçin. |
   | Kaynak Grubu | Mantıksal uygulama için var olan bir Azure kaynak grubunu seçin veya yeni grup oluşturun. |
   | Konum       | Mantıksal uygulamanızın dağıtılacağı veri merkezi bölgesini seçin. |
   | Log Analytics  | Mantıksal uygulamanızın her çalıştırmasının durumunu Log Analytics çalışma alanında günlüğe kaydetmek istiyorsanız seçin.  |

    
3. **Oluştur**'u seçin. **Dağıtım Başarılı** bildirimi görüntülendiğinde, Mantıksal Uygulamanızı açmak için **Kaynağa git**'e tıklayın.

4. **Şablonlar** bölümünde **Boş Mantıksal Uygulama**'yı seçin. 

Logic Apps Tasarımcısı'nda mantıksal uygulama iş akışınızı başlatmak için kullanacağınız uygun bağlayıcılar ve tetikleyicileri gösterilir.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Olay Hub'ı tetikleyicisi ekleme

1. Logic Apps Tasarımcısı'nın arama kutusunda, filtreniz için *olay hub'ları* yazın. **Event Hubs - Olay Hub'ında kullanılabilir olaylar olduğunda** tetikleyicisini seçin.

   ![mantıksal uygulamalarda olay hub'ı tetikleyicisi ekleme resmi](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Kimlik bilgileri istendiğinde, Event Hubs ad alanınıza bağlanın. Bağlantınızın adını ve kopyaladığınız bağlantı dizesini girin.  **Oluştur**'u seçin.

   ![mantıksal uygulamalarda olay hub'ı bağlantısı ekleme resmi](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Bağlantıyı oluşturduktan sonra, tetikleyicinin ayarlarını düzenleyin. Başlangıç olarak **Olay Hub'ı adı** açılan listesinden **insights-operational-logs** öğesini seçin.

   ![Kullanılabilir olaylar olduğunda iletişim kutusu](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. **Gelişmiş seçenekleri göster** öğesini genişletin ve **İçerik türü**'nü *application/json* olarak değiştirin.

   ![Olay hub'ı yapılandırma iletişim kutusu](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>JSON Ayrıştır eylemi ekleme

Olay Hub'ından gelen çıkış bir JSON yükü ve bir kayıt dizisi içerir. [JSON Ayrıştır](../../logic-apps/logic-apps-content-type.md) eylemi, yalnızca Log Analytics çalışma alanına göndermek için kayıt dizisini ayıklamak üzere kullanılır.

1. **Yeni adım** > **Eylem ekle**'ye tıklayın.
2. Arama kutusuna filtreniz için *json ayrıştır* yazın. **Veri İşlemleri - JSON Ayrıştır** eylemini seçin.

   ![logic apps'e json ayrıştır eylemi ekleme](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. **İçerik** alanının içine tıklayın ve *Gövde*'yi seçin.

4. Aşağıdaki şemayı kopyalayın ve **Şema** alanına yapıştırın.  Bu şema Olay Hub'ı eyleminden gelen çıkışla eşleşir.  

   ![json ayrıştır iletişim kutusu](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Olay Hub'ında **Çalıştır**'a tıklayıp **Ham Çıkış**'a bakarak bir örnek yük görebilirsiniz.  Ardından bu çıkışı **JSON Ayrıştır** etkinliğindeki **Şema oluşturmak için örnek yük kullanma** ile kullanarak şemayı oluşturabilirsiniz.

### <a name="add-compose-action"></a>Oluştur eylemi ekleme
[Oluştur](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) eylemi JSON çıkışını alır ve Log Analytics eylemi tarafından kullanılabilecek bir nesne oluşturur.

1. **Yeni adım** > **Eylem ekle**'ye tıklayın.
2. Filtreniz için *oluştur* yazın ve ardından **Veri İşlemleri - Oluştur** eylemini seçin.

    ![Oluştur eylemi ekleme](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. **Girişler** alanına tıklayın ve **JSON Ayrıştır** etkinliğinin altında **Gövde**'yi seçin.


### <a name="add-log-analytics-send-data-action"></a>Log Analytics Veri Gönder eylemi ekleme
[Azure Log Analytics veri toplayıcı](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) eylemi, nesneyi oluşturma eyleminden alır ve bir Log Analytics çalışma alanına gönderir.

1. **Yeni adım** > **Eylem ekle**'ye tıklayın.
2. Filtreniz için *log analytics* yazın ver ardından **Azure Log Analytics Veri Toplayıcısı - Veri Gönder** eylemini seçin.

   ![logic apps'e log analytics veri gönder eylemini ekleme](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Bağlantınız için bir ad girin ve Log Analytics çalışma alanınızın **Çalışma Alanı Kimliği**'ni ve **Çalışma Alanı Anahtarı**'nı yapıştırın.  **Oluştur**’ tıklayın.

   ![logic apps'e log analytics bağlantısını ekleme](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Bağlantıyı oluşturduktan sonra, aşağıdaki tabloda ayarları düzenleyin. 

    ![Veri gönder eylemini yapılandırma](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Ayar        | Değer           | Açıklama  |
   |---------------|---------------------------|--------------|
   |JSON İsteği gövdesi  | **Oluştur** eyleminden **Çıkış** | Oluştur eyleminin gövdesinden kayıtları alır. |
   | Özel Günlük Adı | AzureActivity | İçeri aktarılan verileri tutmak için Log Analytics çalışma alanında oluşturulacak özel günlük tablosunun adı. |
   | Saat oluşturma alanı | time | **time** için JSON alanını seçmeyin - yalnızca time sözcüğünü yazın. JSON alanını seçerseniz, tasarımcı **Veri Gönder** eylemini *For Each* döngüsüne sokar ve siz bunu istemezsiniz. |




10. Mantıksal Uygulamanızda yaptığınız değişiklikleri kaydetmek için **Kaydet**'e tıklayın.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>4\. Adım - Mantıksal Uygulamayı test etme ve sorunlarını giderme
İş akışının tamamlanması üzerine, hatasız çalıştığını doğrulamak için bunu tasarımcıda test edebilirsiniz.

Logic Apps Tasarımcısı'nda, Mantıksal Uygulamayı test etmek için **Çalıştır**'a tıklayın. Mantıksal Uygulamadaki her adımda bir durum simgesi gösterilir; yeşil daire içinde beyaz onay işareti başarının göstergesidir.

   ![Mantıksal uygulamayı test etme](media/collect-activity-logs-subscriptions/test-logic-app.png)

Her adımla ilgili ayrıntılı bilgileri görmek için, adım adına tıklayarak öğeyi genişletin. Her adımda alınan ve gönderilen veriler hakkında daha fazla bilgi görmek için **Ham girişleri görüntüleyin**'e ve **Ham çıkışları görüntüleyin**'e tıklayın.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>5\. Adım - Log Analytics'de Azure Etkinlik Günlüğü'nü görüntüleme
Son adım Log Analytics çalışma alanını denetleyip verilerin beklendiği gibi toplandığından emin olmaktır.

1. Azure portalının sol alt köşesinde bulunan **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**’i seçin.
2. Log Analytics çalışma alanlarınızın listesinde çalışma alanınızı seçin.
3.  **Günlük Araması** kutucuğuna tıklayın ve Günlük Araması bölmesinde, sorgu alanında `AzureActivity_CL` yazıp Enter tuşuna basın veya sorgu alanının sağındaki arama düğmesine tıklayın. Özel günlüğünüze *AzureActivity* adını vermediyseniz, seçtiğiniz adı yazın ve sonuna `_CL` ekleyin.

>[!NOTE]
> Log Analytics çalışma alanına ilk kez yeni bir özel günlük gönderildiğinde, özel günlüğün aranabilir olması bir saate kadar sürebilir.

>[!NOTE]
> Etkinlik günlükleri bir özel tabloya yazılır ve [Etkinlik Günlüğü çözümünde](./activity-log-collect.md) görüntülenmez.


![Mantıksal uygulamayı test etme](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir olay hub 'ından Azure etkinlik günlüklerini okumak ve bunları analiz için Log Analytics çalışma alanına göndermek üzere bir mantıksal uygulama oluşturdunuz. Panolar oluşturma da dahil olmak üzere bir çalışma alanındaki verileri görselleştirme hakkında daha fazla bilgi edinmek için verileri görselleştirmeye yönelik öğreticiyi inceleyin.

> [!div class="nextstepaction"]
> [Günlük Araması verilerini görselleştirme öğreticisi](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
