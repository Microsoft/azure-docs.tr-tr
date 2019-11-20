---
title: Azure Storage kullanarak PaaS uygulamalarının güvenliğini sağlama | Microsoft Docs
description: PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamak için Azure Depolama güvenliği en iyi uygulamaları hakkında bilgi edinin.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999163"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Azure Storage kullanarak PaaS Web ve mobil uygulamaları güvenli hale getirmenin en iyi yöntemleri
Bu makalede, hizmet olarak platform (PaaS) Web ve mobil uygulamalarınızın güvenliğini sağlamak için Azure Depolama güvenliği en iyi uygulamalarının bir koleksiyonunu tartıştık. Bu en iyi uygulamalar, Azure deneyimimizden ve sizin gibi müşterilerin deneyimlerini elde edilmiştir.

Azure, depolamayı kolayca ulaşılabilir şekilde dağıtmayı ve kullanmayı mümkün kılar. Azure depolama ile yüksek düzeyde ölçeklenebilirlik ve kullanılabilirlik sayesinde nispeten az çabayla iletişime ulaşabilirsiniz. Azure depolama, Windows ve Linux Azure sanal makineleri için de değil, büyük dağıtılmış uygulamaları da destekleyebilir.

Azure Storage aşağıdaki dört hizmeti sağlar: BLOB depolama, tablo depolama, kuyruk depolama ve dosya depolama. Daha fazla bilgi edinmek için bkz. [Microsoft Azure depolama giriş](/azure/storage/common/storage-introduction).

[Azure depolama Güvenlik Kılavuzu](/azure/storage/common/storage-security-guide) , Azure depolama ve güvenlik hakkında ayrıntılı bilgi edinmek için harika bir kaynaktır. Bu en iyi yöntem, daha fazla bilgi için güvenlik kılavuzunda bulunan kavramların ve güvenlik kılavuzunun bağlantılarının yanı sıra güvenlik kılavuzu 'nda bulunan kavramlardan bazılarının adresleridir.

Bu makalede aşağıdaki en iyi yöntemler ele alınmaktadır:

- Paylaşılan erişim imzaları (SAS)
- Rol tabanlı erişim denetimi (RBAC)
- Yüksek değerli veriler için istemci tarafı şifrelemesi
- Depolama Hizmeti Şifrelemesi


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Depolama hesabı anahtarı yerine paylaşılan erişim imzası kullanın
Erişim denetimi kritiktir. Azure depolama 'ya erişimi denetlemenize yardımcı olmak için, depolama hesabı oluştururken Azure 2 512-bit depolama hesabı anahtarları (SAKs) oluşturur. Anahtar yedeklilik düzeyi, rutin anahtar döndürme sırasında hizmet kesintilerini önlemenize olanak sağlar. 

Depolama erişim anahtarları yüksek öncelikli gizli dizlardır ve yalnızca depolama erişim denetiminden sorumlu olarak erişilebilir olmalıdır. Bu anahtarlara yanlış kişiler erişebiliyorlarsa, depolama üzerinde tamamen denetim sahibi olur ve dosyaları değiştirebilir, silebilir veya depolama alanına ekleyebilir. Bu, kuruluşunuzun veya müşterilerinizin güvenliğini tehlikeye atabilecek kötü amaçlı yazılımlar ve diğer içerik türlerini içerir.

Hala depolamadaki nesnelere erişim sağlamak için bir yol gerekir. Daha ayrıntılı erişim sağlamak için, paylaşılan erişim imzası (SAS) avantajlarından yararlanabilirsiniz. SAS, depolama alanındaki belirli nesneleri önceden tanımlanmış bir zaman aralığı ve belirli izinlerle paylaşmanıza olanak sağlar. Paylaşılan erişim imzası şunları tanımlamanızı sağlar:

- Başlangıç saati ve sona erme saati dahil olmak üzere SAS 'ın geçerli olduğu Aralık.
- SAS tarafından verilen izinler. Örneğin, bir blob üzerindeki bir SAS, kullanıcıya bu bloba okuma ve yazma izinleri verebilir, ancak silme izinleri vermez.
- Azure Storage 'ın SAS 'yi kabul ettiği isteğe bağlı IP adresi veya IP adresleri aralığı. Örneğin, kuruluşunuza ait bir IP adresi aralığı belirtebilirsiniz. Bu, SAS 'niz için bir güvenlik önlemi sağlar.
- Azure Storage 'ın SAS kabul ettiği protokol. HTTPS kullanarak istemcilere erişimi kısıtlamak için bu isteğe bağlı parametreyi kullanabilirsiniz.

SAS, depolama hesabı anahtarlarınızı dışarıda bırakmadan içeriği paylaşmak istediğiniz şekilde paylaşmanızı sağlar. Uygulamanızda her zaman SAS kullanımı, depolama hesabı anahtarlarınızla ödün vermeden depolama kaynaklarınızı paylaşmanın güvenli bir yoludur.

