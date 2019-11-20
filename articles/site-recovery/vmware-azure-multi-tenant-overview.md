---
title: Azure Site Recovery ile VMware VM çok kiracılı olağanüstü durum kurtarma
description: Çok kiracılı bir ortam (CSP) programında Azure 'a yönelik VMWare olağanüstü durum kurtarma desteğine yönelik Azure Site Recovery bir genel bakış sağlar.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083985"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>CSP ile Azure 'da VMware olağanüstü durum kurtarma için çok kiracılı desteğe genel bakış

[Azure Site Recovery](site-recovery-overview.md) , kiracı abonelikleri için çok kiracılı ortamları destekler. Ayrıca, Microsoft Bulut çözümü sağlayıcısı (CSP) programı aracılığıyla oluşturulup yönetilen kiracı abonelikleri için çoklu kiracıyı destekler.

Bu makalede, çok kiracılı VMware 'yi Azure çoğaltmaya uygulama ve yönetmeye ilişkin bir genel bakış sunulmaktadır.

## <a name="multi-tenant-environments"></a>Çok kiracılı ortamlar

Üç büyük çok kiracılı model vardır:

* **Paylaşılan barındırma hizmetleri sağlayıcısı (HSP)** : ortak fiziksel altyapıyı kullanır ve aynı altyapıda birden çok Kiracı VM barındırmak için paylaşılan kaynaklar (vCenter, datacenters, fiziksel depolama vb.) kullanır. Ortak, yönetilen bir hizmet olarak olağanüstü durum kurtarma yönetimi sağlayabilir veya kiracı bir self servis çözümü olarak olağanüstü durum kurtarma yapabilir.

* **Adanmış barındırma hizmetleri sağlayıcısı**: iş ortağı fiziksel altyapıyı kullanır, ancak her kiracının sanal makinelerini ayrı bir altyapıda barındırmak için adanmış kaynakları (birden çok sanal ortalar, fiziksel veri depoları vb.) kullanır. Ortak, yönetilen bir hizmet olarak olağanüstü durum kurtarma yönetimi sağlayabilir veya kiracı bunu self servis çözümü olarak kullanabilir.

* **Yönetilen hizmetler sağlayıcısı (MSP)** : müşteri, VM 'leri barındıran fiziksel altyapının sahibidir ve iş ortağı olağanüstü durum kurtarma etkinleştirme ve yönetimi sağlar.

## <a name="shared-hosting-services-provider-hsp"></a>Paylaşılan barındırma hizmetleri sağlayıcısı (HSP)

Diğer iki senaryo, paylaşılan barındırma senaryosunun alt kümeleridir ve aynı ilkeleri kullanır. Farklar, paylaşılan barındırma kılavuzunun sonunda açıklanmıştır.

Çok kiracılı bir senaryoda temel gereksinim, kiracıların yalıtılmış olması gerekir. Bir kiracı, başka bir kiracının barındırıldığı şeyi gözlemlayamaz. İş ortağı tarafından yönetilen bir ortamda, bu gereksinim, bir self servis ortamında olduğu için önemli değildir ve burada önemli olabilir. Bu makalede, kiracı yalıtımının gerekli olduğu varsayılır.

Mimari aşağıdaki diyagramda gösterilmiştir.

