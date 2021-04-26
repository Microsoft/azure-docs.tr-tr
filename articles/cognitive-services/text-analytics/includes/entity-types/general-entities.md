---
title: Genel adlandırılmış varlıklar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99500225"
---
Metin Analizi NER özelliği, aşağıdaki genel (tanımlayıcı olmayan) varlık kategorilerini döndürür. Örneğin, `/entities/recognition/general` uç noktaya istek gönderirken.


| Kategori | Açıklama                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Kişi](#category-person)     | Kişilerin adları.  |
| [Kişilik türü](#category-persontype) | Bir kişi tarafından tutulan iş türleri veya roller. |
| [Konum](#category-location)    | Doğal ve insan tarafından oluşturulan yer işaretleri, yapılar, coğrafi özellikler ve geopolitik varlıklar |
| [Kuruluş](#category-organization)  | Şirketler, siyatik gruplar, müzik bantları, spor sinek, kamu gövdeleri ve kamu kuruluşları.  |
| [Olay](#category-event)  | Geçmiş, sosyal ve doğal olarak gerçekleşen olaylar. |
| [Ürün](#category-product) | Çeşitli kategorilerin fiziksel nesneleri. |
| [İmde](#category-skill) | Yetenek, beceri veya uzmanlık.  |
| [Adres](#category-address) | Tam posta adresleri.  |
| [Telefon numarası](#category-phonenumber) | Telefon numaraları. |
| [E-posta](#category-email) | E-posta adresleri. |
| [URL](#category-url) | Web sitelerinin URL 'Leri. |
| [IP](#category-ip) | Ağ IP adresleri. |
| [Tarih Saat](#category-datetime) | Günün tarihleri ve saatleri. |
| [Miktar](#category-quantity) | Sayısal ölçümler ve birimler. |


### <a name="category-person"></a>Kategori: kişi

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kişi

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Kişilerin adları.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategori: PersonType

Bu kategori aşağıdaki varlığı içerir:


:::row:::
    :::column span="":::
        **Varlık**

        Kişilik türü

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bir kişi tarafından tutulan iş türleri veya roller
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategori: konum

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Konum

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Doğal ve insan tarafından oluşturulan yer işaretleri, yapılar, coğrafi özellikler ve geopolitik varlıklar.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Geopolitik varlık (GPE)

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Şehir, ülke/bölge, eyalet.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Yapısal

    :::column-end:::
    :::column span="2":::

        Manın yapıları. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coğrafi

    :::column-end:::
    :::column span="2":::

        Rivers, Oceans ve deserler gibi coğrafi ve doğal Özellikler.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategori: kuruluş

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kuruluş

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Şirketler, siyatik gruplar, müzik bantları, spor sinek, kamu gövdeleri ve kamu kuruluşları. Bu varlık türünde ülke almallikleri ve dini dahil değildir.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Birinin

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Tıbbi şirketler ve gruplar.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stok alışverişi

    :::column-end:::
    :::column span="2":::

        Kambiyo senedi grupları. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spor

    :::column-end:::
    :::column span="2":::

        Spor ile ilgili kuruluşlar.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategori: olay

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Olay

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Geçmiş, sosyal ve doğal olarak gerçekleşen olaylar.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es` , `fr` , `de` , `it` , `zh-hans` , `ja` , `ko` `pt-pt` ve `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Kültürel

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Kültürel olaylar ve tatiller.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Olmayan

    :::column-end:::
    :::column span="2":::

        Doğal olarak gerçekleşen olaylar.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spor

    :::column-end:::
    :::column span="2":::

        Spor olayları.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategori: ürün

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Ürün

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Çeşitli kategorilerin fiziksel nesneleri.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Ürünleri hesaplama
    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Ürünleri hesaplama.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategori: yetenek

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        İmde

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Yetenek, beceri veya uzmanlık.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategori: adres

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Adres

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Tam posta adresi.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategori: PhoneNumber

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Telefon numaraları (yalnızca ABD ve AB telefon numaraları).
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategori: e-posta

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        E-posta

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        E-posta adresleri.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategori: URL

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        URL

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Web sitelerinin URL 'Leri. 
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategori: IP

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        IP

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        ağ IP adresleri. 
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategori: DateTime

Bu kategori aşağıdaki varlıkları içerir:

:::row:::
    :::column span="":::
        **Varlık**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Günün tarihleri ve saatleri. 
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Bu kategorideki varlıklar aşağıdaki alt kategorilere sahip olabilir

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Tarih

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Takvim tarihleri.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Saat

    :::column-end:::
    :::column span="2":::

        Günün saati.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Tarih aralıkları.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Zaman aralıkları.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Süre

    :::column-end:::
    :::column span="2":::

        Sürelerde.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ayarla

    :::column-end:::
    :::column span="2":::

        Ayarlama, yinelenme süreleri.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategori: miktar

Bu kategori aşağıdaki varlıkları içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Miktar

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Sayılar ve sayısal miktarlar.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Kategor

Bu kategorideki varlık aşağıdaki alt kategorilere sahip olabilir.

:::row:::
    :::column span="":::
        **Varlık alt kategorisi**

        Sayı

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Sayılarının.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Yüzde

    :::column-end:::
    :::column span="2":::

        Değerleri
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Sıra sayıları

    :::column-end:::
    :::column span="2":::

        Sıra sayıları.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Yaş

    :::column-end:::
    :::column span="2":::

        Geçirir.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Para Birimi

    :::column-end:::
    :::column span="2":::

        Ayarlarsanız
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Boyutlar

    :::column-end:::
    :::column span="2":::

        Boyutlar ve ölçümler.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Sıcaklık

    :::column-end:::
    :::column span="2":::

        Sıcak.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
