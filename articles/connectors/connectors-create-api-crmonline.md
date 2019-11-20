---
title: Dynamics 365 'e bağlanma-Azure Logic Apps
description: Dynamics 365 (çevrimiçi) REST API 'Leri ve Azure Logic Apps kayıt oluşturma ve yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051016"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Azure Logic Apps ile Dynamics 365 kayıtlarını yönetme

Azure Logic Apps ve Dynamics 365 bağlayıcısı sayesinde, Dynamics 365’teki kayıtlarınızı temel alarak otomatik görevler ve iş akışları oluşturabilirsiniz. İş akışlarınız, Dynamics 365 hesabınızda kayıtlar oluşturabilir, öğeleri güncelleştirebilir, kayıtlar döndürebilir ve daha fazlasını yapabilir. Mantıksal uygulamalarınıza, Dynamics 365 ' den yanıt alan ve çıktıyı diğer eylemler için kullanılabilir hale getirmek için kullanabileceğiniz eylemler ekleyebilirsiniz. Örneğin, Dynamics 365 ' de bir öğe güncelleştirildiği zaman Office 365 kullanarak bir e-posta gönderebilirsiniz.

Bu makalede, Dynamics 365 ' de yeni bir müşteri adayı kaydı oluşturulduğunda Dynamics 365 ' de bir görev oluşturan mantıksal uygulamayı nasıl oluşturabileceğiniz gösterilmektedir.
Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps? ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Dynamics 365 hesabı](https://dynamics.microsoft.com)

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Dynamics 365 hesabınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı Dynamics 365 tetikleyicisi ile başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365 tetikleyicisi Ekle

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

İlk olarak, Dynamics 365 ' de yeni bir müşteri adayı kaydı göründüğünde harekete çıkan bir Dynamics 365 tetikleyicisi ekleyin.

1. [Azure Portal](https://portal.azure.com), henüz açık değilse, mantıksal uygulama Tasarımcısı 'nda boş mantıksal uygulamanızı açın.

1. Arama kutusuna filtreniz olarak "Dynamics 365" yazın. Bu örnekte, Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **Bir kayıt oluşturulduğunda**

   ![Tetikleyici seçin](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Dynamics 365 ' de oturum açmanız istenirse, şimdi oturum açın.

1. Bu tetikleyici ayrıntılarını belirtin:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Kuruluş adı** | Evet | Kuruluşunuzun Dynamics 365 örneğinin izlenecek adı, örneğin "contoso" |
   | **Varlık adı** | Evet | İzlenecek varlığın adı, örneğin "müşteri adayları" | 
   | **Sıklık** | Evet | Tetikleyiciyle ilgili güncelleştirmeler denetlenirken zaman aralıklarıyla kullanılacak zaman birimi |
   | **Aralık** | Evet | Sonraki denetim öncesinde geçen saniye, dakika, saat, gün, hafta veya ay sayısı |
   ||| 

   ![Tetikleyici ayrıntıları](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365 eylemi ekleme

Şimdi yeni müşteri adayı kaydı için bir görev kaydı oluşturan Dynamics 365 eylemini ekleyin.

1. Tetikleyicinizin altında **yeni adım**' ı seçin.

1. Arama kutusuna filtreniz olarak "Dynamics 365" yazın. Eylemler listesinden şu eylemi seçin: **Yeni bir kayıt oluştur**

   ![Eylem Seç](./media/connectors-create-api-crmonline/select-action.png)

1. Bu eylem ayrıntılarını belirtin:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Kuruluş adı** | Evet | Kayıt oluşturmak istediğiniz, tetikleyicinizde aynı örnek olması gerektirmeyen, ancak bu örnekte "contoso" olan Dynamics 365 örneği |
   | **Varlık adı** | Evet | Kaydı oluşturmak istediğiniz varlık (örneğin, "görevler" |
   | | |

   ![Eylem ayrıntıları](./media/connectors-create-api-crmonline/action-details.png)

1. Eylemde **Konu** kutusu belirdiğinde, dinamik içerik listesinin görünmesi için **Konu** kutusunun içine tıklayın. Bu listeden, yeni müşteri adayı kaydıyla ilişkili görev kaydına dahil edilecek alan değerlerini seçin:

   | Alan | Açıklama |
   |-------|-------------|
   | **Soyadı** | Kayıttaki birincil kişi olarak müşteri adayının son adı |
   | **Konu** | Kayıttaki müşteri adayının açıklayıcı adı |
   | | |

   ![Görev kaydı ayrıntıları](./media/connectors-create-api-crmonline/create-record-details.png)

1. Tasarımcı araç çubuğunda mantıksal uygulamanız için **Kaydet** ' i seçin. 

1. Mantıksal uygulamayı el ile başlatmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   ![Mantık uygulaması çalıştır](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Şimdi Dynamics 365 ' de bir müşteri adayı kaydı oluşturarak mantıksal uygulamanızın iş akışını tetikleyebilirsiniz.

## <a name="add-filter-or-query"></a>Filtre veya sorgu Ekle

Bir Dynamics 365 eyleminde verilerin nasıl filtreleneceğini belirtmek için bu eylemde **Gelişmiş seçenekleri göster** ' i seçin. Ardından sorguya göre bir filtre veya sıralama ekleyebilirsiniz.
Örneğin, yalnızca etkin hesapları almak ve bu kayıtları hesap adına göre sıralamak için bir filtre sorgusu kullanabilirsiniz. Bu görev için şu adımları izleyin:

1. **Filtre sorgusu**altında şu OData filtre sorgusunu girin:`statuscode eq 1`

2. **Sıralama ölçütü**altında, dinamik içerik listesi göründüğünde **Hesap adı**' nı seçin. 

   ![Filtre ve siparişi belirtin](./media/connectors-create-api-crmonline/advanced-options.png)

Daha fazla bilgi için, bkz. bu Dynamics 365 müşteri katılımı Web API 'SI sistem sorgu seçenekleri:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Gelişmiş seçenekler için en iyi uygulamalar

Bir eylem veya tetikleyici içindeki bir alan için bir değer belirttiğinizde, değeri el ile girip girmeyeceğinizi veya dinamik içerik listesinden değeri seçip değerin veri türü alan türüyle aynı olmalıdır.

Bu tabloda, değerleri için bazı alan türleri ve gerekli veri türleri açıklanmaktadır.

| Alan türü | Gerekli veri türü | Açıklama | 
|------------|--------------------|-------------|
| Metin alanları | Tek satırlık metin | Bu alanlar, metin türüne sahip tek satırlık bir metin veya dinamik içerik gerektirir. <p><p>*Örnek alanlar*: **Açıklama** ve **Kategori** | 
| Tamsayı alanları | Tam sayı | Bazı alanlar tamsayı türüne sahip tamsayı veya dinamik içerik gerektirir. <p><p>*Örnek alanlar*: Tamamlanma **yüzdesi** ve **süre** | 
| Tarih alanları | Tarih ve saat | Bazı alanlar aa/gg/yyyy biçiminde bir tarih veya tarih türüne sahip dinamik içerik gerektirir. <p><p>*Örnek alanlar*: **Oluşturulma**tarihi, **Başlangıç tarihi**, **gerçek başlangıç**, **gerçek bitiş**ve **son tarih** | 
| Kayıt KIMLIĞI ve arama türü gerektiren alanlar | Birincil anahtar | Başka bir varlık kaydına başvuruda bulunan bazı alanlar, hem kayıt KIMLIĞI hem de arama türü gerektirir. | 
||||

Bu alan türlerini genişleterek, Dynamics 365 tetikleyicilerinde ve hem kayıt KIMLIĞI hem de arama türü gerektiren eylemlerde örnek alanlar aşağıda verilmiştir. Bu gereksinim, dinamik listeden seçtiğiniz değerlerin işe gerekmediği anlamına gelir.

| Alan | Açıklama |
|-------|-------------|
| **Sahip** | Geçerli bir kullanıcı KIMLIĞI ya da takım kayıt KIMLIĞI olmalıdır. |
| **Sahip türü** | `systemusers` Ya`teams`da olmalıdır. |
| **İnizle** | Hesap KIMLIĞI veya ilgili kişi kaydı KIMLIĞI gibi geçerli bir kayıt KIMLIĞI olmalıdır. |
| **İlgili tür** | , `accounts` Veya`contacts`gibi bir arama türü olmalıdır. |
| **Müşteri** | Hesap KIMLIĞI veya ilgili kişi kaydı KIMLIĞI gibi geçerli bir kayıt KIMLIĞI olmalıdır. |
| **Müşteri türü** | , `accounts` Veya`contacts`gibi arama türü olmalıdır. |
|||

Bu örnekte, **Yeni bir kayıt oluştur** adlı eylem yeni bir görev kaydı oluşturur:

![Kayıt kimlikleri ve arama türleriyle görev kaydı oluşturma](./media/connectors-create-api-crmonline/create-record-advanced.png)

Bu eylem, görev kaydını, **sahip** ALANıNDAKI kayıt kimliğine ve **sahip türü** alanındaki arama türüne göre belirli bir Kullanıcı KIMLIĞINE veya takım kayıt kimliğine atar:

![Sahip kayıt KIMLIĞI ve arama türü](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Bu eylem ayrıca ilgili alanına eklenen kayıt KIMLIĞIYLE ilişkili hesap kaydını ve **Ilgili tür** alanındaki arama türünü de ekler:

![Kayıt KIMLIĞI ve arama türü ile ilgili](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Kayıt KIMLIĞINI bul

Bir kayıt KIMLIĞI bulmak için şu adımları izleyin:

1. Dynamics 365 ' de, hesap kaydı gibi bir kayıt açın.

2. Eylemler araç çubuğunda şu adımlardan birini seçin:

   * **Açılan pencereyi**seçin. ![öne kaydı](./media/connectors-create-api-crmonline/popout-record.png) 
   * Tam URL 'yi varsayılan e-posta programınıza kopyalayabilmeniz için **BIR bağlantıyı e-posta gönder** ' i seçin.

   Kayıt kimliği, `%7b` ve `%7d` kodlama karakterleri arasında URL 'de görünür:

   ![Kayıt KIMLIĞINI bul](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Başarısız çalıştırmalar sorunlarını giderme

Mantıksal uygulamanızda başarısız olan adımları bulmak ve gözden geçirmek için mantıksal uygulamanızın çalışma geçmişi, durumu, girişleri, çıkışları vb. görüntüleyebilirsiniz.

1. Azure portal, mantıksal uygulamanızın ana menüsünde **genel bakış**' ı seçin. Mantıksal uygulamanıza yönelik tüm çalıştırma durumlarını gösteren çalışma **geçmişi** bölümünde, daha fazla bilgi için başarısız bir çalıştırma seçin.

   ![Mantıksal uygulama çalıştırma durumu](./media/connectors-create-api-crmonline/run-history.png)

1. Daha fazla ayrıntı görüntüleyebilmeniz için başarısız bir adımı genişletin.

   ![Genişletme başarısız adımı](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Hatanın arkasındaki nedeni bulmanıza yardımcı olabilecek giriş ve çıkış gibi adım ayrıntılarını gözden geçirin.

   ![Başarısız adım-girişler ve çıktılar](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Logic Apps sorunlarını giderme hakkında daha fazla bilgi için bkz. [mantıksal uygulama başarısızlıklarını tanılama](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/dynamicscrmonline/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
