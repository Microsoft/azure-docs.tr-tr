---
title: "Azure’da Kubernetes kümesi hızlı başlangıç | Microsoft Docs"
description: "Azure Container Service’te Kubernetes kümesi dağıtma ve kullanmaya başlama"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e4f47341554e2de514c8be2f5c85983d09bbb760
ms.lasthandoff: 04/03/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Kapsayıcı Hizmetinde Kubernetes kümesine başlangıç


Bu makaledeki yönergeler, Azure CLI 2.0 komutlarını kullanarak Kubernetes kümesi oluşturmayı gösterir. Daha sonra `kubectl` komut satırı aracını kullanarak kümedeki kapsayıcılarla çalışmaya başlayabilirsiniz.

Aşağıdaki görüntüde bir ana ve iki aracı düğüme sahip kapsayıcı hizmeti kümesinin mimarisi gösterilmektedir. Ana düğüm Kubernetes REST API işlevi görür. Aracı düğümleri Azure kullanılabilirlik kümesinde gruplandırılır ve kapsayıcılarınızı çalıştırır. Tüm sanal makineler aynı gizli sanal ağ üzerindedir ve birbirlerine tam olarak erişilebilir.

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Ön koşullar
Bu kılavuzda [Azure CLI v. 2.0](/cli/azure/install-az-cli2) bileşenini yüklediğiniz ve ayarladığınız varsayılır. Ayrıca, `~/.ssh/id_rsa.pub` konumunda bir SSH RSA ortak anahtarınız da olmalıdır. Bu anahtara sahip değilseniz [OS X ve Linux](../virtual-machines/linux/mac-create-ssh-keys.md) veya [Windows](../virtual-machines/linux/ssh-from-windows.md)’a yönelik adımlara bakın.






## <a name="create-your-kubernetes-cluster"></a>Kubernetes kümenizi oluşturma

Aşağıda Azure CLI 2.0 ile kümenizi oluşturmak için kullanabileceğiniz kısa bir kabuk komutları listesi sağlanmıştır. Daha fazla bilgi edinmek için bkz. [Azure CLI 2.0 ile bir Azure Container Service kümesi oluşturma](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kümenizi oluşturmak için ilk olarak belirli bir konumda bir kaynak grubu oluşturmanız gerekir. Aşağıdaki gibi komutlar çalıştırın:

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Küme oluşturma
Bir kaynak grubu oluşturduktan sonra bu grupta bir küme oluşturabilirsiniz:

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Dağıtım sırasında, `~/.ssh/id_rsa.pub` dosyası CLI tarafından Linux sanal makinelerine yüklenir.
>

Bu komut tamamlandıktan sonra çalışan bir Kubernetes kümesine sahip olursunuz.

### <a name="connect-to-the-cluster"></a>Kümeye bağlanma

İstemci bilgisayarınızdan Kubernetes’in komut satırı istemcisi olan `kubectl` ile Kubernetes kümesine bağlanmak için kullanabileceğiniz Azure CLI komutları aşağıda verilmiştir. Daha fazla bilgi için bkz. [Azure Container Service kümesine bağlanma](container-service-connect.md).

`kubectl` henüz yüklenmediyse şununla yükleyebilirsiniz:

```console
az acs kubernetes install-cli
```

`kubectl` yüklendikten sonra aşağıdaki komutu çalıştırarak ana Kubernetes kümesinin yapılandırmasını ~/.kube/config dosyasına indirin:

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Bu noktada makinenizden kümenize erişmeye hazır olmanız gerekir. Şunu çalıştırmayı deneyin:
```console
kubectl get nodes
```

Kümenizdeki bir makine listesi görebildiğinizi doğrulayın.

## <a name="create-your-first-kubernetes-service"></a>İlk Kubernetes hizmetinizi oluşturma

Bu kılavuzu tamamladıktan sonra şunları öğrenmiş olacaksınız:
 * Docker uygulaması dağıtma ve genel kullanıma sunma
 * `kubectl exec` kullanarak bir kapsayıcıdaki komutları çalıştırma 
 * Kubernetes panosuna erişme

### <a name="start-a-simple-container"></a>Basit bir kapsayıcı başlatma
Aşağıdaki komutu çalıştırarak basit bir kapsayıcı (bu örnekte Nginx web sunucusu) çalıştırabilirsiniz:

```console
kubectl run nginx --image nginx
```

Bu komut, düğümlerin birindeki bir pod içinde Nginx Docker kapsayıcısını başlatır.

Çalıştırma kapsayıcısını görmek için şu komutu çalıştırın:

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Hizmeti genel kullanıma sunma
Hizmeti genel kullanıma sunmak için `LoadBalancer` türünde bir Kubernetes `Service` oluşturun:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Bu işlem, Kubernetes’in genel bir IP adresi ile Azure Load Balancer kuralı oluşturmasına neden olur. Değişikliğin yük dengeleyiciye yayılması yaklaşık birkaç dakika sürer. Daha fazla bilgi edinmek için bkz. [Azure Container Service’teki bir Kubernetes kümesinde yük dengeleme kapsayıcıları](container-service-kubernetes-load-balancing.md).

Hizmetin `pending` durumundan çıkarak harici bir IP adresi görüntülemesini sağlamak için aşağıdaki komutu çalıştırın:

```console
watch 'kubectl get svc'
```

  ![Bekliyor durumundan harici IP adresine geçişi izleme görüntüsü](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Harici IP adresini gördükten sonra tarayıcınızdan bu adrese gidebilirsiniz:

  ![Nginx’e göz atma görüntüsü](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes Kullanıcı Arabirimini Göz Atma
Kubernetes web arabirimini görmek için şunu kullanabilirsiniz:

```console
kubectl proxy
```
Bu komut [http://localhost:8001/ui](http://localhost:8001/ui) üzerinde çalışan Kubernetes web kullanıcı arabirimini görüntülemek için kullanabileceğiniz basit bir kimliği doğrulanmış proxy çalıştırır. Daha fazla bilgi edinmek için bkz: [Kubernetes web kullanıcı arabirimini Azure Container Service ile kullanma](container-service-kubernetes-ui.md).

![Kubernetes panosunun görüntüsü](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Kapsayıcılarınızın içindeki uzak oturumlar
Kubernetes, komutları kümenizde çalışan uzak bir Docker kapsayıcısında çalıştırmanıza olanak tanır.

```console
# Get the name of your nginx pods
kubectl get pods
```

Pod adınızı kullanarak, pod üzerinde bir uzak komutu çalıştırabilirsiniz.  Örneğin:

```console
kubectl exec <pod name> date
```

Ayrıca `-it` bayraklarını kullanarak tam etkileşimli bir oturum elde edebilirsiniz:

```console
kubectl exec <pod name> -it bash
```

![Bir kapsayıcı içinde uzak oturum](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Sonraki adımlar

Kubernetes kümenizle daha fazlasını yapmak için aşağıdaki kaynaklara bakın:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) - kapsayıcılı uygulamalar için dağıtma, ölçeklendirme, güncelleştirme ve hata ayıklama işlemlerini gösterir.
* [Kubernetes Kullanıcı Kılavuzu](http://kubernetes.io/docs/user-guide/) - var olan bir Kubernetes kümesindeki çalışan programlar hakkında bilgi sağlar.
* [Kubernetes Örnekleri](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes ile gerçek uygulama çalıştırmaya ilişkin örnekler sunar.

