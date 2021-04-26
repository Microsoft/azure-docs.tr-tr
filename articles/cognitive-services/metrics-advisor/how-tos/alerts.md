---
title: Ölçüm Danışmanı uyarılarını yapılandırma
titleSuffix: Azure Cognitive Services
description: E-posta, Web ve Azure DevOps için kancalar kullanarak ölçüm Danışmanı uyarılarınızı yapılandırma.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420929"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Nasıl yapılır: bir kanca kullanarak uyarıları yapılandırma ve bildirim alma

Ölçüm Danışmanı tarafından bir anomali algılandığında, bir kanca kullanılarak uyarı ayarlarına bağlı olarak bir uyarı bildirimi tetiklenir. Uyarı ayarı birden çok algılama yapılandırmasıyla birlikte kullanılabilir, uyarı kuralınızı özelleştirmek için çeşitli parametreler mevcuttur.

## <a name="create-a-hook"></a>Kanca oluşturma

Ölçüm Danışmanı üç farklı tür kancaları destekler: e-posta kancası, Web kancası ve Azure DevOps. Belirli senaryonuz için çalışacak olanı seçebilirsiniz.       

### <a name="email-hook"></a>E-posta kancası

> [!Note]
> Ölçüm Danışmanı kaynak yöneticileri, e-posta ayarlarını yapılandırmak ve anomali uyarıların gönderilebilmesi için, SMTP ile ilgili bilgileri ölçüm Danışmanı 'nda girmeleri gerekir. Kaynak grubu yöneticisinin veya abonelik yöneticisinin, ölçüm Danışmanı kaynağının erişim denetimi sekmesinde en az bir bilişsel *Hizmetler ölçüm Danışmanı yönetici* rolü ataması gerekir. [E-posta ayarları yapılandırması hakkında daha fazla bilgi edinin](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

E-posta kancası oluşturmak için aşağıdaki parametreler kullanılabilir: 

E-posta kancası, anomali uyarıların **e-posta** adresi bölümünde belirtilen e-posta adreslerine gönderilmesini sağlar. İki tür uyarı e-postası gönderilir: *veri akışı kullanılabilir* uyarılar ve bir veya birden çok Anobirini içeren *Olay raporları* . 

|Parametre |Açıklama  |
|---------|---------|
| Ad | E-posta kancası adı |
| E-posta| Uyarı gönderecek e-posta adresleri|
| Dış bağlantı | Sorun giderme notları gibi özelleştirilmiş bir yeniden yönlendirmeyi sağlayan isteğe bağlı alan. |
| Özelleştirilmiş anomali uyarı başlığı | Başlık şablonu,,,,,,,, `${severity}` `${alertSettingName}` `${datafeedName}` `${metricName}` `${detectConfigName}` `${timestamp}` `${topDimension}` `${incidentCount}``${anomalyCount}`

**Tamam**' a tıkladıktan sonra bir e-posta kancası oluşturulur. Bunu, anomali uyarılar almak için herhangi bir uyarı ayarlarında kullanabilirsiniz. 

### <a name="web-hook"></a>Web kancası

> [!Note]
> * **Post** isteği yöntemini kullanın.
> * İstek gövdesi WIL şuna benzer:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Web kancası oluşturulduğunda veya değiştirildiğinde, API boş bir istek gövdesi olan bir test olarak çağırılır. API 'nizin bir 200 HTTP Kodu döndürmesi gerekir.

Web kancası, ölçüm Danışmanı hizmetinden kullanılabilen tüm bilgilerin giriş noktasıdır ve bir uyarı tetiklendiğinde Kullanıcı tarafından sağlanan bir API çağırır. Tüm uyarılar bir Web Kancası aracılığıyla gönderilebilir.

Bir Web kancası oluşturmak için aşağıdaki bilgileri eklemeniz gerekir:

|Parametre |Açıklama  |
|---------|---------|
|Uç Nokta     | Bir uyarı tetiklendiğinde çağrılacak API adresi.        |
|Kullanıcı adı/parola | API adresinde kimlik doğrulaması için. Kimlik doğrulaması gerekmiyorsa bu siyahı bırakın.         |
|Üst bilgi     | API çağrısındaki özel üstbilgiler.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Web kancası oluşturma penceresi.":::

Bir Web Kancası aracılığıyla bir bildirim gönderildiğinde, uyarının ayrıntılarını almak için aşağıdaki API 'Leri kullanabilirsiniz. API hizmetinize itilmiş *zaman damgası* ve *Alertsettingguıd* ' i ayarlayın, sonra aşağıdaki sorguları kullanın: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Ölçüm Danışmanı, her türlü anomali algılandığında sorunları/hataları izlemek üzere Azure DevOps 'da otomatik olarak bir iş öğesi oluşturmayı da destekler. Tüm uyarılar, Azure DevOps kancaları aracılığıyla gönderilebilir.

Bir Azure DevOps kancası oluşturmak için aşağıdaki bilgileri eklemeniz gerekir

|Parametre |Açıklama  |
|---------|---------|
| Ad | Kanca için bir ad |
| Kuruluş | DevOps 'nizin ait olduğu kuruluş |
| Project | DevOps içindeki belirli proje. |
| Erişim Belirteci |  DevOps kimlik doğrulaması için bir belirteç. | 

