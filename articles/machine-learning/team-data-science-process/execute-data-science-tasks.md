---
title: Veri bilimi görevlerini yürütme-Team Data Science Işlemi
description: Veri bilimccisi, bir veri bilimi projesini, denetimli, sürümlü ve işbirliğine dayalı bir şekilde nasıl yürütebilirler.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100371966"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Veri bilimi görevlerini yürütme: araştırma, modelleme ve dağıtım

Veri araştırması, modelleme ve dağıtım tipik bir veri bilimi görevi içerir. Bu makalede etkileşimli veri araştırması, veri analizi, raporlama ve model oluşturma gibi çeşitli yaygın veri bilimi görevlerini tamamlamaya yönelik görevler özetlenmektedir. Bir üretim ortamına model dağıtmaya yönelik seçenekler şunlar olabilir:

- [Azure Machine Learning](../index.yml)
- [ML Hizmetleri ile SQL-Server](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> araştırma 

Bir veri bilimcesi, araştırma ve raporlamayı çeşitli yollarla gerçekleştirebilir: Python için kullanılabilen kitaplıkları ve paketleri (örneğin, Matplotlib) veya R (ggçiz veya latku gibi) kullanarak. Veri bilimcileri, söz konusu kodu belirli senaryolar için veri keşfi ihtiyaçlarına uyacak şekilde özelleştirebilir. Yapılandırılmış verilerle ilgilenme ihtiyacı, metin veya görüntüler gibi yapılandırılmamış veriler için farklıdır. 

Azure Machine Learning gibi ürünler Ayrıca, özellik oluşturma da dahil olmak üzere veri denetimi ve araştırmasına yönelik [Gelişmiş veri hazırlığı](../how-to-create-register-datasets.md) sağlar. Kullanıcı, ihtiyaçlarını en iyi şekilde sunan araçlara, kitaplıklara ve paketlere karar vermelidir. 

Bu aşamanın sonundaki teslim edilebilir bir veri araştırma rapordur. Rapor, modelleme için kullanılacak verilerin ayrıntılı bir görünümünü ve verilerin modelleme adımına devam etmek için uygun olup olmadığına ilişkin bir değerlendirme sağlamalıdır. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelleme

Çeşitli dillerde eğitim modelleri için çok sayıda araç seti ve paket vardır. Veri bilimcileri, uygun iş kullanım örnekleri ve üretim senaryoları için doğruluk ve gecikme süresi ile ilgili performans konuları karşılanmadığı sürece, ne kadar rahat olduğunu bilmekten çekinmeyin.

### <a name="model-management"></a>Model yönetimi
Birden çok Model derlendikten sonra, modelleri kaydetmek ve yönetmek için genellikle bir sisteme sahip olmanız gerekir. Genellikle betiklerin veya API 'lerin ve arka uç veritabanının veya sürüm oluşturma sisteminin bir birleşimine ihtiyacınız vardır. Bu yönetim görevleri için göz önünde bulundurmanız gereken birkaç seçenek şunlardır:

1. [Azure Machine Learning-model yönetimi hizmeti](../index.yml)
2. [MıT 'ten ModelDB](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [Model yönetimi sistemi olarak SQL-Server](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> dağıtım

Üretim dağıtımı, bir modelin bir işletmede etkin bir rol oynamasını sağlar. Dağıtılan bir modelden tahminler, iş kararları vermek için kullanılabilir.

### <a name="production-platforms"></a>Üretim platformları
Modelleri üretime koymak için çeşitli yaklaşımlar ve platformlar vardır. Birkaç seçenek aşağıda verilmiştir:


- [Azure Machine Learning 'de model dağıtımı](../how-to-deploy-and-where.md)
- [SQL Server 'da bir modelin dağıtılması](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Dağıtımdan önce, model Puanlama gecikmesinin süresinin üretilmesi, üretimde kullanılmak üzere yeterince düşüktür.
>
>

**Belirli senaryolar** için işlemdeki tüm adımları gösteren izlenecek yollarda daha fazla örnek mevcuttur. Bunlar, [örnek yönergeler](walkthroughs.md) makalesinde listelenmiş ve küçük resim açıklamalarıyla bağlantılandırılır. Bulut, şirket içi araçları ve hizmetleri akıllı bir uygulama oluşturmak için bir iş akışı veya işlem hattı halinde nasıl birleştirebileceğinizi gösterir.

> [!NOTE]
> Azure Machine Learning Studio kullanarak dağıtım için bkz. [Azure Machine Learning Web hizmeti dağıtma](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B testi
Üretimde birden çok model olduğunda, modellerin performansını karşılaştırmak için [bir/B testi](https://en.wikipedia.org/wiki/A/B_testing) gerçekleştirmek yararlı olabilir. 

 
## <a name="next-steps"></a>Sonraki adımlar

Veri bilimi [projelerinin Ilerlemesini izlemek](track-progress.md) , bir veri bilimcinin bir veri bilimi projesinin ilerlemesini nasıl izleyebilme şeklini gösterir.

[Model işlemi ve CI/CD](ci-cd-flask.md) , geliştirilmiş modeller ile CI/CD 'nin nasıl gerçekleştirileceğini gösterir.
