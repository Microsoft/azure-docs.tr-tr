---
title: Ortamınızdaki veri bekletmesini anlama-Azure zaman serisi öngörüleri | Microsoft Docs
description: Bu makalede, Azure Time Series Insights ortamınızda verilerin bekletilmesini denetleyen iki ayar açıklanmaktadır.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 26363031aea37c53cce098e2b6cbc2b4d93b918f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307087"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1 'da veri bekletmeyi anlama

> [!CAUTION]
> Bu bir Gen1 makaledir.

Bu makalede, Azure Time Series Insights ortamınızda veri bekletmeyi etkileyen iki birincil ayar açıklanmaktadır.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Aşağıdaki videoda, veri saklama Azure Time Series Insights ve bunun nasıl planlanacağı özetlenmektedir.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Azure Time Series Insights ortamlarınızın her biri, **veri saklama süresini** denetleyen bir ayara sahiptir. Değer 1 ile 400 gün arasında yayılır. Veriler ortam depolama kapasitesi veya Bekletme süresine göre silinir, hangisi önce gelir.

Ayrıca, Azure Time Series Insights ortamınızda bir **depolama sınırı aşıldı davranış** ayarı vardır. Bir ortamın en fazla kapasitesine ulaşıldığında giriş ve temizleme davranışını denetler. Yapılandırma sırasında aralarından seçim yapabileceğiniz iki davranış vardır:

- **Eski verileri temizle** (varsayılan)  
- **Girişi Duraklat**

