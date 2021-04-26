---
title: Eğitim & dağıtımı oluşturma (Studio)
titleSuffix: Azure Machine Learning
description: Machine Learning için eğitim ve dağıtım işlem kaynakları (işlem hedefleri) oluşturmak için Studio 'yu kullanma
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 4194c71823e1affde1dcae47fd7e64668b57c0cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149367"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio 'da model eğitimi ve dağıtımı için işlem hedefleri oluşturma

Bu makalede, Azure Machine Studio 'da işlem hedefleri oluşturmayı ve yönetmeyi öğrenin.  Ayrıca, ile işlem hedefleri oluşturup bunları yönetebilirsiniz:

* Azure Machine Learning için Azure Machine Learning Learning SDK veya CLı uzantısı
  * [İşlem örneği](how-to-create-manage-compute-instance.md)
  * [İşlem kümesi](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes hizmet kümesi](how-to-create-attach-kubernetes.md)
  * [Diğer işlem kaynakları](how-to-attach-compute-targets.md)
* Azure Machine Learning için [vs Code uzantısı](how-to-manage-resources-vscode.md#compute-clusters) .


## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin
* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>İşlem hedefi nedir? 

Azure Machine Learning, modelinizi toplu olarak [__işlem hedefleri__](concept-azure-machine-learning-architecture.md#compute-targets)olarak adlandırılan çeşitli kaynaklar veya ortamlar üzerinde eğitebilirsiniz. İşlem hedefi bir yerel makine veya Azure Machine Learning Işlem, Azure HDInsight veya uzak bir sanal makine gibi bir bulut kaynağı olabilir.  Model dağıtımı için, ["modellerinizi dağıtma"](how-to-deploy-and-where.md)bölümünde açıklandığı gibi işlem hedefleri de oluşturabilirsiniz.

## <a name="view-compute-targets"></a><a id="portal-view"></a>İşlem hedeflerini görüntüleme

Çalışma alanınızın tüm işlem hedeflerini görmek için aşağıdaki adımları kullanın:

1. [Azure Machine Learning Studio](https://ml.azure.com)'ya gidin.
 
1. __Yönet__ altında __işlem__' i seçin.

1. Her bir işlem hedefi türünü göstermek için üstteki sekmeleri seçin.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="İşlem hedefleri listesini görüntüle":::

## <a name="create-compute-target"></a><a id="portal-create"></a>İşlem hedefi oluştur

İşlem hedeflerinin listesini görüntülemek için önceki adımları izleyin. Daha sonra bir işlem hedefi oluşturmak için bu adımları kullanın:

1. En üstteki, oluşturacağınız işlem türüne karşılık gelen sekmeyi seçin.

1. İşlem hedefi yoksa, sayfanın ortasında  **Oluştur** ' u seçin.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="İşlem hedefi oluştur":::

1. İşlem kaynaklarının bir listesini görürseniz, listenin üstündeki **+ Yeni** ' yi seçin.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Yeni ' yi seçin":::


1. İşlem türü için formu doldurun:

  * [İşlem örneği](#compute-instance)
  * [İşlem kümeleri](#amlcompute)
  * [Çıkarım kümeleri](#inference-clusters)
  * [Bağlı işlem](#attached-compute)

1. __Oluştur__’u seçin.

1. Listeden işlem hedefini seçerek oluşturma işleminin durumunu görüntüleyin:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Bir listeden işlem durumunu görüntüleme":::


### <a name="compute-instance"></a>İşlem örneği

İşlem örneğini oluşturmak için [Yukarıdaki adımları](#portal-create) kullanın.  Daha sonra formu aşağıdaki gibi doldurun:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Yeni bir işlem örneği oluştur":::


|Alan  |Açıklama  |
|---------|---------|
|İşlem adı     |  <li>Ad gereklidir ve 3 ila 24 karakter uzunluğunda olmalıdır.</li><li>Geçerli karakterler büyük ve küçük harfler, rakamlar ve  **-** karakterdir.</li><li>Ad bir harfle başlamalıdır</li><li>Adın, bir Azure bölgesindeki tüm mevcut hesaplar arasında benzersiz olması gerekir. Seçtiğiniz ad benzersiz değilse bir uyarı görürsünüz</li><li>**-** Karakter kullanılıyorsa, daha sonra adının sonunda en az bir harf gelmelidir</li>     |
|Sanal makine türü |  CPU veya GPU seçin. Bu tür, oluşturulduktan sonra değiştirilemez     |
|Sanal makine boyutu     |  Desteklenen sanal makine boyutları bölgenizde kısıtlanmış olabilir. [Kullanılabilirlik listesini](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) denetleme     |
|SSH erişimini etkinleştirme/devre dışı bırakma     |   SSH erişimi varsayılan olarak devre dışıdır.  SSH erişimi olamaz. oluşturulduktan sonra değiştirildi. [Vs Code uzak](how-to-set-up-vs-code-remote.md) ile etkileşimli olarak hata ayıklamayı planlıyorsanız erişimi etkinleştirdiğinizden emin olun   |
|Gelişmiş ayarlar     |  İsteğe bağlı. Bir sanal ağ yapılandırın. Bir Azure sanal ağı (VNet) içinde işlem örneği oluşturmak için **kaynak grubu**, **sanal ağ** ve **alt ağ** belirtin. Daha fazla bilgi için, VNET için bu [ağ gereksinimlerine](./how-to-secure-training-vnet.md) bakın.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> İşlem kümeleri

Eğitim, toplu iş veya pekiştirmeye dayalı öğrenme iş yükleriniz için tek veya çok düğümlü bir işlem kümesi oluşturun. Hesaplama kümesini oluşturmak için [Yukarıdaki adımları](#portal-create) kullanın.  Daha sonra formu aşağıdaki gibi doldurun:


|Alan  |Açıklama  |
|---------|---------|
|İşlem adı     |  <li>Ad gereklidir ve 3 ila 24 karakter uzunluğunda olmalıdır.</li><li>Geçerli karakterler büyük ve küçük harfler, rakamlar ve  **-** karakterdir.</li><li>Ad bir harfle başlamalıdır</li><li>Adın, bir Azure bölgesindeki tüm mevcut hesaplar arasında benzersiz olması gerekir. Seçtiğiniz ad benzersiz değilse bir uyarı görürsünüz</li><li>**-** Karakter kullanılıyorsa, daha sonra adının sonunda en az bir harf gelmelidir</li>     |
|Sanal makine türü |  CPU veya GPU seçin. Bu tür, oluşturulduktan sonra değiştirilemez     |
|Sanal makine önceliği | **Adanmış** veya **düşük öncelik** seçin.  Düşük öncelikli sanal makineler, daha ucuz ancak işlem düğümlerini garanti etmez. İşiniz bozulmuş olabilir.
|Sanal makine boyutu     |  Desteklenen sanal makine boyutları bölgenizde kısıtlanmış olabilir. [Kullanılabilirlik listesini](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) denetleme     |
|Minimum düğüm sayısı | Sağlamak istediğiniz düğüm sayısı alt sınırı. Adanmış sayıda düğüm istiyorsanız, bu sayıyı buraya ayarlayın. En az 0 olarak ayarlanarak tasarruf yaparak, küme boşta kaldığında hiçbir düğüm için ödeme yapmazsınız. |
|En fazla düğüm sayısı | Sağlamak istediğiniz en fazla düğüm sayısı. Bir iş gönderildiğinde işlem bu düğüm sayısı üst sınırına göre otomatik olarak ayarlanır. |
|Gelişmiş ayarlar     |  İsteğe bağlı. Bir sanal ağ yapılandırın. Bir Azure sanal ağı (VNet) içinde işlem örneği oluşturmak için **kaynak grubu**, **sanal ağ** ve **alt ağ** belirtin. Daha fazla bilgi için, VNET için bu [ağ gereksinimlerine](./how-to-secure-training-vnet.md) bakın.   Ayrıca kaynaklara erişim izni vermek için [Yönetilen kimlikler](#managed-identity) iliştirme     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Yönetilen kimliği ayarlama

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Küme oluşturma sırasında veya işlem kümesi ayrıntılarını düzenlediğinizde, **Gelişmiş ayarlar**' da **yönetilen kimlik ata** ' yı açıp sistem tarafından atanan bir kimlik veya Kullanıcı tarafından atanan kimlik belirtin.

#### <a name="managed-identity-usage"></a>Yönetilen kimlik kullanımı

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Çıkarım kümeleri

> [!IMPORTANT]
> Azure Machine Learning ile Azure Kubernetes hizmeti 'nin kullanılması birden çok yapılandırma seçeneği içerir. Ağ gibi bazı senaryolar ek kurulum ve yapılandırma gerektirir. Azure ML ile AKS kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmet kümesi oluşturma ve iliştirme](how-to-create-attach-kubernetes.md).

Büyük ölçekli ikinci kullanım için bir Azure Kubernetes hizmeti (AKS) kümesi oluşturun veya ekleyin. AKS kümesini oluşturmak için [Yukarıdaki adımları](#portal-create) kullanın.  Daha sonra formu aşağıdaki gibi doldurun:


|Alan  |Açıklama  |
|---------|---------|
|İşlem adı     |  <li>Ad gereklidir. Ad, 2 ila 16 karakter arasında olmalıdır. </li><li>Geçerli karakterler büyük ve küçük harfler, rakamlar ve  **-** karakterdir.</li><li>Ad bir harfle başlamalıdır</li><li>Adın, bir Azure bölgesindeki tüm mevcut hesaplar arasında benzersiz olması gerekir. Seçtiğiniz ad benzersiz değilse bir uyarı görürsünüz</li><li>**-** Karakter kullanılıyorsa, daha sonra adının sonunda en az bir harf gelmelidir</li>     |
|Kubernetes Hizmeti | **Yeni oluştur** ' u seçin ve formun geri kalanını doldurun.  Veya **var olanı kullan** ' ı seçin ve ardından aboneliğinizden mevcut bir aks kümesi seçin.
|Region |  Kümenin oluşturulacağı bölgeyi seçin |
|Sanal makine boyutu     |  Desteklenen sanal makine boyutları bölgenizde kısıtlanmış olabilir. [Kullanılabilirlik listesini](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) denetleme     |
|Küme amacı  | **Üretim** veya **geliştirme testi** seçin |
|Düğüm sayısı | Sanal makinenin çekirdek sayısıyla (vCPU 'Lar) çarpıldığı düğüm sayısı 12 ' den büyük veya buna eşit olmalıdır. |
| Ağ yapılandırması | Mevcut bir sanal ağ içinde işlem oluşturmak için **Gelişmiş** ' i seçin. Bir sanal ağdaki AKS hakkında daha fazla bilgi için bkz. [Özel uç noktalar ve sanal ağlarla eğitim ve çıkarım sırasında ağ yalıtımı](./how-to-secure-inferencing-vnet.md). |
| SSL yapılandırmasını etkinleştir | İşlem üzerinde SSL sertifikası yapılandırmak için bunu kullanın |

### <a name="attached-compute"></a>Bağlı işlem

Azure Machine Learning çalışma alanı dışında oluşturulan işlem hedeflerini kullanmak için, onları iliştirmeli. İşlem hedefini iliştirmek, çalışma alanınız için kullanılabilir hale getirir.  **Eğitim** için bir işlem hedefi eklemek üzere **bağlı işlem** kullanın.  **Inım** için bir aks kümesi iliştirmek üzere çıkarma **kümelerini** kullanın.

Bir işlem eklemek için [Yukarıdaki adımları](#portal-create) kullanın.  Daha sonra formu aşağıdaki gibi doldurun:

1. İşlem hedefi için bir ad girin. 
1. İliştirilecek işlem türünü seçin. Tüm bilgi işlem türleri Azure Machine Learning Studio 'dan iliştirilemez. Şu anda eğitim için iliştirilebilecek işlem türleri şunlardır:
    * Bir Azure sanal makinesi (Veri Bilimi Sanal Makinesi iliştirmek için)
    * Azure Databricks (makine öğrenimi ardışık düzenleri kullanımı için)
    * Azure Data Lake Analytics (makine öğrenimi ardışık düzenleri kullanımı için)
    * Azure HDInsight

1. Formu doldurun ve gerekli özellikler için değerler sağlayın.

    > [!NOTE]
    > Microsoft, parolalardan daha güvenli olan SSH anahtarlarını kullanmanızı önerir. Parolalar, deneme yanılma saldırılarına karşı savunmasızdır. SSH anahtarları şifreleme imzaları kullanır. Azure sanal makineler ile kullanmak üzere SSH anahtarları oluşturma hakkında bilgi için aşağıdaki belgelere bakın:
    >
    > * [Linux veya macOS 'ta SSH anahtarları oluşturma ve kullanma](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Windows 'da SSH anahtarları oluşturma ve kullanma](../virtual-machines/linux/ssh-from-windows.md)

1. __Ekle__' yi seçin. 


## <a name="next-steps"></a>Sonraki adımlar

Bir hedef oluşturulduktan ve çalışma alanınıza eklendikten sonra, [çalışma yapılandırmanızda](how-to-set-up-training-targets.md) bunu bir `ComputeTarget` nesneyle kullanacaksınız:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* [Eğitim çalışması göndermek](how-to-set-up-training-targets.md)için işlem kaynağını kullanın.
* [Öğretici: bir modeli eğitme](tutorial-train-models-with-aml.md) bir modeli eğmek için yönetilen bir işlem hedefi kullanır.
* Daha iyi modeller oluşturmak için [hiper parametreleri verimli](how-to-tune-hyperparameters.md) bir şekilde ayarlamayı öğrenin.
* Eğitilen bir modelden sonra [modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)öğrenin.
* [Azure sanal ağları ile Azure Machine Learning kullanma](./how-to-network-security-overview.md)