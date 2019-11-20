---
title: Sınırlar ve Kotalar-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Bu makalede farklı lisans anahtarı türleri ve Özel Görüntü İşleme Hizmeti için sınırlar ve Kotalar açıklanmaktadır.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: e4fc3555d7d880dcbd4f76d8f507300e98c237db
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718354"
---
# <a name="limits-and-quotas"></a>Limitler ve kotalar

Özel Görüntü İşleme Hizmeti için iki anahtar katmanı vardır. Azure portal aracılığıyla bir F0 (ücretsiz) veya S0 (Standart) aboneliğine kaydolabilirsiniz. Fiyatlandırma ve işlemler hakkındaki ayrıntılar için ilgili bilişsel [Hizmetler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) bakın.

Her proje için bir proje ve etiket için eğitim görüntülerinin sayısının, S0 projelerinin zaman içinde artması beklenmektedir.

||**F0**|**S0**|
|-----|-----|-----|
|projeyle|2|100|
|Proje başına eğitim görüntüleri |5\.000|100.000|
|Tahmin/ay|10,000 |Sınırsız|
|Etiketler/proje|50|500|
|Tekrarları |10|10|
|Etiket başına en az etiketlenmiş görüntü, sınıflandırma (50 + önerilir) |5|5|
|Etiket başına en az etiketlenmiş görüntü, nesne algılama (50 + önerilir)|15|15|
|Tahmin görüntülerinin depolandığı süre|30 gün|30 gün|
|Depolama ile [tahmin](https://go.microsoft.com/fwlink/?linkid=865445) Işlemleri (saniye başına işlem)|2|10|
|Depolama olmadan [tahmin](https://go.microsoft.com/fwlink/?linkid=865445) Işlemleri (saniye başına işlem)|2|20|
|[Traınproject](https://go.microsoft.com/fwlink/?linkid=865446) (saniye başına API çağrısı)|2|10|
|[DIĞER API çağrıları](https://go.microsoft.com/fwlink/?linkid=865446) (saniye başına işlem)|10|10|
|Kabul edilen görüntü türleri|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Piksel cinsinden en az görüntü yüksekliği/genişliği|256 (bkz. nota bakın)|256 (bkz. nota bakın)|
|Piksel cinsinden en fazla görüntü yüksekliği/genişliği|sınırsız|sınırsız|
|En fazla görüntü boyutu (eğitim resmi yüklemesi) |6 MB|6 MB|
|En fazla görüntü boyutu (tahmin)|4 MB|4 MB|
|Nesne algılama eğitim görüntüsü başına en fazla bölge|200|200|
|Sınıflandırma görüntüsü başına en fazla etiket|30|30|

> [!NOTE]
> 256 pikselden küçük resimler kabul edilir ancak ölçeklendirilir.
