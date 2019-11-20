---
title: Grup Azure Logic Apps EDI iletilerini toplu işleme
description: EDI iletilerini Azure Logic Apps içinde toplu işlemler, gruplar veya koleksiyonlar olarak gönderin ve alın
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 28e51363ca99182c9b6520ab1dea5aa13b16ea12
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680175"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Azure Logic Apps ile EDI iletilerini toplu iş ortaklarına gönderin

İşletmeler arası (B2B) senaryolarında, iş ortakları genellikle grupları veya *toplu işleri*değiş tokuş ediyor. Logic Apps bir toplu işleme çözümü oluşturduğunuzda, ticari iş ortaklarına ileti gönderebilir ve bu iletileri toplu işlerle birlikte işleyebilirsiniz. Bu makalede, bir "Batch sender" mantıksal uygulaması ve bir "Batch alıcısı" mantıksal uygulaması oluşturup x12 kullanarak EDI iletilerini nasıl toplu olarak işleyekullanabileceğinizi gösterir. 

Toplu x12 iletileri diğer iletileri toplu işleme gibi çalışarak. toplu iş 'e ileti toplayan bir Batch tetikleyicisi ve toplu işe ileti gönderen bir Batch eylemi kullanırsınız. Ayrıca, x12 toplu işlem, iletilerin ticari ortağa veya başka bir hedefe gitmesi için bir x12 kodlama adımı içerir. Batch tetikleyicisi ve eylemi hakkında daha fazla bilgi edinmek için bkz. [Batch işlem iletileri](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Bu makalede, aynı Azure aboneliği, Azure bölgesi içinde iki mantıksal uygulama oluşturarak ve bu belirli sırayı izleyerek bir toplu işlem çözümü oluşturacaksınız:

