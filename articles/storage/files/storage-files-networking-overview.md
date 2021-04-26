---
title: Azure dosyaları ağ oluşturma konuları | Microsoft Docs
description: Azure dosyaları için ağ seçeneklerine genel bakış.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759696"
---
# <a name="azure-files-networking-considerations"></a>Azure dosyaları ağ iletişimi konuları 
Bir Azure dosya paylaşımının bağlantısını iki şekilde yapabilirsiniz:

- Sunucu Ileti bloğu (SMB), ağ dosya sistemi (NFS) (Önizleme) veya Dosyasıest protokolleri aracılığıyla paylaşıma doğrudan erişme. Bu erişim stili, birincil olarak çok sayıda şirket içi sunucuyu ortadan kaldırmak için kullanılır.
- Azure Dosya Eşitleme ile şirket içi sunucuda (veya bir Azure VM 'de) Azure dosya paylaşımının bir önbelleğini oluşturma ve şirket içi sunucudaki dosya paylaşımının verilerine (SMB, NFS, FTPS, vb.) kullanım örneği için erişim Bu erişim deseninin her ikisi de şirket içi performans ve bulut ölçeğinden ve Azure Backup gibi sunucusuz eklenebilir hizmetlerden en iyi şekilde birleştirilemediğinden yararlı olur.

Bu makalede, kullanım örneği Azure Dosya Eşitleme kullanmak yerine doğrudan Azure dosya paylaşımında erişim için çağrı yapıldığında, için ağ yapılandırma konusuna odaklanılır. Azure Dosya Eşitleme dağıtımına yönelik ağ konuları hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme ağ değerlendirmeleri](../file-sync/file-sync-networking-overview.md).

Azure dosya paylaşımları için ağ yapılandırması, Azure depolama hesabında yapılır. Depolama hesabı, birden çok dosya paylaşımını ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzunu temsil eden bir yönetim yapısıdır. Depolama hesapları, dosya paylaşımlarınıza ağ erişimini güvenli hale getirmenize yardımcı olan birden çok ayarı sunar: ağ uç noktaları, depolama hesabı güvenlik duvarı ayarları ve aktarım sırasında şifreleme. 

Bu kavramsal kılavuzu okumadan önce [bir Azure dosyaları dağıtımı Için planlamayı](storage-files-planning.md) okumanız önerilir.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Bu video, Azure dosya paylaşımlarını doğrudan bilgi çalışanları ve uygulamaları için beş basit adımda güvenli bir şekilde kullanıma sunma kılavuzudur. Aşağıdaki bölümler, videoda başvurulan belgelere bağlantılar ve ek bağlam sağlar.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Azure dosya paylaşımlarınız ile erişme
Bir depolama hesabı içinde bir Azure dosya paylaşımından dağıtırken, dosya paylaşımınıza depolama hesabının genel uç noktası aracılığıyla hemen erişilebilir. Bu, bir kullanıcının oturum açma kimliği tarafından yetkilendirilmiş istekler gibi kimliği doğrulanmış isteklerin Azure içinden veya dışından güvenli bir şekilde kaynaklanabilmesi anlamına gelir. 

Birçok müşteri ortamında, Azure VM 'lerinden gelen Mount başarılı olsa bile, şirket içi iş istasyonunuzda Azure dosya paylaşımının ilk bağlanması başarısız olur. Bunun nedeni, SMB 'nin iletişim kurmak için kullandığı bağlantı noktasını pek çok kuruluşun ve Internet hizmet sağlayıcısının (ISS) engellemesini, bağlantı noktası 445 ' dir. NFS paylaşımlarında bu sorun yoktur. Bu uygulama, SMB protokolünün eski ve kullanımdan kaldırılmış sürümleriyle ilgili güvenlik kılavuzlarından kaynaklanır. SMB 3,0, internet güvenli bir protokol olsa da, SMB 'nin eski sürümleri, özellikle SMB 1,0 değildir. Azure dosya paylaşımlarına yalnızca genel uç nokta aracılığıyla SMB 3,0 ve en son dosya Protokolü (aynı zamanda bir internet güvenli Protokolü) aracılığıyla dışarıdan erişilebilir.

