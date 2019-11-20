---
title: "Öğretici: Oracle 'ın PostgreSQL için Azure veritabanı 'na çevrimiçi geçişini gerçekleştirmek için Azure veritabanı geçiş hizmeti 'ni kullanma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak, Oracle şirket içi veya sanal makinelerinizden PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 1ac5e4dd28f7565f546c700a4bbb0076fd793bb7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163434"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Öğretici: DMS kullanarak Azure 'a PostgreSQL için Azure veritabanı 'na geçiş (Önizleme)

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içinde veya sanal makinelerde barındırılan Oracle veritabanlarından, en az kapalı kalma süresine sahip [PostgreSQL Için Azure veritabanı](https://docs.microsoft.com/azure/postgresql/) 'na geçirebilirsiniz. Diğer bir deyişle, uygulamayı uygulamanın en az kapalı kalma süresiyle tamamlayabilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak, Oracle 11g 'nin şirket içi veya sanal makine örneğinden, PostgreSQL için Azure veritabanı 'na **HR** örnek veritabanını geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Ora2pg aracını kullanarak geçiş çabalarını değerlendirin.
> * Ora2pg aracını kullanarak örnek şemayı geçirin.
> * Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
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
* Ora2pg 'i [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) veya [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf)üzerinde indirin ve yükleyin.
* [PostgreSQL için Azure Veritabanı’nda örnek oluşturma](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Örneğe bağlanın ve bu [belgedeki](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal)yönergeyi kullanarak bir veritabanı oluşturun.
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

  > [!NOTE]
  > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
  > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
  > * Depolama uç noktası
  > * Service Bus uç noktası
  >
  > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* VNet ağ güvenlik grubu (NSG) kurallarınızın Azure veritabanı geçiş hizmeti 'ne yönelik aşağıdaki gelen iletişim bağlantı noktalarını engellemediğinden emin olun: 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak Oracle sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 1521 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı için sunucu düzeyinde bir [güvenlik duvarı kuralı](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan VNet 'in alt ağ aralığını belirtin.
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

      Onay `'ORACLE instance shut down'`bekleyin.

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

      Bir yanıt `'ARCHIVELOG'`almalısınız. Yanıt `'NOARCHIVELOG'`, gereksinim karşılanmıyor.

  * Aşağıdaki seçeneklerden birini kullanarak, çoğaltma için ek günlüğe kaydetmeyi etkinleştirin.

    * **1. seçenek**.
      PK ve benzersiz dizin içeren tüm tabloları kapsayacak şekilde veritabanı düzeyinde ek günlüğe kaydetme 'yi değiştirin. Algılama sorgusu `'IMPLICIT'`döndürür.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Tablo düzeyinde ek günlüğe kaydetmeyi değiştirin. Yalnızca veri işleme olan ve PKs ya da Unique dizinleri olmayan tablolar için çalıştırın.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Seçenek 2**.
      Veritabanı düzeyinde ek günlüğe kaydetme işlemini tüm tabloları kapsayacak şekilde değiştirin ve algılama sorgusu `'YES'`döndürür.

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

    Bir yanıt `'YES'`almalısınız.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Bir Oracle ile PostgreSQL için Azure veritabanı geçişi için çabayı değerlendirin

Oracle 'dan PostgreSQL için Azure veritabanı 'na geçiş yapmak için gereken çabayı değerlendirmek için ora2pg kullanmanızı öneririz. Tüm Oracle nesnelerini, tahmini geçiş maliyetini (Geliştirici Günleri) ve dönüştürmenin bir parçası olarak özel dikkat gerektirebilecek belirli veritabanı nesnelerini listelerken bir rapor oluşturmak için `ora2pg -t SHOW_REPORT` yönergesini kullanın.

Çoğu müşteri değerlendirme raporunu inceleyerek önemli bir miktar zaman harcayacak ve otomatik ve el ile dönüştürme çabalarının göz önünde bulunduracaktır.

Bir değerlendirme raporu oluşturmak için ora2pg yapılandırmak ve çalıştırmak için, [Oracle 'Dan PostgreSQL Için Azure veritabanı tanımlama kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)' nın **ön geçiş: değerlendirme** bölümüne bakın. [Burada](http://ora2pg.darold.net/report.html)başvuru için örnek bir ora2pg değerlendirmesi raporuna ulaşabilirsiniz.

## <a name="export-the-oracle-schema"></a>Oracle şemasını dışarı aktarma

Oracle şemasını ve diğer Oracle nesnelerini (türler, yordamlar, işlevler, vb.) PostgreSQL için Azure veritabanı ile uyumlu bir şemaya dönüştürmek için ora2pg kullanmanızı öneririz. ora2pg, belirli veri türlerini önceden tanımlamanızı sağlayacak birçok yönergesi içerir. Örneğin, her SAYıYı (*, 0) sayısal (38) yerine bigint ile değiştirmek için `DATA_TYPE` yönergesini kullanabilirsiniz.

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

* [Ora2pg 'i Windows üzerinde yükler](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf)
* [Oracle 'dan Azure 'a PostgreSQL geçişi kılavuz kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure veritabanı geçiş hizmeti, PostgreSQL tablo şemasını da oluşturabilir. Hizmet, bağlı Oracle kaynağında bulunan tablo şemasına erişir ve PostgreSQL için Azure veritabanı 'nda uyumlu bir tablo şeması oluşturur. Azure veritabanı geçiş hizmeti şemayı oluşturmayı ve verileri taşımayı tamamladıktan sonra PostgreSQL için Azure veritabanı 'nda şema biçimini doğrulayıp denetlediğinizden emin olun.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti yalnızca tablo şemasını oluşturur; saklı yordamlar, paketler, dizinler vb. gibi diğer veritabanı nesneleri oluşturulmaz.

Ayrıca, tam yükün çalıştırılabilmesi için hedef veritabanına yabancı anahtarı bırakmaya de dikkat edin. Yabancı anahtarı bırakmak için kullanabileceğiniz bir betik için [buradaki](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) makalenin **örnek şemasını geçirme** bölümüne bakın. Tam yükleme ve eşitleme için çalıştırmak üzere Azure veritabanı geçiş hizmeti 'ni kullanın.

### <a name="when-the-postgresql-table-schema-already-exists"></a>PostgreSQL tablo şeması zaten mevcut olduğunda

Azure veritabanı geçiş hizmeti ile veri hareketini başlatmadan önce ora2pg gibi araçları kullanarak bir PostgreSQL şeması oluşturursanız, kaynak tabloları Azure veritabanı geçiş hizmeti 'ndeki hedef tablolarla eşleyin.

1. Yeni bir Oracle ile PostgreSQL için Azure veritabanı geçiş projesi oluşturduğunuzda, şemaları Seç adımında hedef veritabanı ve hedef şema ' ı seçmeniz istenir. Hedef veritabanını ve hedef şemayı girin.

   ![Portal aboneliklerini gösterme](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. **Geçiş ayarları** ekranı, Oracle kaynağında bulunan tabloların bir listesini gösterir. Azure veritabanı geçiş hizmeti, kaynak ve hedef tablolardaki tabloları tablo adına göre eşleştirmeye çalışır. Farklı küçük harflere sahip birden fazla eşleşen hedef tablo varsa, hangi hedef tabloya eşlendiğini seçebilirsiniz.

    ![Portal aboneliklerini gösterme](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Kaynak tablosu adlarını farklı adlara sahip tablolarla (e-posta [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) eşlemeniz gerekiyorsa ve işlemi otomatikleştirmek için bir betik sağlayabiliriz.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>PostgreSQL tablo şeması mevcut olmadığında

Hedef PostgreSQL veritabanı hiçbir tablo şeması bilgisi içermiyorsa, Azure veritabanı geçiş hizmeti kaynak şemayı dönüştürür ve hedef veritabanında yeniden oluşturur. Azure veritabanı geçiş hizmeti, saklı yordamlar, paketler ve dizinler gibi diğer veritabanı nesnelerini değil, yalnızca tablo şemasını oluşturduğunu unutmayın.
Azure veritabanı geçiş hizmeti 'nin sizin için şemayı oluşturması için, hedef ortamınızın, mevcut tablo olmayan bir şema içerdiğinden emin olun. Azure veritabanı geçiş hizmeti herhangi bir tabloyu tespit ederseniz, hizmet şemanın ora2pg gibi bir dış araç tarafından oluşturulduğunu varsayar.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti, Azure veritabanı geçiş hizmeti ya da ora2pg gibi bir araç kullanılarak tüm tabloların aynı şekilde oluşturulmasını gerektirir, ancak her ikisi birden değildir.

Kullanmaya başlamak için:

1. Uygulama gereksinimlerinize bağlı olarak hedef veritabanında bir şema oluşturun. Varsayılan olarak, PostgreSQL tablo şeması ve sütun adları daha düşüktür. Oracle tablo şeması ve sütunları, diğer taraftan, tüm sermaye durumunda varsayılan olarak varsayılandır.
2. Şemaları seçin adımında hedef veritabanını ve hedef şemayı belirtin.
3. PostgreSQL için Azure veritabanı 'nda oluşturduğunuz şemaya bağlı olarak, Azure veritabanı geçiş hizmeti aşağıdaki dönüşüm kurallarını kullanır:

    Oracle kaynağında şema adı ve PostgreSQL için Azure veritabanı ile eşleşiyorsa, Azure veritabanı geçiş hizmeti, *Hedefteki ile aynı büyük/küçük harf kullanarak tablo şemasını oluşturur*.

    Örnek:

    | Kaynak Oracle şeması | Hedef PostgreSQL database. Schema | DMS tarafından oluşturulan Schema. Table. Column |
    | ------------- | ------------- | ------------- |
    | İK | targetHR. public | public. ülkeler. country_id |
    | İK | targetHR. trgthr | trgthr. ülkeler. country_id |
    | İK | targetHR. TARGETHR | "TARGETHR". " ÜLKELER "." COUNTRY_ID" |
    | İK | targetHR.HR | "HR". " ÜLKELER "." COUNTRY_ID" |
    | İK | targetHR.Hr | \* Karışık durumlar eşlenemiyor |

    \* Hedef PostgreSQL içinde karma durum şeması ve tablo adları oluşturmak için [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)başvurun. Hedef PostgreSQL veritabanında karma durum tablosu şeması ayarlamak için bir betik sağlayabiliriz.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Azure Veritabanı Geçiş Hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve sonra **Kaynak sağlayıcıları**’nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS örneği oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure Marketi](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Mevcut bir VNet seçin veya yeni bir sanal ağ oluşturun.

    VNet, kaynak Oracle ve PostgreSQL için Azure veritabanı örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal VNet oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](https://aka.ms/DMSVnet)makalesine bakın.

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

1. **Kaydet**' i seçin ve ardından **OCI Sürücü Yükle** ekranında, Oracle hesabınızda oturum açın ve **instantclient-basiclite-Windows. x64-12.2.0.1.0. zip** (37.128.586 bayt) (SHA1 sağlama toplamı: 865082268) sürücüsünü [buradan indirin ](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Sürücüyü paylaşılan bir klasöre indirin.

   Klasörün, en düşük salt okuma erişimiyle belirttiğiniz kullanıcı adıyla paylaşıldığından emin olun. Azure veritabanı geçiş hizmeti, belirttiğiniz kullanıcı adını taklit ederek OCı sürücüsünü Azure 'a yüklemek için paylaşıma erişir ve paylaşımdan okur.

   Belirttiğiniz Kullanıcı adı bir Windows Kullanıcı hesabı olmalıdır.

   ![OCı sürücü yüklemesi](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Kaydet**' i seçin ve ardından **hedef ayrıntıları** ekranında, **HR** şemasının olduğu PostgreSQL için Azure veritabanı 'Nın önceden sağlanmış örneği olan PostgreSQL için hedef Azure veritabanı sunucusu için bağlantı ayrıntılarını belirtin. dağıtılan.

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

2. **Veritabanı adı**altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

    Tam veri yüklemesi ilk yük geçiş durumunu, Artımlı veri eşitleme ise değişiklik verilerini yakalama (CDC) durumunu gösterir.

     ![Etkinlik Durumu - Tam yük tamamlandı](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Etkinlik Durumu - Artımlı veri eşitleme](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk Tam yük tamamlandıktan sonra, veritabanları **Geçiş için hazır** olarak işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

2. **Bekleyen değişiklikler** sayacı **0** değerini gösterene kadar bekleyerek kaynak veritabanına gelen tüm işlemleri durdurduğunuzdan emin olun.

   ![Tam geçişi başlat](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. **Onayla**'yı ve ardından, **Uygula**'yı seçin.
4. Veritabanı geçiş durumu **tamamlandı**olarak görüntülendiğinde, uygulamalarınızı yeni hedef PostgreSQL Için Azure veritabanı örneğine bağlayın.

 > [!NOTE]
 > PostgreSQL varsayılan olarak Schema. Table. Column ' a sahip olduğundan, bu makalenin önceki kısımlarında yer aldığı **PostgreSQL Için Azure veritabanı** bölümünde bulunan betiği kullanarak büyük küçük harfe daha küçük harfe geri döndürebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* PostgreSQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [PostgreSQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-postgresql-online.md) başlıklı makaleye bakın.
* Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](https://docs.microsoft.com/azure/dms/dms-overview)makalesine bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](https://docs.microsoft.com/azure/postgresql/overview)makalesine bakın.