* Bu iletilerin serbest bırakılması ve işlenmesi için belirtilen ölçütleriniz karşılanana kadar iletileri kabul eden ve toplayan bir ["toplu iş alıcısı"](#receiver) mantıksal uygulaması. Bu senaryoda Batch alıcısı Ayrıca, belirtilen x12 sözleşmesini veya iş ortağı kimliklerini kullanarak toplu işteki iletileri kodluyor.

  Toplu işi oluştururken daha sonra Batch hedefini seçebilmeniz için toplu iş alıcısını oluştururken emin olun.

* Daha önce oluşturulan toplu iş alıcısına iletileri gönderen ["toplu gönderici"](#sender) mantıksal uygulaması. 

Batch alıcılarınızın ve toplu iş göndericisinin aynı Azure aboneliğini *ve* Azure bölgesini paylaştığından emin olun. Böyle bir işlem yoksa, Batch sender 'ı bir diğeri tarafından görülemeyen için oluşturduğunuzda Batch alıcısını seçemezsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği takip etmek için şu öğelere ihtiyacınız vardır:

* Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz bir Azure hesabı ile başlayabilirsiniz](https://azure.microsoft.com/free/). Ya da [bir Kullandıkça Öde aboneliğine kaydolun](https://azure.microsoft.com/pricing/purchase-options/).

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Azure aboneliğinizle ilişkili olan ve Logic Apps ile bağlantılı olan mevcut bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Tümleştirme hesabınızda en az iki mevcut [iş ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md) . Her ortağın, x12 (Standart taşıyıcı Alfa kodu) niteleyicisi için iş kimliği olarak ortağın özelliklerinde kullanması gerekir.

* Tümleştirme hesabınızda mevcut bir [x12 sözleşmesi](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Azure portal yerine Visual Studio 'yu kullanmak için, [Logic Apps birlikte çalışmak üzere Visual Studio 'yu ayarladığınızdan](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)emin olun.

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 Batch alıcısı oluşturma

Bir toplu işe ileti gönderebilmeniz için önce bu toplu işin önce bu iletileri göndereceğiniz hedef olarak bulunması gerekir. İlk olarak, **Batch** tetikleyicisiyle başlayan "Batch alıcısı" mantıksal uygulamasını oluşturmanız gerekir. Bu şekilde, "Batch sender" mantıksal uygulamasını oluşturduğunuzda Batch alıcısı mantıksal uygulamasını seçebilirsiniz. Toplu iş alıcısı, bu iletilerin serbest bırakılması ve işlenmesi için belirtilen ölçütleriniz karşılanana kadar iletileri toplamaya devam eder. Batch alıcılarının toplu göndericiler hakkında herhangi bir şeyi bilmeleri gerekmiyorsa, Batch gönderenlerin iletileri gönderdikleri hedefi bilmeleri gerekir. 

Bu toplu iş alıcısı için Batch modunu, adı, yayın ölçütünü, x12 sözleşmesini ve diğer ayarları belirtirsiniz. 

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'da şu ada sahip bir mantıksal uygulama oluşturun: "BatchX12Messages"

2. [Mantıksal uygulamanızı tümleştirme hesabınıza bağlayın](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Logic Apps tasarımcısında, mantıksal uygulama iş akışınızı başlatan **Batch** tetikleyicisini ekleyin. Arama kutusuna filtreniz olarak "Batch" yazın. Şu tetikleyiciyi seçin: **Batch iletileri**

   ![Batch tetikleyicisi Ekle](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Toplu iş alıcısı özelliklerini ayarlayın: 

   | Özellik | Değer | Notlar | 
   |----------|-------|-------|
   | **Toplu işlem modu** | Çizgi |  |  
   | **Toplu iş adı** | TestBatch | Yalnızca **satır içi** Batch moduyla kullanılabilir | 
   | **Yayın ölçütleri** | İleti sayısı tabanlı, zamanlama tabanlı | Yalnızca **satır içi** Batch moduyla kullanılabilir | 
   | **İleti sayısı** | 10 | Yalnızca **ileti sayısı tabanlı** yayın ölçütlerine göre kullanılabilir | 
   | **Aralık** | 10 | Yalnızca **zamanlama tabanlı** yayın ölçütleriyle kullanılabilir | 
   | **Sıklık** | dakikaya | Yalnızca **zamanlama tabanlı** yayın ölçütleriyle kullanılabilir | 
   ||| 

   ![Toplu tetikleyici ayrıntılarını sağlama](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Bu örnek, toplu iş için bir bölüm oluşturmaz, bu nedenle her Batch aynı bölüm anahtarını kullanır. Bölümler hakkında daha fazla bilgi için bkz. [Batch işlem iletileri](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Şimdi her toplu işi kodlayan bir eylem ekleyin: 

   1. Batch tetikleyicisi altında **yeni adım**' ı seçin.

   2. Arama kutusuna filtreniz olarak "x12 Batch" yazın ve bu eylemi (herhangi bir sürüm) seçin: **toplu kodlama <*Sürüm*>-x12** 

      ![X12 Batch kodlama eylemini seç](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Daha önce tümleştirme hesabınıza bağlanamadıysanız bağlantıyı şimdi oluşturun. Bağlantınız için bir ad girin, istediğiniz tümleştirme hesabını seçin ve ardından **Oluştur**' u seçin.

      ![Batch Encoder ile tümleştirme hesabı arasında bağlantı oluştur](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Batch Encoder eyleminiz için bu özellikleri ayarlayın:

      | Özellik | Açıklama |
      |----------|-------------|
      | **X12 sözleşmesinin adı** | Listeyi açın ve mevcut sözleşmenizi seçin. <p>Listeniz boşsa, mantıksal uygulamanızı istediğiniz sözleşmeye sahip [tümleştirme hesabına bağtığınızdan](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) emin olun. | 
      | **BatchName** | Bu kutunun içine tıklayın ve dinamik içerik listesi görüntülendikten sonra **Batch adı** belirtecini seçin. | 
      | **Madı** | Bu kutunun içine tıklayın ve dinamik içerik listesi görüntülendikten sonra **bölüm adı** belirtecini seçin. | 
      | **Öğeler** | Öğe ayrıntıları kutusunu kapatın ve ardından bu kutunun içine tıklayın. Dinamik içerik listesi görüntülendikten sonra, **toplu öğe** belirtecini seçin. | 
      ||| 

      ![Toplu kodlama eylemi ayrıntıları](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      **Öğeler** kutusu için:

      ![Toplu iş kodlama eylem öğeleri](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Mantıksal uygulamanızı kaydedin. 

7. Visual Studio kullanıyorsanız, [Batch ahize mantıksal uygulamanızı Azure 'a dağıttığınızdan](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)emin olun. Aksi takdirde, Batch sender 'ı oluştururken Batch alıcısını seçemezsiniz.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

Batch alıcılarınızın beklendiği gibi çalıştığından emin olmak için, test amaçları için bir HTTP eylemi ekleyebilir ve [Istek bin hizmetine](https://requestbin.com/)bir toplu ileti gönderebilirsiniz. 

1. X12 encode eylemi altında **yeni adım**' ı seçin. 

2. Arama kutusuna filtreniz olarak "http" yazın. Şu eylemi seçin: **http-http**
    
   ![HTTP eylemi seçin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. HTTP eyleminin özelliklerini ayarlayın:

   | Özellik | Açıklama | 
   |----------|-------------|
   | **Yöntem** | Bu listeden **gönderi**' ı seçin. | 
   | **Kullanılmamışsa** | İstek sepeti için bir URI oluşturun ve bu kutuya bu URI 'yi girin. | 
   | **Gövde** | Bu kutunun içine tıklayın ve dinamik içerik listesi açıldıktan sonra, **sözleşme adına göre toplu kodlama**bölümünde görüntülenen **gövde** belirtecini seçin. <p>**Gövde** belirtecini görmüyorsanız, **sözleşme adına göre Batch kodlama**' nın yanında, **daha fazla göster**' i seçin. | 
   ||| 

   ![HTTP eylemi ayrıntılarını sağlayın](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Mantıksal uygulamanızı kaydedin. 

   Batch ahize mantıksal uygulamanız şu örnekteki gibi görünür: 

   ![Batch alıcısı mantıksal uygulamanızı kaydedin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 Batch göndericisi oluşturma

Şimdi Batch alıcı mantığı uygulamasına ileti gönderen bir veya daha fazla mantıksal uygulama oluşturun. Her toplu iş göndericisine toplu iş alıcısı mantıksal uygulamasını ve toplu iş adını, ileti içeriğini ve diğer ayarları belirtirsiniz. İsteğe bağlı olarak, bu anahtarla iletileri toplamak için toplu iş kümelerine bölmek üzere benzersiz bir bölüm anahtarı sağlayabilirsiniz. 

* Batch yayınınızı oluştururken zaten [Batch alıcılarınızı](#receiver) oluşturduğunuzdan emin olun, var olan toplu alıcıyı hedef toplu iş olarak seçebilirsiniz. Batch alıcılarının toplu göndericiler hakkında herhangi bir şeyi bilmeleri gerekmiyorsa, toplu göndericiler iletilerin nereden gönderileceğini bilmelidir. 

* Batch alıcılarınızın ve toplu iş göndericisinin aynı Azure bölgesini *ve* Azure aboneliğini paylaştığından emin olun. Böyle bir işlem yoksa, Batch sender 'ı bir diğeri tarafından görülemeyen için oluşturduğunuzda Batch alıcısını seçemezsiniz.

1. Şu adla başka bir mantıksal uygulama oluşturun: "SendX12MessagesToBatch" 

2. Arama kutusuna filtreniz olarak "http isteği" yazın. Bu tetikleyiciyi seçin: **BIR http isteği alındığında** 
   
   ![Istek tetikleyicisini ekleme](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Toplu iş 'e ileti göndermek için bir eylem ekleyin.

   1. HTTP isteği eylemi altında **yeni adım**' ı seçin.

   2. Arama kutusuna filtreniz olarak "Batch" yazın. 
   **Eylemler** listesini seçin ve ardından şu eylemi seçin: **batch tetikleyicisiyle Logic Apps iş akışı seçme-toplu Iş 'e ileti gönderme**

      !["Batch tetikleyicisiyle Logic Apps iş akışı Seç" i seçin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Şimdi, daha önce oluşturduğunuz "BatchX12Messages" mantıksal uygulamanızı seçin.

      !["Batch alıcısı" mantıksal uygulamasını seçin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Şu eylemi seçin: **Batch_messages-*Batch-alıcıdan* < >**

      !["Batch_messages" eylemini seçin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Batch göndericisinin özelliklerini ayarlayın.

   | Özellik | Açıklama | 
   |----------|-------------| 
   | **Toplu iş adı** | Bu örnekte "TestBatch" olan alıcı mantıksal uygulaması tarafından tanımlanan toplu işlem adı <p>**Önemli**: Batch adı, çalışma zamanında onaylanır ve alıcı mantıksal uygulaması tarafından belirtilen adla eşleşmelidir. Toplu işlem adının değiştirilmesi toplu iş göndericisinin başarısız olmasına neden olur. | 
   | **İleti Içeriği** | Bu örnekteki **gövde** belirteci olan göndermek istediğiniz iletinin içeriği | 
   ||| 
   
   ![Batch özelliklerini ayarla](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Mantıksal uygulamanızı kaydedin. 

   Batch gönderici mantıksal uygulamanız şu örnekteki gibi görünür:

   ![Batch gönderici mantıksal uygulamanızı kaydedin](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Mantıksal uygulamalarınızı test etme

Toplu işlem çözümünüzü test etmek için [Postman](https://www.getpostman.com/postman) veya benzer bir araç aracılığıyla Batch gönderici mantıksal uygulamanıza x12 iletileri gönderin. Yakında, her 10 dakikada bir veya 10 ' un, her biri de aynı bölüm anahtarıyla olan istek sepetinizdeki x12 iletileri almaya başlanıyor olursunuz.

## <a name="next-steps"></a>Sonraki adımlar

* [İletileri toplu işlem olarak işle](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
