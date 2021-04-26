---
title: Azure SQL veritabanı 'ndaki veritabanları için sanal ağ uç noktaları ve kuralları
description: Bir alt ağı bir sanal ağ hizmeti uç noktası olarak işaretleyin. Ardından, uç noktayı veritabanınıza yönelik ACL 'ye bir sanal ağ kuralı olarak ekleyin. Veritabanınız daha sonra tüm sanal makinelerden ve alt ağdaki diğer düğümlerden gelen iletişimi kabul eder.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784434"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Azure SQL Veritabanı'nda sunucular için sanal ağ hizmet uç noktalarını ve kuralları kullanma

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Sanal ağ kuralları* Azure [SQL veritabanı](sql-database-paas-overview.md) 'nda veritabanlarınızı ve elastik havuzlarınızın ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'teki ADANMıŞ SQL Havuzu (eski adıyla SQL DW) veritabanları için sanal ağlardaki belirli alt ağlardan gönderilen iletişimleri kabul edip etmediğini denetleyen bir güvenlik duvarı güvenlik özelliğidir. Bu makalede, sanal ağ kurallarının neden bazen SQL veritabanı ve Azure SYNAPSE Analytics 'te veritabanınıza yönelik iletişime güvenli bir şekilde izin vermek için en iyi seçenektir.

> [!NOTE]
> Bu makale hem SQL veritabanı hem de Azure SYNAPSE Analytics için geçerlidir. Kolaylık sağlaması için, *VERITABANı* SQL veritabanı ve Azure SYNAPSE Analytics 'te her iki veritabanına da başvurur. Benzer şekilde, *sunucu* başvuruları SQL veritabanı ve Azure SYNAPSE Analytics 'i BARıNDıRAN [mantıksal SQL Server](logical-servers.md) 'a başvurur.

Bir sanal ağ kuralı oluşturmak için öncelikle kuralın başvurması için bir [sanal ağ hizmet uç noktası][vm-virtual-network-service-endpoints-overview-649d] olması gerekir.

## <a name="create-a-virtual-network-rule"></a>Sanal ağ kuralı oluşturma