Şirket içinden Azure SMB dosya paylaşımınıza erişmenin en kolay yolu, şirket içi ağınızı 445 numaralı bağlantı noktasına açmak için Microsoft, ortamınızdaki SMB 1,0 ' yi kaldırmak için aşağıdaki adımları önerir:

1. SMB 1,0 ' nin, kuruluşunuzun cihazlarında kaldırıldığından veya devre dışı bırakıldığından emin olun. Windows ve Windows Server 'ın Şu anda desteklenen tüm sürümleri, SMB 1,0 ' nin kaldırılmasını veya devre dışı bırakılmasını destekler ve Windows 10, sürüm 1709 ' den başlayarak SMB 1,0 varsayılan olarak Windows 'da yüklü değildir. SMB 1,0 ' i devre dışı bırakma hakkında daha fazla bilgi için, bkz. işletim sistemine özgü sayfalarımız:
    - [Windows/Windows Server'ı güvenli hale getirme](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux güvenliğini sağlama](storage-how-to-use-files-linux.md#securing-linux)
1. Kuruluşunuzdaki hiçbir ürünün SMB 1,0 gerektirmediğinden emin olun ve bunu kaldıranları kaldırın. SMB 1,0 gerektiren Microsoft tarafından bilinen birinci ve üçüncü taraf ürünlerin tümünü içeren bir [SMB1 ürün Clearinghouse](https://aka.ms/stillneedssmb1)'ı sunuyoruz. 
1. Seçim SMB 1,0 trafiğinin kuruluş sınırınızdan ayrılmasını engellemek için kuruluşunuzun şirket içi ağı ile bir üçüncü taraf güvenlik duvarı kullanın.

Kuruluşunuz, ilke veya yönetmelik için 445 bağlantı noktasını bloke etmek isterse veya kuruluşunuz Azure 'a bir belirleyici yolu izlemek için trafik gerektiriyorsa, Azure dosya paylaşımlarınız için trafik tünellemek üzere Azure VPN Gateway veya ExpressRoute 'u kullanabilirsiniz. NFS paylaşımları, 445 bağlantı noktasına gerek duymadığından, bunlardan herhangi birini gerektirmez.

> [!Important]  
> Azure dosya paylaşımlarınızda erişim için alternatif bir yöntem kullanmaya karar verseniz bile, Microsoft, ortamınızdaki SMB 1,0 ' i kaldırmayı hala öneriyor.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Bir sanal özel ağ veya ExpressRoute üzerinden tünel trafiği
Şirket içi ağınız ve Azure arasında bir ağ tüneli oluşturduğunuzda, Azure 'daki bir veya daha fazla sanal ağ ile şirket içi ağınızı eşleyelim. Bir [sanal ağ](../../virtual-network/virtual-networks-overview.md)veya VNet, şirket içinde çalışdığınız geleneksel bir ağa benzerdir. Bir Azure depolama hesabı veya bir Azure VM gibi, VNet bir kaynak grubuna dağıtılan bir Azure kaynağıdır. 

Azure dosyaları, şirket içi iş istasyonlarınız ve sunucularınız ile Azure SMB/NFS dosya paylaşımlarınız arasında trafiği tünele sağlamak için aşağıdaki mekanizmaları destekler:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN Gateway, bir Azure sanal ağı ile Internet üzerinden farklı bir konum (örneğin, şirket içi) arasında şifrelenmiş trafik göndermek için kullanılan belirli bir sanal ağ geçidi türüdür. Azure VPN Gateway, bir depolama hesabının veya diğer Azure kaynaklarının yanı bir şekilde bir kaynak grubuna dağıtılabilecek bir Azure kaynağıdır. VPN ağ geçitleri iki farklı bağlantı türünü kullanıma sunar:
    - Azure ile tek bir istemci arasındaki VPN bağlantıları olan [Noktadan siteye (P2S) VPN](../../vpn-gateway/point-to-site-about.md) ağ geçidi bağlantıları. Bu çözüm, genellikle kuruluşunuzun şirket içi ağına ait olmayan cihazlar için yararlıdır; Örneğin, Azure dosya paylaşımlarını yoldayken evden, kahve dükkanı veya otel 'tan bağlayabilmek ister. Azure dosyaları ile P2S VPN bağlantısı kullanmak için, bağlanmak isteyen her istemci için bir P2S VPN bağlantısının yapılandırılması gerekir. Bir P2S VPN bağlantısının dağıtımını basitleştirmek için bkz. Azure [dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-windows.md) ve [Azure dosyaları Ile kullanmak Için Linux 'ta Noktadan siteye (P2S) VPN](storage-files-configure-p2s-vpn-linux.md)yapılandırma.
    - Azure ile kuruluşunuzun ağı arasındaki VPN bağlantıları olan [siteden siteye (S2S) VPN](../../vpn-gateway/design.md#s2smulti). S2S VPN bağlantısı, Azure dosya paylaşımınıza erişmesi gereken her istemci cihaz için yerine, kuruluşunuzun ağında barındırılan bir VPN sunucusu veya cihazı için bir kez VPN bağlantısı yapılandırmanıza olanak sağlar. Bir S2S VPN bağlantısının dağıtımını basitleştirmek için bkz. [Azure dosyaları ile kullanmak Için siteden siteye (S2S) VPN yapılandırma](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), Azure ile internet 'te geçiş olmayan şirket içi ağınız arasında tanımlı bir yol oluşturmanızı sağlar. ExpressRoute, şirket içi veri merkeziniz ile Azure arasında ayrılmış bir yol sağladığından, ExpressRoute, ağ performansı dikkate alındığı zaman yararlı olabilir. Kuruluşunuzun ilkesi veya mevzuata gereksinimleri, buluttaki kaynaklarınıza yönelik belirleyici bir yol gerektirdiğinde, ExpressRoute da iyi bir seçenektir.

Azure dosya paylaşımlarınız için kullandığınız tünel yönteminden bağımsız olarak, depolama hesabınızın trafiğinin normal internet bağlantınız yerine tünelden geçmesinden emin olmak için bir mekanizmaya ihtiyacınız vardır. Depolama hesabının genel uç noktasına yönlendirmek Teknik olarak mümkündür, ancak depolama hesapları herhangi bir zamanda depolama kümeleri arasında taşınabildiğinden, bu durum, bir bölgedeki Azure depolama kümelerinin tüm IP adreslerini sabit kodlamaya ihtiyaç duyar. Bu, yeni kümelerin her zaman eklendiğinden, IP adresi eşlemelerinin sürekli güncelleştirilmesini de gerektirir.

Depolama hesaplarınızın IP adresini VPN yönlendirme kurallarınıza sabit kodlamak yerine, depolama hesabınıza bir Azure sanal ağının adres alanından bir IP adresi veren özel uç noktalar kullanmanızı öneririz. Azure 'a bir tünel oluşturmak, şirket içi ağınız ile bir veya daha fazla sanal ağ arasında eşleme oluşturduğundan bu, doğru yönlendirmeyi dayanıklı bir şekilde sağlar.

### <a name="private-endpoints"></a>Özel uç noktalar
Azure dosyaları, bir depolama hesabı için varsayılan genel uç noktaya ek olarak, bir veya daha fazla özel uç noktaya sahip olmak için seçeneği sağlar. Özel uç nokta yalnızca bir Azure sanal ağı içinden erişilebilen bir uç noktadır. Depolama hesabınız için özel bir uç nokta oluşturduğunuzda, depolama hesabınız, şirket içi bir dosya sunucusu ya da NAS cihazının şirket içi ağınızın özel adres alanında bir IP adresi alma gibi, sanal ağınızın adres alanının içinden özel bir IP adresi alır. 

Tek bir özel uç nokta, belirli bir Azure sanal ağ alt ağı ile ilişkilendirilir. Depolama hesabı, birden fazla sanal ağda özel uç noktalara sahip olabilir.

Azure dosyaları ile özel uç noktalar kullanmak şunları yapmanızı sağlar:
- Özel eşleme ile VPN veya ExpressRoute bağlantısı kullanarak şirket içi ağlardan Azure dosya paylaşımlarınıza güvenli bir şekilde bağlanın.
- Depolama hesabı güvenlik duvarını genel uç noktasındaki tüm bağlantıları engelleyecek şekilde yapılandırarak Azure dosya paylaşımlarınızın güvenliğini sağlayın. Varsayılan olarak, özel bir uç nokta oluşturulması genel uç noktaya bağlantıları engellemez.
- Sanal ağ (ve eşleme sınırları) içindeki verilerin ayıklanarak engellenmesini sağlayarak Sanal ağ güvenliğini artırın.

Özel bir uç nokta oluşturmak için bkz. [Azure dosyaları için özel uç noktaları yapılandırma](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Özel uç noktalar ve DNS
Özel bir uç nokta oluşturduğunuzda, varsayılan olarak alt etki alanına karşılık gelen bir özel DNS bölgesi de oluşturur (veya var olan) `privatelink` . Tam anlamda, depolama hesabınız için özel bir uç nokta kullanmak üzere özel bir DNS bölgesi oluşturmak gerekmez, ancak Azure dosya paylaşımınızı Active Directory bir Kullanıcı sorumlusu veya dosya Dosyasıapı 'sinden erişim için bağlama sırasında genel ve açıkça gerekli değildir.

> [!Note]  
> Bu makalede, Azure ortak bölgeleri için depolama hesabı DNS son eki kullanılmaktadır `core.windows.net` . Bu yorum, Azure ABD kamu bulutu ve Azure Çin bulutu gibi Azure Sogeign bulutları için de geçerlidir. ortamınız için uygun son ekleri yerine koyun. 

Özel DNS bölgeniz için bir kayıt `storageaccount.privatelink.file.core.windows.net` ve depolama hesabının normal adı için BIR CNAME kaydı oluşturacağız ve bu da bu kalıbı izler `storageaccount.file.core.windows.net` . Azure özel DNS bölgeniz özel uç noktasını içeren sanal ağa bağlı olduğundan, `Resolve-DnsName` PowerShell 'den cmdlet 'ini bir Azure sanal makinesinde ÇAĞıRARAK DNS yapılandırmasını gözlemleyebilirsiniz (alternatif `nslookup` olarak Windows ve Linux 'ta):

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

Bu, depolama hesabının hem genel uç nokta hem de bir veya daha fazla özel uç nokta sergileyebilecekleri gerçeğini yansıtır. Depolama hesabı adının özel uç noktanın özel IP adresine çözümlendiğinden emin olmak için, şirket içi DNS sunucularınızda yapılandırmayı değiştirmeniz gerekir. Bu, çeşitli yollarla gerçekleştirilebilir:

- İstemcilerinizdeki ana bilgisayar dosyasını değiştirerek `storageaccount.file.core.windows.net` istenen özel uç noktanın özel IP adresine çözüm sağlayın. Azure dosya paylaşımlarınızı bağlamak isteyen her istemcide bu değişiklikleri yapmanız gerektiğinden, depolama hesabında veya özel uç noktada yapılan değişiklikler otomatik olarak işlenmediğinden, bu, üretim ortamları için kesinlikle önerilmez.
- Şirket içi DNS sunucularınızda için bir kayıt oluşturma `storageaccount.file.core.windows.net` . Bu, şirket içi ortamınızdaki istemcilerin her bir istemciyi yapılandırmaya gerek kalmadan depolama hesabını otomatik olarak çözümleyebilmesini sağlar, ancak bu çözüm, değişiklikler yansıtılmadığı için ana bilgisayar dosyasını değiştirmeye benzer şekilde Brittle. Bu çözüm Brittle olsa da, bazı ortamlar için en iyi seçenek olabilir.
- Şirket `core.windows.net` ıçı DNS sunucularınızdaki bölgeyi Azure özel DNS bölgenize iletin. Azure özel DNS ana bilgisayarına `168.63.129.16` yalnızca Azure özel DNS bölgesine bağlı sanal ağlardan erişilebilen özel bır IP adresi () üzerinden erişilebilir. Bu sınırlamaya geçici bir çözüm olarak, `core.windows.net` Azure özel DNS bölgesine iletecek sanal ağınız içinde ek DNS sunucuları çalıştırabilirsiniz. Bu ayarı basitleştirmek için, Azure sanal ağınızda DNS sunucularını otomatik olarak dağıtan ve istediğiniz gibi yapılandırabilecek PowerShell cmdlet 'leri sağladık. DNS iletmeyi ayarlamayı öğrenmek için bkz. [DNS 'ı Azure dosyaları Ile yapılandırma](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Depolama hesabı güvenlik duvarı ayarları
Bir güvenlik duvarı, bir depolama hesabı için genel uç noktaya hangi isteklerin erişmelerine izin verileceğini denetleyen bir ağ ilkesidir. Depolama hesabı güvenlik duvarını kullanarak, depolama hesabının genel uç noktasına erişimi belirli IP adresleri veya aralıklarıyla veya bir sanal ağ ile kısıtlayabilirsiniz. Genel olarak, bir depolama hesabı için çoğu güvenlik duvarı ilkesi, ağ erişimini bir veya daha fazla sanal ağa kısıtlar. 

Bir depolama hesabına erişimi bir sanal ağla kısıtlamak için iki yaklaşım vardır:
- Depolama hesabı için bir veya daha fazla özel uç nokta oluşturun ve tüm erişimi genel uç noktaya sınırlayın. Bu, yalnızca istenen sanal ağlardan gelen trafiğin depolama hesabı içindeki Azure dosya paylaşımlarına erişebilmesini sağlar.
- Genel uç noktayı bir veya daha fazla sanal ağla sınırlayın. Bu, *hizmet uç noktaları* adlı sanal ağın bir özelliği kullanılarak işe yarar. Bir hizmet uç noktası aracılığıyla bir depolama hesabıyla trafiği kısıtladığınızda, hala genel IP adresi aracılığıyla depolama hesabına erişmeye devam edersiniz.

> [!NOTE]
> NFS paylaşımları, genel IP adresi aracılığıyla depolama hesabının genel uç noktasına erişemez, yalnızca sanal ağları kullanarak depolama hesabının genel uç noktasına erişebilirler. NFS paylaşımları ayrıca, Özel uç noktaları kullanarak depolama hesabına erişebilir.

Depolama hesabı güvenlik duvarını yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

> [!IMPORTANT]
> Bu bölüm, SMB paylaşımları için iletim ayrıntılarının şifrelemesini anlatmaktadır. NFS paylaşımları ile aktarım sırasında şifreleme ile ilgili ayrıntılar için bkz. [güvenlik](storage-files-compare-protocols.md#security).

Varsayılan olarak, tüm Azure depolama hesaplarının geçiş etkin olarak şifrelenmesi vardır. Bu, SMB üzerinden bir dosya paylaşımının bağladığınızda veya dosyayı dosya paylaşımından (Azure portal, PowerShell/CLı veya Azure SDK 'Ları aracılığıyla) eriştiğinizde, Azure dosyaları yalnızca şifreleme veya HTTPS ile SMB 3.0 + ile yapılırsa bağlantıya izin verir. SMB 3,0 ' i veya SMB 3,0 ' i destekleyen ancak SMB 'yi destekleyen istemcileri desteklemeyen istemciler, aktarım sırasında şifreleme etkinse Azure dosya paylaşımının bağlanabilmesi mümkün olmayacaktır. Şifreleme ile SMB 3,0 ' i destekleyen işletim sistemleri hakkında daha fazla bilgi için bkz. [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)ve [Linux](storage-how-to-use-files-linux.md)için ayrıntılı Belgelerimiz. PowerShell, CLı ve SDK 'ların tüm geçerli sürümleri HTTPS 'yi destekler.  

Azure depolama hesabı için iletim sırasında şifrelemeyi devre dışı bırakabilirsiniz. Şifreleme devre dışı bırakıldığında, Azure dosyaları SMB 2,1, şifrelemeden SMB 3,0 ve HTTP üzerinden şifrelenmemiş en iyi API çağrıları da sağlar. Geçiş sırasında şifrelemeyi devre dışı bırakmak için birincil neden, Windows Server 2008 R2 veya daha eski Linux dağıtımı gibi eski bir işletim sisteminde çalıştırılması gereken eski bir uygulamayı desteklemedir. Azure dosyaları yalnızca Azure dosya paylaşımıyla aynı Azure bölgesi içinde SMB 2,1 bağlantılarına izin verir; Azure dosya paylaşımının Azure bölgesinin dışında bir SMB 2,1 istemcisi, örneğin şirket içi veya farklı bir Azure bölgesinde, dosya paylaşımıyla erişemeyecektir.

Aktarım sırasında şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama 'da güvenli aktarım gerektirme](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosyalar'a genel bakış](storage-files-introduction.md)
- [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)