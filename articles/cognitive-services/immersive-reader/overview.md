---
title: Tam ekran okuyucu API nedir?
titleSuffix: Azure Cognitive Services
description: Modern okuyucu API 'SI, kişileri öğrenme farklılığı veya yeni okuyucular ve dil öğrenimine yardımcı olmak için kullanılabilen bir araçtır.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3e6889cd5d26b88cedd6b110510cb89777252f54
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904376"
---
# <a name="what-is-immersive-reader"></a>Tam Ekran Okuyucu nedir?

[Modern okuyucu](https://www.onenote.com/learningtools) , gelişmekte olan okuyucular, dil öğrenimi ve öğrenimi gibi öğrenme farklılığı olan kişiler için okuma kavramasını geliştiren, benzer şekilde tasarlanmış bir araçtır.

Tam Ekran Okuyucu SDK’sını kullanarak web uygulamanızda Tam Ekran Okuyucu’yu kullanabilirsiniz.

## <a name="what-does-immersive-reader-do"></a>Modern okuyucu ne yapar?

Derinlikli okuyucu herkes için daha fazla erişilebilir hale getirmek üzere tasarlanmıştır.

* En küçük okuma görünümündeki içeriği gösterir

  ![Tam Ekran Okuyucu](./media/immersive-reader.png)

* Yaygın olarak kullanılan sözcüklerin resimlerini görüntüler

  ![Resim sözlüğü](./media/picture-dictionary.png)

* İsimler, fiiller, sıfatlar ve zarflara önemli noktalar

  ![Konuşma bölümleri](./media/parts-of-speech.png)

* İçeriğinizi sizin için sesli okur

  ![Sesli oku](./media/read-aloud.png)

* İçeriğinizi başka bir dile çevirir

  ![Çeviri](./media/translation.png)

* Sözcükleri hecelere ayırır

  ![Heceye birleşme](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Modern okuyucu nasıl çalışır?

Tam ekran okuyucu, modern okuyucu JavaScript SDK 'Sı kullanılarak çağrıldığında, mevcut Web uygulamanızın en üstünde bir `iframe`ile görüntülendiğinde tek başına bir Web uygulamasıdır. Modern okuyucuyu başlatmak için API 'YI çağırdığınızda, tam ekran okuyucusunda göstermek istediğiniz içeriği belirtin. SDK, `iframe` oluşturma ve stillendirme, okuma ve metin okuma, çeviri vb. için içeriği işleyen, derinlikli okuyucu arka uç hizmetiyle iletişim kurmasından de ilgilenir.

## <a name="next-steps"></a>Sonraki adımlar

Tam Ekran Okuyucu’yu kullanmaya başlama:

* [Hızlı](./quickstart.md) başlangıca atlayın
* [GitHub 'Da modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) keşfet
* [Tam ekran okuyucu SDK başvurusunu](./reference.md) okuyun