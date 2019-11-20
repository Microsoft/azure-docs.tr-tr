---
title: Azure şifrelemeye genel bakış | Microsoft Docs
description: Azure 'da çeşitli şifreleme seçenekleri hakkında bilgi edinin
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 541039c82d5ea21c43a847da2710bef4162a2bc7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804047"
---
# <a name="azure-encryption-overview"></a>Azure şifrelemeye genel bakış

Bu makalede, Microsoft Azure ' de şifrelemenin nasıl kullanıldığına ilişkin bir genel bakış sunulmaktadır. Bekleyen şifreleme, uçuşta şifreleme ve Azure Key Vault ile anahtar yönetimi dahil olmak üzere, şifrelemenin ana bölgelerini ele alır. Her bölüm daha ayrıntılı bilgi için bağlantılar içerir.

## <a name="encryption-of-data-at-rest"></a>Bekleyen verilerin şifrelenmesi

Bekleyen veriler, fiziksel medyada kalıcı depolamada bulunan ve herhangi bir dijital biçimdeki bilgileri içerir. Medya, manyetik veya optik medya, arşivlenmiş veriler ve veri yedeklemeleri üzerinde dosya içerebilir. Microsoft Azure, dosya, disk, blob ve tablo depolama dahil olmak üzere farklı ihtiyaçları karşılamak için çeşitli veri depolama çözümleri sunar. Microsoft ayrıca [Azure SQL veritabanı](../../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)ve Azure Data Lake korumak için şifreleme sağlar.

Bekleyen veri şifrelemesi, hizmet olarak yazılım (SaaS), hizmet olarak platform (PaaS) ve hizmet olarak altyapı (IaaS) bulut modelleri arasında hizmetler için kullanılabilir. Bu makale, Azure şifreleme seçeneklerini kullanmanıza yardımcı olacak kaynakları özetler ve sağlar.

