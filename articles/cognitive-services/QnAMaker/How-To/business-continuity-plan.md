---
title: İş sürekliliği planı-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: İş sürekliliği planının birincil amacı dayanıklı bir bilgi tabanı uç noktası oluşturmaktır, bu, bot veya onu kullanan uygulama için zaman kaybı olmamasını sağlar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 0e748e81de39b2bef14b543063adeb51b8b3ecdb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486712"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Soru-Cevap Oluşturma hizmetiniz için bir iş sürekliliği planı oluşturma

İş sürekliliği planının birincil amacı dayanıklı bir bilgi tabanı uç noktası oluşturmaktır, bu, bot veya onu kullanan uygulama için zaman kaybı olmamasını sağlar.

![Soru-Cevap Oluşturma bcp planı](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Yukarıda gösterilen üst düzey fikir aşağıdaki gibidir:

1. [Azure eşlenmiş bölgelerde](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)iki paralel [soru-cevap oluşturma hizmeti](../How-To/set-up-qnamaker-service-azure.md) ayarlayın.

2. Birincil ve ikincil Azure arama dizinlerini eşitlenmiş halde tutun. Azure dizinlerini yedekleme ve geri yükleme işlemlerinin nasıl yapılacağını görmek için [burada](https://github.com/pchoudhari/QnAMakerBackupRestore) GitHub örneğini kullanın.

3. Application Insights [sürekli dışarı aktarma](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)kullanarak yedekleyin.

4. Birincil ve ikincil yığınlar kurulduktan sonra, iki uç noktayı yapılandırmak ve bir yönlendirme yöntemi ayarlamak için [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) 'ı kullanın.

5. Traffic Manager uç noktanız için bir SSL sertifikası oluşturmanız gerekir. Uygulama hizmetlerinize [SSL sertifikasını bağlayın](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) .

6. Son olarak, bot veya uygulamanızdaki Traffic Manager uç noktasını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma dağıtımınız için kapasiteyi seçin](../Tutorials/choosing-capacity-qnamaker-deployment.md)
