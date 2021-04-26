---
title: 'Web hizmeti giriş/çıkış: modül başvurusu'
description: Giriş ve çıkışları yönetmek için Azure Machine Learning tasarımcısında Web hizmeti modüllerini nasıl kullanacağınızı öğrenin.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421133"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Web hizmeti giriş ve Web hizmeti çıkış modülleri

Bu makalede, Azure Machine Learning tasarımcısında Web hizmeti giriş ve Web hizmeti çıkış modülleri açıklanmaktadır.

Web hizmeti giriş modülü yalnızca **Dataframedirectory** türünde bir giriş bağlantı noktasına bağlanabilir. Web hizmeti çıkış modülü yalnızca **Dataframedirectory** türünde bir çıkış bağlantı noktasından bağlanabilir. Modül ağacında, **Web hizmeti** kategorisi altında iki modülü bulabilirsiniz. 

Web hizmeti giriş modülü, Kullanıcı verilerinin işlem hattına girdiği yeri gösterir. Web hizmeti çıkış modülü, Kullanıcı verilerinin gerçek zamanlı bir çıkarım ardışık düzeninde döndürüldüğü yeri gösterir.

## <a name="how-to-use-web-service-input-and-output"></a>Web hizmeti girişi ve çıkışı nasıl kullanılır

Eğitim işlem hattınızdan [gerçek zamanlı bir çıkarım işlem hattı oluşturduğunuzda](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) , Web hizmeti giriş ve Web hizmeti çıkış modülleri, Kullanıcı verilerinin ardışık düzene girdiği ve verilerin döndürüldüğü yeri göstermek için otomatik olarak eklenir. 

> [!NOTE]
> Gerçek zamanlı çıkarım işlem hattını otomatik olarak oluşturmak, kural tabanlı ve en iyi çaba bir işlemdir. Doğruluk garantisi yoktur. 

Gereksinimlerinizi karşılamak için Web hizmeti giriş ve Web hizmeti çıkış modüllerini el ile ekleyebilir veya kaldırabilirsiniz. Gerçek zamanlı çıkarım işlem hattının en az bir Web hizmeti giriş modülüne ve bir Web hizmeti çıkış modülüne sahip olduğundan emin olun. Birden çok Web hizmeti girişi veya Web hizmeti çıkış modülünüz varsa, benzersiz adlara sahip olduklarından emin olun. Modülün sağ paneline adını girebilirsiniz.

Ayrıca, gönderilmemiş işlem hattınızı Web hizmeti girişi ve Web hizmeti çıkış modüllerini ekleyerek el ile gerçek zamanlı bir çıkarım işlem hattı oluşturabilirsiniz.

> [!NOTE]
> İşlem hattı türü ilk kez gönderdiğinizde belirlenir. İlk kez göndermeden önce Web hizmeti giriş ve Web hizmeti çıkış modüllerini eklediğinizden emin olun.

Aşağıdaki örnek, Python betik modülünden nasıl el ile gerçek zamanlı çıkarım işlem hattı oluşturulacağını göstermektedir. 

![Örnek](media/module/web-service-input-output-example.png)
   
İşlem hattını gönderdikten ve çalıştırma başarılı bir şekilde tamamlandıktan sonra gerçek zamanlı uç noktayı dağıtabilirsiniz.
   
> [!NOTE]
>  Yukarıdaki örnekte, **verileri el ile** Web hizmeti girişi için veri şeması sağlar ve gerçek zamanlı uç noktanın dağıtımı için gereklidir. Genellikle, veri şemasını sağlamak üzere **Web hizmeti girişinin** bağlandığı bağlantı noktasına her zaman bir modül veya veri kümesi bağlamanız gerekir.
   
## <a name="next-steps"></a>Sonraki adımlar
[Gerçek zamanlı uç noktayı dağıtma](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint)hakkında daha fazla bilgi edinin.

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.