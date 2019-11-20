---
title: SQL Server veya Azure SQL veritabanı 'na bağlanma-Azure Logic Apps
description: Azure Logic Apps kullanarak şirket içinde veya bulutta SQL veritabanları için görevleri otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 11/08/2019
ms.openlocfilehash: a6367e5897e9bd548550b099c0bd2e6186845d6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826318"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>SQL Server veya Azure SQL veritabanı için iş akışlarını Azure Logic Apps kullanarak otomatikleştirin

Bu makalede, SQL veritabanınızdaki verilere SQL Server Bağlayıcısı ile bir mantıksal uygulama içinden nasıl erişebileceğiniz gösterilmektedir. Bu şekilde, mantıksal uygulamalar oluşturarak SQL verilerinizi ve kaynaklarınızı yöneten görevleri, işlemleri veya iş akışlarını otomatikleştirebileceğinizi unutmayın. SQL Server bağlayıcısı hem [Şirket içi SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) hem de [bulut tabanlı Azure SQL veritabanı](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)için geçerlidir.

SQL veritabanınızda veya Dynamics CRM Online gibi diğer sistemlerde olaylar tarafından tetiklendiğinde çalışan Logic Apps oluşturabilirsiniz. Mantıksal uygulamalarınız, çalışan SQL sorguları ve saklı yordamlar ile birlikte verileri de alabilir, ekleyebilir ve silebilir. Örneğin, Dynamics CRM Online 'da yeni kayıtları otomatik olarak denetleyen bir mantıksal uygulama oluşturabilir, her yeni kayıt için SQL veritabanınıza öğe ekler ve ardından eklenen öğeler hakkında e-posta uyarıları gönderir.

Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin. Bağlayıcıya özgü teknik bilgiler, sınırlamalar ve bilinen sorunlar için, [SQL Server Bağlayıcısı başvuru sayfasına](https://docs.microsoft.com/connectors/sql/)bakın.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [SQL Server veritabanı](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) veya [Azure SQL veritabanı](../sql-database/sql-database-get-started-portal.md)

  Mantıksal uygulamanızın işlemleri çağırırken sonuçlar döndürebilmesi için tablolarınızın verileri olmalıdır. Azure SQL veritabanı oluşturursanız, dahil edilen örnek veritabanlarını kullanabilirsiniz.

* SQL sunucunuzun adı, veritabanı adı, Kullanıcı adınız ve parolanız. Mantığınızı SQL Server 'a erişim için yetkilendirebilmeniz için bu kimlik bilgilerine ihtiyacınız vardır.

  * SQL Server için bu ayrıntıları bağlantı dizesinde bulabilirsiniz:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL veritabanı için bu ayrıntıları bağlantı dizesinde veya SQL veritabanı özellikleri altındaki Azure portal bulabilirsiniz:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Yerel bir bilgisayarda yüklü [olan şirket içi veri ağ geçidi](../logic-apps/logic-apps-gateway-install.md) ve bu senaryolar için [Azure Portal oluşturulan bir Azure veri ağ geçidi kaynağı](../logic-apps/logic-apps-gateway-connection.md) :

  * Logic Apps, bir [tümleştirme hizmeti ortamında (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)çalışmaz.

  * Mantıksal uygulamalarınız bir tümleştirme hizmeti *ortamında çalışır,* ancak SQL Server bağlantınız için Windows kimlik doğrulamasını kullanmanız gerekir. Bu senaryo için, ıSE sürümü Windows kimlik doğrulamasını desteklemediğinden, SQL Server bağlayıcısının ıSE sürümünü veri ağ geçidiyle birlikte kullanın.

* SQL veritabanınıza erişmeniz gereken mantıksal uygulama. Mantıksal uygulamanızı bir SQL tetikleyicisi ile başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL tetikleyicisi ekleme

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#logic-app-concepts)başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve mantıksal uygulamanızın iş akışını çalıştırmaya başlar.

1. Azure portal veya Visual Studio 'da, Logic Apps tasarımcısını açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

1. Tasarımcıda arama kutusuna filtreniz olarak "SQL Server" yazın. Tetikleyiciler listesinden istediğiniz SQL tetikleyicisini seçin.

   Bu örnek, **bir öğe oluşturulduğunda** tetikleyicisi kullanır.

   !["Bir öğe oluşturulduğunda" tetikleyicisi seçin](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Bir bağlantı oluşturmanız istenirse, [SQL bağlantınızı şimdi oluşturun](#create-connection). Bağlantınız varsa, bir **tablo adı**seçin.

   ![İstediğiniz tabloyu seçin](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Mantıksal uygulamanızın tabloyu ne sıklıkta denetleyeceğini belirten **Aralık** ve **Sıklık** özelliklerini ayarlayın.

   Bu tetikleyici seçili tablodan yalnızca bir satır döndürür, başka hiçbir şey yapmaz. Diğer görevleri gerçekleştirmek için, istediğiniz görevleri gerçekleştiren diğer eylemleri ekleyin. Örneğin, bu satırdaki verileri görüntülemek için döndürülen satırdaki alanları içeren bir dosya oluşturan başka eylemler ekleyebilir ve ardından e-posta uyarıları gönderebilirsiniz. Bu bağlayıcıya yönelik diğer mevcut eylemler hakkında bilgi edinmek için [bağlayıcının başvuru sayfasına](https://docs.microsoft.com/connectors/sql/)bakın.

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Bu adım otomatik olarak Azure 'da mantıksal uygulamanızı etkin bir şekilde etkinleştirilir ve yayımlar.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL eylemi ekleme

Azure Logic Apps bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , iş akışınızda bir tetikleyiciyi veya başka bir eylemi izleyen bir adımdır. Bu örnekte, mantıksal uygulama [yinelenme tetikleyicisiyle](../connectors/connectors-native-recurrence.md)başlar ve bir SQL veritabanından bir satır alan bir eylem çağırır.

1. Azure portal veya Visual Studio 'da mantıksal uygulamanızı Logic Apps tasarımcısında açın. Bu örnek Azure portal kullanır.

1. SQL eylemini eklemek istediğiniz tetikleyici veya eylem altında **yeni adım**' ı seçin.

   ![Mantıksal uygulamanıza yeni adım ekleme](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Varolan adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "SQL Server" yazın. Eylemler listesinden istediğiniz SQL eylemini seçin.

   Bu örnek, tek bir kayıt alan **satırı al** eylemini kullanır.

   ![SQL "satırı al" eylemini bul ve Seç](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Bu eylem, seçili tablodan yalnızca bir satır döndürür, başka hiçbir şey yapmaz. Bu satırdaki verileri görüntülemek için döndürülen satırdaki alanları içeren bir dosya oluşturan ve bu dosyayı bir bulut depolama hesabında depolayabilen başka eylemler ekleyebilirsiniz. Bu bağlayıcıya yönelik diğer mevcut eylemler hakkında bilgi edinmek için [bağlayıcının başvuru sayfasına](https://docs.microsoft.com/connectors/sql/)bakın.

1. Bir bağlantı oluşturmanız istenirse, [SQL bağlantınızı şimdi oluşturun](#create-connection). Bağlantınız varsa, bir **tablo adı**seçin ve Istediğiniz KAYDıN **satır kimliğini** girin.

   ![Tablo adını ve satır KIMLIĞINI girin](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Bu adım otomatik olarak Azure 'da mantıksal uygulamanızı etkin bir şekilde etkinleştirilir ve yayımlar.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Veritabanınıza bağlanın

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Toplu verileri işle

Bazen, bağlayıcının tüm sonuçları aynı anda döndürmemesi veya sonuç kümelerinizin boyutu ve yapısı üzerinde daha iyi denetim sağlamak istiyorsanız sonuç kümeleriyle çalışmanız gerekir. Bu tür büyük sonuç kümelerini işleyebilmeniz için bazı yollar şunlardır:

* Sonuçları daha küçük kümeler olarak yönetmenize yardımcı olmak için *sayfalandırma*'yı açın. Daha fazla bilgi için bkz. [sayfalandırma kullanarak toplu verileri, kayıtları ve öğeleri edinme](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Sonuçları istediğiniz şekilde düzenleyen bir saklı yordam oluşturun.

  Birden çok satır alırken veya eklerken, mantıksal uygulamanız bu [sınırlar](../logic-apps/logic-apps-limits-and-config.md)dahilinde bir [*until döngüsü*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) kullanarak bu satırları yineleyebilir. Ancak, mantıksal uygulamanızın kayıt kümeleriyle birlikte çalışması gerektiğinde, büyük bir deyişle, binlerce veya milyonlarca satır olmak üzere veritabanına yapılan çağrılardan elde edilen maliyetleri en aza indirmek istersiniz.

  Sonuçları istediğiniz şekilde düzenlemek için, SQL örneğiniz içinde çalışan bir [*saklı yordam*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) oluşturabilir ve **Select-order by** ifadesini kullanabilirsiniz. Bu çözüm, sonuçlarınızın boyutu ve yapısı üzerinde daha fazla denetim sağlar. Mantıksal uygulamanız, SQL Server bağlayıcısının **saklı yordamı Çalıştır** eylemini kullanarak saklı yordamı çağırır.

  Daha fazla çözüm ayrıntısı için şu makalelere bakın:

  * [Logic Apps ile toplu veri aktarımı için SQL sayfalandırma](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY yan tümcesi](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Bağlayıcıya özgü ayrıntılar

Bu bağlayıcının Tetikleyicileri, eylemleri ve limitleri hakkında teknik bilgi için [bağlayıcının başvuru sayfasına](https://docs.microsoft.com/connectors/sql/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps için diğer bağlayıcılar](../connectors/apis-list.md) hakkında bilgi edinin
