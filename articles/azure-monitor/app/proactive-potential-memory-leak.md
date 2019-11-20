---
title: Akıllı algılama-Azure Application Insights tarafından algılanan olası bellek sızıntısı | Microsoft Docs
description: Olası bellek sızıntıları için Azure Application Insights uygulamaları izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: f07da754c6c9e0ad0541db12740c1d80f7f884fd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818798"
---
# <a name="memory-leak-detection-preview"></a>Bellek sızıntısı algılama (Önizleme)

Application Insights uygulamanızdaki her bir işlemin bellek tüketimini otomatik olarak analiz eder ve olası bellek sızıntıları veya daha fazla bellek tüketimi hakkında sizi uyarabilir.

Bu özellik, uygulamanız için [performans sayaçlarını yapılandırma](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) dışında özel bir kurulum gerektirmez. Uygulamanız yeterli bellek performans sayacı telemetrisi (örneğin, özel bayt) oluşturduğunda etkindir.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Bu tür akıllı algılama bildirimini ne zaman alabilirim?
Tipik bir bildirim, bir veya daha fazla işlem ve/veya bir ya da daha fazla makinede, uygulamanızın bir parçası olan bellek tüketimine yönelik tutarlı bir artış izler. Makine öğrenimi algoritmaları, bellek sızıntısı 'nın düzeniyle eşleşen artan bellek tüketimini saptamak için kullanılır.

## <a name="does-my-app-really-have-a-problem"></a>Uygulamamın gerçekten bir sorunu var mı?
Hayır, bir bildirim uygulamanızın kesinlikle bir sorun olduğu anlamına gelmez. Bellek sızıntısı desenleri genellikle bir uygulama sorununa işaret etse de, bu desenler belirli bir işlem için tipik olabilir veya doğal bir iş gerekçesini alabilir ve yoksayılabilir.

## <a name="how-do-i-fix-it"></a>Nasıl yaparım? düzeltilsin mi?
Bildirimler tanılama Analizi işleminde desteklanacak tanılama bilgilerini içerir:
1. **Değerlendirme.** Bildirim, bellek artışını (GB cinsinden) ve belleğin arttığı zaman aralığını gösterir. Bu, soruna bir öncelik atamanıza yardımcı olabilir.
2. **Kapsam.** Kaç makine bellek sızıntısı düzenine neden oluyor? Olası bellek sızıntısı sırasında kaç özel durum tetikleniyor? Bu bilgiler bildirimden elde edilebilir.
3. **Tanın.** Algılama, zaman içinde işlemin bellek tüketimini gösteren bellek sızıntısı modelini içerir. Sorunu daha da tanılamanıza yardımcı olması için, destek bilgilerine bağlanan ilgili öğeleri ve raporları da kullanabilirsiniz.
