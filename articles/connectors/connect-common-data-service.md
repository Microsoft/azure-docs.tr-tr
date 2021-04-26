---
title: Common Data Service bağlanma (Microsoft Datadize)
description: Azure Logic Apps kullanarak Common Data Service (Microsoft Dataverse) kayıtları oluşturun ve yönetin
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382369"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Common Data Service (Microsoft Dataverse) içinde Azure Logic Apps kullanarak kayıt oluşturma ve yönetme

> [!NOTE]
> Kasım 2020 ' de, Common Data Service Microsoft veri deposu olarak yeniden adlandırıldı.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Common Data Service Bağlayıcısı](/connectors/commondataservice/)sayesinde, [artık Microsoft datadize veritabanı Common Data Service,](/powerapps/maker/common-data-service/data-platform-intro) kayıtlarını yöneten otomatik iş akışları oluşturabilirsiniz. Bu iş akışları kayıt oluşturabilir, kayıtları güncelleştirebilir ve diğer işlemleri gerçekleştirebilir. Ayrıca, veri deposu veritabanınızın bilgilerini alabilir ve çıktıyı mantıksal uygulamanızda kullanmak üzere diğer eylemler için kullanılabilir hale getirebilirsiniz. Örneğin, veri deposu veritabanınızda bir kayıt güncelleştirilirse, Office 365 Outlook bağlayıcısını kullanarak e-posta gönderebilirsiniz.

Bu makalede, her yeni bir müşteri adayı kaydı oluşturulduğunda görev kaydı oluşturan bir mantıksal uygulamayı nasıl oluşturabileceğiniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Kuruluşunuzun iş verilerini ve bir Common Data Service veritabanını depoladığı, yönettiği ve paylaştığı bir alan olan [Common Data Service ortamı](/power-platform/admin/environments-overview). Daha fazla bilgi için şu kaynaklara bakın:<p>

  * [Öğrenin: Common Data Service kullanmaya başlayın](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power platform-ortamlara genel bakış](/power-platform/admin/environments-overview)

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) ve veri deposu veritabanınızdaki kayıtlara erişmek istediğiniz mantıksal uygulama hakkında temel bilgi. Mantıksal uygulamanızı bir Common Data Service tetikleyicisi ile başlatmak için boş bir mantıksal uygulama gerekir. Azure Logic Apps yeni başladıysanız hızlı başlangıç ' i inceleyin [: Azure Logic Apps kullanarak ilk iş akışınızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Common Data Service tetikleyicisi Ekle

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Bu örnek için yeni bir kayıt oluşturulduğunda harekete gelen Common Data Service tetikleyiciyi ekleyin.

