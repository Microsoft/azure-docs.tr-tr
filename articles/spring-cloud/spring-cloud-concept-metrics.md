---
title: Azure yay bulutu için ölçümleri anlama
description: Azure Spring Cloud 'da ölçümleri incelemeyi öğrenin
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607801"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure yay bulutu için ölçümler

Azure Izleyici Ölçüm Gezgini, grafikleri çizdirme, eğilimleri görsel olarak ilişkilendirme ve ölçümlerde ani artışları ve DIP 'leri araştırmanıza olanak tanıyan Microsoft Azure portal bir bileşenidir. Kaynaklarınızın sistem durumunu ve kullanımını araştırmak için ölçüm gezginini kullanın. Azure Spring Cloud 'da ölçümleri görüntülemek için iki seçenek sunuyoruz: **uygulamaya genel bakış** sayfasında ve hizmet düzeyi ölçümleri sayfasında.

## <a name="application-overview-page"></a>Uygulamaya genel bakış sayfası

Her uygulamanın **genel bakış** sayfasında, uygulamanızda hızlı bir durum denetimi gerçekleştirmenize olanak tanıyan bir ölçüm grafiği sunulmaktadır.  Azure Spring Cloud Service sayfanıza gidin ve **uygulama panosu**' nu seçip listeden bir uygulama seçin.  

Şunlar için her dakikada ölçüm güncellenen 5 grafik sağlıyoruz:

* **Http sunucusu hataları**: UYGULAMANıZA yönelik http istekleri için hata sayısı.
* **Içindeki veriler**: uygulamanız tarafından alınan bayt.
* **Giden veri**: uygulamanıza gönderilen bayt.
* **İstekler**: uygulamanız tarafından alınan istekler.
* **Ortalama yanıt süresi**: uygulamanızdan alınan ortalama yanıt süresi.

Grafik için 1 saat ila 7 güne kadar bir zaman aralığı seçebilirsiniz.

## <a name="service-level-metric-queries"></a>Hizmet düzeyi ölçüm sorguları

Azure yay bulutu, çeşitli uygulama ölçümlerini izlemenize olanak sağlar. Bu hizmet hakkında daha fazla bilgi edinmek için Azure Izleyici ölçümlerini kullanmaya başlama [kılavuzunu](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) gözden geçirin.

Ölçüm verilerini gözden geçirmek için ölçüm, **toplama**ve zaman aralığınızı seçersiniz.  Bu kavramlar aşağıda açıklanmıştır.

### <a name="aggregation"></a>Toplama 

Azure, ölçümleri her dakikada yoklar ve güncelleştirir. Azure, seçilen bir zaman aralığı için verileri toplamanın üç yolunu sunar:

* **Toplam**: tüm ölçümleri hedef çıktı olarak toplayın.
* **Average**: dönemdeki ortalama değeri hedef çıktı olarak kullanın.
* **Max/min**: dönemde hedef çıktı olarak en fazla/en fazla değeri kullanın.

### <a name="time-range"></a>Zaman aralığı

Varsayılan bir zaman aralığı seçin veya kendinizinkini tanımlayın.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Ölçüm sorgunuzun ayrıntı düzeyini değiştirme

Azure, varsayılan olarak Azure yay bulut hizmeti 'nin tüm uygulamalarına yönelik ölçümleri toplar. Uygulama veya örnek düzeyindeki ölçümleri gözden geçirmek için filtre işlevini kullanın.  
**Filtre Ekle**' yi seçin, özelliği **uygulama** olarak ayarlayın ve izlemek istediğiniz hedef uygulamayı seçin. İsteğe bağlı olarak, grafikteki her bir uygulama için ayrı satırlar çizmek üzere **bölme Uygula** seçeneğini kullanın.

>[!TIP]
> Ölçümler sayfasında kendi grafiklerinizi oluşturabilir ve bunları **panonuza**sabitleyebilirsiniz. Grafiğinizi adlandırarak başlayın.  Sonra **sağ üst köşedeki panoya sabitle**' yi seçin. Artık uygulamanızı Portal **panonuzda**denetleyebilirsiniz.