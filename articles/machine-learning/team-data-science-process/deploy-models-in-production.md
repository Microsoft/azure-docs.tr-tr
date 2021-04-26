---
title: Üretim-Team Data Science Işleminde modelleri dağıtma
description: Modelleri üretime dağıtma iş kararları verirken etkin bir rol oynamalarını sağlar.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 89ea1e991df46b4e4d23305b6118980b80c2f917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321182"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>İş kararları verirken etkin bir rol oynatmak için modelleri üretime dağıtın

Üretim dağıtımı, bir modelin bir işletmede etkin bir rol oynamasını sağlar. Dağıtılan bir modelden tahminler, iş kararları vermek için kullanılabilir.

## <a name="production-platforms"></a>Üretim platformları

Modelleri üretime koymak için çeşitli yaklaşımlar ve platformlar vardır. Birkaç seçenek aşağıda verilmiştir:

- [Modelleri Azure Machine Learning ile dağıtma](../how-to-deploy-and-where.md)
- [SQL Server 'da bir modelin dağıtılması](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Dağıtımdan önce, model Puanlama gecikmesinin süresinin üretilmesi, üretimde kullanılmak üzere yeterince düşüktür.
>

>[!NOTE]
>Azure Machine Learning Studio kullanarak dağıtım için bkz. [Azure Machine Learning Web hizmeti dağıtma](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B testi

Üretimde birden çok model olduğunda, model performansını karşılaştırmak için [A/B testi](https://en.wikipedia.org/wiki/A/B_testing) kullanılabilir. 
 
## <a name="next-steps"></a>Sonraki adımlar

**Belirli senaryolar** için işlemdeki adımların tümünü gösteren yönergeler de sağlanır. Bunlar, [örnek yönergeler](walkthroughs.md) makalesinde listelenmiş ve küçük resim açıklamalarıyla bağlantılandırılır. Bulut, şirket içi araçları ve hizmetleri akıllı bir uygulama oluşturmak için bir iş akışı veya işlem hattı halinde nasıl birleştirebileceğinizi gösterir.