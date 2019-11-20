---
title: Azure Container Instances için hazırlık araştırmalarını yapılandırma
description: Azure Container Instances içindeki kapsayıcıların istekleri yalnızca hazırlandıklarında almasını sağlamak için bir araştırma yapılandırmayı öğrenin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 10/17/2019
ms.author: danlep
ms.openlocfilehash: 9cdc8362c377be28a3ed1300b599dc8ebef9e903
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905582"
---
# <a name="configure-readiness-probes"></a>Hazırlık araştırmalarını yapılandırma

Trafiğe sunan Kapsayıcılı uygulamalar için, kapsayıcının gelen istekleri işlemeye hazırlanmaya yönelik olduğunu doğrulamak isteyebilirsiniz. Azure Container Instances, kapsayıcınıza belirli koşullar altında erişilebilmesi için yapılandırma eklemek üzere hazırlık araştırmalarını destekler. Hazırlık araştırması bir [Kubernetes hazırlık araştırması](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)gibi davranır. Örneğin, bir kapsayıcı uygulamasının başlangıç sırasında büyük bir veri kümesi yüklemesi ve bu süre içinde istek almasını istemediğiniz bir işlem olması gerekebilir.

Bu makalede, bir hazırlık araştırması içeren bir kapsayıcı grubunun nasıl dağıtılacağı açıklanır. bu sayede bir kapsayıcının yalnızca araştırma başarılı olduğunda trafik almasını sağlar.

Azure Container Instances Ayrıca, sağlıksız bir kapsayıcının otomatik olarak yeniden başlatılmasına neden olacak şekilde yapılandırabileceğiniz [lizleştirme yoklamalarını](container-instances-liveness-probe.md)da destekler.

## <a name="yaml-configuration"></a>YAML yapılandırması

Örnek olarak, bir hazırlık araştırması içeren aşağıdaki kod parçacığına sahip `readiness-probe.yaml` bir dosya oluşturun. Bu dosya, küçük bir Web uygulaması çalıştıran bir kapsayıcıdan oluşan bir kapsayıcı grubunu tanımlar. Uygulama, ortak `mcr.microsoft.com/azuredocs/aci-helloworld` görüntüsünden dağıtılır. Bu kapsayıcı uygulaması [, Azure CLI kullanarak Azure 'da bir kapsayıcı örneği dağıtma](container-instances-quickstart.md)gibi hızlı başlangıçlarda da gösterilmiştir.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Başlat komutu

YAML dosyası, kapsayıcı başladığında çalıştırılacak bir başlangıç komutu içerir, bir dize dizisini kabul eden `command` özelliği tarafından tanımlanır. Bu komut, Web uygulamasının çalıştığı ancak kapsayıcının hazırlanmadığında bir zaman benzetimini yapar. İlk olarak, bir kabuk oturumu başlatır ve Web uygulamasını başlatmak için bir `node` komutu çalıştırır. Ayrıca, 240 saniye boyunca uyku moduna geçmek için bir komut başlatır, sonrasında `/tmp` dizininde `ready` adlı bir dosya oluşturulur:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Hazır olma komutu

Bu YAML dosyası, hazırlık denetimi görevi gören bir `exec` hazırlık komutunu destekleyen bir `readinessProbe` tanımlar. Bu örnek, `/tmp` dizinindeki `ready` dosyasının varlığı için komut testlerini hazır olarak onaylar.

`ready` dosyası yoksa, hazır olma komutu sıfır olmayan bir değerle çıkar; kapsayıcı çalışmaya devam eder, ancak erişilemez. Komut 0 çıkış koduyla başarıyla çıktığında, kapsayıcıya erişilmeye hazırlanın. 

`periodSeconds` özelliği, hazırlık komutunun her 5 saniyede bir yürütülmesi gerektiğini belirler. Hazırlık araştırması, kapsayıcı grubunun ömrü boyunca çalışır.

## <a name="example-deployment"></a>Örnek dağıtım

Önceki YAML yapılandırmasıyla bir kapsayıcı grubu dağıtmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Hazırlık denetimlerini görüntüle

Bu örnekte, ilk 240 saniye boyunca, `ready` dosyanın varlığını denetlediğinde hazırlık komutu başarısız olur. Durum kodu, kapsayıcının hazırlantığına işaret eder.

Bu olaylar Azure portal veya Azure CLı 'dan görüntülenebilir. Örneğin, Portal `Unhealthy` türündeki olayları gösterir, hazırlık komutu başarısız olduğunda tetiklenir. 

![Portalın sağlıksız olayı][portal-unhealthy]

## <a name="verify-container-readiness"></a>Kapsayıcının hazır olduğunu doğrulama

Kapsayıcıyı başlattıktan sonra, başlangıçta erişilebilir durumda olmadığından emin olabilirsiniz. Sağlamadıktan sonra kapsayıcı grubunun IP adresini alın:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Hazırlık araştırması başarısız olsa da siteye erişmeyi deneyin:

```bash
wget <ipAddress>
```

Çıkış, siteyi başlangıçta erişilebilir olmadığını gösterir:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 saniye sonra, hazırlık komutu başarılı olur ve kapsayıcının hazır olduğunu işaret edin. Şimdi, `wget` komutunu çalıştırdığınızda başarılı olur:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Kapsayıcı hazırsanız, bir Web tarayıcısı kullanarak IP adresine göz atarak Web uygulamasına da erişebilirsiniz.

> [!NOTE]
> Hazırlık araştırması, kapsayıcı grubunun ömrü boyunca çalışmaya devam eder. Hazırlık komutu daha sonra başarısız olursa kapsayıcının yeniden erişilemez hale gelir. 
> 

## <a name="next-steps"></a>Sonraki adımlar

Hazır olma durumu araştırması, bağımlı kapsayıcılardan oluşan çok Kapsayıcılı grupları kapsayan senaryolarda yararlı olabilir. Çok Kapsayıcılı senaryolar hakkında daha fazla bilgi için [Azure Container Instances Içindeki kapsayıcı grupları](container-instances-container-groups.md)konusuna bakın.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
