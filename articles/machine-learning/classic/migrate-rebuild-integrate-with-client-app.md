---
title: 'ML Studio (klasik): Azure Machine Learning geçirme-işlem hattı uç noktalarını kullanma'
description: İşlem hattı uç noktalarını Azure Machine Learning istemci uygulamalarla tümleştirin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fc7e222d400c2ded602e3d26d504896302fff014
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311796"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>İstemci uygulamalarından işlem hattı uç noktalarını tüketme

Bu makalede, istemci uygulamalarını Azure Machine Learning uç noktalarıyla tümleştirmeyi öğreneceksiniz. Uygulama kodu yazma hakkında daha fazla bilgi için bkz. [Azure Machine Learning uç noktası](../how-to-consume-web-service.md)kullanma.

Bu makale, geçiş serisine Azure Machine Learning için Studio 'nun (klasik) bir parçasıdır. Azure Machine Learning geçirme hakkında daha fazla bilgi için bkz. [geçişe genel bakış makalesi](migrate-overview.md).

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning çalışma alanı. [Azure Machine Learning çalışma alanı oluşturun](../how-to-manage-workspace.md#create-a-workspace).
- Gerçek zamanlı bir [uç nokta veya ardışık düzen uç noktası Azure Machine Learning](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Gerçek zamanlı bir uç nokta kullanın 

Modelinizi gerçek zamanlı bir **uç nokta** olarak dağıttıysanız, onun REST uç noktasını ve C#, Python ve R 'de önceden üretilmiş tüketim kodunu bulabilirsiniz:

1. Azure Machine Learning Studio 'ya gidin ([ml.Azure.com](https://ml.azure.com)).
1. **Uç noktalar** sekmesine gidin.
1. Gerçek zamanlı uç noktanızı seçin.
1. **Tüketme** seçeneğini belirleyin.

> [!NOTE]
> Ayrıca, **Ayrıntılar** sekmesinde uç noktanız için Swagger belirtimini de bulabilirsiniz. Uç nokta şemanızı anlamak için Swagger tanımını kullanın. Swagger tanımı hakkında daha fazla bilgi için bkz. [Swagger resmi belgeleri](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Ardışık düzen uç noktası kullanma

Ardışık düzen uç noktası kullanmanın iki yolu vardır:

- REST API çağrılar
- Azure Data Factory ile tümleştirme

### <a name="use-rest-api-calls"></a>REST API çağrılarını kullanma

İstemci uygulamanızdan REST uç noktasını çağırın. Şemayı anlamak için uç noktanız için Swagger belirtimini kullanabilirsiniz:

1. Azure Machine Learning Studio 'ya gidin ([ml.Azure.com](https://ml.azure.com)).
1. **Uç noktalar** sekmesine gidin.
1. **Ardışık düzen uç noktalarını** seçin.
1. Ardışık düzen uç noktanızı seçin.
1. **Ardışık düzen uç noktası genel bakış** bölmesinde **REST uç nokta belgelerinin** altındaki bağlantıyı seçin.

### <a name="use-azure-data-factory"></a>Azure Data Factory'yi kullanma

Azure Machine Learning işlem hattınızı, bir Azure Data Factory işlem hattında adım olarak çağırabilirsiniz. Daha fazla bilgi için bkz. [Azure Data Factory Azure Machine Learning işlem hatlarını yürütme](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, ardışık düzen uç noktalarınız için şema ve örnek kodu bulmayı öğrendiniz. İstemci uygulamasından alınan uç noktalar hakkında daha fazla bilgi için bkz. [Azure Machine Learning uç noktası](../how-to-consume-web-service.md)kullanma.

Azure Machine Learning geçiş serisinde bulunan makalelerin devamını inceleyin: 
1. [Geçişe genel bakış](migrate-overview.md).
1. [Veri kümesini geçirin](migrate-register-dataset.md).
1. [Bir Studio (klasik) eğitim işlem hattını yeniden derleyin](migrate-rebuild-experiment.md).
1. [Bir Studio (klasik) Web hizmetini yeniden derleyin](migrate-rebuild-web-service.md).
1. **İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin**.
1. [Execute R betiğini geçirin](migrate-execute-r-script.md).