Yalnızca bir sanal ağ kuralı oluşturmak istiyorsanız, [Bu makalenin ilerleyen kısımlarında yer](#anchor-how-to-by-using-firewall-portal-59j)alarak adımları ve açıklamayı atlayabilirsiniz.

## <a name="details-about-virtual-network-rules"></a>Sanal ağ kuralları hakkında ayrıntılar

Bu bölümde, sanal ağ kurallarıyla ilgili çeşitli ayrıntılar açıklanmaktadır.

### <a name="only-one-geographic-region"></a>Yalnızca bir coğrafi bölge

Her sanal ağ hizmeti uç noktası yalnızca bir Azure bölgesi için geçerlidir. Uç nokta diğer bölgelerin alt ağdan iletişim kabul etmesine izin vermez.

Herhangi bir sanal ağ kuralı, temeldeki uç noktasının geçerli olduğu bölge ile sınırlıdır.

### <a name="server-level-not-database-level"></a>Sunucu düzeyi, veritabanı düzeyi değil

Her bir sanal ağ kuralı yalnızca sunucudaki belirli bir veritabanına değil, tüm sunucunuza uygulanır. Diğer bir deyişle, sanal ağ kuralları veritabanı düzeyinde değil, sunucu düzeyinde uygulanır.

Bunun aksine, IP kuralları her iki düzeyde de uygulanabilir.

### <a name="security-administration-roles"></a>Güvenlik yönetimi rolleri

Sanal ağ hizmet uç noktalarının yönetiminde güvenlik rollerinin bir ayrımı vardır. Aşağıdaki rollerden her biri için eylem gereklidir:

- **Ağ Yöneticisi ([ağ katılımcısı](../../role-based-access-control/built-in-roles.md#network-contributor) rolü):** &nbsp; Uç noktasını açın.
- **Veritabanı Yöneticisi ([SQL Server katkıda bulunan](../../role-based-access-control/built-in-roles.md#sql-server-contributor) rolü):** &nbsp; Verilen alt ağı sunucuya eklemek için erişim denetim listesini (ACL) güncelleştirin.

#### <a name="azure-rbac-alternative"></a>Azure RBAC alternatifi

Ağ yöneticisinin ve veritabanı yöneticisinin rollerinin sanal ağ kurallarını yönetmek için gerekenden daha fazla özelliği vardır. Yalnızca kendi yeteneklerinin bir alt kümesi gereklidir.

Azure 'da [rol tabanlı erişim denetimi (RBAC)][rbac-what-is-813s] kullanarak yalnızca gerekli özellik alt kümesini içeren tek bir özel rol oluşturabilirsiniz. Özel rol, ağ yöneticisi ya da veritabanı Yöneticisi dahil olmak yerine kullanılabilir. Özel bir role Kullanıcı eklerseniz, kullanıcıyı diğer iki ana yönetici rolüne ekleyerek güvenlik açıkağınızın yüzey alanı düşüktür.

> [!NOTE]
> Bazı durumlarda, SQL veritabanı ve sanal ağ alt ağı 'ndaki veritabanı farklı aboneliklerde bulunur. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
>
> - Her iki abonelik da aynı Azure Active Directory (Azure AD) kiracısında olmalıdır.
> - Kullanıcı, hizmet uç noktalarını etkinleştirme ve verilen sunucuya bir sanal ağ alt ağı ekleme gibi işlemleri başlatmak için gerekli izinlere sahiptir.
> - Her iki aboneliğin de Microsoft. SQL sağlayıcısı kayıtlı olmalıdır.

## <a name="limitations"></a>Sınırlamalar

SQL veritabanı için, sanal ağ kuralları özelliği aşağıdaki sınırlamalara sahiptir:

- SQL veritabanında veritabanınızın güvenlik duvarında, her bir sanal ağ kuralı bir alt ağa başvurur. Bu başvurulmuş alt ağların tümü, veritabanını barındıran aynı coğrafi bölgede barındırılmalıdır.
- Her bir sunucu, herhangi bir sanal ağ için en fazla 128 ACL girişine sahip olabilir.
- Sanal ağ kuralları yalnızca Azure Resource Manager sanal ağlar için geçerlidir ve [klasik dağıtım modeli][arm-deployment-model-568f] ağlarına uygulanmaz.
- Sanal ağ hizmet uç noktalarını SQL veritabanı 'na açmak, MySQL için Azure veritabanı ve PostgreSQL için Azure veritabanı uç noktalarına da olanak sağlar. Uç noktalar **Açık** olarak ayarlandığında, uç noktalarından MySQL Için Azure veritabanınıza veya PostgreSQL Için Azure veritabanı örneklerine bağlanma girişimleri başarısız olabilir.
  - Temel nedenle, MySQL için Azure veritabanı ve PostgreSQL için Azure veritabanı muhtemelen bir sanal ağ kuralı yapılandırmamış. MySQL için Azure veritabanı ve PostgreSQL için Azure veritabanı için bir sanal ağ kuralı yapılandırmanız gerekir ve bağlantı başarılı olur.
  - Zaten özel uç noktalarla yapılandırılmış bir SQL mantıksal sunucusunda sanal ağ güvenlik duvarı kuralları **tanımlamak için,** **ortak ağ erişimini reddet** olarak ayarlayın.
- Güvenlik duvarında, IP adresi aralıkları aşağıdaki ağ öğelerine uygulanır, ancak sanal ağ kuralları şunları içermez:
  - [Siteden siteye (S2S) sanal özel ağ (VPN)][vpn-gateway-indexmd-608y]
  - [Azure ExpressRoute](../../expressroute/index.yml) aracılığıyla şirket içi

### <a name="considerations-when-you-use-service-endpoints"></a>Hizmet uç noktalarını kullanırken dikkat edilecek noktalar

SQL veritabanı için hizmet uç noktaları kullandığınızda, aşağıdaki noktaları gözden geçirin:

- **Azure SQL veritabanı genel IP 'Leri için çıkış gereklidir.** Bağlantıya izin vermek için ağ güvenlik grupları (NSG 'Ler) SQL veritabanı IP 'lerine açılmalıdır. Bunu SQL veritabanı için NSG [hizmet etiketlerini](../../virtual-network/network-security-groups-overview.md#service-tags) kullanarak yapabilirsiniz.

### <a name="expressroute"></a>ExpressRoute

Şirket içindeki [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 'u genel eşleme veya Microsoft eşlemesi için kullanırsanız, kullanılan NAT IP adreslerini belirlemeniz gerekir. Ortak eşleme için, her bir ExpressRoute varsayılan olarak bağlantı hattında trafik Microsoft Azure omurga ağına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanılır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri ya da hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). ExpressRoute genel ve Microsoft eşlemesi için NAT hakkında daha fazla bilgi edinmek için bkz. [Azure genel eşleme Için NAT gereksinimleri](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Bağlantı hattınızdan SQL veritabanı 'na iletişim sağlamak için NAT 'nizin genel IP adresleri için IP ağ kuralları oluşturmanız gerekir.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Sanal ağ hizmet uç noktalarını Azure depolama ile kullanmanın etkileri

Azure Depolama da, Azure Depolama hesabınızın bağlantılarını sınırlandırmanıza imkan tanıyan aynı özelliğe sahiptir. Bu özelliği SQL veritabanının kullandığı bir Azure depolama hesabıyla kullanmayı tercih ederseniz, sorunlarla birlikte çalıştırabilirsiniz. Bundan sonra SQL Database ve Azure SYNAPSE Analytics özelliklerinin listesi ve tartışılması bir sonraki şeydir.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure SYNAPSE Analytics PolyBase ve COPY deyimleri

PolyBase ve COPY deyimleri, yüksek aktarım hızı verileri alımı için Azure depolama hesaplarından verileri Azure SYNAPSE Analytics 'e yüklemek için yaygın olarak kullanılır. Limitlerden veri yüklediğiniz Azure depolama hesabı yalnızca bir sanal ağ alt ağı kümesiyle erişirse, PolyBase kullandığınızda bağlantı ve depolama hesabına kopyalama ekstresi kesilir. Bir sanal ağla güvenli hale getirilmiş Azure depolama 'ya bağlanarak Azure SYNAPSE Analytics ile kopyalama ve PolyBase kullanarak içeri ve dışarı aktarma senaryolarına olanak tanımak için, bu bölümdeki adımları izleyin.

#### <a name="prerequisites"></a>Önkoşullar

- [Bu kılavuzu](/powershell/azure/install-az-ps)kullanarak Azure PowerShell 'yi.
- Genel amaçlı bir v1 veya Azure Blob depolama hesabınız varsa, [genel amaçlı v2 depolama hesabına yükseltme](../../storage/common/storage-account-upgrade.md)bölümündeki adımları izleyerek öncelikle genel amaçlı v2 'ye yükseltmeniz gerekir.
- Azure depolama hesabı **güvenlik duvarları ve sanal ağlar** ayarları menüsünde, **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver** seçeneğinin etkinleştirilmiş olması gerekir. Bu yapılandırmayı etkinleştirmek, PolyBase ve COPY deyimlerinin, ağ trafiğinin Azure omurgasında kaldığı güçlü kimlik doğrulaması kullanarak depolama hesabına bağlanmasına izin verir. Daha fazla bilgi için [bu kılavuza](../../storage/common/storage-network-security.md#exceptions)bakın.

> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir. Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Adımlar

1. Tek başına adanmış bir SQL havuzunuz varsa, PowerShell kullanarak SQL Server 'ı Azure AD 'ye kaydedin:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Bu adım, bir Azure SYNAPSE Analytics çalışma alanı içindeki adanmış SQL havuzları için gerekli değildir.

1. Bir Azure SYNAPSE Analytics çalışma alanınız varsa, çalışma alanınızın sistem tarafından yönetilen kimliğini kaydedin:

   1. Azure portal Azure SYNAPSE Analytics çalışma alanınıza gidin.
   2. **Yönetilen kimlikler** bölmesine gidin.
   3. **Ardışık düzen Izin ver** seçeneğinin etkinleştirildiğinden emin olun.
   
1. [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md)bölümündeki adımları izleyerek **genel amaçlı v2 depolama hesabı** oluşturun.

   > [!NOTE]
   >
   > - Genel amaçlı bir v1 veya blob depolama hesabınız varsa, önce [genel amaçlı v2 depolama hesabına yükseltme](../../storage/common/storage-account-upgrade.md)içindeki adımları izleyerek *v2 'ye yükseltmeniz* gerekir.
   > - Azure Data Lake Storage 2. ile ilgili bilinen sorunlar için bkz. [Azure Data Lake Storage 2. ile Ilgili bilinen sorunlar](../../storage/blobs/data-lake-storage-known-issues.md).

1. Depolama hesabınız altında **Access Control (IAM)** bölümüne gidin ve **rol ataması Ekle**' yi seçin. Azure AD 'ye kaydettiğiniz adanmış SQL havuzunuzu barındıran sunucu veya çalışma alanına **Depolama Blobu verileri katkıda bulunan** Azure rolünü atayın.

   > [!NOTE]
   > Yalnızca depolama hesabında sahibi ayrıcalığına sahip Üyeler bu adımı gerçekleştirebilir. Çeşitli Azure yerleşik rolleri için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).
  
1. Azure depolama hesabına PolyBase bağlantısını etkinleştirmek için:

   1. Daha önce oluşturmadıysanız bir veritabanı [ana anahtarı](/sql/t-sql/statements/create-master-key-transact-sql) oluşturun.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. **Identity = ' yönetilen hizmet kimliği '** olan veritabanı kapsamlı kimlik bilgileri oluşturun.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Bu mekanizma, kapsamakta olan [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md) kullandığından, Azure depolama ERIŞIM anahtarıyla gizli anahtar belirtmeniz gerekmez.
       > - PolyBase bağlantısının bir sanal ağla korunan bir Azure depolama hesabıyla çalışması için KIMLIK adı **' yönetilen hizmet kimliği '** olmalıdır.

   1. `abfss://`PolyBase kullanarak genel amaçlı v2 depolama hesabınıza bağlanma düzeniyle bir dış veri kaynağı oluşturun.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Zaten genel amaçlı bir v1 veya blob depolama hesabıyla ilişkili dış tablolar varsa, önce bu dış tabloları bırakmalısınız. Ardından karşılık gelen dış veri kaynağını bırakın. Ardından, `abfss://` daha önce gösterildiği gibi, genel amaçlı v2 depolama hesabına bağlanan şemayla bir dış veri kaynağı oluşturun. Ardından, bu yeni dış veri kaynağını kullanarak tüm dış tabloları yeniden oluşturun. Her türlü dış tablo için oluşturma komut dosyaları oluşturmak üzere [komut dosyaları oluşturma ve Yayımlama Sihirbazı 'nı](/sql/ssms/scripting/generate-and-publish-scripts-wizard) kullanabilirsiniz.
       > - Düzen hakkında daha fazla bilgi için `abfss://` bkz. [Azure Data Lake Storage 2. URI 'si kullanma](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Dış VERI kaynağı oluşturma hakkında daha fazla bilgi için [bu kılavuza](/sql/t-sql/statements/create-external-data-source-transact-sql)bakın.

   1. [Dış tablolar](/sql/t-sql/statements/create-external-table-transact-sql)kullanarak normal şekilde sorgulama.

### <a name="sql-database-blob-auditing"></a>SQL veritabanı blobu denetimi

Blob denetimi, Denetim günlüklerini kendi depolama hesabınıza iter. Bu depolama hesabı, sanal ağ hizmeti uç noktaları özelliğini kullanıyorsa, SQL veritabanından depolama hesabına olan bağlantı kesilir.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Sunucunuza bir sanal ağ güvenlik duvarı kuralı ekleme

Uzun bir süre önce, bu özellik arttırmadan önce, güvenlik duvarında canlı bir sanal ağ kuralı uygulayabilmeniz için önce sanal ağ hizmet uç noktalarını açmanız gerekiyordu. Belirli bir sanal ağ alt ağı ile SQL veritabanı 'ndaki bir veritabanıyla ilgili uç noktalar. 2018 Ocak itibariyle **ıgnoremissingvnetserviceendpoint** bayrağını ayarlayarak bu gereksinimi atlayabilirsiniz. Artık sanal ağ hizmeti uç noktalarını açmadan sunucunuza bir sanal ağ güvenlik duvarı kuralı ekleyebilirsiniz.

Yalnızca bir güvenlik duvarı kuralı ayarlamak sunucunun güvenliğinin sağlanmasına yardımcı olmaz. Ayrıca, güvenliğin etkinleşmesi için sanal ağ hizmet uç noktalarını açmanız gerekir. Hizmet uç noktalarını açtığınızda, sanal ağ alt ağınız geçiş kapalı ' dan açık ' a kadar tamamlanana kadar kesinti yaşar. Bu süre kapalı kalma süresi, büyük sanal ağların bağlamında özellikle doğrudur. Geçiş sırasında kesinti süresini azaltmak veya ortadan kaldırmak için **ıgnoremissingvnetserviceendpoint** bayrağını kullanabilirsiniz.

**Ignoremissingvnetserviceendpoint** bayrağını PowerShell kullanarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [PowerShell, SQL veritabanı için sanal ağ hizmeti uç noktası ve kuralı oluşturma][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Hatalar 40914 ve 40615

Bağlantı hatası 40914, Azure portal **güvenlik duvarı** bölmesinde belirtilen *sanal ağ kuralları* ile ilgilidir. Hata 40615 benzerdir, ancak güvenlik duvarındaki *IP adresi kurallarıyla* ilgilidir.

### <a name="error-40914"></a>Hata 40914

**İleti metni:** "Oturum açma tarafından istenen '*[sunucu-adı]*' sunucusu açılamıyor. İstemcinin sunucuya erişmesine izin verilmiyor. "

**Hata açıklaması:** İstemci, sanal ağ sunucusu uç noktaları olan bir alt ağda bulunur. Ancak sunucuda, veritabanıyla iletişim kurmak için gereken alt ağa izin veren bir sanal ağ kuralı yok.

**Hata çözümleme:** Azure portal **güvenlik duvarı** bölmesinde, alt ağa yönelik [bir sanal ağ kuralı eklemek](#anchor-how-to-by-using-firewall-portal-59j) için sanal ağ kuralları denetimini kullanın.

### <a name="error-40615"></a>Hata 40615

**İleti metni:** " {0} Oturum açma tarafından istenen ' ' sunucusu açılamıyor. ' ' IP adresine sahip istemcinin {1} sunucuya erişmesine izin verilmiyor. "

**Hata açıklaması:** İstemci, sunucuya bağlanma yetkisine sahip olmayan bir IP adresinden bağlanmaya çalışıyor. Sunucu güvenlik duvarının, bir istemcinin verilen IP adresinden veritabanıyla iletişim kurmasına izin veren bir IP adresi kuralı yok.

**Hata çözümleme:** İstemcinin IP adresini bir IP kuralı olarak girin. Bu adımı yapmak için Azure portal **güvenlik duvarı** bölmesini kullanın.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Bir sanal ağ kuralı oluşturmak için portalı kullanma

Bu bölümde, SQL veritabanı 'nda veritabanınızda bir *sanal ağ kuralı* oluşturmak için [Azure Portal][http-azure-portal-link-ref-477t] nasıl kullanabileceğiniz gösterilmektedir. Kural, veritabanınıza, *sanal ağ hizmeti uç noktası* olarak etiketlenmiş belirli bir alt ağdan gelen iletişimi kabul etmesini söyler.

> [!NOTE]
> Sunucunuzun sanal ağ güvenlik duvarı kurallarına bir hizmet uç noktası eklemek istiyorsanız, önce alt ağ için hizmet uç noktalarının açık olduğundan emin olun.
>
> Alt ağ için hizmet uç noktaları açık değilse, Portal bunları etkinleştirmenizi ister. Kuralı eklediğiniz bölmedeki aynı bölmede **Etkinleştir** düğmesini seçin.

## <a name="powershell-alternative"></a>PowerShell alternatifi

Bir betik, **New-AzSqlServerVirtualNetworkRule** veya [az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)PowerShell cmdlet 'ini kullanarak sanal ağ kuralları da oluşturabilir. İlgileniyorsanız, [SQL veritabanı için sanal ağ hizmeti uç noktası ve kuralı oluşturmak üzere PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d]bölümüne bakın.

## <a name="rest-api-alternative"></a>REST API alternatifi

Dahili olarak, SQL sanal ağ eylemleri için PowerShell cmdlet 'leri REST API 'Leri çağırır. REST API 'Leri doğrudan çağırabilirsiniz.

- [Sanal ağ kuralları: Işlemler][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Önkoşullar

SQL veritabanı ile ilgili belirli bir sanal ağ hizmet uç noktası *türü adıyla* etiketlenmiş bir alt ağa sahip olmanız gerekir.

- İlgili uç nokta türü adı **Microsoft. SQL**' dir.
- Alt ağınız tür adıyla etiketlenmemişse, bkz. [alt ağınızın bir uç nokta olduğunu doğrulayın][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure portal adımları

1. [Azure portalında][http-azure-portal-link-ref-477t] oturum açın.

1. **SQL sunucuları**' nı arayıp seçin ve ardından sunucunuzu seçin. **Güvenlik altında güvenlik** **duvarları ve sanal ağlar**' ı seçin.

1. **Azure hizmetlerine erişime Izin ver** ' i **kapalı** olarak ayarlayın.

    > [!IMPORTANT]
    > Denetimi **Açık** olarak bırakırsanız, sunucunuz Azure sınırının içindeki herhangi bir alt ağdan gelen iletişimi kabul eder. Bu, Azure veri merkezleri için tanımlanan aralıklar dahilinde tanınan IP adreslerinden birinden kaynaklanan iletişimdir. Denetim kümesini **Açık** olarak bırakmak, bir güvenlik noktasından aşırı erişim sağlayabilir. SQL Database 'in sanal ağ kuralları özelliği ile koordinasyon olarak Microsoft Azure Sanal Ağ Hizmeti uç noktası özelliği, güvenlik yüzeyi alanınızı azaltabilir.

1. **Sanal ağlar** bölümünde **+ Varolanı Ekle** ' yi seçin.

    ![Seçme + varolanı Ekle (bir SQL kuralı olarak alt ağ uç noktası) gösteren ekran görüntüsü.][image-portal-firewall-vnet-add-existing-10-png]

1. Yeni **Oluştur/Güncelleştir** bölmesinde, kutuları Azure kaynaklarınızın adlarıyla girin.

    > [!TIP]
    > Alt ağınız için doğru adres önekini dahil etmeniz gerekir. **Adres ön eki** değerini portalda bulabilirsiniz. Tüm **kaynaklar** &gt; **Tüm** &gt; **sanal ağlar**' a gidin. Filtre, sanal ağlarınızı görüntüler. Sanal ağınızı seçin ve ardından **alt ağlar**' ı seçin. **Adres aralığı** sütunu, ihtiyacınız olan adres ön ekine sahiptir.

    ![Yeni kural için doldurma kutusunu gösteren ekran görüntüsü.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Bölmenin alt kısmındaki **Tamam** düğmesini seçin.

1. **Güvenlik duvarı** bölmesinde ortaya çıkan sanal ağ kuralına bakın.

    ![Güvenlik Duvarı bölmesindeki yeni kuralı gösteren ekran görüntüsü.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Aşağıdaki durumlar veya durumlar kurallar için geçerlidir:
>
> - **Ready**: başlatıldığı işlemin başarılı olduğunu gösterir.
> - **Başarısız**: başlatıldığı işlemin başarısız olduğunu gösterir.
> - **Silindi**: yalnızca silme işlemi için geçerlidir ve kuralın silindiğini ve artık geçerli olmadığını gösterir.
> - **Sürüyor**: işlemin devam ettiğini gösterir. İşlem bu durumdayken eski kural uygulanır.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>İlgili makaleler:

- [Azure sanal ağ hizmet uç noktaları][vm-virtual-network-service-endpoints-overview-649d]
- [Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell kullanarak bir sanal ağ hizmeti uç noktası ve sonra SQL veritabanı için bir sanal ağ kuralı oluşturun][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Sanal ağ kuralları:][rest-api-virtual-network-rules-operations-862r] REST API 'Ler ile işlemler

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
