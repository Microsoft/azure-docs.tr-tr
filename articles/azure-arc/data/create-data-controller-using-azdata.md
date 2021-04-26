---
title: Azure Data CLı (azdata) kullanarak veri denetleyicisi oluşturma
description: Azure Data CLı (azdata) kullanarak zaten oluşturduğunuz tipik bir çok düğümlü Kubernetes kümesinde Azure Arc veri denetleyicisi oluşturun.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: f7bc90f2748d230ad50868cff5d7a8f7b69d850a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029548"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Azure Arc veri denetleyicisi 'ni kullanarak oluşturma [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Genel bakış bilgileri için [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md) konusunu gözden geçirin.

Azure Arc veri denetleyicisi 'ni kullanarak oluşturmak için, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] yüklü olmalıdır.

   [Uygulamasını [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Seçtiğiniz hedef platformdan bağımsız olarak, veri denetleyicisi Yönetici kullanıcısı için oluşturmadan önce aşağıdaki ortam değişkenlerini ayarlamanız gerekir. Bu kimlik bilgilerini, gerektiğinde veri denetleyicisine yönetici erişiminin olması gereken diğer kişilere sağlayabilirsiniz.

**AZDATA_USERNAME** -veri denetleyicisi Yönetici kullanıcısı için tercih ettiğiniz Kullanıcı adı. Örnek: `arcadmin`

**AZDATA_PASSWORD** -veri denetleyicisi Yönetici kullanıcısı için tercih ettiğiniz bir parola. Parola en az sekiz karakter uzunluğunda olmalı ve şu dört kümeden üçünden karakterler içermelidir: büyük harfler, küçük harfler, rakamlar ve semboller.

### <a name="linux-or-macos"></a>Linux veya macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Azure Arc veri denetleyicisi oluşturmaya başlamadan önce bir Kubernetes kümesine bağlanıp kimlik doğrulaması yapmanız ve var olan bir Kubernetes bağlamının seçili olması gerekir. Bir Kubernetes kümesine veya hizmetine nasıl bağlanıyorsunuz değişir. Kubernetes API sunucusuna bağlanmak için kullandığınız Kubernetes dağıtımı veya hizmetine yönelik belgelere bakın.

Geçerli bir Kubernetes bağlantısına sahip olup olmadığınızı ve aşağıdaki komutlarla geçerli bağlamınızı onaylayabilirsiniz.

```console
kubectl get namespace
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturma

> [!NOTE]
> `--namespace`Aşağıdaki örneklerde azdata Arc DC Create komutunun parametresi için farklı bir değer kullanabilirsiniz, ancak `--namespace parameter` aşağıdaki diğer tüm komutlarda için bu ad alanı adını kullandığınızdan emin olun.

- [Azure Kubernetes hizmeti (AKS) üzerinde oluştur](#create-on-azure-kubernetes-service-aks)
- [Azure Stack hub 'da AKS altyapısında oluşturma](#create-on-aks-engine-on-azure-stack-hub)
- [Azure Stack CI üzerinde AKS üzerinde oluşturma](#create-on-aks-on-azure-stack-hci)
- [Azure Red Hat OpenShift (ARO) üzerinde oluştur](#create-on-azure-red-hat-openshift-aro)
- [Red Hat OpenShift kapsayıcı platformu (OCP) üzerinde oluştur](#create-on-red-hat-openshift-container-platform-ocp)
- [Açık kaynak üzerinde oluşturma, yukarı akış Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [AWS elastik Kubernetes hizmeti (EKS) üzerinde oluştur](#create-on-aws-elastic-kubernetes-service-eks)
- [Google Cloud Kubernetes altyapı hizmeti (GKE) üzerinde oluştur](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) üzerinde oluştur

Varsayılan olarak, AKS dağıtım profili `managed-premium` depolama sınıfını kullanır. `managed-premium`Depolama sınıfı yalnızca, Premium disklere sahıp VM görüntüleri kullanılarak dağıtılan VM 'ler varsa çalışır.

`managed-premium`Depolama sınıfınız olarak kullanacaksanız, veri denetleyicisi oluşturmak için aşağıdaki komutu çalıştırabilirsiniz. Komutta yer tutucuları, kaynak grubu adı, abonelik KIMLIĞI ve Azure konumuyla değiştirin.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Hangi depolama sınıfının kullanılacağı konusunda emin değilseniz, `default` KULLANDıĞıNıZ VM türünden bağımsız olarak desteklenen depolama sınıfını kullanmanız gerekir. Yalnızca en hızlı performansı sağlamaz.

`default`Depolama sınıfını kullanmak istiyorsanız şu komutu çalıştırabilirsiniz:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Azure Stack hub 'da AKS altyapısında oluşturma

Varsayılan olarak, dağıtım profili `managed-premium` depolama sınıfını kullanır. `managed-premium`Depolama sınıfı yalnızca Azure Stack Hub üzerinde Premium disklere sahıp VM görüntüleri kullanılarak dağıtılan çalışan sanal makineleriniz varsa çalışır.

Yönetilen-Premium Depolama sınıfını kullanarak veri denetleyicisi oluşturmak için aşağıdaki komutu çalıştırabilirsiniz:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Hangi depolama sınıfının kullanılacağı konusunda emin değilseniz, `default` KULLANDıĞıNıZ VM türünden bağımsız olarak desteklenen depolama sınıfını kullanmanız gerekir. Azure Stack hub 'ında, Premium diskler ve Standart diskler aynı depolama altyapısı tarafından desteklenir. Bu nedenle, aynı genel performansı, ancak farklı ıOPS limitleriyle sağlaması beklenir.

`default`Depolama sınıfını kullanmak istiyorsanız, bu komutu çalıştırabilirsiniz.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack CI üzerinde AKS üzerinde oluşturma

Varsayılan olarak, dağıtım profili, adlı bir depolama sınıfı `default` ve hizmet türü kullanır `LoadBalancer` .

`default`Depolama sınıfı ve hizmet türünü kullanarak veri denetleyicisi oluşturmak için aşağıdaki komutu çalıştırabilirsiniz `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.


### <a name="create-on-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) üzerinde oluştur

Azure Red Hat OpenShift bir güvenlik bağlamı kısıtlaması gerektirir.

#### <a name="apply-the-security-context"></a>Güvenlik bağlamını uygulama

Azure Red Hat OpenShift üzerinde veri denetleyicisi oluşturmadan önce, belirli güvenlik bağlamı kısıtlamalarını (SCC) uygulamanız gerekir. Önizleme sürümü için bu, güvenlik kısıtlamalarını daha rahat hale getiren. Gelecekteki yayınlar, güncelleştirilmiş SCC sağlayacak.

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>Özel dağıtım profili oluştur

`azure-arc-azure-openshift`Açık kaydırma Için Azure RedHat profilini kullanın.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Veri denetleyicisi oluşturma

Veri denetleyicisini oluşturmak için aşağıdaki komutu çalıştırabilirsiniz:

> [!NOTE]
> Burada ve yukarıdaki komutlarda aynı ad alanını kullanın `oc adm policy add-scc-to-user` . Örnek `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OpenShift kapsayıcı platformu (OCP) üzerinde oluştur

> [!NOTE]
> Azure 'da Red Hat OpenShift kapsayıcı platformu kullanıyorsanız, kullanılabilir en son sürümü kullanmanız önerilir.

Red Hat OCP üzerinde veri denetleyicisi oluşturmadan önce, belirli güvenlik bağlamı kısıtlamalarını uygulamanız gerekir. 

#### <a name="apply-the-security-context-constraint"></a>Güvenlik bağlamı kısıtlamasını Uygula

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>Depolama sınıfını belirleme

Ayrıca, aşağıdaki komutu çalıştırarak hangi depolama sınıfının kullanılacağını belirlemeniz gerekir.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Özel dağıtım profili oluştur

Aşağıdaki komutu çalıştırarak dağıtım profilini temel alan yeni bir özel dağıtım profili dosyası oluşturun `azure-arc-openshift` . Bu komut, `custom` geçerli çalışma dizininizde ve bu dizindeki özel bir dağıtım profili dosyasında bir dizin oluşturur `control.json` .

`azure-arc-openshift`OpenShift kapsayıcı platformu profilini kullanın.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Depolama sınıfını ayarla 

Şimdi, `<storageclassname>` aşağıdaki komutta komutunu çalıştırarak, kullanmak istediğiniz depolama sınıfının adı ile aşağıdaki komutu değiştirerek istenen depolama sınıfını ayarlayın `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>LoadBalancer ayarla (isteğe bağlı)

Varsayılan olarak, `azure-arc-openshift` dağıtım profili `NodePort` hizmet türü olarak kullanılır. Yük Dengeleyici ile tümleştirilmiş bir OpenShift kümesi kullanıyorsanız, `LoadBalancer` aşağıdaki komutu kullanarak yapılandırmayı hizmet türünü kullanacak şekilde değiştirebilirsiniz:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Güvenlik ilkelerini doğrulama

OpenShift kullanırken, OpenShift 'te varsayılan güvenlik ilkeleriyle çalıştırmak veya genellikle ortamı normalden daha uzun bir şekilde kilitlemek istemeniz gerekebilir. İsteğe bağlı olarak, aşağıdaki komutları çalıştırarak dağıtım zamanında ve çalışma zamanında gereken izinleri en aza indirmek için bazı özellikleri devre dışı bırakmayı tercih edebilirsiniz.

Bu komut, pods ile ilgili ölçüm koleksiyonlarını devre dışı bırakır. Bu özelliği devre dışı bıraktığınızda Grafana panolarında Pod ile ilgili ölçümleri göremezsiniz. Varsayılan değer doğru değeridir.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Bu komut, düğümler hakkında ölçüm koleksiyonlarını devre dışı bırakır. Bu özelliği devre dışı bıraktığınızda Grafana panolarındaki düğümler hakkında ölçümleri göremezsiniz. Varsayılan değer doğru değeridir.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Bu komut, sorun giderme amacıyla bellek dökümlerini alma özelliğini devre dışı bırakır.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Veri denetleyicisi oluşturma

Artık aşağıdaki komutu kullanarak veri denetleyicisi oluşturmaya hazırsınız.

> [!NOTE]
>   Burada ve yukarıdaki komutlarda aynı ad alanını kullanın `oc adm policy add-scc-to-user` . Örnek `arc` .

> [!NOTE]
>   Parametresi, dosyasında `--path` control.jsdeğil, dosyadaki control.jsiçeren _dizine_ işaret etmelidir.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Açık kaynak üzerinde oluşturma, yukarı akış Kubernetes (kubeadm)

Varsayılan olarak, kubeadm dağıtım profili, adlı bir depolama sınıfı `local-storage` ve hizmet türü kullanır `NodePort` . Bu kabul edilebilir ise, aşağıdaki yönergeleri atlayıp istenen depolama sınıfı ve hizmet türünü ayarlayın ve hemen `azdata arc dc create` komutu çalıştırın.

Dağıtım profilinizi belirli bir depolama sınıfı ve/veya hizmet türü belirtmek için özelleştirmek istiyorsanız, aşağıdaki komutu çalıştırarak kubeadm dağıtım profilini temel alan yeni bir özel dağıtım profili dosyası oluşturarak başlayın. Bu komut, `custom` geçerli çalışma dizininizde ve bu dizindeki özel bir dağıtım profili dosyasında bir dizin oluşturur `control.json` .

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Aşağıdaki komutu çalıştırarak kullanılabilir depolama sınıflarını arayabilirsiniz.

```console
kubectl get storageclass
```

Şimdi, `<storageclassname>` aşağıdaki komutta komutunu çalıştırarak, kullanmak istediğiniz depolama sınıfının adı ile aşağıdaki komutu değiştirerek istenen depolama sınıfını ayarlayın `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Varsayılan olarak, kubeadm dağıtım profili `NodePort` hizmet türü olarak kullanılır. Yük Dengeleyici ile tümleştirilmiş bir Kubernetes kümesi kullanıyorsanız, yapılandırmayı aşağıdaki komutu kullanarak değiştirebilirsiniz.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Artık aşağıdaki komutu kullanarak veri denetleyicisi oluşturmaya hazırsınız.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>AWS elastik Kubernetes hizmeti (EKS) üzerinde oluştur

Varsayılan olarak, EKS depolama sınıfı `gp2` ve hizmet türüdür `LoadBalancer` .

Veri denetleyicisini, belirtilen EKS dağıtım profilini kullanarak oluşturmak için aşağıdaki komutu çalıştırın.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Google Cloud Kubernetes altyapı hizmeti (GKE) üzerinde oluştur

Varsayılan olarak, GKE depolama sınıfı `standard` ve hizmet türüdür `LoadBalancer` .

Veri denetleyicisini, belirtilen GKE dağıtım profilini kullanarak oluşturmak için aşağıdaki komutu çalıştırın.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Komutu çalıştırdığınızda, [oluşturma durumunu izlemek](#monitoring-the-creation-status)için devam edin.

## <a name="monitoring-the-creation-status"></a>Oluşturma durumunu izleme

Denetleyicinin oluşturulması tamamlanması birkaç dakika sürer. İlerleme durumunu başka bir Terminal penceresinde aşağıdaki komutlarla izleyebilirsiniz:

> [!NOTE]
>  Aşağıdaki örnek komutlar, adında bir veri denetleyicisi ve Kubernetes ad alanı oluşturduğunuzu varsayar `arc` . Farklı bir ad alanı/veri denetleyicisi adı kullandıysanız, `arc` adını adıyla değiştirebilirsiniz.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Ayrıca, aşağıdaki gibi bir komut çalıştırarak belirli bir pod 'un oluşturma durumunu da denetleyebilirsiniz. Bu, özellikle herhangi bir sorunu gidermek için kullanışlıdır.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Oluşturma sorunlarını giderme

Oluşturma ile herhangi bir sorun yaşarsanız, [sorun giderme kılavuzuna](troubleshoot-guide.md)bakın.