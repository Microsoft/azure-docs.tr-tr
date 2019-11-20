---
title: include dosyası
description: include dosyası
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929635"
---
İşlem hattı, çalışma alanınıza eklenmiş bir işlem kaynağı olan işlem hedefi üzerinde çalışır. Bir işlem hedefi oluşturduktan sonra, gelecekteki çalıştırmalar için onu yeniden kullanabilirsiniz.

1. İşlem hattını çalıştırmak için tuvalin en üstünde **Çalıştır** ' ı seçin.

1. **Ayarlar** bölmesi göründüğünde, **işlem hedefini seç**' i seçin.

    Zaten kullanılabilir bir işlem hedefi varsa, bu işlem hattını çalıştırmak için seçin.

    > [!NOTE]
    > Tasarımcı yalnızca Azure Machine Learning Işlem hedeflerinde denemeleri çalıştırabilir. Diğer işlem hedefleri gösterilmez.

1. İşlem kaynağı için bir ad girin.

1. **Kaydet**’i seçin.

    ![Kurulum işlem hedefi](./media/aml-ui-create-training-compute/set-compute.png)

1. **Çalıştır**'ı seçin.

1. İşlem **hattı çalıştırmasını ayarla** iletişim kutusunda, **deneme**için **+ Yeni deneme** ' yı seçin.

    > [!NOTE]
    > Denemeleri Group benzer işlem hattı birlikte çalışır. Birden çok kez işlem hattı çalıştırırsanız, art arda çalıştırmalar için aynı denemeyi seçebilirsiniz.

    1. **Deneme adı**için açıklayıcı bir ad girin.

    1. **Çalıştır**'ı seçin.
    
    Çalışma durumunu ve ayrıntılarını tuvalin sağ üst kısmında görüntüleyebilirsiniz.

    > [!NOTE]
    > İşlem kaynağının oluşturulması yaklaşık beş dakika sürer. Kaynak oluşturulduktan sonra yeniden kullanabilir ve gelecekteki çalıştırmalar için bu bekleme süresini atlayabilirsiniz.
    >
    > Maliyet tasarrufu için boşta kaldığında işlem kaynağı sıfır düğümlere otomatik olarak ölçeklendirilir. Bir gecikmeden sonra yeniden kullandığınızda, yeniden ölçeklendirilirken yaklaşık beş dakikalık bekleme süresi yaşayabilirsiniz.
