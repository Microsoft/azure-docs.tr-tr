---
title: Azure Lab Services mimari temelleri | Microsoft Docs
description: Bu makale, Laboratuvar Hizmetleri ve bir laboratuvarın temel mimarisi tarafından kullanılan temel kaynakları kapsar.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: 71d59e8bcf7b4078255d6b119e9d62f366e46033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437329"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Azure Lab Services mimari temelleri

Azure Lab Services, Laboratuvar Hizmetleri için gereken kaynakların sizin için işlendiği anlamına gelen bir SaaS (hizmet olarak yazılım) çözümüdür. Bu makale, Laboratuvar Hizmetleri ve bir laboratuvarın temel mimarisi tarafından kullanılan temel kaynakları kapsar.  

Azure Lab Services, kendi kaynaklarınızı laboratuvar hizmetleriyle birlikte kullanmanıza imkan tanıyan birkaç alan sağlar.  VM 'Leri kendi ağınızda kullanma hakkında daha fazla bilgi için bkz. nasıl yapılır [sanal ağ](how-to-connect-peer-virtual-network.md).  Paylaşılan görüntü galerisindeki görüntüleri yeniden kullanmak için bkz. [paylaşılan görüntü Galerisi nasıl iliştiriyor](how-to-attach-detach-shared-image-gallery.md).

Aşağıda bir sınıf laboratuvarının temel mimarisi verilmiştir.  Laboratuvar hesabı aboneliğinizde barındırılır. Öğrenci VM 'leri, VM 'Leri desteklemek için gereken kaynaklarla birlikte Laboratuvar Hizmetleri 'nin sahip olduğu bir abonelikte barındırılır. Laboratuvar hizmeti aboneliklerinde neler olduğunu daha ayrıntılı bir şekilde konuşalım.

![Labs temel mimarisi](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Barındırılan kaynaklar

Bir sınıf Laboratuvarı çalıştırmak için gereken kaynaklar, Microsoft tarafından yönetilen Azure aboneliklerinden birinde barındırılır.  Kaynaklar, eğitmen için bir şablon sanal makinesi, her öğrencinin sanal makinesi ve yük dengeleyici, sanal ağ ve ağ güvenlik grubu gibi ağla ilgili öğeler içerir.  Bu abonelikler şüpheli etkinlik için izlenir.  Bu izlemenin VM uzantısı veya ağ örüntü izleme aracılığıyla sanal makinelere dışarıdan yapıldığını unutmayın.  [Bağlantıyı kesme sırasında](how-to-enable-shutdown-disconnect.md) devre dışı bırakırsanız, sanal makinede bir tanılama uzantısı etkinleştirilir. Uzantı, Laboratuvar Hizmetleri 'nin Uzak Masaüstü Protokolü (RDP) oturum bağlantı kesme olayından haberdar olmasına olanak sağlar.

## <a name="virtual-network"></a>Sanal Ağ

Her laboratuvar kendi sanal ağı tarafından yalıtılmıştır.  Laboratuvarda eşlenmiş bir [sanal ağ](how-to-connect-peer-virtual-network.md)varsa, her laboratuvar kendi alt ağı tarafından yalıtılmıştır.  Öğrenciler, sanal makinilerine bir yük dengeleyici aracılığıyla bağlanır.  Hiçbir öğrenci sanal makinesi ortak IP adresine sahip değil; yalnızca özel bir IP adresi vardır.  Öğrenci bağlantı dizesi, yük dengeleyicinin genel IP adresi ve 49152 ile 65535 arasında rastgele bir bağlantı noktası olacaktır.  Yük dengeleyicideki gelen kurallar, işletim sistemine bağlı olarak bağlantıyı, uygun sanal makinenin bağlantı noktası 22 (SSH) ya da bağlantı noktası 3389 (RDP) olarak iletir. NSG diğer bağlantı noktalarında trafiği dışarıda önler.

## <a name="access-control-to-the-virtual-machines"></a>Sanal makinelere erişim denetimi

Laboratuvar Hizmetleri, öğrencinin sanal makinelerinde başlatma ve durdurma gibi eylemleri gerçekleştirme yeteneğini işler.  Ayrıca, VM bağlantı bilgilerine erişimi de denetler.

Laboratuvar Hizmetleri ayrıca öğrencilerinin hizmete kaydedilmesini da işler. Şu anda iki farklı erişim ayarı var: kısıtlanmış ve Kısıtlanmamış. Daha fazla bilgi için bkz. [Laboratuvar kullanıcılarını yönetme](how-to-configure-student-usage.md#send-invitations-to-users) makalesi. Kısıtlı erişim, Laboratuvar Hizmetleri 'nin, erişim izni vermeden önce öğrencilerin kullanıcı olarak eklendiğini doğrular. Kısıtlanmamış, kayıt bağlantısına sahip oldukları ve laboratuvarda kapasite olduğu sürece herhangi bir kullanıcının kaydedebileceği anlamına gelir. Yasak olmayan Hackathon olayları için yararlı olabilir.

Sınıf laboratuvarında barındırılan öğrenci VM 'Leri, laboratuvarın Oluşturucusu tarafından ayarlanan bir Kullanıcı adı ve parolaya sahiptir.  Alternatif olarak, laboratuvarın Oluşturucusu kayıtlı öğrencilerin ilk oturum açma sırasında kendi parolasını seçmesine izin verebilir.  

## <a name="next-steps"></a>Sonraki adımlar

Laboratuvar hizmetlerinde kullanılabilen özellikler hakkında daha fazla bilgi edinmek için bkz. [Azure Lab Services kavramlar](classroom-labs-concepts.md) ve [Azure Lab Services genel bakış](classroom-labs-overview.md).
