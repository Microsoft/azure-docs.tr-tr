---
title: Veri alımı & Otomasyonu
titleSuffix: Azure Machine Learning
description: Makine öğrenimi modellerinizi eğitmek için kullanılabilir veri alımı seçeneklerinin avantajlarını ve dezavantajlarını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98872104"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure Machine Learning iş akışları için veri alma seçenekleri

Bu makalede, Azure Machine Learning sunulan veri alma seçeneklerinin avantajlarını ve dezavantajlarını öğrenirsiniz. 

Aşağıdakilerden birini seçin:
+ Verileri ayıklamak, yüklemek ve dönüştürmek için özel olarak oluşturulan işlem hatları [Azure Data Factory](#azure-data-factory)

+ Veri alma görevleri için özel kod çözümü sağlayan [Python SDK Azure Machine Learning](#azure-machine-learning-python-sdk).

+ her ikisinin birleşimi

Veri alımı, yapılandırılmamış verilerin bir veya daha fazla kaynaktan ayıklanması ve ardından eğitim makine öğrenimi modelleri için hazırlandığı işlemdir. Ayrıca, özellikle el ile yapıldıysa ve birden çok kaynaktan büyük miktarda veriniz varsa, bu da yoğun zaman alabilir. Bu çabayı otomatik hale getirmek kaynakları boşaltır ve modellerinizin en son ve uygun verileri kullanmasını sağlar.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) veri alma işlem hatları için veri kaynağı izleme ve Tetikleyicileri için yerel destek sunar.  

Aşağıdaki tabloda, veri alma iş akışlarınız için Azure Data Factory kullanmanın uzmanları ve dezavantajları özetlenmektedir.

|Avantajlar|Dezavantajlar
---|---
Verileri ayıklamak, yüklemek ve dönüştürmek için özel olarak oluşturulmuştur.|Şu anda sınırlı sayıda Azure Data Factory işlem hattı görevi sunmaktadır 
Veri hareketini ve dönüştürmeleri ölçekleyerek düzenlemek için veri odaklı iş akışları oluşturmanıza olanak tanır.|Yapı ve bakım açısından pahalıdır. Daha fazla bilgi için Azure Data Factory [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) bakın.
[Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) ve [Azure Işlevleri](../data-factory/control-flow-azure-function-activity.md) gibi çeşitli Azure araçlarıyla tümleşiktir | Betikleri yerel olarak çalıştırmaz, bunun yerine betik çalıştırmaları için ayrı bir işlem kullanır 
Veri kaynağı tarafından tetiklenen veri alımını yerel olarak destekler| 
Veri hazırlama ve model eğitimi işlemi birbirinden farklıdır.|
Azure Data Factory veri akışları için gömülü Data kökenini özelliği|
Betik olmayan yaklaşımlar için düşük kod deneyimi [Kullanıcı arabirimi](../data-factory/quickstart-create-data-factory-portal.md) sağlar |

Bu adımlar ve aşağıdaki diyagramda Azure Data Factory veri alımı iş akışı gösterilmektedir.

1. Verileri kaynaklarından çekin
1. Verileri Azure Machine Learning için veri depolama işlevi gören bir çıktı blobu kapsayıcısına dönüştürün ve kaydedin
1. Hazırlanan veriler depolandığında Azure Data Factory işlem hattı, model eğitimi için hazırlanan verileri alan bir eğitim Machine Learning işlem hattı çağırır


    ![ADF veri alımı](media/concept-data-ingestion/data-ingest-option-one.svg)
    
[Azure Data Factory](how-to-data-ingest-adf.md)Machine Learning için bir veri alma işlem hattı oluşturmayı öğrenin.

## <a name="azure-machine-learning-python-sdk"></a>Python SDK Azure Machine Learning 

[Python SDK](/python/api/overview/azure/ml)ile veri alma görevlerini Azure Machine Learning bir işlem [hattı](./how-to-create-machine-learning-pipelines.md) adımına ekleyebilirsiniz.

Aşağıdaki tabloda, veri alma görevleri için SDK 'Yı ve bir ML işlem hattı adımını kullanmaya yönelik profesyonelleri ve Con özetlenmektedir.

Avantajlar| Dezavantajlar
---|---
Kendi Python betiklerinizi yapılandırma | Veri kaynağı değişikliği tetiklemesini yerel olarak desteklemez. Mantıksal uygulama veya Azure Işlev uygulamaları gerektirir
Her model eğitimi yürütmesinin parçası olarak veri hazırlama|Veri alma betiği oluşturmak için geliştirme becerileri gerektirir
[Azure Machine Learning işlem](concept-compute-target.md#azure-machine-learning-compute-managed) dahil olmak üzere çeşitli işlem hedeflerinde veri hazırlama betiklerini destekler |Alma mekanizmasını oluşturmak için bir kullanıcı arabirimi sağlamaz

Aşağıdaki diyagramda Azure Machine Learning işlem hattı iki adımdan oluşur: veri alımı ve model eğitimi. Veri alma adımı, Python kitaplıkları ve Python SDK kullanılarak gerçekleştirilebilen, yerel/Web kaynaklarından veri ayıklama ve eksik değer imputation gibi veri dönüştürmeleri gibi görevleri kapsar. Daha sonra eğitim adımı, makine öğrenimi modelinizi eğmek için hazırlanan verileri eğitim betiğe giriş olarak kullanır. 

![Azure işlem hattı + SDK veri alımı](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalelerini izleyin:
* [Azure Data Factory bir veri alma işlem hattı oluşturun](how-to-data-ingest-adf.md)

* [Azure Pipelines ile veri alma işlem hatlarını otomatikleştirin ve yönetin](how-to-cicd-data-ingestion.md).