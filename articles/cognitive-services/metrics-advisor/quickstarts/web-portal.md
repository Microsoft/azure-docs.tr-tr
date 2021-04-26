---
title: 'Hızlı başlangıç: ölçüm Danışmanı Web portalı'
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı Web portalını kullanmaya nasıl başlayacağınızı öğrenin.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531815"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Hızlı başlangıç: Web portalını kullanarak ilk ölçümünüzün Izlenmesi

Bir ölçüm Danışmanı örneği sağladığınızda, hizmetle çalışmak için API 'Leri ve Web tabanlı çalışma alanını kullanabilirsiniz. Web tabanlı çalışma alanı, hizmeti hızlı bir şekilde kullanmaya başlamak için basit bir yol olarak kullanılabilir. Ayrıca, ayarları yapılandırmak, modelinizi özelleştirmek ve kök neden analizini gerçekleştirmek için görsel bir yol sağlar. 

* Ölçüm verilerinizi ekleme
* Ölçümlerinizi ve görselleştirmelerinizi görüntüleme
* Algılama yapılandırmalarının ince ayar yapma
* Tanılama öngörülerini keşfet
* Anomali uyarıları oluşturun ve bunlara abone olun

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure aboneliğiniz olduktan sonra, ölçüm Danışmanı <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" "  target="_blank"> </a> örneğinizi dağıtmak için Azure Portal ölçüm Danışmanı kaynağı oluşturun.  

    
> [!TIP]
> * Ölçüm Danışmanı kaynağınızın dağıtılması 10 ila 30 dakika olabilir. Başarılı bir şekilde dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
> * Hizmetle etkileşim kurmak için REST API kullanmak isterseniz, oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Bunları, oluşturulan kaynaktaki  **anahtarlar ve uç noktalar** sekmesinde bulabilirsiniz.

Bu belge, ilk monitörünüzü oluşturmak için örnek olarak bir SQL veritabanı kullanır.

## <a name="sign-in-to-your-workspace"></a>Çalışma alanınızda oturum açın

