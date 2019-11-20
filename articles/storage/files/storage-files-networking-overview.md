---
title: Azure dosyaları ağ oluşturma konuları | Microsoft Docs
description: Azure dosyaları için ağ seçeneklerine genel bakış.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141797"
---
# <a name="azure-files-networking-considerations"></a>Azure dosyaları ağ iletişimi konuları 
Bir Azure dosya paylaşımının bağlantısını iki şekilde yapabilirsiniz:

- Doğrudan SMB veya FileREST protokolleri aracılığıyla paylaşıma erişme. Bu erişim stili, birincil olarak çok sayıda şirket içi sunucuyu ortadan kaldırmak için kullanılır.
- Azure Dosya Eşitleme ile şirket içi sunucuda Azure dosya paylaşımının bir önbelleğini oluşturma ve şirket içi sunucudaki dosya paylaşımının verilerine (SMB, NFS, FTPS, vb.) kullanım ihtimaliniz için erişme. Bu erişim deseninin her ikisi de şirket içi performans ve bulut ölçeğinden ve Azure Backup gibi sunucusuz eklenebilir hizmetlerden en iyi şekilde birleştirilemediğinden yararlı olur.

Bu makalede, kullanım örneği Azure Dosya Eşitleme kullanmak yerine doğrudan Azure dosya paylaşımında erişim için çağrı yapıldığında, için ağ yapılandırma konusuna odaklanılır. Azure Dosya Eşitleme dağıtımına yönelik ağ konuları hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarını yapılandırma](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Depolama hesabı ayarları
Depolama hesabı, birden çok dosya paylaşımını ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzunu temsil eden bir yönetim yapısıdır. Azure depolama hesapları, ağ güvenliğini sağlamak için iki temel ayar kümesini kullanıma sunar: aktarım ve güvenlik duvarları ve sanal ağlarda (VNet) şifreleme.

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
Varsayılan olarak, tüm Azure depolama hesaplarının geçiş etkin olarak şifrelenmesi vardır. Bu, SMB üzerinden bir dosya paylaşımının bağladığınızda veya dosyayı dosya paylaşımından (Azure portal, PowerShell/CLı veya Azure SDK 'Ları aracılığıyla) eriştiğinizde, Azure dosyaları yalnızca şifreleme veya HTTPS ile SMB 3.0 + ile yapılırsa bağlantıya izin verir. SMB 3,0 ' i veya SMB 3,0 ' i destekleyen ancak SMB 'yi destekleyen istemcileri desteklemeyen istemciler, aktarım sırasında şifreleme etkinse Azure dosya paylaşımının bağlanabilmesi mümkün olmayacaktır. Şifreleme ile SMB 3,0 ' i destekleyen işletim sistemleri hakkında daha fazla bilgi için bkz. [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)ve [Linux](storage-how-to-use-files-linux.md)için ayrıntılı Belgelerimiz. PowerShell, CLı ve SDK 'ların tüm geçerli sürümleri HTTPS 'yi destekler.  

Azure depolama hesabı için iletim sırasında şifrelemeyi devre dışı bırakabilirsiniz. Şifreleme devre dışı bırakıldığında, Azure dosyaları SMB 2,1, şifrelemeden SMB 3,0 ve HTTP üzerinden şifrelenmemiş en iyi API çağrıları da sağlar. Geçiş sırasında şifrelemeyi devre dışı bırakmak için birincil neden, Windows Server 2008 R2 veya daha eski Linux dağıtımı gibi eski bir işletim sisteminde çalıştırılması gereken eski bir uygulamayı desteklemedir. Azure dosyaları yalnızca Azure dosya paylaşımıyla aynı Azure bölgesi içinde SMB 2,1 bağlantılarına izin verir; Azure dosya paylaşımının Azure bölgesinin dışında bir SMB 2,1 istemcisi, örneğin şirket içi veya farklı bir Azure bölgesinde, dosya paylaşımıyla erişemeyecektir.

Aktarım sırasında şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama 'da güvenli aktarım gerektirme](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Güvenlik duvarları ve sanal ağlar 
Güvenlik Duvarı, isteklerin Azure dosya paylaşımlarına ve Depolama hesabınızdaki diğer depolama kaynaklarına erişmelerine izin verilen bir ağ ilkesidir. Varsayılan ağ ayarlarıyla bir depolama hesabı oluşturulduğunda, belirli bir ağla sınırlı değildir ve bu nedenle internet erişimi olur. Bu, internet üzerindeki herkesin depolama hesabınızda barındırılan Azure dosya paylaşımlarında depolanan verilere erişebileceği, ancak depolama hesabının herhangi bir ağdan gelen yetkili istekleri kabul edeceği anlamına gelir. İstekler bir depolama hesabı anahtarı, paylaşılan erişim imzası (SAS) belirteci (yalnızca FileREST) veya Active Directory bir Kullanıcı sorumlusu ile yetkilendirilebilirler. 

Bir depolama hesabı için güvenlik duvarı ilkesi, belirli IP adreslerine veya aralıklarına veya bir sanal ağa erişimi kısıtlamak için kullanılabilir. Genel olarak, bir depolama hesabı için çoğu güvenlik duvarı ilkesi, ağ erişimini bir sanal ağa kısıtlar. 

Bir [sanal ağ](../../virtual-network/virtual-networks-overview.md)veya VNet, kendi veri merkezinizde çalışdığınız geleneksel bir ağa benzerdir. Azure dosya paylaşımları, VM 'Ler, SQL veritabanları vb. gibi Azure kaynaklarınızın birbirleriyle iletişim kurması için güvenli bir iletişim kanalı oluşturmanızı sağlar. Bir Azure depolama hesabı veya bir Azure VM gibi, VNet bir kaynak grubuna dağıtılan bir Azure kaynağıdır. Ek ağ yapılandırması ile Azure sanal ağları, şirket içi ağlarınıza da bağlanabilir.

Bir Azure VM gibi kaynaklar bir sanal ağa eklendiğinde, sanal makineye bağlı bir sanal ağ arabirimi (NIC) özellikle bu VNet ile kısıtlanır. Bu, Azure VM 'lerinin NIC 'Leri olan sanallaştırılmış bilgisayarlar olduğu için mümkündür. Sanal makineler, Azure 'un hizmet olarak altyapı veya IaaS, ürün sırası 'nın bir parçası olarak sunulur. Azure dosya paylaşımları sunucusuz dosya paylaşımları olduğundan, VNet 'e ekleyebileceğiniz bir NIC 'e sahip değildir. Azure dosyaları, Azure 'un hizmet olarak platform veya PaaS, ürün sırası kapsamında sunulan farklı bir yol olduğunu fark etti. Bir depolama hesabının VNet 'in bir parçası olmasını sağlamak için Azure, hizmet uç noktaları olarak adlandırılan PaaS hizmetleri kavramını destekler. Hizmet uç noktası PaaS hizmetlerinin bir sanal ağın parçası olmasına olanak sağlar. Hizmet uç noktaları hakkında daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md).

Bir depolama hesabı, bir veya daha fazla sanal ağa eklenebilir. Depolama hesabınızı bir sanal ağa ekleme veya diğer güvenlik duvarı ayarlarını yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Azure ağı
Varsayılan olarak, Azure dosyaları da dahil olmak üzere Azure hizmetlerine internet üzerinden erişilebilir. Varsayılan olarak, depolama hesabınıza giden trafik şifrelendiğinden (ve SMB 2,1 takmaları bir Azure bölgesinin dışında hiçbir şekilde izin verilmediği), Azure dosya paylaşımlarınızın internet üzerinden erişimine yönelik olarak güvenli olmayan bir şey yoktur. Kuruluşunuzun ilkesi veya benzersiz yasal düzenleme gereksinimlerine bağlı olarak, Azure ile daha kısıtlayıcı bir iletişim gerektirebilir ve bu nedenle Azure, Azure dışından gelen trafiğin Azure dosyalarına nasıl alınacağını kısıtlamak için çeşitli yollar sağlar. Aşağıdaki hizmet tekliflerini kullanarak, Azure dosya paylaşımınıza erişirken ağınızı daha da güvenli hale getirebilirsiniz:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN Gateway, bir Azure sanal ağı ile Internet üzerinden farklı bir konum (örneğin, şirket içi) arasında şifrelenmiş trafik göndermek için kullanılan belirli bir sanal ağ geçidi türüdür. Azure VPN Gateway, bir depolama hesabının veya diğer Azure kaynaklarının yanı bir şekilde bir kaynak grubuna dağıtılabilecek bir Azure kaynağıdır. VPN ağ geçitleri iki farklı bağlantı türünü kullanıma sunar:
    - Azure ile tek bir istemci arasındaki VPN bağlantıları olan [Noktadan siteye (P2S) VPN](../../vpn-gateway/point-to-site-about.md) ağ geçidi bağlantıları. Bu çözüm, genellikle kuruluşunuzun şirket içi ağına ait olmayan cihazlar için yararlıdır; Örneğin, Azure dosya paylaşımlarını yoldayken evden, kahve dükkanı veya otel 'tan bağlayabilmek ister. Azure dosyaları ile P2S VPN bağlantısı kullanmak için, bağlanmak isteyen her istemci için bir P2S VPN bağlantısının yapılandırılması gerekir. 
    - Azure ile kuruluşunuzun ağı arasındaki VPN bağlantıları olan [siteden siteye (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti). S2S VPN bağlantısı, Azure dosya paylaşımınıza erişmesi gereken her istemci cihaz için yerine, kuruluşunuzun ağında barındırılan bir VPN sunucusu veya cihazı için bir kez VPN bağlantısı yapılandırmanıza olanak sağlar.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), Azure ile internet 'te geçiş olmayan şirket içi ağınız arasında tanımlı bir yol oluşturmanızı sağlar. ExpressRoute, şirket içi veri merkeziniz ile Azure arasında ayrılmış bir yol sağladığından, ExpressRoute, ağ performansı dikkate alındığı zaman yararlı olabilir. Kuruluşunuzun ilkesi veya mevzuata gereksinimleri, buluttaki kaynaklarınıza yönelik belirleyici bir yol gerektirdiğinde, ExpressRoute da iyi bir seçenektir.

## <a name="securing-access-from-on-premises"></a>Şirket içinden erişimi güvenli hale getirme 
Genel amaçlı dosya paylaşımlarını (Office belgeleri, PDF 'Ler, CAD belgeleri vb.) Azure dosyalarına geçirdiğinizde, kullanıcılarınız genellikle iş istasyonları, dizüstü bilgisayarlar ve tabletler gibi şirket içi cihazlardan dosyalarına erişmeye devam eder. Genel amaçlı bir dosya paylaşımının ana değerlendirmesi, şirket içi kullanıcıların Internet veya WAN aracılığıyla dosya paylaşımlarına güvenli bir şekilde erişmelerine nasıl olanak sağlamaktır.

Şirket içinden Azure dosya paylaşımınıza erişmenin en kolay yolu, şirket içi ağınızı bağlantı noktası 445 ' e, SMB 'nin kullandığı bağlantı noktasını açmak ve Azure portal tarafından belirtilen UNC yolunu bağlamadır. Bu, özel bir ağ gerektirmez. Çok sayıda müşteri, Microsoft 'un internet güvenli protokolü olduğunu düşünmediği SMB 1,0 ' deki eski güvenlik rehberinden dolayı 445 numaralı bağlantı noktasını açmak için yeniden kullanılabilir. Azure dosyaları, SMB 1,0 ' i uygulamıyor. 

SMB 3,0, internet güvenli dosya paylaşma protokolleri olan açık gereksinimle tasarlanmıştır. Bu nedenle, SMB 3.0 + kullanıldığında, bilgisayar ağı açısından, 445 numaralı bağlantı noktasını açmak, HTTPS bağlantıları için kullanılan bağlantı noktası olan 443 numaralı bağlantı noktasını açmaktan farklı değildir. Güvenli olmayan SMB 1,0 trafiğini engellemek için bağlantı noktası 445 ' ı engellemek yerine, Microsoft aşağıdaki adımları önerir:

> [!Important]  
> 445 numaralı bağlantı noktasını giden trafiğe bırakmaya karar verseniz bile, Microsoft, ortamınızdaki SMB 1,0 ' yi kaldırmak için bu adımları takip edebilir.

1. SMB 1,0 ' nin, kuruluşunuzun cihazlarında kaldırıldığından veya devre dışı bırakıldığından emin olun. Windows ve Windows Server 'ın Şu anda desteklenen tüm sürümleri, SMB 1,0 ' nin kaldırılmasını veya devre dışı bırakılmasını destekler ve Windows 10, sürüm 1709 ' den başlayarak SMB 1,0 varsayılan olarak Windows 'da yüklü değildir. SMB 1,0 ' i devre dışı bırakma hakkında daha fazla bilgi için, bkz. işletim sistemine özgü sayfalarımız:
    - [Windows/Windows Server güvenliğini sağlama](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux güvenliğini sağlama](storage-how-to-use-files-linux.md#securing-linux)
1. Kuruluşunuzdaki hiçbir ürünün SMB 1,0 gerektirmediğinden emin olun ve bunu kaldıranları kaldırın. SMB 1,0 gerektiren Microsoft tarafından bilinen birinci ve üçüncü taraf ürünlerin tümünü içeren bir [SMB1 ürün Clearinghouse](https://aka.ms/stillneedssmb1)'ı sunuyoruz. 
1. Seçim SMB 1,0 trafiğini engellemek için kuruluşunuzun şirket içi ağıyla üçüncü taraf bir güvenlik duvarı kullanın.

Kuruluşunuz ilke veya yönetmelik için 445 bağlantı noktasını bloke etmek isterse, bağlantı noktası 443 üzerinden trafiği tünel oluşturmak için Azure VPN Gateway veya ExpressRoute 'u kullanabilirsiniz. Bunları dağıtmaya yönelik belirli adımlar hakkında daha fazla bilgi edinmek için bkz. özel nasıl yapılır sayfaları:
- [Azure dosyaları ile kullanmak için siteden siteye (S2S) VPN yapılandırma](storage-files-configure-s2s-vpn.md)
- [Azure dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-windows.md)
- [Linux üzerinde Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-linux.md)

Kuruluşunuz, şirket içi sitenizin giden trafiğin buluttaki kaynaklarınızın belirleyici bir yolunu izlemesi gereken ek gereksinime sahip olabilir. Bu durumda, ExpressRoute bu gereksinimi karşılamayabilirdi.

## <a name="securing-access-from-cloud-resources"></a>Bulut kaynaklarından erişimin güvenliğini sağlama
Genellikle, bir şirket içi uygulama yükseltilmemiş ve buluta taşındığında, uygulama ve uygulamanın verileri aynı anda taşınır. Bu, bir kaldırma ve kaydırma geçişinin birincil değerlendirmesi için Azure dosya paylaşımının, dosya paylaşımının çalışması için erişmesi gereken belirli sanal makinelere veya Azure Hizmetlerine erişimini kilitleyen anlamına gelir. 

Ağ bağlantısı yapmasına izin verilen VM 'Leri veya diğer Azure kaynaklarını sınırlamak için sanal ağları kullanmak isteyebilirsiniz (Azure dosya paylaşımınıza SMB bağlama veya REST API çağrıları). Depolama hesabınıza şifreli olmayan trafiğe izin verirseniz Azure dosya paylaşımınızı her zaman bir VNet 'e koymayı öneririz. Aksi takdirde, VNET 'leri kullanıp kullanmayacağınızı, iş gereksinimlerinize ve kuruluş ilkenize göre kullanılması gereken bir karardır.

Azure dosya paylaşımınıza şifreli olmayan trafiğe izin vermek için asıl neden, SMB 2,1 ile Azure dosya paylaşımınıza erişen Windows Server 2008 R2 'yi, Windows 7 ' yi veya diğer eski işletim sistemini veya bazı Linux dağıtımları için şifreleme olmadan SMB 3,0 ' i desteklemektir. SMB 3.0 + şifrelemesini destekleyen işletim sistemlerinde şifreleme olmadan SMB 2,1 veya SMB 3,0 kullanılmasını önermiyoruz.

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosyalar'a genel bakış](storage-files-introduction.md)
- [Azure Dosyaları dağıtımını planlama](storage-files-planning.md)