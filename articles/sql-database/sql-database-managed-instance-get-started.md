---
title: 'Azure portal: yönetilen örnek oluşturma'
description: Erişim için bir SQL veritabanı yönetilen örneği, ağ ortamı ve istemci VM 'si oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 2af153e5cf09cf331be016066e9c9210a0d39b03
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838171"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Hızlı başlangıç: Azure SQL veritabanı yönetilen örneği oluşturma

Bu hızlı başlangıç, Azure portal içinde Azure SQL veritabanı [yönetilen örneği](sql-database-managed-instance.md) oluşturma konusunda size yol gösterir.

> [!IMPORTANT]
> Sınırlamalar için bkz. [Desteklenen bölgeler](sql-database-managed-instance-resource-limits.md#supported-regions) ve [desteklenen Abonelik türleri](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Azure portalda oturum açın

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

[Azure portalda](https://portal.azure.com/) oturum açın.

## <a name="create-a-managed-instance"></a>Yönetilen örnek oluşturma

Aşağıdaki adımlarda, yönetilen bir örnek oluşturma adımları gösterilmektedir:

1. Azure portal sol menüsünde **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna **Azure SQL** ' i girin.
2. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Yönetilen örnekler** kutucuğunda **Ayrıntıları göster** ' i seçerek Azure SQL veritabanı yönetilen örneği hakkındaki ek bilgileri görüntüleyebilirsiniz.
3. **Oluştur**'u seçin.

   ![Yönetilen örnek oluşturma](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Gerekli ve isteğe bağlı bilgileri eklemek için **Azure SQL veritabanı yönetilen örnek** sağlama formu 'ndaki sekmeleri kullanın. Aşağıdaki bölümlerde bu sekmeler açıklanır.

### <a name="basics"></a>Temel Bilgiler

- **Temel** bilgiler sekmesinde gerekli olan zorunlu bilgileri doldurun. Bu, yönetilen bir örnek sağlamak için gereken en düşük bilgi kümesidir.

   ![Yönetilen örnek oluşturmak için "temel bilgiler" sekmesi](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Abonelik** | Aboneliğiniz. | Yeni kaynaklar oluşturmak için size izin veren bir abonelik. |
   | **Kaynak grubu** | Yeni veya mevcut bir kaynak grubu.|Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   | **Yönetilen örnek adı** | Geçerli bir ad.|Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   | **Bölge** |Yönetilen örneği oluşturmak istediğiniz bölge.|Bölgeler hakkında bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).|
   | **Yönetilen örnek yöneticisi oturum açma** | Geçerli bir Kullanıcı adı. | Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). Bu ayrılmış sunucu düzeyi bir rol olduğundan "serveradmin" kullanmayın.|
   | **Parola** | Herhangi bir geçerli parola.| Parola en az 16 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|

- İşlem ve depolama kaynaklarını belirlemek ve fiyatlandırma katmanlarını gözden geçirmek için **yönetilen örneği Yapılandır** ' ı seçin. Depolama miktarını ve sanal çekirdek sayısını belirtmek için kaydırıcıları veya metin çubuklarını kullanın. İşiniz bittiğinde seçiminizi kaydetmek için **Uygula** ' yı seçin. 

   ![Yönetilen örnek formu](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Yönetilen bir örnek oluşturmadan önce Seçimlerinizi gözden geçirmek için, **gözden geçir + oluştur**seçeneğini belirleyebilirsiniz. Ya da Ileri ' yi seçerek ağ seçeneklerini yapılandırın **: ağ**.

### <a name="networking"></a>Ağ

- **Ağ** sekmesinde isteğe bağlı bilgileri doldurun. Bu bilgileri atlarsanız, Portal varsayılan ayarları uygular.

   ![Yönetilen örnek oluşturmak için "ağ" sekmesi](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Sanal ağ** | **Yeni sanal ağ** ya da geçerli bir sanal ağ ve alt ağ oluştur ' u seçin.| Bir ağ veya alt ağ kullanılamaz durumdaysa, yeni yönetilen örnek için bir hedef olarak seçmeden önce [ağ gereksinimlerini karşılamak üzere değiştirilmesi](sql-database-managed-instance-configure-vnet-subnet.md) gerekir. Yönetilen bir örnek için ağ ortamını yapılandırma gereksinimleri hakkında bilgi için bkz. [yönetilen bir örnek için sanal ağ yapılandırma](sql-database-managed-instance-connectivity-architecture.md). |
   | **Bağlantı türü** | Proxy ve yeniden yönlendirme bağlantı türü arasında seçim yapın.|Bağlantı türleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı bağlantı ilkesi](sql-database-connectivity-architecture.md#connection-policy).|
   | **Ortak uç nokta**  | **Etkinleştir**’i seçin. | Yönetilen bir örneğin genel veri uç noktası üzerinden erişilebilir olması için bu seçeneği etkinleştirmeniz gerekir. | 
   | **Erişime Izin ver** ( **genel uç nokta** etkinse) | Seçeneklerden birini belirleyin.   |Portal deneyimi, bir güvenlik grubunu genel bir uç nokta ile yapılandırmaya izin vermez. </br> </br> Senaryonuza bağlı olarak, aşağıdaki seçeneklerden birini seçin: </br> <ul> <li>**Azure hizmetleri**: Power BI veya başka bir çok kiracılı hizmetten bağlanırken bu seçeneği öneririz. </li> <li> **Internet**: yönetilen bir örneği hızlı bir şekilde çalıştırmak istediğinizde test amaçları için kullanın. Bunu üretim ortamları için önermiyoruz. </li> <li> **Erişim yok**: Bu seçenek bir **reddetme** güvenlik kuralı oluşturur. Yönetilen bir örneği ortak bir uç nokta aracılığıyla erişilebilir hale getirmek için bu kuralı değiştirin. </li> </ul> </br> Genel uç nokta güvenliği hakkında daha fazla bilgi için, bkz. [genel bir uç noktayla güvenli bir Azure SQL veritabanı yönetilen örneği kullanma](sql-database-managed-instance-public-endpoint-securely.md).|

- Yönetilen bir örnek oluşturmadan önce Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Ya da Ileri ' yi seçerek daha fazla özel ayar yapılandırın **: ek ayarlar**.

### <a name="additional-settings"></a>Ek ayarlar

- **Ek ayarlar** sekmesinde isteğe bağlı bilgileri doldurun. Bu bilgileri atlarsanız, Portal varsayılan ayarları uygular.

   ![Yönetilen örnek oluşturmak için "ek ayarlar" sekmesi](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Bu sekmede gereken bilgiler için bir başvuru olarak aşağıdaki tabloyu kullanın.

   | Ayar| Önerilen değer | Açıklama |
   | ------ | --------------- | ----------- |
   | **Mediğinden** | Yönetilen örneğiniz için kullanmak istediğiniz harmanlamayı seçin. SQL Server veritabanlarını geçirirseniz, `SELECT SERVERPROPERTY(N'Collation')` kullanarak kaynak harmanlamasını denetleyin ve bu değeri kullanın.| Harmanlamalar hakkında daha fazla bilgi için bkz. [sunucu harmanlamasını ayarlama veya değiştirme](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Saat dilimi** | Yönetilen örneğinizin gözleneceği saat dilimini seçin.|Daha fazla bilgi için bkz. [saat dilimleri](sql-database-managed-instance-timezone.md).|
   | **Yük devretme ikincili olarak kullan** | **Evet**' i seçin. | Yönetilen örneği yük devretme grubu ikincil olarak kullanmak için bu seçeneği etkinleştirin.|
   | **Birincil yönetilen örnek** ( **Yük devretme Ikincili olarak kullan** **Evet**olarak ayarlanırsa) | Oluşturmakta olduğunuz yönetilen örnekle aynı DNS bölgesine katılacak mevcut bir birincil yönetilen örnek seçin. | Bu adım, yük devretme grubunun oluşturma sonrası yapılandırmasını etkinleştirecektir. Daha fazla bilgi için bkz. [öğretici: bir yük devretme grubuna SQL veritabanı yönetilen örneği ekleme](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Gözden geçir + oluştur

5. Yönetilen örneği oluşturmadan önce Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** sekmesini seçin.

   ![Yönetilen örnek İnceleme ve oluşturma sekmesi](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Yönetilen örneği sağlamaya başlamak için **Oluştur** ' u seçin.

> [!IMPORTANT]
> Yönetilen bir örneği dağıtmak uzun süren bir işlemdir. Alt ağdaki ilk Örneğin dağıtımı, genellikle mevcut yönetilen örneklerle bir alt ağa dağıtmaya kıyasla çok daha uzun sürer. Ortalama sağlama süreleri için bkz. [yönetilen örnek yönetimi işlemleri](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Dağıtım ilerlemesini izleme

7. Dağıtım durumunu görüntülemek için **Bildirimler** simgesini seçin.

   ![Yönetilen örnek dağıtımının dağıtım ilerlemesi](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Yönetilen örnek penceresini açmak ve dağıtım ilerlemesini izlemek için bildirimde **devam etmekte olan dağıtım '** ı seçin. 

> [!TIP]
> Web tarayıcınızı kapattıysanız veya dağıtım ilerleme ekranından uzaklaşmanız durumunda dağıtım ilerleme durumunu geri yüklemek için şu adımları izleyin:
> 1. Azure portal, yönetilen bir örnek dağıttığınız kaynak grubunu ( **temel bilgiler** sekmesinde) açın.
> 2. **Dağıtımlar**' ı seçin.
> 3. Devam eden yönetilen örnek dağıtım işlemini seçin.

## <a name="post-deployment-operations"></a>Dağıtım sonrası işlemler

Oluşturulan kaynakları gözden geçirmek için ağ ayarlarını hassas ayarlama ve konak bağlantısı ayrıntılarını alma (FQDN) Bu bölümde açıklanan adımları izleyin.

### <a name="view-resources-created"></a>Oluşturulan kaynakları görüntüle

Yönetilen örnek başarıyla dağıtımı sırasında, oluşturulan kaynakları görüntülemek için:

1. Yönetilen örneğiniz için kaynak grubunu açın. [Yönetilen örnek oluşturma](#create-a-managed-instance) hızlı başlangıç bölümünde sizin için oluşturulan kaynaklarını görüntüleyin.

   ![Yönetilen örnek kaynakları](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Ağ ayarlarını görüntüleme ve üzerinde ayarlama

İsteğe bağlı olarak ağ ayarlarını ince ayar yapmak için aşağıdakileri inceleyin:

1. Sizin için oluşturulan kullanıcı tanımlı yolu (UDR) gözden geçirmek için yol tablosunu seçin.

   ![Yol tablosu](./media/sql-database-managed-instance-get-started/route-table.png)

2. Yönlendirme tablosunda, yönetilen örneğin sanal ağından gelen ve içindeki trafiği yönlendirmek için girişleri gözden geçirin. Yönlendirme tablonuzu el ile oluşturur veya yapılandırırsanız, bu girdileri yönetilen örnek yolu tablosunda oluşturduğunuzdan emin olun.

   ![Yönetilen örnek alt ağını yerel olarak giriş](./media/sql-database-managed-instance-get-started/udr.png)

3. Kaynak grubuna geri dönün ve ağ güvenlik grubunu seçin.

   ![Ağ güvenlik grubu](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Gelen ve giden güvenlik kurallarını gözden geçirin. 

   ![Güvenlik kuralları](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Yönetilen örneğiniz için ortak uç nokta yapılandırdıysanız, ağ trafiğinin genel Internet 'ten yönetilen örneğe bağlantılara izin vermesini sağlamak için bağlantı noktalarını açmanız gerekir. daha fazla bilgi için bkz. [yönetilen örnek için ortak uç nokta yapılandırma](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Yönetilen örneğe bağlantı ayrıntılarını al

Yönetilen örneğe bağlanmak için, ana bilgisayar adı ve tam etki alanı adı (FQDN) almak için aşağıdaki adımları izleyin:

1. Kaynak grubuna dönün ve yönetilen örneğinizi seçin.

   ![Kaynak grubunda yönetilen örnek](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. **Genel bakış** sekmesinde **ana bilgisayar** özelliğini bulun. Yönetilen örnek için konak adını sonraki hızlı başlangıçta kullanılmak üzere kopyalayın.

   ![Ana bilgisayar adı](./media/sql-database-managed-instance-get-started/host-name.png)

   Kopyalanmış değer, yönetilen örneğe bağlanmak için kullanılabilecek bir tam etki alanı adı (FQDN) temsil eder. Şu adrese benzer bir örnek: *your_host_name. a1b2c3d4e5f6. Database. Windows. net*.

## <a name="next-steps"></a>Sonraki adımlar

Yönetilen bir örneğe nasıl bağlanacağınızı öğrenmek için:
- Uygulamalar için bağlantı seçeneklerine genel bakış için bkz. [uygulamalarınızı yönetilen bir örneğe bağlama](sql-database-managed-instance-connect-app.md).
- Azure sanal makinesinden yönetilen bir örneğe bağlanmayı gösteren bir hızlı başlangıç için bkz. [Azure sanal makine bağlantısını yapılandırma](sql-database-managed-instance-configure-vm.md).
- Noktadan siteye bağlantı kullanarak bir şirket içi istemci bilgisayarından yönetilen bir örneğe bağlanmayı gösteren hızlı başlangıç için bkz. [Noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md).

Mevcut bir SQL Server veritabanını Şirket içinden yönetilen bir örneğe geri yüklemek için: 
- [Geçiş Için Azure veritabanı geçiş hizmeti](../dms/tutorial-sql-server-to-managed-instance.md) 'ni kullanarak bir veritabanı yedekleme dosyasından geri yükleme yapın. 
- Bir veritabanı yedekleme dosyasından geri yüklemek için [T-SQL restore komutunu](sql-database-managed-instance-get-started-restore.md) kullanın.

Yerleşik sorun giderme zekası ile yönetilen örnek veritabanı performansının gelişmiş izlenmesi için, bkz. [Azure SQL Analytics kullanarak Azure SQL veritabanını izleme](../azure-monitor/insights/azure-sql.md).
