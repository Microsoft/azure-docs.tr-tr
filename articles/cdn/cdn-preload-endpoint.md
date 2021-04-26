---
title: Azure CDN uç noktasında varlıkları önceden yükleme | Microsoft Docs
description: Azure Content Delivery Network uç noktasında önbelleğe alınmış içeriği önceden yüklemeyi öğrenin. Bu özellik ürünün belirli sürümlerinde kullanılabilir.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385056"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Azure CDN uç noktasında varlıkları önceden yükleme
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Varsayılan olarak, varlıklar yalnızca istendiğinde önbelleğe alınır. Uç sunucular içeriği henüz önbelleğe alınmamış ve isteği kaynak sunucuya ilettiğinden, her bölgeden gelen ilk istek sonraki isteklerden daha uzun sürebilir. Bu ilk isabet gecikmesini önlemek için varlıklarınızı önceden yükleyin. Daha iyi bir müşteri deneyimi sunmanın yanı sıra, önbelleğe alınmış varlıklarınızın ön yüklemesi, kaynak sunucudaki ağ trafiğini azaltabilir.

> [!NOTE]
> Önceden yükleme varlıkları, yeni bir film sürümü veya yazılım güncelleştirmesi gibi birçok kullanıcı için aynı anda kullanılabilecek büyük olaylar veya içerikler için yararlıdır.
> 
> 

Bu öğretici, tüm Azure CDN Edge düğümlerinde önbelleğe alınmış içeriği önceden yükleme işleminde size kılavuzluk eder.

## <a name="to-pre-load-assets"></a>Varlıkları önceden yüklemek için
1. [Azure Portal](https://portal.azure.com), önceden yüklemek istediğiniz uç NOKTAYı içeren CDN profiline gidin. Profil bölmesi açılır.
    
2. Listedeki uç noktaya tıklayın. Uç nokta bölmesi açılır.
3. CDN uç noktası bölmesinden **Yükle**' yi seçin.
   
    ![CDN uç noktası bölmesi](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Yükleme** bölmesi açılır.
   
    ![CDN yükleme bölmesi](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. **İçerik yolu** için, yüklemek istediğiniz her varlığın tam yolunu girin (örneğin, `/pictures/kitten.png` ).
   
   > [!TIP]
   > Metin girmeyi başlattıktan sonra, birden fazla varlık listesi oluşturmanıza izin veren daha fazla **içerik yolu** metin kutusu görünür. Listeden varlıkları silmek için üç nokta (...) düğmesini seçin ve **Sil**' i seçin.
   > 
   > Her içerik yolu, aşağıdaki [normal ifadelere](/dotnet/standard/base-types/regular-expression-language-quick-reference)uyan GÖRELI bir URL olmalıdır:  
   > - Tek bir dosya yolu yükleyin: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Sorgu dizesiyle tek bir dosya yükle: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Her varlığın kendi yolu olması gerektiğinden, önceden yükleme varlıkları için joker karakter işlevselliği yoktur.
   > 
   > 
   
    ![Yükleme düğmesi](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. İçerik yollarını girmeyi tamamladığınızda **Yükle**' yi seçin.
   

> [!NOTE]
> CDN profili başına dakika başına 10 yükleme isteği ve 50 eşzamanlı yolun tek seferde işlenebilmesi için bir sınır vardır. Her yol 1024 karakterlik bir yol uzunluğu sınırına sahiptir.
> 
> 

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN uç noktasını Temizleme](cdn-purge-endpoint.md)
* [Azure CDN REST API başvurusu: bir uç noktada içerik önceden yükleme](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN REST API başvurusu: bir uç noktadan içerik Temizleme](/rest/api/cdn/cdn/endpoints/purgecontent)