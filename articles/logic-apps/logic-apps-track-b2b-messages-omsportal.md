---
title: Azure Izleyici günlükleri ile B2B iletilerini izleme-Azure Logic Apps | Microsoft Docs
description: Azure Log Analytics ile tümleştirme hesapları ve Azure Logic Apps için B2B iletişimini izleyin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 33c4efb2b783b5071513f069beac9cdf73c373a8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997856"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Azure İzleyici günlükleri ile B2B iletilerini izleme

Tümleştirme hesabınızdaki ticari iş ortakları arasında B2B iletişimini ayarladıktan sonra, bu iş ortakları AS2, x12 ve EDIOLGU gibi protokollerle ileti değiş tokuş edebilir. Bu iletilerin doğru şekilde işlendiğini denetlemek için bu iletileri [Azure izleyici günlükleriyle](../log-analytics/log-analytics-overview.md)izleyebilirsiniz. Örneğin, şu Web tabanlı izleme yeteneklerini iletileri izlemek için kullanabilirsiniz:

* İleti sayısı ve durumu
* Durum bildirimleri
* İletileri bildirimler ile ilişkilendirme
* Hatalara ilişkin ayrıntılı hata açıklamaları
* Arama özellikleri

> [!NOTE]
> Bu sayfada daha önce bu görevleri Microsoft Operations Management Suite (OMS) ile gerçekleştirme adımları açıklanmakta ve bu adımların [ocak 2019 ' de devre dışı](../azure-monitor/platform/oms-portal-transition.md)bırakılmakta olan adımlar, bunun yerine Azure Log Analytics ile değiştirilir. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

* Tanılama günlük kaydı ile ayarlanan mantıksal uygulama. [Mantıksal uygulama oluşturmayı](quickstart-create-first-logic-app-workflow.md) ve [Bu mantıksal uygulama için günlüğü ayarlamayı](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)öğrenin.

* İzleme ve günlüğe kaydetme ile ayarlanan bir tümleştirme hesabı. [Tümleştirme hesabı oluşturmayı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ve [Bu hesap için izleme ve günlüğe kaydetmeyi ayarlama](../logic-apps/logic-apps-monitor-b2b-message.md)hakkında bilgi edinin.

* Henüz yapmadıysanız, [tanılama verilerini Azure izleyici günlüklerine yayımlayın](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Önceki gereksinimleri karşıladıktan sonra, Log Analytics aracılığıyla B2B iletişimini izlemek için kullandığınız bir Log Analytics çalışma alanına da ihtiyacınız vardır. Log Analytics çalışma alanınız yoksa, [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/learn/quick-create-workspace.md)öğrenin.

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B çözümü 'ni yükler

Azure Izleyici günlüklerine mantıksal uygulamanız için B2B iletilerini izlemebilmeniz için önce Azure Izleyici günlüklerine **Logic Apps B2B** çözümünü ekleyin. [Azure izleyici günlüklerine çözüm ekleme](../azure-monitor/learn/quick-create-workspace.md)hakkında daha fazla bilgi edinin.

1. [Azure portalda](https://portal.azure.com) **Tüm hizmetler**’i seçin. Arama kutusunda, "Log Analytics" i bulun ve **Log Analytics**' yi seçin.

   ![Log Analytics seçin](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. **Log Analytics**altında Log Analytics çalışma alanınızı bulun ve seçin. 

   ![Log Analytics çalışma alanı seçin](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. **İzleme çözümlerini yapılandırma** >  **Log Analytics kullanmaya başlayın**altında, **çözümleri görüntüle**' yi seçin.

   !["Çözümleri görüntüle" yi seçin](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Genel Bakış sayfasında, **yönetim çözümleri** listesini açan **Ekle**' yi seçin. Bu listeden **Logic Apps B2B**' yi seçin. 

   ![Logic Apps B2B çözümü seçin](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Çözümü bulamıyorsanız, listenin en altında, çözüm görünene kadar **daha fazla yükle** ' yi seçin.

1. **Oluştur**' u seçin, çözümü yüklemek istediğiniz Log Analytics çalışma alanını onaylayın ve sonra yeniden **Oluştur** ' u seçin.   

   ![Logic Apps B2B için "Oluştur" seçeneğini belirleyin](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Mevcut bir çalışma alanını kullanmak istemiyorsanız, bu sırada yeni bir çalışma alanı da oluşturabilirsiniz.

1. İşiniz bittiğinde, çalışma alanınızın **genel bakış** sayfasına geri dönün. 

   Logic Apps B2B çözümü artık genel bakış sayfasında görüntülenir. 
   B2B iletileri işlendiğinde, bu sayfadaki ileti sayısı güncellenir.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>B2B ileti bilgilerini görüntüle

B2B iletileri işlendikten sonra, **Logic Apps B2B** kutucuğunda bu iletilerin durumunu ve ayrıntılarını görüntüleyebilirsiniz.

1. Logic Analytics çalışma alanınıza gidin ve genel bakış sayfasını açın. **Logic Apps B2B**seçin.

   ![Güncelleştirilmiş ileti sayısı](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Varsayılan olarak **Logic Apps B2B** kutucuğu, verileri tek bir güne göre gösterir. Veri kapsamını farklı bir aralığa dönüştürmek için sayfanın üst kısmındaki kapsam denetimini seçin:
   > 
   > ![Aralığı Değiştir](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. İleti durumu panosu görüntülendikten sonra, verileri tek bir güne göre gösteren belirli bir ileti türü için daha fazla ayrıntı görüntüleyebilirsiniz. **AS2**, **x12**veya **ediolgu**için kutucuğu seçin.

   ![İleti durumunu görüntüle](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Seçtiğiniz kutucukta bir ileti listesi görüntülenir. 
   Her ileti türü için özellikler hakkında daha fazla bilgi edinmek için şu ileti özelliği açıklamalarını inceleyin:

   * [AS2 ileti özellikleri](#as2-message-properties)
   * [X12 ileti özellikleri](#x12-message-properties)
   * [EDIOLGU iletisi özellikleri](#EDIFACT-message-properties)

   Örneğin, AS2 ileti listesi şöyle görünebilir:

   ![AS2 iletilerini görüntüleme](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Belirli iletiler için giriş ve çıkışları görüntülemek veya dışarı aktarmak için, bu iletileri seçin ve **İndir**' i seçin. İstendiğinde,. zip dosyasını yerel bilgisayarınıza kaydedin ve ardından bu dosyayı ayıklayın. 

   Ayıklanan klasör, seçili her ileti için bir klasör içerir. 
   Bildirimleri ayarlarsanız ileti klasörü, bildirim ayrıntıları olan dosyaları da içerir. 
   Her ileti klasörü en az şu dosyalara sahiptir: 
   
   * Giriş yükü ve çıkış yükü ayrıntıları ile insan tarafından okunabilen dosyalar
   * Giriş ve çıkışlarla kodlanmış dosyalar

   Her ileti türü için, klasör ve dosya adı biçimlerini buradan bulabilirsiniz:

   * [AS2 klasörü ve dosya adı biçimleri](#as2-folder-file-names)
   * [X12 klasörü ve dosya adı biçimleri](#x12-folder-file-names)
   * [EDIOLGU klasörü ve dosya adı biçimleri](#edifact-folder-file-names)

   ![İleti dosyalarını indir](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Aynı çalıştırma KIMLIĞINE sahip tüm eylemleri görüntülemek için, **günlük araması** sayfasında ileti listesinden bir ileti seçin.

   Bu eylemleri sütuna göre sıralayabilir veya belirli sonuçları arayabilirsiniz.

   ![Aynı çalıştırma KIMLIĞINE sahip eylemler](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Önceden oluşturulmuş sorgularla sonuçları aramak için **Sık Kullanılanlar**' ı seçin.

   * [Filtreler ekleyerek sorgu oluşturmayı](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)öğrenin. 
   Ya da [Azure izleyici günlüklerinde günlük aramalarıyla verileri bulma](../log-analytics/log-analytics-log-searches.md)hakkında daha fazla bilgi edinin.

   * Arama kutusundaki sorguyu değiştirmek için sorguyu, filtre olarak kullanmak istediğiniz sütun ve değerlerle güncelleştirin.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2, x12 ve EDIOLGU iletileri için özellik açıklamaları ve ad biçimleri

Her ileti türü için, indirilen ileti dosyaları için özellik açıklamaları ve ad biçimleri aşağıda verilmiştir.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 ileti özelliği açıklamaları

Her AS2 iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
| --- | --- |
| Gönderen | **Alma ayarlarında**belirtilen Konuk iş ortağı veya bir AS2 sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| Alıcı | **Alma ayarlarında**belirtilen ana bilgisayar ortağı veya bir AS2 sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| Logic App | AS2 eylemlerinin ayarlandığı mantıksal uygulama |
| Durum | AS2 ileti durumu <br>Success = geçerli bir AS2 iletisi alındı veya gönderildi. Hiçbir MDN ayarlanmadı. <br>Success = geçerli bir AS2 iletisi alındı veya gönderildi. MDN ayarlanır ve alınır ya da MDN gönderilir. <br>Failed = geçersiz bir AS2 iletisi alındı. Hiçbir MDN ayarlanmadı. <br>Bekliyor = geçerli bir AS2 iletisi alındı veya gönderildi. MDN ayarlanmış ve MDN bekleniyor. |
| Onay | MDN ileti durumu <br>Kabul edilen = pozitif MDN alındı veya gönderildi. <br>Bekliyor = bir MDN alınması veya gönderilmesi bekleniyor. <br>Reddedildi = negatif MDN alındı veya gönderildi. <br>Gerekli değildir = MDN sözleşmede ayarlanmadı. |
| Direction | AS2 ileti yönü |
| Bağıntı Kimliği | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| İleti kimliği | AS2 ileti başlıklarındaki AS2 ileti KIMLIĞI |
| Timestamp | AS2 eyleminin iletiyi işleme zamanı |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>İndirilen ileti dosyaları için AS2 ad biçimleri

İndirilen her AS2 ileti klasörü ve dosyasının ad biçimleri aşağıda verilmiştir.

| Klasör veya dosya | Ad biçimi |
| :------------- | :---------- |
| İleti klasörü | Gönderen \_\_[alıcı]\_AS2\_[bağıntı-kimliği] [ileti-kimliği] [zaman damgası] \_ |
| Giriş, çıkış ve eğer ayarlandıysa, onay dosyaları | **Giriş yükü**: [Gönderen]\_[alıcı]\_AS2\_[bağıntı-kimliği]\_input_payload. txt </p>**Çıkış yükü**: [Gönderen]\_[alıcı]\_AS2\_[bağıntı-kimliği]\_çıkış\_yükü. txt </p></p>**Girişler**: [Gönderen]\_[alıcı]\_AS2\_[bağıntı-kimliği]\_girişler. txt </p></p>**Çıktılar**: [Gönderen]\_[alıcı]\_AS2\_[bağıntı-kimliği]\_çıktılar. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 ileti özelliği açıklamaları

Her x12 iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
| --- | --- |
| Gönderen | **Alma ayarlarında**belirtilen Konuk iş ortağı veya bir x12 sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| Alıcı | **Alma ayarlarında**belirtilen ana bilgisayar ortağı veya bir x12 sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| Logic App | X12 eylemlerinin ayarlandığı mantıksal uygulama |
| Durum | X12 ileti durumu <br>Success = geçerli bir x12 iletisi alındı veya gönderildi. Ayarlanmış işlevsel ACK yok. <br>Success = geçerli bir x12 iletisi alındı veya gönderildi. İşlev ACK ayarlanır ve alınır ya da işlevsel bir ACK gönderilir. <br>Başarısız = geçersiz bir x12 iletisi alındı veya gönderildi. <br>Bekliyor = geçerli bir x12 iletisi alındı veya gönderildi. İşlevsel ack ayarlanır ve işlevsel bir ACK beklenmektedir. |
| Onay | İşlev ACK (997) durumu <br>Kabul edildi = pozitif bir işlevsel ACK alındı veya gönderildi. <br>Reddedildi = negatif bir işlevsel ACK alındı veya gönderildi. <br>Bekliyor = işlevsel bir ACK bekleniyor ancak alınmadı. <br>Bekliyor = bir işlev ACK oluşturuldu ancak iş ortağına gönderilemiyor. <br>Gerekli değil = Işlev ACK ayarlanmadı. |
| Direction | X12 ileti yönü |
| Bağıntı Kimliği | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| İleti türü | EDI x12 ileti türü |
| ICN | X12 iletisi için değişim denetim numarası |
| TSCN | X12 iletisi için Işlem kümesi denetim numarası |
| Timestamp | X12 eyleminin iletiyi işleme zamanı |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>İndirilen ileti dosyaları için x12 ad biçimleri

İndirilen her x12 ileti klasörü ve dosyasının ad biçimleri aşağıda verilmiştir.

| Klasör veya dosya | Ad biçimi |
| :------------- | :---------- |
| İleti klasörü | Gönderen \_\_\_[alıcı]\_x12\_[Interchange-Control-Number] [Genel-Control-Number] [işlem-Set-Control-Number] [zaman damgası] \_ |
| Giriş, çıkış ve eğer ayarlandıysa, onay dosyaları | **Giriş yükü**: [Gönderen]\_[alıcı]\_x12\_[Interchange-Control-Number]\_input_payload. txt </p>**Çıkış yükü**: [Gönderen]\_[alıcı]\_x12\_[Interchange-Control-Number]\_çıkış\_yükü. txt </p></p>**Girişler**: [Gönderen]\_[alıcı]\_x12\_[Interchange-Control-Number]\_girişleri. txt </p></p>**Çıktılar**: [Gönderen]\_[alıcı]\_x12\_[Interchange-Control-Number]\_çıktılar. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIOLGU iletisi özelliği açıklamaları

Her EDIOLGU iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
| --- | --- |
| Gönderen | **Alma ayarlarında**belirtilen Konuk iş ortağı veya BIR edıolgu sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| Alıcı | **Alma ayarlarında**belirtilen ana bilgisayar ortağı veya BIR edıolgu sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| Logic App | EDIOLGU eylemlerinin ayarlandığı mantıksal uygulama |
| Durum | EDIOLGU iletisi durumu <br>Success = geçerli bir EDIOLGU iletisi alındı veya gönderildi. Ayarlanmış işlevsel ACK yok. <br>Success = geçerli bir EDIOLGU iletisi alındı veya gönderildi. İşlev ACK ayarlanır ve alınır ya da işlevsel bir ACK gönderilir. <br>Başarısız = geçersiz bir EDıOLGU iletisi alındı veya gönderildi <br>Bekliyor = geçerli bir EDIOLGU iletisi alındı veya gönderildi. İşlevsel ack ayarlanır ve işlevsel bir ACK beklenmektedir. |
| Onay | İşlev ACK (conı) durumu <br>Kabul edildi = pozitif bir işlevsel ACK alındı veya gönderildi. <br>Reddedildi = negatif bir işlevsel ACK alındı veya gönderildi. <br>Bekliyor = işlevsel bir ACK bekleniyor ancak alınmadı. <br>Bekliyor = bir işlev ACK oluşturuldu ancak iş ortağına gönderilemiyor. <br>Gerekli değil = Işlev ACK ayarlanmadı. |
| Direction | EDIOLGU iletisi yönü |
| Bağıntı Kimliği | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| İleti türü | EDIOLGU ileti türü |
| ICN | EDIOLGU iletisi için değişim denetim numarası |
| TSCN | EDIOLGU iletisi için Işlem kümesi denetim numarası |
| Timestamp | EDIOLGU eyleminin iletiyi işleme zamanı |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>İndirilen ileti dosyaları için EDIOLGU adı biçimleri

İndirilen her bir EDIOLGU ileti klasörü ve dosyaları için ad biçimleri aşağıda verilmiştir.

| Klasör veya dosya | Ad biçimi |
| :------------- | :---------- |
| İleti klasörü | Gönderen \_\_\_\_\_[alıcı] ediolgu [Interchange-Control-Number] [Global-Control-Number] [işlem-Set-Control-Number] [zaman damgası] \_ |
| Giriş, çıkış ve eğer ayarlandıysa, onay dosyaları | **Giriş yükü**: [Gönderen]\_[alıcı]\_ediolgu\_[Interchange-Control-Number]\_input_payload. txt </p>**Çıkış yükü**: [Gönderen]\_[alıcı]\_ediolgu\_[Interchange-Control-Number]\_çıkış\_yükü. txt </p></p>**Girişler**: [Gönderen]\_[alıcı]\_ediolgu\_[Interchange-Control-Number]\_girişleri. txt </p></p>**Çıktılar**: [Gönderen]\_[alıcı]\_ediolgu\_[Interchange-Control-Number]\_çıktılar. txt |
|          |             |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Izleyici günlüklerinde B2B iletilerini sorgulama](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 izleme şemaları](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 izleme şemaları](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Özel izleme şemaları](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
