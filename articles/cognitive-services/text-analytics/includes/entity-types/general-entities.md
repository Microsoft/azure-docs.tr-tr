---
title: Genel adlandırılmış varlıklar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800204"
---
## <a name="general-entity-types"></a>Genel varlık türleri:

### <a name="person"></a>Kişi
Metindeki bilinen adlar ve diğer kişiler.
Diller:
* Genel Önizleme: `English`

| Alt tür adı | Açıklama             |
|--------------|-------------------------|
| Yok          | Tanınan adlar, örneğin `Bill Gates``Marie Curie` |

### <a name="location"></a>Konum

Doğal ve insan tarafından oluşturulan yer işaretleri, yapılar ve coğrafi özellikler.

Diller:


* Genel Önizleme: `English`

| Alt tür adı | Açıklama                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Yok          | konumlar, örneğin `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Kuruluş  

Tanınan kuruluşlar, şirketler, kurumlar ve diğer kişi grupları. Örneğin: şirketler, siyatik gruplar, müzik bantları, spor sinek, kamu gövdeleri ve kamu kuruluşları. Bu varlık türünde ülke almallikleri ve dini dahil değildir. Diller: 

* Genel Önizleme: `English`

| Alt tür adı | Açıklama                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Yok          | kuruluşlar, örneğin `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefon Numarası

Telefon numaraları. 

Diller:


* Genel Önizleme: `English`

| Alt tür adı | Açıklama                                  |
|----------|----------------------------------------------|
| Yok         | Telefon numaraları, örneğin `+1 123-123-123`. |

### <a name="email"></a>Email

E-posta adresi. 

Diller:


* Genel Önizleme: `English`

| Alt tür adı | Açıklama                                  |
|----------|----------------------------------------------|
| Yok         | E-posta adresi, örneğin `support@contoso.com` |

### <a name="url"></a>URL'si

Internet URL 'Leri.

Diller:


* Genel Önizleme: `English`

| Alt tür adı | Açıklama                                           |
|----------|-------------------------------------------------------|
| Yok         | Web sitelerinin URL 'Leri, örneğin `https://www.bing.com`. |

###  <a name="number"></a>Sayı

Sayılar ve sayısal miktarlar. 

Diller:


* Genel Önizleme: `English`

| Alt tür adı    | Örnekler                     |
|-------------|------------------------------|
| Yok         | `6`, `six`                   |
| Yüzde  | `50%`, `fifty percent`       |
| Sıra     | `2nd`, `second`              |
| Para birimi    | `$10.99`, `€30.00`           |
| Boyut   | `10 miles`, `40 cm`          |
| Sıcaklık | `32 degrees`, `10°C`         |
