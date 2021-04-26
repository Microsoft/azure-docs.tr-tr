---
title: Güvenlik konuları
description: Verilerinizin güvenliğini sağlamaya yardımcı olmak için Azure Data Factory veri taşıma hizmetlerinin kullandığı temel güvenlik altyapısını açıklar.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 1a99fbd3d3163808a364e8b26e770563a901dc18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371337"
---
# <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Azure Data Factory veri hareketine yönelik güvenlik konuları

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
>
> * [Sürüm 1](v1/data-factory-data-movement-security-considerations.md)
> * [Güncel sürüm](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, verilerinizin güvenliğini sağlamaya yardımcı olmak için Azure Data Factory veri taşıma hizmetlerinin kullandığı temel güvenlik altyapısı açıklanır. Data Factory yönetim kaynakları Azure güvenlik altyapısına kurulmuştur ve Azure tarafından sunulan tüm olası güvenlik önlemlerini kullanır.

Bir Data Factory çözümünde bir veya daha fazla [işlem hattı](concepts-pipelines-activities.md) oluşturursunuz. İşlem hattı, bir görevi gerçekleştiren etkinliklerden oluşan mantıksal gruptur. Bu işlem hatları, Data Factory 'nin oluşturulduğu bölgede bulunur.

Data Factory yalnızca birkaç bölgede kullanılabilir olsa da, veri taşıma hizmeti, veri uyumluluğu, verimlilik ve azaltılmış ağ çıkış maliyetlerini sağlamak için [genel olarak kullanılabilir](concepts-integration-runtime.md#integration-runtime-location) .

Azure Integration Runtime ve şirket içinde barındırılan Integration Runtime dahil Azure Data Factory, sertifikaları kullanarak şifrelenen bulut veri depoları için bağlı hizmet kimlik bilgileri hariç, geçici verileri, önbellek verilerini veya günlükleri depolamaz. Data Factory, verileri [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)arasında düzenlemek için veri odaklı iş akışları oluşturun ve diğer bölgelerde veya şirket içi bir ortamda [işlem hizmetlerini](compute-linked-services.md) kullanarak verileri işleme. Ayrıca, SDK 'Ları ve Azure Izleyici 'yi kullanarak iş akışlarını izleyebilir ve yönetebilirsiniz.

Data Factory için sertifikalıdır:

| **[CSA STAR sertifikası](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |
| **[HıTRUST](/compliance/regulatory/offering-hitrust)** |

Azure uyumluluğu ile ilgileniyorsanız ve Azure 'un kendi altyapısını nasıl güvenlik altına alırsanız, [Microsoft Güven Merkezi](https://microsoft.com/trustcenter/default.aspx)' ni ziyaret edin. Tüm Azure uyumluluk teklifleri denetiminin en son listesi için  https://aka.ms/AzureCompliance .

Bu makalede, aşağıdaki iki veri taşıma senaryosunda güvenlik konularını gözden geçiririz:

- **Bulut senaryosu**: Bu senaryoda hem kaynağınız hem de Hedefinizdeki internet üzerinden genel olarak erişilebilir olması gerekir. Bunlar Azure Storage, Azure SYNAPSE Analytics, Azure SQL veritabanı, Azure Data Lake Store, Amazon S3, Amazon Redshift, Salesforce gibi SaaS Hizmetleri ve FTP ve OData gibi web protokolleri gibi yönetilen bulut depolama hizmetlerini içerir. Desteklenen veri  [depoları ve biçimlerinde](copy-activity-overview.md#supported-data-stores-and-formats)desteklenen veri kaynaklarının tüm listesini bulun.
- **Karma senaryo**: Bu senaryoda, kaynağınız veya hedefi bir güvenlik duvarının arkasında ya da şirket içi bir şirket ağında yer alır. Ya da, veri deposu özel bir ağda veya sanal ağda (genellikle kaynak) bulunur ve herkese açık bir şekilde erişilebilir değildir. Sanal makinelerde barındırılan veritabanı sunucuları da bu senaryonun altına düşmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Bulut senaryoları

### <a name="securing-data-store-credentials"></a>Veri deposu kimlik bilgilerinin güvenliğini sağlama

- **Şifrelenmiş kimlik bilgilerini Azure Data Factory yönetilen bir depoda depolayın**. Data Factory, veri deposu kimlik bilgilerinizi Microsoft tarafından yönetilen sertifikalarla şifreleyerek korumanıza yardımcı olur. Bu sertifikalar her iki yılda bir döndürülür (sertifika yenilemesi ve kimlik bilgileri geçişi dahildir). Azure Depolama güvenliği hakkında daha fazla bilgi için bkz. [Azure Storage güvenliğine genel bakış](../storage/blobs/security-recommendations.md).
- **Azure Key Vault kimlik bilgilerini depolayın**. Veri deposunun kimlik bilgilerini [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)de saklayabilirsiniz. Data Factory, bir etkinliğin yürütülmesi sırasında kimlik bilgisini alır. Daha fazla bilgi için bkz. [kimlik bilgilerini Azure Key Vault Içinde depola](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Aktarım sırasında veri şifreleme
Bulut veri deposu HTTPS veya TLS 'yi destekliyorsa, Data Factory ve bulut veri deposundaki veri taşıma hizmetleri arasındaki tüm veri aktarımları, güvenli kanal HTTPS veya TLS aracılığıyla yapılır.

> [!NOTE]
> Azure SQL veritabanı ve Azure SYNAPSE Analytics 'e yönelik tüm bağlantılar, veriler veritabanına aktarılırken ve veritabanından aktarılırken şifreleme (SSL/TLS) gerektirir. JSON kullanarak bir işlem hattı yazarken, şifreleme özelliğini ekleyin ve bağlantı dizesinde **true** olarak ayarlayın. Azure depolama için bağlantı dizesinde **https** kullanabilirsiniz.

> [!NOTE]
> Verileri Oracle 'dan taşırken geçişte şifrelemeyi etkinleştirmek için aşağıdaki seçeneklerden birini izleyin:
>
> 1. Oracle Server 'da Oracle gelişmiş güvenlik (OAS) bölümüne gidin ve Üçlü DES şifrelemesini (3DES) ve Gelişmiş Şifreleme Standardı (AES) destekleyen şifreleme ayarlarını yapılandırın, Ayrıntılar için [buraya](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) bakın. ADF, Oracle bağlantısı kurarken OAS 'de yapılandırdığınız birini kullanmak üzere şifreleme yöntemini otomatik olarak belirler.
> 2. ADF 'de, bağlantı dizesinde (bağlantılı hizmette) EncryptionMethod = 1 ekleyebilirsiniz. Bu, şifreleme yöntemi olarak SSL/TLS kullanır. Bunu kullanmak için, şifreleme çakışmasını önlemek için Oracle sunucu tarafında OAS 'de SSL olmayan şifreleme ayarlarını devre dışı bırakmanız gerekir.

> [!NOTE]
> Kullanılan TLS sürümü 1,2.

### <a name="data-encryption-at-rest"></a>Bekleme sırasında veri şifrelemesi

Bazı veri depoları, bekleyen verilerin şifrelenmesini destekler. Bu veri depoları için veri şifreleme mekanizmasını etkinleştirmenizi öneririz. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure SYNAPSE Analytics 'te Saydam Veri Şifrelemesi (TDE), bekleyen verilerinizin gerçek zamanlı olarak şifrelenmesini ve şifresinin çözülmesini gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur. Bu davranış, istemci için saydamdır. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics 'te veritabanını güvenli hale getirme](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Veritabanı

Azure SQL veritabanı Ayrıca, uygulamada değişiklik yapılmasına gerek kalmadan gerçek zamanlı şifreleme ve veri şifre çözme işlemleri gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olan saydam veri şifrelemesini (TDE) destekler. Bu davranış, istemci için saydamdır. Daha fazla bilgi için bkz. [SQL veritabanı ve veri ambarı Için saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store, hesapta depolanan veriler için de şifreleme sağlar. Etkinleştirildiğinde Data Lake Store, verileri vermeden önce kalıcı hale getirerek ve şifresini çözmeden önce otomatik olarak şifreler ve verilere erişen istemciye saydam hale getirir. Daha fazla bilgi için bkz. [Azure Data Lake Store güvenlik](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob depolama ve Azure Tablo depolama

Azure Blob depolama ve Azure Tablo depolama desteği Depolama Hizmeti Şifrelemesi (SSE), depolamayı kalıcı yapmadan önce verilerinizi otomatik olarak şifreler ve almadan önce çözer. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3

Amazon S3, bekleyen verilerin hem istemci hem de sunucu şifrelemesini destekler. Daha fazla bilgi için bkz. [şifrelemeyi kullanarak verileri koruma](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift

Amazon Redshift, bekleyen veriler için küme şifrelemeyi destekler. Daha fazla bilgi için bkz. [Amazon Redshift veritabanı şifrelemesi](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce

Salesforce, tüm dosyalar, ekler ve özel alanların şifrelenmesini sağlayan kalkan platform şifrelemesini destekler. Daha fazla bilgi için bkz. [Web sunucusu OAuth kimlik doğrulama akışını anlama](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Karma senaryolar

Karma senaryolar, şirket içinde barındırılan tümleştirme çalışma zamanının bir sanal ağ (Azure) içinde veya bir sanal özel bulutun (Amazon) içinde yüklü olmasını gerektirir. Şirket içinde barındırılan tümleştirme çalışma zamanı, yerel veri depolarına erişebilmelidir. Şirket içinde barındırılan tümleştirme çalışma zamanı hakkında daha fazla bilgi için bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](./create-self-hosted-integration-runtime.md). 

![Şirket içinde barındırılan tümleştirme çalışma zamanı kanalları](media/data-movement-security-considerations/data-management-gateway-channels.png)

Komut kanalı, Data Factory ve şirket içinde barındırılan tümleştirme çalışma zamanının veri taşıma hizmetleri arasında iletişime olanak sağlar. İletişim, etkinlikle ilgili bilgiler içerir. Veri kanalı, şirket içi veri depoları ile bulut veri depoları arasında veri aktarmak için kullanılır.    

### <a name="on-premises-data-store-credentials"></a>Şirket içi veri deposu kimlik bilgileri

Kimlik bilgileri veri fabrikası içinde depolanabilir veya Azure Key Vault çalışma zamanı sırasında [Veri Fabrikası tarafından başvurulabilir](store-credentials-in-key-vault.md) . Veri Fabrikası içinde kimlik bilgileri depoluyorsanız, her zaman otomatik olarak barındırılan tümleştirme çalışma zamanı 'nda şifreli olarak depolanır. 

   - **Kimlik bilgilerini yerel olarak depolayın**. **Set-AzDataFactoryV2LinkedService** CMDLET 'ini JSON içinde bağlantı dizeleri ve kimlik bilgileri ile doğrudan kullanırsanız, bağlantılı hizmet şifrelenir ve şirket içinde barındırılan tümleştirme çalışma zamanı üzerinde depolanır.  Bu durumda kimlik bilgileri, son derece güvenli olan Azure arka uç hizmeti üzerinden akar ve bu, son olarak şifrelenir ve saklanır. Şirket içinde barındırılan tümleştirme çalışma zamanı, hassas verileri ve kimlik bilgisi bilgilerini şifrelemek için Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) kullanır.

   - **Azure Key Vault kimlik bilgilerini depolayın**. Veri deposunun kimlik bilgilerini [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)de saklayabilirsiniz. Data Factory, bir etkinliğin yürütülmesi sırasında kimlik bilgisini alır. Daha fazla bilgi için bkz. [kimlik bilgilerini Azure Key Vault Içinde depola](store-credentials-in-key-vault.md).

   - Kimlik bilgilerini **Azure arka ucu aracılığıyla şirket içinde barındırılan tümleştirme çalışma zamanına taşımadan yerel olarak depolayın**. Kimlik bilgilerini veri fabrikası arka ucu aracılığıyla akışa almak zorunda kalmadan şirket içinde barındırılan tümleştirme çalışma zamanında şifrelemek ve depolamak istiyorsanız [Azure Data Factory içindeki şirket içi veri depoları için kimlik bilgilerini şifreleme](encrypt-credentials-self-hosted-integration-runtime.md)bölümündeki adımları izleyin. Tüm bağlayıcılar bu seçeneği destekler. Şirket içinde barındırılan tümleştirme çalışma zamanı, hassas verileri ve kimlik bilgisi bilgilerini şifrelemek için Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) kullanır. 

   - Bağlı hizmette bağlantılı hizmet kimlik bilgilerini ve hassas ayrıntıları şifrelemek için **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet 'ini kullanın. Daha sonra **set-AzDataFactoryV2LinkedService** cmdlet 'ini kullanarak bağlı bir hizmet oluşturmak IÇIN döndürülen JSON (bağlantı dizesindeki **encryptedcredential** öğesiyle) öğesini kullanabilirsiniz.  

#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanında bağlı hizmeti şifrelerken kullanılan bağlantı noktaları

Varsayılan olarak, PowerShell, güvenli iletişim için şirket içinde barındırılan tümleştirme çalışma zamanı ile makinede 8060 numaralı bağlantı noktasını kullanır. Gerekirse, bu bağlantı noktası değiştirilebilir.  

![Ağ Geçidi için HTTPS bağlantı noktası](media/data-movement-security-considerations/https-port-for-gateway.png)


### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

Tüm veri aktarımları, Azure hizmetleriyle iletişim sırasında ortadaki adam saldırılarını engellemek için güvenli kanal HTTPS ve TCP üzerinden TLS aracılığıyla yapılır.

Ayrıca, şirket içi ağınız ve Azure arasında iletişim kanalının güvenliğini sağlamak için [ıPSEC VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) veya [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 'u da kullanabilirsiniz.

Azure sanal ağ, buluttaki ağınızın mantıksal bir gösterimidir. IPSec VPN (siteden siteye) veya ExpressRoute (özel eşleme) ayarlayarak, şirket içi bir ağı sanal ağınıza bağlayabilirsiniz.

Aşağıdaki tabloda, karma veri hareketine yönelik farklı kaynak ve hedef konum birleşimlerine dayanan ağ ve şirket içinde barındırılan tümleştirme çalışma zamanı yapılandırma önerileri özetlenmektedir.

| Kaynak      | Hedef                              | Ağ yapılandırması                    | Tümleştirme çalışma zamanı kurulumu                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Şirket içi | Sanal ağlarda dağıtılan sanal makineler ve bulut Hizmetleri | IPSec VPN (Noktadan siteye veya siteden siteye) | Şirket içinde barındırılan tümleştirme çalışma zamanının sanal ağdaki bir Azure sanal makinesine yüklenmesi gerekir.  |
| Şirket içi | Sanal ağlarda dağıtılan sanal makineler ve bulut Hizmetleri | ExpressRoute (özel eşleme)           | Şirket içinde barındırılan tümleştirme çalışma zamanının sanal ağdaki bir Azure sanal makinesine yüklenmesi gerekir.  |
| Şirket içi | Genel uç noktası olan Azure tabanlı hizmetler | ExpressRoute (Microsoft eşlemesi)            | Şirket içinde barındırılan tümleştirme çalışma zamanı, şirket içinde veya bir Azure sanal makinesine yüklenebilir. |

Aşağıdaki resimlerde ExpressRoute ve IPSec VPN (Azure sanal ağı ile) kullanarak şirket içi veritabanı ve Azure hizmetleri arasında veri taşımak için şirket içinde barındırılan tümleştirme çalışma zamanının kullanımı gösterilmektedir:

#### <a name="express-route"></a>Express Route

![Ağ geçidiyle ExpressRoute kullanma](media/data-movement-security-considerations/express-route-for-gateway.png) 

#### <a name="ipsec-vpn"></a>IPSec VPN

![Ağ Geçidi ile IPSec VPN](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a>Güvenlik Duvarı konfigürasyonları ve izin verilenler listesi IP adresleri için ayarlanıyor

> [!NOTE]
> Bağlantı noktalarını yönetmeniz veya şirket güvenlik duvarı düzeyindeki etki alanları için izin verilenler listesini ilgili veri kaynaklarının gerektirdiği şekilde ayarlamanız gerekebilir. Bu tablo, örnek olarak yalnızca Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure Data Lake Store kullanır.

> [!NOTE]
> Azure Data Factory aracılığıyla veri erişimi stratejileri hakkında daha fazla bilgi için [Bu makaleye](./data-access-strategies.md#data-access-strategies-through-azure-data-factory)bakın.

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Şirket içi/özel ağ için güvenlik duvarı gereksinimleri

Bir kuruluşta kurumsal güvenlik duvarı kuruluşun merkezi yönlendiricisinde çalışır. Windows Güvenlik Duvarı, şirket içinde barındırılan tümleştirme çalışma zamanının yüklendiği yerel makinede bir daemon olarak çalışır. 

Aşağıdaki tabloda, şirket güvenlik duvarları için giden bağlantı noktası ve etki alanı gereksinimleri verilmiştir:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE]
> Bağlantı noktalarını yönetmeniz veya şirket güvenlik duvarı düzeyindeki etki alanları için izin verilenler listesini ilgili veri kaynaklarının gerektirdiği şekilde ayarlamanız gerekebilir. Bu tablo, örnek olarak yalnızca Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure Data Lake Store kullanır.   

Aşağıdaki tabloda Windows Güvenlik Duvarı için gelen bağlantı noktası gereksinimleri verilmiştir:

| Gelen bağlantı noktaları | Description                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | [Azure Data Factory içindeki şirket içi veri depoları için kimlik bilgilerini şifreleme](encrypt-credentials-self-hosted-integration-runtime.md)ve kimlik bilgileri Yöneticisi uygulaması tarafından, şirket içinde barındırılan tümleştirme çalışma zamanı üzerinde şirket içi veri depoları için kimlik bilgilerini güvenli bir şekilde ayarlamak üzere PowerShell şifreleme cmdlet 'i gerekir. |

![Ağ Geçidi bağlantı noktası gereksinimleri](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Veri depolarında IP konfigürasyonları ve izin verilenler listesi ayarı

Buluttaki bazı veri depoları da depoya erişen makinenin IP adresine izin vermeyi gerektirir. Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin IP adresinin güvenlik duvarında uygun şekilde verildiğinden veya yapılandırıldığından emin olun.

Aşağıdaki bulut veri depoları, şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin IP adresine izin vermeniz gerekir. Bu veri mağazalarından bazıları varsayılan olarak izin verilenler listesine gerek olmayabilir.

* [Azure SQL Veritabanı](../azure-sql/database/firewall-configure.md)
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
* [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
* [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Şirket içinde barındırılan tümleştirme çalışma zamanı farklı veri fabrikaları arasında paylaşılabilir mi?**

Evet. Burada daha fazla ayrıntı [bulabilirsiniz](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Şirket içinde barındırılan tümleştirme çalışma zamanının çalışması için bağlantı noktası gereksinimleri nelerdir?**

Şirket içinde barındırılan tümleştirme çalışma zamanı, HTTP tabanlı bağlantıları internet 'e erişmesine olanak sağlar. Bu bağlantıyı yapmak için şirket içinde barındırılan tümleştirme çalışma zamanı için 443 giden bağlantı noktaları açılmalıdır. Kimlik bilgisi Yöneticisi uygulaması için yalnızca makine düzeyinde (Şirket güvenlik duvarı düzeyi değil) gelen bağlantı noktası 8060 ' i açın. Kaynak veya hedef olarak Azure SQL veritabanı veya Azure SYNAPSE Analytics kullanılıyorsa, bağlantı noktası 1433 ' i de açmanız gerekir. Daha fazla bilgi için, bkz. [IP adresleri Için güvenlik duvarı yapılandırması ve izin listesi ayarları](#firewall-configurations-and-allow-list-setting-up-for-ip-addresses) bölümü.

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory etkinliği performansını kopyalama hakkında daha fazla bilgi için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](copy-activity-performance.md).
