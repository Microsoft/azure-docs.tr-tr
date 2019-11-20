---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669857"
---
İşlevler, [Azure Portal]bir işlev uygulamasına Application Insights tümleştirme eklemeyi kolaylaştırır.

1. [Portal][azure portal] **tüm hizmetler > işlev uygulamaları**' nı seçin, işlev uygulamanızı seçin ve ardından pencerenin üst kısmındaki **Application Insights** başlığını seçin

    ![Portaldan Application Insights etkinleştirme](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Görüntünün altındaki tabloda belirtilen ayarları kullanarak bir Application Insights kaynağı oluşturun.

   ![Application Insights kaynağı oluşturma](media/functions-connect-new-app-insights/ai-general.png)

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Benzersiz uygulama adı | Kendi aboneliğinizde benzersiz olması gereken işlev uygulamanız ile aynı adı kullanmak en kolay yoldur. | 
    | **Location** | Batı Avrupa | Mümkünse, işlev uygulamanız ile aynı [bölgeyi](https://azure.microsoft.com/regions/) veya bu bölgeye yakın olanı kullanın. |

1. **Tamam**’ı seçin. Application Insights kaynak, işlev uygulamanız ile aynı kaynak grubunda ve abonelikte oluşturulur. Kaynak oluşturulduktan sonra Application Insights penceresini kapatın.

1. İşlev uygulamanıza geri dönün, **uygulama ayarları**' nı seçin ve ardından **uygulama ayarları**' na kaydırın. Adlı `APPINSIGHTS_INSTRUMENTATIONKEY`bir ayar görürseniz, Azure 'da çalışan işlev uygulamanız için Application Insights tümleştirme etkinleştirilir.

[Azure Portal]: https://portal.azure.com
