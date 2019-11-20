---
title: Sınırlar ve Yapılandırma-Azure Logic Apps
description: Süre, verimlilik ve kapasite gibi hizmet limitleri ve izin verilecek IP adresleri gibi yapılandırma değerleri, Azure Logic Apps için
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/19/2019
ms.openlocfilehash: 568da28d6ba84290af7d467da66713a0950f3e51
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158495"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Azure Logic Apps için sınırlar ve yapılandırma bilgileri

Bu makalede, Azure Logic Apps ile otomatik iş akışları oluşturma ve çalıştırmaya yönelik sınırlar ve yapılandırma ayrıntıları açıklanmaktadır. Güç otomatikleştirme için bkz. [Güç otomatikleştirmede sınırlar ve yapılandırma](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Tanım sınırları

Tek bir mantıksal uygulama tanımının sınırları aşağıda verilmiştir:

| Ad | Sınır | Notlar |
| ---- | ----- | ----- |
| İş akışı başına eylem | 500 | Bu sınırı genişletmek için, gerektiği gibi iç içe geçmiş iş akışları ekleyebilirsiniz. |
| Eylemler için izin verilen iç içe geçme derinliği | 8 | Bu sınırı genişletmek için, gerektiği gibi iç içe geçmiş iş akışları ekleyebilirsiniz. |
| Abonelik başına bölge başına iş akışı | 1000 | |
| İş akışı başına Tetikleyiciler | 10 | Tasarımcı değil kod görünümünde çalışırken |
| Kapsam durumlarının sınırlarını Değiştir | 25 | |
| İş akışı başına değişkenler | 250 | |
| İfade başına karakter | 8,192 | |
| `trackedProperties` için en büyük boyut | 16.000 karakter |
| `action` veya `trigger` adı | 80 karakter | |
| `description` uzunluğu | 256 karakter | |
| Maksimum `parameters` | 50 | |
| Maksimum `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Çalıştırma süresi ve bekletme sınırları

Tek bir mantıksal uygulama çalıştırması için sınırlar şunlardır:

| Ad | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| Çalışma süresi | 90 gün | 365 gün | Varsayılan sınırı değiştirmek için bkz. [çalışma süresini değiştirme](#change-duration). |
| Depolama bekletme | çalıştırmanın başlangıç zamanından 90 gün | 365 gün | Varsayılan sınırı değiştirmek için bkz. [depolama bekletmesini değiştirme](#change-retention). |
| En az yinelenme aralığı | 1 saniye | 1 saniye ||
| En fazla yinelenme aralığı | 500 gün | 500 gün ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Çalışma süresini ve depolama bekletmesini değiştirme

Çalışma süresi ve depolama saklama için varsayılan sınırı değiştirmek üzere aşağıdaki adımları izleyin. Maksimum sınırın üzerine gitmeniz gerekiyorsa, gereksinimlerle ilgili yardım için [Logic Apps ekibine başvurun](mailto://logicappsemail@microsoft.com) .

1. Azure portal, mantıksal uygulamanızın menüsünde, **Iş akışı ayarları**' nı seçin.

2. **Çalışma zamanı seçenekleri**altında, **gün cinsinden çalışma geçmişi saklama** listesinden **özel**' i seçin.

3. Kaydırıcıyı istediğiniz gün sayısı için girin veya sürükleyin.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Eşzamanlılık, döngü ve toplu işleme sınırları

Tek bir mantıksal uygulama çalıştırması için sınırlar şunlardır:

| Ad | Sınır | Notlar |
| ---- | ----- | ----- |
| Eşzamanlılık tetikleme | * Eşzamanlılık denetimi kapalıyken sınırsız <p><p>* 25 eşzamanlılık denetimi açık olduğunda varsayılan limit olan, denetimi etkinleştirdikten sonra geri alınamaz. Varsayılan değeri 1 ile 50 (dahil) arasında bir değer olarak değiştirebilirsiniz. | Bu sınır, aynı anda veya paralel olarak çalışabilen en yüksek mantıksal uygulama örneği sayısını açıklar. <p><p>**Note**: eşzamanlılık açık olduğunda, ayırma sınırı, [toplu işleme dizileri](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)için 100 öğeye düşürülür. <p><p>Varsayılan sınırı 1 ile 50 (dahil) arasında bir değere değiştirmek için, bkz. [tetikleyici eşzamanlılık sınırını değiştirme](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) veya [örnekleri sıralı olarak tetikleme](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| En fazla bekleyen çalışma | Eşzamanlılık denetimi açık olduğunda, bekleyen en az çalışma sayısı 10 ' un yanı sıra eşzamanlı çalıştırma sayısıdır (eşzamanlılık tetikleme). En büyük sayıyı, ikisi de dahil olmak üzere 100 olarak değiştirebilirsiniz. | Bu sınır, mantıksal uygulamanız zaten maksimum eşzamanlı örnekleri çalıştırıyorsa çalışmayı bekleyebilecekleri en yüksek mantıksal uygulama örneği sayısını açıklar. <p><p>Varsayılan sınırı değiştirmek için bkz. [değişiklik bekleyen çalışma sınırı](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Foreach dizi öğeleri | 100.000 | Bu sınır, "for each" döngüsünün işleyebilecekleri en yüksek dizi öğesi sayısını tanımlar. <p><p>Daha büyük dizileri filtrelemek için [sorgu eylemini](../connectors/connectors-native-query.md)kullanabilirsiniz. |
| Foreach eşzamanlılık | Eşzamanlılık denetimi kapalıyken 20 varsayılan limit olur. Varsayılan değeri 1 ile 50 (dahil) arasında bir değer olarak değiştirebilirsiniz. | Bu sınır, aynı anda veya paralel olarak çalışabilen en yüksek "for each" döngüsü yinelemesi sayısıdır. <p><p>Varsayılan sınırı 1 ile 50 (dahil) arasında bir değere değiştirmek için, bkz. ["her bir eşzamanlılık sınırı" değiştirme](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) veya ["for each" döngülerini sırayla çalıştırma](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Spton öğeleri | * Eşzamanlılık tetikleyicisi olmadan * 100.000 <p><p>* Eşzamanlılık tetikleme ile 100 | Bir dizi döndüren Tetikleyiciler için, dizi öğelerini bir "foreach" döngüsü kullanmak yerine [birden çok iş akışı örneğine ayıran veya](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) içermeyen bir ' spton ' özelliği kullanan bir ifade belirtebilirsiniz. Bu ifade, her dizi öğesi için bir iş akışı örneği oluşturmak ve çalıştırmak için kullanılacak diziye başvurur. <p><p>**Note**: eşzamanlılık açık olduğunda, spton limiti 100 öğeye düşürülür. |
| Yinelemeden Until | 5\.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Verimlilik sınırları

Tek bir mantıksal uygulama tanımının sınırları aşağıda verilmiştir:

### <a name="multi-tenant-logic-apps-service"></a>Çok kiracılı Logic Apps hizmeti

| Ad | Sınır | Notlar |
| ---- | ----- | ----- |
| Eylem: 5 dakika başına yürütme sayısı | 100.000 varsayılan sınırıdır, ancak 300.000 en yüksek limit olur. | Varsayılan sınırı değiştirmek için, bkz. [mantıksal uygulamanızı Önizlemedeki "yüksek aktarım hızı" modunda çalıştırma](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode). Ya da iş yükünü, gereken şekilde birden fazla Logic App üzerinde dağıtabilirsiniz. |
| Eylem: eşzamanlı giden aramalar | ~2,500 | Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
| Çalışma zamanı uç noktası: eşzamanlı gelen çağrılar | ~1,000 | Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
| Çalışma zamanı uç noktası: 5 dakikada okuma çağrısı sayısı  | 60,000 | İş yükünü, gerektiğinde birden fazla uygulama arasında dağıtabilirsiniz. |
| Çalışma zamanı uç noktası: 5 dakika başına çağrı çağırma | 45,000 | İş yükünü, gerektiğinde birden fazla uygulama arasında dağıtabilirsiniz. |
| 5 dakika başına içerik işleme | 600 MB | İş yükünü, gerektiğinde birden fazla uygulama arasında dağıtabilirsiniz. |
||||

### <a name="integration-service-environment-ise"></a>Tümleştirme hizmeti ortamı (ıSE)

Premium SKU için üretilen iş sınırları aşağıda verilmiştir:

| Ad | Sınır | Notlar |
|------|-------|-------|
| Taban birimi yürütme sınırı | Altyapı kapasitesi %80 ' a ulaştığında sistem kısıtlanıyor | Ayda ~ 4.000 eylem yürütmelerinin sayısını sağlar; Bu, ayda ~ 160.000.000 eylem yürütmeleri | |
| Ölçek birimi yürütme sınırı | Altyapı kapasitesi %80 ' a ulaştığında sistem kısıtlanıyor | Her ölçek birimi, dakikada ~ 80.000.000 daha fazla eylem yürütmeleri olan ~ 2.000 ek eylem yürütmeleri sağlayabilir | |
| Ekleyebileceğiniz maksimum ölçek birimi | 10 | |
||||

Normal işlemede Bu limitlerin üzerine gitmek veya bu limitlerin üzerine gidebilecek yük testi çalıştırmak için, gereksinimlerle ilgili yardım için [Logic Apps ekibine başvurun](mailto://logicappsemail@microsoft.com) .

> [!NOTE]
> Bu SKU 'nun ölçeklendirme için herhangi bir hizmet düzeyi sözleşmesi (SLA) veya özelliği olmadığı için, [Geliştirici SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) yayımlanmış sınırlara sahip değildir. Bu SKU 'YU yalnızca deneme, geliştirme ve test için kullanın, üretim veya performans testi yapın.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Ağ Geçidi sınırları

Azure Logic Apps, ağ geçidi aracılığıyla ekleme ve güncelleştirme dahil olmak üzere yazma işlemlerini destekler. Ancak, bu işlemlerin [Yük boyutuyla ilgili limitleri](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)vardır.

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP sınırları

Tek bir HTTP isteği veya zaman uyumlu bağlayıcı çağrısının sınırları aşağıda verilmiştir:

#### <a name="timeout"></a>zaman aşımı

Bazı bağlayıcı işlemleri, zaman uyumsuz çağrılar yapar veya Web kancası isteklerini dinler, bu nedenle bu işlemler için zaman aşımı Bu limitlerden daha uzun olabilir. Daha fazla bilgi için, belirli bağlayıcının teknik ayrıntılarına ve ayrıca [Iş akışı tetikleyicilerine ve eylemlerine](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)bakın.

| Ad | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| Giden istek | 120 saniye | 240 saniye | Daha uzun süre çalışan işlemler için [zaman uyumsuz bir yoklama deseninin](../logic-apps/logic-apps-create-api-app.md#async-pattern) veya bir [until döngüsü](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)kullanın. |
| Zaman uyumlu yanıt | 120 saniye | 240 saniye | Yanıtı almaya yönelik özgün istek için, başka bir mantıksal uygulamayı iç içe geçmiş iş akışı olarak çağırmadığınız sürece yanıttaki tüm adımların sınırı içinde tamamlaması gerekir. Daha fazla bilgi için bkz. [çağrı, tetikleyici veya iç içe mantıksal uygulamalar](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>İleti boyutu

| Ad | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| İleti boyutu | 100 MB | 200 MB | Bu sınırı geçici olarak çözmek için bkz. [öbek ile büyük Iletileri işleme](../logic-apps/logic-apps-handle-large-messages.md). Ancak, bazı bağlayıcılar ve API 'Ler parçalama veya varsayılan sınırı desteklemeyebilir. |
| Öbek ile ileti boyutu | 1 GB | 5 GB | Bu sınır, yerel olarak parçalama desteği olan veya çalışma zamanı yapılandırmasında parçalama özelliğini etkinleştirmenize izin veren eylemler için geçerlidir. <p>Tümleştirme hizmeti ortamı için Logic Apps altyapısı bu sınırı destekler, ancak bağlayıcılar altyapı sınırına kadar kendi parçalama sınırlarına sahiptir, örneğin, [Azure Blob Storage BAĞLAYıCıSıNıN API başvurusu](https://docs.microsoft.com/connectors/azureblob/). Daha fazla bilgi parçalama için bkz. [öbek ile büyük Iletileri işleme](../logic-apps/logic-apps-handle-large-messages.md). |
| İfade değerlendirme sınırı | 131.072 karakter | 131.072 karakter | `@concat()`, `@base64()`, `@string()` ifadeleri bu sınırdan daha uzun olamaz. |
|||||

#### <a name="retry-policy"></a>Yeniden deneme ilkesi

| Ad | Sınır | Notlar |
| ---- | ----- | ----- |
| Yeniden deneme sayısı | 90 | Varsayılan değer 4 ' dir. Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
| Yeniden deneme en fazla gecikme | 1 gün | Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
| Yeniden deneme en düşük gecikme | 5 saniye | Varsayılanı değiştirmek için [yeniden deneme ilkesi parametresini](../logic-apps/logic-apps-workflow-actions-triggers.md)kullanın. |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Özel bağlayıcı limitleri

Web API 'Lerinden oluşturabileceğiniz özel bağlayıcıların sınırları aşağıda verilmiştir.

| Ad | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| Özel bağlayıcı sayısı | Azure aboneliği başına 1000 | Azure aboneliği başına 1000 ||
| Özel bağlayıcı için dakika başına istek sayısı | her bağlantı için dakikada 500 istek | *özel bağlayıcı* başına dakika başına 2.000 istek ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Yönetilen kimlikler

| Ad | Sınır |
| ---- | ----- |
| Bölge başına bir Azure aboneliğinde sistem tarafından atanan kimliğe sahip mantıksal uygulama sayısı | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Tümleştirme hesabı limitleri

Her Azure aboneliği şu tümleştirme hesabı sınırlarına sahiptir:

* Azure bölgesi başına bir [ücretsiz katman](../logic-apps/logic-apps-pricing.md#integration-accounts) tümleştirme hesabı

* [Geliştirici ve Premium SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)'larda her türlü [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tümleştirme hesapları dahil olmak üzere 1.000 toplam tümleştirme hesabı.

* [Geliştirici veya Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)'un her BIR Ise, 5 Toplam tümleştirme hesabıyla sınırlıdır:

  | ıSE SKU 'SU | Tümleştirme hesabı limitleri |
  |---------|----------------------------|
  | **Premium** | tek bir standart hesap dahil olmak üzere yalnızca 5 Toplam [Standart](../logic-apps/logic-apps-pricing.md#integration-accounts) hesap. Ücretsiz veya temel hesaba izin verilmez. |
  | **Geliştirici** | 5 Toplam- [ücretsiz](../logic-apps/logic-apps-pricing.md#integration-accounts) (1 hesap ile sınırlı) ve [Standart](../logic-apps/logic-apps-pricing.md#integration-accounts) birleştirilmiş veya tüm standart hesaplar. Temel hesaplara izin verilmez. Deneme, geliştirme ve test için [Geliştirici SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) kullanın, ancak üretim veya performans testi için kullanmayın. |
  |||

Ek maliyetler, bir ıSE 'ye dahil olan tümleştirme hesaplarının ötesine eklediğiniz tümleştirme hesapları için geçerlidir. Fiyatlandırma ve faturalandırma işinin nasıl sesleri olduğunu öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Tümleştirme hesabı başına yapıt sınırları

Her bir tümleştirme hesabı katmanının yapıt sayısına ilişkin sınırlar aşağıda verilmiştir. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/). Fiyatlandırma ve faturalandırma çalışma hesapları için nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#integration-accounts)bakın.

> [!NOTE]
> Ücretsiz katmanı yalnızca araştırmacı senaryolar için kullanın, üretim senaryolarından değil. Bu katman, aktarım hızını ve kullanımı kısıtlar ve hizmet düzeyi anlaşması (SLA) içermez.

| Yapıt | Ücretsiz | Temel | Standart |
|----------|------|-------|----------|
| EDI ticari sözleşmeleri | 10 | 1 | 1000 |
| EDI ticari iş ortakları | 25 | 2 | 1000 |
| Haritalar | 25 | 500 | 1000 |
| Şemalar | 25 | 500 | 1000 |
| Derlemeler | 10 | 25 | 1000 |
| Sertifikalar | 25 | 2 | 1000 |
| Batch yapılandırması | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Yapıt kapasitesi sınırları

| Yapıt | Sınır | Notlar |
| -------- | ----- | ----- |
| Derleme | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için bir [Azure depolama hesabı ve BLOB kapsayıcısı](../logic-apps/logic-apps-enterprise-integration-schemas.md)kullanın. |
| Map (XSLT dosyası) | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için [Azure Logic Apps REST API haritaları](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)kullanın. |
| Şema | 8 MB | 2 MB 'tan büyük dosyaları karşıya yüklemek için bir [Azure depolama hesabı ve BLOB kapsayıcısı](../logic-apps/logic-apps-enterprise-integration-schemas.md)kullanın. |
||||

| Çalışma zamanı uç noktası | Sınır | Notlar |
|------------------|-------|-------|
| 5 dakika başına çağrı okuma | 60,000 | İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| 5 dakika başına çağrı çağır | 45,000 | İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| 5 dakika başına çağrı izleme | 45,000 | İş yükünü, gerektiğinde birden fazla hesapta dağıtabilirsiniz. |
| Eşzamanlı çağrıları engelleme | ~1,000 | Eşzamanlı istek sayısını azaltabilir veya süreyi gerektiği gibi azaltabilirsiniz. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B Protokolü (AS2, x12, EDIOLGU) ileti boyutu

B2B protokolleri için uygulanan ileti boyutu sınırları aşağıda verilmiştir:

| Ad | Çok kiracılı sınır | Tümleştirme hizmeti ortam sınırı | Notlar |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2-100 MB<br>V1-50 MB | v2-200 MB <br>V1-50 MB | Kod çözme ve kodlama için geçerlidir |
| X12 | 50 MB | 50 MB | Kod çözme ve kodlama için geçerlidir |
| EDIFACT | 50 MB | 50 MB | Kod çözme ve kodlama için geçerlidir |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Mantıksal uygulamaları devre dışı bırakma veya silme

Bir mantıksal uygulamayı devre dışı bıraktığınızda, yeni çalıştırmalar örneği oluşturulur. Tüm devam eden ve bekleyen çalıştırmalar tamamlanana kadar devam eder, bu da tamamlanması zaman alabilir.

Mantıksal uygulamayı sildiğinizde yeni çalıştırma başlatılmaz. Devam eden ve bekleme durumunda olan tüm çalıştırmalar iptal edilir. Binlerce çalıştırma varsa iptal işleminin tamamlanması zaman alabilir.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Güvenlik duvarı yapılandırması: IP adresleri

Azure Logic Apps gelen ve giden çağrılar için kullandığı IP adresleri, mantıksal uygulamanızın bulunduğu bölgeye göre değişir. Aynı bölgedeki *Tüm* mantıksal uygulamalar aynı IP adresi aralıklarını kullanır.

> [!NOTE]
> **Http** ve **http + openapı** Istekleri gibi bazı güç otomatikleştirme çağrıları, doğrudan Azure Logic Apps HIZMETINE giderek burada listelenen IP adreslerinden gelir. Güç otomatikleştirme tarafından kullanılan IP adresleri hakkında daha fazla bilgi için bkz. [Güç otomatikleştirmede sınırlamalar ve yapılandırma](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

* Mantıksal uygulamalarınızın [http](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)ve diğer http istekleriyle doğrudan yaptığı çağrıları desteklemek için, Logic Apps tarafından kullanılan *Tüm* [gelen](#inbound) *ve* [giden](#outbound) IP adresleriyle güvenlik duvarınızı ayarlayın mantıksal uygulamalarınızın bulunduğu bölgelere göre hizmet. Bu adresler, bu bölümdeki **gelen** ve **giden** başlıkların altında görüntülenir ve bölgeye göre sıralanır.

* [Microsoft tarafından yönetilen bağlayıcıların](../connectors/apis-list.md) yaptığı çağrıları desteklemek için, mantıksal uygulamalarınızın bulunduğu bölgelere göre bu bağlayıcılar tarafından kullanılan *Tüm* [giden](#outbound) IP adresleriyle güvenlik duvarınızı ayarlayın. Bu adresler, bu bölümdeki **giden** başlık altında görüntülenir ve bölgeye göre sıralanır. 

* Bir tümleştirme hizmeti ortamında (ıSE) çalışan Logic Apps iletişimini etkinleştirmek için, [Bu bağlantı noktalarını açtığınızdan](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#ports)emin olun.

* Mantıksal uygulamalarınızın [güvenlik duvarları ve güvenlik duvarı kuralları](../storage/common/storage-network-security.md)kullanan Azure depolama hesaplarına erişme sorunları varsa, [erişimi etkinleştirmek için çeşitli seçenekleriniz](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)vardır.

  Örneğin Logic Apps, güvenlik duvarı kurallarını kullanan ve aynı bölgede bulunan depolama hesaplarına doğrudan erişemez. Ancak, [bölgeniz içindeki yönetilen bağlayıcılar için gıden IP adreslerine](../logic-apps/logic-apps-limits-and-config.md#outbound)izin verirseniz, mantıksal uygulamalarınız Azure Tablo depolama veya Azure kuyruk depolama bağlayıcıları ' nı kullandığınız durumlar dışında farklı bir bölgedeki depolama hesaplarına erişebilir. Tablo depolama veya kuyruk depolama verilerinize erişmek için bunun yerine HTTP tetikleyicisini ve eylemlerini kullanabilirsiniz. Diğer seçenekler için bkz. [güvenlik duvarlarının arkasındaki depolama hesaplarına erişme](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

* Özel Bağlayıcılar, [Azure Kamu](../azure-government/documentation-government-overview.md)ve [Azure Çin 21Vianet](https://docs.microsoft.com/azure/china/)için, sabit veya ayrılmış IP adresleri kullanılamaz.

> [!IMPORTANT]
> 1 Eylül 2018 ' den önce ayarladığınız güvenlik duvarı yapılandırmalarına sahipseniz, mantıksal uygulamalarınızın bulunduğu bölgeler için bu listelerdeki geçerli IP adresleriyle eşleştiğinden emin olun.

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Gelen IP adresleri-yalnızca Logic Apps hizmet

| Bölge | IP |
|--------|----|
| Avustralya Doğu | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Avustralya Güneydoğu | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Güney Brezilya | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Orta Kanada | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Doğu Kanada | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Orta Hindistan | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Orta ABD | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Doğu Asya | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| Doğu ABD | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Doğu ABD 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Fransa Orta | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Fransa Güney | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japonya Doğu | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japonya Batı | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Kore Orta | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Kore Güney | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Orta Kuzey ABD | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Kuzey Avrupa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Orta Güney ABD | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Güney Hindistan | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Güneydoğu Asya | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Batı Orta ABD | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Batı Avrupa | 13.95.155.53, 52.174.54.218, 52.174.49.6, 51.144.176.185 |
| Batı Hindistan | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Batı ABD | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Batı ABD 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Birleşik Krallık Güney | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Birleşik Krallık Batı | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Giden IP adresleri-Logic Apps hizmeti & yönetilen bağlayıcılar

| Bölge | Logic Apps IP | Yönetilen bağlayıcılar IP 'si |
|--------|---------------|-----------------------|
| Avustralya Doğu | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Avustralya Güneydoğu | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Güney Brezilya | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Orta Kanada | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Doğu Kanada | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| Orta Hindistan | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| Orta ABD | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Doğu Asya | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| Doğu ABD | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205 |
| Doğu ABD 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| Fransa Orta | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28,40.89.190.104 | 40.89.135.2, 40.79.130.208 - 40.79.130.223 |
| Fransa Güney | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.133.184, 40.79.178.240 - 40.79.178.255 |
| Japonya Doğu | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Japonya Batı | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| Kore Orta | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.36.214, 52.231.18.208 - 52.231.18.223 |
| Kore Güney | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.163.10, 52.231.147.0 - 52.231.147.15 |
| Orta Kuzey ABD | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Kuzey Avrupa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| Orta Güney ABD | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63 |
| Güney Hindistan | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Güneydoğu Asya | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| Batı Orta ABD | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Batı Avrupa | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Batı Hindistan | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| Batı ABD | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| Batı ABD 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
| Birleşik Krallık Güney | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Birleşik Krallık Batı | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Sonraki adımlar

* [İlk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md) öğrenin  
* [Yaygın örnekler ve senaryolar](../logic-apps/logic-apps-examples-and-scenarios.md) hakkında bilgi edinin
