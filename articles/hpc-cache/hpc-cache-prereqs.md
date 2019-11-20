---
title: Azure HPC önbellek önkoşulları
description: Azure HPC önbelleğini kullanma önkoşulları
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 406b77a428ec725a3d8d070bd60fcd4440a5cb92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166476"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC önbelleği önkoşulları

Yeni bir Azure HPC önbelleği oluşturmak için Azure portal kullanmadan önce ortamınızın bu gereksinimleri karşıladığından emin olun.

## <a name="azure-subscription"></a>Azure aboneliği

Ücretli bir abonelik önerilir.

> [!NOTE]
> GA sürümünün ilk birkaç ayı sırasında, Azure HPC önbellek ekibinin, bir önbellek örneği oluşturmak için kullanılmadan önce aboneliğinizi erişim listesine eklemesi gerekir. Bu yordam, her müşterinin Önbelleklerinden yüksek kaliteli yanıt verdiğini aldığından emin olmanıza yardımcı olur. Erişim istemek için [Bu formu](https://aka.ms/onboard-hpc-cache) doldurun.

## <a name="network-infrastructure"></a>Ağ altyapısı

Önbelleğinizi kullanabilmeniz için ağ ile ilgili iki önkoşul ayarlanmalıdır:

* Azure HPC önbellek örneği için ayrılmış alt ağ
* Önbelleğin depolamaya ve diğer kaynaklara erişebilmesi için DNS desteği

### <a name="cache-subnet"></a>Önbellek alt ağı

Azure HPC önbelleği, bu kaliteleri olan ayrılmış bir alt ağa ihtiyaç duyuyor:

* Alt ağda en az 64 IP adresi kullanılabilir olmalıdır.
* Alt ağ, istemci makineler gibi ilgili hizmetler için bile diğer VM 'Leri barındıramaz.
* Birden çok Azure HPC önbellek örneği kullanıyorsanız, her birinin kendi alt ağına ihtiyacı vardır.

En iyi yöntem, her önbellek için yeni bir alt ağ oluşturmaktır. Önbellek oluşturmanın bir parçası olarak yeni bir sanal ağ ve alt ağ oluşturabilirsiniz.

### <a name="dns-access"></a>DNS erişimi

Önbelleğin, sanal ağının dışındaki kaynaklara erişmesi için DNS ihtiyacı vardır. Kullandığınız kaynaklara bağlı olarak, özelleştirilmiş bir DNS sunucusu ayarlamanız ve bu sunucu ile Azure DNS sunucuları arasında iletmeyi yapılandırmanız gerekebilir:

* Azure Blob depolama uç noktalarına ve diğer iç kaynaklara erişmek için, Azure tabanlı DNS sunucusuna ihtiyacınız vardır.
* Şirket içi depolamaya erişmek için, depolama ana bilgisayar adlarını çözümleyebilecek özel bir DNS sunucusu yapılandırmanız gerekir.

Yalnızca blob depolamaya erişmeniz gerekiyorsa önbelleğiniz için Azure tarafından sağlanmış varsayılan DNS sunucusunu kullanabilirsiniz. Ancak, diğer kaynaklara erişmeniz gerekiyorsa, özel bir DNS sunucusu oluşturmanız ve Azure 'a özgü tüm çözümleme isteklerini Azure DNS sunucusuna iletecek şekilde yapılandırmanız gerekir.

Basit bir DNS sunucusu, istemci bağlantılarının tüm kullanılabilir önbellek bağlama noktaları arasında yükünü dengelemek için de kullanılabilir.

Azure sanal ağları ve DNS sunucusu konfigürasyonları hakkında daha fazla bilgi [için bkz. Azure sanal ağlardaki kaynaklar Için ad çözümlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>İzinler

Önbelleğinizi oluşturmaya başlamadan önce Bu izinle ilgili önkoşulları denetleyin.

* Önbellek örneğinin sanal ağ arabirimlerini (NIC 'ler) oluşturabiliyor olması gerekir. Önbelleği oluşturan kullanıcının, NIC 'ler oluşturmak için abonelikte yeterli ayrıcalıklara sahip olması gerekir.

* BLOB depolama kullanılıyorsa, Azure HPC önbelleğinin depolama hesabınıza erişmesi için yetkilendirmeye ihtiyacı vardır. Blob depolamaya önbellek erişimi sağlamak için rol tabanlı erişim denetimi (RBAC) kullanın. İki rol gereklidir: depolama hesabı katılımcısı ve Depolama Blobu veri katılımcısı.

  Rolleri eklemek için [depolama hedefleri ekleme](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) bölümündeki yönergeleri izleyin.

## <a name="storage-infrastructure"></a>Depolama altyapısı

Önbellek, Azure Blob kapsayıcılarını veya NFS donanım depolama dışarı aktarmaları destekler. Önbelleği oluşturduktan sonra depolama hedefleri ekleyin.

Her depolama türünün belirli önkoşulları vardır.

### <a name="nfs-storage-requirements"></a>NFS depolama gereksinimleri

Şirket içi donanım depolama kullanılıyorsa, önbelleğin alt ağından veri merkezine yüksek bant genişliğine sahip ağ erişimi olması gerekir. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) veya benzer erişim önerilir.

NFS arka uç depolaması, uyumlu bir donanım/yazılım platformu olmalıdır. Ayrıntılar için Azure HPC önbellek ekibine başvurun.

### <a name="blob-storage-requirements"></a>BLOB depolama gereksinimleri

Önbelleğiniz ile Azure Blob depolama 'yı kullanmak istiyorsanız, uyumlu bir depolama hesabı ve boş bir blob kapsayıcısı ya da [verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)bölümünde açıklandığı gıbı Azure HPC önbellek biçimli verilerle doldurulmuş bir kapsayıcı gerekir.

Bir depolama hedefi olarak eklemeyi denemeden önce hesabı ve kapsayıcıyı oluşturun.

Uyumlu bir depolama hesabı oluşturmak için şu ayarları kullanın:

* Performans: **Standart**
* Hesap türü: **StorageV2 (genel amaçlı v2)**
* Çoğaltma: **yerel olarak yedekli depolama (LRS)**
* Erişim katmanı (varsayılan): **sık** erişimli

Önbelleğiniz ile aynı konumda bir depolama hesabı kullanmak iyi bir uygulamadır.
<!-- clarify location - same region or same resource group or same virtual network? -->

Ayrıca, yukarıdaki [izinlerle](#permissions)belirtilen şekilde, önbellek uygulamasına Azure depolama hesabınıza erişim izni vermeniz gerekir. Önbelleğe gerekli erişim rollerini sağlamak için [depolama hedefleri ekleme](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) bölümündeki yordamı izleyin. Depolama hesabı sahibi değilseniz, sahibi bu adımı izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Azure portal [Azure HPC önbellek örneği oluşturma](hpc-cache-create.md)
