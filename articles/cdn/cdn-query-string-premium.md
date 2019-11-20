---
title: Sorgu dizeleri ile denetim Azure CDN önbelleğe alma davranışı-Premium katman
description: Azure CDN sorgu dizesi önbelleğe alma, bir Web isteği sorgu dizesi içerdiğinde dosyaların nasıl önbelleğe alınacağını denetler. Bu makalede, Verizon ürününden Azure CDN Premium 'da sorgu dizesi önbelleğe alma açıklanır.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 365c52840d281c0f48d17aacc358e4cce513e3b4
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083086"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Sorgu dizeleri ile denetim Azure CDN önbelleğe alma davranışı-Premium katman
> [!div class="op_single_selector"]
> * [Standart katman](cdn-query-string.md)
> * [Premium katman](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Genel Bakış
Azure Content Delivery Network (CDN) ile, bir sorgu dizesi içeren bir Web isteği için dosyaların nasıl önbelleğe alınacağını kontrol edebilirsiniz. Sorgu dizesi olan bir Web isteğinde, sorgu dizesi, isteğin bir soru işareti (?) sonrasında gerçekleşen bölümüdür. Sorgu dizesi bir veya daha fazla anahtar-değer çifti içerebilir; burada alan adı ve değeri bir eşittir işareti (=) ile ayrılmıştır. Her anahtar-değer çifti bir ve işareti (&) ile ayrılır. Örneğin, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Bir isteğin sorgu dizesinde birden fazla anahtar-değer çifti varsa, bunların sırası önemli değildir. 

> [!IMPORTANT]
> Standart ve Premium CDN ürünleri aynı sorgu dizesi önbelleğe alma işlevselliğine sahiptir, ancak kullanıcı arabirimi farklıdır. Bu makalede, **Verizon ' den Premium Azure CDN**için arabirim açıklanmaktadır. Azure CDN Standart ürünlerle sorgu dizesi önbelleğe alma için bkz. [sorgu dizeleri ile standart katman Ile denetim Azure CDN önbelleğe alma davranışı](cdn-query-string.md).
>


Üç sorgu dizesi modu kullanılabilir:

- **Standart önbellek**: varsayılan mod. Bu modda, CDN varlık noktası (POP) düğümü, Sorgu dizelerini istek sahibine ilk istekteki kaynak sunucuya geçirir ve varlığı önbelleğe alır. POP sunucusundan sunulan varlık için sonraki tüm istekler, önbelleğe alınmış varlık sona erene kadar Sorgu dizelerini yoksayar.

    >[!IMPORTANT] 
    > Bu hesaptaki herhangi bir yol için belirteç yetkilendirmesi etkinleştirilirse, standart önbellek modu kullanılabilecek tek moddur. 

- **No-Cache**: Bu modda sorgu dizelerine sahıp ISTEKLER CDN pop düğümünde önbelleğe alınmaz. POP düğümü, varlığı doğrudan kaynak sunucudan alır ve her istekte istek sahibine geçirir.

- **benzersiz önbellek**: Bu modda, sorgu dizesi dahil olmak üzere benzersiz bir URL 'si olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir. Örneğin, örneğin. ashx? q = test1 bir isteğin kaynak sunucudan gelen yanıtı POP düğümünde önbelleğe alınır ve aynı sorgu dizesine sahip sonraki önbellekler için döndürülür. Örneğin. ashx? q = test2 isteği kendi yaşam süresi ayarıyla ayrı bir varlık olarak önbelleğe alınır.
   
    >[!IMPORTANT] 
    > Sorgu dizesi, bir oturum KIMLIĞI veya Kullanıcı adı gibi her bir istekle değiştirecek parametreler içerdiğinde bu modu, düşük bir önbellek isabet oranına neden olacağı sürece kullanmayın.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN profillerinin sorgu dizesi önbelleğe alma ayarlarını değiştirme
1. Bir CDN profili açın ve ardından **Yönet**' e tıklayın.
   
    ![CDN profili Yönet düğmesi](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN yönetim portalı açılır.
2. **Http büyük** sekmesinin üzerine gelin ve ardından **önbellek ayarları** açılır menüsünün üzerine gelin. **Sorgu dizesi önbelleğe alma**' ya tıklayın.
   
    Sorgu dizesi önbelleğe alma seçenekleri görüntülenir.
   
    ![CDN sorgu dizesi önbelleğe alma seçenekleri](./media/cdn-query-string-premium/cdn-query-string.png)
3. Bir sorgu dizesi modu seçin ve ardından **Güncelleştir**' e tıklayın.

> [!IMPORTANT]
> Kaydın CDN aracılığıyla yayılması zaman alacağından, önbellek dizesi ayarları değişiklikleri hemen görünür olmayabilir. Yayma genellikle 10 dakika içinde tamamlanır.
 

