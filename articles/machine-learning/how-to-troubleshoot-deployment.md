---
title: Uzak model dağıtımı sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Azure Kubernetes hizmeti ve Azure Container Instances ile ilgili bazı yaygın Docker dağıtım hatalarını çözmenin, çözme ve sorunlarını giderme hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 8bec083e62bec6a0311487c1e64e780ad14f451b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518272"
---
# <a name="troubleshooting-remote-model-deployment"></a>Uzak model dağıtımı sorunlarını giderme 

Azure Machine Learning kullanarak bir modeli Azure Container Instances (ACI) ve Azure Kubernetes hizmeti 'ne (AKS) dağıttığınızda karşılaşabileceğiniz yaygın hataları nasıl giderebileceğinizi ve çözeceğinizi öğrenin.

> [!NOTE]
> Azure Kubernetes Service 'e (AKS) bir model dağıtıyorsanız, bu küme için [Azure izleyicisini](../azure-monitor/containers/container-insights-enable-existing-clusters.md) etkinleştirmenizi öneririz. Bu, genel küme durumunu ve kaynak kullanımını anlamanıza yardımcı olur. Aşağıdaki kaynakların yararlı olduğunu da görebilirsiniz:
>
> * [AKS kümenizi etkileyen Kaynak Durumu olaylarını denetleme](../aks/aks-resource-health.md)
> * [Azure Kubernetes hizmet tanılaması](../aks/concepts-diagnostics.md)
>
> Bir modeli sağlıksız veya aşırı yüklenmiş bir kümeye dağıtmaya çalışıyorsanız, sorun yaşanması beklenmektedir. AKS kümesi sorunlarını gidermek için yardıma ihtiyacınız varsa lütfen AKS desteğiyle iletişime geçin.

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure aboneliği**. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.
* [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install).
* [Azure CLI](/cli/azure/install-azure-cli).
* [Azure Machine Learning Için CLI uzantısı](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Makine öğrenimi modellerinin Docker dağıtımı için adımlar

Azure Machine Learning, yerel olmayan bir işlem için bir modeli dağıtırken, aşağıdakiler gerçekleşir:

1. Inısenceconfig 'teki ortamlar nesneniz içinde belirttiğiniz Dockerfile, kaynak dizininizin içeriğiyle birlikte buluta gönderilir
1. Daha önce oluşturulmuş bir görüntü, kapsayıcı kayıt defterinizde yoksa, bulutta yerleşik olarak yeni bir Docker görüntüsü oluşturulur ve çalışma alanınızın varsayılan kapsayıcı kayıt defterinde saklanır.
1. Kapsayıcı kayıt defterinizin Docker görüntüsü işlem hedefinizden indirilir.
1. Çalışma alanınızın varsayılan Blob deposu, işlem hedeflerinize bağlanır, böylece kayıtlı modellere erişebilirsiniz
1. Web sunucunuz, giriş betiğinizin işlevi çalıştırılarak başlatılır `init()`
1. Dağıtılan modeliniz bir istek aldığında, `run()` işleviniz bu isteği işler

Yerel bir dağıtım kullanmanın asıl farkı, kapsayıcı görüntüsünün yerel makinenizde yerleşik olarak oluşturulması ve bu nedenle yerel bir dağıtım için Docker 'ın yüklü olması gerekir.

Bu üst düzey adımların anlaşılması hataların nerede olduğunu anlamanıza yardımcı olmalıdır.

## <a name="get-deployment-logs"></a>Dağıtım günlüklerini al

Hata ayıklama hataındaki ilk adım, dağıtım günlüklerinizi almak için kullanılır. İlk olarak, çalışma alanınıza bağlanmak için [buradaki yönergeleri](how-to-deploy-and-where.md#connect-to-your-workspace) izleyin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Dağıtılan bir Web hizmetinden günlükleri almak için şunları yapın:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Çağrılan türünde bir nesneniz olduğu varsayılarak `azureml.core.Workspace` `ws` , şunları yapabilirsiniz:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Yerel olarak hata ayıkla

Bir modeli ACI veya AKS 'e dağıtırken sorun yaşıyorsanız, yerel bir Web hizmeti olarak dağıtın. Yerel web hizmetinin kullanılması sorunları gidermeyi kolaylaştırır. Yerel olarak bir dağıtımda sorun gidermek için, bkz. [Yerel sorun giderme makalesi](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Kapsayıcı zamanlanamaz

Azure Kubernetes Service işlem hedefine hizmet dağıtırken, Azure Machine Learning hizmeti istenen miktarda kaynakla zamanlamayı dener. Kümede 5 dakikadan sonra uygun miktarda kaynağa sahip bir düğüm yoksa dağıtım başarısız olur. Hata iletisi `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Bu hatayı daha fazla düğüm ekleyerek, düğümlerinizin SKU 'sunu değiştirerek ya da hizmetinizin kaynak gereksinimlerini değiştirerek ele alabilirsiniz. 

Hata iletisi genellikle ne kaynak için daha fazla gereksinim duyacağını gösterir. Örneğin, `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` hizmetin GPU gerektirdiğini ve kümede kullanılabilir GPU olmayan üç düğüm olduğunu belirten bir hata iletisi görürseniz. Bu, bir GPU SKU 'SU kullanıyorsanız daha fazla düğüm eklenerek, bir GPU etkin SKU 'ya geçiş yaparak, ortamınızda GPU gerektirmez.  

## <a name="service-launch-fails"></a>Hizmet başlatılamadı

Görüntü başarıyla derlendikten sonra, sistem dağıtım yapılandırmanızı kullanarak bir kapsayıcı başlatmaya çalışır. Kapsayıcı başlatma işleminin bir parçası olarak, `init()` Puanlama betiğinizdeki işlev sistem tarafından çağırılır. İşlevde yakalanamayan özel durumlar varsa `init()` , hata Iletisinde **Crashloopgeri** alma hatası ' nı görebilirsiniz.

[Docker günlüğünü İnceleme](how-to-troubleshoot-deployment-local.md#dockerlog) makalesindeki bilgileri kullanın.

## <a name="function-fails-get_model_path"></a>İşlev başarısız oldu: get_model_path ()

Genellikle, `init()` Puanlama betiğinin işlevindeki [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) işlevi, bir model dosyasını veya kapsayıcıdaki model dosyalarının bir klasörünü bulmak için çağırılır. Model dosyası veya klasörü bulunamazsa işlev başarısız olur. Bu hatada hata ayıklamanın en kolay yolu, kapsayıcı kabuğu 'nda aşağıdaki python kodunu çalıştırmalıdır:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Bu örnek, `/var/azureml-app` Puanlama betiğinizin model dosyasını veya klasörünü bulmasını beklediği kapsayıcıda yerel yolu (göreli olarak) yazdırır. Dosya veya klasörün gerçekten beklenen yerde olduğunu doğrulayabilirsiniz.

Günlüğe kaydetme düzeyinin hata ayıklama olarak ayarlanması ek bilgilerin günlüğe kaydedilmesine neden olabilir ve bu da hatayı belirlemek için yararlı olabilir.

## <a name="function-fails-runinput_data"></a>İşlev başarısız: çalıştırma (input_data)

Hizmet başarıyla dağıtılırsa ancak Puanlama uç noktasına veri gönderdiğinizde çöktüğünde, `run(input_data)` bunun yerine ayrıntılı hata mesajı döndürmesi için işlevinize hata yakalama ifadesini ekleyebilirsiniz. Örnek:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Note**: çağrıdan hata iletilerinin döndürülmesi `run(input_data)` yalnızca hata ayıklama amacıyla yapılmalıdır. Güvenlik nedenleriyle, bir üretim ortamında bu şekilde hata iletileri döndürmemelisiniz.

## <a name="http-status-code-502"></a>HTTP durum kodu 502

502 durum kodu hizmetin bir özel durum yaptığını veya `run()` Score.py dosyasının yönteminde kilitlendiğini gösterir. Dosyada hata ayıklamak için bu makaledeki bilgileri kullanın.

## <a name="http-status-code-503"></a>HTTP durum kodu 503

Azure Kubernetes hizmet dağıtımları otomatik ölçeklendirmeyi destekler, bu da ek yükü desteklemek için çoğaltmaların eklenmesine izin verir. Otomatik Scaler, yükteki **aşamalı** değişiklikleri işlemek için tasarlanmıştır. Saniye başına isteklerde büyük ani artışlar alıyorsanız, istemciler bir HTTP durum kodu 503 alabilir. Otomatik olarak yeniden hareket etmekle birlikte, ek kapsayıcılar oluşturmak için önemli miktarda zaman alır.

Ölçeği artırma/azaltma kararları, geçerli kapsayıcı çoğaltmalarının kullanımına dayanır. Meşgul olan çoğaltma sayısı (bir isteği işleme) geçerli çoğaltmanın toplam sayısına bölünmüş geçerli kullanımdır. Bu sayı aşarsa `autoscale_target_utilization` , daha fazla çoğaltma oluşturulur. Daha düşükse çoğaltmalar azalır. Çoğaltmaları ekleme kararları, ekip ve hızlı (1 saniye içinde). Çoğaltmaları kaldırma kararları (yaklaşık 1 dakika). Varsayılan olarak, otomatik ölçeklendirme hedef kullanımı **%70** olarak ayarlanır, bu da hizmetin saniyede %30 ' a **varan**(RPS) istek sayısını işleyebileceği anlamına gelir.

503 durum kodlarının önlenmesine yardımcı olabilecek iki şey vardır:

> [!TIP]
> Bu iki yaklaşım tek tek veya birlikte kullanılabilir.

* Otomatik ölçeklendirmenin yeni çoğaltmalar oluşturduğu kullanım düzeyini değiştirin. `autoscale_target_utilization`Daha düşük bir değere ayarlayarak kullanım hedefini ayarlayabilirsiniz.

    > [!IMPORTANT]
    > Bu değişiklik çoğaltmaların *daha hızlı* oluşturulmasına neden olmaz. Bunun yerine, daha düşük bir kullanım eşiğine göre oluşturulur. Hizmetin %70 olması beklenene kadar beklemek yerine %30 kullanım gerçekleştiğinde, çoğaltmanın oluşturulmasına neden olur.
    
    Web hizmeti zaten geçerli en fazla çoğaltmaları kullanıyorsa ve 503 durum kodu görmeye devam ediyorsanız, `autoscale_max_replicas` en fazla çoğaltma sayısını artırmak için değeri arttırın.

* En az çoğaltma sayısını değiştirin. En düşük çoğaltmaları artırmak, gelen ani artışları işlemek için daha büyük bir havuz sağlar.

    En az çoğaltma sayısını artırmak için, `autoscale_min_replicas` daha yüksek bir değere ayarlayın. Aşağıdaki kodu kullanarak gerekli çoğaltmaları hesaplayabilirsiniz ve değerleri projenize özgü değerlerle değiştirin:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Yeni en düşük çoğaltmalardan daha büyük istek ani artışları alırsanız, yeniden 503s alabilirsiniz. Örneğin, hizmetinizin trafiği arttıkça, en düşük çoğaltmaları artırmanız gerekebilir.

, Ve için ayarları hakkında daha fazla bilgi için, `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` bkz. [akswebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) modül başvurusu.

## <a name="http-status-code-504"></a>HTTP durum kodu 504

504 durum kodu, isteğin zaman aşımına uğradığını gösterir. Varsayılan zaman aşımı 1 dakikadır.

Gereksiz çağrıları kaldırmak için score.py değiştirerek, zaman aşımını artırabilir veya hizmeti hızlandırmayı deneyebilirsiniz. Bu eylemler sorunu düzeltmez, score.py dosyasında hata ayıklamak için bu makaledeki bilgileri kullanın. Kod, yanıt vermeyen bir durumda veya sonsuz bir döngüde olabilir.

## <a name="other-error-messages"></a>Diğer hata iletileri

Aşağıdaki hatalar için bu eylemleri gerçekleştirin:

|Hata  | Çözüm  |
|---------|---------|
|Web hizmeti dağıtımında görüntü oluşturma hatası     |  Görüntü yapılandırması için Conda dosyasına bir zar bağımlılığı olarak "pynacl = = 1.2.1" ekleyin       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Dağıtımınızda kullanılan VM 'Ler için SKU 'YU daha fazla belleğe sahip olan bir şekilde değiştirin. |
|FPGA hatası     |  FPGA kotası istenene ve onaylanana kadar, Fpg' de modeller dağıtacaksınız. Erişim istemek için kota isteği formunu doldurun: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Gelişmiş hata ayıklama

Model dağıtımınızda yer alan Python kodunda etkileşimli hata ayıklaması yapmanız gerekebilir. Örneğin, giriş betiği başarısız olursa ve neden ek günlüğe kaydetme ile saptanamaz. Visual Studio Code ve hata ayıklama GPY kullanarak, Docker kapsayıcısının içinde çalışan koda iliştirebilirsiniz.

Daha fazla bilgi için [vs Code kılavuzunda etkileşimli hata ayıklamayı](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)ziyaret edin.

## <a name="model-deployment-user-forum"></a>[Model dağıtımı kullanıcı forumu](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Sonraki adımlar

Dağıtım hakkında daha fazla bilgi edinin:

* [Dağıtım ve nerede](how-to-deploy-and-where.md)
* [Öğretici: eğitim & dağıtım modelleri](tutorial-train-models-with-aml.md)
* [Denemeleri yerel olarak nasıl çalıştırılır ve hata ayıklayın](./how-to-debug-visual-studio-code.md)