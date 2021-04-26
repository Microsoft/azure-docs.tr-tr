---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518237"
---
Form tanıyıcı, bu gereksinimleri karşılayan giriş belgelerinde çalışmaktadır:

* Biçim JPG, PNG, PDF (metin veya taranan) veya TIFF olmalıdır. Metin katıştırılmış PDF 'Ler en iyisidir çünkü karakter ayıklama ve konumunda hata olasılığı yoktur.
* Dosya boyutu 50 MB 'tan az olmalıdır.
* Resim boyutları 50 x 50 piksel ve 10000 x 10000 piksel arasında olmalıdır.
* PDF boyutları, yasal veya A3 kağıt boyutlarına ve daha küçük boyutlara karşılık gelen en fazla 17 x 17 inç olmalıdır.
* PDF ve TIFF için yalnızca ilk 200 sayfa işlenir (ücretsiz bir katman aboneliğiyle yalnızca ilk iki sayfa işlenir).
* Eğitim veri kümesinin toplam boyutu 500 sayfa veya daha az olmalıdır.
* PDF 'niz parola kilitliyse, göndermeden önce kilidi kaldırmanız gerekir.
* Kağıt belgelerinden tarandığında, formlar yüksek kaliteli taramalar olmalıdır.
* Denetimsiz öğrenme için (etiketlenmemiş veriler olmadan), verilerin anahtar ve değer içermesi gerekir.
* Denetimsiz öğrenme (etiketli veriler olmadan) için, anahtarların üzerinde veya solunda bir değer görünmelidir; Bunlar sağ tarafta veya doğru görünemez.
