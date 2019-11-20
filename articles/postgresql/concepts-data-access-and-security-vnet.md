---
title: PostgreSQL için Azure veritabanı 'nda sanal ağ (VNet) Hizmetleri uç noktasına genel bakış-tek sunucu
description: Sanal ağ (VNet) hizmet uç noktalarının PostgreSQL için Azure veritabanı-tek sunucu ile nasıl çalıştığını öğrenin.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b03be62a634d04f41513e7cf27c3cb55f69da438
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609978"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı-tek sunucu için sanal ağ hizmet uç noktalarını ve kurallarını kullanın

*Sanal ağ kuralları* , PostgreSQL Için Azure veritabanı sunucunuzun, sanal ağlardaki belirli alt ağlardan gönderilen iletişimleri kabul edip etmediğini denetleyen bir güvenlik duvarı güvenlik özelliğidir. Bu makalede, PostgreSQL için Azure veritabanı sunucunuza iletişimin güvenli bir şekilde yapılmasına olanak tanımak için sanal ağ kuralı özelliğinin neden bazen en iyi seçenektir.

Bir sanal ağ kuralı oluşturmak için öncelikle kuralın başvurması için bir [sanal ağ][vm-virtual-network-overview] (VNet) ve bir [sanal ağ hizmeti uç noktası][vm-virtual-network-service-endpoints-overview-649d] olmalıdır. Aşağıdaki resimde, bir sanal ağ hizmeti uç noktasının PostgreSQL için Azure veritabanı ile nasıl çalıştığı gösterilmektedir:

