---
title: HDInsight 'ta Apache Spark Azure Machine Learning iş yüklerini çalıştırma
description: Azure HDInsight 'ta Apache Spark otomatik makine öğrenimi (otomatik ml) ile Azure Machine Learning iş yüklerini çalıştırmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 14cb4f02bc18bda24f022f1ccf41a06776a5136b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241472"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>HDInsight 'ta Apache Spark otomatik makine öğrenimi ile Azure Machine Learning iş yüklerini çalıştırma

Azure Machine Learning, makine öğrenimi modellerinin oluşturulmasını, eğitimini ve dağıtımını basitleştirir ve hızlandırır. Otomatik makine öğrenimi (otomatik ml) içinde, tanımlı bir hedef özelliğine sahip eğitim verileriyle başlar ve ardından, eğitim puanlarına göre verilerinizin en iyi modelini otomatik olarak seçmek için algoritmaların ve özellik seçimlerinin birleşimleri aracılığıyla yineleyebilirsiniz. HDInsight, müşterilerin yüzlerce düğüm içeren kümeler sağlamasını sağlar. HDInsight kümesinde Spark üzerinde çalışan oto ml, kullanıcıların eğitim işlerini bir genişleme düzeyinde çalıştırmak ve birden çok eğitim işini paralel olarak çalıştırmak için bu düğümlerde işlem kapasitesini kullanmalarına olanak sağlar. Bu, kullanıcıların, diğer büyük veri iş yükleriyle işlem paylaşımı sırasında oto ml denemeleri çalıştırmasına olanak tanır.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight kümesine Azure Machine Learning yüklemesi

Otomatik makine öğrenimi hakkında genel öğreticiler için bkz. [öğretici: gerileme modelinizi derlemek için otomatik makine öğrenimi kullanma](../../machine-learning/service/tutorial-auto-train-models.md).
Tüm yeni HDInsight-Spark kümeleri, AzureML-otomatik ml SDK 'Sı ile önceden yüklenmiş olarak gelir. Bu [örnek Jupyter Not defteri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)ile HDInsight üzerinde, oto ml ile çalışmaya başlayın. Bu Jupyter Notebook, basit bir sınıflandırma sorunu için otomatik makine öğrenimi sınıflandırmasının nasıl kullanılacağını gösterir.

> [!Note]
> Azure Machine Learning paketleri Python3 Conda ortamına yüklenir. Yüklü Jupyter Not defteri, PySpark3 çekirdeği kullanılarak çalıştırılmalıdır.

Alternatif olarak, Zeppelin not defterlerini kullanarak oto ml 'yi de kullanabilirsiniz.

> [!Note]
> Zeppelin, Python 'un doğru sürümünü seçmediği [bilinen bir sorun](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) PySpark3. Lütfen belgelenen işi kullanın.

## <a name="authentication-for-workspace"></a>Çalışma alanı için kimlik doğrulaması

Çalışma alanı oluşturma ve deneme gönderimi bir kimlik doğrulama belirteci gerektirir. Bu belirteç, bir [Azure AD uygulaması](../../active-directory/develop/app-objects-and-service-principals.md)kullanılarak oluşturulabilir. Hesapta Multi-Factor Authentication etkinleştirilmemişse, gerekli kimlik doğrulama belirtecini oluşturmak için bir [Azure AD kullanıcısı](/azure/python/python-sdk-azure-authenticate) da kullanılabilir.  

Aşağıdaki kod parçacığı, bir **Azure AD uygulaması**kullanarak bir kimlik doğrulama belirteci oluşturur.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
Aşağıdaki kod parçacığı, bir **Azure AD kullanıcısı**kullanarak bir kimlik doğrulama belirteci oluşturur.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Veri kümesi yükleniyor

Spark üzerinde otomatik makine öğrenimi, geç değerlendirilen, veriler üzerinde sabit işlemler olan veri **akışlarını**kullanır.  Veri akışı, bir Blobun genel okuma erişimine sahip bir veri kümesini ya da SAS belirtecine sahip blob URL 'sini yükleyebilir.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Ayrıca, bir kerelik kayıt kullanarak, veri deposunu çalışma alanıyla de kaydedebilirsiniz.

## <a name="experiment-submission"></a>Deneme gönderimi

[Otomatik makine öğrenme yapılandırmasında](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), paketin dağıtılmış modda çalışması için `spark_context` özelliği ayarlanmalıdır. Paralel olarak yürütülen en fazla yineleme sayısı olan `concurrent_iterations`özellik, Spark uygulamasının yürütücü çekirdekinden daha düşük bir sayı olarak ayarlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* Otomatik makine öğrenimi 'nin arkasındaki eğitim hakkında daha fazla bilgi için bkz. [Microsoft 'un otomatik makine öğrenimini kullanarak yayın modelleri](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/) .
* Azure ML otomatikleştirilmiş ML yeteneklerini kullanma hakkında daha fazla bilgi için, bkz. [Azure Machine Learning yeni otomatik makine öğrenimi özellikleri](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 'ten bir oto ml projesi](https://www.microsoft.com/research/project/automl/)
