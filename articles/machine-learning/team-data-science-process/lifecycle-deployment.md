---
title: Team Data Science Işlem yaşam döngüsünün dağıtım aşaması
description: Veri bilimi projelerinizin dağıtım aşamasına yönelik hedefler, görevler ve teslim edilebilirler
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
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324545"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Işlem yaşam döngüsünün dağıtım aşaması

Bu makalede, ekip veri bilimi Işleminin (TDSP) dağıtımıyla ilişkili hedefler, görevler ve teslim edilebilirler özetlenmektedir. Bu işlem, veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

   1. **Kurumsal yaklaşım**
   2. **Veri edinme ve anlama**
   3. **Modelleme**
   4. **Dağıtım**
   5. **Müşteri kabulü**

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Hedef
Son Kullanıcı kabulü için bir üretime veya üretime benzer bir ortama bir veri işlem hattı ile modeller dağıtın. 

## <a name="how-to-do-it"></a>Nasıl yapılır?
Bu aşamada belirtilen ana görev:

**Modeli** çalıştırın: model ve işlem hattını, uygulama tüketimi için bir üretime veya üretime benzer bir ortama dağıtın.

### <a name="operationalize-a-model"></a>Bir modeli kullanıma hazır hale getirme
İyi hale getirilen bir modeller kümesi olduktan sonra, bunları kullanmak için diğer uygulamalar için çalıştırabilirsiniz. İş gereksinimlerine bağlı olarak, tahminler gerçek zamanlı olarak veya bir toplu iş temelinde yapılır. Modelleri dağıtmak için, bunları açık bir API arabirimiyle kullanıma sunun. Arabirim, modelin çeşitli uygulamalardan kolayca tüketilmesine olanak sağlar; örneğin:

   * Çevrimiçi Web siteleri
   * Elektronik tablolar 
   * Panolar
   * İş kolu uygulamaları 
   * Arka uç uygulamaları 

Azure Machine Learning Web hizmeti ile model işlemleştirme örnekleri için bkz. [Azure Machine Learning Web hizmeti dağıtma](../classic/deploy-a-machine-learning-web-service.md). Üretim modeli ve dağıttığınız veri işlem hattı için telemetri ve izleme oluşturmak en iyi uygulamadır. Bu uygulama, sonraki sistem durumu raporlama ve sorun giderme konusunda yardımcı olur.  

## <a name="artifacts"></a>Artifacts

* Sistem durumunu ve anahtar ölçümlerini görüntüleyen bir durum panosu
* Dağıtım ayrıntıları içeren son modelleme raporu
* Son Çözüm mimarisi belgesi


## <a name="next-steps"></a>Sonraki adımlar

TDSP yaşam döngüsüyle her adımın bağlantıları aşağıda verilmiştir:

   1. [Kurumsal yaklaşım](lifecycle-business-understanding.md)
   2. [Veri alma ve anlama](lifecycle-data.md)
   3. [Modelleme](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

Belirli senaryolar için işlemdeki tüm adımları gösteren tam izlenecek yollar sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 

Azure Machine Learning Studio kullanan TDSPs 'de adımların nasıl yürütüleceği hakkında örnekler için, bkz. [Azure Machine Learning Ile TDSP kullanma](./index.yml).