1. [Azure Portal](https://portal.azure.com), henüz açık değilse, mantıksal uygulama Tasarımcısı 'nda boş mantıksal uygulamanızı açın.

1. Arama kutusuna `common data service` yazın. Bu örnekte, Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **bir kayıt oluşturulduğunda**

   !["Bir kayıt oluşturulduğunda" tetikleyicisi seçin](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. İstenirse Common Data Service oturum açın.

1. Tetikleyicide yeni "müşteri adayları" kayıtlarını izlemek istediğiniz ortam hakkında bilgi sağlayın; örneğin:

   ![Ortamın izlemeye yönelik bilgileri Tetikle](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Ortam** | Yes | İzlenecek ortam, örneğin, "Fabrikam Sales Production". Daha fazla bilgi için bkz. [Power platform-ortamlara genel bakış](/power-platform/admin/environments-overview). |
   | **Varlık adı** | Yes | İzlenecek varlık, örneğin "müşteri adayları" |
   | **Kapsam** | Yes | Yeni kaydı oluşturan kaynak (örneğin, iş biriminizdeki bir kullanıcı veya kuruluşunuzdaki herhangi bir Kullanıcı). Bu örnekte "Iş birimi" kullanılmaktadır. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service eylem Ekle

Şimdi yeni "müşteri adayları" kaydı için görev kaydı oluşturan bir Common Data Service eylemi ekleyin.

1. **Bir kayıt oluşturulduğunda** tetikleyicisi altında **yeni adım**' ı seçin.

1. Arama kutusuna `common data service` yazın. Eylemler listesinden şu eylemi seçin: **Yeni bir kayıt oluştur**

   !["Yeni kayıt oluştur" eylemini seçin](./media/connect-common-data-service/select-create-new-record-action.png)

1. Eylemde, yeni görev kaydını oluşturmak istediğiniz ortam hakkındaki bilgileri belirtin. Varsa, diğer özellikler bu eylem için seçtiğiniz varlığa göre de görüntülenir, örneğin:

   ![Kaydın oluşturulacağı ortam için eylem bilgileri](./media/connect-common-data-service/create-new-record-action-details.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Kuruluş Adı** | Yes | Kayıt oluşturmak istediğiniz, tetikleyicinizdeki aynı ortam olması gereken ancak bu örnekte "Fabrikam Sales Production" olması gereken ortam |
   | **Varlık adı** | Yes | Kaydı oluşturmak istediğiniz varlık (örneğin, "görevler" |
   | **Konu** | Evet, bu örnekte seçilen varlığa göre | Bu görevin hedefi hakkında kısa bir açıklama |
   ||||

   1. **Konu** özelliği için bu metni sonundaki bir boşluk ile girin:

      `Follow up with new lead:`

   1. Dinamik içerik listesinin görünür kalması için işaretçinizi **Konu** kutusunun içinde tutun.
   
   1. Listede, **bir kayıt oluşturulduğunda** bölümünde, görev kaydına dahil etmek istediğiniz tetikleme çıkışlarını seçin, örneğin:

      ![Görev kaydında kullanmak için tetikleme çıkışlarını seçin](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Çıkışı Tetikle | Description |
      |----------------|-------------|
      | **Ad** | Görev kaydında birincil kişi olarak kullanılacak müşteri adayı kaydından ilk ad |
      | **Soyadı** | Görev kaydında birincil kişi olarak kullanılacak müşteri adayı kaydından soyadı |
      | **Açıklama** | E-posta adresi ve iş telefonu numarası gibi görev kaydına dahil edilecek diğer çıktılar |
      |||

   İşiniz bittiğinde, eylem aşağıdaki örnekte görünebilir:

   !["Yeni kayıt oluştur" eylemi bitti](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

1. Mantıksal uygulamayı el ile başlatmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Mantıksal uygulamanızı test etmek için yeni bir "müşteri adayları" kaydı oluşturun.

## <a name="trigger-only-on-updated-attributes"></a>Yalnızca güncelleştirilmiş özniteliklerde Tetikle

Kayıtlar güncelleştirilirken çalışan Tetikleyiciler (örneğin, **bir kayıt** güncelleştirilirken), mantıksal uygulamanızın yalnızca belirtilen öznitelikler güncelleştirilirken çalışması için filtre öznitelikleri kullanabilirsiniz. Bu özellik, gereksiz mantıksal uygulama çalıştırmalarını önlemenize yardımcı olur.

1. Tetikleyicide, **yeni parametre Ekle** listesinden **öznitelik filtreleri**' ni seçin.

   !["Bir kayıt güncelleştirilirken" eylemi ve "öznitelik filtreleri" özelliği seçiliyken açılan "yeni parametre Ekle" listesini gösteren ekran görüntüsü.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Her **öznitelik filtresi öğesi** için, güncelleştirmeler için izlemek istediğiniz özniteliği seçin, örneğin:

   !["Öznitelik filtreleri" özelliği Ekle](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Kayıtları bir filtreye göre listeleme

Kayıtları **Listele** eylemi gibi kayıtları döndüren eylemler için, belirtilen filtreye göre kayıtları döndüren bir OData sorgusu kullanabilirsiniz. Örneğin, eylem listesini yalnızca etkin hesapların kayıtlarını görürsünüz.

1. Eylemde **yeni parametre Ekle** listesini açın ve **filtre sorgusu** özelliğini seçin.

   !["Filtre sorgusu" özelliği Ekle](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Eylemde şimdi görüntülenen **filtre sorgusu** ÖZELLIĞINDE şu OData filtre sorgusunu girin: `statuscode eq 1`

   ![Kayıtları filtrelemek için ODATA filtre sorgusu girin](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Sistem sorgu seçenekleri hakkında daha fazla bilgi için `$filter` bkz. [Common Data Service-filtre sonuçları](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Kayıtları bir sıraya göre listeleme

Kayıtları **Listele** eylemi gibi kayıtları döndüren eylemler için, belirli bir sırada kayıtları döndüren bir OData sorgusu kullanabilirsiniz. Bu, eylemin döndürdüğü kayıtlara göre farklılık gösterir. Örneğin, kayıt listesini hesap adına göre listeler.

1. Eylemde **yeni parametre Ekle** listesini açın ve **order by** özelliğini seçin.

   !["Order by" özelliği Ekle](./media/connect-common-data-service/list-records-action-order-by.png)

1. Eylemde Şu anda görüntülenen **order by** ÖZELLIĞINDE şu OData filtre sorgusunu girin: `name`

   ![Kayıtları sıralamak için ODATA filtre sorgusu girin](./media/connect-common-data-service/list-records-action-order-by-value.png)

Sistem sorgu seçenekleri hakkında daha fazla bilgi için `$orderby` bkz. [Common Data Service-Order results](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Alan veri türleri

Bir tetikleyici veya eylem içindeki bir alan için el ile değer girip girmeyeceğinizi ya da dinamik içerik listesinden bir değer seçip seçmeksizin, değerin veri türü alanın gerekli veri türüyle eşleşmelidir.

Bu tabloda bazı alan türleri ve bu alanların değerleri için gereken veri türleri açıklanmaktadır.

| Alan | Veri türü | Açıklama |
|-------|-----------|-------------|
| Metin alanı | Tek satırlı metin | Metin veri türüne sahip tek satırlık bir metin veya dinamik içerik gerektirir, örneğin, bu özellikler: <p><p>- **Açıklaması** <br>- **Alan** |
| Tamsayı alanı | Tam sayı | Tamsayı veri türüne sahip bir tamsayı veya dinamik içerik gerektirir, örneğin, bu özellikler: <p><p>- **Tamamlanma yüzdesi** <br>- **Sürenin** |
| Tarih alanı | Tarih ve Saat | AA/gg/YYY biçiminde veya tarih veri türüne sahip dinamik içerikte bir tarih gerektirir, örneğin, bu özellikler: <p><p>- **Oluşturulma tarihi** <br>- **Başlangıç tarihi** <br>- **Gerçek başlangıç** <br>- **Gerçek bitiş** <br>- **Son Tarih** |
| Başka bir varlık kaydına başvuran alan | Birincil anahtar | GUID gibi bir kayıt KIMLIĞI ve bir arama türü gerektirir, bu da dinamik içerik listesindeki değerlerin, örneğin, bu özellikler gibi çalışmadıkları anlamına gelir: <p><p>- **Sahip**: geçerli BIR kullanıcı kimliği veya bir takım kayıt kimliği olmalıdır. <br>- **Sahip türü**: sırasıyla veya gibi bir arama türü olmalıdır `systemusers` `teams` . <p><p>- **İlgili**: hesap kimliği veya ilgili kışı kaydı kimliği gibi geçerli BIR kayıt kimliği olmalıdır. <br>- **Ilgili tür**: sırasıyla veya gibi bir arama türü olmalıdır `accounts` `contacts` . <p><p>- **Müşteri**: hesap kimliği veya ilgili kışı kaydı kimliği gibi geçerli BIR kayıt kimliği olmalıdır. <br>- **Müşteri türü**: sırasıyla veya gibi arama türü olmalıdır `accounts` `contacts` . |
||||

Bu örnek, yeni bir **kayıt oluşturma** eyleminin diğer varlık kayıtlarıyla, özellikle de bir kullanıcı kaydıyla ve bir hesap kaydıyla ilişkili yeni bir "görevler" kaydı nasıl oluşturduğunu gösterir. Eylem, ilgili özellikler için beklenen veri türleriyle eşleşen değerleri kullanarak bu varlık kayıtlarının kimliklerini ve arama türlerini belirtir.

* Bir kullanıcı KIMLIĞI belirten **Owner** özelliği ve arama türünü belirten **Owner türü** özelliği temel alınarak `systemusers` , eylem yeni "görevler" kaydını belirli bir kullanıcıyla ilişkilendirir.

* Bir kayıt KIMLIĞI belirten **ilgili** özelliğine ve arama türünü belirten **ilgili tür** özelliğine dayalı olarak, `accounts` eylem yeni "görevler" kaydını belirli bir hesapla ilişkilendirir.

![Kimlikler ve arama türleriyle ilişkili "görevler" kaydını oluştur](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcı, Eylemler, sınırlar ve diğer ayrıntılar gibi bağlayıcının Swagger açıklamasına dayalı teknik bilgiler için [bağlayıcının başvuru sayfasına](/connectors/commondataservice/)bakın.

## <a name="troubleshooting-problems"></a>Sorun giderme sorunları

### <a name="calls-from-multiple-environments"></a>Çoklu ortamların çağrıları

Her iki bağlayıcı, Common Data Service ve Common Data Service (geçerli ortam), `callbackregistrations` Microsoft veri dizinizdeki varlığı kullanarak varlık değişiklikleri gerektiren mantıksal uygulama iş akışlarıyla ilgili bilgileri depolar. Bir veri deposu organizasyonunu kopyalarsanız, tüm Web kancaları da kopyalanır. Kuruluşunuza eşlenmiş iş akışlarını devre dışı bırakmadan önce kuruluşunuzu kopyalarsanız, tüm kopyalanmış Web kancaları aynı Logic Apps 'e işaret ettikten ve bu da birden çok kuruluştan gelen bildirimleri alır.

İstenmeyen bildirimleri durdurmak için aşağıdaki adımları izleyerek bu bildirimleri gönderen kuruluştan geri çağırma kaydını silin:

1. Bildirimleri kaldırmak istediğiniz veri deposu organizasyonunu ve bu kuruluşta oturum açın.

1. Chrome tarayıcısında, aşağıdaki adımları izleyerek silmek istediğiniz geri çağırma kaydını bulun:

   1. Varlığın içindeki verileri görüntüleyebilmeniz için aşağıdaki OData URI 'sindeki tüm geri çağırma kayıtlarının genel listesini gözden geçirin `callbackregistrations` :

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > Hiçbir değer döndürülmezse, bu varlık türünü görüntüleme izinleriniz olmayabilir veya doğru kuruluşta oturum açmadınız olabilirsiniz.

   1. Tetikleyici varlığın mantıksal adına `entityname` ve mantıksal uygulama iş akışınız (ileti) ile eşleşen bildirim olayına filtre uygulayın. Her olay türü ileti tamsayıya aşağıdaki gibi eşlenir:

      | Olay türü | İleti tamsayısı |
      |------------|-----------------|
      | Oluştur | 1 |
      | Sil | 2 |
      | Güncelleştir | 3 |
      | CreateOrUpdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      Bu örnek `Create` `nov_validation` , örnek bir kuruluş Için AŞAĞıDAKI OData URI 'sini kullanarak adlı bir varlıkta bildirimleri nasıl filtreleyekullanabileceğinizi gösterir:

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![Adres çubuğunda tarayıcı penceresini ve OData URI 'yi gösteren ekran görüntüsü.](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > Aynı varlık veya olay için birden çok tetikleyici varsa, ve öznitelikleri gibi ek filtreler kullanarak listeyi filtreleyebilirsiniz `createdon` `_owninguser_value` . Sahibinin Kullanıcı adı altında görünür `/api/data/v9.0/systemusers({id})` .

   1. Silmek istediğiniz geri çağırma kaydının KIMLIĞINI bulduktan sonra aşağıdaki adımları izleyin:
   
      1. Chrome tarayıcınızda Chrome Geliştirici Araçları açın (klavye: F12).

      1. Pencerede, üst kısımdaki **konsol** sekmesini seçin.

      1. Komut satırı isteminde, belirtilen geri çağırma kaydını silmek için bir istek gönderen şu komutu girin:

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > İstek temelli olmayan bir Istemci arabirimi (UI) sayfasından (örneğin, OData veya API yanıt sayfasının kendisinden) istekte bulunduğundan emin olun. Aksi takdirde, app.js dosyasındaki mantık bu işlemi etkileyebilir.

   1. Geri çağırma kaydının artık mevcut olmadığını doğrulamak için geri çağırma kayıtları listesini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps için diğer bağlayıcılar](../connectors/apis-list.md) hakkında bilgi edinin
