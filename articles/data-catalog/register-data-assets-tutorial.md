---
title: Azure Veri Kataloğu’ndaki veri varlıklarını kaydetme
description: Bu öğreticide, Azure veri kataloğunuza veri varlıklarının nasıl kaydedileceği açıklanmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950241"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Öğretici: Azure Veri Kataloğu’ndaki veri varlıklarını kaydetme

Bu öğreticide, katalog ile Azure SQL veritabanı örneğinden veri varlıkları kaydetmek için kayıt aracını kullanırsınız. Kayıt, veri kaynağı ve içerdiği varlıklara ait adlar, türler ve konumlar gibi önemli yapısal meta verilerin ayıklanması ve meta verilerin kataloğa kopyalanması işlemidir. Veri kaynakları ve veri varlıkları olduğu yerde kalır, ancak katalog tarafından daha kolay bulunabilir ve anlaşılabilir hale getirilmeleri için meta veriler kullanılır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Veri varlıklarını kaydetme 
> * Veri varlıkları ara
> * Veri varlıklarına açıklama ekleme
> * Veri varlıklarına bağlanma
> * Veri varlıklarını yönetme
> * Veri varlıklarını silme

## <a name="prerequisites"></a>Önkoşullar

Başlamak için [hızlı başlangıç](register-data-assets-tutorial.md)' ı doldurmanız gerekir.

