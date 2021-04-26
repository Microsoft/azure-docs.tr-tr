---
title: Azure Service Fabric kafes uygulama gizli dizilerini depolama ve kullanma
description: Service Fabric ağ, Azure kaynakları olarak gizli dizileri destekler. Service Fabric kafes uygulamalarınızla gizli dizileri depolamak ve yönetmek için aşağıdaki adımları uygulayın.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625541"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric kafes uygulama gizli dizileri

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Service Fabric ağ, Azure kaynakları olarak gizli dizileri destekler. Service Fabric bir ağ parolası, depolama bağlantı dizeleri, parolalar veya güvenli bir şekilde depolanması ve aktarılması gereken diğer değerler gibi herhangi bir hassas metin bilgisi olabilir.

![Ağ gizlilikleri genel bakış][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ağ parolaları kaynakları
Bir kafes uygulama gizli dizisi aşağıdakilerden oluşur:
* Metin gizli dizileri depolayan bir kapsayıcı olan **gizlilikler** kaynağı. **Gizli** dizileri kaynağı içinde bulunan gizli dizileri, güvenli bir şekilde depolanır ve iletilir.
* **Gizlilikler** kaynak kapsayıcısında depolanan bir veya daha fazla **gizli dizi/değer** kaynağı. Her **gizli dizi/değer** kaynağı bir sürüm numarasıyla ayırt edilir.

## <a name="next-steps"></a>Sonraki adımlar 
Service Fabric ağ parolaları hakkında daha fazla bilgi edinmek için bkz.:
- [Service Fabric kafes uygulama gizli dizilerini yönetme](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric kaynak modeline giriş](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
