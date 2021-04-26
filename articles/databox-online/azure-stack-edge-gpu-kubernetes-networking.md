---
title: Azure Stack Edge Pro cihazında Kubernetes ağını anlayın | Microsoft Docs
description: Kubernetes ağının Azure Stack Edge Pro cihazında nasıl çalıştığını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560259"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazındaki Kubernetes ağı

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro cihazınızda, işlem rolünü yapılandırırken bir Kubernetes kümesi oluşturulur. Kubernetes kümesi oluşturulduktan sonra kapsayıcılı uygulamalar, pods 'deki Kubernetes kümesine dağıtılabilir. Kubernetes kümenizdeki pods için ağ için kullanılan farklı yollar vardır. 

Bu makalede, genel olarak bir Kubernetes kümesinde ağ ve özellikle de Azure Stack Edge Pro cihazınız bağlamında ağ açıklanmaktadır. 

## <a name="networking-requirements"></a>Ağ gereksinimleri

Aşağıda, Kubernetes kümesine dağıtılan tipik bir 2 katmanlı uygulamaya örnek verilmiştir.

- Uygulamanın arka uçta bir Web sunucusu ön ucu ve bir veritabanı uygulaması vardır. 
- Her pod 'a bir IP atanır, ancak bu IP 'Ler Pod 'ın yeniden başlatılması ve yük devretmesinde değişebilir. 
- Her uygulama birden çok yığından oluşur ve trafiğin tüm Pod çoğaltmalarda yük dengelenmesi gerekir. 

![Kubernetes ağ gereksinimleri](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

Yukarıdaki senaryo aşağıdaki ağ gereksinimlerine neden olur:

 - Dış kullanıma yönelik uygulamaya, Kubernetes kümesi dışındaki bir uygulama kullanıcısı tarafından bir ad veya IP adresi aracılığıyla erişilmesi gerekir. 
 - Kubernetes kümesi içindeki uygulamalar, örneğin ön uç ve arka uç düğüm, burada birbirleriyle iletişim kurabilmesi gerekir.

Yukarıdaki ihtiyaçları çözümlemek için, bir Kubernetes hizmeti tanıtıldık. 


## <a name="networking-services"></a>Ağ hizmetleri

İki tür Kubernetes hizmeti vardır: 

- **Küme IP hizmeti** -bunu, uygulama potıd 'leri için sabit bir uç nokta vererek düşünün. Bu hizmetlerle ilişkili Pod 'a Kubernetes kümesi dışından erişilemez. Bu hizmetlerle kullanılan IP adresi, özel ağdaki adres alanından gelir. 
- **Yük dengeleyici IP 'si** -küme IP hizmeti gibi, ancak ilişkili IP, dış ağdan gelir ve Kubernetes kümesi dışından erişilebilir.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'da Kubernetes ağı

Calıco, Metallb ve Core DNS, Azure Stack Edge Pro 'unuzda ağ için yüklenen tüm bileşenlerdir. 

- **Calıco** , özel bir IP aralığından her Pod 'A bir IP adresi atar ve bir düğümdeki Pod 'ın başka bir düğümdeki Pod ile iletişim kurabilmesi için bu FID 'lerin ağ iletişimini yapılandırır. 
- **Metallb** , küme içi pod üzerinde çalışır ve IP adresini yük dengeleyici türündeki hizmetlere atar. Yük dengeleyici IP adresleri, yerel kullanıcı arabirimi aracılığıyla belirtilen hizmet IP aralığından seçilir. 
- **Çekırdek DNS** -bu EKLENTI, DNS kayıtlarını eşleme hizmeti adını küme IP adresine yapılandırır.

Kubernetes düğümleri ve dış hizmetler için kullanılan IP adresleri, cihazın yerel kullanıcı arabirimindeki **işlem ağı** sayfası aracılığıyla sağlanır.

![Yerel Kullanıcı arabiriminde Kubernetes IP ataması](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP ataması şu şekilde olur:

- **Kubernetes düğüm IP 'leri**: Bu IP aralığı, Kubernetes ana ve çalışan düğümleri için kullanılır. Bu IP, Kubernetes düğümleri birbirleriyle iletişim kurduğunda kullanılır.

- **Kubernetes dış hizmet IP 'leri**: Bu IP aralığı, Kubernetes kümesi dışında kullanıma sunulan dış hizmetler (Load Balancer Hizmetleri olarak da bilinir) için kullanılır.  


## <a name="next-steps"></a>Sonraki adımlar

Azure Stack Edge Pro 'unuzda Kubernetes ağını yapılandırmak için bkz.:

- [Durum bilgisi olan bir uygulamayı, Azure Stack Edge Pro 'yu IoT Edge aracılığıyla dışarıdan kullanıma sunun](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Durum bilgisi olmayan bir uygulamayı kuebctl aracılığıyla Azure Stack Edge Pro 'unuzda dışarıdan kullanıma sunun](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).