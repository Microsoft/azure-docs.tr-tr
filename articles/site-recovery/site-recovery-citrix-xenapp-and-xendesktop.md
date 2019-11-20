---
title: Azure Site Recovery ile Citrix XenDesktop/XenApp olağanüstü durum kurtarmayı ayarlama
description: Bu makalede, Azure Site Recovery kullanarak Citrix XenDesktop ve XenApp dağıtımlarını olağanüstü durum kurtarma 'nın nasıl ayarlanacağı açıklanır.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084549"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>çok katmanlı Citrix XenApp ve XenDesktop dağıtımı için olağanüstü durum kurtarmayı ayarlama



Citrix XenDesktop, masaüstü ve uygulamaları herhangi bir kullanıcıya, her yerden bir ondemandservice olarak sunan bir masaüstü sanallaştırma çözümüdür. Esnek atama teknolojisine sahip XenDesktop, uygulamaları ve masaüstlerini kullanıcılara hızlı ve güvenli bir şekilde teslim edebilir.
Günümüzde Citrix XenApp herhangi bir olağanüstü durum kurtarma özelliği sağlamıyor.

İyi bir olağanüstü durum kurtarma çözümü, yukarıdaki karmaşık uygulama mimarilerinin çevresinde kurtarma planlarının modellenmesi ve ayrıca çeşitli katmanlar arasında uygulama eşlemelerini işlemek için özelleştirilmiş adımlar ekleyebilme olanağı sağlar, bu nedenle tek tıklamayla bir olağanüstü durum lideri durumunda çözümü daha düşük bir RTO 'ya bırakmak.

Bu belge, Hyper-V ve VMware vSphere platformlarındaki şirket içi Citrix XenApp dağıtımlarınız için bir olağanüstü durum kurtarma çözümü oluşturmaya yönelik adım adım yönergeler sağlar. Bu belge Ayrıca, kurtarma planlarını, desteklenen yapılandırma ve önkoşulları kullanarak Azure 'a yük devretme testi (olağanüstü durum kurtarma detayları) ve planlanmamış yük devretme gerçekleştirmeyi açıklar.


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce, aşağıdakileri anladığınızdan emin olun:

