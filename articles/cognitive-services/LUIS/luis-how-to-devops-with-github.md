---
title: GitHub-LUSıS ile DevOps uygulama
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS) ve GitHub ile DevOps 'u uygulayın.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 448b3d93ed58e4cfc73da576f0c5871600400ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98019848"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>GitHub eylemlerini kullanarak LUSıS uygulama geliştirmeye DevOps uygulama

LUSıS için DevOps ve yazılım mühendisliği en iyi uygulamaları uygulayan tüm çözümler için [Lusıs DevOps şablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) deposuna gidin. Bu şablon depoyu, kendi projeniz için LUSıS ile [kaynak denetimi](luis-concept-devops-sourcecontrol.md), [Otomatik derlemeler](luis-concept-devops-automation.md), [Test](luis-concept-devops-testing.md)ve [Release Management](luis-concept-devops-automation.md#release-management) 'ı etkinleştiren CI/CD iş akışları ve uygulamalar için yerleşik destek ile kendi deponuzu oluşturmak için kullanabilirsiniz.

## <a name="the-luis-devops-template-repo"></a>LUSıS DevOps şablon deposu

[Lusıs DevOps şablonu deposu](https://github.com/Azure-Samples/LUIS-DevOps-Template) , nasıl yapılacağını açıklar:

* **Şablonu** kopyala depo-şablonu kendi GitHub deponuza kopyalayın.
* **Lusıs kaynaklarını yapılandırma** -Azure 'da sürekli tümleştirme iş akışları tarafından kullanılacak [lusıs yazma ve tahmin kaynaklarını](./luis-how-to-azure-subscription.md) oluşturun.
* **CI/CD iş akışlarını yapılandırma** -CI/CD iş akışları için parametreleri yapılandırın ve [GitHub gizli](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)dizileri içinde depolayın.
* **["Geliştirme iç döngüsü"](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow) boyunca izlenecek yol** -geliştirici bir geliştirme DALıNDA çalışırken örnek bir Luo uygulaması için güncelleştirmeler yapar, güncelleştirmeleri sınar ve sonra değişiklikler önermek ve araştırma gözden geçirmesi için bir çekme isteği oluşturur.
* **CI/CD iş akışlarını yürütme** -GitHub eylemlerini kullanarak [bir lusıs uygulaması derlemek ve test etmek için sürekli tümleştirme iş akışlarını](luis-concept-devops-automation.md) yürütün.
* **Otomatikleştirilmiş test gerçekleştirin** -uygulamanın kalitesini değerlendirmek IÇIN [bir Luo uygulaması için otomatik toplu işlem testi](luis-concept-devops-testing.md) gerçekleştirin.
* Lusıs **uygulamasını dağıtın** -lusıs uygulamasını yayımlamak için [SÜREKLI teslim (CD) işini](luis-concept-devops-automation.md#continuous-delivery-cd) yürütün.
* **Depoyu kendi projem Ile kullanma** -kendi Luo uygulamanız ile depoyu nasıl kullanacağınızı açıklar.

## <a name="next-steps"></a>Sonraki adımlar

* Kendi projem ile DevOps uygulamak için [Lusıs DevOps şablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) deposunu kullanın.
* [LUIS için kaynak denetimi ve dal stratejileri](luis-concept-devops-sourcecontrol.md)
* [LUSıS DevOps için test etme](luis-concept-devops-testing.md)
* [LUSıS DevOps için Otomasyon iş akışları](luis-concept-devops-automation.md)
