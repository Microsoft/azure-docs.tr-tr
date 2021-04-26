---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94425248"
---
Konuşma hizmetinin temel özelliklerinden biri de insan konuşmanızı algılayabilir ve diğer dillere çevirebilir. Bu hızlı başlangıçta, uygulama ve ürünlarınızdaki konuşma SDK 'sını kullanarak yüksek kaliteli konuşma çevirisi gerçekleştirme hakkında bilgi edinebilirsiniz. Bu hızlı başlangıç, konuşmayı mikrofondan başka bir dilde metne dönüştürür.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Kaynak ve hedef dil ayarla

Bu komut, konuşmayı Italyan 'dan Fransızca 'ya dönüştürmek için konuşma CLı 'Yı çağırır.

```shell
 spx translate --microphone --source it-IT --target fr
```