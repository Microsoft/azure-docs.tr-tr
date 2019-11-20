---
title: Tek ve havuza alınmış veritabanları için VNet uç noktaları ve kuralları
description: Bir alt ağı bir sanal ağ hizmeti uç noktası olarak işaretleyin. Ardından, Azure SQL veritabanınızın ACL 'ye bir sanal ağ kuralı olarak uç nokta. SQL veritabanı bundan sonra tüm sanal makinelerden ve alt ağdaki diğer düğümlerden gelen iletişimi kabul eder.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: faf3573178693ec806000eb89ce7a975955d61b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084127"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Veritabanı sunucuları için sanal ağ hizmet uç noktalarını ve kurallarını kullanma

*Sanal ağ kuralları* , tek veritabanlarınıza yönelik veritabanı sunucusunun ve Azure [SQL veritabanı](sql-database-technical-overview.md) 'ndaki elastik havuzunuzun ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'ndaki veritabanlarınızın iletişim kabul edip etmediğini denetleyen bir güvenlik duvarı güvenlik özelliğidir Bu, sanal ağlardaki belirli alt ağlardan gönderilir. Bu makalede, Azure SQL veritabanınıza ve SQL veri ambarınıza yönelik iletişimin güvenli bir şekilde yapılmasına olanak tanımak için sanal ağ kuralı özelliğinin neden bazen en iyi seçenektir.

> [!IMPORTANT]
> Bu makale Azure SQL Server ve Azure SQL Server 'da oluşturulan SQL veritabanı ve SQL veri ambarı veritabanları için geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır. Bu makale, kendisiyle ilişkili bir hizmet uç noktası olmadığından Azure SQL veritabanı 'nda **yönetilen bir örnek** dağıtımı *için uygulanmıyor.*

Bir sanal ağ kuralı oluşturmak için öncelikle kuralın başvurması için bir [sanal ağ hizmet uç noktası][vm-virtual-network-service-endpoints-overview-649d] olması gerekir.

## <a name="how-to-create-a-virtual-network-rule"></a>Sanal ağ kuralı oluşturma

