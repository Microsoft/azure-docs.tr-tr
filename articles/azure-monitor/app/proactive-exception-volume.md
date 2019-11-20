---
title: Akıllı algılama-özel durum biriminde olağan dışı artış, Azure Application Insights | Microsoft Docs
description: Özel durum hacminde olağan olmayan desenler için Azure Application Insights ile uygulama özel durumlarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/08/2017
ms.openlocfilehash: fa7669b78caabb95b08200e83ed18ea982ce9ac9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820610"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Özel durum biriminde olağan dışı artış (Önizleme)

Application Insights uygulamanızda oluşturulan özel durumları otomatik olarak analiz eder ve özel durum telemetrinizdeki olağan dışı desenler hakkında sizi uyarabilir.

Bu özellik, uygulamanız için [özel durum raporlamayı yapılandırmadan](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) farklı bir kurulum gerektirmez. Uygulamanız yeterli sayıda özel durum telemetrisi oluşturduğunda etkin olur.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Bu tür akıllı algılama bildirimini ne zaman alabilirim?
Bu tür bir bildirime, uygulamanız bir gün içinde belirli bir tür özel durum sayısında, önceki yedi gün içinde hesaplanan bir taban çizgisine kıyasla olağan dışı bir artış yaşanıyorsa elde edebilirsiniz.
Makine öğrenimi algoritmaları, uygulama kullanımınızın doğal bir büyümesini hesaba katlarken özel durum sayısı artışı algılamak için kullanılır.

## <a name="does-my-app-definitely-have-a-problem"></a>Uygulamamın kesinlikle bir sorunu var mı?
Hayır, bir bildirim uygulamanızın kesinlikle bir sorun olduğu anlamına gelmez. Aşırı sayıda özel durum genellikle bir uygulama sorununa işaret etse de, bu özel durumlar sizin uygulamanız tarafından zararsız olabilir ve doğru şekilde işlenebilir.

## <a name="how-do-i-fix-it"></a>Nasıl yaparım? düzeltilsin mi?
Bildirimler tanılama sürecinde desteklanacak tanılama bilgilerini içerir:
1. **Değerlendirme.** Bildirim, kaç kullanıcının veya kaç isteğin etkilendiğini gösterir. Bu, soruna bir öncelik atamanıza yardımcı olabilir.
2. **Kapsam.** Sorun tüm trafiği etkiliyor mu, yoksa yalnızca bir işlem mi? Bu bilgiler bildirimden elde edilebilir.
3. **Tanın.** Algılama, özel durumun oluşturulduğu yöntemin yanı sıra özel durum türü hakkında bilgiler içerir. Sorunu daha da tanılamanıza yardımcı olması için, destek bilgilerine bağlanan ilgili öğeleri ve raporları da kullanabilirsiniz.