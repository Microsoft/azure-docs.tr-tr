---
title: Azure Izleyici 'de uyarı örneklerini yönetme
description: Azure genelinde uyarı örneklerini yönetme
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dc4b62f937e9338051c4018554ec47c94f01a743
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033546"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Uyarı örneklerini birleştirilmiş uyarılarla yönetme

Azure Izleyici 'de [birleştirilmiş uyarılar deneyimiyle](./alerts-overview.md) birlikte, Azure 'daki tüm farklı uyarı tiplerinizi görebilirsiniz. Bu, birden çok aboneliği tek bir bölmede kapsar. Bu makalede, uyarı örneklerinizi nasıl görüntüleyebileceğinizi ve sorun giderme için belirli uyarı örneklerinin nasıl bulunacağını gösterir.

> [!NOTE]
> Yalnızca son 30 gün içinde oluşturulan uyarılara erişebilirsiniz.

## <a name="go-to-the-alerts-page"></a>Uyarılar sayfasına gidin

Uyarılar sayfasına aşağıdaki yollarla gidebilirsiniz:

- [Azure Portal](https://portal.azure.com/) **izleme**  >  **uyarıları**' nı seçin.  

     ![Izleyici uyarıları ekran görüntüsü](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Belirli bir kaynağın bağlamını kullanın. Bir kaynak açın, **izleme** bölümüne gidin ve **Uyarılar**' ı seçin. Giriş sayfası, belirli bir kaynaktaki uyarılar için önceden filtrelenmiştir.

     ![Kaynak Izleme uyarılarının ekran görüntüsü](media/alerts-managing-alert-instances/alert-resource.JPG)

- Belirli bir kaynak grubunun bağlamını kullanın. Bir kaynak grubu açın, **izleme** bölümüne gidin ve **Uyarılar**' ı seçin. Giriş sayfası, söz konusu kaynak grubundaki uyarılar için önceden filtrelenmiştir.    

     ![Kaynak grubu Izleme uyarılarının ekran görüntüsü](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Uyarı örnekleri bul

**Uyarı Özeti** sayfası, Azure genelinde tüm uyarı örneklerinizin genel bir görünümünü sağlar. Özet görünümünü **birden çok abonelik** (en fazla 5 ' e kadar) seçerek veya **kaynak grupları**, belirli **kaynaklar** veya **zaman aralıklarında** filtreleyerek değiştirebilirsiniz. Uyarılarınız için liste görünümüne gitmek için **Toplam uyarı** veya önem bantlarından herhangi birini seçin.     

![Uyarı özet sayfasının ekran görüntüsü](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
**Tüm uyarılar** sayfasında, Azure genelindeki tüm uyarı örnekleri listelenir. Portala bir uyarı bildirimi geliyorsa, bu belirli uyarı örneğinde daraltmak için kullanılabilen filtreleri kullanabilirsiniz.

> [!NOTE]
> Herhangi bir önem derecesindeki herhangi birini seçerek sayfaya geliyorsanız, liste o önem derecesine göre önceden filtrelenmiştir.

Önceki sayfada bulunan süzgeçlerden ayrı olarak, izleme hizmeti (örneğin, ölçümler için Platform), izleme koşulu (tetiklenen veya çözümlenmiş), önem derecesi, uyarı durumu (yeni/onaylanmış/kapalı) veya akıllı Grup KIMLIĞI ' ne göre de filtre uygulayabilirsiniz.

![Tüm uyarılar sayfasının ekran görüntüsü](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Herhangi bir önem derecesindeki herhangi birini seçerek sayfaya geliyorsanız, liste o önem derecesine göre önceden filtrelenmiştir.

Herhangi bir uyarı örneğinin seçilmesi **Uyarı ayrıntıları** sayfasını açar ve bu belirli uyarı örneği hakkında daha fazla ayrıntı görmenizi sağlar.   

![Uyarı Ayrıntıları sayfasının ekran görüntüsü](media/alerts-managing-alert-instances/alert-details.jpg)