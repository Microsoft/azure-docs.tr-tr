---
title: Azure blok zinciri hizmetini izleme (ABS)
description: Azure Izleyici aracılığıyla Azure blok zinciri hizmetini izleme
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594924"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure Izleyici aracılığıyla Azure blok zinciri hizmetini izleme  

Müşteriler, Azure blok zinciri hizmeti 'nde (ABS) üretim sınıfı blok zinciri senaryolarını çalıştırırken, kaynakların kullanılabilirlik, performans ve işlemler için izlenmesi kritik hale gelir. Bu makalede, Azure blok zinciri hizmeti tarafından oluşturulan izleme verileri ve bir hizmetin, üretim sınıfı ortamlarını yönetmek üzere analiz ve uyarı vermek üzere Azure Izleyici 'nin çeşitli özelliklerini ve tümleştirmelerini nasıl kullanabileceği açıklanmaktadır.  

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?

Azure blok zinciri hizmeti, Azure 'da tam bir yığın izleme hizmeti olan Azure Izleyici 'yi kullanarak izleme verileri oluşturur. Bu, Azure kaynaklarınızı izlemeye yönelik eksiksiz bir özellik kümesi sağlar. Azure Izleyici hakkında daha fazla bilgi için bkz. Azure [izleyici Ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md).
 

Aşağıdaki bölümler, Azure blok zinciri hizmetinden toplanan belirli verileri açıklayarak ve veri toplamayı yapılandırmaya ve bu verileri Azure araçları ile çözümlemeye yönelik örnekler sunarak bu makaleyi oluşturur.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Azure blok zinciri hizmeti 'nden toplanan verileri izleme  