Yalnızca bir sanal ağ kuralı oluşturursanız, [Bu makalenin ilerleyen kısımlarında yer](#anchor-how-to-by-using-firewall-portal-59j)alarak adım adım ve açıklama ' ya geçebilirsiniz.

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Sanal ağ kuralları hakkında ayrıntılar

Bu bölümde, sanal ağ kurallarıyla ilgili çeşitli ayrıntılar açıklanmaktadır.

### <a name="only-one-geographic-region"></a>Yalnızca bir coğrafi bölge

Her sanal ağ hizmeti uç noktası yalnızca bir Azure bölgesi için geçerlidir. Uç nokta diğer bölgelerin alt ağdan iletişim kabul etmesine izin vermez.

Herhangi bir sanal ağ kuralı, temeldeki uç noktasının geçerli olduğu bölge ile sınırlıdır.

### <a name="server-level-not-database-level"></a>Sunucu düzeyi, veritabanı düzeyi değil

Her bir sanal ağ kuralı yalnızca sunucudaki belirli bir veritabanına değil, tüm Azure SQL veritabanı sunucunuz için geçerlidir. Diğer bir deyişle, sanal ağ kuralı, veritabanı düzeyinde değil, sunucu düzeyinde geçerlidir.

- Bunun aksine, IP kuralları her iki düzeyde de uygulanabilir.

### <a name="security-administration-roles"></a>Güvenlik yönetimi rolleri

Sanal ağ hizmet uç noktalarının yönetiminde güvenlik rollerinin bir ayrımı vardır. Aşağıdaki rollerden her biri için eylem gereklidir:

- **Ağ Yöneticisi:** uç noktayı açmak &nbsp;.
- **Veritabanı Yöneticisi:** &nbsp; belirtilen alt ağı SQL veritabanı sunucusuna eklemek için erişim denetim LISTESINI (ACL) güncelleştirin.

*RBAC alternatifi:*

Ağ yöneticisinin ve veritabanı yöneticisinin rollerinin sanal ağ kurallarını yönetmek için gerekenden daha fazla özelliği vardır. Yalnızca kendi yeteneklerinin bir alt kümesi gereklidir.

Azure 'da [rol tabanlı erişim denetimi (RBAC)][rbac-what-is-813s] kullanarak yalnızca gerekli özellik alt kümesini içeren tek bir özel rol oluşturabilirsiniz. Özel rol, ağ yöneticisi ya da veritabanı Yöneticisi dahil olmak yerine kullanılabilir. Bir kullanıcıyı özel bir role eklerseniz, diğer iki ana yönetici rolüne kullanıcı ekleyerek güvenlik açıkağınızın yüzey alanı düşüktür.

> [!NOTE]
> Bazı durumlarda, Azure SQL veritabanı ve VNet-subnet farklı aboneliklerde bulunur. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelik da aynı Azure Active Directory kiracısında olmalıdır.
> - Kullanıcı, hizmet uç noktalarını etkinleştirme ve verilen sunucuya VNet-subnet ekleme gibi işlemleri başlatmak için gerekli izinlere sahiptir.
> - Her iki aboneliğin de Microsoft. SQL sağlayıcısı kayıtlı olmalıdır.

## <a name="limitations"></a>Sınırlamalar

Azure SQL veritabanı için sanal ağ kuralları özelliği aşağıdaki sınırlamalara sahiptir:

- SQL veritabanınızın güvenlik duvarında, her bir sanal ağ kuralı bir alt ağa başvurur. Bu başvurulan tüm alt ağlar, SQL veritabanını barındıran aynı coğrafi bölgede barındırılmalıdır.

- Her Azure SQL veritabanı sunucusu, belirli bir sanal ağ için en fazla 128 ACL girişine sahip olabilir.

- Sanal ağ kuralları yalnızca Azure Resource Manager sanal ağlar için geçerlidir; [klasik dağıtım modeli][arm-deployment-model-568f] ağlarına değil.

- Sanal ağ hizmet uç noktalarını Azure SQL veritabanı 'na açmak, MySQL ve PostgreSQL Azure hizmetleri için uç noktaların de kullanılmasına olanak sağlar. Bununla birlikte uç noktalar ile, uç noktalarından MySQL veya PostgreSQL örneklerine bağlanma girişimleri başarısız olabilir.
  - Temel neden MySQL ve PostgreSQL 'in büyük olasılıkla yapılandırılmış bir sanal ağ kuralı yoktur. MySQL ve PostgreSQL için Azure veritabanı için bir sanal ağ kuralı yapılandırmanız gerekir ve bağlantı başarılı olur.

- Güvenlik duvarında, IP adresi aralıkları aşağıdaki ağ öğelerine uygulanır, ancak sanal ağ kuralları şunları içermez:
  - [Siteden siteye (S2S) sanal özel ağ (VPN)][vpn-gateway-indexmd-608y]
  - [ExpressRoute][expressroute-indexmd-744v] aracılığıyla şirket içi

### <a name="considerations-when-using-service-endpoints"></a>Hizmet uç noktalarını kullanırken dikkat edilecek noktalar

Azure SQL veritabanı için hizmet uç noktaları kullanırken aşağıdaki noktaları gözden geçirin:

- **Azure SQL veritabanı genel IP 'Lerine giden**: ağ güvenlik grupları (NSG 'ler), bağlantıya izin vermek IÇIN Azure SQL veritabanı 'na açık olmalıdır. Bunu Azure SQL veritabanı için NSG [hizmet etiketlerini](../virtual-network/security-overview.md#service-tags) kullanarak yapabilirsiniz.

### <a name="expressroute"></a>ExpressRoute

