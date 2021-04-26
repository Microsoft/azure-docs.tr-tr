---
title: Azure CDN uç noktasını Temizleme | Microsoft Docs
description: Azure Content Delivery Network uç noktasındaki tüm önbelleğe alınmış içeriği temizlemeyi öğrenin. Kenar düğümleri, yaşam süresi sona erene kadar varlıkları önbelleğe alırlar.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: 4fe4b99f9635ff254f1a75e03f13d7e6ffcb3c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366526"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Azure CDN uç noktasını Temizleme
## <a name="overview"></a>Genel Bakış
Azure CDN Edge düğümleri, varlığın yaşam süresi (TTL) sona erene kadar varlıkları önbelleğe alacak.  Varlığın TTL 'SI dolduktan sonra, bir istemci kenar düğümünden varlık istediğinde, uç düğüm, istemci isteğine yönelik yeni bir güncelleştirilmiş kopyasını alır ve mağaza önbelleği yeniler.

Kullanıcılarınızın her zaman varlıklarınızın en son kopyasını elde ettiğinizden emin olmak için en iyi yöntem, varlıklarınızın her bir güncelleştirme için sürüm oluşturup yeni URL 'Ler olarak yayınlanmasına yöneliktir.  CDN, sonraki istemci istekleri için yeni varlıkları hemen alacaktır.  Bazen önbelleğe alınmış içeriği tüm kenar düğümlerinden temizlemek ve bunları yeni güncelleştirilmiş varlıkları almaya zorlamak isteyebilirsiniz.  Bunun nedeni, Web uygulamanızdaki güncelleştirmeler veya hatalı bilgiler içeren varlıkları hızlıca güncelleştirmeniz olabilir.

> [!TIP]
> Temizleme 'nin yalnızca CDN Edge sunucularındaki önbelleğe alınmış içeriği temizlemesinin unutmayın.  Proxy sunucuları ve yerel tarayıcı önbellekleri gibi herhangi bir aşağı akış önbelleği, dosyanın önbelleğe alınmış bir kopyasını hala tutabilir.  Bir dosyanın yaşam zamanını ayarladığınızda bunu unutmamanız önemlidir.  Bir aşağı akış istemcisini, her güncelleştirdiğinizde benzersiz bir ad vererek veya [sorgu dizesi önbelleğe alma](cdn-query-string.md)özelliğinden yararlanarak, dosyanızın en son sürümünü isteyecek şekilde zorlayabilirsiniz.  
> 
> 

Bu öğreticide, bir uç noktanın tüm kenar düğümlerinden varlıkları Temizleme işlemi adım adım gösterilmektedir.

## <a name="walkthrough"></a>Kılavuz
1. [Azure portalında](https://portal.azure.com), temizlemek istediğiniz uç NOKTAYı içeren CDN profiline gidin.
2. CDN profili dikey penceresinde, temizle düğmesine tıklayın.
   
    ![CDN profili dikey penceresi](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Temizleme dikey penceresi açılır.
   
    ![CDN Temizleme dikey penceresi](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Temizleme dikey penceresinde, URL açılır listesinden temizlemek istediğiniz hizmet adresini seçin.
   
    ![Formu Temizle](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Ayrıca, CDN uç noktası dikey penceresindeki **Temizle** düğmesine tıklayarak Temizleme dikey penceresine da ulaşabilirsiniz.  Bu durumda, **URL** alanı söz konusu uç noktanın hizmet adresi ile önceden doldurulur.
   > 
   > 
4. Kenar düğümlerinden hangi varlıkları temizlemek istediğinizi seçin.  Tüm varlıkları temizlemek istiyorsanız **Tümünü Temizle** onay kutusuna tıklayın.  Aksi takdirde, **yol** metin kutusuna temizlemek istediğiniz her varlığın yolunu yazın. Aşağıdaki biçimler yolunda desteklenir.
    1. **Tek URL Temizleme**: dosya uzantısıyla veya olmadan tam URL 'yi belirterek tek tek varlığı temizleyin; Örneğin, `/pictures/strasbourg.png` ; `/pictures/strasbourg`
    2. **Joker karakter Temizleme**: yıldız işareti ( \* ) joker karakter olarak kullanılabilir. Bir uç nokta altında bulunan tüm klasörleri, alt klasörleri ve dosyaları `/*` yolda ve sonra, `/*` Örneğin,,,,,,,,,, ve gibi tüm alt klasörleri ve dosyaları belirtilen bir klasörde temizleyin `/pictures/*` .  Joker karakter Temizleme 'nin şu anda Akamai 'tan Azure CDN desteklenmediğini unutmayın. 
    3. **Kök etki alanı temizleme**: yoldaki bitiş noktasının kökünü "/" ile temizleyin.
   
   > [!TIP]
   > Temizleme için yolların belirtilmesi gerekir ve aşağıdaki [normal ifadeye](/dotnet/standard/base-types/regular-expression-language-quick-reference)uyan GÖRELI bir URL olmalıdır. **Akamai 'dan Azure CDN** tarafından desteklenmeyen, **Tümünü Temizle** ve **joker karakter Temizleme** işlemi şu anda desteklenmiyor.
   > > Tek URL Temizleme `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Sorgu dizesi `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Joker karakter Temizleme `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Birden fazla varlık listesi oluşturmanıza olanak tanımak için metin girdikten sonra daha fazla **yol** metin kutuları görüntülenecektir.  Üç nokta (...) düğmesine tıklayarak varlıkları listeden silebilirsiniz.
   > 
5. **Temizle** düğmesine tıklayın.
   
    ![Temizle düğmesi](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Temizleme isteklerinin, **Microsoft 'tan Azure CDN**, yaklaşık 2 dakika **Azure CDN** (Standart ve Premium) ve **Akamai 'den Azure CDN** yaklaşık 10 saniye içinde işlenmesi yaklaşık 10 dakika sürer.  Azure CDN, profil düzeyinde belirli bir zamanda 100 eşzamanlı temizleme isteği sınırı vardır. 
> 
> 

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN uç noktasında varlıkları önceden yükleme](cdn-preload-endpoint.md)
* [Azure CDN REST API başvurusu-bir uç noktayı Temizleme veya önceden yükleme](/rest/api/cdn/cdn/endpoints)

