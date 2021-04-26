---
title: 'Hızlı başlangıç: Azure CLı kullanarak bir AKS kümesini gizli bilgi işlem düğümleri ile dağıtma'
description: Gizli düğümlere sahip bir Azure Kubernetes hizmeti (AKS) kümesi oluşturmayı ve Azure CLı kullanarak Merhaba Dünya uygulaması dağıtmayı öğrenin.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: b1ef397c1a3f6770d197ca8fd0faa83c9e8360f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816467"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak bir AKS kümesini gizli bilgi işlem düğümleri ile dağıtma

Bu hızlı başlangıçta, bir Azure Kubernetes hizmeti (AKS) kümesini gizli bilgi işlem (DCsv2) düğümleri ile dağıtmak için Azure CLı 'yi kullanacaksınız. Daha sonra bir kuşada basit bir Merhaba Dünya uygulaması çalıştıracaksınız. Ayrıca, bir küme temin edebilir ve Azure portal gizli bilgi işlem düğümleri ekleyebilirsiniz, ancak bu hızlı başlangıç Azure CLı 'ye odaklanır.

AKS, geliştiricilerin veya küme işleçlerinin kümeleri hızlı bir şekilde dağıtmasını ve yönetmesini sağlayan, yönetilen bir Kubernetes hizmetidir. Daha fazla bilgi edinmek için [aks özel](../aks/intro-kubernetes.md) [düğümlerine genel bakış](confidential-nodes-aks-overview.md)konusunu okuyun.

Gizli bilgi işlem düğümlerinin özellikleri şunlardır:

- Linux kapsayıcılarını destekleyen Linux çalışan düğümleri.
- Ubuntu 18,04 VM düğümleriyle 2. nesil sanal makine (VM).
- Şifrelenmiş sayfa önbelleği belleğini (EPC) kullanarak, Kapsayıcılarınızı gizli korumalı bir biçimde çalıştırmaya yardımcı olmak için Intel SGX özellikli CPU. Daha fazla bilgi için bkz. [Azure gizli bilgi Işlem hakkında sık sorulan sorular](./faq.md).
- Gizli bilgi işlem düğümlerine önceden yüklenmiş Intel SGX DCAP sürücüsü. Daha fazla bilgi için bkz. [Azure gizli bilgi Işlem hakkında sık sorulan sorular](./faq.md).

> [!NOTE]
> DCsv2 VM 'Ler, daha yüksek fiyatlandırma ve bölge kullanılabilirliğine tabi olan özel donanımlar kullanır. Daha fazla bilgi için bkz. [kullanılabilir SKU 'lar ve desteklenen bölgeler](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

- Etkin bir Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dağıtım makinenizde Azure CLı sürüm 2.0.64 veya üzeri yüklendi ve yapılandırıldı. 

  Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](../container-registry/container-registry-get-started-azure-cli.md).
- Aboneliğinizde en az altı DCsv2 çekirdek kullanılabilir. 

  Varsayılan olarak, Azure aboneliği başına gizli bilgi işlem kotası sekiz VM çekirdeğine sahiptir. Sekiz taneden fazla çekirdek gerektiren bir küme sağlamayı düşünüyorsanız, bir kota artışı bileti yükseltmek için [Bu yönergeleri](../azure-portal/supportability/per-vm-quota-requests.md) izleyin.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Gizli bilgi işlem düğümleri ve eklentisi ile bir AKS kümesi oluşturma

Gizli bilgi işlem eklentisi etkinken bir AKS kümesi oluşturmak için aşağıdaki yönergeleri kullanın, kümeye bir düğüm havuzu ekleyin ve oluşturduğunuz öğeleri doğrulayın.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Sistem düğüm havuzu ile AKS kümesi oluşturma

> [!NOTE]
> Daha önce listelenen önkoşul ölçütlerini karşılayan bir AKS kümeniz zaten varsa, bir gizli bilgi işlem düğüm havuzu eklemek için [sonraki bölüme atlayın](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) .

İlk olarak, [az Group Create][az-group-create] komutunu kullanarak küme için bir kaynak grubu oluşturun. Aşağıdaki örnek *westus2* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Şimdi, [az aks Create][az-aks-create] komutunu kullanarak, gizli bilgi işlem eklentisi etkinken bir aks kümesi oluşturun:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>AKS kümesine gizli bilgi işlem özelliklerine sahip bir Kullanıcı düğümü havuzu ekleme 

