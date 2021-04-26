---
title: Küme bağlantısı-Azure Arc etkin Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes 'in küme bağlama özelliğine kavramsal bir genel bakış sunulmaktadır.
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451191"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Azure Arc üzerinde küme bağlantısı etkin Kubernetes

Azure Arc etkin Kubernetes *küme bağlantısı* özelliği, `apiserver` güvenlik duvarında herhangi bir gelen bağlantı noktasının etkinleştirilmesini gerektirmeden kümenin bağlantısını sağlar. Küme üzerinde çalışan bir ters proxy aracısı, Azure Arc hizmeti ile bir oturumu giden bir şekilde güvenle başlatabilir. 

Küme bağlantısı, geliştiricilerin kendi kümelerine etkileşimli geliştirme ve hata ayıklama için her yerden erişmelerini sağlar. Ayrıca, küme kullanıcılarının ve yöneticilerin kümelerini her yerden erişmesine veya yönetmesine imkan tanır. Şirket içinde barındırılan aracılara gerek kalmadan, şirket içi kümelere uygulama dağıtmak için Azure Pipelines, GitHub eylemlerinin veya diğer bir barındırılan CI/CD hizmetinin barındırılan aracılarını/çalıştıranlar ' ı da kullanabilirsiniz.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Mimari

[![Küme bağlama mimarisi ](./media/conceptual-cluster-connect.png)](./media/conceptual-cluster-connect.png#lightbox)

Aracı hele grafiğinin bir parçası olarak dağıtılan bir ters proxy aracısı olan küme tarafında, `clusterconnect-agent` oturum oluşturmak Için Azure Arc hizmetine giden çağrılar yapar.

Kullanıcı şunu çağırdığında `az connectedk8s proxy` :
1. Azure Arc proxy ikilisi indirilir ve istemci makinesinde bir işlem olarak sona erdirir. 
1. Azure Arc proxy `kubeconfig` , üzerinde çağrıldığı Azure Arc etkin Kubernetes kümesiyle ilişkili bir dosya getirir `az connectedk8s proxy` .
    * Azure Arc proxy, arayanın Azure erişim belirtecini ve Azure Resource Manager KIMLIK adını kullanır. 
1. `kubeconfig`Azure Arc proxy 'si tarafından makineye kaydedilen dosya, Azure Arc proxy işlemindeki bir uç noktaya sunucu URL 'sini gösterir.

Bir Kullanıcı bu dosyayı kullanarak bir istek gönderdiğinde `kubeconfig` :
1. Azure Arc proxy, isteği Azure Arc hizmetine alan uç noktası eşler. 
1. Azure Arc hizmeti daha sonra isteği `clusterconnect-agent` kümedeki çalıştırmaya iletir. 
1. , `clusterconnect-agent` İsteği `kube-aad-proxy` çağıran VARLıKTA Azure AD kimlik doğrulaması gerçekleştiren bileşene geçirir. 
1. Azure AD kimlik doğrulamasından sonra, `kube-aad-proxy` isteği kümeye iletmek Için Kubernetes [Kullanıcı kimliğe bürünme](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) özelliğini kullanır `apiserver` .

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Küme bağlantısı 'nı kullanarak kümenize her yerden güvenli bir şekilde [erişin](./cluster-connect.md) .