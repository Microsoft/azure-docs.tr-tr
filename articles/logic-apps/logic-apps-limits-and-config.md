---
title: Limitler ve yapılandırma
description: Süre, verimlilik ve kapasite gibi hizmet limitleri ve izin verilecek IP adresleri gibi yapılandırma değerleri, Azure Logic Apps için
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/16/2021
ms.openlocfilehash: 286da1412e8a74ffbf34e4abb493241914d4f925
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764882"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Azure Logic Apps için sınırlar ve yapılandırma bilgileri

Bu makalede, Azure Logic Apps ile otomatik iş akışları oluşturma ve çalıştırmaya yönelik sınırlar ve yapılandırma ayrıntıları açıklanmaktadır. Güç otomatikleştirme için bkz. [Güç otomatikleştirmede sınırlar ve yapılandırma](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Mantıksal uygulama tanımı sınırları

Tek bir mantıksal uygulama tanımının sınırları aşağıda verilmiştir:

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| İş akışı başına eylemler | 500 | Bu sınırı genişletmek için, gerektiği gibi iç içe geçmiş iş akışları ekleyebilirsiniz. |
| Eylemler için izin verilen iç içe geçme derinliği | 8 | Bu sınırı genişletmek için, gerektiği gibi iç içe geçmiş iş akışları ekleyebilirsiniz. |
| Abonelik başına bölge başına iş akışı | 1.000 | |
| İş akışı başına Tetikleyiciler | 10 | Tasarımcı değil kod görünümünde çalışırken |
| Kapsam durumlarının sınırlarını Değiştir | 25 | |
| İş akışı başına değişkenler | 250 | |
| Or için ad `action``trigger` | 80 karakter | |
| İfade başına karakter | 8,192 | |
| Uzunluğu `description` | 256 karakter | |
| En fazla sayı `parameters` | 50 | |
| En fazla sayı `outputs` | 10 | |
| En büyük boyut `trackedProperties` | 16.000 karakter |
| Satır içi kod eylemi-en fazla kod karakter sayısı | 1.024 karakter | Bu sınırı 100.000 karaktere genişletmek için mantıksal **uygulama (Önizleme)** kaynak türü ile, [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) veya [Visual Studio Code ve **Azure Logic Apps (Önizleme)** uzantısını](create-stateful-stateless-workflows-visual-studio-code.md)kullanarak mantıksal uygulamalarınızı oluşturun. |
| Satır içi kod eylemi-kod çalıştırmak için maksimum süre | 5 saniye | Bu sınırı 15 saniyeye uzatmak için, mantıksal **uygulama (Önizleme)** kaynak türü ile, [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) veya [Visual Studio Code ve **Azure Logic Apps (Önizleme)** uzantısını](create-stateful-stateless-workflows-visual-studio-code.md)kullanarak mantıksal uygulamalarınızı oluşturun. |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Çalıştırma süresi ve bekletme geçmişi sınırları

Tek bir mantıksal uygulama çalıştırması için sınırlar şunlardır:

| Name | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| Çalıştırma süresi | 90 gün | 366 gün | Çalıştırma süresi, bir çalıştırmanın başlangıç saati ve iş akışı ayarında belirtilen sınır kullanılarak hesaplanır, bu başlangıç saatinde [**Geçmiş bekletme gerçekleştirin**](#change-duration) . <p><p>Varsayılan sınırı değiştirmek için bkz. [depolamada çalışma süresini ve geçmiş bekletmesini değiştirme](#change-duration). |
| Depolama alanında çalışma geçmişi tutma | 90 gün | 366 gün | Bir çalıştırmanın süresi geçerli çalışma geçmişi saklama sınırını aşarsa, çalışma, depolama alanındaki çalıştırma geçmişinden kaldırılır. Çalıştırmanın tamamlanması veya zaman aşımına uğrayıp, çalıştırma geçmişi saklama, iş akışı ayarında belirtilen başlangıç saati ve geçerli sınır kullanılarak her zaman hesaplanır, [**geçmiş tutma işlemini gün olarak çalıştırın**](#change-retention). Önceki sınıra bakılmaksızın, geçerli sınır, saklama hesaplaması için her zaman kullanılır. <p><p>Varsayılan sınırı değiştirmek ve daha fazla bilgi için bkz. [depolama alanında değiştirme süresi ve çalıştırma geçmişi tutma](#change-retention). Maksimum limiti artırmak için, gereksinimlerle ilgili yardım için [Logic Apps ekibine başvurun](mailto://logicappspm@microsoft.com) . |
| En az yinelenme aralığı | 1 saniye | 1 saniye ||
| En fazla yinelenme aralığı | 500 gün | 500 gün ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Depolama sırasında çalışma süresini ve geçmiş bekletmesini değiştirme

Aynı ayar, bir iş akışının çalıştıracağı en fazla gün sayısını ve depolama alanında çalıştırma geçmişinin tutulmasını sağlar. Bu özelliklerin varsayılan veya geçerli sınırını değiştirmek için aşağıdaki adımları izleyin.

* Çok kiracılı Azure 'da mantıksal uygulamalar için, 90 günlük varsayılan sınır, üst limitle aynıdır. Bu değeri yalnızca azaltabilirsiniz.

* Bir tümleştirme hizmeti ortamındaki Logic Apps için 90 günlük varsayılan sınırı azaltabilir veya artırabilirsiniz.

Örneğin, 90 günden 30 güne kadar olan bekletme limitini azaltdığınızı varsayalım. Bir 60 günlük eski çalıştırma, çalışma geçmişinden kaldırılır. Bekletme süresini 30 günden 60 güne kadar artırırsanız, 20 günlük eski bir çalıştırma, çalışma geçmişinde başka bir 40 gün boyunca kalır.

> [!IMPORTANT]
> Bir çalıştırmanın süresi geçerli çalışma geçmişi saklama sınırını aşarsa, çalışma, depolama alanındaki çalıştırma geçmişinden kaldırılır. Çalıştırma geçmişinin kaybedilmesini önlemek için, bekletme sınırının *her zaman* çalıştırmanın en uzun süresinden daha fazla olduğundan emin olun.

1. [Azure Portal](https://portal.azure.com) arama kutusunda **Logic Apps**' i bulun ve seçin.

1. Mantıksal uygulamanızı bulun ve seçin. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulamanın menüsünde, **Iş akışı ayarları**' nı seçin.

1. **Çalışma zamanı seçenekleri** altında, **gün cinsinden çalışma geçmişi saklama** listesinden **özel**' i seçin.

1. İstediğiniz gün sayısını değiştirmek için kaydırıcıyı sürükleyin.

1. İşiniz bittiğinde, **Iş akışı ayarları** araç çubuğunda **Kaydet**' i seçin.

Mantıksal uygulamanız için bir Azure Resource Manager şablonu oluşturursanız, bu ayar iş akışınızın kaynak tanımında [Microsoft. Logic iş akışları şablon başvurusunda](/azure/templates/microsoft.logic/workflows)tanımlanan bir özellik olarak görünür:

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Eşzamanlılık, döngü ve toplu işleme sınırları

Tek bir mantıksal uygulama çalıştırması için sınırlar şunlardır:

### <a name="loops"></a>Döngüler

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| Foreach dizi öğeleri | 100.000 | Bu sınır, "for each" döngüsünün işleyeşabolduğu dizi öğelerinin en fazla sayısını açıklar. <p><p>Daha büyük dizileri filtrelemek için [sorgu eylemini](logic-apps-perform-data-operations.md#filter-array-action)kullanabilirsiniz. |
| Foreach eşzamanlılık | Eşzamanlılık kapalı ile: 20 <p><p>Eşzamanlılık açık: <p><p>-Varsayılan: 20 <br>-Min: 1 <br>-Max: 50 | Bu sınır, aynı anda veya paralel olarak çalışabilen en fazla "for each" döngüsü yinelemesi sayısıdır. <p><p>Bu sınırı değiştirmek için, bkz. ["her bir eşzamanlılık sınırı" değiştirme](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) veya ["for each" döngülerini sırayla çalıştırma](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Yinelemelere kadar | -Varsayılan: 60 <br>-Min: 1 <br>-Max: 5.000 | Bir "Until" döngüsünün mantıksal uygulama çalıştırması sırasında sahip olması için gereken en fazla döngü sayısı. <p><p>Bu sınırı değiştirmek için, "Until" döngüsünde şekil **Değiştir**' i seçin ve **Count** özelliği için değeri belirtin. |
| Until zaman aşımı | -Varsayılan: PT1H (1 saat) | "Until" döngüsünün çıkmadan önce çalıştırılabilmesi ve [ıso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601)belirtilmesi en fazla süre. Zaman aşımı değeri her döngü döngüsü için değerlendirilir. Döngüdeki herhangi bir eylem zaman aşımı sınırından daha uzun sürerse, geçerli döngü durdurulmaz. Ancak, bir sonraki döngüde, sınır koşulu karşılanmadığı için başlamaz. <p><p>Bu sınırı değiştirmek için, "Until" döngüsünde şekil **Değiştir**' i seçin ve **zaman aşımı** özelliği için değeri belirtin. |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Eşzamanlılık ve ayırmayı kaldırma

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| Eşzamanlılık tetikleme | Eşzamanlılık kapalı ile: sınırsız <p><p>' İ etkinleştirdikten sonra geri alamazsınız: <p><p>-Varsayılan: 25 <br>-Min: 1 <br>-Max: 100 | Bu sınır, aynı anda veya paralel olarak çalışabilen mantıksal uygulama örneklerinin maksimum sayısıdır. <p><p>**Note**: eşzamanlılık açık olduğunda, ayırma sınırı, [toplu işleme dizileri](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)için 100 öğeye düşürülür. <p><p>Bu sınırı değiştirmek için bkz. [tetikleyici eşzamanlılık limitini değiştirme](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) veya [örnekleri sıralı olarak tetikleme](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| En fazla bekleyen çalışma | Eşzamanlılık kapalı ile: <p><p>-Min: 1 <br>-Max: 50 <p><p>Eşzamanlılık açık: <p><p>-Min: 10 Plus eşzamanlı çalıştırma sayısı (eşzamanlılık tetikleme) <br>-Max: 100 | Bu sınır, mantıksal uygulamanız zaten en fazla eşzamanlı örnekleri çalıştırıyorsa çalışmayı beklemeleri gereken mantıksal uygulama örneklerinin maksimum sayısıdır. <p><p>Bu sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| SplitOn öğeleri | Eşzamanlılık kapalı: 100.000 <p><p>Eşzamanlılık açık: 100 | Bir dizi döndüren Tetikleyiciler için, dizi öğelerini bir "foreach" döngüsü kullanmak yerine [birden çok iş akışı örneğine ayıran veya](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) içermeyen bir ' spton ' özelliği kullanan bir ifade belirtebilirsiniz. Bu ifade, her dizi öğesi için bir iş akışı örneği oluşturmak ve çalıştırmak için kullanılacak diziye başvurur. <p><p>**Note**: eşzamanlılık açık olduğunda, spton limiti 100 öğeye düşürülür. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>İşleme sınırları

Tek bir mantıksal uygulama tanımının sınırları aşağıda verilmiştir:

### <a name="multi-tenant-logic-apps-service"></a>Çok kiracılı Logic Apps hizmeti

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| Eylem: 5 dakikalık toplama aralığı başına yürütme sayısı | -100.000 yürütmeler (varsayılan) <p><p>-300.000 yürütmeler (yüksek aktarım hızı modunda en fazla)  | Mantıksal uygulamanız için varsayılan sınırı en yüksek sınıra yükseltmek için, önizleme aşamasında olan [yüksek aktarım hızı modunda çalıştır](#run-high-throughput-mode)' a bakın. Ya da iş yükünü, gereken şekilde birden [fazla Logic App üzerinde dağıtabilirsiniz](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) . |
| Eylem: eşzamanlı giden çağrılar | Yaklaşık 2.500 | Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
| Çalışma zamanı uç noktası: eşzamanlı gelen çağrılar | ~ 1.000 | Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
| Çalışma zamanı uç noktası: 5 dakikada okuma çağrısı sayısı  | 60.000 | Bu sınır, mantıksal uygulama çalıştırma geçmişinden gelen ham giriş ve çıkışları alan çağrılar için geçerlidir. İş yükünü, gereken şekilde birden fazla uygulama arasında dağıtabilirsiniz. |
| Çalışma zamanı uç noktası: 5 dakika başına çağrı çağırma | 45.000 | İş yükünü, gerektiğinde birden fazla uygulama arasında dağıtabilirsiniz. |
| 5 dakika başına içerik işleme | 600 MB | İş yükünü, gerektiğinde birden fazla uygulama arasında dağıtabilirsiniz. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Yüksek aktarım hızı modunda çalıştır

Tek bir mantıksal uygulama tanımı için, 5 dakikada bir yürütülen eylemlerin sayısı [varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)sahiptir. Varsayılan sınırı üç kez olan mantıksal uygulamanızın [en üst sınırına](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) (varsayılan sınırı) yükseltmek için önizleme aşamasında olan yüksek aktarım hızı modunu etkinleştirebilirsiniz. Ya da iş yükünü, gereken şekilde birden [fazla Logic App üzerinde dağıtabilirsiniz](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) .

1. Azure portal, mantıksal uygulama menünüzde **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin.

1. **Çalışma zamanı seçenekleri**  >  **yüksek verimlilik** altında ayarını **Açık** olarak değiştirin.

   !["Iş akışı ayarları" ve "yüksek verimlilik" olarak ayarlanmış Azure portal Logic App menüsünü gösteren ekran görüntüsü "açık" olarak ayarlanır.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Mantıksal uygulamanızı dağıtmaya yönelik bir ARM şablonunda bu ayarı etkinleştirmek için, `properties` mantıksal uygulamanızın kaynak tanımına yönelik nesnede, `runtimeConfiguration` `operationOptions` özelliği olarak ayarlanmış nesnesini ekleyin `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Mantıksal uygulama kaynak tanımınız hakkında daha fazla bilgi için bkz. [genel bakış: Azure Resource Manager şablonları kullanarak Azure Logic Apps için dağıtımı otomatikleştirin](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Tümleştirme hizmeti ortamı (ıSE)

* [Geliştirici Ise SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): dakikada 500 yürütme sağlar, ancak aşağıdaki noktalara dikkat edin:

  * Bu SKU 'yu yalnızca araştırma, denemeleri, geliştirme veya test için (üretim veya performans testi için değil) kullandığınızdan emin olun. Bu SKU, geri dönüştürme sırasında bir hizmet düzeyi sözleşmesi (SLA), ölçek artırma yeteneği veya yedekliliği olmadığından gecikme veya kapalı kalma süresi yaşayabileceğiniz anlamına gelir.

  * Arka uç güncelleştirmeleri zaman zaman kesme hizmeti gerektirebilir.

* [Premium Ise SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): aşağıdaki tabloda, bu SKU 'nun üretilen iş sınırları açıklanmakta, ancak normal işlemede bu sınırları aşmamak veya bu limitlerin üzerine gidebilecek yük testi çalıştırmak için gereksinimlerinizin bulunduğu yardım için [Logic Apps ekibine başvurun](mailto://logicappsemail@microsoft.com) .

  | Name | Sınır | Notlar |
  |------|-------|-------|
  | Taban birimi yürütme sınırı | Altyapı kapasitesi %80 ' a ulaştığında sistem kısıtlanıyor | Ayda ~ 4.000 eylem yürütmelerinin sayısını sağlar; Bu, ayda ~ 160.000.000 eylem yürütmeleri |
  | Ölçek birimi yürütme sınırı | Altyapı kapasitesi %80 ' a ulaştığında sistem kısıtlanıyor | Her ölçek birimi, dakikada ~ 80.000.000 daha fazla eylem yürütmeleri olan ~ 2.000 ek eylem yürütmeleri sağlayabilir |
  | Ekleyebileceğiniz maksimum ölçek birimi | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Ağ geçidi limitleri

Azure Logic Apps, ağ geçidi aracılığıyla ekleme ve güncelleştirme dahil olmak üzere yazma işlemlerini destekler. Ancak, bu işlemlerin [Yük boyutuyla ilgili limitleri](/data-integration/gateway/service-gateway-onprem#considerations)vardır.

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP limitleri

Tek bir gelen veya giden çağrının sınırları aşağıda verilmiştir:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Zaman aşımı süresi

Bazı bağlayıcı işlemleri, zaman uyumsuz çağrılar yapar veya Web kancası isteklerini dinler, bu nedenle bu işlemler için zaman aşımı Bu limitlerden daha uzun olabilir. Daha fazla bilgi için, belirli bağlayıcının teknik ayrıntılarına ve ayrıca [Iş akışı tetikleyicilerine ve eylemlerine](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)bakın.

| Name | Logic Apps (çok kiracılı) | Logic Apps (Önizleme) | Tümleştirme hizmeti ortamı | Notlar |
|------|---------------------------|----------------------|---------------------------------|-------|
| Giden istek | 120 saniye <br>(2 dakika) | 230 saniye <br>(3,9 dakika) | 240 saniye <br>(4 dakika) | Giden istek örnekleri, HTTP tetikleyicisi veya eylemi tarafından yapılan çağrıları içerir. Önizleme sürümü hakkında daha fazla bilgi için bkz. [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**İpucu**: daha uzun süre çalışan işlemler için [zaman uyumsuz yoklama deseninin](../logic-apps/logic-apps-create-api-app.md#async-pattern) veya [until döngüsünün](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)kullanılması gerekir. [Çağrılabilir bir uç noktasına](logic-apps-http-endpoint.md)sahip başka bir mantıksal uygulamayı çağırdığınızda zaman aşımı sınırlarına geçici bir çözüm Için, yerleşik Azure Logic Apps eylemini kullanabilirsiniz. bunun yerine, **yerleşik olarak** bağlayıcı seçicisinde bulabilirsiniz. |
| Gelen istek | 120 saniye <br>(2 dakika) | 230 saniye <br>(3,9 dakika) | 240 saniye <br>(4 dakika) | Gelen istek örnekleri, Istek tetikleyicisi, HTTP Web kancası tetikleyicisi ve HTTP Web kancası eylemi tarafından alınan çağrıları içerir. Önizleme sürümü hakkında daha fazla bilgi için bkz. [Azure Logic Apps Preview](logic-apps-overview-preview.md). <p><p>**Note**: özgün arayanın yanıtı alması için, başka bir mantıksal uygulamayı iç içe geçmiş iş akışı olarak çağırmadığınız sürece yanıttaki tüm adımların sınırı içinde tamamlaması gerekir. Daha fazla bilgi için bkz. [çağrı, tetikleyici veya iç içe mantıksal uygulamalar](../logic-apps/logic-apps-http-endpoint.md). |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>İleti boyutu

| Name | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| İleti boyutu | 100 MB | 200 MB | Bu sınırı geçici olarak çözmek için bkz. [öbek ile büyük Iletileri işleme](../logic-apps/logic-apps-handle-large-messages.md). Ancak, bazı bağlayıcılar ve API 'Ler parçalama veya varsayılan sınırı desteklemeyebilir. <p><p>-AS2, x12 ve EDIOLGU gibi bağlayıcılar kendi [B2B ileti sınırlarına](#b2b-protocol-limits)sahiptir. <br>-ISE bağlayıcıları Ise bağlayıcı limitlerini değil ıSE sınırını kullanır. |
| Öbek ile ileti boyutu | 1 GB | 5 GB | Bu sınır, yerel olarak parçalama desteği olan veya çalışma zamanı yapılandırmasında parçalama özelliğini etkinleştirmenize izin veren eylemler için geçerlidir. <p><p>ISE kullanıyorsanız, Logic Apps altyapısı bu sınırı destekler, ancak bağlayıcılar altyapı sınırına kadar kendi parçalama sınırlarına sahiptir; Örneğin, [Azure Blob Storage BAĞLAYıCıSıNıN API başvurusu](/connectors/azureblob/). Öbek oluşturma hakkında daha fazla bilgi için bkz. [öbek ile büyük Iletileri işleme](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Karakter sınırları

| Name | Sınır | Notlar |
|------|-------|-------|
| İfade değerlendirme limiti | 131.072 karakter | , `@concat()` , `@base64()` `@string()` İfadeleri bu sınırdan daha uzun olamaz. |
| İstek URL 'SI karakter sınırı | 16.384 karakter | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Yeniden Deneme ilkesi

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| Yeniden deneme sayısı | 90 | Varsayılan değer 4'tür. Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
| En fazla gecikmede yeniden dene | 1 gün | Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
| Yeniden deneme en az gecikme süresi | 5 saniye | Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Kimlik doğrulama sınırları

Burada, Istek tetikleyicisiyle başlayan ve Istek tetikleyicisine gelen çağrıları yetkilendirmek için [Açık kimlik doğrulaması Azure Active Directory](../active-directory/develop/index.yml) (Azure AD OAuth) izin veren bir mantıksal uygulama sınırları verilmiştir:

| Name | Sınır | Notlar |
| ---- | ----- | ----- |
| Azure AD yetkilendirme ilkeleri | 5 | |
| Yetkilendirme ilkesi başına talepler | 10 | |
| Talep değeri-en fazla karakter sayısı | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Özel bağlayıcı limitleri

Web API 'Lerinden oluşturabileceğiniz özel bağlayıcıların sınırları aşağıda verilmiştir.

| Name | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| Özel bağlayıcı sayısı | Azure aboneliği başına 1000 | Azure aboneliği başına 1000 ||
| Özel bağlayıcı için dakika başına istek sayısı | her bağlantı için dakikada 500 istek | *özel bağlayıcı* başına dakika başına 2.000 istek ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Yönetilen kimlikler

| Name | Sınır |
|------|-------|
| Mantıksal uygulama başına yönetilen kimlikler | Sistem tarafından atanan kimlik ya da 1 Kullanıcı tarafından atanan kimlik |
| Bölge başına bir Azure aboneliğinde yönetilen kimliğe sahip mantıksal uygulama sayısı | 1.000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Tümleştirme hesabı limitleri

Her Azure aboneliği şu tümleştirme hesabı sınırlarına sahiptir:

* Azure bölgesi başına bir [ücretsiz katman](../logic-apps/logic-apps-pricing.md#integration-accounts) tümleştirme hesabı. Bu katman yalnızca Azure 'daki genel bölgeler için kullanılabilir (örneğin, Batı ABD veya Güneydoğu Asya), ancak [Azure Çin 21Vianet](/azure/china/overview-operations) veya [Azure Kamu](../azure-government/documentation-government-welcome.md)için değil.

* [Geliştirici ve Premium SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)'larda her türlü [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tümleştirme hesapları dahil olmak üzere 1.000 toplam tümleştirme hesabı.

* [Geliştirici veya Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)'un her BIR Ise, ek ücret ödemeden tek bir tümleştirme hesabı kullanabilir, ancak dahil edilen hesap türü ıSE SKU 'suna göre değişir. [Ek bir maliyet](logic-apps-pricing.md#fixed-pricing)için, çalışma alanı için toplam sınıra kadar daha fazla tümleştirme hesabı oluşturabilirsiniz:

  | ıSE SKU 'SU | Tümleştirme hesabı limitleri |
  |---------|----------------------------|
  | **Premium** | ek ücret ödemeden bir standart hesap dahil olmak üzere 20 toplam hesap. Bu SKU ile yalnızca [Standart](../logic-apps/logic-apps-pricing.md#integration-accounts) hesaplara sahip olabilirsiniz. Ücretsiz veya temel hesaba izin verilmez. |
  | **Geliştirici** | [ücretsiz](../logic-apps/logic-apps-pricing.md#integration-accounts) bir hesap (1 ile sınırlı) dahil olmak üzere 20 toplam hesap. Bu SKU ile herhangi bir bileşim olabilir: <p>-Ücretsiz bir hesap ve en fazla 19 [Standart](../logic-apps/logic-apps-pricing.md#integration-accounts) hesap. <br>-Ücretsiz hesap ve en fazla 20 Standart hesap. <p>Temel veya ek ücretsiz hesaba izin verilmez. <p><p>**Önemli**: deneme, geliştirme ve test için [Geliştirici SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) kullanın, ancak üretim veya performans testi için kullanmayın. |
  |||

Fiyatlandırma ve faturalandırma işinin nasıl sesleri olduğunu öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Tümleştirme hesabı başına yapıt sınırları

Her bir tümleştirme hesabı katmanının yapıt sayısına ilişkin sınırlar aşağıda verilmiştir. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/). Fiyatlandırma ve faturalandırma çalışma hesapları için nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#integration-accounts)bakın.

> [!NOTE]
> Ücretsiz katmanı yalnızca araştırmacı senaryolar için kullanın, üretim senaryolarından değil. Bu katman, aktarım hızını ve kullanımı kısıtlar ve hizmet düzeyi anlaşması (SLA) içermez.

| Yapıt | Ücretsiz | Temel | Standart |
|----------|------|-------|----------|
| EDI ticari sözleşmeleri | 10 | 1 | 1.000 |
| EDI ticari iş ortakları | 25 | 2 | 1.000 |
| Haritalar | 25 | 500 | 1.000 |
| Şemalar | 25 | 500 | 1.000 |
| Bütünleştirilmiş Kodlar | 10 | 25 | 1.000 |
| Sertifikalar | 25 | 2 | 1.000 |
| Batch yapılandırması | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Yapıt kapasitesi sınırları

| Yapıt | Sınır | Notlar |
| -------- | ----- | ----- |
| Bütünleştirilmiş Kod | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için bir [Azure depolama hesabı ve BLOB kapsayıcısı](../logic-apps/logic-apps-enterprise-integration-schemas.md)kullanın. |
| Map (XSLT dosyası) | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için [Azure Logic Apps REST API haritaları](/rest/api/logic/maps/createorupdate)kullanın. <p><p>**Note**: bir eşlemenin başarıyla işleyebileceğini veri veya kayıt miktarı, Azure Logic Apps ileti boyutunu ve eylem zaman aşımı sınırlarını temel alır. Örneğin, http [ileti boyutu ve zaman aşımı limitlerini](#http-limits)temel alan bir http eylemi kullanıyorsanız, işlem http zaman aşımı sınırı içinde tamamlanırsa, eşleme http ileti boyutu sınırına kadar verileri işleyebilir. |
| Şema | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için bir [Azure depolama hesabı ve BLOB kapsayıcısı](../logic-apps/logic-apps-enterprise-integration-schemas.md)kullanın. |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>İşleme sınırları

| Çalışma zamanı uç noktası | Ücretsiz | Temel | Standart | Notlar |
|------------------|------|-------|----------|-------|
| 5 dakika başına çağrı okuma | 3.000 | 30.000 | 60.000 | Bu sınır, mantıksal uygulama çalıştırma geçmişinden gelen ham giriş ve çıkışları alan çağrılar için geçerlidir. İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| 5 dakika başına çağrı çağır | 3.000 | 30.000 | 45.000 | İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| 5 dakika başına çağrı izleme | 3.000 | 30.000 | 45.000 | İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| Eşzamanlı çağrıları engelleme | ~ 1.000 | ~ 1.000 | ~ 1.000 | Tüm SKU 'Lar için aynı. Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B Protokolü (AS2, x12, EDIOLGU) ileti boyutu

B2B protokolleri için uygulanan ileti boyutu sınırları aşağıda verilmiştir:

| Name | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2-100 MB<br>V1-25 MB | v2-200 MB <br>V1-25 MB | Kod çözme ve kodlama için geçerlidir |
| X12 | 50 MB | 50 MB | Kod çözme ve kodlama için geçerlidir |
| EDIFACT | 50 MB | 50 MB | Kod çözme ve kodlama için geçerlidir |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Mantıksal uygulamaları devre dışı bırakma veya silme

Bir mantıksal uygulamayı devre dışı bıraktığınızda, yeni çalıştırmalar örneği oluşturulur. Tüm devam eden ve bekleyen çalıştırmalar tamamlanana kadar devam eder, bu da tamamlanması zaman alabilir.

Mantıksal uygulamayı sildiğinizde yeni çalıştırma başlatılmaz. Devam eden ve bekleme durumunda olan tüm çalıştırmalar iptal edilir. Binlerce çalıştırma varsa iptal işleminin tamamlanması zaman alabilir.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Güvenlik duvarı yapılandırması: IP adresleri ve hizmet etiketleri

Mantıksal uygulamanızın trafiği belirli IP adresleriyle sınırlayan bir güvenlik duvarı üzerinden iletişim kurması gerektiğinde, bu güvenlik duvarının, mantıksal uygulamanızın bulunduğu Azure bölgesindeki Logic Apps hizmeti veya çalışma zamanı tarafından kullanılan [gelen](#inbound) ve [giden](#outbound) IP *adresleri için erişime* izin verilmesi gerekir. Aynı bölgedeki *Tüm* mantıksal uygulamalar aynı IP adresi aralıklarını kullanır.

Örneğin, Batı ABD bölgesindeki mantıksal uygulamaların, [http tetikleyicisi veya eylemi](../connectors/connectors-native-http.md)gibi yerleşik Tetikleyiciler ve eylemler aracılığıyla gönderme veya alma işlemini desteklemesi için, güvenlik duvarınızın, Batı ABD bölgesinde mevcut olan *Tüm* Logic Apps hizmeti gelen IP adresleri *ve* giden IP adresleri için erişime izin verilmesi gerekir.

Mantıksal uygulamanız ayrıca Office 365 Outlook Bağlayıcısı veya SQL Bağlayıcısı gibi [yönetilen bağlayıcılar](../connectors/managed.md)kullanıyorsa veya [özel bağlayıcılar](/connectors/custom-connectors/)kullanıyorsa, güvenlik duvarının ayrıca mantıksal uygulamanızın Azure bölgesindeki *Tüm* [yönetilen bağlayıcı giden IP adresleri](#outbound) için erişime izin verilmesi gerekir. Ayrıca, [Azure 'da şirket içi veri ağ geçidi kaynağı](logic-apps-gateway-connection.md)aracılığıyla şirket içi kaynaklara erişen özel bağlayıcılar kullanırsanız, ağ geçidi yüklemesini, ilgili *YÖNETILEN bağlayıcılar [giden IP adreslerine](#outbound)* erişime izin verecek şekilde ayarlamanız gerekir.

Ağ geçidinde iletişim ayarlarını ayarlama hakkında daha fazla bilgi için şu konulara bakın:

* [Şirket içi veri ağ geçidi için iletişim ayarlarını yapılandırma](/data-integration/gateway/service-gateway-communication)
* [Şirket içi veri ağ geçidi için ara sunucu ayarlarını yapılandırma](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Güvenlik Duvarı IP yapılandırması konuları

Güvenlik duvarınızı IP adresleriyle ayarlamadan önce, şu hususları gözden geçirin:

* [Güç otomatikleştirme](/power-automate/getting-started)'yi kullanıyorsanız, **http** ve **http + openapı** gibi bazı eylemler doğrudan Azure Logic Apps HIZMETI aracılığıyla gider ve burada listelenen IP adreslerinden gelir. Güç otomatikleştirme tarafından kullanılan IP adresleri hakkında daha fazla bilgi için bkz. [Güç otomatikleştirme Için sınırlamalar ve yapılandırma](/flow/limits-and-config#ip-address-configuration).

* [Azure Çin 21Vianet](/azure/china/)için, [özel bağlayıcılar](../logic-apps/custom-connector-overview.md) ve Azure depolama, SQL Server, Office 365 Outlook gibi [yönetilen BAĞLAYıCıLAR](../connectors/managed.md)için sabit veya ayrılmış IP adresleri kullanılamaz.

* Mantıksal uygulamalarınız bir [tümleştirme hizmeti ortamında (ıSE)](connect-virtual-network-vnet-isolated-environment-overview.md)çalışıyorsa, [Bu bağlantı noktalarını da açtığınızdan](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)emin olun.

* Oluşturmak istediğiniz güvenlik kurallarını basitleştirmenize yardımcı olmak için, her bölge için IP adresi öneklerini belirtmek yerine isteğe bağlı olarak [hizmet etiketleri](../virtual-network/service-tags-overview.md) kullanabilirsiniz. Bu Etiketler Logic Apps hizmetinin kullanılabildiği bölgelerde çalışır:

  * **Logicappsmanagement**: Logic Apps hizmeti IÇIN gelen IP adresi öneklerini temsil eder.

  * **Logicapps**: Logic Apps hizmeti IÇIN giden IP adresi öneklerini temsil eder.

  * **AzureConnectors**: Logic Apps hizmetine gelen Web kancası geri çağırmaları ve Azure depolama veya Azure Event Hubs gibi ilgili hizmetlerine giden çağrıları yapan, yönetilen BAĞLAYıCıLAR için IP adresi öneklerini temsil eder.

* Mantıksal uygulamalarınızın [güvenlik duvarları ve güvenlik duvarı kuralları](../storage/common/storage-network-security.md)kullanan Azure depolama hesaplarına erişme sorunları varsa, [erişimi etkinleştirmek için çeşitli diğer seçenekleriniz](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)vardır.

  Örneğin Logic Apps, güvenlik duvarı kurallarını kullanan ve aynı bölgede bulunan depolama hesaplarına doğrudan erişemez. Ancak, [bölgeniz içindeki yönetilen bağlayıcılar için gıden IP adreslerine](../logic-apps/logic-apps-limits-and-config.md#outbound)izin verirseniz, mantıksal uygulamalarınız Azure Tablo depolama veya Azure kuyruk depolama bağlayıcıları ' nı kullandığınız durumlar dışında farklı bir bölgedeki depolama hesaplarına erişebilir. Tablo depolama veya kuyruk depolama verilerinize erişmek için bunun yerine HTTP tetikleyicisini ve eylemlerini kullanabilirsiniz. Diğer seçenekler için bkz. [güvenlik duvarlarının arkasındaki depolama hesaplarına erişme](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Gelen IP adresleri

Bu bölüm yalnızca Azure Logic Apps hizmeti için gelen IP adreslerini listeler. Azure Kamu kullanıyorsanız bkz. [Azure Kamu-gelen IP adresleri](#azure-government-inbound).

> [!TIP]
> Güvenlik kuralları oluştururken karmaşıklığı azaltmaya yardımcı olmak için, isteğe bağlı olarak her bölge için gelen Logic Apps IP adresi öneklerini belirtmek yerine **logicappsmanagement** [hizmet etiketini](../virtual-network/service-tags-overview.md)kullanabilirsiniz. İsteğe bağlı olarak, her bölge için gelen yönetilen bağlayıcı IP adresi öneklerini belirtmek yerine Logic Apps hizmetine gelen Web kancası geri çağırmaları oluşturan yönetilen bağlayıcılar için **AzureConnectors** hizmeti etiketini kullanabilirsiniz. Bu Etiketler Logic Apps hizmetinin kullanılabildiği bölgelerde çalışır.
>
> Aşağıdaki bağlayıcılar Logic Apps hizmetine gelen Web kancası geri aramalarını yapar:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign demo, Adobe Imza önizleme, Adobe Imzala aşaması, Azure Sentinel, Iş Merkezi, Tavdly, Common Data Service, DocuSign, DocuSign demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP *, Microsoft ekipleri için vardiyalar, ekip çalışma projeleri, Typeform
>
> \***SAP**: Return çağıran, dağıtım ortamının çok kiracılı Azure ya da Ise olmasına bağlıdır. Çok kiracılı ortamda, şirket içi veri ağ geçidi Logic Apps hizmetine geri çağrı yapar. Bir ıSE 'de SAP Bağlayıcısı, Logic Apps hizmetine geri çağrı yapar.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Çok kiracılı Azure-gelen IP adresleri

| Çok kiracılı bölge | IP |
|---------------------|----|
| Doğu Avustralya | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Güneydoğu Avustralya | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Güney Brezilya | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brezilya Güneydoğu | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Orta Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Doğu Kanada | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Orta Hindistan | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Doğu Asya | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| Doğu ABD | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Doğu ABD 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Orta Fransa | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Güney Fransa | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Almanya Kuzey | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Almanya Orta Batı | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Doğu Japonya | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Batı Japonya | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Güney Kore - Orta | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Güney Kore - Güney | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Orta Kuzey ABD | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Kuzey Avrupa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Norveç Doğu | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Güney Afrika - Kuzey | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Güney Afrika - Batı | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Orta Güney ABD | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Güney Hindistan | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Güneydoğu Asya | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| İsviçre Kuzey | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| İsviçre Batı | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| BAE Orta | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| BAE Kuzey | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Güney Birleşik Krallık | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Batı Birleşik Krallık | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Orta Batı ABD | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| West Europe | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Batı Hindistan | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Batı ABD | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Batı ABD 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Kamu-gelen IP adresleri

| Azure Kamu bölgesi | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| Orta US DoD | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Giden IP adresleri

Bu bölümde Azure Logic Apps hizmeti ve yönetilen bağlayıcılar için giden IP adresleri listelenir. Azure Kamu kullanıyorsanız bkz. [Azure Kamu-gıden IP adresleri](#azure-government-outbound).

> [!TIP]
> Güvenlik kuralları oluştururken karmaşıklığı azaltmaya yardımcı olmak için, isteğe bağlı olarak her bölge için giden Logic Apps IP adresi öneklerini belirtmek yerine **logicapps** [hizmet etiketini](../virtual-network/service-tags-overview.md)kullanabilirsiniz. İsteğe bağlı olarak, her bölge için giden yönetilen bağlayıcı IP adresi öneklerini belirtmek yerine, Azure depolama veya Azure Event Hubs gibi ilgili hizmetlere giden çağrılar yapan yönetilen bağlayıcılar için **AzureConnectors** hizmet etiketini kullanabilirsiniz. Bu Etiketler Logic Apps hizmetinin kullanılabildiği bölgelerde çalışır.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Çok kiracılı Azure-giden IP adresleri

| Çok kiracılı bölge | Logic Apps IP | Yönetilen bağlayıcılar IP 'si |
|---------------------|---------------|-----------------------|
| Doğu Avustralya | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Güneydoğu Avustralya | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Güney Brezilya | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Brezilya Güneydoğu | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Orta Kanada | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Doğu Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Orta Hindistan | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Doğu Asya | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| Doğu ABD | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| Doğu ABD 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Orta Fransa | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Güney Fransa | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Almanya Kuzey | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Almanya Orta Batı | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Doğu Japonya | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Batı Japonya | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Güney Kore - Orta | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Güney Kore - Güney | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| Orta Kuzey ABD | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Kuzey Avrupa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Norveç Doğu | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Güney Afrika - Kuzey | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Güney Afrika - Batı | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| Orta Güney ABD | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Güney Hindistan | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Güneydoğu Asya | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| İsviçre Kuzey | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| İsviçre Batı | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| BAE Orta | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| BAE Kuzey | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Güney Birleşik Krallık | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Batı Birleşik Krallık | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| Orta Batı ABD | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| West Europe | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Batı Hindistan | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| Batı ABD | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| Batı ABD 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Kamu-giden IP adresleri

| Region | Logic Apps IP | Yönetilen bağlayıcılar IP 'si |
|--------|---------------|-----------------------|
| Orta US DoD | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| US Gov Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Sonraki adımlar

* [İlk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md) öğrenin
* [Yaygın örnekler ve senaryolar](../logic-apps/logic-apps-examples-and-scenarios.md) hakkında bilgi edinin