> [!NOTE]
> Varsayılan olarak, yeni bir ortam oluştururken bekletme **eski verileri temizlemek** üzere yapılandırılır. Bu ayar, Azure Time Series Insights ortamının **yapılandırma** sayfasında Azure Portal kullanılarak oluşturulma zamanından sonra gerektiği şekilde değiştirilebilir.
>
> - Bekletme ilkelerini yapılandırma hakkında daha fazla bilgi için [Azure Time Series Insights ' de bekletme yapılandırması](time-series-insights-how-to-configure-retention.md)' nı okuyun.

Her iki veri saklama ilkesi de aşağıda daha ayrıntılı olarak açıklanmıştır.

## <a name="purge-old-data"></a>Eski verileri temizle

- **Eski verileri temizle** Azure Time Series Insights ortamları için varsayılan ayardır.  
- Kullanıcılar Azure Time Series Insights ortamlarında *en son verileri* her zaman sahip olmak istedikleri zaman, **eski verileri temizle** tercih edilir.
- **Eski verileri temizle** ayarı, ortamın sınırlarına (saklama süresi, boyutu veya sayısı, hangisi önce gelirse) ulaşıldığında verileri *temizler* . Bekletme varsayılan olarak 30 güne ayarlanır.
- En eski veriler önce temizlenir ("ilk Ilk çıkar" yaklaşımı).

### <a name="example-one"></a>Örnek bir

Bekletme davranışına sahip örnek bir ortam düşünün **ve eski verileri temizleyin**:

**Veri saklama süresi** 400 gün olarak ayarlanır. **Kapasite** , 30 GB toplam kapasiteyi içeren S1 birimi olarak ayarlanır. Gelen verileri ortalama günde 500 MB 'ye birikme edelim. Bu ortam, en yüksek kapasiteye 60 gün sonra ulaşıldığı için, gelen verilerin oranı verilen verilerin yalnızca 60 gün cinsinden kalmasını sağlayabilir. Gelen veriler şu şekilde toplanır: 500 MB her gün x 60 gün = 30 GB.

61 günde, ortam en güncel verileri gösterir, ancak 60 günden eski olan en eski verileri temizler. Temizleme işlemi yeni veri akışı için yer açar, böylece yeni veriler araştırılmaya devam edebilir. Kullanıcı verilerin daha uzun süre korumasını istiyorsa, ek birimler ekleyerek veya daha az veri gönderebilmeleri için ortamın boyutunu artırabilir.  

### <a name="example-two"></a>Örnek iki

Bir ortamı aynı zamanda yapılandırılmış saklama davranışını göz önünde bulundurun **ve eski verileri temizleyin**. Bu örnekte, **veri saklama süresi** 180 gün daha düşük bir değere ayarlanır. **Kapasite** , 30 GB toplam kapasiteyi içeren S1 birimi olarak ayarlanır. Günlük giriş, tam 180 güne ait verileri depolamak için günde 0,166 GB (166 MB) değerini aşamaz.  

Bu ortamın günlük giriş oranı günde 0,166 GB 'ı aştığında, bazı veriler temizlendiğinden veriler 180 gün süreyle depolanamaz. Meşgul zaman çerçevesinde aynı ortamı göz önünde bulundurun. Ortamın giriş hızının günde ortalama 0,189 GB 'a artırabileceğiniz varsayılır. Bu meşgul zaman çerçevesinde 158 günlük veriler korunur (30 GB/0.189 = 158,73 gün bekletme). Bu süre, istenen veri saklama süresi çerçevenden daha az.

## <a name="pause-ingress"></a>Girişi Duraklat

- Saklama **süresi ayarı,** boyut ve sayı sınırlarına bekletme dönemlerinden önce ulaşılırsa verilerin temizlenmemesini sağlamak üzere tasarlanmıştır.  
- Giriş **duraklatma** , bekletme döneminin ihlal olmasından dolayı verilerin temizlenmeden önce, kullanıcıların ortam kapasitesini artırmaları için ek zaman sağlar.
- Veri kaybından korunmanıza yardımcı olur ancak giriş, olay kaynağınızın bekletme döneminin ötesinde durakladığında en son verilerinizin kaybedilmesi için bir fırsat oluşturabilir.
- Ancak, bir ortama en fazla kapasiteye ulaşıldığında, aşağıdaki ek eylemler gerçekleşene kadar ortam veri girişini duraklatır:

  - [Azure Time Series Insights ortamınızı ölçeklendirme](time-series-insights-how-to-scale-your-environment.md)bölümünde açıklandığı gibi daha fazla ölçek birimi eklemek için ortamın maksimum kapasitesini artırabilirsiniz.
  - Veri saklama süresine ulaşıldı ve veriler temizlendi ve ortam en yüksek kapasitesinin altına getiriliyor.

### <a name="example-three"></a>Örnek üç

Girişi **duraklatmak** için yapılandırılan bekletme davranışına sahip bir ortam düşünün. Bu örnekte, **veri saklama süresi** 60 gün olarak yapılandırılır. **Kapasite** , üç (3) S1 birimi olarak ayarlanır. Bu ortamın her gün 2 GB veri aldığını varsayın. Bu ortamda, en yüksek kapasiteye ulaşıldığında giriş duraklatılır.

Bu sırada, giriş devam etmeden veya **çalışmaya devam** edene kadar ortam aynı veri kümesini gösterir (yeni veriler için yer açmak üzere eski verileri temizler).

Giriş devam ettiğinde:

- Veri akışları olay kaynağı tarafından alındığı sırada
- Olay kaynağınızda bekletme ilkelerini aşmadığınız takdirde olaylar, zaman damgalarına göre dizinlenir. Olay kaynağı saklama yapılandırması hakkında daha fazla bilgi için [Event HUBS SSS](../event-hubs/event-hubs-faq.yml)

> [!IMPORTANT]
> Giriş duraklamasını önlemeye yardımcı olmak için uyarılar ayarlamanız gerekir. Varsayılan saklama, Azure olay kaynakları için 1 gün olduğundan veri kaybı mümkündür. Bu nedenle, giriş duraklatıldıktan sonra ek eylem gerçekleşmediğiniz takdirde büyük olasılıkla en son verileri kaybedersiniz. Veri kaybını önlemek için kapasiteyi artırmanız veya **eski verileri temizlemek** için davranış geçişi yapmanız gerekir.

Etkilenen Event Hubs, Azure Time Series Insights ' de duraklatma girişi gerçekleştiğinde veri kaybını en aza indirmek için **Ileti bekletme** özelliğini ayarlamayı göz önünde bulundurun.

[![Olay Hub 'ı ileti bekletme.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Olay kaynağında () bir özellik yapılandırılmamışsa `timeStampPropertyName` , Azure Time Series Insights varsayılan olarak, Olay Hub 'ında X ekseni olarak gelen varışı zaman damgasına. , `timeStampPropertyName` Başka bir şey olacak şekilde yapılandırıldıysa, bu ortam, `timeStampPropertyName` Olaylar ayrıştırıldığında veri paketinde yapılandırılmış öğesine bakar.

Ortamınızı ek kapasiteye uyum sağlayacak şekilde ölçeklendirmek veya saklama süresini artırmak için [Azure Time Series Insights ortamınızı nasıl ölçeklendirebileceğiniz hakkında](time-series-insights-how-to-scale-your-environment.md) bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- Veri saklama ayarlarını yapılandırma veya değiştirme hakkında daha fazla bilgi için [Azure Time Series Insights ' de bekletme yapılandırması](time-series-insights-how-to-configure-retention.md)' nı gözden geçirin.

- Azure Time Series Insights bir [Azaltıcı gecikme](time-series-insights-environment-mitigate-latency.md)hakkında bilgi edinin.