Paylaşılan erişim imzası hakkında daha fazla bilgi edinmek için bkz. [paylaşılan erişim Imzalarını kullanma](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Rol tabanlı erişim denetimi kullanma
Erişimi yönetmenin bir diğer yolu [rol tabanlı erişim denetimi](/azure/role-based-access-control/overview) (RBAC) kullanmaktır. RBAC ile çalışanlara, ihtiyaç duydukları tam izinleri, bilmeniz gerekenler ve en az ayrıcalık güvenlik ilkelerine göre vermek için odaklanırsınız. Çok fazla izin, saldırganlar için bir hesap sunabilir. Çok az izin, çalışanların çalışmalarını verimli bir şekilde kullanamayacağı anlamına gelir. RBAC, Azure için ayrıntılı erişim yönetimi sunarak bu sorunu ele almanıza yardımcı olur. Bu, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için zorunludur.

Kullanıcılara ayrıcalık atamak için Azure 'daki yerleşik RBAC rollerini kullanabilirsiniz. Örneğin, klasik depolama hesaplarını yönetmek için depolama hesaplarını ve klasik depolama hesabı katılımcısı rolünü yönetmesi gereken bulut işleçleri için depolama hesabı katılımcısı ' nı kullanın. VM 'Leri yönetmesi gereken ancak bağlı oldukları sanal ağ veya depolama hesabı olmayan bulut işleçleri için bunları sanal makine katılımcısı rolüne ekleyebilirsiniz.

RBAC gibi özellikleri kullanarak veri erişim denetimini zorunlu olmayan kuruluşlar, kullanıcıları için gerekenden daha fazla ayrıcalık verebilir. Bu, bazı kullanıcıların ilk yerde sahip olmadıkları verilere erişmesine izin vererek veri güvenliğinin aşılmasına yol açabilir.

RBAC hakkında daha fazla bilgi için bkz.:

- [RBAC ve Azure portalı kullanarak erişimi yönetme](/azure/role-based-access-control/role-assignments-portal)
- [Azure kaynakları için yerleşik roller](/azure/role-based-access-control/built-in-roles)
- [Azure Depolama güvenlik kılavuzu](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Yüksek değerli veriler için istemci tarafı şifrelemeyi kullanın
İstemci tarafı şifreleme, Azure depolama 'ya yüklemeden önce yoldaki verileri programlı bir şekilde şifrelemenizi ve verileri alma sırasında program aracılığıyla şifrelerini nasıl çözebilmenizi sağlar. Bu, aktarım sırasında verilerin şifrelenmesini sağlar, ancak bekleyen verilerin şifrelenmesini de sağlar. İstemci tarafı şifreleme, verilerinizi şifrelemek için en güvenli yöntemdir ancak uygulamanızda programlı değişiklikler yapmanız ve anahtar yönetim süreçlerini yerinde yerleştirmeniz gerekir.

İstemci tarafı şifreleme, şifreleme anahtarlarınız üzerinde tek denetim sağlamanıza de olanak sağlar. Kendi şifreleme anahtarlarınızı oluşturabilir ve yönetebilirsiniz. Azure Storage istemci kitaplığı 'nın anahtar şifreleme anahtarı (KEK) kullanılarak Sarmalanan (şifreli) bir içerik şifreleme anahtarı (CEK) oluşturduğu bir Envelope teknii kullanır. KEK bir anahtar tanımlayıcısı tarafından tanımlanır ve asimetrik bir anahtar çifti veya simetrik anahtar olabilir ve yerel olarak yönetilebilir veya [Azure Key Vault](/azure/key-vault/key-vault-overview)depolanabilir.

İstemci tarafı şifreleme, Java ve .NET depolama istemci kitaplıklarında yerleşik olarak bulunur. İstemci uygulamaları içindeki verileri şifreleme ve kendi şifreleme anahtarlarınızı oluşturma ve yönetme hakkında bilgi için bkz. [istemci tarafı şifreleme ve Azure Key Vault Microsoft Azure depolama](/azure/storage/common/storage-client-side-encryption) .

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Bekleyen veriler için Depolama Hizmeti Şifrelemesi etkinleştir
Dosya depolama için [depolama hizmeti şifrelemesi](/azure/storage/common/storage-service-encryption) etkinleştirildiğinde, veriler AES-256 şifrelemesi kullanılarak otomatik olarak şifrelenir. Microsoft tüm şifreleme, şifre çözme ve anahtar yönetimini işler. Bu özellik LRS ve GRS artıklık türleri için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamak için Azure Depolama güvenliği en iyi uygulamaları koleksiyonu tanıtılmıştır. PaaS dağıtımlarınızın güvenliğini sağlama hakkında daha fazla bilgi edinmek için bkz.:

- [PaaS dağıtımlarının güvenliğini sağlama](paas-deployments.md)
- [Azure Uygulama Hizmetleri 'ni kullanarak PaaS Web ve mobil uygulamalarının güvenliğini sağlama](paas-applications-using-app-services.md)
- [Azure 'da PaaS veritabanlarının güvenliğini sağlama](paas-applications-using-sql.md)
