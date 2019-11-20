---
title: Azure geçişi çoğaltma Gereci mimarisi | Microsoft Docs
description: Azure geçişi çoğaltma Gereci genel bir bakış sağlar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811446"
---
# <a name="replication-appliance"></a>Çoğaltma gereç

Bu makalede, Azure geçişi tarafından kullanılan çoğaltma Gereci açıklanır: Bir aracı tabanlı geçişi kullanarak sanal makineleri azure'a geçirme VMware Vm'lerini ve fiziksel makineler private/public bulut olduğunda server değerlendirmesi. 

Aracı [Azure geçişi](migrate-overview.md) hub. Hub'ı değerlendirme ve geçiş için yerel araçlar sağlar, aynı zamanda diğer Azure Hizmetleri ve üçüncü taraf bağımsız yazılım satıcılarına (ISV) araçları.


## <a name="appliance-overview"></a>Gereç genel bakış

Çoğaltma gereç, tek bir makine, bir VMware VM veya fiziksel sunucu olarak şirket olarak dağıtılır. Bunu çalıştırır:
- **Çoğaltma Gereci**: Çoğaltma Gereci iletişimleri koordine eder ve şirket içi VMware Vm'leri ve fiziksel sunucuları Azure'a çoğaltma için veri çoğaltma işlemlerini yönetir.
- **İşlem sunucusu**: Çoğaltma gereçte varsayılan olarak yüklenir ve aşağıdaki işlem sunucusu:
    - **Çoğaltma ağ geçidi**: Bu, bir çoğaltma ağ geçidi olarak davranır. Çoğaltma için etkinleştirilmiş makinelerden çoğaltma verilerini alır. Çoğaltma verileri önbelleğe alma, sıkıştırma ve şifreleme ile iyileştirir ve Azure'a gönderir.
    - **Aracı yükleyici**: Mobility hizmetinin göndererek yükleme gerçekleştirir. Her çalıştırma için geçiş çoğaltmak istiyorsanız makine şirket içinde ve bu hizmeti yüklü olmalıdır.

## <a name="appliance-deployment"></a>Gereç dağıtım

**Olarak dağıtma** | **İçin kullanılan** | **Ayrıntılar**
--- | --- |  ---
VMware VM | Genellikle, Azure geçişi geçiş aracı ile aracı tabanlı geçiş kullanarak VMware Vm'lerini geçiş sırasında kullanılır. | Azure geçişi hub'ından OVA şablonu indirin ve vCenter Server'a VM Gereci oluşturmak için içeri aktarın.
Fiziksel makine | Bir VMware altyapınız yoksa, şirket içi fiziksel sunucuları geçirirken ya da bir OVA şablonu kullanarak bir VMware VM oluşturmak zamanınız yoksa kullanılır. | Azure geçişi hub'ından yazılım yükleyicisini indirin ve gereç makine kurmak için çalıştırın.

## <a name="appliance-deployment-requirements"></a>Gereç dağıtım gereksinimleri

[Gözden geçirme](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) dağıtım gereksinimleri.



## <a name="appliance-license"></a>Gereç lisans
Gereç, 180 gün boyunca geçerli bir Windows Server 2016 değerlendirme lisansı ile birlikte gelir. Değerlendirme süresi sona erme yakın ise, indirin ve yeni Gereci dağıtmadan veya gerecinin VM işletim sistemi lisans etkinleştirmenizi öneririz.

## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, belirtilen çoğaltma ilkesini kullanarak Azure depolama için ilk çoğaltma başlar. 
2. Trafik, internet üzerinden genel uç noktaları Azure depolama alanına çoğaltır. Trafiği bir siteden siteye sanal özel ağ (VPN) bir şirket içi siteden Azure'a çoğaltılması desteklenmez.
3. İlk çoğaltma sonlandırıldıktan sonra değişim çoğaltması başlar. Bir makine için izlenen değişiklikler kaydedilir.
4. İletişim şu şekilde olur:
    - Vm'leri HTTPS 443 numaralı bağlantı noktasında çoğaltma Gereci ile çoğaltma yönetimi için gelen iletişim.
    - Çoğaltma gereç HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltma işlemlerini yönetir.
    - VM'ler, gelen çoğaltma verilerini HTTPS 9443 numaralı bağlantı noktasında (çoğaltma gerecinde çalıştıran) işlem sunucusuna gönderir. Bu bağlantı noktası değiştirilebilir.
    - İşlem sunucusu çoğaltma verilerini alıp, en iyi duruma getirir ve şifreler ve Azure depolamaya bağlantı noktası 443 üzerinden giden gönderir.
5. Çoğaltma verilerinin bir önbellek depolama hesabına azure'da ilk land kaydeder. Bu günlükler işlenir ve bir Azure'da depolanan verileri yönetilen disk.

![Mimari](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Gereç yükseltmeleri

Gereç el ile Azure geçişi hub'ından yükseltilir. Her zaman en son sürümünü çalıştırmanızı öneririz.

1. Azure'da geçirme > sunucuları > Azure geçişi: Server değerlendirmesi, altyapı sunucularını tıklatın **yapılandırma sunucusu**.
2. İçinde **yapılandırma sunucusu**, bir bağlantı görünür **aracı sürümü** çoğaltma gerecinin yeni bir sürüm olduğunda kullanılabilir. 
3. Çoğaltma Gereci makinesinde yükleyiciyi indirin ve yükseltme yükleyin. Yükleyici, sürüm geçerli gerecinde çalıştıran algılar.
 
## <a name="next-steps"></a>Sonraki adımlar

[Bilgi nasıl](tutorial-assess-vmware.md#set-up-the-appliance-vm) VMware için gerecini ayarlamak için.
[Bilgi nasıl](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) gerecini ayarlamak için Hyper-V için.

