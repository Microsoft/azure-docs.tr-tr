---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657890"
---
Form tanıyıcı, bu gereksinimleri karşılayan giriş belgelerinde çalışmaktadır:

* Biçim JPG, PNG veya PDF olmalıdır (metin veya taranan). Metin katıştırılmış PDF 'Ler en iyisidir çünkü karakter ayıklama ve konumunda hata olasılığı yoktur.
* PDF 'niz parola kilitliyse, göndermeden önce kilidi kaldırmanız gerekir.
* Dosya boyutu 4 MB 'tan az olmalıdır.
* Görüntüler için Boyutlar 600 x 100 piksel ve 4200 x 4200 piksel arasında olmalıdır.
* Kağıt belgelerinden tarandığında, formlar yüksek kaliteli taramalar olmalıdır.
* Metin Latin alfabesini (Ingilizce karakterler) kullanmalıdır.
* Veriler, anahtar ve değer içermelidir.
* Anahtarlar, değerlerin solunda veya solunda görünebilir, ancak aşağıdan veya sağında yer alabilir.

Form tanıyıcı Şu anda bu tür giriş verilerini desteklemez:

* Karmaşık tablolar (iç içe tablolar, birleştirilmiş üstbilgiler veya hücreler vb.).
* Onay kutuları veya radyo düğmeleri.
* 50 sayfadan daha uzun PDF belgeleri.
