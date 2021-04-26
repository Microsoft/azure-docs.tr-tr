---
title: Azure Izleyici 'yi kullanarak veri fabrikalarını izleme
description: Data Factory bilgi ile tanılama günlüklerini etkinleştirerek/Azure Data Factory işlem hatlarını izlemek için Azure Izleyici 'yi nasıl kullanacağınızı öğrenin.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: bceacf0a22b7add0533b72b4ebf06d503366baa4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932497"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak Data Factory izleyin ve uyarır

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bulut uygulamaları karmaşıktır ve birçok hareketli parçaya sahiptir. İzleyiciler, uygulamalarınızın sağlıklı durumda kalmasını ve çalışmasını sağlamaya yardımcı olmak için veri sağlar. İzleyicilerin olası sorunları önlemenize ve geçmişteki sorunları gidermenize yardımcı olur. Uygulamalarınız hakkında derin Öngörüler elde etmek için izleme verilerini kullanabilirsiniz. Bu bilgi, uygulama performansını ve bakımlılığını iyileştirmenize yardımcı olur. Ayrıca, el ile müdahale gerektiren eylemleri otomatikleştirmenize de yardımcı olur.

Azure Izleyici, çoğu Azure hizmeti için temel düzeyde altyapı ölçümleri ve günlükleri sağlar. Azure tanılama günlükleri bir kaynak tarafından dağıtılır ve bu kaynağın çalışması hakkında zengin ve sık veriler sağlar. Azure Data Factory (ADF), Azure Izleyici 'de tanılama günlükleri yazabilir. Bu özelliğin yedi dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Daha fazla bilgi için bkz. [Azure izleyiciye genel bakış](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory ölçümleri ve işlem hattını tutma-verileri çalıştırma

Data Factory depolar işlem hattı-verileri yalnızca 45 gün boyunca çalıştırın. Bu verileri daha uzun süre tutmak istiyorsanız Azure Izleyici 'yi kullanın. Izleyici ile tanılama günlüklerini analiz için birden çok farklı hedefe yönlendirebilirsiniz.

* **Depolama hesabı**: Tanılama günlüklerinizi denetim veya el ile inceleme için bir depolama hesabına kaydedin. Bekletme süresini gün cinsinden belirtmek için tanılama ayarlarını kullanabilirsiniz.
* **Olay Hub 'ı**: günlükleri Azure Event Hubs Stream. Günlükler, Power BI gibi bir iş ortağı hizmeti/özel analiz çözümüne giriş haline gelir.
* **Log Analytics**: günlükleri Log Analytics analiz edin. Azure Izleyici ile Data Factory tümleştirme aşağıdaki senaryolarda kullanışlıdır:
  * Izlemek üzere Data Factory tarafından yayınlanan zengin bir ölçüm kümesine karmaşık sorgular yazmak istiyorsunuz. Bu sorgularda, Izleyici aracılığıyla özel uyarılar oluşturabilirsiniz.
  * Veri fabrikaları genelinde izlemek istiyorsunuz. Birden çok veri fabrikasına ait verileri tek bir Izleyici çalışma alanına yönlendirebilirsiniz.

Ayrıca, günlükleri yayan kaynağın aboneliğinde olmayan bir depolama hesabı veya Olay Hub 'ı ad alanı da kullanabilirsiniz. Ayarı yapılandıran kullanıcının her iki aboneliğe da uygun Azure rol tabanlı erişim denetimi (Azure RBAC) erişimi olmalıdır.

## <a name="configure-diagnostic-settings-and-workspace"></a>Tanılama ayarlarını ve çalışma alanını yapılandırma

Veri fabrikanızın tanılama ayarlarını oluşturun veya ekleyin.

1. Portalda Izleyici ' ye gidin. **Ayarlar**  >  **Tanılama ayarları**' nı seçin.

1. Tanılama ayarı ayarlamak istediğiniz veri fabrikasını seçin.

