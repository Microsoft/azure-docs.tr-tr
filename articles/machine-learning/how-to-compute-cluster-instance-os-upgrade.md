---
title: İşlem kümesi ve örneği için konak işletim sistemini yükseltme
titleSuffix: Azure Machine Learning
description: İşlem kümesi ve işlem örneği için konak işletim sistemini Ubuntu 16,04 LTS 'den 18,04 LTS 'ye yükseltin.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872020"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>İşlem örneğini ve işlem kümesi konak işletim sistemini yükselt

Azure Machine Learning __işlem kümesi__ ve __işlem örneği__ yönetilen işlem altyapısıdır. Yönetilen bir hizmet olarak, Microsoft konak işletim sistemini ve yüklü olan paketleri ve yazılım sürümlerini yönetir.

İşlem kümesi ve işlem örneği için ana bilgisayar işletim sistemi Ubuntu 16,04 LTS olarak belirlenmiştir. **30 nisan 2021**' de Ubuntu, 16,04 için desteği sonlandırıyor. Microsoft, __15 mart 2021__' den itibaren ana bilgisayar Işletim sistemini Ubuntu 18,04 LTS olarak güncelleştirecektir. 18,04 ' ye güncelleştirme, Ubuntu topluluğundan devam eden güvenlik güncelleştirmelerinin ve desteğinin olmasını sağlar. Bu güncelleştirme, Azure bölgeleri arasında kullanıma sunulacaktır ve __09 nisan 2021 ' ye__ kadar tüm bölgelerde kullanılabilir olacaktır. 16,04 için Ubuntu sonlandırma desteği hakkında daha fazla bilgi için [Ubuntu yayın bloguna](https://wiki.ubuntu.com/Releases)bakın.

> [!TIP]
> * Konak işletim sistemi, bir modeli eğitmek veya dağıtmak için bir [ortam](how-to-use-environments.md) için belirtebileceğiniz işletim sistemi sürümüdür. Ortamlar Docker içinde çalışır. Docker, ana bilgisayar IŞLETIM sisteminde çalışır.
> * Şu anda eğitim veya dağıtım için Ubuntu 16,04 tabanlı ortamları kullanıyorsanız, Microsoft, Ubuntu 18,04 tabanlı görüntüleri kullanmaya geçiş yapmanızı önerir. Daha fazla bilgi için bkz. ortamları ve [Azure Machine Learning kapsayıcıları deposunu](https://github.com/Azure/AzureML-Containers/tree/master/base) [kullanma](how-to-use-environments.md) .
> * Ubuntu 18,04 tabanlı bir Azure Machine Learning işlem örneği kullanırken, varsayılan Python sürümü _python 3,8_' dir.
## <a name="creating-new-resources"></a>Yeni kaynaklar oluşturma

İşlem kümesi veya işlem örnekleri 09 Nisan 'dan sonra oluşturulan __2021,__ varsayılan olarak konak işletim sistemi olarak Ubuntu 18,04 LTS 'yi kullanır. Farklı bir konak işletim sistemi seçemezsiniz.

## <a name="upgrade-existing-resources"></a>Mevcut kaynakları yükselt

__15 mart 2021 '__ den önce oluşturulan işlem kümeleriniz veya işlem örnekleri varsa, konak Işletim sistemini Ubuntu 18,04 ' ye yükseltmek için işlem yapmanız gerekir. Azure Machine Learning erişiminizin bulunduğu bölgeye bağlı olarak, değişikliklerinizin tüm bölgelere alındığından emin olmak için bu işlemleri __09 nisan 2021 '__ den sonra yapmanız önerilir:

* __Azure Machine Learning işlem kümesi__:

    * Küme __Min Nodes = 0__ ile yapılandırıldıysa, tüm işler tamamlandığında otomatik olarak yükseltilir ve sıfır düğüm olarak azaltılır.
    * Minimum __düğümler 0 >__, geçici olarak en düşük düğümleri sıfıra değiştirin ve kümenin sıfır düğüme azalmasına izin verin.

    En düşük düğümleri değiştirme hakkında daha fazla bilgi için, [az ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) Azure CLI komutuna veya [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK başvurusuna bakın.

* __Azure Machine Learning işlem örneği__: yeni bir işlem örneği oluşturun (Ubuntu 18,04 ' i kullanacak) ve eski örneği silmez.

    * Çalışma alanı dosya paylaşımında, veri depolarında depolanan tüm not defterine yeni işlem örneğinden erişilebilecektir.
    * Özel Conda ortamları oluşturduysanız, bu ortamları mevcut örnekten dışarı aktarabilir ve yeni örneğe içeri aktarabilirsiniz. Conda dışa aktarma ve içeri aktarma hakkında bilgi için docs.conda.io adresindeki [Conda belgelerine](https://docs.conda.io/) bakın.

    Daha fazla bilgi için bkz. [işlem örneği nedir](concept-compute-instance.md) ve [Azure Machine Learning işlem örneği oluşturma ve yönetme](how-to-create-manage-compute-instance.md) makaleleri

## <a name="check-host-os-version"></a>Konak işletim sistemi sürümünü denetle

Konak işletim sistemi sürümünü denetleme hakkında daha fazla bilgi için Ubuntu Community Wiki sayfasına bkz. [Ubuntu sürümünüzü denetleme](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> `lsb_release -a`Wiki 'den komutunu kullanmak için [bir işlem örneğinde terminal oturumu kullanabilirsiniz](how-to-access-terminal.md).
## <a name="next-steps"></a>Sonraki adımlar

Başka sorularınız veya endişeleriniz varsa, adresinden bizimle iletişim kurun [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
