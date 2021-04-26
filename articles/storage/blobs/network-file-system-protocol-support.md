---
title: Azure Blob depolamada ağ dosya sistemi 3,0 desteği (Önizleme) | Microsoft Docs
description: Blob Storage artık ağ dosya sistemi (NFS) 3,0 protokolünü desteklemektedir. Bu destek, Linux istemcilerinin bir Azure sanal makinesinden (VM) veya şirket içinde çalışan bir bilgisayardan BLOB depolama alanına bir kapsayıcı takmasına olanak sağlar.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 57dec4187ea42476ff1c0a993b751a7389da5639
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490361"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob depolamada ağ dosya sistemi (NFS) 3,0 protokol desteği (Önizleme)

Blob Storage artık ağ dosya sistemi (NFS) 3,0 protokolünü desteklemektedir. Bu destek, nesne depolama ölçeğinde ve fiyatlarında Linux dosya sistemi uyumluluğu sağlar ve Linux istemcilerinin bir Azure sanal makinesinden (VM) veya şirket içi bir bilgisayardan BLOB depolama alanına bir kapsayıcı takmalarını sağlar. 

> [!NOTE]
> Azure Blob depolamada NFS 3,0 protokol desteği genel önizlemeye sunuldu. Şu bölgelerde Standart katman performansına sahip GPV2 Storage hesaplarını destekler: Avustralya Doğu, Kore Orta, Doğu ABD ve Orta Güney ABD. Önizleme Ayrıca tüm genel bölgelerde Premium performans katmanıyla Blok Blobu destekler.

Bulutta yüksek performanslı bilgi Işlem (HPC) gibi büyük ölçekli eski iş yüklerini çalıştırmak her zaman bir zorluk olmuştur. Bunun nedeni, uygulamaların verilere erişmek için genellikle NFS veya sunucu Ileti bloğu (SMB) gibi geleneksel dosya protokollerini kullanlarıdır. Ayrıca, yerel bulut depolama hizmetleri, hiyerarşik bir ad alanı ve verimli meta veri işlemleri sağlayan dosya sistemleri yerine düz bir ad alanı ve kapsamlı meta veriler içeren nesne depolamasına odaklanır. 

Blob Storage artık hiyerarşik bir ad alanını destekliyor ve NFS 3,0 protokol desteğiyle birleştirildiğinde, Azure eski uygulamaları büyük ölçekli bulut nesne depolama alanının üzerinde çalıştırmanızı çok daha kolay hale getiriyor. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Bu özellik için uygun uygulamalar ve iş yükleri

NFS 3,0 protokol özelliği, yüksek aktarım hızı, yüksek ölçekli, medya işleme, risk benzetimleri ve Genomiks sıralaması gibi yoğun iş yüklerinin işlenmesine en uygun seçenektir. Birden çok okuyucu ve çok sayıda iş parçacığı kullanan diğer iş yükü türleri için bu özelliği kullanmayı göz önünde bulundurmanız gerekir, bu da yüksek bant genişliği gerektirir. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3,0 ve hiyerarşik ad alanı

NFS 3,0 protokol desteği, Blobların hiyerarşik bir ad alanında düzenlenmesini gerektirir. Bir depolama hesabı oluşturduğunuzda hiyerarşik bir ad alanını etkinleştirebilirsiniz. Hiyerarşik ad alanı kullanma özelliği Azure Data Lake Storage 2. tarafından sunulmuştur. Nesneleri (dosyaları), bilgisayarınızdaki dosya sisteminin organize olduğu şekilde bir dizin ve alt dizin hiyerarşisine düzenler.  Hiyerarşik ad alanı doğrusal olarak ölçeklenir ve veri kapasitesini veya performansını düşürür. Farklı protokoller hiyerarşik ad alanından genişletilir. NFS 3,0 protokolü, bu kullanılabilir protokollerden biridir.   

