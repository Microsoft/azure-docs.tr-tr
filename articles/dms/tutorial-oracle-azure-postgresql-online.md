---
title: "Öğretici: Oracle Online 'ı PostgreSQL için Azure veritabanı 'na geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak, Oracle şirket içi veya sanal makinelerinizden PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 76ab2204a81d3cbfb559bfa4591f1f332b1707ee
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388083"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Öğretici: DMS kullanarak Azure 'a PostgreSQL için Azure veritabanı 'na geçiş (Önizleme)

> [!IMPORTANT]
> **Azure 'Dan PostgreSQL Için Azure veritabanı** geçiş senaryosu (Şu anda önizleme aşamasındadır) 1 Mayıs 2021 ' den sonra kullanılamayacak. Alternatif araç (örneğin, Ora2pg) aracılığıyla destek sağlamaya devam edeceğiz ve Oracle to PostgreSQL geçişleri için en iyi geçiş deneyimini sağlar. En iyi geçiş yöntemleri için bkz. [PostgreSQL Için Oracle 'A Azure veritabanı geçiş kılavuzu](https://aka.ms/OracletoPGguide). 

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içinde veya sanal makinelerde barındırılan Oracle veritabanlarından, en az kapalı kalma süresine sahip [PostgreSQL Için Azure veritabanı](../postgresql/index.yml) 'na geçirebilirsiniz. Diğer bir deyişle, uygulamayı uygulamanın en az kapalı kalma süresiyle tamamlayabilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak, Oracle 11g 'nin şirket içi veya sanal makine örneğinden, PostgreSQL için Azure veritabanı 'na **HR** örnek veritabanını geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Ora2pg aracını kullanarak geçiş çabalarını değerlendirin.
> * Ora2pg aracını kullanarak örnek şemayı geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir.

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, Oracle 'dan PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirme işlemi açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [Oracle 11g Release 2 ' yi (Standard Edition, Standard Edition bir veya Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)indirip yükleyin.
* Örnek **HR** veritabanını [buradan](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)indirin.
* [Ora2pg 'ı Windows veya Linux üzerinde](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)indirin ve yükleyin.
* [PostgreSQL için Azure Veritabanı’nda örnek oluşturma](../postgresql/quickstart-create-server-database-portal.md).
* Örneğe bağlanın ve bu [belgedeki](../postgresql/tutorial-design-database-using-azure-portal.md)yönergeyi kullanarak bir veritabanı oluşturun.
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

  > [!NOTE]
  > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
  >
  > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
  > * Depolama uç noktası
  > * Service Bus uç noktası
  >
  > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağ ağ güvenlik grubu (NSG) kurallarınızın, ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak Oracle sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 1521 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı için sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* Kaynak Oracle veritabanlarına erişimi etkinleştirin.

  > [!NOTE]
  > Bir kullanıcının Oracle kaynağına bağlanması için DBA rolü gereklidir.

  * Veri değişikliğini yakalamak için Azure veritabanı geçiş hizmeti 'nde artımlı eşitleme için Arşiv yineleme günlükleri gereklidir. Oracle kaynağını yapılandırmak için aşağıdaki adımları izleyin:
    * Aşağıdaki komutu çalıştırarak SYSDBA ayrıcalığını kullanarak oturum açın:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Aşağıdaki komutu çalıştırarak veritabanı örneğini kapatın.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Onay için bekleyin `'ORACLE instance shut down'` .

    * Yeni örneği başlatın ve veritabanını arşivlemeyi etkinleştirmek veya devre dışı bırakmak için şu komutu çalıştırın:

      ```
      STARTUP MOUNT;
      ```

      Veritabanı takılmalıdır; ' Oracle örneğinin başlatıldığı ' onay için bekleyin.

    * Aşağıdaki komutu çalıştırarak veritabanı arşivleme modunu değiştirin:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Aşağıdaki komutu çalıştırarak veritabanını normal işlemler için açın:

      ```
      ALTER DATABASE OPEN;
      ```

      YAY dosyasının gösterilmesi için yeniden başlatmanız gerekebilir.

    * Doğrulamak için şu komutu çalıştırın:

      ```
      SELECT log_mode FROM v$database;
      ```

      Yanıt almanız gerekir `'ARCHIVELOG'` . Yanıt varsa `'NOARCHIVELOG'` , gereksinim karşılanmıyor.

  * Aşağıdaki seçeneklerden birini kullanarak, çoğaltma için ek günlüğe kaydetmeyi etkinleştirin.

    * **1. seçenek**.
      PK ve benzersiz dizin içeren tüm tabloları kapsayacak şekilde veritabanı düzeyinde ek günlüğe kaydetme 'yi değiştirin. Algılama sorgusu döndürülür `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Tablo düzeyinde ek günlüğe kaydetmeyi değiştirin. Yalnızca veri işleme olan ve PKs ya da Unique dizinleri olmayan tablolar için çalıştırın.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Seçenek 2**.
      Veritabanı düzeyinde ek günlüğe kaydetme işlemini tüm tabloları kapsayacak şekilde değiştirin ve algılama sorgusu geri döner `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Tablo düzeyinde ek günlüğe kaydetmeyi değiştirin. Her tablo için yalnızca bir ifade çalıştırmak üzere aşağıdaki mantığı izleyin.

      Tabloda bir birincil anahtar varsa:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Tabloda benzersiz bir dizin varsa:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Aksi takdirde, aşağıdaki komutu çalıştırın:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Doğrulamak için şu komutu çalıştırın:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Yanıt almanız gerekir `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Bir Oracle ile PostgreSQL için Azure veritabanı geçişi için çabayı değerlendirin

Oracle 'dan PostgreSQL için Azure veritabanı 'na geçiş yapmak için gereken çabayı değerlendirmek için ora2pg kullanmanızı öneririz. `ora2pg -t SHOW_REPORT`Tüm Oracle nesnelerini, tahmini geçiş maliyetini (Geliştirici Günleri) ve dönüştürmenin bir parçası olarak özel dikkat gerektirebilecek belirli veritabanı nesnelerini listelemeyi sağlayan bir rapor oluşturmak için yönergesini kullanın.

Çoğu müşteri değerlendirme raporunu inceleyerek önemli bir miktar zaman harcayacak ve otomatik ve el ile dönüştürme çabalarının göz önünde bulunduracaktır.

Bir değerlendirme raporu oluşturmak için ora2pg yapılandırmak ve çalıştırmak için, [Oracle 'Dan PostgreSQL Için Azure veritabanı tanımlama kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)' nın **ön geçiş: değerlendirme** bölümüne bakın. [Burada](https://ora2pg.darold.net/report.html)başvuru için örnek bir ora2pg değerlendirmesi raporuna ulaşabilirsiniz.

## <a name="export-the-oracle-schema"></a>Oracle şemasını dışarı aktarma

Oracle şemasını ve diğer Oracle nesnelerini (türler, yordamlar, işlevler, vb.) PostgreSQL için Azure veritabanı ile uyumlu bir şemaya dönüştürmek için ora2pg kullanmanızı öneririz. ora2pg, belirli veri türlerini önceden tanımlamanızı sağlayacak birçok yönergesi içerir. Örneğin, `DATA_TYPE` her sayıyı (*, 0) sayısal (38) yerine bigint ile değiştirmek için yönergesini kullanabilirsiniz.

. SQL dosyalarındaki veritabanı nesnelerinin her birini dışarı aktarmak için ora2pg çalıştırabilirsiniz. Daha sonra, psql kullanarak PostgreSQL için Azure veritabanı 'na aktarmadan önce. SQL dosyalarını gözden geçirebilir veya çalıştırabilirsiniz. PgAdmin içindeki SQL betiği.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Örnek:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Şema dönüştürmesi için ora2pg yapılandırmak ve çalıştırmak için, [Oracle 'Dan PostgreSQL Için Azure veritabanı tanımlama kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)için **geçiş: şema ve veri** bölümüne bakın.

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda şemayı ayarlama

Azure veritabanı geçiş hizmeti 'nde bir geçiş işlem hattı başlatmadan önce ora2pg kullanarak bunları uyumlu hale getirmek için Oracle tablo şemalarını, saklı yordamları, paketleri ve diğer veritabanı nesnelerini dönüştürmeyi tercih edebilirsiniz. Ora2pg ile çalışma hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın:

* [Ora2pg 'i Windows üzerinde yükler](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle 'dan Azure 'a PostgreSQL geçişi kılavuz kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure veritabanı geçiş hizmeti, PostgreSQL tablo şemasını da oluşturabilir. Hizmet, bağlı Oracle kaynağında bulunan tablo şemasına erişir ve PostgreSQL için Azure veritabanı 'nda uyumlu bir tablo şeması oluşturur. Azure veritabanı geçiş hizmeti şemayı oluşturmayı ve verileri taşımayı tamamladıktan sonra PostgreSQL için Azure veritabanı 'nda şema biçimini doğrulayıp denetlediğinizden emin olun.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti yalnızca tablo şemasını oluşturur; saklı yordamlar, paketler, dizinler vb. gibi diğer veritabanı nesneleri oluşturulmaz.

Ayrıca, tam yükün çalıştırılabilmesi için hedef veritabanına yabancı anahtarı bırakmaya de dikkat edin. Yabancı anahtarı bırakmak için kullanabileceğiniz bir betik için [buradaki](./tutorial-postgresql-azure-postgresql-online.md) makalenin **örnek şemasını geçirme** bölümüne bakın. Tam yükleme ve eşitleme için çalıştırmak üzere Azure veritabanı geçiş hizmeti 'ni kullanın.

### <a name="when-the-postgresql-table-schema-already-exists"></a>PostgreSQL tablo şeması zaten mevcut olduğunda

Azure veritabanı geçiş hizmeti ile veri hareketini başlatmadan önce ora2pg gibi araçları kullanarak bir PostgreSQL şeması oluşturursanız, kaynak tabloları Azure veritabanı geçiş hizmeti 'ndeki hedef tablolarla eşleyin.

1. Yeni bir Oracle ile PostgreSQL için Azure veritabanı geçiş projesi oluşturduğunuzda, şemaları Seç adımında hedef veritabanı ve hedef şema ' ı seçmeniz istenir. Hedef veritabanını ve hedef şemayı girin.

   ![Ekran görüntüsü hedef veritabanlarına yönelik eşlemeyi gösterir.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. **Geçiş ayarları** ekranı, Oracle kaynağında bulunan tabloların bir listesini gösterir. Azure veritabanı geçiş hizmeti, kaynak ve hedef tablolardaki tabloları tablo adına göre eşleştirmeye çalışır. Farklı küçük harflere sahip birden fazla eşleşen hedef tablo varsa, hangi hedef tabloya eşlendiğini seçebilirsiniz.

    ![Ekran görüntüsü, geçiş ayarlarını gösterir.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Kaynak tablo adlarını farklı adlara sahip tablolarla eşlemeniz gerekiyorsa, e-posta ile [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) işlemi otomatik hale getirmek için bir betik sağlayabiliriz.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>PostgreSQL tablo şeması mevcut olmadığında

Hedef PostgreSQL veritabanı hiçbir tablo şeması bilgisi içermiyorsa, Azure veritabanı geçiş hizmeti kaynak şemayı dönüştürür ve hedef veritabanında yeniden oluşturur. Azure veritabanı geçiş hizmeti, saklı yordamlar, paketler ve dizinler gibi diğer veritabanı nesnelerini değil, yalnızca tablo şemasını oluşturduğunu unutmayın.
Azure veritabanı geçiş hizmeti 'nin sizin için şemayı oluşturması için, hedef ortamınızın, mevcut tablo olmayan bir şema içerdiğinden emin olun. Azure veritabanı geçiş hizmeti herhangi bir tabloyu tespit ederseniz, hizmet şemanın ora2pg gibi bir dış araç tarafından oluşturulduğunu varsayar.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti, Azure veritabanı geçiş hizmeti ya da ora2pg gibi bir araç kullanılarak tüm tabloların aynı şekilde oluşturulmasını gerektirir, ancak her ikisi birden değildir.

Başlamak için:

1. Uygulama gereksinimlerinize bağlı olarak hedef veritabanında bir şema oluşturun. Varsayılan olarak, PostgreSQL tablo şeması ve sütun adları daha düşüktür. Oracle tablo şeması ve sütunları, diğer taraftan, tüm sermaye durumunda varsayılan olarak varsayılandır.
2. Şemaları seçin adımında hedef veritabanını ve hedef şemayı belirtin.
3. PostgreSQL için Azure veritabanı 'nda oluşturduğunuz şemaya bağlı olarak, Azure veritabanı geçiş hizmeti aşağıdaki dönüşüm kurallarını kullanır:

    Oracle kaynağında şema adı ve PostgreSQL için Azure veritabanı ile eşleşiyorsa, Azure veritabanı geçiş hizmeti, *Hedefteki ile aynı büyük/küçük harf kullanarak tablo şemasını oluşturur*.

    Örnek:

    | Kaynak Oracle şeması | Hedef PostgreSQL database. Schema | DMS tarafından oluşturulan Schema. Table. Column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. public | public.countries.country_id |
    | HR | targetHR. trgthr | trgthr.countries.country_id |
    | HR | targetHR. TARGETHR | "TARGETHR". " ÜLKELER "." COUNTRY_ID " |
    | HR | targetHR.HR | "HR". " ÜLKELER "." COUNTRY_ID " |
    | HR | targetHR.Hr | * Karışık durumlar eşlenemiyor |

    * Hedef PostgreSQL 'te karışık Case şeması ve tablo adları oluşturmak için, iletişim kurun [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Hedef PostgreSQL veritabanında karma durum tablosu şeması ayarlamak için bir betik sağlayabiliriz.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Azure Veritabanı Geçiş Hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve sonra **Kaynak sağlayıcıları**’nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS örneği oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ne kaynak Oracle ve PostgreSQL için Azure veritabanı örneğine erişim sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

5. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure Veritabanı Geçiş Hizmeti’nin tüm örneklerini bulma](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında oluşturduğunuz Azure Veritabanı Geçiş Hizmeti örneğinin adını arayın ve sonuçlardan bu örneği seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğinizi bulma](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. +**Yeni Geçiş Projesi**'ni seçin.
4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **Oracle**' ı seçin, **hedef sunucu türü** metin kutusunda, **PostgreSQL için Azure veritabanı**' nı seçin.
5. **Etkinlik türünü seçin** bölümünde **çevrimiçi veri geçişi**' ni seçin.

   ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

6. **Kaydet**' i seçin, çevrimiçi bir geçiş gerçekleştirmek Için Azure veritabanı geçiş hizmeti 'ni başarıyla kullanma gereksinimleri ' ni ve ardından **etkinlik oluştur ve Çalıştır**' ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

* **Kaynak ayrıntıları Ekle** ekranında, kaynak Oracle örneği için bağlantı ayrıntılarını belirtin.

  ![Kaynak Ayrıntıları Ekleyin ekranı](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCı sürücüsünü karşıya yükle

1. **Kaydet**' i seçin ve ardından **OCI Sürücü Yükle** ekranında Oracle hesabınızda oturum açın ve sürücü **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37.128.586 bayt) (SHA1 sağlama toplamı: 865082268) [buradan](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)indirin.
2. Sürücüyü paylaşılan bir klasöre indirin.

   Klasörün, en düşük salt okuma erişimiyle belirttiğiniz kullanıcı adıyla paylaşıldığından emin olun. Azure veritabanı geçiş hizmeti, belirttiğiniz kullanıcı adını taklit ederek OCı sürücüsünü Azure 'a yüklemek için paylaşıma erişir ve paylaşımdan okur.

   Belirttiğiniz Kullanıcı adı bir Windows Kullanıcı hesabı olmalıdır.

   ![OCı sürücü yüklemesi](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Kaydet**' i seçin ve ardından **hedef ayrıntıları** ekranında, **HR** şemasının dağıtıldığı PostgreSQL için Azure veritabanı 'Nın önceden sağlanmış örneği olan PostgreSQL için hedef Azure veritabanı sunucusu için bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntıları ekranı](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. **Kaydet**'i seçin, **Geçiş özeti** ekranındaki **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin ve ardından, kaynak ve hedef ayrıntılarının önceden belirttiğiniz ayrıntılarla eşleştiğinden emin olmak üzere özeti gözden geçirin.

    ![Geçiş Özeti](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

  Geçiş etkinliği penceresi açılır ve etkinliğin **Durum** bilgisi **Başlatılıyor** olarak değişir.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Çalıştırılıyor** olana kadar güncelleştirebilirsiniz.

     ![Etkinlik durumu-çalışıyor](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. **Veritabanı adı** altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

    Tam veri yüklemesi ilk yük geçiş durumunu, Artımlı veri eşitleme ise değişiklik verilerini yakalama (CDC) durumunu gösterir.

     ![Etkinlik Durumu - Tam yük tamamlandı](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Etkinlik Durumu - Artımlı veri eşitleme](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk Tam yük tamamlandıktan sonra, veritabanları **Geçiş için hazır** olarak işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

2. **Bekleyen değişiklikler** sayacı **0** değerini gösterene kadar bekleyerek kaynak veritabanına gelen tüm işlemleri durdurduğunuzdan emin olun.

   ![Tam geçişi başlat](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. **Onayla**'yı ve ardından, **Uygula**'yı seçin.
4. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, uygulamalarınızı yeni hedef PostgreSQL Için Azure veritabanı örneğine bağlayın.

 > [!NOTE]
 > PostgreSQL varsayılan olarak Schema. Table. Column ' a sahip olduğundan, bu makalenin önceki kısımlarında yer aldığı **PostgreSQL Için Azure veritabanı** bölümünde bulunan betiği kullanarak büyük küçük harfe daha küçük harfe geri döndürebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* PostgreSQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [PostgreSQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-postgresql-online.md) başlıklı makaleye bakın.
* Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](./dms-overview.md)makalesine bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](../postgresql/overview.md)makalesine bakın.
