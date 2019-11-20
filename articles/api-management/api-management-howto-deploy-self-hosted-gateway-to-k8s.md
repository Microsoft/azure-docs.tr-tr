---
title: Kubernetes 'e şirket içinde barındırılan bir Azure API Management ağ geçidi dağıtma | Microsoft Docs
description: Şirket içinde barındırılan bir Azure API Management ağ geçidini Kubernetes 'e dağıtmayı öğrenin
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513841"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Kubernetes 'e şirket içinde barındırılan bir Azure API Management ağ geçidi dağıtma

Bu makalede, bir Kubernetes kümesine şirket içinde barındırılan Azure API Management ağ geçidi dağıtma adımları sağlanmaktadır.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi özelliği önizlemededir. Önizleme sırasında, şirket içinde barındırılan ağ geçidi, yalnızca geliştirici ve Premium katmanlarda ek ücret ödemeden kullanılabilir. Geliştirici katmanı, tek bir şirket içinde barındırılan ağ geçidi dağıtımıyla sınırlıdır.


## <a name="prerequisites"></a>Önkoşullar

- Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
- Kubernetes kümesi oluşturun. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) , geliştirme ve değerlendirme amaçları için iyi bir seçenektir. Üretim iş yükleri için, yabancı bir bulutta [Azure Kubernetes hizmeti](https://azure.microsoft.com/services/kubernetes-service/) veya bir Kubernetes kümesi kullanabilirsiniz.
- [API Management örneğiniz için bir ağ geçidi kaynağı sağlayın](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Ağ geçidini Kubernetes 'e dağıtma

1. **Ayarlar**altında **ağ geçitleri** ' ni seçin.
2. Dağıtmayı düşündüğünüz ağ geçidi kaynağını seçin.
3. **Dağıtım**' ı seçin.
4. Varsayılan **süre sonu** ve **gizli anahtar** değerlerini kullanarak, **belirteç** metin kutusundaki yeni bir belirtecin sizin için otomatik olarak oluşturulduğunu unutmayın. İsterseniz veya her ikisini de ayarlayın ve yeni bir belirteç oluşturmak için **Oluştur** ' u seçin.
5. **Dağıtım betikleri**altında **Kubernetes** 'in seçildiğinden emin olun.
6. Dosyayı indirmek için **dağıtım** ' nın yanındaki **< ağ geçidi-adı >. yıml** dosya bağlantısı ' nı seçin.
7. Bağlantı noktası eşlemelerini ve kapsayıcı adını, gereken şekilde değiştirin.
8. `kubectl` komutunu panoya kaydetmek için **Dağıt** metin kutusunun sağ ucunda bulunan **Kopyala** simgesini seçin. 
9. Komutu Terminal (veya komut) penceresine yapıştırın. Komutun indirilen ortam dosyasının geçerli dizinde mevcut olmasını beklediğini unutmayın.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Komutu yürütün. Bu komut, Kubernetes kümenizi, şirket içinde barındırılan ağ geçidinin Microsoft Container Registry indirilen görüntüsünü kullanarak kapsayıcıyı çalıştırmak ve kapsayıcıyı HTTP (8080) ve HTTPS (443) bağlantı noktalarını kullanıma sunmak üzere yapılandırmak için yönlendirir.
11. Ağ Geçidi Pod 'un çalıştığını denetlemek için aşağıdaki komutu çalıştırın. Pod adınızın farklı olacağını unutmayın. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Ağ Geçidi hizmetinin çalıştığını kontrol etmek için aşağıdaki komutu çalıştırın. Hizmet adınızın farklı olacağını unutmayın. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Azure portal geri dönüp, az önce dağıttığınız ağ geçidi düğümünün sağlıklı durum bildirimi olduğunu onaylayın.

![Ağ Geçidi durumu](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Şirket içinde barındırılan ağ geçidi günlüğünün anlık görüntüsünü görüntülemek için <code>kubectl logs <gateway-pod-name></code> komutunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management Self-barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md)
* [Azure Kubernetes hizmeti](https://docs.microsoft.com/azure/aks/intro-kubernetes) hakkında daha fazla bilgi edinin