* [Microsoft Azure](https://azure.microsoft.com/) aboneliği.
* Kendi [Azure Active Directory kiracınız](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)olması gerekir.

Veri Kataloğu 'Nu ayarlamak için bir Azure aboneliğinin sahibi veya ortak sahibi olmanız gerekir.

## <a name="register-data-assets"></a>Veri varlıklarını kaydetme

### <a name="register-a-data-source"></a>Veri kaynağını kaydetme

Veri varlıklarını (tablolarını) bir [Azure SQL veritabanı örneğinden](../sql-database/sql-database-single-database-get-started.md)kaydedersiniz, ancak rolünüz ile ilgili tanıdık ve alakalı verilerle çalışmayı tercih ediyorsanız desteklenen herhangi bir veri kaynağını kullanabilirsiniz. Desteklenen veri kaynaklarının listesi için bkz. [Desteklenen veri kaynakları](data-catalog-dsr.md).

Bu öğreticide kullandığımız Azure SQL veritabanı adı, *Rlstest*' dir.

Artık Azure Veri Kataloğu 'nu kullanarak Azure SQL veritabanı örneğinden veri varlıklarını kaydedebilirsiniz.

1. [Azure Veri Kataloğu giriş sayfasına](http://azuredatacatalog.com) gidin ve **veri Yayımla**' yı seçin.

   ![Azure Veri Kataloğu--Verileri Yayımla düğmesi](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. kayıt aracını bilgisayarınıza indirmek, yüklemek ve çalıştırmak için **Uygulamayı Başlat** ' ı seçin.

   ![Azure Veri Kataloğu--Başlat düğmesi](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. **Hoş geldiniz** sayfasında **oturum aç** ' ı seçin ve kimlik bilgilerinizi girin.

    ![Azure Veri Kataloğu--Hoş geldiniz sayfası](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. **Microsoft Azure Veri Kataloğu** sayfasında **SQL Server** ve **İleri**' yi seçin.

    ![Azure Veri Kataloğu--veri kaynakları](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Azure SQL veritabanı örneğiniz için SQL Server bağlantı özelliklerini girin ve **Bağlan**' ı seçin.

   ![Azure Veri Kataloğu--SQL Server bağlantı ayarları](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Veri varlığınızın meta verilerini kaydedin. Bu örnekte, Azure SQL veritabanı örnek ad alanından **ürün** nesnelerini kaydedersiniz:

    1. **Sunucu hiyerarşisi** ağacında, Azure SQL veritabanı örneğinizi genişletin ve **SalesLT**öğesini seçin.

    2. CTRL + SELECT kullanarak **Product**, **ProductCategory**, **ProductDescription**ve **ProductModel** ' i seçin.

    3. **Taşı-seçili oku** ( **>** ) seçin. Bu eylem seçilen tüm nesneleri **Kaydedilecek nesneler** listesine taşır.

          ![Azure Veri Kataloğu öğreticisi--nesnelere göz atma ve seçme](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Verilerin bir anlık görüntü önizlemesini dahil etmek için **Önizleme Ekle**’yi seçin. Anlık görüntü, her tablodan 20 ' ye kadar kayıt içerir ve bu, kataloğa kopyalanırlar.

    5. Veri profili için nesne istatistiklerinin bir anlık görüntüsünü dahil etmek üzere **Veri Profili Ekle**’yi seçin (örneğin: bir sütun için en küçük, en büyük ve ortalama değerler, satır sayısı).

    6. **Etiket ekle** alanına **Sales, Product, Azure SQL**girin. Bu eylem söz konusu veri varlıklarına arama etiketleri ekler. Etiketler, kullanıcıların kayıtlı bir veri kaynağını bulmasına yardımcı olmak için kullanışlı bir yoludur.

    7. Bu veriler için bir **uzman** adı belirtin (isteğe bağlı).

          ![Azure Veri Kataloğu öğreticisi--kaydedilecek nesneler](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. **Kaydol**' u seçin. Azure Veri Kataloğu seçtiğiniz nesneleri kaydeder. Bu alıştırmada, Azure SQL veritabanı örneğinizdeki seçili nesneler kaydedilir. Kayıt aracı, veri varlığından meta verileri ayıklar ve bu verileri Azure Veri Kataloğu hizmetine kopyalar. Veriler şu anda kalan yerde kalır. Veriler, kaynak sistemin yöneticileri ve ilkeleri denetiminde kalır.

          ![Azure Veri Kataloğu--kayıtlı nesneler](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Kayıtlı veri kaynağı nesnelerinizi görmek için **portalı görüntüle**' yi seçin. Azure Veri Kataloğu portalında, Kılavuz görünümündeki tüm dört tabloyu ve veritabanını gördiğinizi doğrulayın (arama çubuğunun açık olduğunu doğrulayın).

        ![Azure Veri Kataloğu portalındaki nesneler](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Bu alıştırmada, kuruluşunuzdaki kullanıcılar tarafından kolayca bulunabilmesi için Azure SQL veritabanı örneğinden nesne kaydettiniz.

Sonraki alıştırmada, kayıtlı veri varlıklarını nasıl bulacağınızı öğreneceksiniz.

## <a name="discover-data-assets"></a>Veri varlıklarını bulma

Azure Veri Kataloğu’nda bulma işlemi iki birincil mekanizmayı kullanır: arama ve filtreleme.

Arama hem sezgisel hem de güçlü olacak şekilde tasarlanmıştır. Varsayılan olarak, arama terimleri kullanıcı tarafından ek açıklamalar dahil olmak üzere katalogdaki herhangi bir özellikle eşleştirilir.

Filtreleme, aramayı tamamlamak üzere tasarlanmıştır. Eşleşen veri varlıklarını görüntülemek ve arama sonuçlarını eşleşen varlıklarla kısıtlamak için uzmanlar, veri kaynağı türü, nesne türü ve etiketler gibi belirli özellikleri seçebilirsiniz.

Arama ve filtreleme birleşimini kullanarak, Azure Veri Kataloğu 'Na kayıtlı veri kaynaklarında hızlıca gezinebilirsiniz.

Bu alıştırmada, önceki alıştırmada kaydettiğiniz veri varlıklarını bulmak için Azure Veri Kataloğu portalını kullanacaksınız. Arama söz dizimiyle ilgili ayrıntılar için bkz. [Veri Kataloğu Arama söz dizimi başvurusu](/rest/api/datacatalog/#search-syntax-reference).

Katalogdaki veri varlıklarını bulmaya yönelik birkaç örnek aşağıda verilmiştir.  

### <a name="discover-data-assets-with-basic-search"></a>Basit arama ile veri varlıklarını bulma

Basit arama bir veya daha fazla arama terimi kullanarak bir katalogda arama yapmanıza yardımcı olur. Sonuçlar herhangi bir özellikte belirtilen terimlerin bir veya daha fazlasıyla eşleşen tüm varlıkları içerir.

1. Azure Veri Kataloğu portalında **giriş** ' i seçin. Web tarayıcısını kapattıysanız [Azure Veri Kataloğu giriş sayfasına](https://www.azuredatacatalog.com)gidin.

2. Arama kutusuna `product` yazın ve **ENTER** tuşuna basın.

    ![Azure Veri Kataloğu--basit metin araması](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Sonuçlarda tüm dört tabloyu ve veritabanını görtığınızdan emin olun. Aşağıdaki görüntüde gösterildiği gibi araç çubuğundaki düğmeleri seçerek **ızgara görünümü** ve **liste görünümü** arasında geçiş yapabilirsiniz. **Vurgula** seçeneği **Açık** olduğu için arama anahtar sözcüğü arama sonuçlarında vurgulanır. Arama sonuçlarında **sayfa başına sonuç** sayısını da belirtebilirsiniz.

    ![Azure Veri Kataloğu--basit metin araması sonuçları](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    **Aramalar** bölmesi sol tarafta, **Özellikleri** bölmesi sağ taraftadır. **Aramalar** bölmesinde arama ölçütlerini değiştirebilir ve sonuçları filtreleyebilirsiniz. **Özellikler** bölmesinde seçili nesnenin özellikleri ızgara veya liste görünümünde gösterilir.

4. arama sonuçlarında **ürün** ' ı seçin. **Önizleme**, **sütunlar**, **veri profili**ve **belge** sekmelerini seçin ya da alt Bölmeyi genişletmek için oku seçin.  

    ![Azure Veri Kataloğu--alt bölme](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    **Önizleme** sekmesinde **Ürün** tablosundaki verilerin bir önizlemesini görürsünüz.  
5. veri varlığının sütunları ( **ad** ve **veri türü**gibi) hakkındaki ayrıntıları bulmak için **sütunlar** sekmesini seçin.

6. veri varlığı içindeki veri profilini (örneğin, satır sayısı, veri boyutu veya bir sütundaki minimum değer) görmek için **veri profili** sekmesini seçin.

### <a name="discover-data-assets-with-property-scoping"></a>Özellik kapsamı ile veri varlıklarını bulma

Özellik kapsamı, arama teriminin belirtilen özellikle eşleştirildiği veri varlıklarını bulmanıza yardımcı olur.

1. **Filtreler** içindeki **Nesne Türü** altında **Tablo** filtresini temizleyin.  

2. Arama kutusuna `tags:product` yazın ve **ENTER** tuşuna basın. Veri kataloğunu aramak üzere kullanabileceğiniz tüm özellikler için bkz. [Veri Kataloğu Arama söz dizimi başvurusu](/rest/api/datacatalog/#search-syntax-reference).

3. Sonuçlarda tabloları ve veritabanını görtığınızdan emin olun.  

    ![Veri Kataloğu--özellik kapsamı arama sonuçları](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Aramayı kaydetme

1. **Geçerli arama** bölümündeki **aramalar** bölmesinde, arama için bir ad girin ve **Kaydet**' i seçin.

    ![Azure Veri Kataloğu--aramayı kaydetme](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Kayıtlı aramanın **Kayıtlı Aramalar** altında gösterildiğini onaylayın.

3. Kayıtlı arama üzerinde gerçekleştirebileceğiniz eylemlerden birini seçin (aramayı **Yeniden Adlandır**, **Sil**, **Varsayılan Olarak Kaydet**).

### <a name="grouping-with-parentheses"></a>Parantezler ile gruplandırma

Parantezler ile gruplandırma yaparak, özellikle Boole işleçleri ile birlikte mantıksal ayırma sağlamak için sorgunun bölümlerini gruplandırabilirsiniz.

1. Arama kutusuna `name:product AND (tags:product AND objectType:table)` yazın ve **ENTER** tuşuna basın.

2. Arama sonuçlarında yalnızca **Ürün** tablosunu gördüğünüzü onaylayın.

    ![Azure Veri Kataloğu--gruplandırma araması](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Karşılaştırma işleçleri

Karşılaştırma işleçleri ile sayısal ve tarih veri türlerine sahip özellikler için eşitlik dışındaki karşılaştırmaları kullanabilirsiniz.

1. Arama kutusuna `lastRegisteredTime:>"06/09/2016"` yazın.

2. **Nesne Türü** altındaki **Tablo** filtresini temizleyin.

3. **ENTER**'a basın.

4. **Ürün**, **ProductCategory**ve **ProductDescription** TABLOLARıNı ve arama sonuçlarında kaydettiğiniz Azure SQL veritabanını görtığınızdan emin olun.

    ![Azure Veri Kataloğu--karşılaştırma arama sonuçları](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Veri varlıklarını bulma hakkında ayrıntılı bilgi için bkz. [veri varlıklarını keşfetme](data-catalog-how-to-discover.md) . Arama sözdizimi hakkında daha fazla bilgi için bkz. [Veri Kataloğu arama söz dizimi başvurusu](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Veri varlıklarına açıklama ekleme

Bu alıştırmada, Azure Veri Kataloğu portalı 'nı kullanarak katalogdaki mevcut veri varlıklarını not ekleme (açıklamalar, Etiketler veya uzmanlar gibi bilgiler ekleme). Ek açıklamalar, kayıt sırasında veri kaynağından ayıklanan yapısal meta verileri tamamlar. Ek açıklama, veri varlıklarının keşfedilmesine ve anlaşılmasını çok daha kolay hale getirir.

Bu alıştırmada tek bir veri varlığına (ProductPhoto) açıklama eklersiniz. ProductPhoto veri varlığına kolay bir ad ve açıklama eklersiniz.  

1. [Azure Veri Kataloğu giriş sayfasına](https://www.azuredatacatalog.com) gidin ve kaydettiğiniz veri varlıklarını bulmak `tags:product` için ile arama yapın.

2. arama sonuçlarında **ProductModel** ' i seçin.  

3. **Kolay Ad** için **Ürün görüntüleri** ve **Açıklama** için **Pazarlama malzemeleri için ürün fotoğrafları** yazın.

    ![Azure Veri Kataloğu--ProductPhoto açıklaması](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **Açıklama** başkalarının seçilen veri varlığının neden ve nasıl kullanılacağını keşfedip anlamasına yardımcı olur. Ayrıca daha fazla etiket ekleyebilir ve sütunları görüntüleyebilirsiniz. Kataloğa eklediğiniz açıklayıcı meta verileri kullanarak veri kaynaklarını arayabilir ve filtreleyebilirsiniz.

Ayrıca, bu sayfada aşağıdaki adımları uygulayabilirsiniz:

* Veri varlığı için uzmanlar ekleyin. **uzmanlar** alanında **Ekle** ' yi seçin.

* Veri kümesi düzeyinde etiketler ekleyin. **Etiketler** alanında **Ekle** ' yi seçin. Etiket bir kullanıcı etiketi veya bir sözlük etiketi olabilir. Veri Kataloğu Standart Sürümü, katalog yöneticilerinin merkezi bir iş sınıflandırması tanımlamasına olanak tanıyan bir iş sözlüğü içerir. Böylece katalog kullanıcıları, sözlük terimlerini veri varlıklarına ek açıklama olarak dahil edebilir. Daha fazla bilgi için bkz. [İş Sözlüğünü Yönetilen Etiketleme için ayarlama](data-catalog-how-to-business-glossary.md)

* Sütun düzeyinde etiketler ekleyin. Not eklemek istediğiniz sütun için **Etiketler** altında **Ekle** ' yi seçin.

* Sütun düzeyinde açıklama ekleyin. Sütun için **Açıklama** girin. Ayrıca veri kaynağından ayıklanan açıklama meta verilerini görüntüleyebilirsiniz.

* Kullanıcılara veri varlığına nasıl erişeceklerini gösteren **Erişim isteği** bilgilerini ekleyin.
  
* **Belgeler** sekmesini seçin ve veri varlığı için belgeleri belirtin. Azure Veri Kataloğu belgeleri ile veri varlıklarınızın tam bir açıklamasını oluşturmak üzere veri kataloğunuzu bir içerik deposu olarak kullanabilirsiniz.
  
Birden fazla veri varlığına da ek açıklama ekleyebilirsiniz. Örneğin, kaydettiğiniz tüm veri varlıklarını seçebilir ve bunlar için bir uzman belirtebilirsiniz.

![Azure Veri Kataloğu--birden fazla veri varlığına açıklama ekleme](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Veri Kataloğu, ek açıklamalara yönelik kitle kaynak yaklaşımını destekler. Herhangi bir veri kataloğu kullanıcısı Etiketler (Kullanıcı veya sözlük), açıklamalar ve diğer meta verileri ekleyebilirler. Böylece, kullanıcılar bir veri varlığına ve kullanımına perspektif ekler ve bu perspektifi diğer kullanıcılarla paylaşır.

Veri varlıklarına açıklama ekleme hakkında ayrıntılı bilgi için bkz. [Veri varlıklarına açıklama ekleme](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Veri varlıklarına bağlanma

Bu alıştırmada bağlantı bilgilerini kullanarak veri varlıklarını tümleşik bir istemci aracında (Excel) ve tümleşik olmayan bir araçta (SQL Server Management Studio) açarsınız.

> [!NOTE]
> Azure Veri Kataloğu gerçek veri kaynağına erişmenizi sağlamak; yalnızca onu bulup anlamanızı kolaylaştırır. Bir veri kaynağına bağlandığınızda seçtiğiniz istemci uygulaması Windows kimlik bilgilerinizi kullanır veya gerektiğinde kimlik bilgilerinizi ister. Daha önce veri kaynağı için size erişim verilmemişse bağlanabilmeniz için ilk olarak erişim verilmesi gerekir.

### <a name="connect-to-a-data-asset-from-excel"></a>Excel'den veri varlığına bağlanma

1. Arama sonuçlarından **Ürün**’ü seçin. araç çubuğunda **Aç** ' ı seçin ve **Excel**' i seçin.

    ![Azure Veri Kataloğu--veri varlığına bağlanma](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. İndirme açılır penceresinde **Aç** ' ı seçin. Bu deneyim tarayıcıya bağlı olarak farklılık gösterir.

3. **Microsoft Excel güvenlik bildirimi** penceresinde **Etkinleştir**' i seçin.

    ![Azure Veri Kataloğu--Excel güvenlik açılır penceresi](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. **Verileri Içeri aktar** iletişim kutusunda Varsayılanları tutun ve **Tamam**' ı seçin.

    ![Azure Veri Kataloğu--Excel ile verileri içeri aktarma](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Excel'de veri kaynağını görüntüleyin.

    ![Azure Veri Kataloğu--Excel’deki ürün tablosu](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Bu alıştırmada Azure Veri Kataloğu kullanarak bulunan veri varlıklarına bağlandınız. Azure Veri Kataloğu portalı ile **Şurada Aç** menüsüne tümleştirilmiş istemci uygulamalarını kullanarak doğrudan bağlantı kurabilirsiniz. Ayrıca varlık meta verilerine dahil edilen bağlantı konumu bilgilerini kullanarak seçtiğiniz herhangi bir uygulamayla bağlantı kurabilirsiniz. Örneğin, bu öğreticide kayıtlı veri varlıklarıyla ilgili verilere erişmek için Azure SQL veritabanına bağlanmak üzere SQL Server Management Studio kullanabilirsiniz.

1. **SQL Server Management Studio**'yu açın.

2. **Sunucuya Bağlan** iletişim kutusunda Azure Veri Kataloğu portalındaki **Özellikler** bölmesinde bulunan sunucu adını girin.

3. Veri varlığına erişmek için uygun kimlik doğrulama ve kimlik bilgilerini kullanın. Erişiminiz yoksa **Erişim İsteği** alanındaki bilgileri kullanarak erişim elde edin.

    ![Azure Veri Kataloğu--erişim isteği](media/register-data-assets-tutorial/data-catalog-request-access.png)

Uygulamanızda kullanmak üzere ADO.NET, ODBC ve OLEDB bağlantı dizelerini görüntülemek ve panoya kopyalamak için **bağlantı dizelerini görüntüle** ' yi seçin.

## <a name="manage-data-assets"></a>Veri varlıklarını yönetme

Bu adımda veri varlıklarınız için güvenliği ayarlarsınız. Veri Kataloğu kullanıcılara verilerin kendisi için erişim vermez. Veri erişimini veri kaynağının sahibi denetler.

Veri Kataloğu’nu kullanarak veri kaynaklarını bulabilir ve kataloğa kayıtlı kaynaklarla ilgili meta verileri görüntüleyebilirsiniz. Ancak, veri kaynaklarının yalnızca belirli kullanıcılara veya belirli grupların üyelerine görünmesi gereken durumlar olabilir. Bu senaryolarda, kayıtlı veri varlıklarının sahipliğini almak ve sahip olduğunuz varlıkların görünürlüğünü denetlemek için veri kataloğunu kullanabilirsiniz.

> [!NOTE]
> Bu alıştırmada tanımlanan yönetim özellikleri yalnızca Azure Veri Kataloğu Standart Sürümü'nde mevcuttur; Ücretsiz Sürüm içinde mevcut değildir.
> Azure Veri Kataloğu'nda veri varlıklarının sahipliğini alabilir, veri varlıklarına ikincil sahip atayabilir ve veri varlıklarının görünürlüğünü ayarlayabilirsiniz.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Veri varlıklarının sahipliğini alma ve görünürlüğü kısıtlama

1. [Azure Veri Kataloğu giriş sayfasına](https://www.azuredatacatalog.com) gidin. **Arama** metin kutusuna `tags:cycles` yazın ve **ENTER** tuşuna basın.

2. Sonuç listesinden bir öğe seçin ve araç çubuğunda **sahipliğini al** ' ı seçin.

3. **Özellikler** panelinin **Yönetim** bölümünde **Sahipliği Al**' ı seçin.

    ![Azure Veri Kataloğu--sahipliği alma](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Görünürlüğü kısıtlamak için, **görünürlük** bölümünde **Bu kullanıcılar & sahipler** ' i seçin ve **Ekle**' yi seçin. Metin kutusuna kullanıcı e-posta adreslerini girin ve **ENTER** tuşuna basın.

    ![Azure Veri Kataloğu--erişimi kısıtlama](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Veri varlıklarını kaldırma

Bu alıştırmada, önizleme verilerini kayıtlı veri varlıklarından kaldırmak ve veri varlıklarını katalogdan silmek için Azure Veri Kataloğu portalını kullanırsınız.

Azure Veri Kataloğu'nda tek bir varlığı veya birden çok varlığı silebilirsiniz.

1. [Azure Veri Kataloğu giriş sayfasına](https://www.azuredatacatalog.com) gidin.

2. **Arama** metin kutusuna girin `tags:cycles` ve **ENTER**' u seçin.

3. Sonuç listesinden bir öğe seçin ve aşağıdaki görüntüde gösterildiği gibi araç çubuğunda **Sil** ' i seçin:

    ![Azure Veri Kataloğu--ızgara öğesini silme](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Liste görünümünü kullanıyorsanız, aşağıdaki görüntüde gösterildiği gibi onay kutusu öğenin solunda bulunur:

    ![Azure Veri Kataloğu--liste öğesini silme](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Ayrıca birden fazla veri varlığı seçebilir ve aşağıdaki görüntüde gösterildiği gibi silebilirsiniz:

    ![Azure Veri Kataloğu--birden fazla veri varlığını silme](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Kataloğun varsayılan davranışı, herhangi bir kullanıcının herhangi bir veri kaynağını kaydetmesine ve herhangi bir kullanıcının herhangi bir kayıtlı veri varlığını silmesine olanak tanımaktır. Azure Veri Kataloğu Standart Sürümü'ne dahil edilen yönetim özellikleri, varlıkların sahipliğini almaya, varlıkları bulabilecek kişileri kısıtlamaya ve varlıkları silebilecek kişileri kısıtlamaya yönelik ek seçenekler sağlar.

## <a name="summary"></a>Özet

Bu öğreticide, kurumsal veri varlıklarını kaydetme, bulma, yönetme ve bunlara ek açıklama ekleme de dahil olmak üzere Azure Veri Kataloğu'nun temel özelliklerini incelediniz. Öğreticiyi tamamladığınıza göre artık kataloğu kullanmaya başlayabilirsiniz. Sizin ve ekibinizin bağlı olduğunuz veri kaynaklarını kaydederek ve iş arkadaşlarınızı kataloğu kullanmaya davet ederek hemen şimdi başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Desteklenen veri kaynakları](data-catalog-dsr.md)
