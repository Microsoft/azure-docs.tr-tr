---
title: KEDA ile Kubernetes üzerinde Azure Işlevleri
description: Kubernetes 'te Azure Işlevlerinin nasıl çalıştırılacağını, KEDA, Kubernetes tabanlı olay temelli otomatik ölçeklendirmeyi kullanarak bulutta veya şirket içinde nasıl çalıştıracağınızı anlayın.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari, Kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 0b77946b24bcc2e329a5c4480e9bd5ef055ef82b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173682"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA ile Kubernetes üzerinde Azure Işlevleri

Azure Işlevleri çalışma zamanı, nerede ve nasıl istediğinizi barındırmak için esneklik sağlar.  [Keda](https://keda.sh) (Kubernetes tabanlı olay temelli otomatik ölçeklendirme), Kubernetes 'te olay odaklı ölçek sağlamak Için Azure işlevleri çalışma zamanı ve araçları ile sorunsuz bir şekilde çiftler sağlar.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes tabanlı işlevler nasıl çalışır?

Azure Işlevleri hizmeti iki temel bileşenden oluşur: çalışma zamanı ve ölçek denetleyicisi.  Işlevler çalışma zamanı, kodunuzu çalıştırır ve yürütür.  Çalışma zamanı, işlev yürütmelerinin tetiklenmesi, günlüğe kaydı ve yönetilmesi ile ilgili mantığı içerir.  Azure Işlevleri çalışma zamanı *her yerde*çalıştırılabilir.  Diğer bileşen bir ölçek denetleyicisidir.  Ölçek denetleyicisi, işlevinizi hedefleyen olayların oranını izler ve uygulamanızı çalıştıran örnek sayısını etkin bir şekilde ölçeklendirir.  Daha fazla bilgi için bkz. [Azure işlevleri ölçeklendirme ve barındırma](functions-scale.md).

Kubernetes tabanlı Işlevler, bir [Docker kapsayıcısında](functions-create-function-linux-custom-image.md) , Keda ile olay odaklı ölçeklendirmeyle işlevleri çalışma zamanı sağlar.  KEDA, 0 örneğe (hiçbir olay gerçekleşmesiz) ve en fazla *n* örneğe kadar ölçeklendirebilir. Bu, Kubernetes otomatik (yatay Pod otomatik Scaler) için özel ölçümler ortaya çıkaran bunu yapar.  Bir Kubernetes kümesinde, Işlev kapsayıcılarını KEDA kullanarak, sunucusuz işlev yeteneklerini çoğaltabilirsiniz.  Bu işlevler, sunucusuz altyapı için [Azure Kubernetes Hizmetleri (AKS) sanal düğümleri](../aks/virtual-nodes-cli.md) özelliği kullanılarak da dağıtılabilir.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes 'te KEDA ve işlevleri yönetme

Kubernetes kümenizdeki Işlevleri çalıştırmak için KEDA bileşenini yüklemelisiniz. Bu bileşeni, [Azure Functions Core Tools](functions-run-local.md)kullanarak yükleyebilirsiniz.

### <a name="installing-with-the-azure-functions-core-tools"></a>Azure Functions Core Tools ile yükleme

Varsayılan olarak, temel araçlar, sırasıyla olay odaklı ve HTTP ölçeklendirmesini destekleyen KEDA ve Osıris bileşenlerini de yüklerse.  Yükleme geçerli bağlamda çalışan `kubectl` kullanıyor.

Aşağıdaki Install komutunu çalıştırarak KEDA kümenize ' i de yüklersiniz:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes 'e işlev uygulaması dağıtma

Herhangi bir işlev uygulamasını KEDA çalıştıran bir Kubernetes kümesine dağıtabilirsiniz.  İşlevleriniz bir Docker kapsayıcısında çalıştığından, projenizin bir `Dockerfile`olması gerekir.  Henüz bir tane yoksa, Işlevler projenizin kökünde aşağıdaki komutu çalıştırarak bir Dockerfile ekleyebilirsiniz:

```cli
func init --docker-only
```

Bir görüntü oluşturmak ve işlevlerinizi Kubernetes 'e dağıtmak için aşağıdaki komutu çalıştırın:

> [!NOTE]
> Temel araçlar, görüntüyü derlemek ve yayımlamak için Docker CLı özelliğinden yararlanır. Docker 'ın zaten yüklü olduğundan ve `docker login`hesabınıza bağlı olduğundan emin olun.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`, işlev uygulamanızın adıyla değiştirin.

Bu bir Kubernetes `Deployment` kaynağı, bir `ScaledObject` kaynağı ve `Secrets`, `local.settings.json` dosyasından içeri aktarılan ortam değişkenlerini içeren bir şekilde oluşturur.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Özel bir kayıt defterinden işlev uygulaması dağıtma

Yukarıdaki akış, özel kayıt defterleri için de kullanılır.  Kapsayıcı görüntünüzü özel bir kayıt defterinden çekeediyorsanız, `func kubernetes deploy`çalıştırırken özel kayıt defteri kimlik bilgilerini tutan Kubernetes gizliliğine başvuran `--pull-secret` bayrağını ekleyin.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes 'ten bir işlev uygulamasını kaldırma

Dağıttıktan sonra, ilişkili `Deployment``ScaledObject`, oluşturulan `Secrets` kaldırarak bir işlevi kaldırabilirsiniz.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes 'ten KEDA kaldırma

Bir Kubernetes kümesinden KEDA kaldırmak için aşağıdaki çekirdek araçları komutunu çalıştırabilirsiniz:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>KEDA 'da desteklenen Tetikleyiciler

KEDA, aşağıdaki Azure Işlev Tetikleyicileri için destek içerir:

* [Azure depolama kuyrukları](functions-bindings-storage-queue.md)
* [Azure Service Bus kuyrukları](functions-bindings-service-bus.md)
* [Azure olay/IoT Hub 'Ları](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Oybbitmq kuyruğu](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP tetikleyicisi desteği

HTTP Tetikleyicileri sunan Azure Işlevlerini kullanabilirsiniz, ancak KEDA bunları doğrudan yönetemez.  Azure Functions Core Tools, HTTP uç noktalarının ölçeğini 0 ' dan 1 ' e ölçeklendirmeye olanak tanıyan, ilgili bir proje olan Osıris 'yi yükler  1 ile *n* arasında ölçekleme geleneksel Kubernetes ölçekleme ilkelerine bağlıdır.

## <a name="next-steps"></a>Sonraki Adımlar
Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Özel görüntü kullanarak bir işlev oluşturma](functions-create-function-linux-custom-image.md)
* [Azure İşlevleri’ni yerel olarak kodlama ve test etme](functions-develop-local.md)
* [Azure Işlevi tüketim planı nasıl çalışır?](functions-scale.md)