![VNet hizmeti uç noktasının nasıl çalıştığı hakkında örnek](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Bu özellik, Azure genel bulutu 'nın, PostgreSQL için Azure veritabanı 'nın Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için dağıtıldığı tüm bölgelerde kullanılabilir.
> VNet eşlemesi söz konusu olduğunda trafik, hizmet uç noktaları içeren bir ortak VNet ağ geçidi üzerinden akar ve eşe akışı gerekiyorsa, ağ geçidi VNet 'teki Azure sanal makinelerinin PostgreSQL için Azure veritabanı sunucusuna erişmesine izin vermek için lütfen bir ACL/VNet kuralı oluşturun.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminoloji ve açıklama

**Sanal ağ:** Azure aboneliğinizle ilişkili sanal ağlarınız olabilir.

**Alt ağ** Bir sanal ağ, **alt ağlar**içerir. Sahip olduğunuz tüm Azure sanal makineleri (VM 'Ler) alt ağlara atanır. Bir alt ağ birden çok VM veya başka işlem düğümü içerebilir. Ağınızı erişime izin verecek şekilde yapılandırmadığınız müddetçe, sanal ağınızın dışındaki işlem düğümleri sanal ağınıza erişemez.

**Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası][vm-virtual-network-service-endpoints-overview-649d] , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. Bu makalede, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL**tür adı ile ilgileniyoruz. Bu hizmet etiketi PostgreSQL için Azure veritabanı ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulanırken, tüm Azure SQL veritabanı, PostgreSQL Için Azure veritabanı ve alt ağdaki MySQL sunucuları Için Azure veritabanı için hizmet uç noktası trafiğini yapılandıracaksınız. 

**Sanal ağ kuralı:** PostgreSQL için Azure veritabanı sunucusu için bir sanal ağ kuralı, PostgreSQL için Azure veritabanı sunucunuzun erişim denetim listesinde (ACL) listelenen bir alt ağıdır. PostgreSQL için Azure veritabanı sunucunuzun ACL 'sinde olması için, alt ağda **Microsoft. SQL** tür adı bulunmalıdır.

Bir sanal ağ kuralı, PostgreSQL için Azure veritabanı 'na, alt ağdaki her düğümden gelen iletişimleri kabul etmesini söyler.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Bir sanal ağ kuralının avantajları

İşlem yapana kadar, alt ağlardaki VM 'Ler PostgreSQL için Azure veritabanı sunucusu ile iletişim kuramaz. İletişim kuran bir eylem, bir sanal ağ kuralı oluşturma işlemi olur. VNet kuralı yaklaşımını seçmeye yönelik korvaale, güvenlik duvarı tarafından sunulan rekabet güvenlik seçeneklerini içeren bir karşılaştırma ve kontrast tartışması gerektirir.

### <a name="a-allow-access-to-azure-services"></a>A. Azure hizmetlerine erişime izin ver

Bağlantı güvenlik bölmesinde, **Azure hizmetlerine erişime Izin ver**etiketli bir **açık/kapalı** düğmesi vardır. **Açık** ayarı tüm Azure IP adreslerinden ve tüm Azure alt ağlarının iletişimlerine izin verir. Bu Azure IP 'Leri veya alt ağları size ait olmayabilir. Bu **ayar** , PostgreSQL Için Azure veritabanınızın veritabanının olmasını istediğinizden daha açık olabilir. Sanal ağ kuralı özelliği, daha ayrıntılı bir denetim sağlar.

### <a name="b-ip-rules"></a>B. IP kuralları

PostgreSQL için Azure veritabanı güvenlik duvarı, iletişimin PostgreSQL için Azure veritabanı veritabanına kabul edileceği IP adresi aralıklarını belirtmenize olanak tanır. Bu yaklaşım, Azure özel ağının dışında olan kararlı IP adresleri için çok uygundur. Ancak, Azure özel ağı içindeki birçok düğüm *dinamik* IP adresleriyle yapılandırılır. SANAL makinenizin yeniden başlatılması gibi dinamik IP adresleri değişebilir. Bir güvenlik duvarı kuralında, bir üretim ortamında dinamik bir IP adresi belirtmek de bu şekilde yapılır.

VM 'niz için bir *statik* IP adresı alarak IP seçeneğini hurda yapabilirsiniz. Ayrıntılar için bkz. [Azure Portal kullanarak bir sanal makine için özel IP adreslerini yapılandırma][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Ancak, statik IP yaklaşımının yönetilmesi zor olabilir ve ölçekteki tamamlandığında maliyetli hale gelir. Sanal ağ kuralları kurmak ve yönetmek daha kolaydır.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Hizmet uç noktası tanımlamadan bir alt ağ üzerinde PostgreSQL için Azure veritabanı kullanılamaz

**Microsoft. SQL** Server ağınız sanal ağınızdaki bir alt ağda yer alıyorsa, sanal ağ içindeki tüm düğümler PostgreSQL Için Azure veritabanı sunucusu ile iletişim kurabilir. Bu durumda, sanal makineler herhangi bir sanal ağ kuralına veya IP kuralına gerek duymadan PostgreSQL için Azure veritabanı ile iletişim kurabilir.

Ancak, Ağustos 2018 itibariyle PostgreSQL için Azure veritabanı hizmeti henüz bir alt ağa atanabilen hizmetler arasında değil.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Sanal ağ kuralları hakkında ayrıntılar

Bu bölümde, sanal ağ kurallarıyla ilgili çeşitli ayrıntılar açıklanmaktadır.

### <a name="only-one-geographic-region"></a>Yalnızca bir coğrafi bölge

Her sanal ağ hizmeti uç noktası yalnızca bir Azure bölgesi için geçerlidir. Uç nokta diğer bölgelerin alt ağdan iletişim kabul etmesine izin vermez.

Herhangi bir sanal ağ kuralı, temeldeki uç noktasının geçerli olduğu bölge ile sınırlıdır.

### <a name="server-level-not-database-level"></a>Sunucu düzeyi, veritabanı düzeyi değil

Her bir sanal ağ kuralı, yalnızca sunucudaki belirli bir veritabanına değil, tüm PostgreSQL için Azure veritabanı sunucusuna uygulanır. Diğer bir deyişle, sanal ağ kuralı, veritabanı düzeyinde değil, sunucu düzeyinde geçerlidir.

#### <a name="security-administration-roles"></a>Güvenlik yönetimi rolleri

Sanal ağ hizmet uç noktalarının yönetiminde güvenlik rollerinin bir ayrımı vardır. Aşağıdaki rollerden her biri için eylem gereklidir:

- **Ağ Yöneticisi:** &nbsp;Uç noktasını açın.
- **Veritabanı Yöneticisi:** &nbsp;Verilen alt ağı PostgreSQL için Azure veritabanı sunucusuna eklemek üzere erişim denetim listesini (ACL) güncelleştirin.

*RBAC alternatifi:*

Ağ yöneticisinin ve veritabanı yöneticisinin rollerinin sanal ağ kurallarını yönetmek için gerekenden daha fazla özelliği vardır. Yalnızca kendi yeteneklerinin bir alt kümesi gereklidir.

Azure 'da [rol tabanlı erişim denetimi (RBAC)][rbac-what-is-813s] kullanarak yalnızca gerekli özellik alt kümesini içeren tek bir özel rol oluşturabilirsiniz. Özel rol, ağ yöneticisi ya da veritabanı Yöneticisi dahil olmak yerine kullanılabilir. Bir kullanıcıyı özel bir role eklerseniz, diğer iki ana yönetici rolüne kullanıcı ekleyerek güvenlik açıkağınızın yüzey alanı düşüktür.

> [!NOTE]
> Bazı durumlarda PostgreSQL için Azure veritabanı ve sanal ağ alt ağı farklı aboneliklerde bulunur. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelik da aynı Azure Active Directory kiracısında olmalıdır.
> - Kullanıcı, hizmet uç noktalarını etkinleştirme ve verilen sunucuya VNet-subnet ekleme gibi işlemleri başlatmak için gerekli izinlere sahiptir.
> - Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="limitations"></a>Sınırlamalar

PostgreSQL için Azure veritabanı 'nda, sanal ağ kuralları özelliği aşağıdaki sınırlamalara sahiptir:

- Bir Web uygulaması, VNet/alt ağdaki özel bir IP ile eşleştirilebilir. Hizmet uç noktaları, belirtilen VNet/alt ağdan açık olsa bile, Web uygulamasından sunucusuna yapılan bağlantıların VNet/alt ağ kaynağına değil, Azure genel IP kaynağı olur. Bir Web uygulamasından VNet güvenlik duvarı kurallarına sahip bir sunucuya bağlantıyı etkinleştirmek için, Azure hizmetlerinin sunucuda sunucuya erişmesine Izin vermelisiniz.

- PostgreSQL için Azure veritabanı güvenlik duvarında, her bir sanal ağ kuralı bir alt ağa başvurur. Bu başvurulan tüm alt ağlar, PostgreSQL için Azure veritabanı 'nı barındıran aynı coğrafi bölgede barındırılmalıdır.

- Her PostgreSQL için Azure veritabanı sunucusu, belirli bir sanal ağ için en fazla 128 ACL girişine sahip olabilir.

- Sanal ağ kuralları yalnızca Azure Resource Manager sanal ağlar için geçerlidir; [klasik dağıtım modeli][arm-deployment-model-568f] ağlarına değil.

- **Microsoft. SQL** Service etiketi kullanılarak PostgreSQL Için Azure veritabanı 'na sanal ağ hizmeti uç noktaları açmak, tüm Azure veritabanı hizmetleri için uç noktaları da sağlar: MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı, Azure SQL veritabanı ve Azure SQL veri ambarı.

- Yalnızca genel amaçlı ve bellek için iyileştirilmiş sunucuları için sanal ağ hizmet uç noktaları desteğidir.

- Güvenlik duvarında, IP adresi aralıkları aşağıdaki ağ öğelerine uygulanır, ancak sanal ağ kuralları şunları içermez:
    - [Siteden siteye (S2S) sanal özel ağ (VPN)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v] aracılığıyla şirket içi

## <a name="expressroute"></a>ExpressRoute

Ağınız [ExpressRoute][expressroute-indexmd-744v]kullanılarak Azure ağına bağlandıysa, her bağlantı hattı Microsoft Edge 'de ıkı genel IP adresi ile yapılandırılır. Azure genel eşlemesi kullanılarak Azure Storage gibi Microsoft hizmetlerine bağlanmak için iki IP adresi kullanılır.

Bağlantı hattınızdan PostgreSQL için Azure veritabanı 'na yönelik iletişime izin vermek için, Devrelerinizin genel IP adresleri için IP ağ kuralları oluşturmanız gerekir. ExpressRoute devrenizin genel IP adreslerini bulmak için Azure portal kullanarak ExpressRoute ile bir destek bileti açın.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET hizmet uç noktalarını açmadan sunucunuza VNET güvenlik duvarı kuralı ekleme

Yalnızca bir güvenlik duvarı kuralı ayarlandığında sunucunun VNet 'e güvenli hale getirilmesine yardımcı olmaz. Ayrıca güvenliğin etkili olabilmesi için VNet hizmet **uç noktalarını** açmanız gerekir. Hizmet uç noktalarını **Açık**olarak açtığınızda, VNET alt ağınız **kapalı** kalma süresini **Açık**olarak tamamlanana kadar kesinti yaşar. Bu, büyük sanal ağlar bağlamında özellikle doğrudur. Geçiş sırasında kesinti süresini azaltmak veya ortadan kaldırmak için **ıgnoremissingserviceendpoint** bayrağını kullanabilirsiniz.

**Ignoremissingserviceendpoint** BAYRAĞıNı Azure CLI veya portalını kullanarak ayarlayabilirsiniz.

## <a name="related-articles"></a>İlgili makaleler
- [Azure sanal ağları][vm-virtual-network-overview]
- [Azure sanal ağ hizmet uç noktaları][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Sonraki adımlar
VNet kuralları oluşturma hakkında makaleler için bkz.:
- [Azure portal kullanarak PostgreSQL için Azure veritabanı sanal ağ kuralları oluşturun ve yönetin](howto-manage-vnet-using-portal.md)
- [Azure CLı kullanarak PostgreSQL için Azure veritabanı sanal ağ kuralları oluşturma ve yönetme](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md
