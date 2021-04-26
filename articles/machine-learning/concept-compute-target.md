---
title: İşlem hedefleri nelerdir?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ile modelinizi eğitme veya dağıtmaya yönelik bir işlem kaynağı veya ortamı nasıl belirleyeceğiniz hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 16c3ac10af7d39ec35cde1cd9d279bced54fd8aa
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062514"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning'deki işlem hedefleri nelerdir?

*İşlem hedefi* , eğitim betiğinizi çalıştırdığınız veya hizmet dağıtımınızı barındırabileceğiniz, belirlenen bir işlem kaynağıdır veya ortamıdır. Bu konum, yerel makineniz veya bulut tabanlı bir işlem kaynağı olabilir. İşlem hedeflerini kullanmak, kodunuzu değiştirmek zorunda kalmadan daha sonra işlem ortamınızı değiştirmenizi kolaylaştırır.

Tipik bir model geliştirme yaşam döngüsünde şunları yapabilirsiniz:

1. Daha az miktarda veri geliştirme ve deneme ile başlayın. Bu aşamada, işlem hedefi olarak yerel bilgisayar veya bulut tabanlı sanal makine (VM) gibi yerel ortamınızı kullanın.
1. Daha büyük verilere kadar ölçeklendirin ve bu [eğitim işlem hedeflerinden](#train)birini kullanarak dağıtılmış eğitim yapın.
1. Modeliniz hazırlandıktan sonra, bu [dağıtım işlem hedeflerinin](#deploy)birine sahip bir Web barındırma ortamına veya IoT cihazına dağıtın.

İşlem hedeflerinizin kullanacağı işlem kaynakları bir [çalışma alanına](concept-workspace.md)eklenir. Yerel makine dışında işlem kaynakları, çalışma alanının kullanıcıları tarafından paylaşılır.

## <a name="training-compute-targets"></a><a name="train"></a> Eğitim işlem hedefleri

Azure Machine Learning, farklı işlem hedefleri arasında değişen desteğe sahiptir. Tipik bir model geliştirme yaşam döngüsü, düşük miktarda veri üzerinde geliştirme veya deneme ile başlar. Bu aşamada, Yerel bilgisayarınız veya bulut tabanlı bir VM gibi yerel bir ortam kullanın. Daha büyük veri kümelerinde eğitime göre ölçeklendirirken veya dağıtılmış eğitim gerçekleştirirken, her çalıştırma gönderdiğinizde otomatik olarak ölçeklenen tek veya çok düğümlü bir küme oluşturmak için Azure Machine Learning işlem kullanın. Ayrıca, farklı senaryolara yönelik destek farklılık gösterebilse de, kendi işlem kaynağınızı ekleyebilirsiniz.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[Bir eğitim çalıştırmasının bir işlem hedefine nasıl gönderileceği](how-to-set-up-training-targets.md)hakkında daha fazla bilgi edinin.

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Çıkarımı için işlem hedefleri

Aşağıdaki işlem kaynakları, model dağıtımınızı barındırmak için kullanılabilir.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Çıkarımı gerçekleştirirken, Azure Machine Learning modeli ve onu kullanmak için gereken ilişkili kaynakları barındıran bir Docker kapsayıcısı oluşturur. Bu kapsayıcı daha sonra aşağıdaki dağıtım senaryolarından birinde kullanılır:

* Gerçek zamanlı çıkarım için kullanılan bir *Web hizmeti* olarak. Web hizmeti dağıtımları aşağıdaki işlem hedeflerinden birini kullanır:

    * [Yerel bilgisayar](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning işlem örneği](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Işlevleri (Önizleme). IŞLEVLERE dağıtım yalnızca Docker kapsayıcısını oluşturmak için Azure Machine Learning bağımlıdır. Buradan, Işlevleri kullanılarak dağıtılır. Daha fazla bilgi için bkz. [Azure işlevlerine makine öğrenimi modeli dağıtma (Önizleme)](how-to-deploy-functions.md).

* Düzenli aralıklarla veri toplu işlemleri işlemek için kullanılan bir _Batch çıkarım_ uç noktası olarak. Batch ınlade [Azure Machine Learning işlem kümelerini](how-to-create-attach-compute-cluster.md)kullanır.

* Bir _IoT cihazına_ (Önizleme). IoT cihazına dağıtım, Docker kapsayıcısını oluşturmak için yalnızca Azure Machine Learning bağımlıdır. Buradan, Azure IoT Edge kullanılarak dağıtılır. Daha fazla bilgi için bkz. [IoT Edge modülü olarak dağıtma (Önizleme)](../iot-edge/tutorial-deploy-machine-learning.md).

[Modelinizin bir işlem hedefine dağıtımını nerede ve nasıl dağıtacağınızı](how-to-deploy-and-where.md)öğrenin.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning işlem (yönetilen)

Yönetilen bir işlem kaynağı Azure Machine Learning tarafından oluşturulur ve yönetilir. Bu işlem makine öğrenimi iş yükleri için iyileştirilmiştir. Azure Machine Learning işlem kümeleri ve [işlem örnekleri](concept-compute-instance.md) yalnızca yönetilen hesaplalardır.

Azure Machine Learning işlem örnekleri veya işlem kümeleri oluşturabilirsiniz:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).
* Python SDK 'Sı ve CLı:
    * [İşlem örneği](how-to-create-manage-compute-instance.md).
    * [İşlem kümesi](how-to-create-attach-compute-cluster.md).
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (Önizleme).
* Azure Resource Manager şablonu. Örnek bir şablon için bkz. [Azure Machine Learning işlem kümesi oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* [Azure CLI için](reference-azure-machine-learning-cli.md#resource-management)Machine Learning uzantısı.

Oluşturulduğunda, bu işlem kaynakları diğer tür bilgi işlem hedeflerinin aksine, çalışma alanınızın otomatik olarak bir parçasıdır.


|Özellik  |İşlem kümesi  |İşlem örneği  |
|---------|---------|---------|
|Tek veya çok düğümlü küme     |    **&check;**       |         |
|Her çalıştırma gönderdiğinizde otomatik ölçekler     |     **&check;**      |         |
|Otomatik küme yönetimi ve iş zamanlaması     |   **&check;**        |     **&check;**      |
|Hem CPU hem de GPU kaynakları için destek     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Bir işlem *kümesi* boşta olduğunda, otomatik olarak 0 düğüme ölçeklenirken, kullanımda olmadığında ödeme yapmayın. İşlem *örneği* her zaman açık olur ve otomatik ölçeklendirme yapmaz. Ek maliyetten kaçınmak için, kullanmadığınız zaman [işlem örneğini durdurmanız](how-to-create-manage-compute-instance.md#manage) gerekir.

### <a name="supported-vm-series-and-sizes"></a>Desteklenen VM Serisi ve boyutları

Azure Machine Learning yönetilen bir işlem kaynağı için bir düğüm boyutu seçtiğinizde, Azure 'da kullanılabilir olan VM boyutları arasından seçim yapabilirsiniz. Azure, farklı iş yükleri için Linux ve Windows için bir dizi boyut sunar. Daha fazla bilgi için bkz. [VM türleri ve boyutları](../virtual-machines/sizes.md).

VM boyutu seçmenin bazı özel durumları ve sınırlamaları vardır:

* Bazı VM serileri Azure Machine Learning desteklenmez.
* Bazı VM dizileri kısıtlıdır. Kısıtlı bir seriyi kullanmak için desteğe başvurun ve seri için bir kota artışı isteyin. Desteğe başvurma hakkında daha fazla bilgi için bkz. [Azure destek seçenekleri](https://azure.microsoft.com/support/options/).

Desteklenen seriler ve kısıtlamalar hakkında daha fazla bilgi edinmek için aşağıdaki tabloya bakın.

| **Desteklenen VM Serisi**  | **Kısıtlamalar** | **Kategori** | **Desteklediği** |
|------------|------------|------------|------------|
| D | Yok. | Genel amaçlı | İşlem kümeleri ve örnek |
| DDSv4 | Yok. | Genel amaçlı | İşlem kümeleri ve örnek |
| Dv2 | Yok. | Genel amaçlı | İşlem kümeleri ve örnek |
| Dv3 | Yok.| Genel amaçlı | İşlem kümeleri ve örnek |
| DSv2 | Yok. | Genel amaçlı | İşlem kümeleri ve örnek |
| DSv3 | Yok.| Genel amaçlı | İşlem kümeleri ve örnek |
| EAv4 | Yok. | Bellek için iyileştirilmiş | İşlem kümeleri ve örnek |
| Ev3 | Yok. | Bellek için iyileştirilmiş | İşlem kümeleri ve örnek |
| FSv2 | Yok. | İşlem için iyileştirilmiş | İşlem kümeleri ve örnek |
| H | Yok. | Yüksek performanslı işlem | İşlem kümeleri ve örnek |
| HB | Onay gerektirir. | Yüksek performanslı işlem | İşlem kümeleri ve örnek |
| HBv2 | Onay gerektirir. |  Yüksek performanslı işlem | İşlem kümeleri ve örnek |
| HCS | Onay gerektirir. |  Yüksek performanslı işlem | İşlem kümeleri ve örnek |
| M | Onay gerektirir. | Bellek için iyileştirilmiş | İşlem kümeleri ve örnek |
| NC | Yok. |  GPU | İşlem kümeleri ve örnek |
| NC promosyon | Yok. | GPU | İşlem kümeleri ve örnek |
| NCsv2 | Onay gerektirir. | GPU | İşlem kümeleri ve örnek |
| NCsv3 | Onay gerektirir. | GPU | İşlem kümeleri ve örnek |  
| Üstündeki | Onay gerektirir. | GPU | İşlem kümeleri ve örnek | 
| NDv2 | Onay gerektirir. | GPU | İşlem kümeleri ve örnek | 
| NV | Yok. | GPU | İşlem kümeleri ve örnek | 
| NVv3 | Onay gerektirir. | GPU | İşlem kümeleri ve örnek | 


Azure Machine Learning bu VM serisini desteklese de, tüm Azure bölgelerinde kullanılamayabilir. VM serisinin kullanılabilir olup olmadığını denetlemek için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning, Azure Işlem tarafından desteklenen tüm VM boyutlarını desteklemez. Kullanılabilir VM boyutlarını listelemek için aşağıdaki yöntemlerden birini kullanın:
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK'sı](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>İşlem yalıtımı

Azure Machine Learning işlem, belirli bir donanım türüne yalıtılmış ve tek bir müşteriye adanmış VM boyutları sunar. Yalıtılmış VM boyutları, diğer müşterilerin iş yüklerinden, Toplantı uyumluluk ve mevzuat gereksinimlerini kapsayan nedenlerle yüksek derecede yalıtım gerektiren iş yükleri için idealdir. Yalıtılmış bir boyut kullanılması, sanal makinenizin o belirli sunucu örneğinde çalışan tek bir işlem olmasını sağlar.

Geçerli yalıtılmış VM teklifleri şunları içerir:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA özellikli

Yalıtım hakkında daha fazla bilgi edinmek için bkz. [Azure genel bulutundaki yalıtım](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Yönetilmeyen işlem

Yönetilmeyen bir işlem hedefi Azure Machine Learning *tarafından yönetilmiyor.* Bu tür bir işlem hedefini Azure Machine Learning dışında oluşturun ve sonra çalışma alanınıza ekleyin. Yönetilmeyen işlem kaynakları, makine öğrenimi iş yüklerinin performansını korumanız veya iyileştirebilmeniz için ek adımlar gerektirebilir.

## <a name="next-steps"></a>Sonraki adımlar

Şunları nasıl yapacağınızı öğrenin:
* [Modelinizi eğitme bir işlem hedefi kullanın](how-to-set-up-training-targets.md)
* [Modelinizi bir işlem hedefine dağıtın](how-to-deploy-and-where.md)
