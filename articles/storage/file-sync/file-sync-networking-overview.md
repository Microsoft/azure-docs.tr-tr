---
title: Azure Dosya Eşitleme ağ değerlendirmeleri | Microsoft Docs
description: Şirket içindeki dosyaları önbelleğe almak için Azure Dosya Eşitleme kullanmak üzere ağ iletişimini nasıl yapılandıracağınızı öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6c761edec571f404a538025c868750bc5712eced
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797303"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure Dosya Eşitleme ağ değerlendirmeleri
Bir Azure dosya paylaşımının bağlantısını iki şekilde yapabilirsiniz:

- Doğrudan SMB veya FileREST protokolleri aracılığıyla paylaşıma erişme. Bu erişim stili, birincil olarak çok sayıda şirket içi sunucuyu ortadan kaldırmak için kullanılır.
- Azure Dosya Eşitleme ile şirket içi sunucuda (veya bir Azure VM 'de) Azure dosya paylaşımının bir önbelleğini oluşturma ve şirket içi sunucudaki dosya paylaşımının verilerine (SMB, NFS, FTPS, vb.) kullanım örneği için erişim Bu erişim deseninin her ikisi de şirket içi performans ve bulut ölçeğinden ve Azure Backup gibi sunucusuz eklenebilir hizmetlerden en iyi şekilde birleştirilemediğinden yararlı olur.

Bu makalede, kullanım örneği Azure dosya paylaşımının SMB üzerinden doğrudan bağlanması yerine şirket içi dosyaları önbelleğe almak için Azure Dosya Eşitleme kullanmak için ağ iletişimini yapılandırma konusuna odaklanılır. Bir Azure dosyaları dağıtımı için ağ hususları hakkında daha fazla bilgi için bkz. [Azure dosyaları ağ iletişimi konuları](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

Azure Dosya Eşitleme için ağ yapılandırması iki farklı Azure nesnesine yayılmıştır: bir depolama eşitleme hizmeti ve bir Azure depolama hesabı. Depolama hesabı, birden çok dosya paylaşımını ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzunu temsil eden bir yönetim yapısıdır. Depolama eşitleme hizmeti, Azure Dosya Eşitleme ile kurulan bir güven ilişkisine sahip Windows dosya sunucuları olan kayıtlı sunucuları temsil eden bir yönetim yapısıdır ve eşitleme ilişkisinin topolojisini tanımlayan eşitleme gruplarıdır. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Windows dosya sunucusu Azure Dosya Eşitleme Azure 'a bağlanıyor 
Bir şirket içi Windows dosya sunucusu ile Azure dosyaları ve Azure Dosya Eşitleme ayarlamak ve kullanmak için, temel bir internet bağlantısının ötesinde Azure için özel ağ gerekmez. Azure Dosya Eşitleme dağıtmak için, Azure ile eşitlemek istediğiniz Windows dosya sunucusuna Azure Dosya Eşitleme aracısını yüklersiniz. Azure Dosya Eşitleme Aracısı iki kanal aracılığıyla bir Azure dosya paylaşımıyla eşitlemeye erişir:

- Azure dosya paylaşımınıza erişmek için HTTPS tabanlı protokol olan FileREST protokolü. FileREST protokolü veri aktarımı için standart HTTPS 'yi kullandığından, yalnızca bağlantı noktası 443 ' nin erişilebilir olması gerekir. Azure Dosya Eşitleme, şirket içi Windows Server 'larınızdaki verileri Azure dosya paylaşımınıza aktarmak için SMB protokolünü kullanmaz.
- Eşitleme bilgilerini değiş tokuş eden HTTPS tabanlı protokol olan Azure Dosya Eşitleme eşitleme protokolü, yani ortamınızdaki uç noktalar arasında, ortamınızdaki dosya ve klasörlerle ilgili sürüm bilgileri. Bu protokol Ayrıca, ortamınızda zaman damgaları ve erişim denetim listeleri (ACL 'Ler) gibi dosya ve klasörlerle ilgili meta verileri değiş tokuş etmek için de kullanılır. 

Azure dosyaları, Azure dosya paylaşımlarında doğrudan SMB protokol erişimi sağladığından, müşteriler genellikle Azure Dosya Eşitleme aracısının erişmesi için Azure dosya paylaşımlarını SMB ile bağlamak üzere özel ağ yapılandırmamaları gerektiğini merak ediyor. Bu yalnızca gerekli değildir, ancak Azure dosya paylaşımında yapılan değişiklikler üzerinde hızlı değişiklik algılamaların olmamasından dolayı (Azure dosya paylaşımındaki öğelerin boyutuna ve sayısına bağlı olarak 24 saatten uzun bir süre içinde bulunamayamayabilir), yönetici senaryoları dışında da önerilmez. Azure dosya paylaşımının doğrudan kullanılmasını isterseniz (örneğin, şirket içinde önbelleğe almak için Azure Dosya Eşitleme kullanmıyorsanız, [Azure dosyaları ağlarına genel bakış ' a](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)bakarak doğrudan erişim için ağ hususları hakkında daha fazla bilgi edinebilirsiniz.

Azure Dosya Eşitleme özel ağ yapılandırması gerektirmese de, bazı müşteriler aşağıdaki senaryoları etkinleştirmek için Gelişmiş ağ ayarlarını yapılandırmak isteyebilir:

- Kuruluşunuzun proxy sunucu yapılandırmasıyla birlikte çalışma.
- Kuruluşunuzun şirket içi güvenlik duvarını Azure dosyaları ve Azure Dosya Eşitleme Hizmetleri 'ne açın.
- ExpressRoute veya VPN bağlantısı üzerinden Azure dosyalarını ve Azure Dosya Eşitleme tünel oluşturma.

### <a name="configuring-proxy-servers"></a>Proxy sunucularını yapılandırma
Birçok kuruluş, Azure 'da olduğu gibi, kendi şirket içi ağı ve kaynakları arasındaki kaynaklar arasında aracı olarak bir ara sunucu kullanır. Ara sunucular ağ yalıtımı ve güvenlik gibi birçok uygulama için ve izleme ve günlüğe kaydetme gibi yararlı olur. Azure Dosya Eşitleme, bir ara sunucu ile tam olarak birlikte çalışabilir, ancak ortamınız için proxy uç noktası ayarlarını Azure Dosya Eşitleme ile el ile yapılandırmanız gerekir. Bu, Azure Dosya Eşitleme sunucusu cmdlet 'leri kullanılarak PowerShell aracılığıyla yapılmalıdır. 

Proxy sunucusu ile Azure Dosya Eşitleme yapılandırma hakkında daha fazla bilgi için bkz. [proxy sunucusu ile Azure dosya eşitleme yapılandırma](file-sync-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Güvenlik duvarlarını ve hizmet etiketlerini yapılandırma
Dosya sunucularınızı, güvenlik amacıyla en çok Internet konumundan ayırabilirsiniz. Ortamınızda Azure Dosya Eşitleme kullanmak için, Azure hizmetleri 'ni seçmek üzere güvenlik duvarınızı açmak üzere ayarlamanız gerekir. Bunu, [hizmet etiketleri](../../virtual-network/service-tags-overview.md)adlı bir mekanizmaya gereken HIZMETLER için IP adresi aralıklarını alarak yapabilirsiniz.

Azure Dosya Eşitleme, hizmet etiketlerinin tanımlandığı şekilde aşağıdaki hizmetler için IP adresi aralıklarını gerektirir:

| Hizmet | Açıklama | Hizmet etiketi |
|---------|-------------|-------------|
| Azure Dosya Eşitleme | Depolama eşitleme hizmeti nesnesi tarafından temsil edilen Azure Dosya Eşitleme hizmeti, verileri bir Azure dosya paylaşımıyla Windows dosya sunucusu arasında eşitlemenin temel etkinliğinden sorumludur. | `StorageSyncService` |
| Azure Dosyaları | Azure Dosya Eşitleme aracılığıyla eşitlenen tüm veriler Azure dosya paylaşımında depolanır. Windows dosya sunucularınızda değiştirilen dosyalar Azure dosya paylaşımınıza çoğaltılır ve şirket içi dosya sunucunuzdaki katmanlı dosyalar, bir Kullanıcı istediğinde sorunsuz bir şekilde indirilir. | `Storage` |
| Azure Resource Manager | Azure Resource Manager, Azure Yönetim arabirimidir. Azure Dosya Eşitleme sunucu kaydı ve devam eden eşitleme sunucusu görevleri dahil olmak üzere tüm yönetim çağrıları, Azure Resource Manager üzerinden yapılır. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory veya Azure AD, bir depolama Eşitleme hizmetine göre sunucu kaydını yetkilendirmek için gereken kullanıcı sorumlularını ve Azure Dosya Eşitleme bulut kaynaklarınıza erişim yetkisi sağlamak için gereken hizmet sorumlularını içerir. | `AzureActiveDirectory` |

Farklı bir bölgese bile Azure 'da Azure Dosya Eşitleme kullanıyorsanız, bu hizmete giden trafiğe izin vermek için hizmet etiketinin adını doğrudan ağ güvenlik grubunuzda kullanabilirsiniz. Bunun nasıl yapılacağı hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grupları](../../virtual-network/network-security-groups-overview.md). 

Şirket içi Azure Dosya Eşitleme kullanıyorsanız, güvenlik duvarınızın izin verilenler listenize belirli IP adresi aralıklarını almak için hizmet etiketi API 'sini kullanabilirsiniz. Bu bilgileri almak için iki yöntem vardır:

- Hizmet etiketlerini destekleyen tüm Azure hizmetleri için IP adresi aralıklarının geçerli listesi, haftalık olarak Microsoft Indirme merkezi 'nde bir JSON belgesi biçiminde yayımlanır. Her Azure bulutu, bu bulut için uygun olan IP adresi aralıklarına sahip kendi JSON belgesine sahiptir:
    - [Azure genel](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure ABD Kamu](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Çin](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Almanya](https://www.microsoft.com/download/details.aspx?id=57064)
- Hizmet etiketi bulma API 'SI (Önizleme), geçerli hizmet etiketleri listesinin programlı olarak alınmasına izin verir. Önizleme aşamasında hizmet etiketi bulma API 'SI, Microsoft Indirme Merkezi ' nde yayınlanan JSON belgelerinden daha az güncel bilgiler döndürebilir. Otomasyon tercihinizi temel alarak API yüzeyini kullanabilirsiniz:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Hizmetlerinizin adreslerini almak için hizmet etiketi API 'sinin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için bkz. [Azure dosya EŞITLEME IP adresleri Için Izin verme listesi](file-sync-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Bir sanal özel ağ veya ExpressRoute üzerinden tünel trafiği
Bazı kuruluşların, ek bir güvenlik katmanı için bir sanal özel özel ağ (VPN) veya ExpressRoute gibi bir ağ tünelinin üzerinde olması veya Azure ile iletişimin bir belirleyici yol ile iletişim sağlamak için Azure ile iletişim olması gerekir. 

Şirket içi ağınız ve Azure arasında bir ağ tüneli oluşturduğunuzda, Azure 'daki bir veya daha fazla sanal ağ ile şirket içi ağınızı eşleyelim. Bir [sanal ağ](../../virtual-network/virtual-networks-overview.md)veya VNet, şirket içinde çalışdığınız geleneksel bir ağa benzerdir. Bir Azure depolama hesabı veya bir Azure VM gibi, VNet bir kaynak grubuna dağıtılan bir Azure kaynağıdır. 

Azure dosyaları ve Dosya Eşitleme, şirket içi sunucularınız ile Azure arasında trafiği tünele sağlamak için aşağıdaki mekanizmaların desteklenmesi:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN Gateway, bir Azure sanal ağı ile Internet üzerinden farklı bir konum (örneğin, şirket içi) arasında şifrelenmiş trafik göndermek için kullanılan belirli bir sanal ağ geçidi türüdür. Azure VPN Gateway, bir depolama hesabının veya diğer Azure kaynaklarının yanı bir şekilde bir kaynak grubuna dağıtılabilecek bir Azure kaynağıdır. Azure Dosya Eşitleme, şirket içi bir Windows dosya sunucusu ile kullanılması gerektiği için, normalde Noktadan siteye [(S2S) VPN](../../vpn-gateway/design.md#s2smulti)kullanırsınız, ancak bu durum teknik olarak [Noktadan siteye (P2S) VPN](../../vpn-gateway/point-to-site-about.md)kullanmak için kullanılır. 

    Siteden siteye (S2S) VPN bağlantıları, Azure Sanal ağınızı ve kuruluşunuzun şirket içi ağını birbirine bağlama. S2S VPN bağlantısı, Azure dosya paylaşımınıza erişmesi gereken her istemci cihaz için yerine, kuruluşunuzun ağında barındırılan bir VPN sunucusu veya cihazı için bir kez VPN bağlantısı yapılandırmanıza olanak sağlar. Bir S2S VPN bağlantısının dağıtımını basitleştirmek için bkz. [Azure dosyaları ile kullanmak Için siteden siteye (S2S) VPN yapılandırma](../files/storage-files-configure-s2s-vpn.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), Azure ile internet 'te geçiş olmayan şirket içi ağınız arasında tanımlı bir yol oluşturmanızı sağlar. ExpressRoute, şirket içi veri merkeziniz ile Azure arasında ayrılmış bir yol sağladığından, ExpressRoute, ağ performansı dikkate alındığı zaman yararlı olabilir. Kuruluşunuzun ilkesi veya mevzuata gereksinimleri, buluttaki kaynaklarınıza yönelik belirleyici bir yol gerektirdiğinde, ExpressRoute da iyi bir seçenektir.

### <a name="private-endpoints"></a>Özel uç noktalar
Azure dosyaları ve Dosya Eşitleme depolama hesabı ve depolama eşitleme hizmeti aracılığıyla sağladığı Dosya Eşitleme, Azure dosyaları ve, kaynak başına bir veya daha fazla özel uç nokta olmasını sağlar. Bir Azure kaynağı için özel bir uç nokta oluşturduğunuzda, şirket içi Windows dosya sunucunuzun şirket içi ağınızın özel adres alanı içinde bir IP adresine sahip olduğu gibi, sanal ağınızın adres alanından özel bir IP adresi alır. 

> [!Important]  
> Depolama eşitleme hizmeti kaynağında özel uç noktaları kullanmak için, Azure Dosya Eşitleme Aracısı sürüm 10,1 veya üstünü kullanmanız gerekir. 10,1 ' dan önceki Aracı sürümleri, depolama eşitleme hizmeti 'ndeki özel uç noktaları desteklemez. Tüm önceki Aracı sürümleri, depolama hesabı kaynağında özel uç noktaları destekler.

Tek bir özel uç nokta, belirli bir Azure sanal ağ alt ağı ile ilişkilendirilir. Depolama hesapları ve depolama Eşitleme Hizmetleri, birden fazla sanal ağda özel uç noktalara sahip olabilir.

Özel uç noktaları kullanmak şunları yapmanızı sağlar:
- Özel eşleme ile VPN veya ExpressRoute bağlantısı kullanarak şirket içi ağlardan Azure kaynaklarınıza güvenli bir şekilde bağlanın.
- Azure dosyaları ve Dosya Eşitleme genel uç noktalarını devre dışı bırakarak Azure kaynaklarınızın güvenliğini sağlayın. Varsayılan olarak, özel bir uç nokta oluşturulması genel uç noktaya bağlantıları engellemez.
- Sanal ağ (ve eşleme sınırları) içindeki verilerin ayıklanarak engellenmesini sağlayarak Sanal ağ güvenliğini artırın.

Özel bir uç nokta oluşturmak için, bkz. [Azure dosya eşitleme için özel uç noktaları yapılandırma](file-sync-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Özel uç noktalar ve DNS
Özel bir uç nokta oluşturduğunuzda, varsayılan olarak alt etki alanına karşılık gelen bir özel DNS bölgesi de oluşturur (veya var olan) `privatelink` . Genel bulut bölgeleri için bu DNS bölgeleri `privatelink.file.core.windows.net` Azure dosyaları ve `privatelink.afs.azure.net` Azure dosya eşitleme içindir.

> [!Note]  
> Bu makalede, Azure ortak bölgeleri için depolama hesabı DNS son eki kullanılmaktadır `core.windows.net` . Bu yorum, Azure ABD kamu bulutu ve Azure Çin bulutu gibi Azure Sogeign bulutları için de geçerlidir. ortamınız için uygun son ekleri yerine koyun. 

Bir depolama hesabı ve depolama eşitleme hizmeti için özel uç noktalar oluşturduğunuzda, bunlar için ilgili özel DNS bölgelerinde bir kayıt oluşturacağız. Ayrıca, ilgili Privatelink adı için normal tam etki alanı adlarının CNAMEs olması gibi genel DNS girişini de güncelleştiririz. Bu, tam etki alanı adlarının, istek sahibi sanal ağın içindeyken özel uç nokta IP adreslerini işaret etmesini ve istek sahibi sanal ağın dışında olduğunda genel uç nokta IP adreslerini işaret etmesini sağlar. 

Azure dosyaları için, her özel uç noktanın, `storageaccount.privatelink.file.core.windows.net` Özel uç nokta için tek bir özel IP adresiyle eşlenmiş şekilde, tek bir tam etki alanı adı vardır. Azure Dosya Eşitleme için her özel uç nokta dört Azure Dosya Eşitleme farklı etki alanı adına sahiptir: yönetim, eşitleme (birincil), eşitleme (ikincil) ve izleme. Bu uç noktalar için tam etki alanı adları, genellikle, ad ASCII olmayan karakterler içermiyorsa depolama eşitleme hizmeti 'nin adını izler. Örneğin, depolama eşitleme hizmeti adınız `mysyncservice` Batı ABD 2 bölgedeyse, eşdeğer uç noktalar,, `mysyncservicemanagement.westus2.afs.azure.net` ve olur `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` `mysyncservicemonitoring.westus2.afs.azure.net` . Bir depolama eşitleme hizmeti için her özel uç nokta, 4 ayrı IP adresi içerir. 

Azure özel DNS bölgeniz özel uç noktasını içeren sanal ağa bağlı olduğundan, `Resolve-DnsName` PowerShell 'den cmdlet 'ini bir Azure sanal makinesinde ÇAĞıRARAK DNS yapılandırmasını gözlemleyebilirsiniz (alternatif `nslookup` olarak Windows ve Linux 'ta):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Bu örnekte, depolama hesabı `storageaccount.file.core.windows.net` Özel uç noktanın özel IP adresi olarak çözümlenmektedir `192.168.0.4` .

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Aynı komutu şirket içinde çalıştırırsanız, aynı depolama hesabı adının bunun yerine depolama hesabının genel IP adresine çözümlendiğini görürsünüz; `storageaccount.file.core.windows.net` , için BIR CNAME kaydıdır `storageaccount.privatelink.file.core.windows.net` , bu da depolama hesabını barındıran Azure Storage kümesi IÇIN bir CNAME kaydıdır:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Bu, Azure dosyalarının ve Azure Dosya Eşitleme kaynak başına hem genel uç noktalarını hem de bir veya daha fazla özel uç noktasını kullanıma sunabilecekleri gerçeğini yansıtır. Kaynaklarınızın tam etki alanı adlarının özel uç noktalar özel IP adreslerine çözümlendiğinden emin olmak için şirket içi DNS sunucularınızda yapılandırmayı değiştirmeniz gerekir. Bu, çeşitli yollarla gerçekleştirilebilir:

- Depolama hesaplarınız ve depolama Eşitleme Hizmetleri için tam etki alanı adlarının istenen özel IP adreslerine çözümlenmesi için istemcilerinizdeki ana bilgisayar dosyasını değiştirme. Özel uç noktalarınıza erişmesi gereken her istemcide bu değişiklikleri yapmanız gerektiğinden, üretim ortamları için bu kesinlikle önerilmez. Özel uç noktalarınızda/kaynaklarda yapılan değişiklikler (silme işlemleri, değişiklikler vb.) otomatik olarak işlenmez.
- `privatelink.file.core.windows.net` `privatelink.afs.azure.net` Azure kaynaklarınız için bir kayıt ile ve için şirket ıçı sunucularınızda DNS bölgeleri oluşturma. Bu, şirket içi ortamınızdaki istemcilerin her bir istemciyi yapılandırmaya gerek kalmadan Azure kaynaklarını otomatik olarak çözümleyebilmesini sağlar, ancak değişiklikler yansıtılmadığı için bu çözüm aynı şekilde Hosts dosyasını değiştirmek için de Brittle. Bu çözüm Brittle olsa da, bazı ortamlar için en iyi seçenek olabilir.
- Şirket `core.windows.net` `afs.azure.net` içi DNS sunucularınızdan ve bölgelerini Azure özel DNS bölgenize iletin. Azure özel DNS ana bilgisayarına `168.63.129.16` yalnızca Azure özel DNS bölgesine bağlı sanal ağlardan erişilebilen özel bır IP adresi () üzerinden erişilebilir. Bu sınırlamaya geçici bir çözüm olarak, sanal ağınız içinde, `core.windows.net` `afs.azure.net` eşdeğer Azure özel DNS bölgelerine iletmek ve bunlara YÖNELIK ek DNS sunucuları çalıştırabilirsiniz. Bu ayarı basitleştirmek için, Azure sanal ağınızda DNS sunucularını otomatik olarak dağıtan ve istediğiniz gibi yapılandırabilecek PowerShell cmdlet 'leri sağladık. DNS iletmeyi ayarlamayı öğrenmek için bkz. [DNS 'ı Azure dosyaları Ile yapılandırma](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
Azure Dosya Eşitleme aracıdan Azure dosya paylaşımınıza veya depolama eşitleme hizmetinize yapılan bağlantılar her zaman şifrelenir. Azure depolama hesaplarının, Azure dosyaları (ve depolama hesabından yönetilen diğer Azure Depolama Hizmetleri) ile iletişim için, aktarım sırasında şifreleme kullanılmasını zorunlu kılmak üzere bir ayarı olsa da, bu ayarı devre dışı bırakmak, Azure dosyalarıyla iletişim kurarken Azure Dosya Eşitleme şifrelemesini etkilemez. Varsayılan olarak, tüm Azure depolama hesaplarının geçiş etkin olarak şifrelenmesi vardır. 

Aktarım sırasında şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama 'da güvenli aktarım gerektirme](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)
- [Azure Dosya Eşitleme’yi dağıtma](file-sync-deployment-guide.md)