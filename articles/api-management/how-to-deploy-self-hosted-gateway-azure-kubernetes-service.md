---
title: Azure Kubernetes hizmetine şirket içinde barındırılan bir ağ geçidi dağıtma | Microsoft Docs
description: Azure API Management Azure Kubernetes hizmetine şirket içinde barındırılan ağ geçidi bileşenini dağıtmayı öğrenin
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015230"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Azure Kubernetes Service’e dağıtma

Bu makalede, Azure [Kubernetes hizmetine](https://azure.microsoft.com/services/kubernetes-service/)Azure API Management şirket içinde barındırılan ağ geçidi bileşeni dağıtma adımları sağlanmaktadır. Şirket içinde barındırılan ağ geçidini bir Kubernetes kümesine dağıtmak için bu[belgeye](how-to-deploy-self-hosted-gateway-kubernetes.md)bakın.

## <a name="prerequisites"></a>Önkoşullar

- [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
- [Azure Kubernetes kümesi oluşturma](../aks/kubernetes-walkthrough-portal.md)
- [API Management örneğiniz için bir ağ geçidi kaynağı sağlayın](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Şirket içinde barındırılan ağ geçidini AKS 'e dağıtma

1. **Dağıtım ve altyapı** altında **ağ geçitleri** ' ni seçin.
2. Dağıtmayı düşündüğünüz şirket içinde barındırılan ağ geçidi kaynağını seçin.
3. **Dağıtım**' ı seçin.
4. Varsayılan **süre sonu** ve **gizli anahtar** değerlerini kullanarak, **belirteç** metin kutusundaki yeni bir belirtecin sizin için otomatik olarak oluşturulduğunu unutmayın. İsterseniz veya her ikisini de ayarlayın ve yeni bir belirteç oluşturmak için **Oluştur** ' u seçin.
5. **Dağıtım betikleri** altında **Kubernetes** 'in seçildiğinden emin olun.
6. Dosyayı indirmek için **dağıtım** ' nın yanındaki **<ağ geçidi-adı>. yıml** dosya bağlantısı ' nı seçin.
7. Bağlantı noktası eşlemelerini ve kapsayıcı adını, gereken şekilde değiştirin.
8. Senaryonuza bağlı olarak, [hizmet türünü](../aks/concepts-network.md#services)değiştirmeniz gerekebilir. `NodePort` varsayılan değerdir.
9. Komutu panoya kaydetmek için **Dağıt** metin kutusunun sağ ucunda bulunan **Kopyala** simgesini seçin `kubectl` .
10. Komutu Terminal (veya komut) penceresine yapıştırın. Komutun indirilen ortam dosyasının geçerli dizinde mevcut olmasını beklediğini unutmayın.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Komutu yürütün. Bu komut, AKS kümenizin, Microsoft Container Registry indirilen şirket içinde barındırılan ağ geçidinin görüntüsünü kullanarak kapsayıcıyı çalıştırmasını ve kapsayıcıyı HTTP (8080) ve HTTPS (443) bağlantı noktalarını kullanıma sunacak şekilde yapılandırmasını sağlar.
12. Ağ Geçidi Pod 'un çalıştığını denetlemek için aşağıdaki komutu çalıştırın. Pod adınızın farklı olacağını unutmayın.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Ağ Geçidi hizmetinin çalıştığını kontrol etmek için aşağıdaki komutu çalıştırın. Hizmet adınızın ve IP adreslerinizin farklı olacağını unutmayın.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Azure portal geri dönüp, az önce dağıttığınız ağ geçidi düğümünün sağlıklı durum bildirimi olduğunu onaylayın.

> [!TIP]
> <code>kubectl logs <gateway-pod-name></code>Şirket içinde barındırılan ağ geçidi günlüğünün anlık görüntüsünü görüntülemek için komutunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management Self-barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md)
* [Azure Kubernetes hizmeti](../aks/intro-kubernetes.md) hakkında daha fazla bilgi edinin
* [Buluttaki günlükleri yapılandırmayı ve kalıcı hale getirme hakkında](how-to-configure-cloud-metrics-logs.md) bilgi edinin
* * [Günlükleri yerel olarak yapılandırma ve kalıcı hale getirme hakkında](how-to-configure-local-metrics-logs.md) bilgi edinin
