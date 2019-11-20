---
title: Azure Container Instances 'de anomali algılayıcı kapsayıcısını çalıştırma
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcı kapsayıcısını bir Azure Container örneğine dağıtın ve bir Web tarayıcısında test edin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d2e3ed59a7ad71177f34909e37ce885d603297
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515282"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Azure Container Instances için bir anomali algılayıcı kapsayıcısı dağıtın

Bilişsel Hizmetler [anomali algılayıcısı](../anomaly-detector-container-howto.md) kapsayıcısını Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/)dağıtmayı öğrenin. Bu yordam bir anomali algılayıcısı kaynağı oluşturmayı gösterir. Ardından, ilişkili kapsayıcı görüntüsünü çekme hakkında tartışın. Son olarak, bir tarayıcıdan ikisini de düzenleme özelliğini vurgulayacağız. Kapsayıcıları kullanmak, geliştiricilerin bu dikkatini, uygulama geliştirmeye odaklanmadan, altyapının bir şekilde yönetilmesini sağlar.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Özel kapsayıcı kayıt defterine erişim isteme

Kapsayıcıya erişim istemek için öncelikle [anomali algılayıcı kapsayıcı isteği formunu](https://aka.ms/adcontainer) tamamlayıp göndermeniz gerekir.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Kapsayıcı görüntüsünü çekmek ve kapsayıcıyı çalıştırmak için [kapsayıcıları yüklemeyi ve çalıştırmayı](../anomaly-detector-container-configuration.md) gözden geçirin
* Yapılandırma ayarları için [kapsayıcıları](../anomaly-detector-container-configuration.md) yapılandırmayı gözden geçir
* [Anomali algılayıcı API hizmeti hakkında daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
