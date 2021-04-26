---
title: Azure Kubernetes hizmetini çalıştırma-Metin Analizi
titleSuffix: Azure Cognitive Services
description: Metin Analizi kapsayıcı görüntüsünü Azure Kubernetes hizmetine dağıtın ve bir Web tarayıcısında test edin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fb33bd4fe70c51e1e2afffece10ba3b29dcb0450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932441"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Azure Kubernetes hizmetine bir Metin Analizi kapsayıcısı dağıtma

Azure bilişsel Hizmetler [metin analizi](./text-analytics-how-to-install-containers.md) kapsayıcı görüntüsünü Azure Kubernetes Service 'e (aks) dağıtmayı öğrenin. Bu yordamda, bir Metin Analizi kaynağı oluşturma, ilişkili bir yaklaşım Analizi görüntüsü oluşturma ve bu iki tarayıcıyı tarayıcıdan nasıl alıştırma yapılacağı gösterilmektedir. Kapsayıcıları kullanmak, dikkat etmeniz gereken altyapıyı, uygulama geliştirmeye odaklanmak yerine altyapıya kadar bir yere kaydırabilirler.

## <a name="prerequisites"></a>Önkoşullar

Bu yordam, yüklenmesi ve yerel olarak çalıştırılması gereken çeşitli araçlar gerektirir. Azure Cloud Shell kullanmayın. Şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services) oluşturun.
* Bir metin Düzenleyicisi, örneğin [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](/cli/azure/install-azure-cli) yüklendi.
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) yüklendi.
* Doğru fiyatlandırma katmanına sahip bir Azure kaynağı. Fiyatlandırma katmanlarının tümü bu kapsayıcı ile çalışmaz:
    * Yalnızca F0 veya standart fiyatlandırma katmanlarına sahip **Azure metin analizi** kaynağı.
    * S0 fiyatlandırma katmanı ile Azure bilişsel **Hizmetler** kaynağı.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Anahtar İfade Ayıklama](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Dil Algılama](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Yaklaşım Analizi](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilişsel [Hizmetler kapsayıcısı](../../cognitive-services-container-support.md) kullanın
* [Metin analizi bağlı hizmetini](../index.yml) kullanma
