---
title: IBM DB2-Azure Logic Apps bağlanma
description: IBM DB2 REST API 'Leri ve Azure Logic Apps kaynakları yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: a7079115b381d094cec77f96015342b5bc568c27
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051027"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Azure Logic Apps ile IBM DB2 kaynaklarını yönetme

Azure Logic Apps ve IBM DB2 Bağlayıcısı sayesinde, DB2 veritabanınızda depolanan kaynaklara göre otomatik görevler ve iş akışları oluşturabilirsiniz. İş akışlarınız veritabanınızdaki kaynaklara bağlanabilir, veritabanı tablolarınızı okuyabilir ve listeleyebilir, satır ekleyebilir, satırları değiştirebilir, satırları silebilir ve daha fazlasını yapabilir. Verilerinize yanıt veren ve çıktıyı diğer eylemler için kullanılabilir hale getirmek için mantıksal uygulamalarınıza eylemler ekleyebilirsiniz.

Bu makalede çeşitli veritabanı işlemlerini gerçekleştiren bir mantıksal uygulama oluşturma işlemi gösterilmektedir. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="supported-platforms-and-versions"></a>Desteklenen platformlar ve sürümler

DB2 Bağlayıcısı, bir TCP/IP ağı üzerinde uzak DB2 sunucularıyla iletişim kuran bir Microsoft istemcisi içerir. Bu bağlayıcıyı, Azure Sanallaştırması 'nda çalışan Windows için IBM DB2 gibi bulut veritabanlarına erişmek için kullanabilirsiniz. Şirket içi [veri ağ geçidini yükleyip ayarladıktan](../logic-apps/logic-apps-gateway-connection.md)sonra ŞIRKET içi DB2 veritabanlarına da erişebilirsiniz.

IBM DB2 Bağlayıcısı, bu IBM DB2 platformlarını ve sürümlerini, dağıtılmış Ilişkisel veritabanı mimarisi (DRDA) SQL Access Manager (SQLAM) sürüm 10 ve 11 ' i destekleyen IBM DB2 uyumlu ürünlerle birlikte destekler:

| Platform | Version | 
|----------|---------|
| Z/ç için IBM DB2 | 11,1, 10,1 |
| I için IBM DB2 | 7,3, 7,2, 7,1 |
| LUW için IBM DB2 | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Desteklenen veritabanı işlemleri

IBM DB2 Bağlayıcısı, bağlayıcıdaki ilgili eylemlerle eşleşen bu veritabanı işlemlerini destekler:

| Veritabanı işlemi | Bağlayıcı eylemi |
|--------------------|------------------|
| Veritabanı tablolarını listeleme | Tabloları al |
| Seç kullanarak bir satırı okuma | Satırı al |
| Seç kullanarak tüm satırları oku | Satırları al |
| INSERT kullanarak bir satır ekleme | Satır ekle |
| GÜNCELLEŞTIRME kullanarak bir satırı düzenleme | Satırı güncelleştir |
| SIL kullanarak bir satırı kaldırma | Satırı sil |
|||

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Bulut tabanlı ya da şirket içi bir IBM DB2 veritabanı

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* DB2 veritabanınıza erişmek istediğiniz mantıksal uygulama. Bu bağlayıcı yalnızca eylemler sağlar, bu nedenle mantıksal uygulamanızı başlatmak için **yineleme** tetikleyicisi gibi ayrı bir tetikleyici seçin.
Bu makaledeki örneklerde **yineleme** tetikleyicisi kullanılır.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2 eylemi ekleme-tabloları al