![Bir vCenter ile paylaşılan HSP](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Bir vCenter Server ile paylaşılan barındırma**

Diyagramda, her müşterinin ayrı bir yönetim sunucusu vardır. Bu yapılandırma, kiracıya özgü VM 'lere kiracı erişimini kısıtlar ve kiracı yalıtımı sağlar. VMware VM çoğaltma, VM 'Leri bulacak ve aracıları yükleyecek yapılandırma sunucusunu kullanır. Aynı ilkeler, çok kiracılı ortamlar için, vCenter Access Control kullanılarak VM bulmayı kısıtlama eklenmesiyle de geçerlidir.

Veri yalıtımı gereksinimi, tüm hassas altyapı bilgilerinin (örneğin, erişim kimlik bilgileri) kiracıların açıklanmadığını ortadan kaldırmasıdır. Bu nedenle, yönetim sunucusu tüm bileşenlerinin iş ortağının özel denetimi altında kalmasını öneririz. Yönetim sunucusu bileşenleri şunlardır:

* Yapılandırma Sunucusu
* İşlem sunucusu
* Ana hedef sunucu

Ayrı bir genişleme işlem sunucusu ayrıca iş ortağının denetimi altındadır.

## <a name="configuration-server-accounts"></a>Yapılandırma sunucusu hesapları

Çoklu kiracı senaryosunda bulunan her yapılandırma sunucusu iki hesap kullanır:

- **vCenter erişim hesabı**: Bu hesap, Kiracı VM 'lerini saptamak için kullanılır. Kendisine atanmış vCenter erişim izinleri vardır. Erişim sızıntılarını önlemeye yardımcı olmak için iş ortaklarının bu kimlik bilgilerini yapılandırma aracında girmesini öneririz.

- **Sanal makine erişim hesabı**: Bu hesap, bir otomatik gönderim ile, Kiracı VM 'lerine Mobility hizmet Aracısı 'nı yüklemek için kullanılır. Genellikle bir kiracının iş ortağı tarafından sağlayabilecek bir etki alanı hesabı veya ortağın doğrudan yönetebileceği bir hesaptır. Bir kiracı, ayrıntıları iş ortağıyla doğrudan paylaşmak istemiyor, yapılandırma sunucusuna sınırlı süreli erişim yoluyla kimlik bilgilerini girebilirler. Ya da iş ortağının yardımı ile Mobility hizmeti aracısını el ile yükleyebilir.

## <a name="vcenter-account-requirements"></a>vCenter hesabı gereksinimleri

Yapılandırma sunucusunu, kendisine atanmış özel bir role sahip bir hesapla yapılandırın.

- Rol atamasının her vCenter nesnesi için vCenter erişim hesabına uygulanması ve alt nesnelere yayılmaması gerekir. Bu yapılandırma, kiracı yalıtımı sağlar, çünkü erişim yayılması diğer nesnelere yanlışlıkla erişim elde edebilir.

    ![Alt nesnelere yay seçeneği](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternatif yaklaşım, veri merkezi nesnesine kullanıcı hesabı ve rolü atamak ve bunları alt nesnelere yaymalıdır. Ardından, belirli bir kiracıya erişilemeyen her nesne için hesaba **erişim** rolü (diğer kiracılara ait VM 'ler gibi) verin. Bu yapılandırma, kısaberbir yapılandırmadır. Her yeni alt nesneye Ayrıca otomatik olarak üst öğeden devralınan erişim verildiğinden, bu, yanlışlıkla erişim denetimleri sunar. Bu nedenle, ilk yaklaşımı kullanmanızı öneririz.

### <a name="create-a-vcenter-account"></a>VCenter hesabı oluşturma

1. Önceden tanımlanmış *salt okunurdur* rolünü kopyalayarak yeni bir rol oluşturun ve ardından buna uygun bir ad verin (örneğin, bu örnekte gösterildiği gibi Azure_Site_Recovery).
2. Bu role aşağıdaki izinleri atayın:

   * **Veri deposu**: alan ayır, veri deposuna gözatmaya, alt düzey dosya işlemlerine, dosya kaldırma, sanal makine dosyalarını güncelleştirme
   * **Ağ**: ağ atama
   * **Kaynak**: VM 'yi kaynak havuzuna ata, sanal makineyi kapalı GEÇIR, VM 'ye geçiş yap
   * **Görevler**: görev oluştur, görevi Güncelleştir
   * **VM-yapılandırma**: tümü
   * **VM-etkileşim** > Yanıt sorusu, cihaz BAĞLANTıSı, CD medyasını yapılandırma, disket medyasını yapılandırma, kapatma, açma, VMware araçları yüklemesi
   * **VM-envanter** > Mevcut, yeni oluştur, Kaydet, kayıt Sil
   * **VM-sağlama** > sanal makine Indirmesine izin ver, sanal makine dosyalarının karşıya yüklenmesine izin ver
   * **VM-anlık görüntü yönetimi** > anlık görüntüleri kaldır

       ![Rolü Düzenle iletişim kutusu](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Çeşitli nesneler için aşağıdaki gibi, vCenter hesabına (kiracı yapılandırma sunucusunda kullanılır) erişim düzeyleri atayın:

>| Nesne | Rol | Açıklamalar |
>| --- | --- | --- |
>| vCenter | Salt okunurdur | Yalnızca farklı nesneleri yönetmek için vCenter erişimine izin vermek için gereklidir. Hesap hiçbir şekilde bir kiracıya sağlanmaması veya vCenter 'daki herhangi bir yönetim işlemi için kullanılması durumunda bu izni kaldırabilirsiniz. |
>| Veri merkezi | Azure_Site_Recovery |  |
>| Konak ve konak kümesi | Azure_Site_Recovery | Erişim 'in, yük devretmeden önce ve yeniden çalışma sonrasında yalnızca erişilebilir ana bilgisayarların Kiracı VM 'Leri olması için, erişimin nesne düzeyinde olduğundan emin olur. |
>| Veri deposu ve veri deposu kümesi | Azure_Site_Recovery | Önceki gibi. |
>| Ağ | Azure_Site_Recovery |  |
>| Yönetim sunucusu | Azure_Site_Recovery | CS makinesi dışındaki tüm bileşenlere (CS, PS ve MT) erişim içerir. |
>| Kiracı VM 'Leri | Azure_Site_Recovery | Belirli bir kiracının tüm yeni kiracı VM 'lerinin de bu erişimi almasını sağlar veya Azure portal aracılığıyla keşfedilemez. |

VCenter hesabı erişimi artık tamamlanmış. Bu adım, yeniden çalışma işlemlerini tamamlamaya yönelik en düşük izinler gereksinimini karşılar. Ayrıca, bu erişim izinlerini mevcut ilkelerinizle birlikte kullanabilirsiniz. Mevcut izinlerinizi, daha önce ayrıntılandırılan adım 2 ' den rol izinleri içerecek şekilde değiştirmeniz yeterlidir.

### <a name="failover-only"></a>Yalnızca yük devretme
Olağanüstü durum kurtarma işlemlerini yalnızca yük devretme yapılıncaya kadar (yani, yeniden çalışma özellikleri olmadan) kısıtlamak için, önceki yordamı aşağıdaki özel durumlarla kullanın:

- *Azure_Site_Recovery* rolünü vCenter erişim hesabına atamak yerine, bu hesaba yalnızca *salt okunurdur* bir rol atayın. Bu izin kümesi VM çoğaltma ve yük devretme işlemlerini sağlar ve yeniden çalışmaya izin vermez.
- Önceki işlemdeki diğer her şey olduğu gibi kalır. Kiracı yalıtımı sağlamak ve VM bulmayı kısıtlamak için, her izin hala nesne düzeyinde atanır ve alt nesnelere yayılmaz.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Kaynakları kiracı aboneliğine dağıtma

1. Azure portal, bir kaynak grubu oluşturun ve her zamanki işlem başına bir kurtarma hizmetleri Kasası dağıtın.
2. Kasa kayıt anahtarını indirin.
3. Kasa kayıt anahtarını kullanarak kiracı için CS 'yi kaydettirin.
4. İki erişim hesabı için kimlik bilgilerini, vCenter sunucusuna erişmek için kullanılacak hesabı ve sanal makineye erişmek için hesabı girin.

    ![Yönetici yapılandırma sunucusu hesapları](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Kasadaki sunucuları kaydetme

1. Daha önce oluşturduğunuz kasadaki Azure portal, vCenter sunucusunu, oluşturduğunuz vCenter hesabını kullanarak yapılandırma sunucusuna kaydedin.
2. Her zamanki işleme göre Site Recovery için "altyapıyı hazırla" işlemini sona erdirin.
3. VM 'Ler artık çoğaltılmaya hazırdır. **Sanal makineler seçin** > **çoğaltma** ' da yalnızca kiracının VM 'lerinin görüntülendiğini doğrulayın.

## <a name="dedicated-hosting-solution"></a>Adanmış barındırma çözümü

Aşağıdaki diyagramda gösterildiği gibi, adanmış bir barındırma çözümünde mimari fark, her kiracının altyapısının yalnızca o kiracı için ayarlanansıdır.

![mimarisi-paylaşılan-HSP](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Birden çok vCenter ile adanmış barındırma senaryosu**

## <a name="managed-service-solution"></a>Yönetilen hizmet çözümü

Aşağıdaki diyagramda gösterildiği gibi, yönetilen bir hizmet çözümünde mimari fark, her kiracının altyapısının diğer kiracıların altyapısından da fiziksel olarak ayrılması gerektiğidir. Bu senaryo genellikle kiracı altyapının sahibi olduğunda ve bir çözüm sağlayıcısının olağanüstü durum kurtarmayı yönetmesini istediğinde vardır.

![mimarisi-paylaşılan-HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Birden çok vCenter ile yönetilen hizmet senaryosu**

## <a name="next-steps"></a>Sonraki adımlar
- Site Recovery ' de rol tabanlı erişim denetimi hakkında [daha fazla bilgi edinin](site-recovery-role-based-linked-access-control.md) .
- [VMware VM 'Lerinin Azure 'a olağanüstü durum kurtarmayı ayarlamayı](vmware-azure-tutorial.md)öğrenin.
- [VMware VM 'leri IÇIN CSP ile çoklu kiralama](vmware-azure-multi-tenant-csp-disaster-recovery.md)hakkında daha fazla bilgi edinin.