AKS kümesine üç düğüme sahip bir Kullanıcı düğümü havuzu eklemek için aşağıdaki komutu çalıştırın `Standard_DC2s_v2` . [Desteklenen DCsv2 SKU 'ları ve bölgeleri listesinden](../virtual-machines/dcv2-series.md)başka bir SKU seçebilirsiniz.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Komutu çalıştırdıktan sonra, DCsv2 ile yeni bir düğüm havuzu, gizli bilgi işlem eklentisi DaemonSets ([SGX cihaz eklentisi](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)) ile görünür olmalıdır.

### <a name="verify-the-node-pool-and-add-on"></a>Düğüm havuzunu ve eklentiyi doğrulama

[Az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak aks kümeniz için kimlik bilgilerini alın:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl get pods`Düğümlerin düzgün bir şekilde oluşturulduğunu ve SGX ile Ilgili DaemonSets DCsv2 düğüm havuzlarında çalıştığını doğrulamak için komutunu kullanın:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Çıkış yukarıdaki kodla eşleşiyorsa, AKS kümeniz artık gizli uygulamaları çalıştırmaya hazırdır.

Bir şifreleme ortamında uygulamayı test etmek için bu hızlı başlangıçta [yalıtılmış bir şifreleme uygulamasına Merhaba Dünya dağıt](#hello-world) bölümüne gidebilirsiniz. Veya AKS 'e daha fazla düğüm havuzu eklemek için aşağıdaki yönergeleri kullanın. (AKS, SGX düğüm havuzlarını ve SGX olmayan düğüm havuzlarını karıştırmaya destekler.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Mevcut bir AKS kümesine gizli bilgi işlem düğümü havuzu ekleme<a id="existing-cluster"></a>

Bu bölümde, bu hızlı başlangıçta daha önce listelenen önkoşul ölçütlerini karşılayan bir AKS kümesi zaten çalıştırıyorsunuz demektir.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Mevcut kümede gizli bilgi işlem AKS eklentisini etkinleştirin

Gizli bilgi işlem eklentisini etkinleştirmek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Kümeye DCsv2 Kullanıcı düğümü havuzu ekleme

> [!NOTE]
> Gizli bilgi işlem özelliğini kullanmak için, mevcut AKS kümenizin bir DCsv2 VM SKU 'SU temel alan en az bir düğüm havuzuna sahip olması gerekir. DC 'ler-v2 VM SKU 'Ları hakkında daha fazla bilgi edinmek için bkz. [kullanılabilir SKU 'lar ve desteklenen bölgeler](virtual-machine-solutions.md).

Düğüm havuzu oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

*Confcompool1* adlı yeni düğüm havuzunun oluşturulduğunu doğrulayın:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>DaemonSets 'in gizli düğüm havuzlarında çalıştığını doğrulama

Aşağıdaki doğrulamayı gerçekleştirmek için mevcut AKS kümenizde oturum açın:

```console
kubectl get nodes
```

Çıktı, AKS kümesinde yeni eklenen *confcompool1* havuzunu göstermelidir. Diğer DaemonSets de görebilirsiniz.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Çıkış yukarıdaki kodla eşleşiyorsa, AKS kümeniz artık gizli uygulamaları çalıştırmaya hazırdır. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Yalıtılmış bir şifreleme uygulamasından Merhaba Dünya dağıtma <a id="hello-world"></a>
Şimdi bir test uygulaması dağıtmaya hazırsınız. 

*Hello-World-Enclave. YAML* adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın. Bu örnek uygulama kodunu [Açık şifreleme projesinde](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)bulabilirsiniz. Bu dağıtım, *confcom* eklentisini dağıttığınızı varsayar.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Şimdi `kubectl apply` Aşağıdaki örnek çıktıda gösterildiği gibi güvenli bir kuşda açılacak örnek bir iş oluşturmak için komutunu kullanın:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Aşağıdaki komutları çalıştırarak iş yükünün güvenilir bir yürütme ortamını (Enclave) başarıyla oluşturduğunu doğrulayabilirsiniz:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz gizli bilgi işlem düğüm havuzunu kaldırmak için aşağıdaki komutu kullanın: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

AKS kümesini silmek için aşağıdaki komutu kullanın: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub 'daki gizli kapsayıcı örneklerini](https://github.com/Azure-Samples/confidential-container-samples)kullanarak, Python, Node veya diğer uygulamaları gizli kapsayıcılar aracılığıyla çalıştırın.

* [GitHub 'daki şifreli ve uyumlu Azure Container örneklerini](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)kullanarak şifreleme kullanan uygulamaları çalıştırın.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
