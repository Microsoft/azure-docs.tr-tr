---
title: Sınırlar ve Kotalar-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Bu makalede farklı lisans anahtarı türleri ve Özel Görüntü İşleme Hizmeti için sınırlar ve Kotalar açıklanmaktadır.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 3392cc5f3ee9daef1ae8397f6829f4ca7a42373a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98871409"
---
# <a name="limits-and-quotas"></a>Limitler ve kotalar

Özel Görüntü İşleme Hizmeti için iki anahtar katmanı vardır. Azure portal aracılığıyla bir F0 (ücretsiz) veya S0 (Standart) aboneliğine kaydolabilirsiniz. Fiyatlandırma ve işlemler hakkındaki ayrıntılar için ilgili bilişsel [Hizmetler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) bakın.

Her proje için bir proje ve etiket için eğitim görüntülerinin sayısının, S0 projelerinin zaman içinde artması beklenmektedir.

|Faktör|**F0**|**S0**|
|-----|-----|-----|
|Projeler|2|100|
|Proje başına eğitim görüntüleri |5.000|100.000|
|Tahmin/ay|10,000 |Sınırsız|
|Etiketler/proje|50|500|
|Yinelemeler |10|10|
|Etiket başına en az etiketlenmiş görüntü, sınıflandırma (50 + önerilir) |5|5|
|Etiket başına en az etiketlenmiş görüntü, nesne algılama (50 + önerilir)|15|15|
|Tahmin görüntülerinin depolandığı süre|30 gün|30 gün|
|Depolama ile [tahmin](https://go.microsoft.com/fwlink/?linkid=865445) Işlemleri (saniye başına işlem)|2|10|
|Depolama olmadan [tahmin](https://go.microsoft.com/fwlink/?linkid=865445) Işlemleri (saniye başına işlem)|2|20|
|[Traınproject](https://go.microsoft.com/fwlink/?linkid=865446) (saniye başına API çağrısı)|2|10|
|[DIĞER API çağrıları](https://go.microsoft.com/fwlink/?linkid=865446) (saniye başına işlem)|10|10|
|Kabul edilen görüntü türleri|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Piksel cinsinden en az görüntü yüksekliği/genişliği|256 (bkz. nota bakın)|256 (bkz. nota bakın)|
|Piksel cinsinden en fazla görüntü yüksekliği/genişliği|10.240|10.240|
|En fazla görüntü boyutu (eğitim resmi yüklemesi) |6 MB|6 MB|
|En fazla görüntü boyutu (tahmin)|4 MB|4 MB|
|Görüntü başına en fazla bölge sayısı (nesne algılama)|300|300|
|Görüntü başına en fazla etiket sayısı (sınıflandırma)|100|100|

> [!NOTE]
> 256 pikselden küçük resimler kabul edilir ancak ölçeklendirilir.
> Görüntü en boy oranı 25 ' ten büyük olmamalıdır
