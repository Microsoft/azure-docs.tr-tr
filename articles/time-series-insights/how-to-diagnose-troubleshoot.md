---
title: Gen2 ortamını tanılama ve sorunlarını giderme-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 ortamını tanılamayı ve sorun gidermeyi öğrenin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: d9dd07e3a35d83ff6bd9c7c493768d1197667c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108798"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 ortamını tanılama ve sorun giderme

Bu makalede, Azure Time Series Insights Gen2 ortamınızla çalışırken karşılaşabileceğiniz bazı yaygın sorunlar özetlenmektedir. Makalede ayrıca, her bir sorun için olası nedenler ve çözümler açıklanmaktadır.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Sorun: Gen2 Gezgini 'nde ortamımı bulamıyorum

Time Series Insights ortamına erişim izniniz yoksa bu sorun oluşabilir. Kullanıcıların Time Series Insights ortamını görüntülemesi için okuyucu düzeyinde bir erişim rolü olması gerekir. Geçerli erişim düzeylerini doğrulamak ve ek erişim vermek için [Azure portal](https://portal.azure.com/)Time Series Insights kaynağın **veri erişim ilkeleri** bölümüne gidin.

  [![Veri erişim ilkelerini doğrulayın.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Sorun: Gen2 Explorer 'da hiçbir veri görülmedi

Verilerinizin [Azure Time Series Insights Gen2 Explorer](https://insights.timeseries.azure.com/preview)'da görünmemesinin bazı yaygın nedenleri vardır.

- Olay kaynağınız veri almıyor olabilir.

    Bir olay hub 'ı veya IoT Hub 'ı olan olay kaynağınızın etiketinizden veya örneklerinizden veri aldığını doğrulayın. Doğrulamak için Azure portal kaynağınızın genel bakış sayfasına gidin.

    [![Pano ölçümlerine genel bakış konusunu inceleyin.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Olay kaynak verileriniz JSON biçiminde değil.

    Time Series Insights yalnızca JSON verilerini destekler. JSON örnekleri için [desteklenen JSON şekillerini](./concepts-json-flattening-escaping-rules.md)okuyun.

- Olay kaynak anahtarınıza gerekli bir izin eksik.

  - IoT Hub 'ı için, **hizmet bağlantısı** iznine sahip anahtarı sağlamanız gerekir.

    [![IoT Hub izinlerini doğrulayın.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Her iki ilke de **ıothubowner** ve **Service** , **hizmet Connect** iznine sahip olduklarından çalışır.

  - Bir olay hub 'ı için, **dinlemesi** iznine sahip anahtarı sağlamanız gerekir.
  
    [![Olay Hub 'ı izinlerini gözden geçirin.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - **Okuma** ve **yönetme** Ilkeleri her ikisi de **dinleme** iznine sahip olduklarından çalışır.

- Belirtilen tüketici grubunuz Time Series Insights için özel değil.

    Bir IoT Hub 'ı veya Olay Hub 'ı kaydı sırasında, verileri okumak için kullanılan tüketici grubunu belirtirsiniz. Bu Tüketici grubu, ortam başına benzersiz olmalıdır. Tüketici grubu paylaşılmışsa, temeldeki Olay Hub 'ı, okuyuculardan birinin rastgele bağlantısını otomatik olarak keser. Okunacak Time Series Insights için benzersiz bir tüketici grubu sağlayın.

- Sağlama sırasında belirtilen zaman serisi KIMLIĞI özelliği yanlış, eksik veya null.

    Bu sorun, ortamı sağlama sırasında zaman serisi KIMLIĞI özelliği yanlış yapılandırılmışsa meydana gelebilir. Daha fazla bilgi için, [zaman SERISI kimliği seçmek üzere en iyi uygulamaları](./how-to-select-tsid.md)okuyun. Şu anda, mevcut bir Time Series Insights ortamını farklı bir zaman serisi KIMLIĞI kullanacak şekilde güncelleştiremezsiniz.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Sorun: bazı veriler gösteriliyor, ancak bazıları eksik

Zaman serisi KIMLIĞI olmadan veri gönderiyor olabilirsiniz.

- Bu sorun, yük içindeki zaman serisi KIMLIĞI alanı olmadan olayları gönderdiğinizde meydana gelebilir. Daha fazla bilgi için [desteklenen JSON şekillerini](./concepts-json-flattening-escaping-rules.md)okuyun.
- Bu sorun, ortamınız kısıtlanmakta olduğu için meydana gelebilir.

    > [!NOTE]
    > Time Series Insights, en fazla 1 MB/sn alma oranını destekler.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Sorun: veriler gösteriliyor, ancak şimdi alındı

- Olay kaynak anahtarınız yeniden üretildi ve Gen2 ortamınız yeni olay kaynak anahtarına ihtiyaç duyuyor olabilir.

Bu sorun, olay kaynağınızı oluştururken girilen anahtar artık geçerli olmadığında oluşur. Hub 'ınızda telemetri görürsünüz ancak Time Series Insights Ileti alınmadı. Anahtarın yeniden oluşturulup oluşturulmayacağını bilmiyorsanız, "ad alanı yetkilendirme kuralları oluşturma veya güncelleştirme" için Event Hubs ' etkinlik günlüğünde arama yapabilir veya IoT Hub 'ı için "Create or Update ıothub Resource" araması yapabilirsiniz.

Time Series Insights Gen2 ortamınızı yeni anahtarla güncelleştirmek için, hub kaynağınızı Azure portal açın ve yeni anahtarı kopyalayın. TSI kaynağına gidin ve olay kaynakları ' na tıklayın.

   [![Ekran görüntüsü, olay kaynakları menü öğesi olarak adlandırılan T S I kaynağını gösterir.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Alma işleminden durdurulan olay kaynaklarını seçin, yeni anahtarı yapıştırın ve Kaydet ' e tıklayın.

   [![Ekran görüntüsünde, T. T Merkez İlkesi anahtarı girilen T S ı kaynağını gösterir.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Sorun: olay kaynağınızın zaman damgası Özellik adı çalışmıyor

Ad ve değerin aşağıdaki kurallara uygun olduğundan emin olun:

- Zaman damgası Özellik adı büyük/küçük harfe duyarlıdır.
- Olay kaynağınızdan JSON dizesi olarak gelen zaman damgası özelliği değeri biçimindedir `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Bu tür bir dizeye örnek olarak `"2008-04-12T12:53Z"` .

Zaman damgası özelliği adınızın yakalanıp düzgün şekilde çalışmasını sağlamanın en kolay yolu Time Series Insights Gen2 Gezginini kullanmaktır. Time Series Insights Gen2 Explorer içinde, zaman damgası Özellik adı sağlamadıktan sonra bir süre seçmek için grafiği kullanın. Seçime sağ tıklayın ve **olayları keşfet** seçeneğini belirleyin. İlk sütun üst bilgisi, zaman damgası özellik adıdır. Bunun `($ts)` yerine sözcüğün yanına olması gerekir `Timestamp` :

- `(abc)`Time Series Insights, veri değerlerini dizeler olarak okuduğunu gösterir.
- Time Series Insights, veri değerini DateTime olarak okuduğunu gösteren **Takvim** simgesi.
- `#`Time Series Insights, veri değerlerini tamsayı olarak okuduğunu gösterir.

Zaman damgası özelliği açıkça belirtilmemişse, varsayılan zaman damgası olarak bir olayın IoT Hub 'ı veya Olay Hub 'ı sıraya alma zamanı kullanılır.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Sorun: gezgin 'de sıcak mağazamın verilerini görüntüleyemiyorum

- Kısa süre önce ısınma deponuzu sağlamış olabilirsiniz ve veriler hala içinde akar.
- Isınma deponuzu silmiş olabilirsiniz ve bu durumda veri kaybı olur.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Sorun: zaman serisi modelimi görüntüleyemiyorum veya düzenleyemiyorum

- Time Series Insights S1 veya S2 ortamına erişim sağlayabilirsiniz.

   Zaman serisi modelleri yalnızca Kullandıkça Öde ortamlarında desteklenir. S1 veya S2 ortamınıza Time Series Insights Gen2 Explorer 'dan erişme hakkında daha fazla bilgi için, [Gezgin 'de verileri görselleştirme](./concepts-ux-panels.md)makalesini okuyun.

   [![Ortamda olay yok.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Modeli görüntüleme ve düzenleme izinlerine sahip olmayabilirsiniz.

   Kullanıcıların zaman serisi modelini düzenlemek ve görüntülemek için katkıda bulunan düzeyinde erişimi olması gerekir. Geçerli erişim düzeylerini doğrulamak ve ek erişim vermek için, Azure portal Time Series Insights kaynağınızın **veri erişim ilkeleri** bölümüne gidin.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Sorun: Gen2 Explorer 'daki tüm örneklerim üst öğeye sahip değil

Ortamınızda tanımlı bir zaman serisi model hiyerarşisi yoksa bu sorun oluşabilir. Daha fazla bilgi için [zaman serisi modelleriyle çalışma](./time-series-insights-overview.md)hakkında bilgi edinin.

  [![Üst öğe olmayan örnekler bir uyarı görüntüler.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- [Zaman serisi modelleriyle çalışma](./time-series-insights-overview.md)hakkında bilgi edinin.

- [Desteklenen JSON şekilleri](./concepts-json-flattening-escaping-rules.md)hakkında bilgi edinin.

- Azure Time Series Insights Gen2 ' de [planlamayı ve limitleri](./how-to-plan-your-environment.md) gözden geçirin.