---
title: Azure Data Factory veri hareketine yönelik güvenlik konuları
description: Azure Data Factory veri hareketini güvenli hale getirme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 7f18505e02c5d65d21e93759eb5da480c20e2eb3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682633"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory-veri hareketine yönelik güvenlik konuları

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory için veri taşıma güvenliği konuları](../data-movement-security-considerations.md).

## <a name="introduction"></a>Giriş
Bu makalede, veri taşıma hizmetlerinin Azure Data Factory verileri güvenli hale getirmek için kullandığı temel güvenlik altyapısı açıklanır. Azure Data Factory yönetim kaynakları Azure güvenlik altyapısına kurulmuştur ve Azure tarafından sunulan tüm olası güvenlik önlemlerini kullanır.

Bir Data Factory çözümünde bir veya daha fazla [işlem hattı](data-factory-create-pipelines.md) oluşturursunuz. İşlem hattı, bir araya geldiğinde bir görev gerçekleştiren mantıksal etkinlik grubudur. Bu işlem hatları, Data Factory 'nin oluşturulduğu bölgede bulunur. 

Data Factory yalnızca **Batı ABD**, **Doğu ABD**ve **Kuzey Avrupa** bölgelerinde kullanılabilir olsa da, veri taşıma hizmeti [küresel olarak birkaç bölgede](data-factory-data-movement-activities.md#global)kullanılabilir. Data Factory hizmeti, veri taşıma hizmeti henüz o bölgeye dağıtılmamışsa, hizmeti açık bir şekilde bir alternatif bölge kullanmaya yönlendirmediğiniz müddetçe verilerin coğrafi bir alana/bölgeye ayrılmamasını sağlar. 

Azure Data Factory kendisi, sertifikalar kullanılarak şifrelenen bulut veri depoları için bağlı hizmet kimlik bilgileri dışında hiçbir veri depolamaz. Veri hareketini [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) arasında, verilerin işlenmesini de başka bölgelerde veya şirket içi bir ortamda [işlem hizmetleri](data-factory-compute-linked-services.md) kullanarak düzenlemek için veri temelinde iş akışları oluşturmanızı sağlar. Hem programlama, hem de kullanıcı arabirimi mekanizmalarını kullanarak [iş akışlarını izlemenizi ve yönetmenizi](data-factory-monitor-manage-pipelines.md) de sağlar.

Azure Data Factory kullanarak veri taşıma için **sertifikalıdır** :
-   [HıPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA YıLDıZı](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Azure uyumluluğu ile ilgileniyorsanız ve Azure 'un kendi altyapısını nasıl güvenlik altına alıyorsa, [Microsoft Güven Merkezi](https://microsoft.com/en-us/trustcenter/default.aspx)' ni ziyaret edin. 

Bu makalede, aşağıdaki iki veri taşıma senaryosunda güvenlik konularını gözden geçiririz: 

- **Bulut senaryosu**-Bu senaryoda, hem kaynak hem de hedef Internet üzerinden herkese açık bir şekilde erişilebilir. Bunlar Azure depolama, Azure SQL veri ambarı, Azure SQL veritabanı, Azure Data Lake Store, Amazon S3, Amazon Redshift, Salesforce gibi SaaS Hizmetleri ve FTP ve OData gibi web protokolleri gibi yönetilen bulut depolama hizmetlerini içerir. Desteklenen veri kaynaklarının tüm listesini [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats)bulabilirsiniz.
- **Karma senaryo**-Bu senaryoda, kaynak veya hedef bir güvenlik duvarının arkasında veya şirket içi bir şirket ağında yer alır ya da veri deposu özel bir ağ/sanal ağda (genellikle kaynak) ve genel olarak erişilebilir değildir. Sanal makinelerde barındırılan veritabanı sunucuları da bu senaryonun altına düşmektedir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Bulut senaryoları
### <a name="securing-data-store-credentials"></a>Veri deposu kimlik bilgilerinin güvenliğini sağlama
Azure Data Factory, **Microsoft tarafından yönetilen sertifikaları**kullanarak verileri **şifreleyerek** veri deposu kimlik bilgilerinizi korur. Bu sertifikalar her **iki yılda** bir döndürülür (sertifika yenileme ve kimlik bilgilerinin geçirilmesi dahildir). Bu şifrelenmiş kimlik bilgileri, **Azure Data Factory Yönetim Hizmetleri tarafından yönetilen bir Azure depolama**alanında güvenli bir şekilde depolanır. Azure Depolama güvenliği hakkında daha fazla bilgi için [Azure depolama güvenliğine genel bakış](../../security/fundamentals/storage-overview.md)konusuna bakın.

### <a name="data-encryption-in-transit"></a>Aktarım sırasında veri şifreleme
Bulut veri deposu HTTPS veya TLS 'yi destekliyorsa, Data Factory ve bulut veri deposundaki veri taşıma hizmetleri arasındaki tüm veri aktarımları, güvenli kanal HTTPS veya TLS aracılığıyla yapılır.

> [!NOTE]
> **Azure SQL veritabanı** ve **Azure SQL veri ambarı** 'na yönelik tüm bağlantılar her zaman ŞIFRELEME (SSL/TLS) gerektirir ve veriler veritabanına aktarılırken ve veritabanından gönderilir. JSON Düzenleyicisi kullanarak bir işlem hattı yazarken, **şifreleme** özelliğini ekleyin ve **bağlantı dizesinde** **true** olarak ayarlayın. [Kopyalama Sihirbazı](data-factory-azure-copy-wizard.md)'nı kullandığınızda, sihirbaz bu özelliği varsayılan olarak ayarlar. **Azure depolama**için bağlantı dizesinde **https** kullanabilirsiniz.

### <a name="data-encryption-at-rest"></a>Bekleme sırasında veri şifrelemesi
Bazı veri depoları, bekleyen verilerin şifrelenmesini destekler. Bu veri depoları için veri şifreleme mekanizmasını etkinleştirmenizi öneririz. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı
Azure SQL veri ambarı 'nda Saydam Veri Şifrelemesi (TDE), bekleyen veriler için gerçek zamanlı şifreleme ve şifre çözme gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korumaya yardımcı olur. Bu davranış, istemci için saydamdır. Daha fazla bilgi için bkz. [SQL veri ambarı 'nda veritabanını güvenli hale getirme](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Veritabanı
Azure SQL veritabanı Ayrıca, uygulamada değişiklik yapılmasına gerek kalmadan gerçek zamanlı şifreleme ve veri şifre çözme işlemleri gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korumaya yardımcı olan saydam veri şifrelemesini (TDE) destekler. Bu davranış, istemci için saydamdır. Daha fazla bilgi için bkz. [Azure SQL veritabanı ile saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake depolama, hesapta depolanan veriler için de şifreleme sağlar. Etkinleştirildiğinde, Data Lake deposu verileri vermeden önce kalıcı hale getirerek ve şifresini çözmeden önce otomatik olarak şifreler ve verilere erişen istemciye saydam hale getirir. Daha fazla bilgi için bkz. [Azure Data Lake Store güvenlik](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob depolama ve Azure Tablo depolama
Azure Blob depolama ve Azure Tablo depolama, verileri depolamaya kalıcı yapmadan önce otomatik olarak şifreleyen Depolama Hizmeti Şifrelemesi (SSE) destekler ve almadan önce şifresini çözer. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3, bekleyen verilerin hem istemci hem de sunucu şifrelemesini destekler. Daha fazla bilgi için bkz. [şifrelemeyi kullanarak verileri koruma](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Şu anda Data Factory, bir sanal özel bulut (VPC) içinde Amazon S3 desteklemez.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift, bekleyen veriler için küme şifrelemeyi destekler. Daha fazla bilgi için bkz. [Amazon Redshift veritabanı şifrelemesi](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Şu anda Data Factory, bir VPC içinde Amazon Redshift 'ı desteklemez. 

#### <a name="salesforce"></a>Salesforce
Salesforce, tüm dosyaları, ekleri ve özel alanları şifrelemeye izin veren Shield platform şifrelemesini destekler. Daha fazla bilgi için bkz. [Web sunucusu OAuth kimlik doğrulama akışını anlama](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Karma senaryolar (Veri Yönetimi ağ geçidini kullanarak)
Karma senaryolar, Veri Yönetimi ağ geçidinin şirket içi bir ağa veya bir sanal ağ (Azure) ya da bir sanal özel buluta (Amazon) yüklenmesini gerektirir. Ağ geçidinin yerel veri depolarına erişebilmesi gerekir. Ağ Geçidi hakkında daha fazla bilgi için bkz. [veri yönetimi Gateway](data-factory-data-management-gateway.md). 

![Veri Yönetimi ağ geçidi kanalları](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Komut kanalı** , Data Factory ve veri yönetimi ağ geçidinde veri taşıma hizmetleri arasında iletişime olanak sağlar. İletişim, etkinlikle ilgili bilgiler içerir. Veri kanalı, şirket içi veri depoları ile bulut veri depoları arasında veri aktarmak için kullanılır.    

### <a name="on-premises-data-store-credentials"></a>Şirket içi veri deposu kimlik bilgileri
Şirket içi veri depolarınız için kimlik bilgileri yerel olarak depolanır (bulutta değil). Bunlar üç farklı şekilde ayarlanabilir. 

- Azure portalından/kopyalama sihirbazından HTTPS aracılığıyla **düz metin** (daha az güvenli) kullanma. Kimlik bilgileri, şirket içi ağ geçidine düz metin olarak geçirilir.
- **Kopyalama sihirbazından JavaScript şifreleme kitaplığı 'nı**kullanma.
- **Tıklama tabanlı kimlik bilgileri Yöneticisi uygulamasını**kullanma. Bir kez tıklama uygulaması, ağ geçidine erişimi olan şirket içi makinede yürütülür ve veri deposunun kimlik bilgilerini ayarlar. Bu seçenek ve bir sonraki seçenek en güvenli seçeneklerdir. Kimlik bilgisi Yöneticisi uygulaması, varsayılan olarak güvenli iletişim için ağ geçidine sahip makinede 8050 numaralı bağlantı noktasını kullanır.  
- Kimlik bilgilerini şifrelemek için [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell cmdlet 'ini kullanın. Cmdlet 'i, kimlik bilgilerini şifrelemek için kullanılacak ağ geçidinin yapılandırıldığı sertifikayı kullanır. Bu cmdlet tarafından döndürülen şifrelenmiş kimlik bilgilerini kullanabilir ve bunu [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet 'iyle veya içindeki JSON kod PARÇACıĞıNDA kullandığınız JSON **kimlik bilgileri** öğesine **ekleyebilirsiniz.** Portalda düzenleyici Data Factory. Bu seçenek ve tek tıklama uygulaması en güvenli seçeneklerdir. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript şifreleme kitaplığı tabanlı şifreleme
[Kopyalama sihirbazından](data-factory-copy-wizard.md) [JavaScript şifreleme kitaplığı](https://www.microsoft.com/download/details.aspx?id=52439) 'nı kullanarak veri deposu kimlik bilgilerini şifreleyebilirsiniz. Bu seçeneği belirlediğinizde, kopyalama Sihirbazı ağ geçidinin ortak anahtarını alır ve veri deposu kimlik bilgilerini şifrelemek için kullanır. Kimlik bilgilerinin ağ geçidi makinesi tarafından şifresi çözülür ve Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)tarafından korunur.

**Desteklenen tarayıcılar:** IE8, ıE9, ıE10, ıE11, Microsoft Edge ve en son Firefox, Chrome, Opera, Safari tarayıcıları. 

#### <a name="click-once-credentials-manager-app"></a>Tıkla-bir kez kimlik bilgileri Yöneticisi uygulaması
İşlem hatları yazarken Azure portal/kopyalama sihirbazından tıklama tabanlı kimlik bilgileri Yöneticisi uygulamasını başlatabilirsiniz. Bu uygulama, kimlik bilgilerinin kablo üzerinden düz metin olarak aktarılmamasını sağlar. Varsayılan olarak, güvenli iletişim için ağ geçidine sahip makinede **8050** numaralı bağlantı noktasını kullanır. Gerekirse, bu bağlantı noktası değiştirilebilir.  
  
![Ağ Geçidi için HTTPS bağlantı noktası](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Şu anda Veri Yönetimi ağ geçidi tek bir **sertifika**kullanıyor. Bu sertifika, ağ geçidi yüklemesi sırasında oluşturulur (2016 Kasım ve sürüm 2.4 ' den sonra oluşturulan Veri Yönetimi ağ geçidi için geçerlidir. xxxx. x veya üzeri). Bu sertifikayı kendi SSL/TLS sertifikanız ile değiştirebilirsiniz. Bu sertifika, veri deposu kimlik bilgilerini ayarlamak için ağ geçidi makinesine güvenli bir şekilde bağlanmak üzere tıkla-bir tek kimlik bilgileri Yöneticisi uygulaması tarafından kullanılır. Ağ geçidine sahip makinede Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) kullanarak şirket içinde güvenli bir şekilde veri deposu kimlik bilgilerini depolar. 

> [!NOTE]
> 2016 Kasım veya 2.3 sürümünden önce yüklenen eski ağ geçitleri. xxxx. x, buluta şifrelenmiş ve depolanan kimlik bilgilerini kullanmaya devam eder. Ağ geçidini en son sürüme yükseltseniz bile, kimlik bilgileri şirket içi bir makineye geçirilmez    
  
| Ağ Geçidi sürümü (oluşturma sırasında) | Depolanan kimlik bilgileri | Kimlik bilgisi şifreleme/güvenlik | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3. xxxx. x | Bulutta | Sertifika kullanılarak şifrelendi (kimlik bilgileri Yöneticisi uygulamasının kullandığı bilgisayardan farklı) | 
| > = 2,4. xxxx. x | Şirket içi | DPAPI aracılığıyla güvenli hale getirilir | 
  

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
Tüm veri aktarımları, Azure hizmetleriyle iletişim sırasında ortadaki adam saldırılarını engellemek için güvenli kanal **https** ve **TCP üzerinden TLS** aracılığıyla yapılır.
 
Ayrıca, şirket içi ağınız ve Azure arasında iletişim kanalını daha güvenli hale getirmek için [ıPSEC VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) veya [Express Route](../../expressroute/expressroute-introduction.md) 'u da kullanabilirsiniz.

Sanal ağ, buluttaki ağınızın mantıksal bir gösterimidir. IPSec VPN (siteden siteye) veya Express Route (özel eşleme) ayarlayarak, şirket içi bir ağı Azure sanal ağınıza (VNet) bağlayabilirsiniz     

Aşağıdaki tabloda, karma veri hareketine yönelik farklı kaynak ve hedef konum birleşimlerine göre ağ ve ağ geçidi yapılandırma önerileri özetlenmektedir.

| Kaynak | Hedef | Ağ yapılandırması | Ağ geçidi kurulumu |
| ------ | ----------- | --------------------- | ------------- | 
| Şirket içi | Sanal ağlarda dağıtılan sanal makineler ve bulut Hizmetleri | IPSec VPN (Noktadan siteye veya siteden siteye) | Ağ Geçidi, şirket içinde veya VNet 'teki bir Azure sanal makinesine (VM) yüklenebilir | 
| Şirket içi | Sanal ağlarda dağıtılan sanal makineler ve bulut Hizmetleri | ExpressRoute (özel eşleme) | Ağ Geçidi, şirket içinde veya VNet 'teki bir Azure VM 'ye yüklenebilir | 
| Şirket içi | Genel uç noktası olan Azure tabanlı hizmetler | ExpressRoute (genel eşleme) | Ağ geçidinin şirket içinde yüklü olması gerekir | 

Aşağıdaki resimlerde Express Route ve IPSec VPN (sanal ağ ile) kullanarak şirket içi veritabanı ve Azure hizmetleri arasında veri taşımak için Veri Yönetimi ağ geçidinin kullanımı gösterilmektedir:

**Hızlı rota:**
 
![Ağ Geçidi ile Express Route kullanma](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![Ağ Geçidi ile IPSec VPN](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Güvenlik duvarı yapılandırmalarının ve güvenilir listenin IP adresi

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Şirket içi/özel ağ için güvenlik duvarı gereksinimleri  
Bir kuruluşta kurumsal **güvenlik duvarı** kuruluşun merkezi yönlendiricisinde çalışır. Ve **Windows Güvenlik Duvarı** , ağ geçidinin yüklü olduğu yerel makinede bir daemon olarak çalışır. 

Aşağıdaki tabloda, **Şirket güvenlik duvarı**için **giden bağlantı noktası** ve etki alanı gereksinimleri verilmiştir.

| Etki alanı adları | Giden bağlantı noktaları | Açıklama |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Data Factory içindeki veri taşıma hizmetlerine bağlanmak için ağ geçidi için gereklidir |
| `*.core.windows.net` | 443 | [Hazırlanmış kopya](data-factory-copy-activity-performance.md#staged-copy) özelliğini kullandığınızda, Azure depolama hesabına bağlanmak için ağ geçidi tarafından kullanılır. | 
| `*.frontend.clouddatahub.net` | 443 | Azure Data Factory hizmetine bağlanmak için ağ geçidi gereklidir. | 
| `*.database.windows.net` | 1433   | (Isteğe bağlı) hedef Azure SQL veritabanı/Azure SQL veri ambarı olduğunda gereklidir. Azure SQL veritabanı/Azure SQL veri ambarı 'na veri kopyalamak için 1433 numaralı bağlantı noktasını açmadan hazırlanan kopyalama özelliğini kullanın. | 
| `*.azuredatalakestore.net` | 443 | (Isteğe bağlı) hedef Azure Data Lake deposu olduğunda gereklidir | 

> [!NOTE] 
> Şirket güvenlik duvarı düzeyindeki bağlantı noktalarını, ilgili veri kaynakları için gereken şekilde yönetmeniz/bu etki alanlarını daha fazla listeye almanız gerekebilir. Bu tablo yalnızca Azure SQL veritabanı, Azure SQL veri ambarı, örnek olarak Azure Data Lake Store kullanır.   

Aşağıdaki tabloda **Windows Güvenlik Duvarı**için **gelen bağlantı noktası** gereksinimleri verilmiştir.

| Gelen bağlantı noktaları | Açıklama | 
| ------------- | ----------- | 
| 8050 (TCP) | Ağ geçidinde şirket içi veri depoları için kimlik bilgilerini güvenli bir şekilde ayarlamak üzere kimlik bilgileri Yöneticisi uygulaması gereklidir. | 

![Ağ Geçidi bağlantı noktası gereksinimleri](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Veri deposunda IP yapılandırması/beyaz listeleme
Buluttaki bazı veri depoları da bunlara erişen makinenin IP adresinin beyaz listesini gerektirir. Ağ Geçidi makinesinin IP adresinin güvenlik duvarından uygun şekilde listelendiğinden/yapılandırıldığından emin olun.

Aşağıdaki bulut veri depoları, ağ geçidi makinesinin IP adresinin beyaz listesini gerektirir. Bu veri mağazalarından bazıları varsayılan olarak IP adresinin beyaz listesini gerektirmeyebilir. 

- [Azure SQL Veritabanı](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Veri Ambarı](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Soru:** Ağ Geçidi farklı veri fabrikaları genelinde paylaşılabilir mi?
**Cevap:** Henüz bu özelliği desteklemiyoruz. Üzerinde çalışmaya devam ediyoruz.

**Soru:** Ağ geçidinin çalışması için bağlantı noktası gereksinimleri nelerdir?
**Cevap:** Ağ Geçidi, HTTP tabanlı bağlantıları internet 'te açmaya olanak sağlar. Bu bağlantıyı yapmak için ağ geçidi **443 ve 80 giden bağlantı noktaları** açılmalıdır. Kimlik bilgisi Yöneticisi uygulaması için yalnızca makine düzeyinde (kurumsal güvenlik duvarı düzeyinde değil) **gelen bağlantı noktası 8050** ' i açın. Azure SQL veritabanı veya Azure SQL veri ambarı kaynak/hedef olarak kullanılıyorsa, **1433** bağlantı noktasını da açmanız gerekir. Daha fazla bilgi için bkz. [güvenlik duvarı yapılandırması ve beyaz LISTEYE IP adresleri](#firewall-configurations-and-whitelisting-ip-address-of gateway) bölümü. 

**Soru:** Ağ Geçidi için sertifika gereksinimleri nelerdir?
**Cevap:** Geçerli ağ geçidi, veri deposu kimlik bilgilerini güvenli bir şekilde ayarlamak için kimlik bilgisi Yöneticisi uygulaması tarafından kullanılan bir sertifika gerektirir. Bu sertifika, ağ geçidi kurulumu tarafından oluşturulan ve yapılandırılan kendinden imzalı bir sertifikadır. Bunun yerine kendi TLS/SSL sertifikanızı kullanabilirsiniz. Daha fazla bilgi için bkz. [tıklama-bir kez kimlik bilgileri Yöneticisi uygulaması](#click-once-credentials-manager-app) bölümü. 

## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliğinin performansı hakkında daha fazla bilgi için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md).

 
