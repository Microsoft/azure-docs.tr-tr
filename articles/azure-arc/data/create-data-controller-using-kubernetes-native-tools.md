---
title: Kubernetes araçlarını kullanarak bir veri denetleyicisi oluşturma
description: Kubernetes araçlarını kullanarak bir veri denetleyicisi oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519988"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Kubernetes araçlarını kullanarak Azure Arc veri denetleyicisi oluşturma

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Genel bakış bilgileri için [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md) konusunu gözden geçirin.

Kubernetes araçlarını kullanarak Azure Arc veri denetleyicisi oluşturmak için Kubernetes araçları 'nın yüklü olması gerekir.  Bu makaledeki örneklerde kullanılacak `kubectl` , ancak benzer yaklaşımlar, Kubernetes panosu gibi diğer Kubernetes araçlarıyla `oc` veya `helm` Bu araçlar ve Kubernetes YAML/JSON hakkında bilginiz varsa kullanılabilir.

[Kubectl aracını kurma](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Aşağıda belirtilen Azure Arc veri denetleyicisi oluşturmaya yönelik bazı adımlar, Kubernetes Küme Yöneticisi izinleri gerektirir.  Kubernetes küme yöneticisi değilseniz, Kubernetes küme yöneticisinin sizin adınıza bu adımları gerçekleştirmesini gerekir.

### <a name="cleanup-from-past-installations"></a>Geçmiş yüklemelerinden Temizleme

Azure Arc Data Controller 'ı geçmişte yüklediyseniz, aynı kümede ve Azure Arc veri denetleyicisi komutunu kullanarak sildiyseniz `azdata arc dc delete` , hala silinmesi gereken bazı küme düzeyi nesneler olabilir. Azure Arc Data Controller küme düzeyi nesnelerini silmek için aşağıdaki komutları çalıştırın:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Genel Bakış

Azure Arc Data Controller 'ın oluşturulması, aşağıdaki üst düzey adımlara sahiptir:
1. Arc veri denetleyicisi, Azure SQL yönetilen örneği ve PostgreSQL hiper ölçek için özel kaynak tanımları oluşturun. **[Kubernetes Küme Yöneticisi Izinleri gerektirir]**
2. Veri denetleyicisinin oluşturulacağı bir ad alanı oluşturun. **[Kubernetes Küme Yöneticisi Izinleri gerektirir]**
3. Çoğaltma kümesi, hizmet hesabı, rol ve rol bağlama dahil olmak üzere önyükleyici hizmetini oluşturun.
4. Veri denetleyicisi Yönetici Kullanıcı adı ve parolası için bir parola oluşturun.
5. Veri denetleyicisini oluşturun.
   
## <a name="create-the-custom-resource-definitions"></a>Özel kaynak tanımları oluşturma

Özel kaynak tanımlarını oluşturmak için aşağıdaki komutu çalıştırın.  **[Kubernetes Küme Yöneticisi Izinleri gerektirir]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Veri denetleyicisinin oluşturulacağı bir ad alanı oluşturun

Veri denetleyicisinin oluşturulacağı yeni, ayrılmış bir ad alanı oluşturmak için aşağıdakine benzer bir komut çalıştırın.  Bu örnekte ve bu makaledeki örneklerin geri kalanında, bir ad alanı adı `arc` kullanılacaktır. Farklı bir ad kullanmayı seçerseniz, içinde aynı adı kullanın.

```console
kubectl create namespace arc
```

Diğer kişiler küme yöneticileri olmayan bu ad alanını kullanacaksanız, bir ad alanı yönetici rolü oluşturmanızı ve bu rolü rol bağlama aracılığıyla bu kullanıcılara vermeyi öneririz.  Ad alanı yöneticisinin ad alanı üzerinde tam izinleri olmalıdır.  Daha sonra, kullanıcılara daha ayrıntılı rol tabanlı erişim sağlama hakkında daha fazla bilgi sağlanacaktır.

## <a name="create-the-bootstrapper-service"></a>Önyükleyici hizmetini oluşturma

Önyükleyici hizmeti, veri denetleyicisi, SQL yönetilen örneği veya PostgreSQL hiper ölçek sunucu grubu gibi özel kaynakları oluşturma, düzenlemeyle ve silmeye yönelik gelen istekleri işler.

Önyükleyici hizmeti, önyükleyici hizmeti için bir hizmet hesabı ve önyükleyici hizmet hesabı için rol ve rol bağlama oluşturmak için aşağıdaki komutu çalıştırın.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Aşağıdaki komutu kullanarak önyükleyici Pod 'un çalıştığını doğrulayın.  Durum ' a dönüşene kadar birkaç kez çalıştırmanız gerekebilir `Running` .

```console
kubectl get pod --namespace arc
```

Bootstrapper. YAML şablon dosyası, önyükleyici kapsayıcı görüntüsünü Microsoft Container Registry (MCR) üzerinden çekmesini sağlar.  Ortamınız doğrudan Microsoft Container Registry erişimi yoksa şunları yapabilirsiniz:
- [Microsoft Container Registry kapsayıcı görüntülerini çekmek ve özel bir kapsayıcı kayıt defterine göndermek](offline-deployment.md)için adımları izleyin.
- Özel kapsayıcı kayıt defteriniz için [bir resim çekme gizli anahtarı oluşturun](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) .
- Önyükleyici kapsayıcısına bir resim çekme gizli anahtarı ekleyin. Aşağıdaki örneğe bakın.
- Önyükleyici görüntüsünün görüntü konumunu değiştirin. Aşağıdaki örneğe bakın.

Aşağıdaki örnek, `regcred` Kubernetes belgelerinde gösterildiği gibi bir resim çekme gizli adı oluşturduğunuzu varsayar.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Veri denetleyicisi Yöneticisi için gizli dizi oluşturma

Veri denetleyicisi Yönetici Kullanıcı adı ve parolası, yönetim işlevlerini gerçekleştirmek için veri denetleyicisi API 'sine kimlik doğrulaması yapmak için kullanılır.  Güvenli bir parola seçin ve yalnızca küme yöneticisi ayrıcalıklarına sahip olmaları gereken kişilerle paylaşabilirsiniz.

Bir Kubernetes parolası, Kullanıcı adı ve diğeri parola için Base64 kodlamalı dize olarak depolanır.

İstediğiniz kullanıcı adınızı ve parolanızı Base64 olarak kodlamak için çevrimiçi bir araç kullanabilir veya platformunuza bağlı olarak yerleşik CLı araçlarını kullanabilirsiniz.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Kullanıcı adını ve parolayı kodladıktan sonra [şablon dosyasını](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) temel alan bir dosya oluşturabilir ve Kullanıcı adı ve parola değerlerini kendi değerlerinizle değiştirin.

Ardından, parolayı oluşturmak için aşağıdaki komutu çalıştırın.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Veri denetleyicisi oluşturma

Artık veri denetleyicisinin kendisini oluşturmaya hazırsınız.

İlk olarak, bazı ayarları değiştirebilmeniz için, bilgisayarınızda yerel olarak [şablon dosyasının](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) bir kopyasını oluşturun.

Gerektiğinde aşağıdakileri düzenleyin:

**Gerekli**
- **konum**: bunu, veri denetleyicisi hakkındaki _meta_ verilerin depolanacağı Azure konumu olacak şekilde değiştirin.  Kullanılabilir Azure konumlarının listesini, [veri denetleyicisine genel bakış](create-data-controller.md) makalesinde görebilirsiniz.
- **resourceGroup**: Azure Resource Manager veri denetleyicisi Azure kaynağı oluşturmak istediğiniz Azure Kaynak grubu.  Genellikle bu kaynak grubu zaten var olmalıdır, ancak verileri Azure 'a yüklediğiniz zamana kadar gerekli değildir.
- **abonelik**: içinde Azure kaynaklarını oluşturmak Istediğiniz aboneliğin Azure abonelik GUID 'i.

**GÖZDEN GEÇIRMENIZ VE MUHTEMELEN VARSAYıLANLARı DEĞIŞTIRMENIZ ÖNERILIR**
- **depolama.. className**: veri denetleyicisi verileri ve günlük dosyaları için kullanılacak depolama sınıfı.  Kubernetes kümenizdeki kullanılabilir depolama sınıflarından emin değilseniz, şu komutu çalıştırabilirsiniz: `kubectl get storageclass` .  Varsayılan değer, `default` var olan ve `default`  varsayılan olan bir depolama sınıfı olan adlı bir depolama sınıfı olduğunu varsaymaktadır.  Note: veri için bir tane olmak üzere istenen depolama sınıfına ayarlanacak iki className ayarı vardır.
- **serviceType**: bir LoadBalancer kullanmıyorsanız, hizmet türünü olarak değiştirin `NodePort` .  Note: değiştirilmesi gereken iki serviceType ayarı vardır.
- Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformunda, veri denetleyicisini oluşturmadan önce güvenlik bağlamı kısıtlamasını uygulamanız gerekir. [OpenShift üzerinde Azure Arc etkin veri Hizmetleri için bir güvenlik bağlamı kısıtlaması uygulama](how-to-apply-security-context-constraint.md)konusundaki yönergeleri izleyin.
- **Güvenlik** Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformu için, `security:` ayarları Data Controller YAML dosyasındaki aşağıdaki değerlerle değiştirin. 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**SEÇIM**
- **ad**: veri denetleyicisinin varsayılan adı, `arc` ancak isterseniz bunu değiştirebilirsiniz.
- **DisplayName**: bunu, dosyanın en üstündeki ad özniteliğiyle aynı değere ayarlayın.
- **kayıt defteri**: Microsoft Container Registry varsayılandır.  Microsoft Container Registry görüntüleri çekiliyor ve [bunları özel bir kapsayıcı kayıt defterine](offline-deployment.md)alıyorsa, kayıt defterinizin IP ADRESINI veya DNS adını buraya girin.
- **Dockerregistry**: gerekirse özel bir kapsayıcı kayıt defterinden görüntüleri çekmek için kullanılacak görüntü çekme gizli dizisi.
- **Depo**: Microsoft Container Registry varsayılan depo `arcdata` .  Özel bir kapsayıcı kayıt defteri kullanıyorsanız, Azure Arc etkin veri Hizmetleri kapsayıcı görüntülerini içeren klasör/deponun yolunu girin.
- **ImageTag**: geçerli en son sürüm etiketi şablonda varsayılan olarak ayarlanır, ancak eski bir sürümü kullanmak istiyorsanız bunu değiştirebilirsiniz.

Aşağıdaki örnek, tamamlanmış bir Data Controller YAML dosyasını göstermektedir. Gereksinimlerinize ve yukarıdaki bilgilere göre ortamınızın örneğini güncelleştirin.

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Düzenlenmiş dosyayı yerel bilgisayarınıza kaydedin ve veri denetleyicisini oluşturmak için aşağıdaki komutu çalıştırın:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Oluşturma durumunu izleme

Denetleyicinin oluşturulması tamamlanması birkaç dakika sürer. İlerleme durumunu başka bir Terminal penceresinde aşağıdaki komutlarla izleyebilirsiniz:

> [!NOTE]
>  Aşağıdaki örnek komutlar, adında bir veri denetleyicisi ve Kubernetes ad alanı oluşturduğunuzu varsayar `arc` .  Farklı bir ad alanı/veri denetleyicisi adı kullandıysanız, `arc` adını adıyla değiştirebilirsiniz.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Ayrıca, aşağıdaki gibi bir komut çalıştırarak belirli bir pod 'un oluşturma durumunu da denetleyebilirsiniz.  Bu, özellikle herhangi bir sorunu gidermek için kullanışlıdır.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Azure Data Studio için Azure Arc uzantısı, Azure Arc etkin Kubernetes ayarlama ve bunu örnek bir SQL yönetilen örnek YAML dosyası içeren bir Git deposunu izlemek üzere yapılandırma deneyiminde size yol gösterecek bir not defteri sağlar. Her şey bağlıyken, Kubernetes kümenize yeni bir SQL yönetilen örnek dağıtılır.

Azure Data Studio için Azure Arc uzantısında **Azure Arc etkin Kubernetes ve Flox Not defterini kullanarak SQL yönetilen örneği dağıtma** konusuna bakın.

## <a name="troubleshooting-creation-problems"></a>Oluşturma sorunlarını giderme

Oluşturma ile herhangi bir sorun yaşarsanız, lütfen [sorun giderme kılavuzuna](troubleshoot-guide.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Kubernetes-Native araçları kullanarak bir SQL yönetilen örneği oluşturma](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Kubernetes-Native araçlarını kullanarak bir PostgreSQL hiper ölçek sunucu grubu oluşturma](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
