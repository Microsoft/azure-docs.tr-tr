---
title: Azure RBAC-Azure yay etkin Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes 'te Azure RBAC özelliğine kavramsal bir genel bakış sunulmaktadır
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451192"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure 'da Azure 'daki yay etkin Kubernetes

Kubernetes [Clusterrolebinding ve rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) nesne türleri, Kubernetes 'te yerel olarak yetkilendirme tanımlamaya yardımcı olur. Azure RBAC ile, kümedeki yetkilendirme denetimlerini denetlemek için Azure 'daki Azure Active Directory (Azure AD) ve rol atamalarını kullanabilirsiniz.

Bu özellik sayesinde, Azure rolü atamalarının tüm avantajları, örneğin Azure 'daki tüm Azure RBAC değişikliklerini gösteren etkinlik günlükleri, Azure Arc etkinleştirilmiş Kubernetes kümeniz için geçerli hale gelir.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Mimari-Azure yay üzerinde Azure RBAC etkin Kubernetes

[![Azure RBAC mimarisi ](./media/conceptual-azure-rbac.png)](./media/conceptual-azure-rbac.png#lightbox)

Tüm yetkilendirme erişimi denetimlerini Azure 'daki yetkilendirme hizmetine yönlendirmek için, kümeye bir Web kancası sunucusu ([Guard](https://github.com/appscode/guard)) dağıtılır.

`apiserver`Kümenin, ve isteklerin Guard Web kancası sunucusuna yönlendirilmesi için [Web kancası belirteci kimlik doğrulaması](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) ve [Web kancası yetkilendirmesi](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) kullanacak şekilde yapılandırılmış olması `TokenAccessReview` `SubjectAccessReview` . `TokenAccessReview`Ve `SubjectAccessReview` istekleri, ' a gönderilen Kubernetes kaynakları istekleri tarafından tetiklenir `apiserver` .

Daha sonra koruma, `checkAccess` Istenen Azure AD varlığının sorun kaynağına erişimi olup olmadığını görmek Için Azure 'daki yetkilendirme hizmetinde bir çağrı yapar. 

Atamadaki bu erişime izin veren bir rol varsa, `allowed` yetkilendirme hizmeti koruyucusu 'ndan bir yanıt gönderilir. Koruma, sırasıyla, `allowed` `apiserver` Istenen Kubernetes kaynağına erişmek için çağıran varlığı etkinleştirerek öğesine bir yanıt gönderir.


Atamadaki bu erişime izin veren bir rol yoksa, `denied` Yetkilendirme hizmetinden koruma 'e bir yanıt gönderilir. Guard öğesine bir `denied` yanıt gönderir ve `apiserver` çağıran varlığa istenen kaynakta 403 yasaklanmış bir hata verir.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Azure yay etkin Kubernetes kümesi kümenizde [Azure RBAC 'Yi ayarlayın](./azure-rbac.md) .