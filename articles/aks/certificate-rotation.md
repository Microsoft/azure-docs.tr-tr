---
title: Azure Kubernetes Service (AKS) içinde sertifikaları döndürme
description: Sertifikalarınızı bir Azure Kubernetes hizmeti (AKS) kümesinde döndürme hakkında bilgi edinin.
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776024"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde sertifikaları döndürme

Azure Kubernetes hizmeti (AKS), birçok bileşeni ile kimlik doğrulaması için sertifikalar kullanır. Düzenli olarak, güvenlik veya ilke nedenleriyle bu sertifikaları döndürmeniz gerekebilir. Örneğin, tüm sertifikalarınızı her 90 günde bir döndürebilmeniz için bir ilkeniz olabilir.

Bu makalede, AKS kümenizdeki sertifikaların nasıl döndürübir gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2.0.77 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS sertifikaları, sertifika yetkilileri ve hizmet hesapları

AKS aşağıdaki sertifikaları, sertifika yetkililerini ve hizmet hesaplarını oluşturur ve kullanır:

* AKS API sunucusu, küme CA 'sı adlı bir sertifika yetkilisi (CA) oluşturur.
* API sunucusunda, API sunucusundan kubeyalar 'a tek yönlü iletişim için sertifikalar imzalayan bir küme CA 'sı vardır.
* Her kubelet aynı zamanda, API sunucusuna kubelet 'ten iletişim sağlamak için, küme CA 'sı tarafından imzalanan bir sertifika Imzalama Isteği (CSR) oluşturur.
* API toplayıcısı, diğer API 'lerle iletişim için sertifikalar vermek üzere Küme CA 'sını kullanır. API toplayıcısı 'nın bu sertifikaları vermek için kendi CA 'sı da olabilir, ancak şu anda küme CA 'sını kullanır.
* Her düğüm, küme CA 'sı tarafından imzalanmış bir hizmet hesabı (SA) belirteci kullanır.
* `kubectl`İstemcinin AKS kümesiyle iletişim kurmak için bir sertifikası vardır.

> [!NOTE]
> Mart 2019 ' den önce oluşturulan AKS kümelerinde iki yıl sonra süre sonu olan sertifikalar vardır. Mart 2019 ' den sonra oluşturulan herhangi bir küme veya sertifikaları döndürülen herhangi bir küme, 30 yıl sonra süresi dolan küme CA sertifikalardır. Diğer tüm sertifikaların süreleri iki yıl sonra doluyor. Kümenizin oluşturulduğunu doğrulamak için, `kubectl get nodes` düğüm havuzlarınızın *yaşını* görmek üzere kullanın.
> 
> Ayrıca, kümenizin sertifikasının sona erme tarihini kontrol edebilirsiniz. Örneğin, aşağıdaki Bash komutu *Myakscluster* kümesi için sertifika ayrıntılarını görüntüler.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Küme sertifikalarınızı döndürme

> [!WARNING]
> Kullanarak sertifikalarınızı döndürme `az aks rotate-certs` , AKS kümeniz için 30 dakikalık kapalı kalma süresine kadar zaman alabilir.

AKS kümenizde oturum açmak için [az aks Get-Credentials][az-aks-get-credentials] kullanın. Bu komut ayrıca `kubectl` Yerel makinenizde istemci sertifikasını indirir ve yapılandırır.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

`az aks rotate-certs`Kümenizdeki tüm sertifikaları, CA 'ları ve SAS 'yi döndürmek için kullanın.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Tamamlanması 30 dakika sürebilir `az aks rotate-certs` . Komut tamamlanmadan önce başarısız olursa, `az aks show` kümenin durumunun *sertifika döndürme* olduğunu doğrulamak için kullanın. Küme başarısız durumdaysa, `az aks rotate-certs` sertifikalarınızı yeniden döndürmek için yeniden çalıştırın.

Bir komut çalıştırarak eski sertifikaların artık geçerli olmadığını doğrulayın `kubectl` . Tarafından kullanılan sertifikaları güncelleştirmediyseniz `kubectl` bir hata görürsünüz.  Örnek:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Çalıştırarak tarafından kullanılan sertifikayı güncelleştirin `kubectl` `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Bir komut çalıştırarak sertifikaların güncelleştirildiğini doğrulayın `kubectl` , şimdi başarılı olur. Örnek:

```console
kubectl get no
```

> [!NOTE]
> AKS üzerinde çalışan, [Azure dev Spaces][dev-spaces]gibi bir hizmetleriniz varsa, [Bu hizmetlerle ilgili sertifikaları da güncelleştirmeniz][dev-spaces-rotate] gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kümenizin sertifikalarını, CA 'Ları ve SAs 'yi otomatik olarak döndürme konusu gösterilmektedir. AKS güvenlik en iyi uygulamaları hakkında daha fazla bilgi için [Azure Kubernetes Service 'te (aks) küme güvenliği ve yükseltmeleri Için en iyi uygulamaları][aks-best-practices-security-upgrades] görebilirsiniz.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
