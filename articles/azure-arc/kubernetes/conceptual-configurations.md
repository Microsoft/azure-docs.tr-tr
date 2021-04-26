---
title: Konfigürasyonlar ve Gilar-Azure yay etkin Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes 'in Gilar ve yapılandırma özelliklerine kavramsal bir genel bakış sunulmaktadır.
keywords: Kubernetes, yay, Azure, kapsayıcılar, yapılandırma, Gilar
ms.openlocfilehash: 198bd45374d998ef0d9d0e612e974484d52e2f20
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443315"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes ile yapılandırma ve Gilar

Kubernetes ile ilgili olarak, Giüstler, bir git deposunda Kubernetes küme yapılandırmalarının (dağıtımlar, ad alanları vb.) istenen durumunu bildirme uygulamasıdır. Bu bildirimin ardından bir işleç kullanarak bu küme yapılandırmalarının yoklaması ve çekme tabanlı dağıtımı gelir. Git deposu şunları içerebilir:
* YAML-ad alanları, ConfigMaps, dağıtımlar, DaemonSets vb. gibi geçerli bir Kubernetes kaynağını tanımlayan bildirimleri biçimlendirir.
* Uygulamaları dağıtmak için Held grafikleri.

Bir git deposundan bir Kubernetes kümesine yapılandırmaların akışını kolaylaştırmak için, Gilar alanındaki popüler bir açık kaynak aracı olan [Flox](https://docs.fluxcd.io/), Kubernetes kümesine dağıtılabilir. Flox, hem küme hem de ad alanı kapsamlarındaki işlecinin dağıtımını destekler. Ad alanı kapsamıyla dağıtılan bir Flox işleci, yalnızca ilgili ad alanı içinde Kubernetes nesnelerini dağıtabilir. Küme veya ad alanı kapsamı arasında seçim yapma özelliği, aynı Kubernetes kümesinde çok kiracılı dağıtım desenleri almanıza yardımcı olur.

## <a name="configurations"></a>Yapılandırmalar

[![Yapılandırma mimarisi ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

Kümeniz ve git deposu arasındaki bağlantı, `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Arc etkin Kubernetes kaynağı (tarafından temsil edilen) üzerinde Azure Resource Manager içinde bir yapılandırma kaynağı () olarak oluşturulur `Microsoft.Kubernetes/connectedClusters` . 

Yapılandırma kaynağı özellikleri, küme üzerinde, bildirim almak için gereken git deposunda ve bunların alınacağı yoklama aralığında yer alan uygun parametrelerle birlikte akıcı x operatörü dağıtmak için kullanılır. Yapılandırma kaynak verileri, veri gizliliğini sağlamak için bir Azure Cosmos DB veritabanında şifreli olarak depolanır.

`config-agent`Kümenizde çalışan şu şekilde sorumludur:
* Azure Arc etkin Kubernetes kaynağında yeni veya güncelleştirilmiş yapılandırma kaynakları izleniyor.
* Her yapılandırma kaynağı için Git deposunu izlemek üzere Flox operatörü dağıtma.
* Herhangi bir yapılandırma kaynağına yapılan herhangi bir güncelleştirme uygulanıyor. 

Çoklu kiracı elde etmek için aynı Azure Arc etkin Kubernetes kümesinde birden çok ad alanı kapsamlı yapılandırma kaynağı oluşturabilirsiniz.

> [!NOTE]
> * `config-agent` Yeni veya güncelleştirilmiş yapılandırma kaynaklarını yay etkin Kubernetes kaynağında kullanılabilir olacak şekilde izler. Bu nedenle, aracıların kümeye çekilmesi için istenen durumun bağlantısı olması gerekir. Aracılar Azure 'a bağlanamazsa, istenen durumu kümeye yayılırken bir gecikme vardır.
> * Özel anahtar, bilinen ana bilgisayar içeriği, HTTPS Kullanıcı adı ve belirteç/parola gibi hassas müşteri girişleri, Azure Arc etkin Kubernetes hizmetlerinde 48 ' den fazla saat boyunca depolanmaz. Yapılandırma için hassas girişler kullanıyorsanız, kümeleri mümkün olduğunca düzenli olarak çevrimiçi duruma getirin.

## <a name="apply-configurations-at-scale"></a>Yapılandırma ölçeğini ölçekli uygulama

Azure Resource Manager yapılandırmalarını yönettiğinden, bir aboneliğin veya bir kaynak grubunun kapsamındaki Azure Ilkesi 'ni kullanarak tüm Azure Arc etkin Kubernetes kaynakları genelinde aynı yapılandırmanın oluşturulmasını otomatik hale getirebilirsiniz. 

Bu ölçekli uygulama, ortak bir temel yapılandırma (ClusterRoleBindings, RoleBindings ve NetworkPolicy gibi yapılandırmalar içeren), Azure Arc etkinleştirilmiş Kubernetes kümelerinin tamamına veya envanterine yönelik olarak bir dünya genelinde uygulanabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Azure Arc etkin Kubernetes kümesinde [yapılandırma oluşturma](./tutorial-use-gitops-connected-cluster.md) .
* [Yapılandırmalara göre yapılandırma uygulamak Için Azure Ilkesini kullanın](./use-azure-policy.md).