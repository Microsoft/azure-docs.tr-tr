---
title: include dosyası
description: include dosyası
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 722893fca90e5a5d0958d1de1698b625af4a5e21
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497011"
---
| İşlem hedefi | Kullanıldığı yerler | GPU desteği | FPGA desteği | Açıklama |
| ----- | ----- | ----- | ----- | ----- |
| [Yerel&nbsp;Web&nbsp;hizmeti](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/hata ayıklama | &nbsp; | &nbsp; | Sınırlı test ve sorun giderme için kullanın. Donanım hızlandırma, yerel sistemdeki kitaplıkların kullanımına bağlıdır.
| [&nbsp;Web&nbsp;hizmeti Azure Machine Learning işlem örneği](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Test/hata ayıklama | &nbsp; | &nbsp; | Sınırlı test ve sorun giderme için kullanın.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Gerçek zamanlı çıkarım |  [Evet](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (Web hizmeti dağıtımı) | [Evet](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Yüksek ölçekli üretim dağıtımları için kullanın. Dağıtılan hizmetin hızlı yanıt süresini ve otomatik ölçeklendirilmesini sağlar. Küme otomatik ölçeklendirme, Azure Machine Learning SDK aracılığıyla desteklenmez. AKS kümesindeki düğümleri değiştirmek için Azure portal AKS kümeniz için Kullanıcı arabirimini kullanın. AKS, tasarımcı için kullanılabilen tek seçenektir. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Test veya geliştirme | &nbsp;  | &nbsp; | 48 GB 'den az RAM gerektiren düşük ölçekli CPU tabanlı iş yükleri için kullanın. |
| [İşlem kümelerini Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Önizle Batch&nbsp;çıkarımı | [Evet](../articles/machine-learning/service/how-to-run-batch-predictions.md) (Machine Learning işlem hattı) | &nbsp;  | Batch Puanlama 'nı sunucusuz işlem üzerinde çalıştırın. Normal ve düşük öncelikli VM 'Leri destekler. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Önizle IoT&nbsp;modülü |  &nbsp; | &nbsp; | IoT cihazlarında ML modellerini dağıtın ve sunun. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge aracılığıyla |  &nbsp; | Evet | IoT cihazlarında ML modellerini dağıtın ve sunun. |

> [!NOTE]
> Yerel, Azure Machine Learning işlem örneği gibi işlem hedefleri ve Azure Machine Learning işlem kümeleri eğitim ve deneme için GPU 'YU desteklese de, __bir Web hizmeti olarak dağıtıldığında__ , yalnızca Azure 'da destekleniyorsa, çıkarım için GPU kullanımı Kubernetes hizmeti.
>
> __Makine öğrenimi ardışık düzenine sahip Puanlama__ yalnızca Azure Machine Learning işlem sırasında desteklenirken çıkarımı için GPU kullanımı desteklenir.