Rest 'deki verilerin Azure 'da nasıl şifrelendiği hakkında daha ayrıntılı bir tartışma için bkz. [Azure veri şifreleme-Rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure şifreleme modelleri

Azure, hizmet tarafından yönetilen anahtarlar, Key Vault içindeki müşteri tarafından yönetilen anahtarlar veya müşteri tarafından denetlenen donanımda müşteri tarafından yönetilen anahtarlar kullanan sunucu tarafı şifreleme dahil olmak üzere çeşitli şifreleme modellerini destekler. İstemci tarafı şifreleme ile, anahtarları şirket içinde veya başka bir güvenli konumda yönetebilir ve saklayabilirsiniz.

### <a name="client-side-encryption"></a>İstemci Tarafında Şifreleme

İstemci tarafı şifreleme, Azure dışında gerçekleştirilir. Şunları içerir:

- Müşterinin veri merkezinde veya bir hizmet uygulaması tarafından çalışan bir uygulama tarafından şifrelenen veriler.
- Azure tarafından alındığında zaten şifrelenmiş veriler.

İstemci tarafı şifreleme ile, bulut hizmeti sağlayıcılarının şifreleme anahtarlarına erişimi yoktur ve bu verilerin şifresini çözemez. Anahtarların tamamen denetimini sürdürmenize devam edersiniz.

### <a name="server-side-encryption"></a>Sunucu tarafı şifrelemesi

Üç sunucu tarafı şifreleme modeli, gereksinimlerinize göre seçebileceğiniz farklı anahtar yönetim özellikleri sunar:

- **Hizmet tarafından yönetilen anahtarlar**: düşük ek yük ile denetim ve kolaylık sağlayan bir bileşim sunar.

- **Müşteri tarafından yönetilen anahtarlar**: kendi anahtarlarınızı getır (bYok) desteği de dahil olmak üzere anahtarlar üzerinde denetim sağlar veya yenilerini oluşturmanıza izin verir.

- **Müşteri denetimli donanımda hizmet tarafından yönetilen anahtarlar**: özel deponuzdaki anahtarları Microsoft denetimi dışında yönetmenizi sağlar. Bu özellik kendi anahtarınızı barındırın (HYOK) olarak adlandırılır. Ancak, yapılandırma karmaşıktır ve çoğu Azure hizmeti bu modeli desteklemez.

### <a name="azure-disk-encryption"></a>Azure disk şifrelemesi

Windows ve Linux sanal makinelerini, [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) teknolojisini ve Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) [' i kullanarak](/azure/security/fundamentals/azure-disk-encryption-vms-vmss), hem işletim sistemi disklerini hem de tam birim şifrelemesi ile veri disklerini korumak üzere koruyabilirsiniz.

Şifreleme anahtarları ve gizli dizileri [Azure Key Vault aboneliğinizde](../../key-vault/key-vault-overview.md)korunmuş. Azure Backup hizmetini kullanarak, anahtar şifreleme anahtarı (KEK) yapılandırmasını kullanan şifrelenmiş sanal makineleri (VM) yedekleyebilir ve geri yükleyebilirsiniz.

### <a name="azure-storage-service-encryption"></a>Azure Depolama Hizmeti Şifrelemesi

Azure Blob depolama ve Azure dosya paylaşımlarında bekleyen veriler, sunucu tarafı ve istemci tarafı senaryolarda şifrelenir.

[Azure depolama hizmeti şifrelemesi (SSE)](../../storage/common/storage-service-encryption.md) , verileri depolanmadan önce otomatik olarak şifreleyebilir ve verileri alırken otomatik olarak çözer. İşlem kullanıcılara tamamen saydamdır. Depolama Hizmeti Şifrelemesi, kullanılabilir en güçlü blok şifrelemelerin biri olan 256-bit [Gelişmiş Şifreleme Standardı (AES) şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanır. AES şifreleme, şifre çözme ve anahtar yönetimini saydam bir şekilde işler.

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob 'larının istemci tarafı şifrelemesi

Azure Blob 'larının istemci tarafı şifrelemesini çeşitli şekillerde gerçekleştirebilirsiniz.

İstemci uygulamalarınızdaki verileri Azure depolama birimine yüklemeden önce şifrelemek için .NET NuGet paketi için Azure depolama Istemci kitaplığı ' nı kullanabilirsiniz.

.NET NuGet paketi için Azure depolama Istemci kitaplığı hakkında daha fazla bilgi edinmek ve indirmek için bkz. [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Key Vault ile istemci tarafı şifrelemesini kullandığınızda verileriniz, Azure depolama istemci SDK 'Sı tarafından oluşturulan tek seferlik bir simetrik Içerik şifreleme anahtarı (CEK) kullanılarak şifrelenir. CEK, bir simetrik anahtar veya asimetrik anahtar çifti olabilen bir anahtar şifreleme anahtarı (KEK) kullanılarak şifrelenir. Yerel olarak yönetebilir veya Key Vault ' de kaydedebilirsiniz. Şifrelenmiş veriler daha sonra Azure depolama alanına yüklenir.

Key Vault ile istemci tarafı şifreleme hakkında daha fazla bilgi edinmek ve nasıl yapılır yönergeleriyle çalışmaya başlamak için bkz. [öğretici: Azure depolama 'daki blobları Key Vault kullanarak şifreleme ve şifre çözme](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Son olarak, Azure depolama 'ya veri yüklemeden önce, istemci tarafı şifrelemeyi gerçekleştirmek ve verileri istemciye indirdiğinizde şifrelerini çözmek için, Java için Azure Storage Istemci kitaplığı ' nı da kullanabilirsiniz. Bu kitaplık Ayrıca depolama hesabı anahtar yönetimi için [Key Vault](https://azure.microsoft.com/services/key-vault/) tümleştirmeyi destekler.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Azure SQL veritabanı ile bekleyen verilerin şifrelenmesi

[Azure SQL veritabanı](../../sql-database/sql-database-technical-overview.md) , Azure 'da ilişkisel VERI, JSON, uzamsal ve XML gibi yapıları destekleyen genel amaçlı bir ilişkisel veritabanı hizmetidir. SQL veritabanı, Always Encrypted özelliği aracılığıyla Saydam Veri Şifrelemesi (TDE) özelliği ve istemci tarafı şifreleme aracılığıyla sunucu tarafı şifrelemeyi destekler.

#### <a name="transparent-data-encryption"></a>Saydam Veri Şifrelemesi

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) , kurtarma sırasında kullanılabilirlik için veritabanı önyükleme kaydında depolanan bir veritabanı şifreleme anahtarı (dek) kullanarak [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL veritabanı](../../sql-database/sql-database-technical-overview.md)ve [Azure SQL veri ambarı](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) veri dosyalarını gerçek zamanlı olarak şifrelemek için kullanılır.

TDE, AES ve üçlü veri şifreleme standardı (3DES) şifreleme algoritmaları kullanarak verileri ve günlük dosyalarını korur. Veritabanı dosyasının şifrelenmesi sayfa düzeyinde gerçekleştirilir. Şifrelenmiş bir veritabanındaki sayfalar diske yazılmadan önce şifrelenir ve belleğe okunduklarında şifresi çözülür. TDE, yeni oluşturulan Azure SQL veritabanlarında varsayılan olarak etkinleştirilmiştir.

#### <a name="always-encrypted-feature"></a>Always Encrypted özelliği

Azure SQL 'de [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) özelliği ile, istemci uygulamalarında VERILERI Azure SQL veritabanı 'nda depolamadan önce şifreleyebilirsiniz. Ayrıca, üçüncü taraflara şirket içi veritabanı yönetiminin temsilciliğini etkinleştirebilir ve verileri ve bunları yöneten ancak erişimi olmayan, bu verilere ait olmayan ayrımı koruyabilirsiniz.

#### <a name="cell-level-or-column-level-encryption"></a>Hücre düzeyi veya sütun düzeyinde şifreleme

Azure SQL veritabanı ile Transact-SQL ' y i kullanarak bir veri sütununa simetrik şifreleme uygulayabilirsiniz. Bu yaklaşım, belirli sütunları şifrelemek için veya farklı şifreleme anahtarlarıyla belirli veri hücrelerini şifrelemek için kullanabileceğiniz [hücre düzeyinde şifreleme veya sütun düzeyinde şifreleme (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)olarak adlandırılır. Bu sayede, sayfalardaki verileri şifreleyen TDE daha ayrıntılı şifreleme özelliği elde edersiniz.

CLE, simetrik veya asimetrik anahtarları, bir sertifikanın ortak anahtarını veya 3DES kullanarak bir parolayı kullanarak verileri şifrelemek için kullanabileceğiniz yerleşik işlevlere sahiptir.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB veritabanı şifrelemesi

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) , Microsoft 'un genel olarak dağıtılmış, çok modelli veritabanıdır. Geçici olmayan depolamada Cosmos DB depolanan kullanıcı verileri (katı hal sürücüleri) varsayılan olarak şifrelenir. Açmak veya kapatmak için denetim yoktur. Bekleyen şifreleme, güvenli anahtar depolama sistemleri, şifrelenmiş ağlar ve şifreleme API 'Leri dahil olmak üzere çeşitli güvenlik teknolojileri kullanılarak uygulanır. Şifreleme anahtarları Microsoft tarafından yönetilir ve Microsoft iç yönergeleri uyarınca döndürülür.

### <a name="at-rest-encryption-in-data-lake"></a>Data Lake 'de Rest şifreleme

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) , tek bir yerde toplanan her veri türünün, gereksinim veya şemanın herhangi bir resmi tanımından önce toplanmasından oluşan kurumsal çapta bir depodur. Data Lake Store, bekleyen verilerin "açık" olarak şifrelenmesini destekler ve bu, hesabınızın oluşturulması sırasında ayarlanır. Varsayılan olarak, Azure Data Lake Store anahtarları yönetir, ancak bunları kendiniz yönetme seçeneğiniz vardır.

Verileri şifrelemek ve şifrelerini çözmek için üç tür anahtar kullanılır: Ana şifreleme anahtarı (MEK), veri şifreleme anahtarı (DEK) ve blok şifreleme anahtarı (BEK). MEK, kalıcı medyada depolanan DEK ' ı şifrelemek için kullanılır ve BEK, DEK ve veri bloğundan türetilir. Kendi anahtarlarınızı yönetiyorsanız, MEK döndürebilirsiniz.

## <a name="encryption-of-data-in-transit"></a>Yoldaki verilerin şifrelenmesi

Azure, verileri bir konumdan diğerine taşırken verilerin özel tutulması için birçok mekanizma sunar.

### <a name="tlsssl-encryption-in-azure"></a>Azure 'da TLS/SSL şifrelemesi

Microsoft, bulut hizmetleri ve müşteriler arasında seyahat edildiğinde verileri korumak için [Aktarım Katmanı Güvenliği](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokolünü kullanır. Microsoft veri merkezleri, Azure hizmetlerine bağlanan istemci sistemleriyle bir TLS bağlantısı anlaşmasına sahiptir. TLS, güçlü kimlik doğrulaması, ileti gizliliği ve bütünlük (ileti izinsiz değişikliği, yakalama ve forgery), birlikte çalışabilirlik, algoritma esnekliği ve dağıtım ve kullanım kolaylığı sağlar.

[Kusursuz Iletme gizliliği](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS), müşterilerin istemci sistemleri ile Microsoft bulut hizmetleri arasındaki bağlantıları benzersiz anahtarlara karşı korur. Bağlantılar Ayrıca RSA tabanlı 2.048 bit şifreleme anahtar uzunluklarını kullanır. Bu bileşim, birisinin aktarım aşamasında olan verileri kesmesini ve erişimini zorlaştırır.

### <a name="azure-storage-transactions"></a>Azure depolama işlemleri

Azure depolama ile Azure portal aracılığıyla etkileşim kurarken tüm işlemler HTTPS üzerinden gerçekleşir. Azure depolama ile etkileşim kurmak için depolama REST API HTTPS üzerinden de kullanabilirsiniz. Depolama hesabı için gerekli olan güvenli aktarımı etkinleştirerek depolama hesaplarındaki nesnelere erişmek için REST API 'Lerini çağırdığınızda HTTPS kullanımını zorunlu kılabilirsiniz.

Azure depolama nesnelerine erişim yetkisi vermek için kullanılabilen paylaşılan erişim Imzaları ([SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)), paylaşılan erişim imzaları KULLANDıĞıNıZDA yalnızca HTTPS protokolünün kullanılabileceğini belirtme seçeneği de vardır. Bu yaklaşım, SAS belirteçleriyle bağlantı gönderen herhangi bir gövdenin uygun protokolü kullanmasını sağlar.

Azure dosya paylaşımlarına erişmek için kullanılan [SMB 3,0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), şifrelemeyi destekler ve windows Server 2012 R2, Windows 8, Windows 8.1 ve Windows 10 ' da kullanılabilir. Çapraz bölge erişimine ve hatta masaüstüne erişime izin verir.

İstemci tarafı şifreleme, verileri Azure depolama örneğinize gönderilmeden önce şifreler, böylece ağ üzerinde ilerledikçe şifrelenir.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure sanal ağları üzerinden SMB şifrelemesi 

Windows Server 2012 veya üzeri bir sürümü çalıştıran VM 'lerde [SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) ' i kullanarak, Azure sanal ağları üzerinden geçiş sırasında verileri şifreleyerek veri aktarımlarını güvende hale getirebilirsiniz. Verileri şifreleyerek, izinsiz ve gizlice dinleme saldırılarına karşı korumaya yardımcı olursunuz. Yöneticiler, tüm sunucu veya yalnızca belirli paylaşımlar için SMB şifrelemeyi etkinleştirebilir.

Varsayılan olarak, bir paylaşım veya sunucu için SMB şifrelemesi açık olduktan sonra, şifrelenmiş paylaşımlara yalnızca SMB 3,0 istemcilerinin erişmesine izin verilir.

## <a name="in-transit-encryption-in-vms"></a>VM 'lerde geçiş içi şifreleme

Windows çalıştıran VM 'Ler arasında geçiş yapılan veriler, bağlantının doğasına bağlı olarak çeşitli yollarla şifrelenir.

### <a name="rdp-sessions"></a>RDP oturumları

Bir Windows istemci bilgisayarından [Uzak Masaüstü Protokolü (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) kullanarak veya bir RDP istemcisinin yüklü olduğu bir Mac 'TEN bir VM 'ye bağlanabilir ve oturum açabilirsiniz. RDP oturumlarındaki ağ üzerinden aktarım sırasında bulunan veriler TLS ile korunabilir.

Ayrıca, uzak masaüstü 'Nü kullanarak Azure 'da bir Linux VM 'sine bağlanabilirsiniz.

### <a name="secure-access-to-linux-vms-with-ssh"></a>SSH ile Linux VM 'lerine güvenli erişim

Uzaktan Yönetim için, Azure 'da çalışan Linux VM 'lerine bağlanmak üzere [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) kullanabilirsiniz. SSH, güvenli olmayan bağlantılarda güvenli oturum açma işlemlerinin yapılmasına izin veren şifreli bir bağlantı protokolüdür. Azure 'da barındırılan Linux VM 'Leri için varsayılan bağlantı protokolüdür. Kimlik doğrulaması için SSH anahtarlarını kullanarak, oturum açmak için parola ihtiyacını ortadan kaldırın. SSH, kimlik doğrulaması için ortak/özel anahtar çifti (asimetrik şifreleme) kullanır.

## <a name="azure-vpn-encryption"></a>Azure VPN şifrelemesi

Ağ üzerinden gönderilen verilerin gizliliğini korumak için güvenli bir tünel oluşturan bir sanal özel ağ aracılığıyla Azure 'a bağlanabilirsiniz.

### <a name="azure-vpn-gateways"></a>Azure VPN ağ geçitleri

[Azure VPN ağ geçidini](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) , sanal ağınız ile şirket içi konumunuz arasında ortak bir bağlantı üzerinden şifrelenmiş trafik göndermek veya sanal ağlar arasında trafik göndermek için kullanabilirsiniz.

Siteden siteye VPN 'Ler, aktarım şifrelemesi için [IPSec](https://en.wikipedia.org/wiki/IPsec) kullanır. Azure VPN ağ geçitleri bir dizi varsayılan teklif kullanır. Azure VPN ağ geçitlerini, Azure varsayılan ilke kümeleri yerine belirli şifreleme algoritmaları ve anahtar güçleriyle özel bir IPSec/ıKE ilkesi kullanacak şekilde yapılandırabilirsiniz.

### <a name="point-to-site-vpns"></a>Noktadan siteye VPN 'Ler

Noktadan siteye VPN 'Ler, tek tek istemci bilgisayarlarının bir Azure sanal ağına erişmesine izin verir. [Güvenli Yuva Tünel Protokolü (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) , VPN tüneli oluşturmak için kullanılır. Güvenlik duvarlarına çapraz geçiş yapabilir (tünel bir HTTPS bağlantısı olarak görünür). Noktadan siteye bağlantı için kendi iç ortak anahtar altyapısı (PKI) kök sertifika yetkilinizi (CA) kullanabilirsiniz.

Sertifika kimlik doğrulaması veya PowerShell ile Azure portal kullanarak bir sanal ağa yönelik bir noktadan siteye VPN bağlantısı yapılandırabilirsiniz.

Azure sanal ağlarına Noktadan siteye VPN bağlantıları hakkında daha fazla bilgi edinmek için bkz.:

[Sertifika kimlik doğrulaması kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma: Azure portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Sertifika kimlik doğrulaması kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Siteden siteye VPN 'Ler 

Şirket içi ağınızı bir IPSec/ıKE (IKEv1 veya Ikev2) VPN tüneli üzerinden bir Azure sanal ağına bağlamak için siteden siteye VPN Ağ Geçidi bağlantısı kullanabilirsiniz. Bu tür bir bağlantı, kendisine atanmış bir genel IP adresi olan bir şirket içi VPN cihazı gerektirir.

Azure portal, PowerShell veya Azure CLı kullanarak bir sanal ağa siteden siteye VPN bağlantısı yapılandırabilirsiniz.

Daha fazla bilgi için bkz.

[Azure portal siteden siteye bağlantı oluşturma](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[PowerShell 'de siteden siteye bağlantı oluşturma](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[CLı kullanarak siteden siteye VPN bağlantısı olan bir sanal ağ oluşturma](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Data Lake 'de geçiş içi şifreleme

Data Lake Store'da aktarımdaki (diğer adıyla hareket halindeki) veriler de her zaman şifrelenir. Verileri kalıcı medyada depolamadan önce şifrelemeye ek olarak, verilerin de her zaman HTTPS kullanılarak iletimde güvenliği sağlanır. HTTPS, Data Lake Store REST arabirimleri için desteklenen tek protokoldür.

Data Lake aktarım sırasında verilerin şifrelenmesi hakkında daha fazla bilgi edinmek için bkz. [Data Lake Store verileri şifreleme](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Key Vault ile anahtar yönetimi

Anahtarların düzgün korunması ve yönetilmesi olmadan, şifreleme kullanılamaz şekilde işlenir. Key Vault, bulut Hizmetleri tarafından kullanılan şifreleme anahtarlarına erişimi yönetmeye ve denetlemeye yönelik Microsoft tarafından önerilen çözümdür. Anahtarlara erişim izinleri, Azure Active Directory hesapları aracılığıyla hizmetlere veya kullanıcılara atanabilir.

Donanım güvenlik modüllerini (HSM 'ler) ve anahtar yönetim yazılımını yapılandırma, düzeltme eki uygulama ve bakımını yapma ihtiyacı olan sizi maliyetinden kurtarır kuruluşları Key Vault. Key Vault kullandığınızda denetim devam edersiniz. Microsoft, anahtarlarınızı hiçbir şekilde görmez ve uygulamalar bunlara doğrudan erişemez. Ayrıca, HSM 'lerde de anahtarlar içeri aktarabilir veya oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure güvenliğine genel bakış](get-started-overview.md)
- [Azure ağ güvenliğine genel bakış](network-overview.md)
- [Azure veritabanı güvenliğine genel bakış](database-security-overview.md)
- [Azure sanal makineler güvenliğine genel bakış](virtual-machines-overview.md)
- [Bekleme sırasında veri şifrelemesi](encryption-atrest.md)
- [Veri güvenliği ve şifreleme için en iyi uygulamalar](data-encryption-best-practices.md)