1. Seçili veri fabrikasında herhangi bir ayar yoksa, bir ayar oluşturmanız istenir. **Tanılamayı aç '** ı seçin.

   ![Hiçbir ayar yoksa bir tanılama ayarı oluştur](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Data Factory 'de mevcut ayarlar varsa, veri fabrikasında zaten yapılandırılmış ayarların bir listesini görürsünüz. **Tanılama ayarı ekle**’yi seçin.

   ![Ayarlar varsa bir tanılama ayarı ekleyin](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Ayarınızı bir ad verin, **Log Analytics gönder**' i seçin ve sonra **Log Analytics çalışma** alanından bir çalışma alanı seçin.

    * _Azure-Diagnostics_ modunda, tanılama günlükleri _AzureDiagnostics_ tablosuna akar.

    * _Kaynağa özgü_ modda Azure Data Factory akışından gelen tanılama günlükleri aşağıdaki tablolara kaydedilir:
      - _ADFActivityRun_
      - _Adfardışık düzen eylemsizlik_
      - _ADFTriggerRun_
      - _Adfssisıntegrationruntimelogs 'lar_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _Adfssispackageyürütülebilirstatıstıcs_
      - _Adfssispackageexecutioncomponentaşamalar_
      - _Adfssispackageexecutiondatastatıstıcs_

      Log Analytics tablolarına göndermek için iş yüklerinize uygun çeşitli Günlükler seçebilirsiniz. Örneğin, SQL Server Integration Services (SSIS) hiç kullanmıyorsanız, herhangi bir SSIS günlüğü seçmeniz gerekir. SSIS Integration Runtime (IR) Başlat/Durdur/bakım işlemlerini günlüğe kaydetmek istiyorsanız SSIS IR günlüklerini seçebilirsiniz. SSIS paket yürütmelerini SQL Server Management Studio (SSMS), SQL Server Agent veya diğer belirlenmiş araçlar aracılığıyla çağırdığınızda, SSIS paket günlükleri ' ni seçebilirsiniz. ADF işlem hatlarında SSIS paketi yürütme etkinliklerini kullanarak SSIS paket yürütmelerini çağırırsanız, tüm Günlükler ' i seçebilirsiniz.

    * _Allölçümler_' i SEÇERSENIZ, ADF etkinliğinin ölçümleri, işlem hattı ve tetikleyici ÇALıŞTıRMALARı ve SSIS IR IŞLEMLERI ve SSIS paket yürütmeleri dahil olmak üzere, üzerinde uyarı izlemeniz veya bunları uygulamanız IÇIN çeşitli ADF ölçümleri kullanılabilir hale getirilir.

   ![Ayarlarınızı adlandırın ve bir Log Analytics çalışma alanı seçin](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Bir Azure günlük tablosunda 500 ' den fazla sütun olabileceğinden _kaynağa özgü mod_' u **seçmeniz önerilir.** Daha fazla bilgi için bkz. [AzureDiagnostics logs Reference](/azure/azure-monitor/reference/tables/azurediagnostics).

1. **Kaydet**’i seçin.

Birkaç dakika sonra, bu veri fabrikasının ayarları listenizde yeni ayar görüntülenir. Tanılama günlükleri, yeni olay verileri oluşturulmasıyla hemen bu çalışma alanına akışlardır. Bir olay yayınlandığınızda ve Log Analytics göründüğünde 15 dakikaya kadar zaman çıkabilir.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketi 'nden Azure Data Factory Analytics çözümü yüklemesi

Bu çözüm, ayrıntıları ayrıntıya gitme ve beklenmeyen davranış desenlerinde sorun giderme seçenekleri ile Data Factory genel durumunun bir özetini sağlar. Zengin, kullanıma hazır görünümler sayesinde, aşağıdakiler dahil olmak üzere anahtar işleme hakkında Öngörüler edinebilirsiniz:

* Data Factory işlem hattının, etkinlik ve tetikleyici çalıştırmalarının bir bakışta Özeti
* Türe göre Veri Fabrikası etkinlik çalıştırmaları ile detaya gitme yeteneği
* Data Factory üst işlem hattının Özeti, etkinlik hataları

1. **Azure Marketi**' ne gidin, **analiz** filtresi ' ni seçin ve **Azure Data Factory Analytics 'i arayın (Önizleme)**

   !["Azure Marketi" ne gidin, "analiz filtresi" yazın ve "Azure Data Factory analiz (Önizleme") seçeneğini belirleyin.](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Azure Data Factory Analytics ile ilgili ayrıntılar **(Önizleme)**

   !["Azure Data Factory Analytics (Önizleme)" hakkındaki ayrıntılar](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. **Oluştur** ' u seçin ve ardından **Log Analytics çalışma alanını** oluşturun veya seçin.

   ![Yeni çözüm oluşturma](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory ölçümlerini izleme

Bu çözümün yüklenmesi, seçilen Log Analytics çalışma alanının çalışma kitapları bölümünde varsayılan bir görünüm kümesi oluşturur. Sonuç olarak, aşağıdaki ölçümler etkin hale gelir:

* ADF çalıştırmaları-1) Data Factory tarafından işlem hattı çalıştırmaları
* ADF çalıştırmalar-2) Data Factory tarafından çalışan etkinlik çalıştırmaları
* ADF çalıştırmalar-3) Data Factory tarafından çalıştırılan tetikler
* ADF hataları-1) Data Factory göre Ilk 10 ardışık düzen hatası
* ADF hataları-2) Data Factory tarafından Ilk 10 etkinlik çalıştırılır
* ADF hataları-3) Ilk 10 Data Factory tarafından hata tetikleyin
* ADF Istatistikleri-1) türe göre etkinlik çalıştırmaları
* ADF Istatistikler-2) türe göre tetikleme çalıştırmaları
* ADF Istatistikleri-3) en fazla işlem hattı çalıştırma süresi

!["Çalışma kitapları (Önizleme)" ve "AzureDataFactoryAnalytics" vurgulanmış pencere](media/data-factory-monitor-oms/monitor-oms-image6.png)

Önceki ölçümleri görselleştirin, bu ölçümlerin arkasındaki sorgulara bakabilir, sorguları düzenleyebilir, uyarılar oluşturabilir ve başka işlemler yapabilirsiniz.

