---
title: Azure Application Insights 'de HockeyApp verilerini keşfetme | Microsoft Docs
description: Azure uygulamanızın kullanımını ve performansını Application Insights ile çözümleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819590"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Application Insights 'de HockeyApp verilerini keşfetme

> [!NOTE]
> HockeyApp artık yeni uygulamalar için kullanılamaz. Mevcut HockeyApp dağıtımları çalışmaya devam edecektir. Visual Studio App Center artık yeni mobil uygulamaları izlemek için Microsoft 'tan önerilen hizmettir. [App Center ve Application Insights ile uygulamalarınızı ayarlamayı öğrenin](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) , canlı masaüstü ve mobil uygulamaları izlemeye yönelik bir hizmettir. HockeyApp adresinden, kullanımı ve tanılama 'yı izlemek için özel ve izleme telemetrisi gönderebilirsiniz (kilitlenme verilerini almaya ek olarak). Bu telemetri akışı, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)güçlü [analiz](../../azure-monitor/app/analytics.md) özelliği kullanılarak sorgulanabilir. Ayrıca, [özel ve izleme telemetrisini de dışarı aktarabilirsiniz](export-telemetry.md). Bu özellikleri etkinleştirmek için, Application Insights HockeyApp özel verileri aktaran bir köprü ayarlarsınız.

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp Köprüsü uygulaması
HockeyApp Köprüsü uygulaması, analiz ve sürekli dışa aktarma özellikleri aracılığıyla Application Insights özel ve izleme telemetrinize erişmenizi sağlayan temel özelliktir. HockeyApp Köprüsü uygulamasının oluşturulmasından sonra, HockeyApp tarafından toplanan özel ve izleme olayları bu özelliklerden erişilebilir olur. Şimdi bu köprü uygulamalarından birini ayarlamayı görelim.

HockeyApp ' de, hesap ayarları, [API belirteçleri](https://rink.hockeyapp.net/manage/auth_tokens)' ni açın. Yeni bir belirteç oluşturun ya da var olan bir belirteci yeniden kullanın. Gereken en düşük haklar "salt okunurdur". API belirtecinin bir kopyasını alın.

![HockeyApp API belirteci al](./media/hockeyapp-bridge-app/01.png)

Microsoft Azure portal açın ve [bir Application Insights kaynağı oluşturun](../../azure-monitor/app/create-new-resource.md ). Uygulama türünü "HockeyApp Bridge uygulaması" olarak ayarlayın:

![Yeni Application Insights kaynağı](./media/hockeyapp-bridge-app/02.png)

Ad ayarlamanız gerekmez-Bu, otomatik olarak HockeyApp adından ayarlanır.

HockeyApp Bridge alanları görüntülenir. 

![Köprü alanlarını girin](./media/hockeyapp-bridge-app/03.png)

Daha önce not ettiğiniz HockeyApp belirtecini girin. Bu eylem, "HockeyApp uygulaması" açılır menüsünü tüm HockeyApp uygulamalarınızla doldurur. Kullanmak istediğiniz birini seçin ve kalan alanları doldurun. 

Yeni kaynağı açın. 

Verilerin akışa başlaması biraz zaman aldığını unutmayın.

![Verileri bekleyen Application Insights kaynak](./media/hockeyapp-bridge-app/04.png)

İşte bu kadar! HockeyApp tarafından izlenen uygulamanızda toplanan özel ve izleme verileri artık Application Insights analiz ve sürekli dışa aktarma özelliklerinde de sunulmaktadır.

Şimdi kullanabileceğiniz bu özelliklerin her birini kısaca gözden geçirelim.

## <a name="analytics"></a>Analiz
Analiz, verilerinizin geçici olarak sorgulanmasına yönelik güçlü bir araçtır. Bu, telemetrinizi tanılamanıza ve analiz etmenize ve temel nedenler ile desenleri hızlı bir şekilde bulmanıza olanak tanır.

![Analiz](./media/hockeyapp-bridge-app/05.png)

* [Analiz hakkında daha fazla bilgi edinin](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Sürekli dışarı aktarma
Sürekli dışarı aktarma, verilerinizi bir Azure Blob depolama kapsayıcısına vermenize olanak tanır. Bu, verilerinizi Application Insights tarafından sunulan saklama süresinden daha uzun süre tutmanız gerektiğinde çok yararlı olur. Verileri blob depolamada tutabilir, bir SQL veritabanında veya tercih ettiğiniz veri ambarı çözümünüz olarak işleyebilirsiniz.

[Sürekli dışarı aktarma hakkında daha fazla bilgi edinin](export-telemetry.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Verilerinize analiz uygulayın](../../azure-monitor/log-query/get-started-portal.md)