1. [Azure Portal](https://portal.azure.com), zaten açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Tetikleyici altında **yeni adım**' ı seçin.

1. Arama kutusuna filtreniz olarak "DB2" yazın. Bu örnekte, eylemler listesi altında şu eylemi seçin: **Tabloları al (Önizleme)**

   ![Eylem Seç](./media/connectors-create-api-db2/select-db2-action.png)

   Artık DB2 veritabanınız için bağlantı ayrıntıları sağlamanız istenir.

1. [Bulut veritabanları](#cloud-connection) veya [Şirket içi veritabanları](#on-premises-connection)için bağlantı oluşturma adımlarını izleyin.

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Cloud DB2 'ye bağlanma

Bağlantınızı ayarlamak için, istendiğinde bu bağlantı ayrıntılarını sağlayın, **Oluştur**' u seçin ve ardından mantıksal uygulamanızı kaydedin:

| Özellik | Gerekli | Açıklama |
|----------|----------|-------------|
| **Şirket içi ağ geçidi üzerinden Bağlan** | Hayır | Yalnızca şirket içi bağlantılar için geçerlidir. |
| **Bağlantı Adı** | Evet | Bağlantınızın adı, örneğin "MyLogicApp-DB2-Connection" |
| **Sunucu** | Evet | DB2 sunucunuzun adresi veya diğer adı iki nokta üst üste bağlantı noktası numarası (örneğin, "myDB2server.cloudapp.net:50000") <p><p>**Not**: Bu değer, bir TCP/IP adresini veya diğer adı IPv4 veya IPv6 biçiminde, ardından iki nokta üst üste ve TCP/IP bağlantı noktası numarasına göre temsil eden bir dizedir. |
| **Veritabanı** | Evet | Veritabanınızın adı <p><p>**Not**: Bu değer, bir DRDA Ilişkisel veritabanı adını (RDBNAM) temsil eden bir dizedir: <p>-DB2 for z/OS, veritabanının "z/ç için IBM DB2" konumu olarak bilinen bir 16 baytlık dizeyi kabul eder. <br>-DB2, veritabanının "ı için IBM DB2" ilişkisel veritabanı olarak bilinen bir 18 baytlık dizeyi kabul eder. <br>-LUW için-DB2 8 baytlık bir dizeyi kabul eder. |
| **Kullanıcı Adı** | Evet | Veritabanı için Kullanıcı adınız <p><p>**Not**: Bu değer, belirli bir veritabanını temel alan bir dizedir: <p><p>-Z/ç için-DB2 8 baytlık bir dizeyi kabul eder. <br>-DB2, 10 baytlık bir dizeyi kabul eder. <br>-Linux veya UNIX için DB2, 8 baytlık bir dizeyi kabul eder. <br>-Windows için-DB2, 30 baytlık bir dizeyi kabul eder. |
| **Parola** | Evet | Veritabanı için parolanız |
||||

Örneğin:

![Bulut tabanlı veritabanları için bağlantı ayrıntıları](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Şirket içi DB2 'ye bağlanma

Bağlantınızı oluşturmadan önce şirket içi veri ağ geçidiniz zaten yüklü olmalıdır. Aksi takdirde, bağlantınızın kurulumunu tamamlayamıyoruz. Ağ Geçidi yüklemeniz varsa, bu bağlantı ayrıntılarını sağlamaya devam edin ve **Oluştur**' u seçin.

| Özellik | Gerekli | Açıklama |
|----------|----------|-------------|
| **Şirket içi ağ geçidi üzerinden Bağlan** | Evet | Şirket içinde bağlantı istediğinizde geçerlidir ve şirket içi bağlantı özelliklerini gösterir. |
| **Bağlantı Adı** | Evet | Bağlantınızın adı, örneğin "MyLogicApp-DB2-Connection" | 
| **Sunucu** | Evet | DB2 sunucunuzun adresi veya diğer adı iki nokta üst üste bağlantı noktası numarası, örneğin, "myDB2server: 50000" <p><p>**Not**: Bu değer, bir TCP/IP adresini veya diğer adı IPv4 veya IPv6 biçiminde, ardından iki nokta üst üste ve TCP/IP bağlantı noktası numarasına göre temsil eden bir dizedir. |
| **Veritabanı** | Evet | Veritabanınızın adı <p><p>**Not**: Bu değer, bir DRDA Ilişkisel veritabanı adını (RDBNAM) temsil eden bir dizedir: <p>-DB2 for z/OS, veritabanının "z/ç için IBM DB2" konumu olarak bilinen bir 16 baytlık dizeyi kabul eder. <br>-DB2, veritabanının "ı için IBM DB2" ilişkisel veritabanı olarak bilinen bir 18 baytlık dizeyi kabul eder. <br>-LUW için-DB2 8 baytlık bir dizeyi kabul eder. |
| **Kimlik Doğrulaması** | Evet | Bağlantınızın kimlik doğrulama türü, örneğin, "temel" <p><p>**Not**: Bu değeri, temel veya Windows (Kerberos) içeren listeden seçin. |
| **Kullanıcı Adı** | Evet | Veritabanı için Kullanıcı adınız <p><p>**Not**: Bu değer, belirli bir veritabanını temel alan bir dizedir: <p><p>-Z/ç için-DB2 8 baytlık bir dizeyi kabul eder. <br>-DB2, 10 baytlık bir dizeyi kabul eder. <br>-Linux veya UNIX için DB2, 8 baytlık bir dizeyi kabul eder. <br>-Windows için-DB2, 30 baytlık bir dizeyi kabul eder. |
| **Parola** | Evet | Veritabanı için parolanız |
| **Ağ geçidi** | Evet | Yüklü şirket içi veri ağ geçidinizin adı <p><p>**Not**: Azure aboneliğiniz ve kaynak grubunuz içindeki tüm yüklü veri ağ geçitlerini içeren listeden bu değeri seçin. |
||||

Örneğin:

![Şirket içi veritabanları için bağlantı ayrıntıları](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Çıkış tablolarını görüntüleme

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

   ![Çalıştırma geçmişini görüntüleme](./media/connectors-create-api-db2/run-history.png)

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Tabloları al** eylemini genişletin.

   ![Eylemi Genişlet](./media/connectors-create-api-db2/expand-action-step.png)

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıkışlar, tabloların bir listesini içerir.

   ![Çıkış tablolarını görüntüleme](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Satırı al

Bir DB2 veritabanı tablosunda bir kayıt getirmek için mantıksal uygulamanızdaki **satırı al** eylemini kullanın. Bu eylem, örneğin, `SELECT WHERE` `SELECT FROM AREA WHERE AREAID = '99999'`bir DB2 ekstresi çalıştırır.

1. Mantıksal uygulamanızda daha önce DB2 eylemleri kullanmadıysanız, [DB2 eylem-tabloları al](#add-db2-action) bölümündeki adımları gözden geçirin, ancak bunun yerine **satırı al** eylemini ekleyin ve ardından devam etmek için buraya dönün.

   **Satırı al** eylemini ekledikten sonra örnek mantıksal uygulamanız şöyle görünür:

   ![Satır al eylemi](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Gerekli tüm özellikler için değerleri belirtin (*). Bir tablo seçtikten sonra, eylem söz konusu tablodaki kayıtlara özgü ilgili özellikleri gösterir.

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Tablo adı** | Evet | Bu örnekteki "alan" gibi istediğiniz kayda sahip tablo |
   | **Alan KIMLIĞI** | Evet | Bu örnekte "99999" gibi istediğiniz kaydın KIMLIĞI |
   ||||

   ![Tablo seçin](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="view-output-row"></a>Çıkış satırını görüntüle

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Satırı al** eylemini genişletin.

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıktılar, belirtilen satırı içerir.

   ![Çıkış satırını görüntüle](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Satırları al

Bir DB2 veritabanı tablosundaki tüm kayıtları getirmek için mantıksal uygulamanızdaki **satırları al** eylemini kullanın. Bu eylem, örneğin, `SELECT` `SELECT * FROM AREA`bir DB2 ekstresi çalıştırır.

1. Mantıksal uygulamanızda daha önce DB2 eylemleri kullanmadıysanız, [DB2 eylem-tabloları al](#add-db2-action) bölümündeki adımları gözden geçirin, ancak bunun yerine **satırları al** eylemini ekleyin ve ardından devam etmek için buraya dönün.

   **Satırları al** eylemini ekledikten sonra örnek mantıksal uygulamanız şöyle görünür:

   ![Satırları al eylemi](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. **Tablo adı** listesini açın ve istediğiniz tabloyu seçin, bu örnekte "alan" olur:

   ![Tablo seçin](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Sonuçlar için bir filtre veya sorgu belirtmek üzere **Gelişmiş seçenekleri göster**' i seçin.

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="view-output-rows"></a>Çıktı satırlarını görüntüle

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Satırları al** eylemini genişletin.

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıkışlar, belirtilen tablodaki tüm kayıtları içerir.

   ![Çıktı satırlarını görüntüle](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Satır ekle

Bir DB2 veritabanı tablosuna tek bir kayıt eklemek için mantıksal uygulamanızdaki **satır ekle** eylemini kullanın. Bu eylem, örneğin, `INSERT` `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`bir DB2 ekstresi çalıştırır.

1. Mantıksal uygulamanızda daha önce DB2 eylemleri kullanmadıysanız, [DB2 eylem-tabloları al](#add-db2-action) bölümündeki adımları gözden geçirin, ancak bunun yerine **satır ekle** eylemini ekleyin ve ardından devam etmek için buraya dönün.

   **Satır ekle** eylemini ekledikten sonra örnek mantıksal uygulamanız şöyle görünür:

   ![Satır Ekle eylemi](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Gerekli tüm özellikler için değerleri belirtin (*). Bir tablo seçtikten sonra, eylem söz konusu tablodaki kayıtlara özgü ilgili özellikleri gösterir.

   Bu örnek için şu özellikler verilmiştir:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Tablo adı** | Evet | Kaydın ekleneceği tablo, örneğin "alan" |
   | **Alan KIMLIĞI** | Evet | Eklenecek alanın KIMLIĞI, örneğin "99999" |
   | **Alan açıklaması** | Evet | Eklenecek alanın açıklaması, örneğin "alan 99999" |
   | **Bölge KIMLIĞI** | Evet | Eklenecek bölgenin KIMLIĞI, örneğin "102" |
   |||| 

   Örneğin:

   ![Tablo seçin](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="view-insert-row-outputs"></a>Satır ekleme çıkışlarını görüntüle

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Satır ekle** eylemini genişletin.

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıkışlar, belirtilen tablonuza eklediğiniz kaydı içerir.

   ![Ekli satır ile çıktıyı görüntüleme](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Satırı güncelleştir

Bir DB2 veritabanı tablosundaki tek bir kaydı güncelleştirmek için mantıksal uygulamanızdaki **satırı Güncelleştir** eylemini kullanın. Bu eylem, örneğin, `UPDATE` `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`bir DB2 ekstresi çalıştırır.

1. Mantıksal uygulamanızda daha önce DB2 eylemleri kullanmadıysanız, [DB2 eylem-tabloları al](#add-db2-action) bölümündeki adımları gözden geçirin, ancak bunun yerine **satırı Güncelleştir** eylemini ekleyin ve ardından devam etmek için buraya dönün.

   **Satırı Güncelleştir** eylemini ekledikten sonra örnek mantıksal uygulamanız şöyle görünür:

   ![Satırı Güncelleştir eylemi](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Gerekli tüm özellikler için değerleri belirtin (*). Bir tablo seçtikten sonra, eylem söz konusu tablodaki kayıtlara özgü ilgili özellikleri gösterir.

   Bu örnek için şu özellikler verilmiştir:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Tablo adı** | Evet | Kaydın güncelleştirilmesi gereken tablo, örneğin "alan" |
   | **Satır KIMLIĞI** | Evet | Güncelleştirilecek kaydın KIMLIĞI, örneğin "99999" |
   | **Alan KIMLIĞI** | Evet | Yeni alan KIMLIĞI ("99999" gibi) |
   | **Alan açıklaması** | Evet | Yeni alan açıklaması ("güncelleştirilmiş 99999" gibi) |
   | **Bölge KIMLIĞI** | Evet | Yeni bölge KIMLIĞI ("102" gibi) |
   ||||

   Örneğin:

   ![Tablo seçin](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="view-update-row-outputs"></a>Güncelleştirme satırı çıkışlarını görüntüle

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Satırı Güncelleştir** eylemini genişletin.

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıkışlar, belirtilen tablonuzda güncelleştirdiğiniz kaydı içerir.

   ![Güncelleştirilmiş satır ile çıktıyı görüntüleme](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Satırı sil

Bir DB2 veritabanı tablosundan tek bir kaydı silmek için, mantıksal uygulamanızdaki **satırı sil** eylemini kullanın. Bu eylem, örneğin, `DELETE` `DELETE FROM AREA WHERE AREAID = '99999'`bir DB2 ekstresi çalıştırır.

1. Mantıksal uygulamanızda daha önce DB2 eylemleri kullanmadıysanız, [DB2 eylem-tabloları al](#add-db2-action) bölümündeki adımları gözden geçirin, ancak bunun yerine **satırı sil** eylemini ekleyin ve ardından devam etmek için buraya dönün.

   **Satırı sil** eylemini ekledikten sonra örnek mantıksal uygulamanız şöyle görünür:

   ![Satır silme eylemi](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Gerekli tüm özellikler için değerleri belirtin (*). Bir tablo seçtikten sonra, eylem söz konusu tablodaki kayıtlara özgü ilgili özellikleri gösterir.

   Bu örnek için şu özellikler verilmiştir:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Tablo adı** | Evet | Kaydın silineceği tablo, örneğin "alan" |
   | **Satır KIMLIĞI** | Evet | Silinecek kaydın KIMLIĞI, örneğin "99999" |
   ||||

   Örneğin:

   ![Tablo seçin](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="view-delete-row-outputs"></a>Satır silme çıkışlarını görüntüle

Mantıksal uygulamanızı el ile çalıştırmak için tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanız çalışmayı bitirdikten sonra, çalıştırmadan çıktıyı görüntüleyebilirsiniz.

1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

1. **Özet**altında, çalışma **geçmişi** bölümünde, listedeki ilk öğe olan en son çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırması**altında artık mantıksal uygulamanızdaki her adımın durumunu, girişlerini ve çıkışlarını gözden geçirebilirsiniz.
**Satırı sil** eylemini genişletin.

1. Girişleri görüntülemek için **Ham girdileri göster**' i seçin.

1. Çıkışları görüntülemek için **Ham çıkışları göster**' i seçin.

   Çıkışlar artık belirtilen tablodan sildiğiniz kaydı içermez.

   ![Çıktıyı silinen satır olmadan görüntüle](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/db2/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
