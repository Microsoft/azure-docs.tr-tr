---
title: IoT Tak ve Kullan cihazlarınızın sorunlarını giderme
description: IoT Tak ve Kullan cihazına sertifika verecek iş ortakları için önerilen sorun giderme adımları Kılavuzu.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559311"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>IoT Tak ve Kullan sertifika projenizde sorun giderme

IoT Tak ve Kullan sertifika projenizin Connect & test aşamasında, IoT sertifika hizmeti (AıCS) testini Azure 'a geçirmeyi önleyen bazı senaryolar üzerinde karşılaşabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Oturumunuz açık olmalıdır ve [Azure Sertifikalı cihaz portalında](https://certify.azure.com)cihazınızda oluşturulmuş bir projeniz olmalıdır. Daha fazla bilgi için [öğreticiyi](tutorial-01-creating-your-project.md)görüntüleyin.

## <a name="when-aics-tests-arent-passing"></a>AICS testleri geçirilmemişse

Birçok nedenden dolayı AICS testi geçemeyebilir. Yaygın sorunları denetlemek ve cihazınızda sorun gidermek için bu adımları izleyin.

1. Cihaz kodunuzun, DPS sağlama sırasında model KIMLIĞI yükünü ayarlamaktan emin olun. Bu, AıCS 'nin cihazınızı doğrulama gereksinimidir.
1. `View Logs`Testin başarısız olmasına neden olduğunu belirlemek için düğmeye basarak önceki test çalıştırmalarının telemetri günlüklerini görüntüleyebilirsiniz. Test mesajlaşma ve ham verilerin her ikisi de gözden geçirilmek üzere kullanılabilir.  

    ![Test verilerini gözden geçirme](./media/images/review-logs.png)

1. Günlüklerin işaret ettiği bazı örneklerde `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` , cihazı yeniden başlatmayı ve cihaz sağlama işlemini yeniden başlatmayı deneyin.
1. Otomatikleştirilmiş testler başarısız olmaya devam ederse, `request a manual review` yerine konacak sonuçları kullanabilirsiniz. Bu, Azure Sertifikalı cihaz ekibiyle **el ile doğrulama** isteği tetikler.  

    ![El ile inceleme iste](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>"Uyarılarla geçildi" gördüğünüzde

Testleri çalıştırırken, bir sonucu alırsanız `Passed with warnings` Bu, test döneminde bazı telemetrinin alınmadığı anlamına gelir. Bunun nedeni, telemetrinin daha uzun zaman aralıklarıyla veya kullanılamayan dış tetikleyicilere bağımlılığı olabilir. Cihazınızı gözden geçirilmek üzere göndermeye devam edebilirsiniz, bu sırada İnceleme ekibinin ileride **el ile doğrulamanın** gerekli olup olmadığını saptacaktır.

## <a name="when-you-need-help-with-the-model-repository"></a>Model deposu ile ilgili yardıma ihtiyacınız olduğunda

Model deposuyla ilgili IoT Tak ve Kullan sorunları için, [cihaz modeli deposu hakkında belge kılavuzumuza](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuz, IoT Tak ve Kullan sertifika yolculuğuna devam etmenize yardımcı olur! AıCS 'yi geçtikten sonra cihazınızı göndermek ve yayımlamak için öğreticilerimizle devam edebilirsiniz.

- [Öğretici: cihazınızı test etme](tutorial-03-testing-your-device.md)
