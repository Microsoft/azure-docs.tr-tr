---
title: 'Öğretici: yeni bir Azure Application Gateway yeni bir AKS kümesi için giriş denetleyicisi eklentisini etkinleştirin'
description: Yeni bir Application Gateway örneğiyle yeni AKS kümeniz için giriş denetleyicisi eklentisini nasıl etkinleştireceğinizi öğrenmek için bu öğreticiyi kullanın.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: aad57c75481230db16a63aec7fb04fc5987ae8f0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772845"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Öğretici: yeni bir Application Gateway örneğiyle yeni bir AKS kümesi için giriş denetleyicisi eklentisini etkinleştirin

Yeni bir [Azure Kubernetes hizmeti (AKS)](https://azure.microsoft.com/services/kubernetes-service/) kümesi için [Application Gateway giriş denetleyicisi (agic)](ingress-controller-overview.md) EKLENTISINI etkinleştirmek üzere Azure CLI 'yı kullanabilirsiniz.

Bu öğreticide, AGIC eklentisi etkinken bir AKS kümesi oluşturacaksınız. Kümenin oluşturulması, otomatik olarak kullanılacak bir Azure Application Gateway örneği oluşturur. Ardından, uygulamayı Application Gateway aracılığıyla kullanıma sunmak için eklentiyi kullanacak bir örnek uygulama dağıtırsınız. 

Eklenti, AKS kümeniz için [AGP 'yi daha önce helk aracılığıyla](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)dağıtmanın çok daha hızlı bir yolunu sağlar. Ayrıca, tam olarak yönetilen bir deneyim sunar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir kaynak grubu oluşturun. 
> * AGIC eklentisi etkinken yeni bir AKS kümesi oluşturun.
> * AKS kümesindeki Infer için AGIC kullanarak örnek bir uygulama dağıtın.
> * Application Gateway aracılığıyla uygulamanın erişilebilir olup olmadığını denetleyin.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. [Az Group Create](/cli/azure/group#az_group_create)kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnek, *canadamerkezi* konumunda (bölge) *myresourcegroup* adlı bir kaynak grubu oluşturur: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Eklenti etkinken bir AKS kümesi dağıtma

Artık AGIC eklentisi etkinken yeni bir AKS kümesi dağıtacaksınız. Bu işlemde kullanılmak üzere mevcut bir Application Gateway örneği sağlamazsanız, AKS kümesine trafik sunmak için otomatik olarak yeni bir Application Gateway örneği oluşturacak ve ayarlayacağız.  

> [!NOTE]
> Application Gateway giriş denetleyicisi eklentisi, Application Gateway v1 SKU 'Larını *değil* *yalnızca* Application Gateway v2 SKU 'larını (Standart ve WAF) destekler. AGIC eklentisi aracılığıyla yeni bir Application Gateway örneği dağıtırken, yalnızca bir Application Gateway Standard_v2 SKU 'SU dağıtabilirsiniz. Application Gateway WAF_v2 SKU 'SU için eklentiyi etkinleştirmek istiyorsanız aşağıdaki yöntemlerden birini kullanın:
>
> - Portal üzerinden Application Gateway WAF 'yi etkinleştirin. 
> - Önce WAF_v2 Application Gateway örneğini oluşturun ve ardından [mevcut BIR AKS kümesi ve mevcut Application Gateway örneğiyle AGIC eklentisinin nasıl etkinleştirileceği](tutorial-ingress-controller-add-on-existing.md)hakkındaki yönergeleri izleyin. 

Aşağıdaki örnekte, [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) ve [yönetilen kimlikleri](../aks/use-managed-identity.md)kullanarak *MYCLUSTER* adlı yeni bir aks kümesi dağıtırsınız. AGIC eklentisi, oluşturduğunuz kaynak grubunda, *Myresourcegroup*' de etkinleştirilir. 

Yeni bir AKS kümesini, mevcut bir Application Gateway örneği belirtilmeden etkin bir şekilde dağıtmak, Standard_v2 SKU Application Gateway örneğinin otomatik olarak oluşturulmasını ifade eder. Bu nedenle, Application Gateway örneğinin adını ve alt ağ adres alanını da belirtirsiniz. Application Gateway örneğinin adı *myApplicationGateway* olur ve kullandığımız alt ağ adres alanı 10.2.0.0/16 ' dır.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Komuta ek parametreler yapılandırmak için `az aks create` , [Bu başvurulara](/cli/azure/aks#az_aks_create)bakın. 

> [!NOTE]
> Oluşturduğunuz AKS kümesi, *Myresourcegroup adlı* kaynak grubunda görüntülenir. Ancak, otomatik olarak oluşturulan Application Gateway örneği, aracı havuzlarının olduğu düğüm kaynak grubunda olur. Tarafından düğüm kaynak grubu, varsayılan olarak *MC_resource-Group-name_cluster-name_location* olarak adlandırılır, ancak değiştirilebilir. 

## <a name="deploy-a-sample-application-by-using-agic"></a>AGIC kullanarak örnek uygulama dağıtma

Artık oluşturduğunuz AKS kümesine örnek bir uygulama dağıtırsınız. Uygulama giriş için AGIC eklentisini kullanır ve Application Gateway örneğini AKS kümesine bağlayacaktır. 

İlk olarak, komutunu çalıştırarak AKS kümesi için kimlik bilgilerini alın `az aks get-credentials` : 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Kimlik bilgileriniz olduğuna göre, kümeye giriş için AGIC kullanan bir örnek uygulama ayarlamak için aşağıdaki komutu çalıştırın. AGIC, daha önce ayarladığınız Application Gateway örneğini, dağıttığınız yeni örnek uygulamaya karşılık gelen yönlendirme kuralları ile güncelleştirir.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Uygulamanın erişilebilir olup olmadığını denetleyin

Application Gateway örneği, AKS kümesine trafik sunacak şekilde ayarlandığına göre, uygulamanızın ulaşılabilir olduğunu doğrulayalım. İlk olarak, giriş adresinin IP adresini alın: 

```azurecli-interactive
kubectl get ingress
```

Oluşturduğunuz örnek uygulamanın şu şekilde çalıştığından emin olun:

- Yukarıdaki komutu çalıştırmadan aldığınız Application Gateway örneğinin IP adresini ziyaret edin.
- Kullanılarak `curl` . 

Application Gateway güncelleştirmeyi almak bir dakika sürebilir. Application Gateway hala portalda **güncelleştirme** DURUMUNDAYSA, IP adresine erişmeyi denemeden önce bunun bitmesini sağlayın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında, kaynak grubunu, Application Gateway örneğini ve tüm ilgili kaynakları kaldırın:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [AGIC eklentisini devre dışı bırakma hakkında bilgi edinin](./ingress-controller-disable-addon.md)