Şirket içinde genel eşleme veya Microsoft eşlemesi için [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanıyorsanız, kullanılan NAT IP adreslerini belirlemeniz gerekir. Ortak eşleme için, her bir ExpressRoute varsayılan olarak bağlantı hattında trafik Microsoft Azure omurga ağına girdiğinde Azure hizmet trafiğine uygulanan iki NAT IP adresi kullanılır. Microsoft eşlemesi için, kullanılan NAT IP adresleri müşteri tarafından sağlanır veya hizmet sağlayıcısı tarafından sağlanır. Hizmet kaynaklarınıza erişime izin vermek için, bu genel IP adreslerine kaynak IP güvenlik duvarı ayarında izin vermeniz gerekir. Ortak eşleme ExpressRoute bağlantı hattı IP adreslerinizi bulmak için Azure portalında [ExpressRoute ile bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute genel ve Microsoft eşlemesi için NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering) hakkında daha fazla bilgi edinin.
  
Bağlantı hattınızdan Azure SQL veritabanı 'na yönelik iletişime izin vermek için NAT 'nizin genel IP adresleri için IP ağ kuralları oluşturmanız gerekir.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->



## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>VNet hizmet uç noktalarını Azure depolama ile kullanmanın etkileri

Azure depolama, Azure depolama hesabınızla olan bağlantıyı sınırlandırmanıza olanak tanıyan özelliği de uygulamıştır. Bu özelliği Azure SQL Server tarafından kullanılmakta olan bir Azure depolama hesabıyla kullanmayı tercih ederseniz, sorunlar ' de çalıştırabilirsiniz. Bundan etkilenen Azure SQL veritabanı ve Azure SQL veri ambarı özelliklerinin bir listesi ve tartışılması daha vardır.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL veri ambarı PolyBase

PolyBase, Azure depolama hesaplarından Azure SQL veri ambarı 'na veri yüklemek için yaygın olarak kullanılır. Verileri yüklediğiniz Azure depolama hesabı, erişimi yalnızca bir VNet-alt ağ kümesine sınırlandırırsanız, PolyBase 'den hesaba bağlantı kesilir. VNet ile güvenli hale getirilmiş Azure depolama 'ya bağlanan Azure SQL veri ambarı ile hem PolyBase içeri ve dışarı aktarma senaryolarını etkinleştirmek için aşağıda belirtilen adımları izleyin:

#### <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

1.  Bu [Kılavuzu](https://docs.microsoft.com/powershell/azure/install-az-ps)kullanarak Azure PowerShell 'i yükler.
2.  Genel amaçlı v1 veya blob depolama hesabınız varsa, önce bu [Kılavuzu](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)kullanarak genel amaçlı v2 'ye yükseltmeniz gerekir.
3.  Azure depolama hesabı **güvenlik duvarları ve sanal ağlar** ayarları menüsünde **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin vermeniz** gerekir. Daha fazla bilgi için bu [kılavuza](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) bakın.
 
#### <a name="steps"></a>Adımlar
1. PowerShell 'de Azure SQL veri ambarı örneğinizi Azure Active Directory (AAD) barındırarak **azure SQL Server kaydettirin** :

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```
    
   1. Bu [Kılavuzu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)kullanarak **genel amaçlı v2 depolama hesabı** oluşturun.

   > [!NOTE]
   > - Genel amaçlı bir v1 veya blob depolama hesabınız varsa, önce bu [Kılavuzu](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)kullanarak **v2 'ye yükseltmeniz** gerekir.
   > - Azure Data Lake Storage 2. ile ilgili bilinen sorunlar için lütfen bu [kılavuza](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)bakın.
    
1. Depolama hesabınız altında **Access Control (IAM)** bölümüne gidin ve **rol ataması Ekle**' ye tıklayın. Azure SQL veri Ambarınızı, 1. adımda Azure Active Directory (AAD) ile kaydolduysanız Azure SQL Server barındırarak **Depolama Blobu veri katılımcısı** RBAC rolünü Azure 'a atayın.

   > [!NOTE] 
   > Yalnızca sahibi ayrıcalığına sahip Üyeler bu adımı gerçekleştirebilir. Azure kaynakları için çeşitli yerleşik roller için bu [kılavuza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)bakın.
  
1. **Azure depolama hesabına PolyBase bağlantısı:**

   1. Daha önce oluşturmadıysanız bir veritabanı **[ana anahtarı](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** oluşturun:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. **Kimlik = ' yönetilen hizmet kimliği '** olan veritabanı kapsamlı kimlik bilgileri oluşturun:

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Bu mekanizma, kapsamakta olan [yönetilen kimliği](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) kullandığından Azure depolama erişim anahtarı ile gizli dizi belirtilmesi gerekmez.
       > - PolyBase bağlantısının VNet ile güvenli hale getirilmiş Azure Storage hesabıyla çalışması için KIMLIK adı **' yönetilen hizmet kimliği '** olmalıdır.    
    
   1. PolyBase kullanarak genel amaçlı v2 depolama hesabınıza bağlanmak için abfss://düzeniyle dış veri kaynağı oluşturun:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Genel amaçlı v1 veya blob depolama hesabıyla ilişkili dış tablolar zaten varsa, önce bu dış tabloları bırakmalısınız, ardından karşılık gelen dış veri kaynağını bırakmalısınız. Ardından, yukarıdaki gibi genel amaçlı v2 depolama hesabına bağlanan abfss://düzenine sahip dış veri kaynağı oluşturun ve bu yeni dış veri kaynağını kullanarak tüm dış tabloları yeniden oluşturun. Her türlü dış tablo için oluşturma komut dosyaları oluşturmak için [komut dosyaları oluşturma ve Yayımlama Sihirbazı 'nı](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) kullanabilirsiniz.
       > - Abfss://şeması hakkında daha fazla bilgi için bu [kılavuza](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)bakın.
       > - Dış VERI kaynağı oluşturma hakkında daha fazla bilgi için bu [kılavuza](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)bakın.
        
   1. [Dış tablolar](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)kullanarak normal şekilde sorgulama.

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL veritabanı blobu denetimi

Blob denetimi, Denetim günlüklerini kendi depolama hesabınıza iter. Bu depolama hesabı VNet hizmet uç noktaları özelliğini kullanıyorsa, Azure SQL veritabanı 'ndan depolama hesabına olan bağlantı kesilir.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNet hizmet uç noktalarını açmadan sunucunuza VNet güvenlik duvarı kuralı ekleme

Uzun bir süre önce, bu özellik arttırmadan önce, güvenlik duvarında canlı VNet kuralı uygulamadan önce VNet hizmet uç noktalarını açmanız gerekiyordu. Belirli bir sanal ağ alt ağı ile bir Azure SQL veritabanına ilgili uç noktalar. Ancak artık 2018 Ocak itibariyle **ıgnoremissingvnetserviceendpoint** bayrağını ayarlayarak bu gereksinimi atlayabilirsiniz.

Yalnızca bir güvenlik duvarı kuralı ayarlamak sunucunun güvenliğinin sağlanmasına yardımcı olmaz. Ayrıca güvenliğin etkili olabilmesi için VNet hizmet uç noktalarını açmanız gerekir. Hizmet uç noktalarını açık olarak açtığınızda, VNet alt ağınız kapalı kalma süresini açık olarak tamamlanana kadar kesinti yaşar. Bu, büyük sanal ağlar bağlamında özellikle doğrudur. Geçiş sırasında kesinti süresini azaltmak veya ortadan kaldırmak için **ıgnoremissingvnetserviceendpoint** bayrağını kullanabilirsiniz.

**Ignoremissingvnetserviceendpoint** bayrağını PowerShell kullanarak ayarlayabilirsiniz. Ayrıntılar için bkz. [PowerShell, Azure SQL veritabanı Için sanal ağ hizmeti uç noktası ve kuralı oluşturma][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Hatalar 40914 ve 40615

Bağlantı hatası 40914, Azure portal güvenlik duvarı bölmesinde belirtilen *sanal ağ kuralları*ile ilgilidir. Hata 40615 benzerdir, ancak güvenlik duvarındaki *IP adresi kurallarıyla* ilgilidir.

### <a name="error-40914"></a>Hata 40914

*İleti metni:* Oturum açma tarafından istenen ' *[sunucu-adı]* ' sunucusu açılamıyor. İstemcinin sunucuya erişmesine izin verilmiyor.

*Hata açıklaması:* İstemci, sanal ağ sunucusu uç noktaları olan bir alt ağda bulunur. Ancak Azure SQL Veritabanı sunucusunda, alt ağa SQL Veritabanı ile iletişim kurma hakkı veren bir sanal ağ kuralı yok.

*Hata çözümleme:* Azure portal güvenlik duvarı bölmesinde, alt ağa yönelik [bir sanal ağ kuralı eklemek](#anchor-how-to-by-using-firewall-portal-59j) için sanal ağ kuralları denetimini kullanın.

### <a name="error-40615"></a>Hata 40615

*İleti metni:* Oturum açma tarafından istenen '{0}' sunucusu açılamıyor. '{1}' IP adresine sahip istemcinin sunucuya erişmesine izin verilmiyor.

*Hata açıklaması:* İstemci, Azure SQL veritabanı sunucusuna bağlanma yetkisine sahip olmayan bir IP adresinden bağlanmaya çalışıyor. Sunucu güvenlik duvarında, istemcinin belirtilen IP adresinden SQL Veritabanı ile iletişim kurmasına izin veren bir IP adresi kuralı yok.

*Hata çözümleme:* İstemcinin IP adresini bir IP kuralı olarak girin. Bu işlemi Azure portalındaki Güvenlik Duvarı bölmesini kullanarak yapabilirsiniz.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal, bir sanal ağ kuralı oluşturabilir

Bu bölümde, Azure SQL veritabanınızda bir *sanal ağ kuralı* oluşturmak için [Azure Portal][http-azure-portal-link-ref-477t] nasıl kullanabileceğiniz gösterilmektedir. Kural, SQL veritabanınıza, *sanal ağ hizmeti uç noktası*olarak etiketlenmiş belirli bir alt ağdan gelen iletişimi kabul etmesini söyler.

> [!NOTE]
> Azure SQL veritabanı sunucunuzun VNet güvenlik duvarı kurallarına bir hizmet uç noktası eklemek istiyorsanız, önce alt ağ için hizmet uç noktalarının açık olduğundan emin olun.
>
> Alt ağ için hizmet uç noktaları açık değilse, Portal bunları etkinleştirmenizi ister. Kuralı eklediğiniz dikey pencerede **Etkinleştir** düğmesine tıklayın.

## <a name="powershell-alternative"></a>PowerShell alternatifi

Bir PowerShell betiği, sanal ağ kuralları da oluşturabilir. **Yeni-AzSqlServerVirtualNetworkRule**önemli cmdlet 'i. İlgileniyorsanız, [Azure SQL veritabanı Için sanal ağ hizmeti uç noktası ve kuralı oluşturmak üzere PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d]bölümüne bakın.

## <a name="rest-api-alternative"></a>REST API alternatifi

Dahili olarak, SQL VNet eylemleri için PowerShell cmdlet 'leri REST API 'Leri çağırır. REST API 'Leri doğrudan çağırabilirsiniz.

- [Sanal ağ kuralları: Işlemler][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Ön koşullar

Azure SQL veritabanı ile ilgili belirli bir sanal ağ hizmet uç noktası *türü adıyla* etiketlenmiş bir alt ağa sahip olmanız gerekir.

- İlgili uç nokta türü adı **Microsoft. SQL**' dir.
- Alt ağınız tür adıyla etiketlenmemişse, bkz. [alt ağınızın bir uç nokta olduğunu doğrulayın][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure portal adımları

1. [Azure portalında][http-azure-portal-link-ref-477t] oturum açın.

2. Ardından portalda **SQL server** &gt; **güvenlik duvarı/sanal ağlar**'a gidin.

3. **Azure hizmetleri denetimine erişime Izin ver** denetimini kapalı olarak ayarlayın.

    > [!IMPORTANT]
    > Denetimi açık olarak bırakırsanız, Azure SQL veritabanı sunucunuz Azure sınırının içindeki herhangi bir alt ağdan gelen iletişimi kabul eder. Örneğin, Azure veri merkezleri için tanımlanan aralıklar dahilinde tanınan IP adreslerinden biridir. Denetim kümesini açık olarak bırakmak, bir güvenlik noktasından aşırı erişim sağlayabilir. SQL veritabanı 'nın sanal ağ kuralı özelliğiyle birlikte Microsoft Azure Sanal Ağ Hizmeti uç noktası özelliği, güvenlik yüzeyi alanınızı azaltabilir.

4. **Sanal ağlar** bölümünde **+ var olan denetimi Ekle** ' ye tıklayın.

    ![Mevcut Ekle ' ye tıklayın (bir SQL kuralı olarak alt ağ uç noktası).][image-portal-firewall-vnet-add-existing-10-png]

5. Yeni **Oluştur/Güncelleştir** bölmesinde, denetimleri Azure kaynaklarınızın adlarıyla girin.

    > [!TIP]
    > Alt ağınız için doğru **Adres önekini** dahil etmeniz gerekir. Değeri portalda bulabilirsiniz.
    > Tüm **kaynaklara** &gt; tüm **türler** &gt; **sanal ağlar**' a gidin. Filtre, sanal ağlarınızı görüntüler. Sanal ağınıza ve ardından **alt ağlar**' a tıklayın. **Adres aralığı** sütunu, Ihtiyacınız olan adres ön ekine sahiptir.

    ![Yeni kural için alanları doldur.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Bölmenin alt kısmındaki **Tamam** düğmesine tıklayın.

7. Güvenlik Duvarı bölmesinde ortaya çıkan sanal ağ kuralına bakın.

    ![Güvenlik Duvarı bölmesinde yeni kurala bakın.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Aşağıdaki durumlar veya durumlar kurallar için geçerlidir:
> - **Hazırlanıyor:** Başlatıldığı işlemin başarılı olduğunu gösterir.
> - **Başarısız oldu:** Başlatıldığı işlemin başarısız olduğunu gösterir.
> - **Silindi:** Yalnızca silme işlemi için geçerlidir ve kuralın silindiğini ve artık geçerli olmadığını gösterir.
> - **Sürüyor:** İşlemin devam ettiğini gösterir. İşlem bu durumdayken eski kural uygulanır.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>İlgili makaleler:

- [Azure sanal ağ hizmet uç noktaları][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL veritabanı sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları][sql-db-firewall-rules-config-715d]

Azure SQL veritabanı için sanal ağ kuralı özelliği, geç Eylül 2017 ' de kullanıma sunulmuştur.

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell kullanarak bir sanal ağ hizmeti uç noktası ve ardından Azure SQL veritabanı için bir sanal ağ kuralı oluşturun.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Sanal ağ kuralları:][rest-api-virtual-network-rules-operations-862r] REST API 'Ler ile işlemler

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