Kaynağınız oluşturulduktan sonra, [ölçüm Danışmanı portalında](https://go.microsoft.com/fwlink/?linkid=2143774)oturum açın. Ölçümlerinizi izlemeye başlamak için çalışma alanınızı seçin. 
 
Şu anda, kullanılabilir her bölgede bir ölçüm Danışmanı kaynağı oluşturabilirsiniz. Ölçüm Danışmanı portalındaki çalışma alanlarını dilediğiniz zaman değiştirebilirsiniz.


## <a name="onboard-time-series-data"></a>Zaman serisi verilerini ekleme

Ölçüm Danışmanı SQL veritabanı, Azure Veri Gezgini ve Azure Tablo depolaması gibi farklı veri kaynakları için bağlayıcılar sağlar. Veri bağlama adımları farklı bağlayıcılar için benzerdir, ancak bazı yapılandırma parametreleri farklılık gösterebilir. Belirli veri kaynakları için gerekli parametreler için bkz. [farklı kaynaklardaki verilerinizi bağlama](../data-feeds-from-different-sources.md) .

Bu hızlı başlangıç, örnek olarak bir SQL veritabanı kullanır. Ayrıca, kendi verilerinizi de aynı adımları izleyerek izleyebilirsiniz.

Başlamak için, ölçüm Danışmanı çalışma alanınızda Active Directory hesabınızla oturum açın. Giriş sayfasından **Dizin**, **abonelik** ve yeni oluşturduğunuz **çalışma alanınızı** seçip **Başlarken**' e tıklayın. İş yükünün ana sayfası yüklendikten sonra, sol menüden **veri akışı Ekle** ' yi seçin.

### <a name="data-schema-requirements-and-configuration"></a>Veri şeması gereksinimleri ve yapılandırması

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Bağlantı ayarlarını yapılandırma

> [!TIP]
> Kullanılabilir parametreler hakkında daha fazla bilgi için bkz. [veri akışları ekleme](../how-tos/onboard-your-data.md) .

Veri akışını, zaman serisi veri kaynağınıza bağlanarak ekleyin. Aşağıdaki parametreleri seçerek başlayın:

* **Kaynak türü**: zaman serisi verilerinizin depolandığı veri kaynağının türü.
* **Ayrıntı düzeyi**: zaman serisi verilerinizde ardışık veri noktaları arasındaki Aralık (örneğin yıllık, aylık, günlük). En düşük Aralık özelleştirmesi 60 saniyedir.
* **Veri alma (UTC)**: ilk zaman damgasına alınacak başlangıç saati. 


Sonra, **bağlantı dizesini** veri kaynağınız için kimlik bilgileriyle ve özel bir **sorgu** belirtin. Sorgu, alınacak verileri belirtmek ve gerekli şemaya dönüştürmek için kullanılır.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Bağlantı ayarları" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Bağlantıyı doğrulama ve veri şemasını yükleme

Bağlantı dizesi ve sorgu dizesi oluşturulduktan sonra, bağlantıyı doğrulamak için **Doğrula ve şemayı al** ' ı seçin ve veri kaynağınızdan veri şemasını almak için sorguyu çalıştırın. Normalde veri kaynağı bağlantınıza bağlı olarak birkaç saniye sürer. Bu adımda bir hata oluşursa şunları onaylayın:

1. Bağlantı dizeniz ve sorgunuz doğru.
2. Güvenlik Duvarı ayarları varsa, ölçüm Danışmanı örneğiniz veri kaynağına bağlanabilir.

### <a name="schema-configuration"></a>Şema yapılandırması

Veri şeması yüklenip aşağıda gösterildiği gibi, uygun alanları seçin.


|Seçim  |Açıklama  |Notlar  |
|---------|---------|---------|
|**Timestamp**     | Bir veri noktasının zaman damgası. Atlanırsa, ölçüm Danışmanı veri noktasının yerine kullanıldığı zaman damgasını kullanır. Her veri akışı için zaman damgası olarak en fazla bir sütun belirtebilirsiniz.        | İsteğe bağlı. En fazla bir sütun ile belirtilmelidir.       |
|**Measure**     |  Veri akışındaki sayısal değerler. Her veri akışı için birden çok ölçü belirtebilirsiniz, ancak en az bir sütun ölçü olarak seçilmelidir.        | En az bir sütunla belirtilmelidir.        |
|**Boyut**     | Kategorik değerler. Farklı değerlerin bir birleşimi belirli bir tek boyutlu zaman serisini tanımlar, örneğin: ülke, dil, kiracı. Boyut olarak hiçbiri veya rastgele sütun sayısını seçebilirsiniz. Note: dize olmayan bir sütunu boyut olarak seçiyorsanız boyut açılımına karşı dikkatli olun. | İsteğe bağlı.        |
|**Yoksay**     | Seçili sütunu yoksayın.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Şema yapılandırması" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Otomatik toplama ayarları

> [!IMPORTANT]
> **Kök neden analizini** ve diğer tanılama yeteneklerini etkinleştirmek istiyorsanız ' otomatik toplama ayarı 'nın yapılandırılması gerekir. Etkinleştirildikten sonra otomatik toplama ayarları değiştirilemez.

Ölçüm Danışmanı alma sırasında her boyutta toplama (SUM/MAX/MIN...) otomatik olarak gerçekleştirebilir, ardından kök örnek olay çözümlemede ve diğer tanılama özelliklerinde kullanılacak bir hiyerarşi oluşturur. Daha fazla ayrıntı için bkz. [otomatik toplama ayarları](../how-tos/onboard-your-data.md#automatic-roll-up-settings) .

Çalışma alanınızda görüntülenecek olan veri akışı için özel bir ad verin. **Gönder**' e tıklayın. 

## <a name="tune-detection-configuration"></a>Algılama yapılandırmasını ayarla

Veri akışı eklendikten sonra, ölçüm Danışmanı belirtilen başlangıç tarihinden itibaren ölçüm verilerini alma girişiminde bulunur. Verilerin tam olarak eklenmesi biraz zaman alabilir ve veri akışı sayfasının üst kısmında alma **ilerleme** durumu ' na tıklayarak alma durumunu görüntüleyebilirsiniz. Veriler belirtilmişse, ölçüm Danışmanı algılamayı uygular ve kaynağı yeni veriler için izlemeye devam eder.

Algılama uygulandığında, **ölçüm ayrıntısı sayfasını** bulmak için veri akışında listelenen ölçülerden birine tıklayın: 
- Bu ölçüm altındaki tüm zaman serisi dilimlerinin görselleştirmelerini görüntüleyin
- Güncelleştirme, beklenen sonuçları karşılayacak şekilde yapılandırmayı algılıyor
- Algılanan bozukluklar için bildirim ayarlayın

:::image type="content" source="../media/metric-details.png" alt-text="Ölçüm ayrıntıları" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Tanılama öngörülerini görüntüleme

Algılama yapılandırmasını ayarladıktan sonra, bulunan bozukluklar verilerinize ait gerçek bozuklukları yansıtmalıdır. Ölçüm Danışmanı anomali kümeleme, olay bağıntısı ve kök neden analizi gibi çok boyutlu ölçümler üzerinde analiz gerçekleştirir. Verilerinizde olayları çözümlemek ve tanılamak için bu özellikleri kullanın.

Tanılama öngörülerini görüntülemek için, algılanan bozuklukları temsil eden zaman serisi görselleştirmelerinde kırmızı noktalara tıklayın. Bir pencere, olay Analizi sayfasına bağlantı ile görüntülenir. 

:::image type="content" source="../media/incident-link.png" alt-text="Olay bağlantısı" lightbox="../media/incident-link.png":::

Bağlantıya tıkladıktan sonra, bir grup tanılama öngörüleri ile ilgili anomali üzerinde analiz olan olay Analizi sayfasına göre özetlendirilecektir. En üstte, olayla ilgili olarak **önem derecesi**, **bozukluklar** ve etkilenen **Başlangıç saati** ile **bitiş saati** gibi istatistikler olacaktır. 

Daha sonra olayın üst anomali durumunu ve otomatik kök nedeni önerisi görürsünüz. Bu otomatik kök neden, ilgili tüm anormalilerin olay ağacı analiz edilirken (sapma, dağıtım ve üst bozukluklar dahil) oluşturulur. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Olay tanılama" lightbox="../media/incident-diagnostic.png":::

Bunlara göre, ne olduğunu ve olayın etkisini ve en olası temel nedeni daha kolay bir şekilde görebilirsiniz. Bu nedenle, olayı en kısa sürede çözümlemek için hemen eyleme alınamaz. 

Ancak, daha fazla tanılama içgörüler arasında daha fazla bilgi elde etmek için ek özelliklerden yararlanarak daha fazla bilgi edinin, benzer anormallikleri görüntüleyebilir ve ölçümler arasında karşılaştırma yapabilirsiniz. Lütfen [nasıl yapılır: bir olayı tanılama hakkında](../how-tos/diagnose-incident.md)daha fazla bilgi edinin. 

## <a name="get-notified-when-new-anomalies-are-found"></a>Yeni anomali bulunduğunda bildirim alın

Verilerinizde bir anomali algılandığında uyarılmak isterseniz, ölçümlerinizin bir veya birkaçı için bir abonelik oluşturabilirsiniz. Ölçüm Danışmanı, uyarı göndermek için kancalar kullanır. Üç tür kanca desteklenir: e-posta kancası, Web kancası ve Azure DevOps. Örnek olarak Web kancası kullanacağız. 

### <a name="create-a-web-hook"></a>Web kancası oluşturma

Web kancası, bir uyarı tetiklendiğinde Kullanıcı tarafından sağlanmış bir API 'yi çağıran ölçüm Danışmanı hizmetinden programlı bir şekilde fark eden bir yol almak için giriş noktasıdır. Kanca **oluşturma hakkında** ayrıntılı bilgi için, bkz. [nasıl yapılır: uyarıları yapılandırma ve kanca kullanarak bildirim alma](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Uyarı ayarlarını yapılandırma

Bir kanca oluşturduktan sonra bir uyarı ayarı nasıl ve hangi uyarı bildirimlerinin gönderileceğini belirler. Her ölçüm için birden çok uyarı ayarı ayarlayabilirsiniz. için iki önemli ayar, dahil edilecek anormallikleri belirten **uyarılardır** ve uyarıya hangi **anomali** değerler ekleneceğini tanımlar. Daha fazla ayrıntı için bkz. [nasıl yapılır: uyarıları yapılandırma ve kanca kullanarak bildirim alma konusundaki](../how-tos/alerts.md#add-or-edit-alert-settings) **uyarı ayarlarını ekleme veya düzenleme**


## <a name="next-steps"></a>Sonraki adımlar

- [Veri akışlarınızı ekleme](../how-tos/onboard-your-data.md)
    - [Veri akışlarını yönetme](../how-tos/manage-data-feeds.md)
    - [Farklı veri kaynakları için yapılandırma](../data-feeds-from-different-sources.md)
- [REST API veya Istemci kitaplıklarını kullanma](./rest-api-and-client-library.md)
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](../how-tos/configure-metrics.md)
