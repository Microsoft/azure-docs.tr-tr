---
title: Üretim-Team Data Science Işleminde modelleri dağıtma
description: Modelleri üretime dağıtma iş kararları verirken etkin bir rol oynamalarını sağlar.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6f371d07fdf09aae6ce4396f0916758f8588506b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670298"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>İş kararları verirken etkin bir rol oynatmak için modelleri üretime dağıtın

Üretim dağıtımı, bir modelin bir işletmede etkin bir rol oynamasını sağlar. Dağıtılan bir modelden tahminler, iş kararları vermek için kullanılabilir.

## <a name="production-platforms"></a>Üretim platformları

Modelleri üretime koymak için çeşitli yaklaşımlar ve platformlar vardır. Birkaç seçenek aşağıda verilmiştir:

- [Modelleri Azure Machine Learning ile dağıtma](../service/how-to-deploy-and-where.md)
- [SQL Server 'da bir modelin dağıtılması](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Dağıtımdan önce, model Puanlama gecikmesinin süresinin üretilmesi, üretimde kullanılmak üzere yeterince düşüktür.
>

>[!NOTE]
>Azure Machine Learning Studio kullanarak dağıtım için bkz. [Azure Machine Learning Web hizmeti dağıtma](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B testi

Üretimde birden çok model olduğunda, modellerin performansını karşılaştırmak için [bir/B testi](https://en.wikipedia.org/wiki/A/B_testing) gerçekleştirmek yararlı olabilir. 
 
## <a name="next-steps"></a>Sonraki adımlar

**Belirli senaryolar** için işlemdeki adımların tümünü gösteren yönergeler de sağlanır. Bunlar, [örnek yönergeler](walkthroughs.md) makalesinde listelenmiş ve küçük resim açıklamalarıyla bağlantılandırılır. Bulut, şirket içi araçları ve hizmetleri akıllı bir uygulama oluşturmak için bir iş akışı veya işlem hattı halinde nasıl birleştirebileceğinizi gösterir. 