> [!div class="mx-imgBorder"]
> ![hiyerarşik ad alanı](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Blok Blobları olarak depolanan veriler

NFS 3,0 protokol desteğini etkinleştirirseniz, Depolama hesabınızdaki tüm veriler blok Bloblar olarak depolanır. Blok Blobları, büyük miktarlarda okuma ağır verileri verimli bir şekilde işlemek için iyileştirilmiştir. Blok Blobları bloklardan oluşur. Her blok bir blok KIMLIĞIYLE tanımlanır. Blok Blobu, en fazla 50.000 blok içerebilir. Bir blok Blobun içindeki her bir blok, hesabınızın kullandığı hizmet sürümü için izin verilen en büyük boyuta kadar farklı bir boyut olabilir.

Uygulamanız NFS 3,0 protokolünü kullanarak bir istek yaptığında, bu istek Blok Blobu işlemlerinin birleşimine çevrilir. Örneğin, NFS 3,0 okuma uzak yordam çağrısı (RPC) istekleri [BLOB al](/rest/api/storageservices/get-blob) işlemine çevrilir. NFS 3,0 yazma RPC istekleri, [Get bloğu listesi](/rest/api/storageservices/get-block-list), [PUT bloğu](/rest/api/storageservices/put-block)ve [PUT bloğu listesi](/rest/api/storageservices/put-block-list)birleşimine çevrilir.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Genel iş akışı: depolama hesabı kapsayıcısı bağlama

Linux istemcileriniz, bir Azure sanal makinesinden (VM) veya şirket içi bir bilgisayardan BLOB depolama alanına bir kapsayıcı bağlayabilir. Bir depolama hesabı kapsayıcısını bağlamak için bu işlemleri yapmanız gerekir.

1. Aboneliğiniz ile NFS 3,0 protokol özelliğini kaydedin.

2. Özelliğin kayıtlı olduğunu doğrulayın.

3. Bir Azure sanal ağı (VNet) oluşturun.

4. Ağ güvenliğini yapılandırın.

5. Yalnızca VNet 'ten trafiği kabul eden depolama hesabı oluşturun ve yapılandırın.

6. Depolama hesabında bir kapsayıcı oluşturun.

7. Kapsayıcıyı bağlayın.

Adım adım yönergeler için bkz. [ağ dosya sistemi (NFS) 3,0 protokolünü (Önizleme) kullanarak blob depolamayı bağlama](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Bu görevleri sırayla gerçekleştirmek önemlidir. Hesabınızda NFS 3,0 protokolünü etkinleştirmeden önce oluşturduğunuz kapsayıcıları bağlayamıyoruz. Ayrıca, hesabınızda NFS 3,0 protokolünü etkinleştirdikten sonra devre dışı bırakabilirsiniz.

## <a name="network-security"></a>Ağ güvenliği

Depolama hesabınız bir sanal ağ içinde yer almalıdır. VNet, istemcilerin depolama hesabınıza güvenli bir şekilde bağlanmasını sağlar. Hesabınızdaki verileri güvenli hale getirmenin tek yolu, VNet ve diğer ağ güvenlik ayarlarını kullanmaktır. Hesap anahtarı yetkilendirme, Azure Active Directory (AD) güvenliği ve erişim denetim listeleri (ACL 'Ler) dahil olmak üzere verilerin güvenliğini sağlamak için kullanılan diğer herhangi bir araç, NFS 3,0 protokol desteği etkinleştirilmiş hesaplarda henüz desteklenmemektedir. 

Daha fazla bilgi için bkz. [BLOB depolama Için ağ güvenlik önerileri](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Desteklenen ağ bağlantıları

Bir istemci ortak veya [özel bir uç nokta](../common/storage-private-endpoints.md)üzerinden bağlanabilir ve aşağıdaki ağ konumlarından herhangi birinden bağlanabilir:

- Depolama hesabınız için yapılandırdığınız VNet. 

  Bu makalede, bu VNet 'e *birincil VNET* olarak başvuracağız. Daha fazla bilgi için bkz. [bir sanal ağ üzerinden erişim verme](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Birincil VNet ile aynı bölgedeki eşlenmiş bir sanal ağ.

  Bu eşlenen VNet 'e erişime izin vermek için depolama hesabınızı yapılandırmanız gerekecektir. Daha fazla bilgi için bkz. [bir sanal ağ üzerinden erişim verme](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute ağ geçidi](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)kullanarak birincil VNET 'iniz ile bağlantılı bir şirket içi ağ. 

  Daha fazla bilgi edinmek için bkz. Şirket [içi ağlardan erişimi yapılandırma](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Eşlenmiş ağa bağlı bir şirket içi ağ.

  Bu işlem, [ağ geçidi](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)aktarımlarıyla birlikte [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute ağ geçidi](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) kullanılarak yapılabilir. 

> [!IMPORTANT]
> Şirket içi bir ağdan bağlanıyorsanız, istemcinizin 111 ve 2048 bağlantı noktaları üzerinden giden iletişime izin verdiğinden emin olun. NFS 3,0 protokolü bu bağlantı noktalarını kullanır.

## <a name="azure-storage-features-not-yet-supported"></a>Azure depolama özellikleri henüz desteklenmiyor

Hesabınızda NFS 3,0 protokolünü etkinleştirdiğinizde aşağıdaki Azure depolama özellikleri desteklenmez. 

- Azure Active Directory (AD) güvenlik

- POSIX benzeri erişim denetim listeleri (ACL 'Ler)

- Mevcut depolama hesaplarında NFS 3,0 desteğini etkinleştirebilme

- NFS 3,0 desteğini bir depolama hesabında devre dışı bırakma özelliği (etkinleştirildikten sonra)

- REST API 'Leri veya SDK 'ları kullanarak bloblara yazma özelliği. 
  
## <a name="nfs-30-features-not-yet-supported"></a>NFS 3,0 özellikleri henüz desteklenmiyor

Aşağıdaki NFS 3,0 özellikleri henüz desteklenmiyor.

- UDP üzerinden NFS 3,0. Yalnızca TCP üzerinden NFS 3,0 desteklenir.

- Ağ kilitleme Yöneticisi (NLM) ile dosyaları kilitleme. Mount komutları `-o nolock` parametresini içermelidir.

- Alt dizinler bağlanıyor. Kök dizinini (kapsayıcı) yalnızca bağlayabilirsiniz.

- Bağlama listeleme (örneğin: komutunu kullanarak `showmount -a` )

- Dışarı aktarmaları listeleme (örneğin: komutunu kullanarak `showmount -e` )

- Sabit bağlantı

- Kapsayıcıyı salt okunurdur dışa aktarma

## <a name="nfs-30-clients-not-yet-supported"></a>NFS 3,0 istemcileri henüz desteklenmiyor

Aşağıdaki NFS 3,0 istemcileri henüz desteklenmemektedir.

- NFS için Windows istemcisi

## <a name="pricing"></a>Fiyatlandırma

Önizleme sırasında, depolama hesabınızda depolanan veriler aylık GB başına blob depolama ücretlerine göre aynı kapasite fiyatı üzerinden faturalandırılır. 

Önizleme sırasında bir işlem ücretlendirilmez. İşlemlere yönelik fiyatlandırma değişikliğe tabidir ve genel kullanıma sunulduğunda belirlenir.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [ağ dosya sistemi (NFS) 3,0 protokolünü (Önizleme) kullanarak blob depolamayı bağlama](network-file-system-protocol-support-how-to.md).

- Performansı iyileştirmek için bkz. [Azure Blob depolamada (Önizleme) ağ dosya sistemi (NFS) 3,0 performans konuları](network-file-system-protocol-support-performance.md).
