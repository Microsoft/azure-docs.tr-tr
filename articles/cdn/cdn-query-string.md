---
title: Sorgu dizeleri ile denetim Azure CDN önbelleğe alma davranışı-Standart katman
description: Azure CDN sorgu dizesi önbelleğe alma, bir Web isteği sorgu dizesi içerdiğinde dosyaların nasıl önbelleğe alınacağını denetler. Bu makalede, Azure CDN Standart ürünlerde sorgu dizesi önbelleğe alma açıklanmaktadır.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083038"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Sorgu dizeleri ile denetim Azure CDN önbelleğe alma davranışı-Standart katman
> [!div class="op_single_selector"]
> * [Standart katman](cdn-query-string.md)
> * [Premium katman](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Genel Bakış
Azure Content Delivery Network (CDN) ile, bir sorgu dizesi içeren bir Web isteği için dosyaların nasıl önbelleğe alınacağını kontrol edebilirsiniz. Sorgu dizesi olan bir Web isteğinde, sorgu dizesi, isteğin bir soru işareti (?) sonrasında gerçekleşen bölümüdür. Sorgu dizesi bir veya daha fazla anahtar-değer çifti içerebilir; burada alan adı ve değeri bir eşittir işareti (=) ile ayrılmıştır. Her anahtar-değer çifti bir ve işareti (&) ile ayrılır. Örneğin, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Bir isteğin sorgu dizesinde birden fazla anahtar-değer çifti varsa, bunların sırası önemli değildir. 

> [!IMPORTANT]
> Azure CDN Standart ve Premium ürünleri aynı sorgu dizesi önbelleğe alma işlevselliğine sahiptir, ancak kullanıcı arabirimi farklıdır. Bu makalede Azure CDN, **Microsoft 'tan Azure CDN Standard**, Akamai 'ten **Standart** ve **Verizon 'den Azure CDN**standart için arabirim açıklanmaktadır. **Verizon ' den Azure CDN Premium**ile sorgu dizesi önbelleği için bkz. [sorgu dizeleri ile denetim Azure CDN önbelleğe alma davranışı-Premium katman](cdn-query-string-premium.md).

Üç sorgu dizesi modu kullanılabilir:

- **Sorgu dizelerini yoksay**: varsayılan mod. Bu modda, CDN varlık noktası (POP) düğümü, Sorgu dizelerini istek sahibine ilk istekteki kaynak sunucuya geçirir ve varlığı önbelleğe alır. POP 'tan sunulan varlık için sonraki tüm istekler, önbelleğe alınmış varlık sona erene kadar Sorgu dizelerini yoksayar.

- **Sorgu dizeleri için önbelleğe almayı atla**: Bu modda sorgu dizelerine sahıp ISTEKLER CDN pop düğümünde önbelleğe alınmaz. POP düğümü, varlığı doğrudan kaynak sunucudan alır ve her istekte istek sahibine geçirir.

- **Her benzersiz URL 'Yi önbelleğe al**: Bu modda, sorgu dizesi dahil olmak üzere benzersiz bir URL 'si olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir. Örneğin, örneğin. ashx? q = test1 bir isteğin kaynak sunucudan gelen yanıtı POP düğümünde önbelleğe alınır ve aynı sorgu dizesine sahip sonraki önbellekler için döndürülür. Örneğin. ashx? q = test2 isteği kendi yaşam süresi ayarıyla ayrı bir varlık olarak önbelleğe alınır.
   
    >[!IMPORTANT] 
    > Sorgu dizesi, bir oturum KIMLIĞI veya Kullanıcı adı gibi her bir istekle değiştirecek parametreler içerdiğinde bu modu, düşük bir önbellek isabet oranına neden olacağı sürece kullanmayın.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Standart CDN profillerinin sorgu dizesi önbelleğe alma ayarlarını değiştirme
1. Bir CDN profili açın ve ardından yönetmek istediğiniz CDN uç noktasını seçin.
   
   ![CDN profili uç noktaları](./media/cdn-query-string/cdn-endpoints.png)
   
2. Sol bölmede ayarlar ' ın altında **önbelleğe alma kuralları**' na tıklayın.
   
    ![CDN Önbelleğe alma kuralları düğmesi](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. **Sorgu dizesi önbellek davranışı** listesinde bir sorgu dizesi modu seçin, sonra **Kaydet**' e tıklayın.
   
   ![CDN sorgu dizesi önbelleğe alma seçenekleri](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Kaydın Azure CDN üzerinden yayılması zaman alacağından, önbellek dizesi ayarları değişiklikleri hemen görünür olmayabilir:
> - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
> - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
> - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır. 