![Veri Fabrikası tarafından çalıştırılan işlem hattının grafik gösterimi "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Önizleme), tanılama günlüklerini _kaynağa özgü_ hedef tablolarına gönderir. Şu tablolara yönelik sorgular yazabilirsiniz: _Adfardışık düzen eylemsizlik_, _Adftriggerrun_ ve _adfactivityrun_.

## <a name="data-factory-metrics"></a>Data Factory ölçümleri

Izleyici ile Azure iş yüklerinizin performansı ve sistem durumu hakkında görünürlük elde edebilirsiniz. En önemli Izleyici verileri türü, performans sayacı olarak da adlandırılan ölçümdür. Ölçümler çoğu Azure kaynağı tarafından yayınlanır. İzleyici, izleme ve sorun giderme amacıyla bu ölçümleri yapılandırmak ve kullanmak için çeşitli yollar sağlar.

Azure Data Factory sürüm 2 tarafından yayılan ölçülerden bazıları şunlardır:

| **Ölçüm**                           | **Ölçüm görünen adı**                  | **Birim** | **Toplama türü** | **Açıklama**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| Activitycancelledçalıştırmaları                 | İptal edilen etkinlik ölçümleri çalıştırıyor           | Count    | Toplam                | Bir dakika penceresi içinde iptal edilen toplam etkinlik çalışması sayısı. |
| Activityfailedçalıştırmaları                   | Başarısız etkinlik çalıştırma ölçümleri             | Count    | Toplam                | Bir dakika penceresi içinde başarısız olan etkinlik çalıştırmalarının toplam sayısı. |
| ActivitySucceededRuns                | Başarılı etkinlik ölçümleri çalıştırıyor          | Count    | Toplam                | Bir dakika penceresi içinde başarılı olan etkinlik çalıştırmalarının toplam sayısı. |
| Ardışık düzen ınecancelledçalıştırmaları                 | İptal edilen işlem hattı çalışma ölçümleri           | Count    | Toplam                | Bir dakika penceresi içinde iptal edilen toplam işlem hattı çalıştırması sayısıdır. |
| Pipelinefailedçalıştırmaları                   | Başarısız işlem hattı çalıştırmaları ölçümleri             | Count    | Toplam                | Bir dakika penceresinde başarısız olan işlem hattı çalıştırmalarının toplam sayısı. |
| Ardışık düzen Inesucceededçalıştırmaları                | Başarılı işlem hattı çalışma ölçümleri          | Count    | Toplam                | Bir dakika penceresi içinde başarılı olan işlem hattı çalıştırmalarının toplam sayısı. |
| Triggercancelledçalıştırmaları                  | İptal edilen tetikleyici çalışan ölçümleri            | Count    | Toplam                | Bir dakika penceresi içinde iptal edilen tetikleyici çalıştırmalarının toplam sayısı. |
| Triggerfailedçalıştırmaları                    | Başarısız tetikleyici çalıştırma ölçümleri              | Count    | Toplam                | Bir dakika penceresi içinde başarısız olan tetikleyici çalıştırmalarının toplam sayısı. |
| TriggerSucceededRuns                 | Başarılı tetikleyici çalışan ölçümleri           | Count    | Toplam                | Bir dakika penceresi içinde başarılı olan tetikleyici çalıştırmalarının toplam sayısı. |
| Ssisıntegrationruntimestartiptal edildi  | İptal edilen SSIS Integration Runtime başlangıç ölçümleri           | Count    | Toplam                | Bir dakika penceresi içinde iptal edilen SSIS tümleştirme çalışma zamanı 'nın toplam sayısı. |
| Ssisıntegrationruntimestartfailed    | Başarısız SSIS Integration Runtime başlangıç ölçümleri             | Count    | Toplam                | Bir dakika penceresinde başarısız olan SSIS tümleştirme çalışma zamanı 'nın toplam sayısı. |
| Ssisıntegrationruntimestartsucceeded | Başarılı SSIS Integration Runtime başlangıç ölçümleri          | Count    | Toplam                | SSIS tümleştirme çalışma zamanının toplam sayısı bir dakika penceresi içinde başarılı olur. |
| Ssisıntegrationruntimestoptakılmış      | Takılmış SSIS tümleştirme çalışma zamanı ölçümleri durdur               | Count    | Toplam                | Bir dakika penceresi içinde takılmış olan SSIS tümleştirme çalışma zamanı duraklarının toplam sayısı. |
| Ssisıntegrationruntimestopsucceeded  | Başarılı SSIS tümleştirme çalışma zamanı ölçümleri durdur           | Count    | Toplam                | Bir dakika penceresinde başarılı olan SSIS tümleştirme çalışma zamanının toplam sayısı. |
| Ssispackageexecutioniptal edildi         | İptal edilen SSIS paketi yürütme ölçümleri  | Count    | Toplam                | Bir dakika penceresi içinde iptal edilen SSIS paket yürütmelerinin toplam sayısı. |
| Ssıspackageexecutionfailed           | Başarısız SSIS paketi yürütme ölçümleri    | Count    | Toplam                | Bir dakikalık pencere içinde başarısız olan SSSıS paket yürütmelerinin toplam sayısı. |
| Ssıspackageexecutionsucceeded        | Başarılı SSIS paketi yürütme ölçümleri | Count    | Toplam                | Bir dakika penceresinde başarılı olan SSIS paket yürütmelerinin toplam sayısı. |

Ölçümlere erişmek için [Azure izleyici veri platformundaki](../azure-monitor/data-platform.md)yönergeleri doldurun.

> [!NOTE]
> Yalnızca tamamlanan, tetiklenen etkinlik ve işlem hattı çalıştırmalarının olayları yayınlanır. Devam eden ve hata ayıklama çalıştırmaları **yayılmadı** . Diğer taraftan, **Tüm** SSIS paket yürütmelerinin olayları, çağrı metotlarından bağımsız olarak, tamamlanmış ve sürmekte olanlar da dahil olmak üzere yayınlanır. Örneğin, Azure özellikli SQL Server Veri Araçları (SSDT) üzerinde paket yürütmelerini, SSMS 'de T-SQL, SQL Server Agent veya diğer belirlenmiş araçlar aracılığıyla veya ADF işlem hatlarında SSIS paket etkinliklerini yürütme veya hata ayıklama çalıştırmaları olarak çalıştırabilirsiniz.

## <a name="data-factory-alerts"></a>Data Factory uyarılar

Azure Portal oturum açın ve   >  uyarı oluşturmak için **uyarıları** İzle ' yi seçin.

![Portal menüsündeki uyarılar](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Uyarı oluşturma

1. Yeni bir uyarı oluşturmak için **+ Yeni uyarı kuralı** ' nı seçin.

    ![Yeni uyarı kuralı](media/monitor-using-azure-monitor/alerts_image4.png)

1. Uyarı koşulunu tanımlayın.

    > [!NOTE]
    > **Kaynak türüne göre filtrele** aşağı açılan listesinden **Tümünü** seçtiğinizden emin olun.

    !["Kaynak Seç" bölmesini açan "hedef seç" > "uyarı koşulunu tanımlayın" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Sinyal mantığını yapılandırma" bölmesini açan "" ölçüt ekle ">" uyarı koşulunu tanımlayın "](media/monitor-using-azure-monitor/alerts_image6.png)

    !["Sinyal türünü yapılandırma" bölmesi](media/monitor-using-azure-monitor/alerts_image7.png)

1. Uyarı ayrıntılarını tanımlayın.

    ![Uyarı ayrıntıları](media/monitor-using-azure-monitor/alerts_image8.png)

1. Eylem grubunu tanımlayın.

    !["Yeni eylem grubu" vurgulanmış olarak bir kural oluşturun](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Yeni bir eylem grubu oluştur](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-posta, SMS, push ve ses yapılandırma](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Eylem grubunu tanımlama](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Izleyici aracılığıyla tanılama günlüklerini ayarlama REST API

### <a name="diagnostic-settings"></a>Tanılama ayarları

İşlem dışı kaynaklar için tanılama günlüklerini yapılandırmak üzere tanılama ayarlarını kullanın. Kaynak denetiminin ayarları aşağıdaki özelliklere sahiptir:

* Tanılama günlüklerinin nereye gönderileceğini belirtir. Azure depolama hesabı, Azure Olay Hub 'ı veya Izleme günlüklerini örnek olarak içerir.
* Hangi günlük kategorilerinin gönderileceğini belirtir.
* Her günlük kategorisinin bir depolama hesabında ne kadar süreyle tutulması gerektiğini belirtir.
* Sıfır gün saklama, günlüklerin sınırsız süreyle tutulacağı anlamına gelir. Aksi takdirde, değer 1 ile 2.147.483.647 arasında herhangi bir sayıda gün olabilir.
* Bekletme ilkeleri ayarlanır ancak günlükleri bir depolama hesabında depolamak devre dışı bırakılırsa, bekletme ilkelerinin etkisi yoktur. Örneğin, bu durum yalnızca Event Hubs veya Izleyici günlükleri seçeneklerinin seçildiği zaman gerçekleşebilir.
* Bekletme ilkeleri gün başına uygulanır. Günler arasındaki sınır, Eşgüdümlü Evrensel Saat (UTC) ile gece yarısı arasında gerçekleşir. Bir günün sonunda, bekletme ilkesinin ötesinde olan günlerdeki Günlükler silinir. Örneğin, bir güne ait bir bekletme ilkeniz varsa, bugünün başlangıcında dün olan Günlükler silinir.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Izleyici REST API aracılığıyla tanılama günlüklerini etkinleştirme

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Izleyici REST API bir tanılama ayarı oluşturun veya güncelleştirin

##### <a name="request"></a>İstek

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Üst Bilgiler

* `{api-version}` yerine `2016-09-01` yazın.
* `{resource-id}`Tanılama ayarlarını düzenlemek istediğiniz KAYNAĞıN kimliğiyle değiştirin. Daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek Için kaynak gruplarını kullanma](../azure-resource-manager/management/manage-resource-groups-portal.md).
* `Content-Type`Üstbilgiyi olarak ayarlayın `application/json` .
* Yetkilendirme üst bilgisini Azure Active Directory (Azure AD) ' dan aldığınız JSON Web belirtecine ayarlayın. Daha fazla bilgi için bkz. [Istekleri kimlik doğrulama](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="body"></a>Gövde

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Özellik | Tür | Description |
| --- | --- | --- |
| **Storageaccountıd** |Dize | Tanılama günlükleri göndermek istediğiniz depolama hesabının kaynak KIMLIĞI. |
| **Servicebusruleıd** |Dize | ' In, akış tanılama günlükleri için Event Hubs oluşturulmasını istediğiniz hizmet veri yolu ad alanının hizmet veri yolu kuralı KIMLIĞI. Kural KIMLIĞI biçimi vardır `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Dize | Günlüklerin kaydedileceği çalışma alanının çalışma alanı KIMLIĞI. |
|**ölçümler**| Çağrılan işlem hattına geçirilecek işlem hattının parametre değerleri| Parametre adlarını bağımsız değişken değerleriyle eşleyen bir JSON nesnesi. |
| **açıldığında**| Karmaşık Tür| Kaynak türü için bir tanılama günlüğü kategorisinin adı. Bir kaynak için tanılama günlük kategorilerinin listesini almak için, tanı ayarlarını al işlemini gerçekleştirin. |
| **alan**| Dize| Bir dizi günlük kategorisi ve bekletme ilkeleri. |
| **zamandilimi** | Dize | ISO 8601 Duration biçiminde yakalanan ölçüm ayrıntı düzeyi. Özellik değeri `PT1M` bir dakika belirten olmalıdır. |
| **etkinletir**| Boole | Ölçüm veya günlük kategorisi koleksiyonunun bu kaynak için etkinleştirilip etkinleştirilmeyeceğini belirtir. |
| **retentionPolicy**| Karmaşık Tür| Ölçüm veya günlük kategorisi için bekletme ilkesini açıklar. Bu özellik yalnızca depolama hesapları için kullanılır. |
|**miş**| int| Ölçüm veya günlüklerin saklanacağı gün sayısı. Özellik değeri 0 ise, Günlükler süresiz tutulur. Bu özellik yalnızca depolama hesapları için kullanılır. |

##### <a name="response"></a>Yanıt

200 TAMAM.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Izleyici REST API Tanılama ayarları hakkında bilgi alın

##### <a name="request"></a>İstek

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Üst Bilgiler

* `{api-version}` yerine `2016-09-01` yazın.
* `{resource-id}`Tanılama ayarlarını düzenlemek istediğiniz KAYNAĞıN kimliğiyle değiştirin. Daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek Için kaynak gruplarını kullanma](../azure-resource-manager/management/manage-resource-groups-portal.md).
* `Content-Type`Üstbilgiyi olarak ayarlayın `application/json` .
* Yetkilendirme üst bilgisini Azure AD 'den aldığınız bir JSON Web belirtecine ayarlayın. Daha fazla bilgi için bkz. [Istekleri kimlik doğrulama](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="response"></a>Yanıt

200 TAMAM.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Daha fazla bilgi için bkz. [Tanılama ayarları](/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Günlüklerin ve olayların şeması

### <a name="monitor-schema"></a>Şemayı izle

#### <a name="activity-run-log-attributes"></a>Etkinlik-günlük özniteliklerini Çalıştır

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzeyde** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**Activityrunıd**| Dize| Etkinliğin çalıştırması KIMLIĞI. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**Ardışık düzen eylemsizlik kimliği**| Dize| İşlem hattı çalıştırmasının KIMLIĞI. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini olarak ayarlayın `ActivityRuns` . | `ActivityRuns` |
|**düzey**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini olarak ayarlayın `Informational` . | `Informational` |
|**operationName**| Dize | Etkinliğin durumuyla ilgili ad. Etkinlik başlangıç sinyalinise, özellik değeri olur `MyActivity -` . Etkinlik son sinyaldir ise, özellik değeri olur `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**Ardışık Düzen adı**| Dize | İşlem hattının adı. | `MyPipeline` |
|**activityName**| Dize | Etkinliğin adı. | `MyActivity` |
|**başından**| Dize | Etkinliğin Başlangıç saati, TimeSpan UTC biçiminde çalışır. | `2017-06-26T20:55:29.5007959Z`|
|**erer**| Dize | Etkinliğin bitiş saati, TimeSpan UTC biçiminde çalışır. Tanılama günlüğünde bir etkinliğin başlatıldığını ancak henüz bitmemiş olduğunu gösteriyorsa, özellik değeri olur `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>İşlem hattı-günlük özniteliklerini çalıştırma

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzeyde** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**RunId**| Dize| İşlem hattı çalıştırmasının KIMLIĞI. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini olarak ayarlayın `PipelineRuns` . | `PipelineRuns` |
|**düzey**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini olarak ayarlayın `Informational` . | `Informational` |
|**operationName**| Dize | İşlem hattının, durumu ile birlikte adı. İşlem hattı çalıştırması tamamlandıktan sonra özellik değeri olur `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**Ardışık Düzen adı**| Dize | İşlem hattının adı. | `MyPipeline` |
|**başından**| Dize | Etkinliğin Başlangıç saati, TimeSpan UTC biçiminde çalışır. | `2017-06-26T20:55:29.5007959Z`. |
|**erer**| Dize | Etkinliğin bitiş saati, TimeSpan UTC biçiminde çalışır. Tanılama günlüğünde bir etkinlik başlatılmış ancak henüz bitmemişse, özellik değeri olur `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**durumlarına**| Dize | İşlem hattının son durumu. Olası özellik değerleri `Succeeded` ve ' dir `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Tetikleme-günlük özniteliklerini Çalıştır

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Özellik | Tür | Açıklama | Örnek |
| --- | --- | --- | --- |
| **Düzeyde** |Dize | Tanılama günlüklerinin düzeyi. Etkinlik çalıştırma günlükleri için özellik değerini 4 olarak ayarlayın. | `4` |
| **ID** |Dize | Belirli bir isteği izlemeye yönelik benzersiz KIMLIK. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **ışınızda** | Dize | Zaman aralığı UTC biçimindeki olayın saati `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**Triggerıd**| Dize| Tetikleyici çalıştırmasının KIMLIĞI. | `08587023010602533858661257311` |
|**RESOURCEID**| Dize | Data Factory kaynağıyla ilişkili KIMLIK. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**alan**| Dize | Tanılama günlüklerinin kategorisi. Özellik değerini olarak ayarlayın `PipelineRuns` . | `PipelineRuns` |
|**düzey**| Dize | Tanılama günlüklerinin düzeyi. Özellik değerini olarak ayarlayın `Informational` . | `Informational` |
|**operationName**| Dize | Tetikleyicinin başarıyla tetikleyip tetiklenmediğini belirten, son durumu ile tetikleyicinin adı. Sinyal başarılı olduysa, özellik değeri olur `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Dize | Tetikleyicinin adı. | `MyTrigger` |
|**triggerType**| Dize | Tetikleyicinin türü. Olası özellik değerleri `Manual Trigger` ve ' dir `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Dize | Tetikleyicinin olayı. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**başından**| Dize | Zaman aralığı UTC biçiminde tetikleyicinin tetiklemenin başlangıç saati. | `2017-06-26T20:55:29.5007959Z`|
|**durumlarına**| Dize | Tetikleyicinin başarıyla harekete geçirilip tetiklenmediğini gösteren nihai durum. Olası özellik değerleri `Succeeded` ve ' dir `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS tümleştirme çalışma zamanı günlük öznitelikleri

SSIS IR Başlat/Durdur/bakım işlemlerinin günlük öznitelikleri aşağıda verilmiştir.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Özellik                   | Tür   | Açıklama                                                   | Örnek                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **ışınızda**                   | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Dize | SSIS IR işleminizi adı                            | `Start/Stop/Maintenance` |
| **alan**               | Dize | Tanılama günlükleri kategorisi                               | `SSISIntegrationRuntimeLogs` |
| **ID**          | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Dize | ADF 'nizin adı                                          | `MyADFv2` |
| **ıntegrationruntimename** | Dize | SSIS IR 'nizin adı                                      | `MySSISIR` |
| **düzey**                  | Dize | Tanılama günlüklerinin düzeyi                                  | `Informational` |
| **'ı**             | Dize | SSIS IR işleminin sonucu                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | Dize | SSIS IR işleminizi çıkış iletisi                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **RESOURCEID**             | Dize | ADF kaynağınızın benzersiz KIMLIĞI                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS olay iletisi bağlam günlüğü öznitelikleri

SSIS IR 'niz üzerinde SSIS paket yürütmeleri tarafından oluşturulan olay iletileriyle ilgili koşulların günlük öznitelikleri aşağıda verilmiştir. Birçok SSIS paketi özelliklerinin çalışma zamanı değerlerini gösteren [SSIS Katalog (SSıSDB) olay iletisi bağlam tablosu veya görünümü](/sql/integration-services/system-views/catalog-event-message-context) gibi benzer bilgileri alırlar. `Basic/Verbose`Günlüğe kaydetme düzeyi ' ni seçtiğinizde ve hata ayıklama/uyumluluk denetimi için faydalı olduğunda oluşturulur.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Özellik                   | Tür   | Açıklama                                                          | Örnek                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **ışınızda**                   | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Dize | Bu ayarı `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **alan**               | Dize | Tanılama günlükleri kategorisi                                      | `SSISPackageEventMessageContext` |
| **ID**          | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Dize | ADF 'nizin adı                                                 | `MyADFv2` |
| **ıntegrationruntimename** | Dize | SSIS IR 'nizin adı                                             | `MySSISIR` |
| **düzey**                  | Dize | Tanılama günlüklerinin düzeyi                                         | `Informational` |
| **operationId**            | Dize | SSSıSDB 'de belirli bir işlemi izlemeye yönelik benzersiz KIMLIK          | `1` (1 SSSıSDB 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili işlemleri belirtir) |
| **contextDepth**           | Dize | Olay iletisi bağlamınızın derinliği                              | `0` (0 paket yürütme başlamadan önce bağlamı belirtir, 1 hata oluştuğunda bağlamı belirtir ve bağlam hatadan daha fazla olduğunda artar) |
| **packagePath**            | Dize | Olay iletisi bağlam kaynağınız olarak paket nesnesinin yolu      | `\Package` |
| **contextType**            | Dize | Olay iletisi bağlam kaynağınız olarak paket nesnesi türü      | `60`( [daha fazla bağlam türü](/sql/integration-services/system-views/catalog-event-message-context#remarks)görüntüleyin) |
| **contextSourceName**      | Dize | Olay iletisi bağlam kaynağınız olarak paket nesnesinin adı      | `MyPackage` |
| **Contextsourceıd**        | Dize | Olay iletisi bağlam kaynağınız olarak paket nesnesinin benzersiz KIMLIĞI | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **Başlaması**           | Dize | Olay iletisi bağlam kaynağınız için paket özelliğinin adı   | `DelayValidation` |
| **propertyValue**          | Dize | Olay iletisi bağlam kaynağınız için paket özelliğinin değeri  | `False` |
| **RESOURCEID**             | Dize | ADF kaynağınızın benzersiz KIMLIĞI                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS olay iletileri günlük öznitelikleri

SSIS IR 'niz üzerinde SSIS paket yürütmeleri tarafından oluşturulan olay iletilerinin günlük öznitelikleri aşağıda verilmiştir. Olay iletilerinin ayrıntılı metin/meta verilerini gösteren [Sssısdb olay iletileri tablosu veya görünümü](/sql/integration-services/system-views/catalog-event-messages) gibi benzer bilgileri alırlar. Bunlar haricinde herhangi bir günlük düzeyinde oluşturulur `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Özellik                   | Tür   | Açıklama                                                        | Örnek                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **ışınızda**                   | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Dize | Bu ayarı `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **alan**               | Dize | Tanılama günlükleri kategorisi                                    | `SSISPackageEventMessages` |
| **ID**          | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Dize | ADF 'nizin adı                                               | `MyADFv2` |
| **ıntegrationruntimename** | Dize | SSIS IR 'nizin adı                                           | `MySSISIR` |
| **düzey**                  | Dize | Tanılama günlüklerinin düzeyi                                       | `Informational` |
| **operationId**            | Dize | SSSıSDB 'de belirli bir işlemi izlemeye yönelik benzersiz KIMLIK        | `1` (1 SSSıSDB 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili işlemleri belirtir) |
| **messageTime**            | Dize | Olay iletinizin UTC biçiminde oluşturulduğu zaman          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | Dize | Olay iletinizin türü                                     | `70`( [daha fazla ileti türüne](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)bakın) |
| **messageSourceType**      | Dize | Olay iletisi kaynağınızın türü                              | `20`( [daha fazla ileti kaynağı türü](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)görüntüleyin) |
| **message**                | Dize | Olay iletinizin metni                                     | `MyPackage:Validation has started.` |
| **PaketAdı**            | Dize | Yürütülen paket dosyanızın adı                             | `MyPackage.dtsx` |
| **eventName**              | Dize | İlgili çalışma zamanı olayının adı                                 | `OnPreValidate` |
| **messageSourceName**      | Dize | Olay iletisi kaynağınız olarak paket bileşeninin adı         | `Data Flow Task` |
| **Iletiourceıd**        | Dize | Olay iletisi kaynağınız olarak paket bileşeninin benzersiz KIMLIĞI    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **Alt ComponentName**       | Dize | Olay iletisi kaynağınız olarak veri akışı bileşeninin adı       | `SSIS.Pipeline` |
| **packagePath**            | Dize | Olay iletisi kaynağınız olarak paket nesnesinin yolu            | `\Package\Data Flow Task` |
| **executionPath**          | Dize | Ana paketten yürütülen bileşene tam yol            | `\Transformation\Data Flow Task` (Bu yol bileşen yinelemelerini de yakalar) |
| **ThreadID**               | Dize | Olay iletiniz günlüğe kaydedildiğinde yürütülen iş parçacığının benzersiz KIMLIĞI | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS çalıştırılabilir istatistik günlüğü öznitelikleri

İşte SSIS IR 'niz üzerinde SSIS paket yürütmeleri tarafından oluşturulan yürütülebilir istatistiklerin günlük öznitelikleri burada, yürütülebilir dosyalar paketlerin Denetim akışında kapsayıcı veya görevlerdir. Bunlar, yineleme dahil, çalışan her çalıştırılabilir için bir satır gösteren [Sssısdb çalıştırılabilir istatistik tablosu veya görünümü](/sql/integration-services/system-views/catalog-executable-statistics) gibi benzer bilgileri de iletirler. Bunlar dışında `None` , görev düzeyi darboğazları/başarısızlıklarını belirlemek için yararlı olan herhangi bir günlük düzeyinde oluşturulur.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Özellik                   | Tür   | Açıklama                                                      | Örnek                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **ışınızda**                   | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Dize | Bu ayarı `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **alan**               | Dize | Tanılama günlükleri kategorisi                                  | `SSISPackageExecutableStatistics` |
| **ID**          | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Dize | ADF 'nizin adı                                             | `MyADFv2` |
| **ıntegrationruntimename** | Dize | SSIS IR 'nizin adı                                         | `MySSISIR` |
| **düzey**                  | Dize | Tanılama günlüklerinin düzeyi                                     | `Informational` |
| **Yürütme**            | Dize | SSSıSDB 'de belirli bir yürütmeyi izlemeye yönelik benzersiz KIMLIK      | `1` (1 SSSıSDB 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili yürütmeleri belirtir) |
| **executionPath**          | Dize | Ana paketten yürütülen bileşene tam yol          | `\Transformation\Data Flow Task` (Bu yol bileşen yinelemelerini de yakalar) |
| **startTime**              | Dize | Yürütülebilir zaman, UTC biçiminde yürütme öncesi aşamasına giriyor  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Dize | Yürütülebilir dosyanın UTC biçiminde yürütme sonrası aşamasına girdiğinde geçen süre | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Dize | Çalıştırmanın çalışma süresi (milisaniye)                   | `1,125` |
| **executionResult**        | Dize | Yürütülebilir dosya çalıştırmanın sonucu                                 | `0` (0 başarıyı belirtir, 1 hata belirtir, 2 tamamlanmayı belirtir ve 3, iptal) |
| **executionValue**         | Dize | Yürütülebilir dosya çalıştırılarak döndürülen Kullanıcı tanımlı değer            | `1` |
| **RESOURCEID**             | Dize | ADF kaynağınızın benzersiz KIMLIĞI                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS yürütme bileşeni aşama günlüğü öznitelikleri

SSIS IR 'niz üzerinde SSIS paket yürütmeleri tarafından oluşturulan veri akışı bileşenlerine ilişkin çalışma zamanı istatistiklerinin günlük öznitelikleri aşağıda verilmiştir. Bunlar, tüm yürütme aşamalarında veri akışı bileşenleri tarafından harcanan süreyi gösteren [Sssısdb yürütme bileşeni aşamaları tablosu veya görünümü](/sql/integration-services/system-views/catalog-execution-component-phases) olarak benzer bilgileri iletirler. `Performance/Verbose`Günlüğe kaydetme düzeyi ' ni seçtiğinizde ve veri akışı yürütme istatistiklerini yakalamak için işinize yarayacaktır.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Özellik                   | Tür   | Açıklama                                                         | Örnek                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **ışınızda**                   | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Dize | Bu ayarı `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **alan**               | Dize | Tanılama günlükleri kategorisi                                     | `SSISPackageExecutionComponentPhases` |
| **ID**          | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Dize | ADF 'nizin adı                                                | `MyADFv2` |
| **ıntegrationruntimename** | Dize | SSIS IR 'nizin adı                                            | `MySSISIR` |
| **düzey**                  | Dize | Tanılama günlüklerinin düzeyi                                        | `Informational` |
| **Yürütme**            | Dize | SSSıSDB 'de belirli bir yürütmeyi izlemeye yönelik benzersiz KIMLIK         | `1` (1 SSSıSDB 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili yürütmeleri belirtir) |
| **PaketAdı**            | Dize | Yürütülen paket dosyanızın adı                              | `MyPackage.dtsx` |
| **Silinecek**               | Dize | Yürütülen veri akışı görevinin adı                                 | `Data Flow Task` |
| **Alt ComponentName**       | Dize | Veri akışı bileşeninin adı                                     | `Derived Column` |
| **aşaması**                  | Dize | Yürütme aşamasının adı                                         | `AcquireConnections` |
| **startTime**              | Dize | Yürütme aşamasının UTC biçiminde başladığı saat                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Dize | Yürütme aşamasının UTC biçiminde bittiği zaman                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Dize | Veri akışı görevinin yürütme yolu                            | `\Transformation\Data Flow Task` |
| **RESOURCEID**             | Dize | ADF kaynağınızın benzersiz KIMLIĞI                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS yürütme verileri istatistikleri günlük öznitelikleri

Veri akışı işlem hatlarında, SSIS IR 'niz üzerinde SSIS paket yürütmeleri tarafından oluşturulan yukarı akış bileşenlerinden alınan veri hareketlerinin günlük öznitelikleri aşağıda verilmiştir. Veri akışı görevleri aracılığıyla taşınan veri satır sayılarını gösteren [SSISDB yürütme verileri istatistikleri tablosu veya görünümü](/sql/integration-services/system-views/catalog-execution-data-statistics) gibi benzer bilgileri de iletirler. `Verbose`Günlüğe kaydetme düzeyi ' ni seçtiğinizde ve veri akışı iş akışını hesaplama için işinize yarayacaktır.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Özellik                     | Tür   | Açıklama                                                        | Örnek                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **ışınızda**                     | Dize | UTC biçiminde olay saati: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Dize | Bu ayarı `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **alan**                 | Dize | Tanılama günlükleri kategorisi                                    | `SSISPackageExecutionDataStatistics` |
| **ID**            | Dize | Belirli bir işlemi izlemeye yönelik benzersiz KIMLIK                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Dize | ADF 'nizin adı                                               | `MyADFv2` |
| **ıntegrationruntimename**   | Dize | SSIS IR 'nizin adı                                           | `MySSISIR` |
| **düzey**                    | Dize | Tanılama günlüklerinin düzeyi                                       | `Informational` |
| **Yürütme**              | Dize | SSSıSDB 'de belirli bir yürütmeyi izlemeye yönelik benzersiz KIMLIK        | `1` (1 SSSıSDB 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili yürütmeleri belirtir) |
| **PaketAdı**              | Dize | Yürütülen paket dosyanızın adı                             | `MyPackage.dtsx` |
| **Silinecek**                 | Dize | Yürütülen veri akışı görevinin adı                                | `Data Flow Task` |
| **Dataflowpathıdstring**     | Dize | İzleme veri akışı yolunun benzersiz KIMLIĞI                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Dize | Veri akışı yolunun adı                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Dize | Veri gönderen veri akışı bileşeninin adı                    | `SQLDB Table3` |
| **destinationComponentName** | Dize | Veri alan veri akışı bileşeninin adı                 | `Derived Column` |
| **rowsSent**                 | Dize | Kaynak bileşen tarafından gönderilen satır sayısı                        | `500` |
| **createdTime**              | Dize | UTC biçiminde satır değerlerinin alındığı zaman                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Dize | Veri akışı görevinin yürütme yolu                           | `\Transformation\Data Flow Task` |
| **RESOURCEID**               | Dize | ADF kaynağınızın benzersiz KIMLIĞI                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics şeması

Log Analytics şemayı Izleyiciden aşağıdaki özel durumlarla devralır:

* Her sütun adının ilk harfi büyük harfle ayarlanır. Örneğin, Monitor içindeki "Bağıntıkimliği" sütun adı, Log Analytics "Bağıntıkimliği" dır.
* "Düzey" sütunu yok.
* Dinamik "Özellikler" sütunu aşağıdaki dinamik JSON blob türü olarak korunur.

    | Azure Izleyici sütunu | Log Analytics sütunu | Tür |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinamik |
    | $. Properties. Açıklamaları | Ek Açıklamalar | Dinamik |
    | $. Properties. Girişinin | Giriş | Dinamik |
    | $. Properties. Çıktıların | Çıktı | Dinamik |
    | $. Properties. Hata. errorCode | ErrorCode | int |
    | $. Properties. Hata. ileti | Hata | string |
    | $. Properties. Hatayla | Hata | Dinamik |
    | $. Properties. Öncül | Öncül | Dinamik |
    | $. Properties. Parametrelere | Parametreler | Dinamik |
    | .properties.SystemParameters | SystemParameters | Dinamik |
    | $. Properties. Lerimi | Etiketler | Dinamik |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Izleyici ile SSIS işlemlerini izleme

SSIS iş yüklerinizi kaldırmak & geçiş yapmak için, [ADF 'de SSIS IR](./tutorial-deploy-ssis-packages-azure.md) 'yi şunları destekler:

- Azure SQL veritabanı sunucusu/yönetilen örneği (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma
- Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtılan Paketleri çalıştırma

Sağlandıktan sonra, [Azure PowerShell veya ADF portalının **Monitor** hub 'ında bulunan SSIS IR işletimsel durumunu denetleyebilirsiniz](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Proje dağıtım modeliyle, SSIS paketi yürütme günlükleri SSSıSDB iç tablolarında veya görünümlerinde depolanır, bu sayede SSMS gibi tasarlanan araçları kullanarak bunları sorgulayabilir, çözümleyebilir ve görsel olarak sunabilirsiniz. Paket dağıtım modeli ile, SSIS paketi yürütme günlükleri dosya sistemi veya Azure dosyalarında, sorgu, analiz etmeden ve görsel olarak kullanmadan önce diğer belirlenen araçları kullanarak ayrıştırmanız ve işlem yapmanız gereken CSV dosyaları olarak depolanabilir.

Artık [Azure izleyici](../azure-monitor/data-platform.md) TÜMLEŞTIRMESIYLE, SSIS IR işlemlerinden oluşturulan tüm ölçümleri ve günlükleri ve Azure Portal üzerinde SSIS paket yürütmelerini sorgulayabilir, çözümleyebilir ve görsel olarak sunabilirsiniz. Ayrıca, bunlar üzerinde de uyarı oluşturabilirsiniz.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS işlemleri için tanılama ayarlarını ve çalışma alanını yapılandırma

SSIS IR işlemlerinden ve SSIS paket yürütmelerinin Azure Izleyici 'de oluşturulan tüm ölçümleri ve günlükleri göndermek için, [ADF 'niz için tanılama ayarlarını ve çalışma alanını yapılandırmanız](#configure-diagnostic-settings-and-workspace)gerekir.

### <a name="ssis-operational-metrics"></a>SSIS işletimsel ölçümleri

SSIS işletimsel [ölçümleri](../azure-monitor/essentials/data-platform-metrics.md) , SSIS IR başlatma ve durdurma işlemlerinin durumunu ve belirli bır zamanda SSIS paket yürütmelerini tanımlayan, performans sayaçları veya sayısal değerlerdir. Bunlar, [Azure izleyici 'Deki ADF ölçümlerinin](#data-factory-metrics)bir parçasıdır.

ADF 'niz Azure Izleyici 'de tanılama ayarlarını ve çalışma alanını yapılandırdığınızda, _Allölçümler_ onay kutusunun BELIRLENMESI, SSIS Işletimsel ölçümlerini Azure Ölçüm Gezgini, [Azure panosu 'nda sunumu](../azure-monitor/app/tutorial-app-dashboards.md)ve [neredeyse gerçek zamanlı uyarılar](../azure-monitor/alerts/alerts-metric.md) [kullanarak etkileşimli analiz](../azure-monitor/essentials/metrics-getting-started.md)için kullanılabilir hale getirir.

![Ayarlarınızı adlandırın ve bir Log Analytics çalışma alanı seçin](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS işletimsel uyarıları

ADF portalından SSIS işletimsel ölçümlerinde uyarılar yükseltmek için [ADF **izleyici** hub 'ının **Uyarılar & ölçümler** sayfasını seçin ve sunulan adım adım yönergeleri izleyin](./monitor-visually.md#alerts).

![SSIS işletimsel uyarılarını ADF portalından oluşturma](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Azure portal SSIS işletimsel ölçümlerinde uyarılar yükseltmek için [Azure **izleyici** hub ' ın **Uyarılar** sayfasını seçin ve sunulan adım adım yönergeleri izleyin](#data-factory-alerts).

![Azure portal SSIS işletimsel uyarılarını yükseltme](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS işletimsel günlükleri

SSIS işletimsel [günlükleri](../azure-monitor/logs/data-platform-logs.md) , tanımlanan herhangi bir sorun üzerinde yeterli bağlam sunan ve kök neden analizi için YARARLı olan SSIS IR IŞLEMLERI ve SSIS paket yürütmeleri tarafından oluşturulan olaylardır. 

ADF 'niz Azure Izleyicisinde tanılama ayarlarını ve çalışma alanını yapılandırdığınızda ilgili SSIS işletimsel günlüklerini seçip Azure Veri Gezgini tabanlı Log Analytics gönderebilirsiniz. Burada, [zengin sorgu dili](../azure-monitor/logs/log-query-overview.md), [Azure panosu 'nda sunum](../azure-monitor/app/tutorial-app-dashboards.md)ve [gerçek zamanlı uyarılar](../azure-monitor/alerts/alerts-log.md)kullanılarak analiz için kullanılabilir hale getirilir.

![Ayarlarınızı adlandırın ve bir Log Analytics çalışma alanı seçin](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Izleyici ve Log Analytics SSIS paketi yürütme günlüklerinin şemaları ve içerikleri, SSıSDB iç tablolarının veya görünümlerinin şemalarına benzer.

| Azure Izleyici günlük kategorileri          | Log Analytics tabloları                     | SSıSDB iç tabloları/görünümleri              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS işletimsel günlük öznitelikleri/özellikleri hakkında daha fazla bilgi için bkz. [ADF Için Azure izleyici ve Log Analytics şemaları](#schema-of-logs-and-events).

Seçtiğiniz SSIS paketi yürütme günlükleriniz her zaman çağrı metotlarından bağımsız olarak Log Analytics gönderilir. Örneğin, Azure özellikli SSDT 'de paket yürütmelerini, SSMS üzerinde T-SQL, SQL Server Agent veya diğer belirlenmiş araçlar aracılığıyla veya ADF işlem hatlarında SSIS paketi yürütme etkinliklerini tetiklemeli veya hata ayıklama çalıştırmaları olarak çalıştırabilirsiniz.

Günlük Analizi üzerinde SSIS IR işlem günlükleri sorgulanırken, sırasıyla ve olarak ayarlanan **OperationName** ve **ResultType** özellikleri kullanabilirsiniz `Start/Stop/Maintenance` `Started/InProgress/Succeeded/Failed` . 

![Log Analytics SSIS IR işlem günlüklerini sorgulama](media/data-factory-monitor-oms/log-analytics-query.png)

Günlük Analizi üzerinde SSIS paketi yürütme günlüklerini sorgularken, **operationId** / **ExecutionID** / **CorrelationProperties** kullanarak bunlara katabilirsiniz. **OperationId** / **ExecutionID** her zaman `1` sssısdb 'de **depolanmayan** ve T-SQL aracılığıyla çağrılan paketlerle ilgili tüm işlemler/yürütmeler için olarak ayarlanır.

![Log Analytics SSIS paketi yürütme günlükleri sorgulanıyor](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Sonraki adımlar
[İşlem hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md)
