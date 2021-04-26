---
title: Team Data Science Işlem yaşam döngüsünün müşteri kabul aşaması
description: Veri bilimi projelerinizin müşteri kabul aşamasına yönelik hedefler, görevler ve teslim edilebilirler
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f2294ccb1d958b229a71e45bb502b8134d8d5c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305665"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Işlem yaşam döngüsünün müşteri kabul aşaması

Bu makalede, Team Data Science Işleminin (TDSP) müşteri kabul aşamasına ilişkin hedefler, görevler ve teslim edilebilirler özetlenmektedir. Bu işlem, veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

   1. **Kurumsal yaklaşım**
   2. **Veri edinme ve anlama**
   3. **Modelleme**
   4. **Dağıtım**
   5. **Müşteri kabulü**

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Hedef
**Proje teslim edilebilirleri Sonlandır**: işlem hattının, modelin ve bir üretim ortamındaki dağıtımının müşterinin hedeflerini karşıladığından emin olun.

## <a name="how-to-do-it"></a>Nasıl yapılır?
Bu aşamada belirtilen iki ana görev vardır:

   * **Sistem doğrulaması**: dağıtılan modelin ve işlem hattının müşterinin gereksinimlerini karşıladığını onaylayın.
   * **Proje el ile**: projeyi üretimde çalıştırmaya devam eden varlığa bırakın.

Müşteri, sistemin kendi iş gereksinimlerini karşıladığını ve sistemin, istemci uygulaması tarafından kullanılmak üzere uygulamayı üretime dağıtmak için kabul edilebilir doğrulukla soruların yanıt verdiği doğrulaması gerekir. Tüm belgeler sonlandırılır ve gözden geçirilir. Proje, işlemlerden sorumlu varlığa devredilmiştir. Bu varlık, örneğin, bir BT veya müşteri veri bilimi ekibi ya da müşterinin üretimde sistem çalıştırmasından sorumlu bir aracı olabilir. 

## <a name="artifacts"></a>Artifacts
Bu son aşamada üretilen ana yapıt, **müşterinin projenin çıkış rapordur**. Bu teknik rapor, sistemin nasıl çalışalınacağını öğrenmek için yararlı olan projenin tüm ayrıntılarını içerir. TDSP bir [Çıkış raporu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) şablonu sağlar. Şablonu olduğu gibi kullanabilir veya belirli istemci ihtiyaçları için özelleştirebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

TDSP yaşam döngüsüyle her adımın bağlantıları aşağıda verilmiştir:

   1. [Kurumsal yaklaşım](lifecycle-business-understanding.md)
   2. [Veri edinme ve anlama](lifecycle-data.md)
   3. [Modelleme](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

Belirli senaryolar için işlemdeki tüm adımları gösteren tam izlenecek yollar sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 

Azure Machine Learning Studio kullanan TDSPs 'de adımların nasıl yürütüleceği hakkında örnekler için, bkz. [Azure Machine Learning Ile TDSP kullanma](./index.yml).