---
title: Site Recovery ve bulut çözümü sağlayıcısı (CSP) programını kullanarak çok kiracılı bir ortamda Azure 'da VMware olağanüstü durum kurtarmayı ayarlama | Microsoft Docs
description: Azure Site Recovery ile çok kiracılı bir ortamda VMware olağanüstü durum kurtarmanın nasıl ayarlanacağını açıklar.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654958"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Bulut çözümü sağlayıcısı (CSP) programıyla çok kiracılı bir ortamda VMware olağanüstü durum kurtarmayı ayarlama

[CSP programı](https://partner.microsoft.com/cloud-solution-provider) , Microsoft 365, Enterprise Mobility Suite ve Microsoft Azure dahil olmak üzere Microsoft bulut hizmetleri için daha iyi bir hikaye toplayın. CSP ile iş ortakları müşterilerle uçtan uca ilişkiye sahiptir ve birincil ilişki iletişim noktası olur. İş ortakları, müşteriler için Azure abonelikleri dağıtabilir ve abonelikleri kendi değer eklenmiş, özelleştirilmiş tekliflerle birleştirebilir.

[Azure Site Recovery](site-recovery-overview.md), iş ortakları olarak müşteriler için olağanüstü durum KURTARMAYı doğrudan CSP aracılığıyla yönetebilirsiniz. Alternatif olarak, Site Recovery ortamları ayarlamak ve müşterilerin kendi olağanüstü durum kurtarma ihtiyaçlarını self servis bir şekilde yönetmesine izin vermek için CSP 'yi kullanabilirsiniz. Her iki senaryoda de iş ortakları Site Recovery ve müşterileri arasında Liaison. İş ortakları müşteri ilişkisine hizmet ve Site Recovery kullanım için fatura müşterileri.

Bu makalede, bir iş ortağının, çok kiracılı bir VMware çoğaltma senaryosunda CSP aracılığıyla kiracı abonelikleri oluşturup yönetebileceğini açıklar.

## <a name="prerequisites"></a>Önkoşullar

VMware çoğaltmasını ayarlamak için şunları yapmanız gerekir:

- [Hazırlama](tutorial-prepare-azure.md) Azure aboneliği, bir Azure sanal ağı ve bir depolama hesabı da dahil olmak üzere Azure kaynakları.
- Şirket içi VMware sunucularını ve VM 'Leri [hazırlayın](vmware-azure-tutorial-prepare-on-premises.md) .
- Her kiracı için Kiracı VM 'Leri ve vCenter sunucularınız ile iletişim kurabilen ayrı bir yönetim sunucusu oluşturun. Yalnızca siz bir iş ortağı olarak bu yönetim sunucusuna erişim haklarına sahip olmanız gerekir. [Çok kiracılı ortamlar](vmware-azure-multi-tenant-overview.md)hakkında daha fazla bilgi edinin.

## <a name="create-a-tenant-account"></a>Kiracı hesabı oluşturma

1. [Microsoft Iş Ortağı Merkezi](https://partnercenter.microsoft.com/)' nde, CSP hesabınızda oturum açın.
2. **Pano** menüsünde **müşteriler**' i seçin.
3. Açılan sayfada **Müşteri Ekle** düğmesine tıklayın.
4. **Yeni müşteri** sayfasında, kiracının hesap bilgileri ayrıntılarını girin.

    ![Hesap bilgileri sayfası](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. **İleri: abonelikler**' e tıklayın.
6. Abonelik seçimi sayfasında **Microsoft Azure** onay kutusunu seçin. Başka abonelikleri şimdi veya başka bir zamanda ekleyebilirsiniz.
7. **Gözden geçirme** sayfasında, kiracı ayrıntılarını onaylayın ve ardından **Gönder**' e tıklayın.
8. Kiracı hesabını oluşturduktan sonra, varsayılan hesabın ve bu aboneliğin parolasının ayrıntılarını görüntüleyen bir onay sayfası görüntülenir. Bilgileri kaydedin ve Azure portal oturum açma sayfasından parolayı daha sonra gerektiği şekilde değiştirin.

Bu bilgileri kiracı ile birlikte paylaşabilirsiniz veya gerekirse ayrı bir hesap oluşturup paylaşabilirsiniz.

## <a name="access-the-tenant-account"></a>Kiracı hesabına erişin

Kiracının aboneliğine Microsoft Iş Ortağı Merkezi panosu aracılığıyla erişebilirsiniz.

1. **Müşteriler** sayfasında, kiracı hesabının adına tıklayın.
2. Kiracı hesabının **abonelikler** sayfasında, mevcut hesap aboneliklerini izleyebilir ve gerektiğinde daha fazla abonelik ekleyebilirsiniz.
3. Kiracının olağanüstü durum kurtarma işlemlerini yönetmek için **tüm kaynaklar ' ı (Azure Portal)** seçin. Bu, kiracının Azure aboneliklerine erişmenizi sağlar.

    ![Tüm kaynaklar bağlantısı](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Azure portal sağ üst köşesindeki Azure Active Directory bağlantısına tıklayarak erişimi doğrulayabilirsiniz.

    ![Azure Active Directory bağlantısı](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Artık Azure portal kiracının tüm Site Recovery işlemlerini gerçekleştirebilir ve yönetebilirsiniz. Yönetilen olağanüstü durum kurtarma için kiracı aboneliğine CSP aracılığıyla erişmek için, daha önce açıklanan süreci izleyin.

## <a name="assign-tenant-access-to-the-subscription"></a>Aboneliğe kiracı erişimi atama

1. Olağanüstü durum kurtarma altyapısının ayarlanmış olduğundan emin olun. İş ortakları, olağanüstü durum kurtarmanın yönetilip yönetilmediği veya self servis olmasından bağımsız olarak, CSP portalı aracılığıyla kiracı aboneliklerine erişir. Kasayı ayarlayın ve altyapıyı kiracı aboneliklerine kaydedin.
1. Kiracıyı [oluşturduğunuz hesapla](#create-a-tenant-account)birlikte sağlayın.
1. Aşağıdaki şekilde, CSP portalı aracılığıyla kiracı aboneliğine yeni bir kullanıcı ekleyebilirsiniz:

    1. Kiracının CSP aboneliği sayfasına gidin ve ardından **Kullanıcılar ve lisanslar** seçeneğini belirleyin.

       ![Kiracının CSP abonelik sayfası](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Şimdi ilgili ayrıntıları girerek ve izinler ' i seçerek ya da bir CSV dosyasındaki kullanıcı listesini karşıya yükleyerek yeni bir kullanıcı oluşturun.

    1. Yeni bir kullanıcı oluşturduktan sonra, Azure portal geri dönün. **Abonelik** sayfasında, ilgili aboneliği seçin.

    1. **Erişim denetimi (IAM)** öğesini seçin ve ardından **rol atamaları**' na tıklayın.

    1. İlgili erişim düzeyiyle bir kullanıcı eklemek için **rol ataması Ekle** ' ye tıklayın. CSP portalı üzerinden oluşturulan kullanıcılar rol atamaları sekmesinde görüntülenir.

        ![Kullanıcı ekleme](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Çoğu yönetim işlemi için *katkıda* bulunan rolü yeterlidir. Bu erişim düzeyine sahip kullanıcılar, erişim düzeyleri ( *sahip* düzeyi erişim için gerekli) dışında bir abonelik üzerinde her şeyi gerçekleştirebilir.
- Site Recovery Ayrıca, erişim düzeylerini gerektiği şekilde kısıtlamak için kullanılabilen üç [önceden tanımlı kullanıcı rolü](site-recovery-role-based-linked-access-control.md)de vardır.

## <a name="multi-tenant-environments"></a>Çok kiracılı ortamlar

Üç büyük çok kiracılı model vardır:

* **Paylaşılan barındırma hizmetleri sağlayıcısı (HSP)**: ortak fiziksel altyapıyı kullanır ve aynı altyapıda birden çok Kiracı VM barındırmak için paylaşılan kaynaklar (vCenter, datacenters, fiziksel depolama vb.) kullanır. Ortak, yönetilen bir hizmet olarak olağanüstü durum kurtarma yönetimi sağlayabilir veya kiracı bir self servis çözümü olarak olağanüstü durum kurtarma yapabilir.

* **Adanmış barındırma hizmetleri sağlayıcısı**: iş ortağı fiziksel altyapıyı kullanır, ancak her kiracının sanal makinelerini ayrı bir altyapıda barındırmak için adanmış kaynakları (birden çok sanal ortalar, fiziksel veri depoları vb.) kullanır. Ortak, yönetilen bir hizmet olarak olağanüstü durum kurtarma yönetimi sağlayabilir veya kiracı bunu self servis çözümü olarak kullanabilir.

* **Yönetilen hizmetler sağlayıcısı (MSP)**: müşteri, VM 'leri barındıran fiziksel altyapının sahibidir ve iş ortağı olağanüstü durum kurtarma etkinleştirme ve yönetimi sağlar.

Bu makalede açıklandığı gibi kiracı abonelikleri ayarlayarak, ilgili çok kiracılı modellerden herhangi birinde müşterileri etkinleştirmeye hızlı bir başlangıç yapabilirsiniz. Farklı çok kiracılı modeller hakkında daha fazla bilgi edinmek ve şirket içi erişim denetimlerini [burada](vmware-azure-multi-tenant-overview.md)etkinleştirmek için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- Azure Site Recovery dağıtımlarını yönetmek için [Azure rol tabanlı erişim denetimi (Azure RBAC)](site-recovery-role-based-linked-access-control.md) hakkında daha fazla bilgi edinin.
- VMware 'den Azure 'a [çoğaltma mimarisi](vmware-azure-architecture.md)hakkında daha fazla bilgi edinin.
- VMware VM 'lerini Azure 'a çoğaltma [öğreticisini gözden geçirin](vmware-azure-tutorial.md) .
VMware VM 'lerini Azure 'a çoğaltmak için [çok kiracılı ortamlar](vmware-azure-multi-tenant-overview.md) hakkında daha fazla bilgi edinin.
