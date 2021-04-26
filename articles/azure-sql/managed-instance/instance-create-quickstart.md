---
title: 'Hızlı başlangıç: Azure SQL yönetilen örneği oluşturma (portal)'
description: Bu hızlı başlangıçta Azure portal kullanarak erişim için yönetilen bir örnek, ağ ortamı ve istemci VM 'si oluşturun.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: d356cad1b4754875574e19be732fdf6481c61e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101691221"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Hızlı başlangıç: Azure SQL yönetilen örneği oluşturma
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu hızlı başlangıçta Azure portal [Azure SQL yönetilen örneği](sql-managed-instance-paas-overview.md) oluşturma öğretilir.

> [!IMPORTANT]
> Sınırlamalar için bkz. [Desteklenen bölgeler](resource-limits.md#supported-regions) ve [desteklenen Abonelik türleri](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL Yönetilen Örnek oluşturma

SQL yönetilen örneği oluşturmak için aşağıdaki adımları izleyin: 

### <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure portal sol menüsünde **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna **Azure SQL** ' i girin.
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **SQL yönetilen örnekler** kutucuğunda **Ayrıntıları göster** ' i seçerek Azure SQL yönetilen örneği hakkındaki ek bilgileri görüntüleyebilirsiniz.
1. **Oluştur**’u seçin.

   ![Yönetilen örnek oluşturma](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Gerekli ve isteğe bağlı bilgileri eklemek için **Azure SQL yönetilen örnek** sağlama formu 'ndaki sekmeleri kullanın. Aşağıdaki bölümlerde bu sekmeler açıklanır.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

- **Temel** bilgiler sekmesinde gerekli olan zorunlu bilgileri doldurun. Bu, bir SQL yönetilen örneği sağlamak için gereken en düşük bilgi kümesidir.

   ![SQL yönetilen örneği oluşturmaya yönelik "temel bilgiler" sekmesi](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Abonelik** | Aboneliğiniz. | Yeni kaynaklar oluşturmak için size izin veren bir abonelik. |
   | **Kaynak grubu** | Yeni veya mevcut bir kaynak grubu.|Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   | **Yönetilen örnek adı** | Geçerli bir ad.|Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   | **Bölge** |Yönetilen örneği oluşturmak istediğiniz bölge.|Bölgeler hakkında bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).|
   | **Yönetilen örnek yöneticisi oturum açma** | Geçerli bir Kullanıcı adı. | Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). Bu ayrılmış sunucu düzeyi bir rol olduğundan "serveradmin" kullanmayın.|
   | **Parola** | Herhangi bir geçerli parola.| Parola en az 16 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|

- İşlem ve depolama kaynaklarını belirlemek ve fiyatlandırma katmanlarını gözden geçirmek için **yönetilen örneği Yapılandır** ' ı seçin. Depolama miktarını ve sanal çekirdek sayısını belirtmek için kaydırıcıları veya metin çubuklarını kullanın. İşiniz bittiğinde seçiminizi kaydetmek için **Uygula** ' yı seçin. 

   ![Yönetilen örnek formu](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Ayar| Önerilen değer | Açıklama |
| ------ | --------------- | ----------- |
| **Hizmet katmanı** | Seçeneklerden birini belirleyin. | Senaryonuza bağlı olarak, aşağıdaki seçeneklerden birini seçin: </br> <ul><li>**Genel amaçlı**: çoğu üretim iş yükleri ve varsayılan seçenek.</li><li>**İş açısından kritik**: yüksek dayanıklılık ve hızlı yük devretme işlemleri ile düşük gecikmeli iş yükleri için tasarlanmıştır.</li></ul><BR>Daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SQL yönetilen örnek hizmeti katmanları](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) ve [Azure SQL yönetilen örnek kaynak sınırlarına genel bakış](../../azure-sql/managed-instance/resource-limits.md).|
| **Donanım oluşturma** | Seçeneklerden birini belirleyin. | Donanım oluşturma genellikle işlem ve bellek sınırlarını ve iş yükünün performansını etkileyen diğer özellikleri tanımlar. **5. nesil** varsayılandır.|
| **Sanal çekirdek işlem modeli** | Bir seçenek belirleyin. | Sanal çekirdekler, iş yükünüz için her zaman sağlanan işlem kaynaklarının tam miktarını temsil eder. **Sekiz sanal çekirdek** varsayılandır.|
| **GB cinsinden depolama** | Bir seçenek belirleyin. | GB cinsinden depolama boyutu, beklenen veri boyutuna göre ' yi seçin. Şirket içi veya çeşitli bulut platformlarındaki mevcut verileri geçiriyorsanız bkz. [geçişe genel bakış: SQL yönetilen örneği 'ne SQL Server](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Azure Hibrit Avantajı** | Varsa, seçeneğini işaretleyin. | Mevcut bir Azure lisansını kullanmak için. Daha fazla bilgi için bkz. [Azure hibrit avantajı-Azure SQL veritabanı & SQL yönetilen örneği](../../azure-sql/azure-hybrid-benefit.md). |
| **Yedekleme depolama yedekliliği** | **Coğrafi olarak yedekli yedekleme depolaması**' nı seçin. | Yedekleme depolaması için Azure 'da depolama artıklığı. Bu değerin daha sonra değiştirilemeyeceğini unutmayın. Coğrafi olarak yedekli yedekleme depolaması varsayılan ve önerilir, ancak bölge ve yerel artıklık, daha fazla maliyet esnekliği ve tek bölge verilerinin fazlalığına izin verir. Daha fazla bilgi için bkz. [yedekleme depolama artıklığı](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- SQL yönetilen örneği oluşturmadan önce Seçimlerinizi gözden geçirmek için, **gözden geçir + oluştur** seçeneğini belirleyebilirsiniz. Ya da Ileri ' yi seçerek ağ seçeneklerini yapılandırın **: ağ**.

### <a name="networking-tab"></a>Ağ sekmesi

- **Ağ** sekmesinde isteğe bağlı bilgileri doldurun. Bu bilgileri atlarsanız, Portal varsayılan ayarları uygular.

   ![Yönetilen örnek oluşturmak için "ağ" sekmesi](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Sanal ağ** | **Yeni sanal ağ** ya da geçerli bir sanal ağ ve alt ağ oluştur ' u seçin.| Bir ağ veya alt ağ kullanılamaz durumdaysa, yeni yönetilen örnek için bir hedef olarak seçmeden önce [ağ gereksinimlerini karşılamak üzere değiştirilmesi](vnet-existing-add-subnet.md) gerekir. SQL yönetilen örneği için ağ ortamını yapılandırma gereksinimleri hakkında bilgi için bkz. [SQL yönetilen örneği için sanal ağ yapılandırma](connectivity-architecture-overview.md). |
   | **Bağlantı türü** | Proxy ve yeniden yönlendirme bağlantı türü arasında seçim yapın.|Bağlantı türleri hakkında daha fazla bilgi için bkz. [Azure SQL yönetilen örnek bağlantı türü](../database/connectivity-architecture.md#connection-policy).|
   | **Genel uç nokta**  | **Devre dışı bırak** seçeneğini belirleyin. | Yönetilen bir örneğin genel veri uç noktası üzerinden erişilebilir olması için bu seçeneği etkinleştirmeniz gerekir. | 
   | **Erişime Izin ver** ( **genel uç nokta** etkinse) | **Erişim yok** ' u seçin  |Portal deneyimi, bir güvenlik grubunu genel bir uç nokta ile yapılandırmaya izin vermez. </br> </br> Senaryonuza bağlı olarak, aşağıdaki seçeneklerden birini seçin: </br> <ul> <li>**Azure hizmetleri**: Power BI veya başka bir çok kiracılı hizmetten bağlanırken bu seçeneği öneririz. </li> <li> **Internet**: yönetilen bir örneği hızlı bir şekilde çalıştırmak istediğinizde test amaçları için kullanın. Bunu üretim ortamları için önermiyoruz. </li> <li> **Erişim yok**: Bu seçenek bir **reddetme** güvenlik kuralı oluşturur. Yönetilen bir örneği ortak bir uç nokta aracılığıyla erişilebilir hale getirmek için bu kuralı değiştirin. </li> </ul> </br> Genel uç nokta güvenliği hakkında daha fazla bilgi için bkz. [Azure SQL yönetilen örneğini genel bir uç noktayla güvenli bir şekilde kullanma](public-endpoint-overview.md).|

- Yönetilen bir örnek oluşturmadan önce Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Ya da Ileri ' yi seçerek daha fazla özel ayar yapılandırın **: ek ayarlar**.


### <a name="additional-settings"></a>Ek ayarlar

- **Ek ayarlar** sekmesinde isteğe bağlı bilgileri doldurun. Bu bilgileri atlarsanız, Portal varsayılan ayarları uygular.

   ![Yönetilen örnek oluşturmak için "ek ayarlar" sekmesi](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Harmanlama** | Yönetilen örneğiniz için kullanmak istediğiniz harmanlamayı seçin. SQL Server veritabanlarını geçirirseniz, kullanarak kaynak harmanlamasını denetleyin `SELECT SERVERPROPERTY(N'Collation')` ve bu değeri kullanın.| Harmanlamalar hakkında daha fazla bilgi için bkz. [sunucu harmanlamasını ayarlama veya değiştirme](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Saat dilimi** | Yönetilen örneğin gözleneceği saat dilimini seçin.|Daha fazla bilgi için bkz. [saat dilimleri](timezones-overview.md).|
   | **Yük devretme ikincili olarak kullan** | **Evet**’i seçin. | Yönetilen örneği yük devretme grubu ikincil olarak kullanmak için bu seçeneği etkinleştirin.|
   | **BIRINCIL SQL yönetilen örneği** ( **Yük devretme Ikincili olarak kullan** **Evet** olarak ayarlanırsa) | Oluşturmakta olduğunuz yönetilen örnekle aynı DNS bölgesine katılacak mevcut bir birincil yönetilen örnek seçin. | Bu adım, yük devretme grubunun oluşturma sonrası yapılandırmasını etkinleştirecektir. Daha fazla bilgi için bkz. [öğretici: bir yük devretme grubuna yönetilen örnek ekleme](failover-group-add-instance-tutorial.md).|

- Yönetilen bir örnek oluşturmadan önce Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Ya da Ileri ' yi seçerek Azure etiketlerini yapılandırın **: Etiketler** (önerilir).

### <a name="tags"></a>Etiketler

- Azure Resource Manager şablonunuzda kaynaklara Etiketler ekleyin (ARM şablonu). [Etiketler](../../azure-resource-manager/management/tag-resources.md) , kaynaklarınızı mantıksal olarak düzenlemenize yardımcı olur. Etiket değerleri, maliyet raporlarında gösterilir ve diğer yönetim etkinliklerinin etikete göre kullanılmasına izin verir. 

- Kimin oluşturduğunu belirlemek için yeni SQL yönetilen örneğinizi sahip etiketiyle etiketleyerek ve bu sistemin üretim, geliştirme vb. olduğunu belirlemek için ortam etiketinin olduğunu göz önünde bulundurun. Daha fazla bilgi için bkz. [Azure kaynakları için adlandırma ve etiketleme stratejinizi geliştirme](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- Devam etmek için **gözden geçir + oluştur** ' u seçin.

## <a name="review--create"></a>Gözden geçirme ve oluşturma

1. Yönetilen bir örnek oluşturmadan önce Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** sekmesini seçin.

   ![Yönetilen örnek İnceleme ve oluşturma sekmesi](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Yönetilen örneği sağlamaya başlamak için **Oluştur** ' u seçin.

> [!IMPORTANT]
> Yönetilen bir örneği dağıtmak uzun süren bir işlemdir. Alt ağdaki ilk Örneğin dağıtımı, genellikle mevcut yönetilen örneklerle bir alt ağa dağıtmaya kıyasla çok daha uzun sürer. Ortalama sağlama süreleri için bkz. [Azure SQL yönetilen örnek yönetimi Işlemlerine genel bakış](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Dağıtım ilerlemesini izleme

1. Dağıtım durumunu görüntülemek için **Bildirimler** simgesini seçin.

   ![SQL yönetilen örnek dağıtımının dağıtım ilerlemesi](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. SQL yönetilen örnek penceresini açmak ve dağıtım ilerlemesini izlemek için bildirimde **devam etmekte olan dağıtım '** ı seçin. 

> [!TIP]
> - Web tarayıcınızı kapattıysanız veya dağıtım ilerleme ekranından uzaklaşmanız durumunda, yönetilen örneğin **genel bakış** sayfası veya PowerShell veya Azure CLI aracılığıyla sağlama işlemini izleyebilirsiniz. Daha fazla bilgi için bkz. [izleme işlemleri](management-operations-monitor.md#monitor-operations). 
> - Hazırlama işlemini Azure portal aracılığıyla veya PowerShell veya Azure CLı ya da REST API kullanarak başka bir araç aracılığıyla iptal edebilirsiniz. Bkz. [Azure SQL yönetilen örnek yönetimi Işlemlerini Iptal etme](management-operations-cancel.md).

> [!IMPORTANT]
> - Aynı alt ağdaki diğer yönetilen örneklerde uzun süre çalışan geri yükleme veya ölçeklendirme işlemleri olduğunda, SQL yönetilen örnek oluşturma 'nın başlatılması, diğer etkileyen işlemler varsa geciktirilebilir. Daha fazla bilgi için bkz. [yönetim işlemleri çapraz etkisi](management-operations-overview.md#management-operations-cross-impact).
> - Yönetilen örnek oluşturma durumunu alabilmeniz için, kaynak grubu üzerinde **Okuma izinlerine** sahip olmanız gerekir. Yönetilen örnek oluşturma işlemi sırasında bu izne sahip değilseniz veya dosyayı iptal ederseniz, bu, SQL yönetilen örneğinin kaynak grubu dağıtımları listesinde görünmemesine neden olabilir.
>

## <a name="view-resources-created"></a>Oluşturulan kaynakları görüntüle

Yönetilen bir örneğin başarıyla dağıtılması sırasında, oluşturulan kaynakları görüntülemek için:

1. Yönetilen örneğiniz için kaynak grubunu açın. 

   ![SQL yönetilen örnek kaynakları](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Ağ ayarlarını görüntüleme ve üzerinde ayarlama

İsteğe bağlı olarak ağ ayarlarını ince ayar yapmak için aşağıdakileri inceleyin:

1. Kaynak listesinde, oluşturulan kullanıcı tanımlı yol tablosu (UDR) nesnesini gözden geçirmek için yol tablosunu seçin.

2. Yönlendirme tablosunda, SQL yönetilen örnek sanal ağının içinden ve içinden trafiği yönlendirmek için girişleri gözden geçirin. Yönlendirme tablonuzu el ile oluşturur veya yapılandırırsanız, bu girdileri SQL yönetilen örnek yolu tablosunda oluşturun.

   ![Yerel olarak bir SQL yönetilen örnek alt ağı girişi](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Yolları değiştirmek veya eklemek için rota tablosu ayarlarında **yolları** açın.

3. Kaynak grubuna geri dönün ve oluşturulan ağ güvenlik grubu (NSG) nesnesini seçin.

4. Gelen ve giden güvenlik kurallarını gözden geçirin. 

   ![Güvenlik kuralları](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Kuralları değiştirmek veya eklemek için, ağ güvenlik grubu ayarlarında **gelen güvenlik kurallarını** ve **giden güvenlik kurallarını** açın.

> [!IMPORTANT]
> SQL yönetilen örneği için genel bir uç nokta yapılandırdıysanız, genel İnternet 'ten SQL yönetilen örneği bağlantılarına izin vermek için bağlantı noktalarını açmanız gerekir. Daha fazla bilgi için bkz. [SQL yönetilen örneği için genel uç nokta yapılandırma](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>SQL Yönetilen Örneği'ne bağlantı ayrıntılarını alma

SQL yönetilen örneğine bağlanmak için, ana bilgisayar adını ve tam etki alanı adını (FQDN) almak için aşağıdaki adımları izleyin:

1. Kaynak grubuna dönün ve oluşturulan SQL yönetilen örnek nesnesini seçin.

2. **Genel bakış** sekmesinde **ana bilgisayar** özelliğini bulun. Bir sonraki hızlı başlangıçta **Panoya Kopyala** düğmesine tıklayarak, yönetilen örnek için Pano 'ya ana bilgisayar adını kopyalayın.

   ![Konak adı](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   Kopyalanmış değer SQL yönetilen örneğine bağlanmak için kullanılabilecek bir tam etki alanı adı (FQDN) temsil eder. Şu adrese benzer bir örnek: *your_host_name. a1b2c3d4e5f6. Database. Windows. net*.

## <a name="next-steps"></a>Sonraki adımlar

SQL yönetilen örneği 'ne nasıl bağlanacağınızı öğrenmek için:
- Uygulamalar için bağlantı seçeneklerine genel bakış için bkz. [UYGULAMALARıNıZı SQL yönetilen örneğine bağlama](connect-application-instance.md).
- Azure sanal makinesinden SQL yönetilen örneği 'ne bağlanmayı gösteren bir hızlı başlangıç için bkz. [Azure sanal makine bağlantısını yapılandırma](connect-vm-instance-configure.md).
- Noktadan siteye bağlantı kullanarak bir şirket içi istemci bilgisayarından SQL yönetilen örneği 'ne bağlanmayı gösteren bir hızlı başlangıç için bkz. [Noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md).

Mevcut bir SQL Server veritabanını Şirket içinden SQL yönetilen örneği 'ne geri yüklemek için: 
- [Geçiş Için Azure veritabanı geçiş hizmeti](../../dms/tutorial-sql-server-to-managed-instance.md) 'ni kullanarak bir veritabanı yedekleme dosyasından geri yükleme yapın. 
- Bir veritabanı yedekleme dosyasından geri yüklemek için [T-SQL restore komutunu](restore-sample-database-quickstart.md) kullanın.

Yerleşik sorun giderme zekası ile SQL yönetilen örnek veritabanı performansının gelişmiş izlenmesi için, bkz. [Azure SQL Analytics kullanarak Azure SQL yönetilen örneğini izleme](../../azure-monitor/insights/azure-sql.md).