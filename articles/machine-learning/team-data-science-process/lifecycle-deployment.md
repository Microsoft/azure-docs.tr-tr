---
title: Team Data Science Işlem yaşam döngüsünün dağıtım aşaması
description: Veri bilimi projelerinizin dağıtım aşamasına yönelik hedefler, görevler ve teslim edilebilirler
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 156f2e95913dffd88de0b4669b13a5af5fc9605c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684687"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Işlem yaşam döngüsünün dağıtım aşaması

Bu makalede, ekip veri bilimi Işleminin (TDSP) dağıtımıyla ilişkili hedefler, görevler ve teslim edilebilirler özetlenmektedir. Bu işlem, veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

   1. **İş anlama**
   2. **Veri edinme ve anlama**
   3. **Oluşturmanın**
   4. **Dağıtım**
   5. **Müşteri kabulü**

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Hedef
Son Kullanıcı kabulü için bir üretime veya üretime benzer bir ortama bir veri işlem hattı ile modeller dağıtın. 

## <a name="how-to-do-it"></a>Nasıl yapılır?
Bu aşamada belirtilen ana görev:

**Modeli**çalıştırın: model ve işlem hattını, uygulama tüketimi için bir üretime veya üretime benzer bir ortama dağıtın.

### <a name="operationalize-a-model"></a>Bir modeli kullanıma hazır hale getirme
İyi hale getirilen bir modeller kümesi olduktan sonra, bunları kullanmak için diğer uygulamalar için çalıştırabilirsiniz. İş gereksinimlerine bağlı olarak, tahminler gerçek zamanlı olarak veya bir toplu iş temelinde yapılır. Modelleri dağıtmak için, bunları açık bir API arabirimiyle kullanıma sunun. Arabirim, modelin çeşitli uygulamalardan kolayca tüketilmesine olanak sağlar; örneğin:

   * Çevrimiçi Web siteleri
   * Tablolarınızı 
   * Panolar
   * İş kolu uygulamaları 
   * Arka uç uygulamaları 

Azure Machine Learning Web hizmeti ile model işlemleştirme örnekleri için bkz. [Azure Machine Learning Web hizmeti dağıtma](../studio/deploy-a-machine-learning-web-service.md). Üretim modeli ve dağıttığınız veri işlem hattı için telemetri ve izleme oluşturmak en iyi uygulamadır. Bu uygulama, sonraki sistem durumu raporlama ve sorun giderme konusunda yardımcı olur.  

## <a name="artifacts"></a>Yapıtlar

* Sistem durumunu ve anahtar ölçümlerini görüntüleyen bir durum panosu
* Dağıtım ayrıntıları içeren son modelleme raporu
* Son Çözüm mimarisi belgesi


## <a name="next-steps"></a>Sonraki adımlar

TDSP yaşam döngüsüyle her adımın bağlantıları aşağıda verilmiştir:

   1. [İş anlama](lifecycle-business-understanding.md)
   2. [Veri alma ve anlama](lifecycle-data.md)
   3. [Oluşturmanın](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

Belirli senaryolar için işlemdeki tüm adımları gösteren eksiksiz bir uçtan uca izlenecek yol sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 

Azure Machine Learning Studio kullanan TDSPs 'de adımların nasıl yürütüleceği hakkında örnekler için, bkz. [Azure Machine Learning Ile TDSP kullanma](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
