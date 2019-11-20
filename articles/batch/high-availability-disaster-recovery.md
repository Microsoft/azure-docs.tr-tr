---
title: Yüksek kullanılabilirlik ve olağanüstü durum kurtarma-Azure Batch | Microsoft Docs
description: Toplu uygulamanızı bölgesel bir kesinti için nasıl tasarlayacağınızı öğrenin
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: 11446f5f23d470234b5dba641dc16e0bf71d9b72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094044"
---
# <a name="design-your-application-for-high-availability"></a>Uygulamanızı yüksek kullanılabilirlik için tasarlama

Azure Batch bölgesel bir hizmettir. Toplu işlem tüm Azure bölgelerinde kullanılabilir, ancak bir Batch hesabı oluşturulduğunda bir bölgeyle ilişkilendirilmesi gerekir. Daha sonra Batch hesabına yönelik tüm işlemler bu bölge için geçerlidir. Örneğin, havuzlar ve ilişkili sanal makineler (VM 'Ler) Batch hesabıyla aynı bölgede oluşturulur.

Batch kullanan bir uygulama tasarlarken, toplu Iş olasılığını bir bölgede kullanılamayan şekilde düşünmeniz gerekir. Bölgenin tamamı, bölgedeki tüm Batch hizmeti veya özel Batch hesabınızla ilgili bir sorun olduğu nadir bir durumla karşılaşmanız mümkündür.

Batch 'i kullanan uygulama veya çözümün her zaman kullanılabilir olması gerekiyorsa, başka bir bölgeye yük devretmek veya her zaman iş yükünün iki veya daha fazla bölge arasında bölünmesini sağlamak üzere tasarlanmalıdır. Her iki yaklaşım da, her hesap farklı bir bölgede bulunan en az iki Batch hesabı gerektirir.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Birden çok bölgede birden çok Batch hesabı

Çeşitli bölgelerde birden çok Batch hesabı kullanılması, başka bir bölgedeki Batch hesabı kullanılamaz hale gelirse uygulamanızın çalışmaya devam etmesine olanak sağlar. Uygulamanızın yüksek oranda kullanılabilir olması gerekiyorsa, birden çok hesap kullanmak özellikle önemlidir.

Bazı durumlarda, bir uygulama her zaman iki veya daha fazla bölge kullanacak şekilde tasarlanabilir. Örneğin, çok sayıda kapasiteye ihtiyaç duyduğunuzda, büyük ölçekli bir uygulamayı veya gelecekteki büyüme için karşılamak 'ı işlemek için birden fazla bölgenin kullanılması gerekebilir.

## <a name="design-considerations-for-providing-failover"></a>Yük devretme sağlamaya yönelik tasarım konuları

Alternatif bir bölgeye yük devretme yeteneği sağlarken göz önünde bulundurmanız gereken bir anahtar noktası, bir Çözümdeki tüm bileşenlerin göz önünde bulundurulmalıdır; yalnızca ikinci bir Batch hesabına sahip olmak yeterli değildir. Örneğin, çoğu Batch uygulamasında, depolama hesabı ve Batch hesabı, kabul edilebilir performans için aynı bölgede olması gereken bir Azure depolama hesabı gereklidir.

Yük devredebileceğiniz bir çözüm tasarlarken aşağıdaki noktaları göz önünde bulundurun:

- Her bölgede tüm gerekli hesapları (Batch hesabı ve depolama hesabı gibi) önceden oluşturun. Yalnızca depolanan veriler olduğunda veya hesap kullanıldığında, hesapların oluşturulması için genellikle herhangi bir ücret alınmaz.
- Kotaların zaman içinde ayarlanmış olduğundan emin olun, böylece Batch hesabını kullanarak gereken sayıda çekirdek ayırabilirsiniz.
- Uygulamanın bir bölgede dağıtımını otomatikleştirmek için şablonları ve/veya betikleri kullanın.
- Tüm bölgelerde uygulama ikili dosyalarını ve başvuru verilerini güncel tutun. Güncel kalmak, bölgenin karşıya yükleme ve dağıtım için beklemek zorunda kalmadan bölgenin çevrimiçi olarak kolayca yapılabildiğini güvence altına alabilir. Örneğin, havuz düğümlerine yüklenecek bir özel uygulama toplu uygulama paketleri kullanılarak depolanıyorsa ve başvurulduğundan, uygulamanın yeni bir sürümü üretildiğinde, her bir Batch hesabına yüklenmelidir ve havuz yapılandırması tarafından başvurulmalıdır (veya yeni sürümü varsayılan sürüm yapın).
- Toplu Işlem, depolama ve diğer Hizmetleri çağıran uygulamada, istemcileri veya farklı bölgeye yük kolayca geçiş yapın.
- Yük devretme işleminin başarılı olabilmesi için en iyi yöntem, normal işlemin bir parçası olarak bir alternatif bölgeye sık sık geçiş sağlamaktır. Örneğin, ayrı bölgelerde iki dağıtım ile her ay alternatif bölgeye geçiş yapın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal](batch-account-create-portal.md), [Azure CLI](cli-samples.md), [PowerShell](batch-powershell-cmdlets-get-started.md)veya [Batch yönetim API 'si](batch-management-dotnet.md)ile Batch hesapları oluşturma hakkında daha fazla bilgi edinin.
- Varsayılan kotalar bir Batch hesabıyla ilişkilendirilir; [Bu makale](batch-quota-limit.md) , varsayılan kota değerlerini ayrıntılandırmakta ve kotaların nasıl arttırılabileceğinizi anlatmaktadır.
