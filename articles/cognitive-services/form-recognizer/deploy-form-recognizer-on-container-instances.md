---
title: Azure Container Instances 'de form tanıyıcı kapsayıcısını Çalıştır
titleSuffix: Azure Cognitive Services
description: Form tanıyıcı kapsayıcısını bir Azure Container örneğine dağıtın ve bir Web tarayıcısında test edin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c45d0e316cf67d22095911fadfd3d84ef407e1b0
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515624"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Form tanıyıcı kapsayıcısını Azure Container Instances dağıtma

Bilişsel Hizmetler [formu tanıyıcı](form-recognizer-container-howto.md) kapsayıcısını Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/)dağıtma hakkında bilgi edinin. Bu yordamda, bir Azure form tanıyıcı kaynağının oluşturulması gösterilmektedir. Ardından, ilişkili kapsayıcı görüntüsünü çekme hakkında tartışın. Son olarak, bir tarayıcıdan ikisini de düzenleme özelliğini vurgulayacağız. Kapsayıcıları kullanmak, geliştiricilerin bu dikkatini, uygulama geliştirmeye odaklanmadan, altyapının bir şekilde yönetilmesini sağlar.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Kapsayıcı kayıt defterine erişim isteme

Kapsayıcıya erişim istemek için önce bilişsel [Hizmetler formu tanıyıcı kapsayıcıları erişim isteği formunu](https://aka.ms/FormRecognizerRequestAccess) doldurmanız ve göndermeniz gerekir. Bunun yapılması Görüntü İşleme için de oturum açar. Görüntü İşleme isteği formuna ayrı olarak kaydolmanız gerekmez. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