Azure blok zinciri hizmeti, Azure kaynaklarından gelen [verileri izleme](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar. Azure blok zinciri hizmeti tarafından oluşturulan günlüklere ve ölçümlere ilişkin ayrıntılı bir başvuru için bkz. [Azure blok zinciri hizmeti veri başvurusunu izleme](#monitor-azure-blockchain-service-data-reference) .

Her Azure blok zinciri hizmeti üye kaynağı için Azure portal genel bakış sayfası, işlenen istekler ve işlenen bloklar dahil olmak üzere işlemlerin kısa bir görünümünü içerir. Bu verilerden bazıları otomatik olarak toplanır ve Azure blok zinciri hizmeti üye kaynağını oluşturduktan sonra ek yapılandırma ile ek veri toplamayı etkinleştirebilmeniz durumunda analiz için kullanılabilir.

## <a name="diagnostic-settings"></a>Tanılama ayarları  

Platform ölçümleri ve etkinlik günlüğü otomatik olarak toplanır, ancak kaynak günlüklerini toplamak veya Azure Izleyici dışında iletmek için bir tanılama ayarı oluşturmanız gerekir. Azure portal, CLı veya PowerShell kullanarak bir tanılama ayarı oluşturmaya yönelik ayrıntılı süreç için [Azure 'da platform günlüklerini ve ölçümlerini toplamak üzere tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md) konusuna bakın.

Bir tanılama ayarı oluşturduğunuzda hangi günlük kategorilerinin toplanacağını belirlersiniz. Azure blok zinciri hizmeti kategorileri aşağıda listelenmiştir.

**Blok zinciri proxy günlükleri** – NGNX proxy günlüklerini izlemek istiyorsanız kategoriyi seçin. Tüm müşteri işlemi ayrıntıları denetim ve hata ayıklama amacıyla kullanılabilir.  

**Blok zinciri uygulama günlükleri** – yönetilen hizmet tarafından barındırılan blok zinciri uygulamasının günlüklerini almak için kategoriyi seçin. Örneğin, bir ABS-Quorum üyesi için, bu Günlükler çekirdekte Günlükler olur.  

**Ölçüm istekleri**: Azure ölçümlerinde otomatik olarak toplanan tanılama ayarındaki hedeflere Azure Cosmos DB ölçüm verileri toplama seçeneğini belirleyin. Her iki veri türünü birlikte analiz etmek ve ölçüm verilerini Azure Izleyici dışında göndermek için kaynak günlükleriyle ölçüm verileri toplayın.

## <a name="analyze-metric-data"></a>Ölçüm verilerini çözümleme  

Azure blok zinciri hizmeti için ölçümleri Ölçüm Gezgini ile analiz edebilir, ABS kaynak dikey penceresinde Izleme bölümü altında ölçümler sekmesine gidebilirsiniz. Aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md) kullanmaya başlama. Azure blok zinciri hizmeti 'nin tüm ölçümleri, Azure blok zinciri hizmeti standart ölçümleri ad alanıdır.

Bir filtre eklerken veya ölçümleri bölerek **düğüm** boyutunu kullanarak, temel olarak işlem düğümleri başına ölçüm DEĞERLERI ve ABS üyesinin Doğrulayıcı düğümlerini sağlar.

## <a name="analyze-log-data"></a>Günlük verilerini analiz etme

Azure blok zinciri hizmeti üyelerinizi izlemenize yardımcı olması için günlük araması çubuğuna girebileceğiniz bazı sorgular aşağıda verilmiştir. Bu sorgular [Yeni dille](../../azure-monitor/logs/log-query-overview.md)çalışır.

Blok zinciri uygulama günlüklerinde hata koşullarını sorgulamak için aşağıdaki sorguyu kullanın:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Blok zinciri ara sunucu günlüklerindeki hata koşullarını sorgulamak için aşağıdaki sorguyu kullanın  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Sorguyu belirli bir zaman aralığı için filtrelemek üzere Azure günlüklerinde bulunan zaman filtrelerini kullanabilirsiniz.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Azure blok zinciri hizmeti veri başvurusunu izleme  

Bu makalede, Azure blok zinciri hizmeti 'nin performansını ve kullanılabilirliğini çözümlemek üzere toplanan günlük ve ölçüm verilerine yönelik bir başvuru sağlanmaktadır.  

### <a name="resource-logs"></a>Kaynak günlükleri

Tüm kaynak günlükleri, blok zinciri hizmetine özgü birkaç benzersiz özellik ile en üst düzey ortak bir şemayı paylaşır. [En üst düzey kaynak günlükleri şeması](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)makalesine başvurabilirsiniz, Azure blok zinciri hizmetine özgü özelliklerin ayrıntılarına aşağıda kapsanacak  

Aşağıdaki tabloda, Azure Izleyici günlüklerinde veya Azure Storage 'da toplandıklarında Azure blok zinciri ara sunucu günlüklerinin özellikleri listelenmektedir.  


| Özellik adı  | Description |
|:---|:---|
| time | İşlem gerçekleştiği tarih ve saat (UTC). |
| RESOURCEID  | Günlüklerin etkinleştirildiği Azure blok zinciri hizmeti kaynağı.  |
| category  |Azure blok zinciri hizmeti için, olası değerler **proxylogs** ve **applicationlogs**' lardır. |
| operationName  | Bu olayla temsil edilen işlemin adı.   |
| Günlük düzeyi  | Varsayılan olarak, Azure blok zinciri hizmeti **bilgilendirme** günlüğü düzeyine izin vermez.   |
| NodeLocation  | Blok zinciri üyesinin dağıtıldığı Azure bölgesi.  |
| BlockchainNodeName  | İşlemin gerçekleştirileceği Azure blok zinciri hizmeti üyesinin düğümünün adı.   |
| Etmi yöntemi  | Temel alınan blok zinciri protokolü tarafından çağrılan yöntem, çekirdekte eth_sendTransactions, eth_getBlockByNumber vb. olabilir.  |
| Aracı  | Web tarayıcısı Mozilla, Edge vb. gibi bir kullanıcı adına davranan Kullanıcı Aracısı. Değer örnekleri şunlardır: "Mozilla/5.0 (Linux x64) node.js/8.16.0 V8/6.2.414.77"  |
| Kod   | HTTP hata kodları. Genellikle 4XX ve 5XX hata koşullarıdır.  |
| NodeHost  | Düğümün DNS adı.   |
| RequestMethodName | HTTP yöntemi çağrıldı, buradaki olası değerler üye Oluştur, var olan üyenin ayrıntılarını alma, silme üyesi için SILME, üyeyi güncelleştirmek için Düzeltme Eki.   |
| BlockchainMemberName  | Kullanıcı tarafından sunulan Azure blok zinciri hizmeti üye adı.  |
| Konsorsiyum | Kullanıcı tarafından sağlandığı şekilde konsorsiyumun adı.   |
| Uzak  | İsteğin geldiği istemcinin IP 'si.  |
| RequestSize  | İsteğin bayt cinsinden boyutu.  |
| RequestTime  | İstek süresi (milisaniye).|




Aşağıdaki tabloda, Azure blok zinciri uygulama günlüklerinin özellikleri listelenmektedir.


| Özellik adı  | Description |
|:---|:---|
| time | İşlem gerçekleştiği tarih ve saat (UTC). |
| RESOURCEID  | Günlüklerin etkinleştirildiği Azure blok zinciri hizmeti kaynağı.|
| category  |Azure blok zinciri hizmeti için, mümkün olan değer **proxylogs** ve **applicationlogs**' lardır.  |
| operationName  | Bu olayla temsil edilen işlemin adı.   |
| Günlük düzeyi  | Varsayılan olarak, Azure blok zinciri hizmeti **bilgilendirme** günlüğü düzeyine izin vermez.   |
| NodeLocation  | Blok zinciri üyesinin dağıtıldığı Azure bölgesi.  |
| BlockchainNodeName  | İşlemin gerçekleştirileceği Azure blok zinciri hizmeti üyesinin düğümünün adı.   |
| BlockchainMessage    | Bu alan, veri düz günlükleri olan blok zinciri uygulama günlüğünü içerecektir. ABS-Quorum için, bu çekirdek günlüklerine sahip olur. Bu, bilgi, hata, uyarı ve yürütülen eylem hakkında daha fazla bilgi veren bir dize olan günlük girdisi türü hakkında bilgi içerir.   |
| Değerine    | Azure blok zinciri hizmeti 'nin bölgeye özgü kiracısı. Bu alanın biçimi https://westlake-rp-prod . <region> . cloudapp.azure.com WHERE bölgesi, dağıtılan üyenin Azure bölgesini belirtir.       |
| SourceSystem   | Bu durumda **Azure** olan Günlükler, sistem tarafından doldurulur.    |



### <a name="metrics"></a>Ölçümler

Aşağıdaki tablolarda, Azure blok zinciri hizmeti için toplanan platform ölçümleri listelenmektedir. Tüm ölçümler, **Azure blok zinciri hizmeti** standart ölçümleri ad alanında depolanır.

Azure Izleyici tarafından desteklenen tüm ölçümlerin (Azure blok zinciri hizmeti dahil) listesi için bkz. [Azure izleyici desteklenen ölçümler](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Blok zinciri ölçümleri

Aşağıdaki tablo, Azure blok zinciri hizmeti üye kaynağı için toplanan blok zinciri ölçümlerinin listesini belirtir.


| Ölçüm adı | Birim  |  Toplama türü| Description   |
|---|---|---|---|
| Bekleyen Işlemler   | Count  |  Ortalama | Mined bekleniyor işlem sayısı.   |
| İşlenen bloklar   | Count  | Sum  |  Her zaman aralığında işlenen blokların sayısı. Şu anda blok boyutu 5 saniyedir, bu nedenle her düğüm 12 blok ve 60 blok 5 dakika içinde işlem görür.   |
|İşlenen Işlemler    | Count  | Sum  | Bir blokta işlenen işlem sayısı.    |
|Kuyruğa alınmış Işlemler    |  Count | Ortalama  | Hemen küçük olmayan işlem sayısı. Bunun nedeni, sıra dışı geldiği ve gelecekte bir önceki işlemin gelmesini beklediği için olabilir. Ya da iki işlem yalnızca bir kez (nonce) ve aynı gaz değeri ile aynı olabilir, bu nedenle ikinci diğeri küçük olamaz.   |

### <a name="connection-metrics"></a>Bağlantı ölçümleri  

Aşağıdaki tabloda, Azure blok zinciri hizmeti üye kaynağı için toplanan farklı bağlantı ölçümleri listelenmektedir. Bunlar NGıNX ara sunucu ölçümleridir.


| Ölçüm adı | Birim  |  Toplama türü| Description |
|---|---|---|---|
| Kabul edilen bağlantılar   | Count  |  Sum | Kabul edilen istemci bağlantılarının toplam sayısı.   |
| Etkin Bağlantılar  | Count  | Ortalama  |  Bekleyen bağlantılar dahil etkin istemci bağlantılarının geçerli sayısı.    |
|İşlenmiş bağlantılar    | Count  | Sum  | İşlenen bağlantıların toplam sayısı. Genellikle, bazı kaynak sınırlarına ulaşılmadığı sürece parametre değeri kabul edilen bağlantılarla aynıdır.     |
|İşlenmiş Istekler     |  Count | Sum  | İstemci isteklerinin toplam sayısı.  |


### <a name="performance-metrics"></a>Performans Ölçümleri

Aşağıdaki tabloda, Azure blok zinciri üye kaynağı düğümlerinin her biri için toplanan performans ölçümleri listelenmektedir.  


| Ölçüm adı | Birim  |  Toplama türü| Description   |
|---|---|---|---|
| CPU kullanım yüzdesi   | Yüzde  |  En yüksek değer | CPU kullanımının yüzdesi.     |
| GÇ okuma baytları   | KB   | Sum  |  Blok zinciri üye kaynağının tüm düğümlerinde GÇ okuma baytlarının toplamı.      |
|GÇ Yazma baytları     | KB   | Sum  | GÇ toplamı blok zinciri üye kaynağının tüm düğümlerinde baytları yazar.     |
|Bellek sınırı       |  Gigab   | Ortalama    | Düğüm başına blok zinciri işlemi için kullanılabilir maksimum bellek. |
|Bellek Kullanımı     | Gigab  |  Ortalama | Tüm düğümlerde ortalama kullanılan bellek miktarı.  |
| Bellek kullanım yüzdesi     | Yüzde   | Ortalama  |  Tüm düğümlerde ortalama kullanılan bellek yüzdesi.       |
|Depolama alanı kullanımı      | Gigab   | Ortalama  | Tüm düğümlerde ortalama kullanılan GB depolama alanı.       |


## <a name="next-steps"></a>Sonraki Adımlar

Blok zinciri verilerini Azure Event Grid yakalamak ve dönüştürmek için [veri Yöneticisi blok zinciri](./data-manager.md) hakkında daha fazla bilgi edinin.
