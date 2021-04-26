---
title: Tümleştirme hesabı yapıt meta verilerini Yönet
description: Enterprise Integration Pack ile Azure Logic Apps tümleştirme hesaplarından yapıt meta verileri ekleyin veya alın
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792477"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps ve Enterprise Integration Pack ile tümleştirme hesaplarındaki yapıt meta verilerini yönetme

Tümleştirme hesaplarında yapıtlar için özel meta veriler tanımlayabilir ve bu meta verileri çalışma zamanında alarak mantıksal uygulamanızda kullanabilirsiniz. Örneğin, iş ortakları, sözleşmeler, şemalar ve haritalar gibi yapılar için meta veriler, anahtar-değer çiftleri kullanarak tüm mağaza meta verileri sağlayabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa, <a href="https://azure.microsoft.com/free/" target="_blank">ücretsiz bir Azure hesabı için kaydolun</a>.

* Meta veri eklemek istediğiniz yapıtlara sahip temel bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , örneğin: 

  * [İş Ortağı](logic-apps-enterprise-integration-partners.md)
  * [Sözleşme](logic-apps-enterprise-integration-agreements.md)
  * [Şema](logic-apps-enterprise-integration-schemas.md)
  * [Harita](logic-apps-enterprise-integration-maps.md)

* Kullanmak istediğiniz tümleştirme hesabı ve yapıt meta verileri ile bağlantılı bir mantıksal uygulama. Mantıksal uygulamanız zaten bağlı değilse, [mantıksal uygulamaları tümleştirme hesaplarına bağlamayı](logic-apps-enterprise-integration-create-integration-account.md#link-account)öğrenin. 

  Henüz bir Logic App yoksa [Logic Apps oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)öğrenin. 
  Yapıt meta verilerini yönetmek için kullanmak istediğiniz tetikleyiciyi ve eylemleri ekleyin. Ya da yalnızca bir şeyi denemek için, mantıksal uygulamanıza **istek** veya **http** gibi bir tetikleyici ekleyin.

## <a name="add-metadata-to-artifacts"></a>Yapıtlara meta veriler ekleme

1. Azure hesabınızın kimlik bilgileriyle <a href="https://portal.azure.com" target="_blank">Azure portalında</a> oturum açın. Tümleştirme hesabınızı bulun ve açın.

1. Meta veri eklemek istediğiniz yapıyı seçin ve **Düzenle**' yi seçin. Bu yapıt için meta veri ayrıntılarını girin, örneğin:

   ![Meta verileri girin](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. İşiniz bittiğinde **Tamam**’ı seçin.

1. Bu meta verileri tümleştirme hesabının JavaScript Nesne Gösterimi (JSON) tanımında görüntülemek için JSON Düzenleyicisi 'nin açılması için **JSON olarak Düzenle** ' yi seçin: 

   ![İş ortağı meta verileri için JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Yapıt meta verilerini al

1. Azure portal, istediğiniz tümleştirme hesabıyla bağlantılı mantıksal uygulamayı açın. 

1. Mantıksal uygulama Tasarımcısı ' nda, iş akışındaki tetikleyici veya son eylem altında meta veri alma adımını ekliyorsanız **yeni adım**  >  **Eylem Ekle**' yi seçin. 

1. Arama kutusuna "tümleştirme hesabı" yazın. Arama kutusunda **Tümü**' ni seçin. Eylemler listesinden şu eylemi seçin: **tümleştirme hesabı yapıt arama-tümleştirme hesabı**

   !["Tümleştirme hesabı yapıt araması" öğesini seçin](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Bulmak istediğiniz yapıt için şu bilgileri sağlayın:

   | Özellik | Gerekli | Değer | Açıklama | 
   |----------|---------|-------|-------------| 
   | **Yapıt türü** | Yes | **Şema**, **eşleme**, **iş ortağı**, **anlaşma** veya özel bir tür | İstediğiniz yapıtın türü | 
   | **Yapıt adı** | Yes | <*yapıt-adı*> | İstediğiniz yapıt adı | 
   ||| 

   Örneğin, bir ticaret ortağı yapıtı için meta verileri almak istediğinizi varsayalım:

   ![Yapıt türünü seçin ve yapıt adını girin](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Bu meta verileri işlemek istediğiniz eylemi ekleyin, örneğin:

   1. **Tümleştirme hesabı yapıt arama** eylemi altında, **İleri adım**' ı seçin ve **Eylem Ekle**' yi seçin. 

   1. Arama kutusuna "http" yazın. Arama kutusunda, **yerleşik**' i seçin ve şu eylemi seçin: **http-http**

      ![HTTP eylemi Ekle](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Yönetmek istediğiniz yapıt meta verileri için bilgi sağlayın. 

      Örneğin, `routingUrl` Bu konunun önceki kısımlarında eklenen meta verileri almak istediğinizi varsayalım. Belirtebileceğiniz özellik değerleri şunlardır: 

      | Özellik | Gerekli | Değer | Açıklama | 
      |----------|----------|-------|-------------| 
      | **Yöntem** | Yes | <*işlem-çalıştırma*> | Yapıtın üzerinde çalıştırılacak HTTP işlemi. Örneğin, bu HTTP eylemi **Get** yöntemini kullanır. | 
      | **URI** | Yes | <*meta veri-konum*> | `routingUrl`Alınan yapıtın meta veri değerine erişmek için bir ifade kullanabilirsiniz, örneğin: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Üst Bilgiler** | No | <*üst bilgi-değerler*> | HTTP eylemine geçirmek istediğiniz tetikleyiciden gelen üst bilgi çıkışları. Örneğin, tetikleyicinin `headers` özellik değerini geçirmek için: bir ifade kullanabilirsiniz, örneğin: <p>`@triggeroutputs()['headers']` | 
      | **Gövde** | No | <*gövde-içerik*> | HTTP eyleminin özelliğinden geçmesini istediğiniz diğer içerikler `body` . Bu örnek, yapıt `properties` DEğERLERINI HTTP eylemine geçirir: <p>1. **gövde** özelliğinin içine tıklayarak dinamik içerik listesi belirir. Hiçbir özellik görünmezse, **daha fazla göster**' i seçin. <br>2. dinamik içerik listesinden, **tümleştirme hesabı yapıt araması** altında **Özellikler**' i seçin. | 
      |||| 

      Örnek:

      ![HTTP eylemi için değerleri ve ifadeleri belirtin](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. HTTP eylemi için verdiğiniz bilgileri denetlemek için mantıksal uygulamanızın JSON tanımını görüntüleyin. Mantıksal uygulama Tasarımcısı araç çubuğunda, uygulamanın JSON tanımının görünmesi için **kod görünümü** ' ne tıklayın, örneğin:

      ![Mantıksal uygulama JSON tanımı](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Mantıksal uygulama tasarımcısına geri geçiş yaptıktan sonra, şu anda kullandığınız herhangi bir ifade çözüldü görünür, örneğin:

      ![Mantıksal uygulama Tasarımcısı 'nda çözümlenen ifadeler](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Sözleşmeler hakkında daha fazla bilgi edinin](logic-apps-enterprise-integration-agreements.md)