1. [Bir sanal makineyi Azure 'a çoğaltma](site-recovery-vmware-to-azure.md)
1. [Kurtarma ağını tasarlama](site-recovery-network-design.md)
1. [Azure 'a yük devretme testi yapma](site-recovery-test-failover-to-azure.md)
1. [Azure 'a yük devretme işlemi yapma](site-recovery-failover.md)
1. [Bir etki alanı denetleyicisini çoğaltma](site-recovery-active-directory.md)
1. [SQL Server çoğaltma](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Dağıtım desenleri

Citrix XenApp ve XenDesktop grubu genellikle aşağıdaki dağıtım düzenine sahiptir:

**Dağıtım kalıbı**

AD DNS sunucusu, SQL veritabanı sunucusu, Citrix Delivery Controller, StoreFront Server, XenApp Master (VDA), Citrix XenApp lisans sunucusu ile Citrix XenApp ve XenDesktop dağıtımı

![Dağıtım kalıbı 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery desteği

Bu makalenin amacı doğrultusunda, DR 'yi kurmak için vSphere 6,0/System Center VMM 2012 R2 tarafından yönetilen VMware sanal makinelerinde Citrix dağıtımları kullanılmıştır.

### <a name="source-and-target"></a>Kaynak ve hedef

**Senaryo** | **İkincil bir siteye** | **Azure’a**
--- | --- | ---
**Hyper-V** | Kapsamda değil | Yes
**VMware** | Kapsamda değil | Yes
**Fiziksel sunucu** | Kapsamda değil | Yes

### <a name="versions"></a>Sürümler
Müşteriler, XenApp bileşenlerini Hyper-V veya VMware üzerinde çalışan sanal makineler olarak veya fiziksel sunucu olarak dağıtabilir. Azure Site Recovery, hem fiziksel hem de sanal dağıtımları Azure ile koruyabilir.
XenApp 7,7 veya üzeri Azure 'da desteklendiğinden, olağanüstü durum kurtarma veya geçiş için yalnızca bu sürümlere sahip dağıtımlar Azure 'a devredilecek.

### <a name="things-to-keep-in-mind"></a>Göz önünde bulundurmanız gerekenler

1. XenApp yayımlanmış uygulamalar ve XenApp yayınlanan masaüstleri sunmak için sunucu işletim sistemi makinelerini kullanan şirket içi dağıtımları koruma ve kurtarma desteklenir.

2. Windows 10 dahil olmak üzere istemci sanal masaüstleri için masaüstü VDı 'yı sunmak üzere masaüstü işletim sistemleri kullanan şirket içi dağıtımları koruma ve kurtarma desteklenmez. Bunun nedeni, Site Recovery masaüstü Osları olan makinelerin kurtarılmasını desteklemez.  Ayrıca, bazı istemci sanal masaüstü işletim sistemleri (örn. Windows 7), Azure 'da lisanslama için henüz desteklenmiyor. Azure’da istemci/sunucu masaüstlerini lisanslama hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/licensing-faq/).

3.  Azure Site Recovery, mevcut şirket içi MCS veya PVS klonlarını çoğaltamaz ve koruyamaz.
Bu kopyaları teslim denetleyicisinden Azure RM sağlama kullanarak yeniden oluşturmanız gerekiyor.

4. NetScaler, FreeBSD tabanlı olduğundan ve Azure Site Recovery FreeBSD OS korumasını desteklemediğinden, NetScaler Azure Site Recovery kullanılarak korunamaz. Azure 'a yük devretmeden sonra Azure Market 'ten yeni bir NetScaler gereci dağıtmanız ve yapılandırmanız gerekir.


## <a name="replicating-virtual-machines"></a>Sanal makineleri çoğaltma

Citrix XenApp dağıtımının aşağıdaki bileşenlerinin çoğaltmayı ve kurtarmayı etkinleştirmek için korunması gerekir.

* AD DNS sunucusunun korunması
* SQL veritabanı sunucusunun korunması
* Citrix Delivery Controller 'ın korunması
* StoreFront sunucusu koruması.
* XenApp Master (VDA) koruması
* Citrix XenApp lisans sunucusu koruması


**AD DNS sunucusu çoğaltması**

Lütfen Azure 'da bir etki alanı denetleyicisini çoğaltmak ve yapılandırmak için [Azure Site Recovery ile Active Directory ve DNS 'Yi koruyun](site-recovery-active-directory.md) bölümüne bakın.

**SQL veritabanı sunucusu çoğaltması**

Lütfen SQL Server korumasına yönelik önerilen seçeneklerle ilgili ayrıntılı teknik yönergeler için [SQL Server olağanüstü durum kurtarma ve Azure Site Recovery SQL Server koruyun '](site-recovery-sql.md) e bakın.

Diğer bileşen sanal makinelerini Azure 'a Çoğaltmaya başlamak için [Bu yönergeleri](site-recovery-vmware-to-azure.md) izleyin.

![XenApp bileşenlerinin korunması](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**İşlem ve ağ ayarları**

Makineler korunduktan sonra (durum çoğaltılan öğeler altında "korumalı" olarak gösterilir), Işlem ve ağ ayarlarının yapılandırılması gerekir.
Işlem ve ağ > Işlem özellikleri ' nde, Azure VM adını ve hedef boyutunu belirtebilirsiniz.
Gerekirse, Azure gereksinimleriyle uyumlu olacak şekilde adı değiştirin. Ayrıca, Azure VM 'ye atanacak hedef ağ, alt ağ ve IP adresi hakkındaki bilgileri görüntüleyebilir ve ekleyebilirsiniz.

Şunlara dikkat edin:

* Hedef IP adresini ayarlayabilirsiniz. Bir IP adresi sağlamazsanız yük devredilen makine DHCP kullanır. Yük devretmede kullanılamayan bir adres ayarlarsanız, yük devretme işlemi çalışmaz. Hedef IP adresi, yük devretme ağı testinde kullanılabilirse aynı IP adresi yük devretme sınamasında da kullanılabilir.

* AD/DNS sunucusu için, şirket içi adresin elinizde olması, Azure sanal ağı için DNS sunucusuyla aynı adresi belirtmenizi sağlar.

Ağ bağdaştırıcılarının sayısı, hedef sanal makine için sizin belirlediğiniz boyuta göre aşağıdaki gibi belirlenmiştir:

*   Kaynak makinedeki ağ bağdaştırıcılarının sayısı, hedef makine boyutu için verilen ağ bağdaştırıcısı sayısına eşitse veya daha azsa hedef makine kaynakla aynı sayıda bağdaştırıcıya sahip olur.
*   Kaynak sanal makinenin bağdaştırıcı sayısı, hedef boyut için izin verilen sayıyı aşarsa maksimum hedef boyutu kullanılır.
* Örneğin, kaynak makinenin iki bağdaştırıcısı varsa ve hedef makine boyutu dört adet bağdaştırıcıyı destekliyorsa hedef makinenin iki bağdaştırıcısı olur. Kaynak makinenin iki bağdaştırıcısı varken hedef boyut yalnızca bir bağdaştırıcıyı destekliyorsa hedef makinenin bir bağdaştırıcısı olur.
*   Sanal makinede birden çok ağ bağdaştırıcısı varsa, hepsi aynı ağa bağlanır.
*   Sanal makinede birden çok ağ bağdaştırıcısı varsa, listede gösterilen ilk, Azure sanal makinesindeki varsayılan ağ bağdaştırıcısı olur.


## <a name="creating-a-recovery-plan"></a>Kurtarma planı oluşturma

XenApp bileşeni VM 'Leri için çoğaltma etkinleştirildikten sonra, bir sonraki adım bir kurtarma planı oluşturmaktır.
Kurtarma planı, yük devretme ve kurtarma için benzer gereksinimlere sahip sanal makineleri birlikte gruplandırır.  

**Kurtarma planı oluşturma adımları**

1. Kurtarma planına XenApp bileşeni sanal makinelerini ekleyin.
2. Kurtarma planları-> + kurtarma planı ' na tıklayın. Kurtarma planı için sezgisel bir ad sağlayın.
3. VMware sanal makineleri için: kaynak olarak VMware işlem sunucusu, hedef Microsoft Azure ve dağıtım Kaynak Yöneticisi modeli olarak seçin ve öğeleri seç ' e tıklayın.
4. Hyper-V sanal makineleri için: VMM sunucusu olarak kaynak ' ı seçin, Microsoft Azure olarak hedefleyin ve dağıtım modeli ' ni Kaynak Yöneticisi ve ardından öğeleri seç ' e tıklayın ve ardından XenApp dağıtım VM 'lerini seçin.

### <a name="adding-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme

Kurtarma planları, belirli başlangıç siparişi, komut dosyaları veya el ile gerçekleştirilen eylemler için yük devretme grupları eklemek üzere özelleştirilebilir. Aşağıdaki grupların kurtarma planına eklenmesi gerekir.

1. Yük devretme Group1: AD DNS
2. Yük devretme grup2: SQL Server VM 'Ler
2. Yük devretme Grup3: VDA ana görüntü VM 'si
3. Yük devretme group4: teslim denetleyicisi ve StoreFront sunucusu VM 'Leri


### <a name="adding-scripts-to-the-recovery-plan"></a>Kurtarma planına betikler ekleme

Betikler, kurtarma planındaki belirli bir gruptan önce veya sonra çalıştırılabilir. El ile gerçekleştirilen eylemler Ayrıca yük devretme sırasında dahil edilebilir ve gerçekleştirilebilir.

Özelleştirilmiş kurtarma planı aşağıdaki gibi görünür:

1. Yük devretme Group1: AD DNS
2. Yük devretme grup2: SQL Server VM 'Ler
3. Yük devretme Grup3: VDA ana görüntü VM 'si

   >[!NOTE]     
   >4, 6 ve el ile veya betik eylemlerini içeren 7 adımları yalnızca, MCS/PVS kataloglarıyla birlikte yalnızca şirket içi XenApp > ortamı için geçerlidir.

4. Grup 3 El Ile veya betik eylemi: Ana VDA VM 'yi kapatın.
Azure 'a yük devredildiği sırada ana VDA VM çalışır durumda olur. Azure barındırma kullanarak yeni bir MCS kataloğu oluşturmak için, ana VDA VM 'sinin durdurulmuş (ayrılmış) durumda olması gerekir. Azure portal 'ten sanal makineyi kapatır.

5. Yük devretme group4: teslim denetleyicisi ve StoreFront sunucusu VM 'Leri
6. Grup3 el ile veya betik eylemi 1:

    ***Azure RM konak bağlantısı ekle***

    Azure 'da yeni MCS katalogları sağlamak için teslim denetleyicisi makinesinde Azure ana bilgisayar bağlantısı oluşturun. Bu [makalede](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)açıklanan adımları izleyin.

7. Grup3 el ile veya betik eylemi 2:

    ***Azure 'da MCS kataloglarını yeniden oluşturma***

    Birincil sitedeki mevcut MCS veya PVS kopyaları Azure 'a çoğaltılmaz. Bu kopyaları, çoğaltılan ana VDA ve Azure sağlama 'yı teslim denetleyicisinden kullanarak yeniden oluşturmanız gerekir. Azure 'da MCS katalogları oluşturmak için bu [makalede](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) açıklanan adımları izleyin.

![XenApp bileşenleri için kurtarma planı](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >DNS 'yi, yük devredilen > sanal makinelerin yeni IP 'Leri ile güncelleştirmek ya da gerekirse yükü devredilen sanal makineye yük dengeleyici eklemek için [konumundaki](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) betikleri kullanabilirsiniz.


## <a name="doing-a-test-failover"></a>Yük devretme testi yapma

Yük devretme testi yapmak için [Bu kılavuzu](site-recovery-test-failover-to-azure.md) izleyin.

![Kurtarma planı](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Yük devretme yapma

Yük devretme yaparken [Bu kılavuzu](site-recovery-failover.md) izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Citrix XenApp ve XenDesktop dağıtımlarını bu teknik incelemeye çoğaltma hakkında [daha fazla bilgi](https://aka.ms/citrix-xenapp-xendesktop-with-asr) edinebilirsiniz. Site Recovery kullanarak [diğer uygulamaları çoğaltmak](site-recovery-workload.md) için rehbere bakın.
