---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001218"
---
`ApplicationInsights`Ayar, kapsayıcınıza [Azure Application Insights](/azure/application-insights) telemetri desteği eklemenize olanak tanır. Application Insights kapsayıcının derinlemesine izlenmesini sağlar. Kapsayıcınızı kullanılabilirlik, performans ve kullanım açısından kolayca izleyebilirsiniz. Ayrıca, kapsayıcıınızda hataları hızlıca tanımlayabilir ve tanılayabilirsiniz.

Aşağıdaki tabloda, bölümünde desteklenen yapılandırma ayarları açıklanmaktadır `ApplicationInsights` .

|Gerekli| Name | Veri türü | Açıklama |
|--|------|-----------|-------------|
|Hayır| `InstrumentationKey` | Dize | Kapsayıcı için Telemetri verilerinin gönderildiği Application Insights örneğinin izleme anahtarı. Daha fazla bilgi için bkz. [ASP.NET Core Application Insights](../articles/azure-monitor/app/asp-net-core.md). <br><br>Örnek:<br>`InstrumentationKey=123456789`|