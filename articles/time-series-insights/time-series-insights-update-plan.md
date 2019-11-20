---
title: Önizleme ortamınızı planlayın-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights önizleme ortamınızı nasıl planlayacağınızı öğrenin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4b87bf9bdb70f2bcef27927dbaa0d79716c81368
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006335"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights Preview ortamınızı planlayın

Bu makalede Azure Time Series Insights önizlemeyi kullanarak hızlı bir şekilde planlamak ve kullanmaya başlamak için en iyi yöntemler açıklanmaktadır.

> [!NOTE]
> Genel kullanılabilirlik Time Series Insights örneği planlamak için en iyi uygulamalar için bkz. [Azure Time Series Insights genel kullanılabilirlik ortamınızı planlayın](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Planlama ve hazırlık için en iyi uygulamalar

Ortamınızı planlamaya ve hazırlamaya yönelik en iyi uygulamalar aşağıdaki makalelerde daha ayrıntılı olarak açıklanmıştır:

* [Time Series Insights önizleme ortamı sağladığınızda](#the-preview-environment)alacağınız Özellikler.
* [Zaman serisi kimlikleriniz ve zaman damgası özellikleridir](#configure-time-series-ids-and-timestamp-properties).
* Yeni [zaman serisi modelinin ne olduğu](#understand-the-time-series-model)ve kendinizinkini nasıl oluşturabileceğiniz.
* [JSON 'da olayları verimli](#shape-your-events)bir şekilde gönderme.
* [İş olağanüstü durum kurtarma seçeneklerini](#business-disaster-recovery)Time Series Insights.

Azure Time Series Insights Kullandıkça Öde iş modelini kullanır. Ücretler ve kapasite hakkında daha fazla bilgi için bkz. [Time Series Insights fiyatlandırması](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Önizleme ortamı

Time Series Insights bir önizleme ortamı sağladığınızda iki Azure kaynağı oluşturursunuz:

* Azure Time Series Insights önizleme ortamı
* Azure depolama genel amaçlı v1 hesabı

Sağlama sürecinin bir parçası olarak, bir ısınma deposunu etkinleştirmek isteyip istemediğinizi belirlersiniz. Sıcak mağaza size katmanlı bir sorgu deneyimi sağlar. Etkinleştirildiğinde, 7 ila 30 gün arasında bir saklama süresi belirtmeniz gerekir. Sıcak mağaza Bekletme dönemi içinde yürütülen sorgular genellikle daha hızlı yanıt süreleri sağlar. Bir sorgu, yarı zamanlı saklama süresi üzerinden yayıldığında, bu, soğuk depodan sunulur.

Isınma mağazasındaki sorgular ücretsizdir, ancak soğuk depodaki sorgular maliyetlendirilir. Sorgu desenlerinizi anlamanız ve ısınma Mağazası yapılandırmanızı buna göre planlamanız önemlidir. En son veriler üzerinde etkileşimli analizler 'in, ısınma mağazalarınızın yanı sıra, uzun süreli eğilimler soğuk bir şekilde bulunmasını öneririz.

> [!NOTE]
> Şu anda, yarı depolama ile en fazla 1.000 Özellik destekliyoruz.

Başlamak için üç ek öğe gereklidir:

* [Zaman serisi modeli](./time-series-insights-update-tsm.md)
* [Time Series Insights bağlı bir olay kaynağı](./time-series-insights-how-to-add-an-event-source-iothub.md)
* Hem modele eşlenmiş hem de geçerli JSON biçiminde olan [olay kaynağına akan olaylar](./time-series-insights-send-events.md)

## <a name="review-preview-limits"></a>Önizleme sınırlarını gözden geçirin

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Zaman serisi kimliklerini ve zaman damgası özelliklerini yapılandırma

Yeni bir Time Series Insights ortamı oluşturmak için bir zaman serisi KIMLIĞI seçin. Bunun yapılması verileriniz için bir mantıksal bölüm işlevi görür. Belirtildiği gibi, zaman serisi kimliklerinizi hazırlayın.

> [!IMPORTANT]
> Zaman serisi kimlikleri *daha sonra değiştirilemez*. Son seçim ve ilk kullanmadan önce her birini doğrulayın.

Kaynaklarınızı benzersiz şekilde ayırt etmek için en fazla üç anahtar seçebilirsiniz. Daha fazla bilgi için, zaman serisi KIMLIĞI ve [depolama ve](./time-series-insights-update-storage-ingress.md)giriş [seçmek üzere en iyi uygulamaları](./time-series-insights-update-how-to-id.md) okuyun.

**Timestamp** özelliği de önemlidir. Olay kaynaklarını eklediğinizde bu özelliği belirleyebilirsiniz. Her olay kaynağında, olay kaynaklarını zaman içinde izlemek için kullanılan isteğe bağlı bir zaman damgası özelliği vardır. Zaman damgası değerleri büyük/küçük harfe duyarlıdır ve her bir olay kaynağının tek bir belirtimine biçimlendirilmelidir.

> [!TIP]
> Olay kaynaklarınız için biçimlendirme ve ayrıştırma gereksinimlerini doğrulayın.

Boş bırakıldığında, olay zaman damgası olarak bir olay kaynağının olay sıraya alma zamanı kullanılır. Geçmiş verileri veya toplu olayları gönderirseniz, zaman damgası özelliğini özelleştirmek varsayılan olay sıraya alma zamanından daha yararlıdır. Daha fazla bilgi için [Azure IoT Hub 'de olay kaynakları ekleme](./time-series-insights-how-to-add-an-event-source-iothub.md)hakkında bilgi edinin.

## <a name="understand-the-time-series-model"></a>Zaman serisi modelini anlama

Artık Time Series Insights ortamınızın zaman serisi modelini yapılandırabilirsiniz. Yeni model IoT verilerini bulmayı ve çözümlemeyi kolaylaştırır. Zaman serisi verilerinin listesini alma, bakım ve zenginleştirmeye olanak sağlar ve tüketiciye yönelik veri kümelerini hazırlamaya yardımcı olur. Model, benzersiz kaynağı türler ve hiyerarşiler olarak bilinen değişkenlerle ilişkilendiren bir örnekle eşlenen zaman serisi kimliklerini kullanır. Yeni [zaman serisi modeli](./time-series-insights-update-tsm.md)hakkında bilgi edinin.

Model dinamiktir, bu nedenle herhangi bir zamanda derlenebilir. Hızlı bir başlangıç yapmak için, Time Series Insights verileri dağıtmadan önce derleyin ve karşıya yükleyin. Modelinizi derlemek için bkz. [zaman serisi modelini kullanma](./time-series-insights-update-how-to-tsm.md).

Birçok müşteri için, zaman serisi modeli mevcut bir varlık modeliyle veya ERP sistemiyle eşlenir. Mevcut bir modeliniz yoksa, hızlı bir şekilde çalışmaya başlaması için önceden oluşturulmuş bir kullanıcı deneyimi [sağlanır](https://github.com/Microsoft/tsiclient) . Bir modelin size nasıl yardımcı olabileceğini öğrenmek için [örnek Tanıtım ortamını](https://insights.timeseries.azure.com/preview/demo)görüntüleyin.

## <a name="shape-your-events"></a>Olaylarınızı şekillendirin

Time Series Insights olayları gönderme yolunu doğrulayabilirsiniz. İdeal olarak, olaylarınızın iyi ve verimli bir şekilde etkili olması gerekir.

Parmak için iyi bir kural:

* Meta verileri zaman serisi modelinize depolayın.
* Zaman serisi modunun, örnek alanlarının ve olayların yalnızca gerekli bilgileri (örneğin, zaman serisi KIMLIĞI veya zaman damgası özelliği) içerdiğinden emin olun.

Daha fazla bilgi için bkz. [Şekil olayları](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Advisor](../advisor/advisor-overview.md) 'ı inceleyerek iş kurtarma yapılandırması seçeneklerinizi planlayın.
- Time Series Insights önizlemede [depolama ve](./time-series-insights-update-storage-ingress.md) giriş hakkında daha fazla bilgi edinin.
- Time Series Insights önizlemede [veri modelleme](./time-series-insights-update-tsm.md) hakkında bilgi edinin.
