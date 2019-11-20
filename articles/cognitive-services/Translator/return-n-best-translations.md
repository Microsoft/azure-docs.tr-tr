---
title: N-En Iyi çevirileri Döndür-Translator Metin Çevirisi
titleSuffix: Azure Cognitive Services
description: Translator Metin Çevirisi API'si kullanarak N-En Iyi çevirileri döndürün.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837252"
---
# <a name="how-to-return-n-best-translations"></a>N-En Iyi çevirileri döndürme

> [!NOTE]
> Bu yöntem kullanım dışıdır. Translator Metin Çevirisi API'si V 3.0 sürümünde kullanılamaz.

Microsoft Translator API 'sinin Getçeviriler () ve GetTranslationsArray () yöntemleri, "ıncludemultipkıtalternatifler" isteğe bağlı Boole bayrağını içerir.
Yöntemi, çevirmen altyapısının N En Iyi listesinden Delta 'un sağlandığı en fazla Maxçevirilerin alternatifleri döndürür.

İmza:

**Sözdizimi**

| C# |
|:---|
| GetTranslationsResponse Microsoft. Translator. Getçevirileri (AppID, metin, from, to, Maxçeviriler, Seçenekler); |

**Parametreler**

| Parametre | Açıklama |
|:---|:---|
| appId | **Gerekli** Yetkilendirme üst bilgisi kullanılırsa, AppID alanını boş bırakın başka bir "taşıyıcı" + "" + erişim belirteci içeren bir dize belirtin.|
| metin | **Gerekli** Çevrilecek metni temsil eden bir dize. Metnin boyutu 10000 karakteri aşmamalıdır.|
| Kaynak | **Gerekli** Çevrilecek metnin dil kodunu temsil eden bir dize. |
| - | **Gerekli** Metnin çevrilmesi için dil kodunu temsil eden bir dize. |
| Maxçeviriler | **Gerekli** Döndürülecek en fazla çeviri sayısını temsil eden bir int. |
| Seçenekler | **Isteğe bağlı** Aşağıda listelenen değerleri içeren bir TranslateOptions nesnesi. Bunların tümü isteğe bağlıdır ve varsayılan olarak en yaygın ayarlara ayarlanır.

* Kategori: desteklenen tek ve varsayılan, "genel" seçeneğidir.
* ContentType: desteklenen tek ve varsayılan, "metin/düz" seçeneğidir.
* Durum: istek ve yanıtın bağıntılı olması için Kullanıcı durumu. Yanıtta aynı içerik döndürülecek.
* Includemultiplimon Talternatifleri: MT altyapısından birden fazla alternatifin döndürülüp döndürülmeyeceğini belirleme bayrağı. Varsayılan değer false 'dur ve yalnızca 1 alternatif içerir.

## <a name="ratings"></a>Lendir
Derecelendirmeler aşağıdaki gibi uygulanır: en iyi otomatik çeviri 5 derecelendirmesine sahiptir.
Otomatik olarak oluşturulan (N-En Iyi) çeviri alternatifleri 0 derecelendirmesine sahiptir ve 100 eşleşme derecesine sahiptir.

## <a name="number-of-alternatives"></a>Alternatifin sayısı
Döndürülen alternatiflere göre Maxçevirilerin sayısı, ancak daha az olabilir.

## <a name="language-pairs"></a>Dil çiftleri
Bu işlev, Basitleştirilmiş ve geleneksel Çince arasında her iki yönde de çeviri için kullanılamaz. Diğer tüm Microsoft Translator desteklenen dil çiftleri için kullanılabilir.
