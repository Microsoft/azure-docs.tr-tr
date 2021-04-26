---
title: Veritabanını güvenli hale getirme
description: Bu öğretici, bir Azure SQL veritabanının güvenliğini sağlamaya yönelik teknikler ve özellikler hakkında bilgi sağlar. Bu, tek bir veritabanı veya havuza alınmış olsun.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 306a8d78caf36291573d021ed1d814aa4f7a2164
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642781"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Öğretici: Azure SQL veritabanında bir veritabanını güvenli hale getirme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
>
> - Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları oluşturma
> - Azure Active Directory (Azure AD) yöneticisini yapılandırma
> - SQL kimlik doğrulaması, Azure AD kimlik doğrulaması ve güvenli bağlantı dizeleri ile Kullanıcı erişimini yönetme
> - SQL için Azure Defender, denetim, veri maskeleme ve şifreleme gibi güvenlik özelliklerini etkinleştirin

Azure SQL veritabanı, aşağıdakileri sağlayarak verilerinizi güvenli bir şekilde sağlar:

- Güvenlik duvarı kurallarını kullanarak erişimi sınırlama
- Kimlik gerektiren kimlik doğrulama mekanizmalarını kullanın
- Rol tabanlı Üyelikler ve izinlerle yetkilendirmeyi kullanma
- Güvenlik özelliklerini etkinleştir

> [!NOTE]
> Azure SQL yönetilen örneği, [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md) ve [bağlantı mimarisi](../managed-instance/connectivity-architecture-overview.md)' nde açıklandığı gibi ağ güvenlik kuralları ve özel uç noktalar kullanılarak güvenliği sağlanmış olur.

Daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı güvenliğine genel bakış](./security-overview.md) ve [yetenekler](security-overview.md) makaleleri.

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, [Azure SQL veritabanı 'nda veritabanınızın güvenliğini sağlama](/learn/modules/secure-your-azure-sql-database/)hakkında bilgi edinmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- [Sunucu](logical-servers.md) ve tek veritabanı
  - [Azure Portal](single-database-create-quickstart.md), [CLI](az-cli-script-samples-content-guide.md)veya [PowerShell](powershell-script-content-guide.md) ile oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Öğreticideki tüm adımlar için [Azure Portal](https://portal.azure.com/) oturum açın

## <a name="create-firewall-rules"></a>Güvenlik duvarı kuralları oluşturma

SQL veritabanındaki veritabanları, Azure 'daki güvenlik duvarları tarafından korunmaktadır. Varsayılan olarak, sunucu ve veritabanına yönelik tüm bağlantılar reddedilir. Daha fazla bilgi için bkz. [sunucu düzeyi ve veritabanı düzeyinde güvenlik duvarı kuralları](firewall-configure.md).

En güvenli yapılandırma için **Azure hizmetlerine erişime Izin ver** ' i **kapalı** olarak ayarlayın. Ardından, bağlanması gereken kaynak için bir Azure VM veya bulut hizmeti gibi [ayrılmış BIR IP (klasik dağıtım)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) oluşturun ve yalnızca bu IP adresinin güvenlik duvarı üzerinden erişimine izin verin. [Kaynak Yöneticisi](../../virtual-network/public-ip-addresses.md) dağıtım modelini kullanıyorsanız, her kaynak için adanmış BIR genel IP adresi gereklidir.

> [!NOTE]
> SQL Veritabanı 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, yöneticiniz 1433 numaralı bağlantı noktasını açmadığı takdirde sunucuya bağlanamazsınız.

### <a name="set-up-server-level-firewall-rules"></a>Sunucu düzeyinde güvenlik duvarı kuralları ayarlama

Sunucu düzeyinde IP güvenlik duvarı kuralları aynı sunucu içindeki tüm veritabanlarına uygulanır.

Sunucu düzeyinde bir güvenlik duvarı kuralı kurmak için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

    ![sunucu güvenlik duvarı kuralı](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Öğreticide daha sonra kullanmak üzere tam sunucu adını (örneğin, *yourserver.Database.Windows.net*) kopyalamaya dikkat edin.

1. **Genel bakış** sayfasında **sunucu güvenlik duvarını ayarla**' yı seçin. Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

   1. Geçerli IP adresinizi yeni bir güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin. Kural tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir. **Kaydet**’i seçin.

      ![sunucu güvenlik duvarı kuralı ayarla](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. **Tamam** ' ı seçin ve **güvenlik duvarı ayarları** sayfasını kapatın.

Artık sunucuda belirtilen IP adresine veya IP adresi aralığına sahip herhangi bir veritabanına bağlanabilirsiniz.

### <a name="setup-database-firewall-rules"></a>Kurulum veritabanı güvenlik duvarı kuralları

Veritabanı düzeyinde güvenlik duvarı kuralları yalnızca ayrı veritabanları için geçerlidir. Veritabanı, sunucu yük devretmesi sırasında bu kuralları korur. Veritabanı düzeyinde güvenlik duvarı kuralları yalnızca Transact-SQL (T-SQL) deyimleri kullanılarak ve yalnızca sunucu düzeyinde bir güvenlik duvarı kuralı yapılandırdıktan sonra yapılandırılabilir.

Veritabanı düzeyinde bir güvenlik duvarı kuralı kurmak için:

1. Veritabanına bağlanın, örneğin [SQL Server Management Studio](connect-query-ssms.md)kullanarak.

1. **Nesne Gezgini**, veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Sorgu penceresinde, bu ifadeyi ekleyin ve IP adresini genel IP adresiniz olarak değiştirin:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Araç çubuğundan **Çalıştır** ' ı seçerek güvenlik duvarı kuralı oluşturun.

> [!NOTE]
> Ayrıca, SSMS 'de [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current&preserve-view=true) komutunu kullanarak sunucu düzeyinde bir güvenlik duvarı kuralı oluşturabilirsiniz, ancak *ana* veritabanına bağlı olmanız gerekir.

## <a name="create-an-azure-ad-admin"></a>Azure AD Yöneticisi oluşturma

Uygun Azure Active Directory (AD) yönetilen etki alanını kullandığınızdan emin olun. AD etki alanını seçmek için Azure portal sağ üst köşesini kullanın. Bu işlem, hem Azure AD hem de veritabanınızı veya veri Ambarınızı barındıran mantıksal SQL Server için aynı aboneliğin kullanıldığını onaylar.

   ![ad seçin](./media/secure-database-tutorial/8choose-ad.png)

Azure AD yöneticisi 'ni ayarlamak için:

1. Azure portal, **SQL Server** sayfasında **Active Directory yönetici**' yi seçin. Sonra **yöneticiyi ayarla**' yı seçin.

    ![active directory seçme](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Bu görevi gerçekleştirmek için "genel yönetici" olmanız gerekir.

1. **Yönetici Ekle** SAYFASıNDA, ad kullanıcısını veya grubunu arayıp seçin ve **Seç**' i seçin. Active Directory tüm Üyeler ve gruplar listelenir ve gri renkte olan girişler Azure AD yöneticileri olarak desteklenmez. Bkz. [Azure AD özellikleri ve sınırlamaları](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![Yönetici seçin](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Azure rol tabanlı erişim denetimi (Azure RBAC) yalnızca portala uygulanır ve SQL Server yayılmaz.

1. **Active Directory Yöneticisi** sayfasının en üstünde **Kaydet**' i seçin.

    Bir yöneticiyi değiştirme işlemi birkaç dakika sürebilir. Yeni yönetici **Active Directory yönetici** kutusunda görünür.

> [!NOTE]
> Bir Azure AD yöneticisi ayarlanırken yeni yönetici adı (Kullanıcı veya grup) *ana* veritabanında SQL Server oturum açma veya Kullanıcı olarak bulunamaz. Varsa, kurulum başarısız olur ve değişiklikleri geri alarak bu tür yönetici adının zaten var olduğunu gösterir. SQL Server oturum açma veya Kullanıcı Azure AD 'nin bir parçası olmadığından, kullanıcıyı Azure AD kimlik doğrulaması kullanarak bağlama çabaları başarısız olur.

Azure AD 'yi yapılandırma hakkında daha fazla bilgi için bkz.

- [Şirket içi kimliklerinizi Azure AD ile tümleştirin](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Azure AD 'ye kendi etki alanı adınızı ekleyin](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure artık Windows Server AD ile Federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Azure AD dizinini yönetme](../../active-directory/fundamentals/active-directory-whatis.md)
- [PowerShell kullanarak Azure AD 'yi yönetme](/powershell/azure/)
- [Karma kimlik için gerekli bağlantı noktaları ve protokoller](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Veritabanı erişimini yönetme

Veritabanına Kullanıcı ekleyerek veya güvenli bağlantı dizeleriyle Kullanıcı erişimine izin vererek veritabanı erişimini yönetin. Bağlantı dizeleri, dış uygulamalar için faydalıdır. Daha fazla bilgi için bkz. [oturum açma bilgilerini ve Kullanıcı hesaplarını](logins-create-manage.md) ve [ad kimlik doğrulamasını](authentication-aad-overview.md)yönetme.

Kullanıcıları eklemek için veritabanı kimlik doğrulaması türünü seçin:

- **SQL kimlik doğrulaması**, oturum açma işlemleri için bir Kullanıcı adı ve parola kullanın ve yalnızca sunucu içindeki belirli bir veritabanı bağlamında geçerlidir

- **Azure AD kimlik doğrulaması**, Azure AD tarafından yönetilen kimlikleri kullanma

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

SQL kimlik doğrulaması ile bir kullanıcı eklemek için:

1. Veritabanına bağlanın, örneğin [SQL Server Management Studio](connect-query-ssms.md)kullanarak.

1. **Nesne Gezgini**, veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Sorgu penceresinde, aşağıdaki komutu girin:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Araç çubuğunda, Kullanıcı oluşturmak için **Yürüt** ' ü seçin.

1. Varsayılan olarak, kullanıcı veritabanına bağlanabilir ancak verileri okuma veya yazma izni yoktur. Bu izinleri vermek için, yeni bir sorgu penceresinde aşağıdaki komutları yürütün:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Yeni kullanıcılar oluşturma gibi yönetici görevleri yürütmelerine gerek olmadığı takdirde, veritabanı düzeyinde yönetici olmayan hesaplar oluşturun.

### <a name="azure-ad-authentication"></a>Azure AD kimlik doğrulaması

Azure Active Directory kimlik doğrulaması, veritabanı kullanıcılarının kapsanan olarak oluşturulmasını gerektirir. Kapsanan bir veritabanı kullanıcısı, veritabanıyla ilişkili Azure AD dizinindeki bir kimlikle eşlenir ve *ana* veritabanında oturum açma bilgileri yoktur. Azure AD kimliği, tek bir kullanıcı veya grup için olabilir. Daha fazla bilgi için bkz. [Kapsanan Veritabanı kullanıcıları, veritabanınızı taşınabilir hale getirme](/sql/relational-databases/security/contained-database-users-making-your-database-portable) ve Azure ad kullanarak kimlik doğrulaması yapma hakkında [Azure AD öğreticisini](authentication-aad-configure.md) İnceleme.

> [!NOTE]
> Veritabanı kullanıcıları (Yöneticiler hariç) Azure portal kullanılarak oluşturulamaz. Azure rolleri SQL Server, veritabanlarına veya veri ambarlarına yayılmaz. Bunlar yalnızca Azure kaynaklarını yönetmek için kullanılır ve veritabanı izinleri için uygulanmaz.
>
> Örneğin, *SQL Server katkıda bulunan* rolü bir veritabanına veya veri ambarına bağlanmak için erişim vermez. Bu izin, T-SQL deyimleri kullanılarak veritabanı içinde verilmelidir.

> [!IMPORTANT]
> `:` `&` T-SQL `CREATE LOGIN` ve deyimlerdeki kullanıcı adlarında iki nokta veya ampersan gibi özel karakterler desteklenmez `CREATE USER` .

Azure AD kimlik doğrulamasıyla bir kullanıcı eklemek için:

1. En azından *alter any user* iznine sahip BIR Azure AD hesabı kullanarak Azure 'daki sunucunuza bağlanın.

1. **Nesne Gezgini**, veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Sorgu penceresinde, aşağıdaki komutu girin ve Azure AD `<Azure_AD_principal_name>` kullanıcısının asıl adına veya Azure AD grubunun görünen adına değiştirin:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD kullanıcıları, `E (EXTERNAL_USER)` gruplar için tür ve tür ile veritabanı meta verilerinde işaretlenir `X (EXTERNAL_GROUPS)` . Daha fazla bilgi için bkz. [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Güvenli bağlantı dizeleri

İstemci uygulaması ile SQL veritabanı arasında güvenli, şifreli bir bağlantı sağlamak için bir bağlantı dizesinin yapılandırılması gerekir:

- Şifrelenmiş bir bağlantı isteyin
- Sunucu sertifikasına güvenmiyor

Bağlantı, Aktarım Katmanı Güvenliği (TLS) kullanılarak oluşturulur ve ortadaki adam saldırısı riskini azaltır. Bağlantı dizeleri veritabanı başına kullanılabilir ve ADO.NET, JDBC, ODBC ve PHP gibi istemci sürücülerini destekleyecek şekilde önceden yapılandırılmıştır. TLS ve bağlantı hakkında daha fazla bilgi için bkz. [TLS konuları](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Güvenli bir bağlantı dizesini kopyalamak için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Genel bakış** sayfasında, **veritabanı bağlantı dizelerini göster**' i seçin.

1. Bir sürücü sekmesi seçin ve tüm bağlantı dizesini kopyalayın.

    ![ADO.NET bağlantı dizesi](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Güvenlik özelliklerini etkinleştir

Azure SQL veritabanı, Azure portal kullanılarak erişilen güvenlik özellikleri sağlar. Bu özellikler, yalnızca veritabanında bulunan veri maskeleme hariç olmak üzere hem veritabanı hem de sunucu için kullanılabilir. Daha fazla bilgi edinmek için bkz. [SQL Için Azure Defender](azure-defender-for-sql.md), [Denetim](../../azure-sql/database/auditing-overview.md), [dinamik veri maskeleme](dynamic-data-masking-overview.md)ve [Saydam veri şifrelemesi](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>SQL için Azure Defender

SQL için Azure Defender özelliği, meydana gelebilecek olası tehditleri algılar ve anormal etkinliklerde güvenlik uyarıları sağlar. Kullanıcılar, denetim özelliğini kullanarak bu şüpheli olayları keşfedebilir ve olayın veritabanındaki verilere erişip erişmediğine, ihlalin veya açıktan yararlanıp yararlanmadığını belirleyebilir. Kullanıcılara ayrıca bir güvenlik açığı değerlendirmesi ve veri bulma ve sınıflandırma aracı içeren bir güvenlik Özeti sağlanır.

> [!NOTE]
> Örnek bir tehdit, saldırganların uygulama girdilerine kötü amaçlı SQL eklemesine neden olan SQL ekleme işlemidir. Bir uygulama daha sonra kötü amaçlı bir SQL 'i açabilir ve saldırganlar 'in veritabanındaki verileri ihlal etmek ya da değiştirmek için erişimine izin verebilir.

SQL için Azure Defender 'ı etkinleştirmek için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Genel bakış** sayfasında **sunucu adı** bağlantısını seçin. Sunucu sayfası açılır.

1. **SQL Server** sayfasında **güvenlik** bölümünü bulun ve **Güvenlik Merkezi**' ni seçin.

   1. Özelliği etkinleştirmek için **SQL Için Azure Defender** **altında öğesini** seçin. Güvenlik açığı değerlendirmesi sonuçlarının kaydedileceği bir depolama hesabı seçin. Sonra **Kaydet**'i seçin.

      ![Gezinti bölmesi](./media/secure-database-tutorial/threat-settings.png)

      Ayrıca, güvenlik uyarılarını, depolama ayrıntılarını ve tehdit algılama türlerini almak için e-postaları yapılandırabilirsiniz.

1. Veritabanınızın **SQL veritabanları** sayfasına dönün ve **güvenlik** bölümünde güvenlik **Merkezi** ' ni seçin. Burada, veritabanı için kullanılabilen çeşitli güvenlik göstergeleri bulacaksınız.

    ![Tehdit durumu](./media/secure-database-tutorial/threat-status.png)

Anormal etkinlikler algılanırsa, olayla ilgili bilgileri içeren bir e-posta alırsınız. Bu, etkinlik, veritabanı, sunucu, olay süresi, olası nedenler ve olası tehdidi araştırmak ve azaltmak için önerilen eylemlerin yapısını içerir. Böyle bir e-posta alındığında, Azure portal başlatmak ve olay saati için ilgili denetim kayıtlarını göstermek üzere **Azure SQL denetim günlüğü** bağlantısını seçin.

   ![Tehdit algılama e-postası](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Denetim

Denetim özelliği, veritabanı olaylarını izler ve olayları Azure depolama, Azure Izleyici günlükleri veya bir olay hub 'ında bir denetim günlüğüne yazar. Denetim mevzuatla uyumluluğu korumaya, veritabanı etkinliğini anlamaya ve olası güvenlik ihlallerine işaret edebilecek farklılıklar ve anomaliler hakkında içgörü elde etmeye yardımcı olur.

Denetimi etkinleştirmek için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Güvenlik** bölümünde **Denetim**' i seçin.

1. **Denetim** ayarları altında aşağıdaki değerleri ayarlayın:

   1. **Denetim** seçeneğini **AÇIK** olarak ayarlayın.

   1. Aşağıdaki gibi, **Denetim günlüğü hedefini** seçin:

       - **Depolama**, olay günlüklerinin kaydedildiği ve *. XEL* dosyaları olarak indirilebilen bir Azure depolama hesabıdır.

          > [!TIP]
          > Denetim raporu şablonlarından en iyi şekilde yararlanmak için, denetlenen tüm veritabanları için aynı depolama hesabını kullanın.

       - Sorgu veya daha fazla analiz için olayları otomatik olarak depolayan **Log Analytics**

           > [!NOTE]
           > Analiz, özel uyarı kuralları ve Excel veya Power BI dışarı aktarmalar gibi gelişmiş özellikleri desteklemek için bir **Log Analytics çalışma alanı** gereklidir. Çalışma alanı olmadan yalnızca sorgu Düzenleyicisi kullanılabilir.

       - Olayların diğer uygulamalarda kullanılmak üzere yönlendirilmesine izin veren **Olay Hub**'ı.

   1. **Kaydet**’i seçin.

      ![Denetim ayarları](./media/secure-database-tutorial/audit-settings.png)

1. Artık veritabanı olayları verilerini görüntülemek için **Denetim günlüklerini görüntüle** ' yi seçebilirsiniz.

    ![Denetim kayıtları](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> PowerShell veya REST API kullanarak denetim olaylarını nasıl özelleştireceğinizi öğrenmek için bkz. [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md) .

### <a name="dynamic-data-masking"></a>Dinamik veri maskeleme

Veri maskeleme özelliği, veritabanınızdaki hassas verileri otomatik olarak gizleyecek.

Veri maskeleme 'yi etkinleştirmek için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Güvenlik** bölümünde **dinamik veri maskeleme**' yi seçin.

1. **Dinamik veri maskeleme** ayarları altında maske kuralı eklemek Için **maske Ekle** ' yi seçin. Azure, kullanılabilir veritabanı şemalarını, tabloları ve sütunları seçmek için otomatik olarak doldurur.

    ![Maske ayarları](./media/secure-database-tutorial/mask-settings.png)

1. **Kaydet**’i seçin. Seçili bilgiler artık gizlilik için maskelenir.

    ![Maske örneği](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Saydam veri şifrelemesi

Şifreleme özelliği, bekleyen verileri otomatik olarak şifreler ve şifrelenmiş veritabanına erişen uygulamalarda değişiklik gerektirmez. Yeni veritabanları için şifreleme varsayılan olarak açık olur. Ayrıca SSMS ve [Always şifreli](always-encrypted-certificate-store-configure.md) özelliğini kullanarak verileri şifreleyebilirsiniz.

Şifrelemeyi etkinleştirmek veya doğrulamak için:

1. Azure portal, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Güvenlik** bölümünde **Saydam veri şifrelemesi**' ni seçin.

1. Gerekirse, **veri şifrelemeyi** **Açık** olarak ayarlayın. **Kaydet**’i seçin.

    ![Saydam Veri Şifrelemesi](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Şifreleme durumunu görüntülemek için [SSMS](connect-query-ssms.md) kullanarak veritabanına bağlanın ve `encryption_state` [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) görünümünün sütununu sorgulayın. Durumu `3` , veritabanının şifrelendiğini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, yalnızca birkaç basit adımla veritabanınızın güvenliğini geliştirmeyi öğrendiniz. Şunları öğrendiniz:

> [!div class="checklist"]
>
> - Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları oluşturma
> - Azure Active Directory (AD) yöneticisini yapılandırma
> - SQL kimlik doğrulaması, Azure AD kimlik doğrulaması ve güvenli bağlantı dizeleri ile Kullanıcı erişimini yönetme
> - SQL için Azure Defender, denetim, veri maskeleme ve şifreleme gibi güvenlik özelliklerini etkinleştirin

Coğrafi dağıtımın nasıl uygulanacağını öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
>[Coğrafi olarak dağıtılmış bir veritabanı uygulama](geo-distributed-application-configure-tutorial.md)