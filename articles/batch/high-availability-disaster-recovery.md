---
title: Yüksek kullanılabilirlik ve olağanüstü durum kurtarma
description: Toplu uygulamanızı bölgesel bir kesinti için nasıl tasarlayacağınızı öğrenin.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831015"
---
# <a name="design-your-batch-application-for-high-availability"></a>Toplu uygulamanızı yüksek kullanılabilirlik için tasarlama

Azure Batch tüm Azure bölgelerinde kullanılabilir, ancak bir Batch hesabı oluşturulduğunda, belirli bir bölgeyle ilişkilendirilmesi gerekir. Daha sonra Batch hesabına yönelik tüm işlemler bu bölge için geçerlidir. Örneğin, havuzlar ve ilişkili sanal makineler (VM 'Ler) Batch hesabıyla aynı bölgede oluşturulur.

Batch kullanan bir uygulama tasarlarken, toplu Iş olasılığını bir bölgede kullanılamayan şekilde düşünmeniz gerekir. Bölgede bir bütün olarak bir sorun olduğunda, bölgedeki tüm Batch hizmetinin veya özel Batch hesabınızdaki bir sorun olduğu nadir bir durumla karşılaşmanız mümkündür.

Batch 'i kullanan uygulama veya çözümün her zaman kullanılabilir olması gerekiyorsa, başka bir bölgeye yük devretmek veya her zaman iş yükünün iki veya daha fazla bölge arasında bölünmesini sağlamak üzere tasarlanmalıdır. Her iki yaklaşım da, her hesap farklı bir bölgede bulunan en az iki Batch hesabı gerektirir.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Birden çok bölgede birden çok Batch hesabı

Çeşitli bölgelerde birden çok Batch hesabı kullanılması, bir bölgedeki Batch hesabı kullanılamaz hale gelirse uygulamanızın çalışmaya devam etmesine olanak sağlar. Uygulamanızın yüksek oranda kullanılabilir olması gerekiyorsa, birden çok hesap olması özellikle önemlidir.

Bazı durumlarda, uygulamalar, kasıtlı olarak iki veya daha fazla bölge kullanacak şekilde tasarlanmış olabilir. Örneğin, çok sayıda kapasiteye ihtiyaç duyduğunuzda, büyük ölçekli bir uygulamayı veya gelecekteki büyüme için karşılamak 'ı işlemek için birden fazla bölgenin kullanılması gerekebilir. Bu uygulamalar Ayrıca birden çok Batch hesabı (kullanılan bölge başına) gerektirir.

## <a name="design-considerations-for-providing-failover"></a>Yük devretme sağlamaya yönelik tasarım konuları

Alternatif bir bölgeye yük devretme özelliği sağlarken, bir Çözümdeki tüm bileşenlerin göz önünde bulundurulması gerekir; yalnızca ikinci bir Batch hesabına sahip olmak yeterli değildir. Örneğin, çoğu Batch uygulamasında, depolama hesabı ve Batch hesabı, kabul edilebilir performans için aynı bölgede olması gereken bir Azure depolama hesabı gereklidir.

Yük devredebileceğiniz bir çözüm tasarlarken aşağıdaki noktaları göz önünde bulundurun:

- Her bölgede tüm gerekli hesapları (Batch hesabı ve depolama hesabı gibi) önceden oluşturun. Genellikle hesapların oluşturulması için ücret alınmaz ve ücret yalnızca hesap kullanıldığında veya veriler depolandığında tahakkuk eder.
- Uygun [kotaların](batch-quota-limit.md) tüm hesaplarda ayarlanmış olduğundan emin olun. bu nedenle, Batch hesabını kullanarak gerekli sayıda çekirdek ayırabilirsiniz.
- Uygulamanın bir bölgede dağıtımını otomatikleştirmek için şablonları ve/veya betikleri kullanın.
- Tüm bölgelerde uygulama ikili dosyalarını ve başvuru verilerini güncel tutun. Güncel kalmak, bölgenin karşıya yükleme ve dağıtım için beklemek zorunda kalmadan bölgenin çevrimiçi olarak kolayca yapılabildiğini güvence altına alabilir. Örneğin, havuz düğümlerine yüklenecek bir özel uygulama toplu uygulama paketleri kullanılarak depolanıyorsa ve başvurulduğundan, uygulamanın yeni bir sürümü üretildiğinde, her bir Batch hesabına yüklenmelidir ve havuz yapılandırması tarafından başvurulmalıdır (veya yeni sürümü varsayılan sürümü olarak oluşturmalısınız).
- Toplu Işlem, depolama ve diğer Hizmetleri çağıran uygulamada, istemciler üzerinde geçiş yapmayı veya farklı bölgelere yük geçirmeyi kolaylaştırın.
- Normal işlemin bir parçası olarak, diğer bir bölgeye sıklıkla geçiş yapmayı düşünün. Örneğin, ayrı bölgelerde iki dağıtım ile her ay alternatif bölgeye geçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal](batch-account-create-portal.md), [Azure CLI](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md)veya [Batch yönetim API 'si](batch-management-dotnet.md)ile Batch hesapları oluşturma hakkında daha fazla bilgi edinin.
- [Bir Batch hesabıyla ilişkili varsayılan kotalar](batch-quota-limit.md) ve kotaların nasıl artılabiliyor hakkında bilgi edinin.