> [!Note]
> Ölçüm Danışmanı 'Nın anomali uyarılarını temel alan iş öğeleri oluşturmasını istiyorsanız, yazma izinleri vermeniz gerekir. Kancaları oluşturduktan sonra bunları uyarı ayarlarınızda kullanabilirsiniz. Kanca **ayarları** sayfasında Kancalarınızı yönetin.

## <a name="add-or-edit-alert-settings"></a>Uyarı ayarlarını Ekle veya Düzenle

Ölçüm Ayrıntıları sayfasının sol alt köşesindeki **Uyarı ayarları** bölümünü bulmak için ölçüm ayrıntısı sayfasına gidin. Seçili algılama yapılandırması için uygulanan tüm uyarı ayarlarını listeler. Yeni bir algılama yapılandırması oluşturulduğunda, hiçbir uyarı ayarı yoktur ve hiçbir uyarı gönderilmez.  
Uyarı ayarlarını değiştirmek için **Ekle**, **Düzenle** ve **Sil** simgelerini kullanabilirsiniz.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Uyarı ayarları menü öğesi.":::

Uyarı ayarlarınızı eklemek veya düzenlemek üzere bir pencere almak için **Ekle** veya **Düzenle** düğmelerine tıklayın.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Uyarı ayarlarını Ekle veya Düzenle":::

**Uyarı ayarı adı**: Bu uyarı ayarının adı. Uyarı e-postası başlığında görüntülenecektir.

**Kancalar**: uyarıların gönderileceği kancalar listesi.

Yukarıdaki ekran görüntüsünde işaretlenen bölüm, bir algılama yapılandırmasının ayarlarından biridir. Farklı algılama yapılandırmalarının farklı uyarı ayarlarını yapabilirsiniz. Bu penceredeki üçüncü açılan listeyi kullanarak hedef yapılandırmayı seçin. 

### <a name="filter-settings"></a>Filtre ayarları 

Bir algılama yapılandırması için filtre ayarları aşağıda verilmiştir.

**Için uyarı** , anormallikleri filtrelemek için 4 seçeneğe sahiptir:

* **Tüm serilerde bulunan bozukluklar**: uyarıya tüm bozukluklar dahil edilecek.         
* **Seri grubundaki bozukluklar**: diziyi boyut değerlerine göre filtreleyin. Bazı boyutlarda belirli değerleri ayarlayın. Bozukluklar yalnızca seri belirtilen değerle eşleştiğinde uyarıya dahil edilir.       
* **Sık kullanılan serideki bozukluklar**: uyarıya yalnızca sık kullanılan olarak işaretlenen seriler dahil edilir.        |
* **Tüm serilerin en üstte bulunan bozukluklar**: Bu filtre, yalnızca değeri en üstteki n olan seriler hakkında dikkat etmeniz durumunda olur. Bazı zaman damgalarını geri bakacağız ve bu zaman damgasında serinin değerinin ilk N olup olmadığını kontrol edeceğiz. "İlk n" sayısı belirtilen sayıdan daha büyükse, anomali bir uyarıya dahil edilir.        |

**Filtre anomali seçenekleri** aşağıdaki seçeneklere sahip ek bir filtredir:

- **önem derecesi** : anomali, yalnızca anomali önem derecesi belirtilen aralık dahilinde olduğunda dahil edilir.
- **Erteleme** : bir uyarı içinde tetiklendiğinde sonraki N noktasında (Period) bozukluklar için uyarıları geçici olarak durdurun.
    - **erteleme türü** : **seri** olarak ayarlandığında, tetiklenen bir anomali yalnızca serisini ertelecektir. **Ölçüm** için, tetiklenen bir anomali, bu ölçümdeki tüm serileri ertelenecek.
    - **erteleme numarası** : erteleme noktası sayısı (nokta).
    - **ardışık olmayan için Sıfırla** : seçildiğinde, tetiklenen bir anomali yalnızca sonraki n birbirini izleyen anormalileri bekler. Aşağıdaki veri noktalarından biri bir anomali değilse, erteleme o noktadan sıfırlanır; Seçilmediğinde, izleyen veri noktaları anormal olmasa bile, tetiklenen tek bir anomali sonraki n noktayı (nokta) erteler.
- **değer** (isteğe bağlı): değere göre filtreleyin. Yalnızca koşulu karşılayan nokta değerleri, anomali dahil edilir. Kendisine karşılık gelen bir başka ölçüm değeri kullanırsanız, iki ölçüm boyut adları tutarlı olmalıdır.

Filtrelenmemiş olan bozukluklar bir uyarıda gönderilir.

### <a name="add-cross-metric-settings"></a>Çapraz ölçüm ayarları ekle

Başka bir bölüm eklemek için uyarı ayarları sayfasında **+ çapraz ölçüm ayarları ekle** ' ye tıklayın.

**İşleç** Seçicisi, bir uyarı gönderdikleri saptanması için her bölümün mantıksal ilişkidir.


|İşleç  |Açıklama  |
|---------|---------|
|AND     | Yalnızca bir dizi her uyarı bölümüyle eşleşiyorsa bir uyarı gönder ve tüm veri noktaları bozukluklar. Ölçümler farklı boyut adlarına sahip ise, bir uyarı tetiklenmez.         |
|VEYA     | En az bir bölüm bozukluklar içeriyorsa uyarıyı gönderin.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Birden çok uyarı ayarı için işleç bölümü":::

## <a name="next-steps"></a>Sonraki adımlar

- [Geri bildirimi kullanarak anomali algılamayı ayarlama](anomaly-feedback.md)
- [Bir olayı tanılayın](diagnose-incident.md).
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)