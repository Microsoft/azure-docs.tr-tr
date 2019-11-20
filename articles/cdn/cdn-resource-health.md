---
title: Azure CDN kaynakları durumunu izleyin | Microsoft Docs
description: Azure CDN kaynaklarınızı Azure kaynak durumu kullanarak durumunu izlemeyi öğrenin.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593448"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Azure CDN kaynakları durumunu izleyin
  
Azure CDN kaynak durumu, bir alt kümesidir [Azure kaynak durumu](../resource-health/resource-health-overview.md).  Azure kaynak durumu, CDN kaynaklarını durumunu izlemek ve sorunlarını gidermeye yönelik uygulanabilir rehberlik almak için kullanabilirsiniz.

>[!IMPORTANT] 
>Azure CDN kaynak durumu şu anda yalnızca sistem durumunu küresel CDN teslim ve API özellikleri için hesaplar.  Azure CDN kaynak durumu, bireysel CDN uç noktası doğrulamaz.
>
>Azure CDN kaynak durumu akış sinyaller Gecikmeli tamamlanması 15 dakika olabilir.

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN kaynak sistem durumu bulma

1. İçinde [Azure portalında](https://portal.azure.com), CDN profilinize gidin.

2. Tıklayın **ayarları** düğmesi.

    ![Ayarlar düğmesi](./media/cdn-resource-health/cdn-profile-settings.png)

3. Altında *destek + sorun giderme*, tıklayın **kaynak durumu**.

    ![CDN kaynak durumu](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Listelenen CDN kaynakları da bulabilirsiniz *kaynak durumu* kutucuğu *Yardım + Destek* dikey penceresi.  Hızlı bir şekilde elde edebilirsiniz *Yardım + Destek* daire içinde tıklayarak **?** Portalın sağ alt köşesinde.
>
> ![Yardım + destek](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN özel iletileri

Azure CDN kaynak sağlığı ile ilgili durumları aşağıda bulabilirsiniz.

|`Message` | Önerilen Eylem |
|---|---|
|Bir veya daha çok CDN uç noktanızı durdurmuş, kaldırmış veya yanlış yapılandırmış olabilirsiniz | Bir veya daha çok CDN uç noktanızı durdurmuş, kaldırmış veya yanlış yapılandırmış olabilirsiniz.|
|Üzgünüz, CDN yönetim hizmeti şu anda kullanılamıyor | Durum güncelleştirmeleri için buraya tekrar denetleyin; Beklenen çözüm süresinden sonra sorun devam ederse desteğe başvurun.|
|CDN uç noktalarınız bazı CDN sağlayıcılarımızla ilgili devam eden sorunlardan etkileniyor olabilir | Durum güncelleştirmeleri için buraya tekrar denetleyin; Kaynağınızı ve CDN uç test etme konusunda bilgi almak için sorun giderme aracını kullanın. Beklenen çözüm süresinden sonra sorun devam ederse desteğe başvurun. |
|Üzgünüz, CDN uç noktası yapılandırma değişikliklerinde yayma gecikmeleri yaşanıyor | Durum güncelleştirmeleri için buraya tekrar denetleyin; Yapılandırma değişiklikleriniz beklenen sürede tamamen yayılmazsa, desteğe başvurun.|
|Üzgünüz, ek portalı yüklerken sorun yaşıyoruz | Durum güncelleştirmeleri için buraya tekrar denetleyin; Beklenen çözüm süresinden sonra sorun devam ederse desteğe başvurun.|
Üzgünüz, bazı CDN sağlayıcılarımızla ilgili sorun yaşıyoruz | Durum güncelleştirmeleri için buraya tekrar denetleyin; Beklenen çözüm süresinden sonra sorun devam ederse desteğe başvurun. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynak durumu hakkında genel bakış okuyun](../resource-health/resource-health-overview.md)
- [CDN sıkıştırma sorunlarını giderme](./cdn-troubleshoot-compression.md)
- [404 hataları ile ilgili sorunları giderme](./cdn-troubleshoot-endpoint.md)