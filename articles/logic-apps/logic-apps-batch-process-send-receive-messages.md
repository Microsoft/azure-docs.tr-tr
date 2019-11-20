---
title: Grup olarak toplu işlem iletileri Azure Logic Apps
description: Azure Logic Apps iletileri toplu olarak gönderin ve alın
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: b56a50fceec8ac6be966c0c58a82e94e0c977143
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680436"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Azure Logic Apps iletileri gönderme, alma ve toplu işleme

İletileri bir araya gruplar halinde göndermek ve işlemek için, belirtilen ölçütleriniz toplanmış iletileri serbest bırakma ve işleme için karşılanana kadar iletileri toplu olarak toplayan bir *toplu işlem* çözümü oluşturabilirsiniz. Toplu işleme, mantıksal uygulamanızın iletileri işleme sıklığını azaltabilir. Bu makalede, aynı Azure aboneliği, Azure bölgesi içinde iki mantıksal uygulama oluşturarak ve bu belirli sırayı izleyerek bir toplu işleme çözümü oluşturma gösterilmektedir: 

* Bu iletilerin serbest bırakılması ve işlenmesi için belirtilen ölçütleriniz karşılanana kadar iletileri kabul eden ve toplayan toplu iş [alıcısı "](#batch-receiver) mantıksal uygulaması.

  Toplu işi oluştururken daha sonra Batch hedefini seçebilmeniz için toplu iş alıcısını oluştururken emin olun.

* İletileri daha önce oluşturulan toplu iş alıcısına gönderen bir veya daha fazla ["toplu gönderici"](#batch-sender) Logic Apps. 

   Ayrıca, bir müşteri numarası gibi benzersiz bir anahtar belirtebilir, bu, hedef toplu işi *bölümler* veya bu anahtara göre mantıksal alt kümeler halinde ayırır. Bu şekilde, alıcı uygulaması aynı anahtara sahip tüm öğeleri toplayabilir ve bunları birlikte işleyebilir.

Batch alıcılarınızın ve toplu iş göndericisinin aynı Azure aboneliğini *ve* Azure bölgesini paylaştığından emin olun. Böyle bir işlem yoksa, Batch sender 'ı bir diğeri tarafından görülemeyen için oluşturduğunuzda Batch alıcısını seçemezsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği takip etmek için şu öğelere ihtiyacınız vardır:

* Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz bir Azure hesabı ile başlayabilirsiniz](https://azure.microsoft.com/free/). Ya da [bir Kullandıkça Öde aboneliğine kaydolun](https://azure.microsoft.com/pricing/purchase-options/).

* [Azure Logic Apps tarafından desteklenen herhangi bir e-posta sağlayıcısına](../connectors/apis-list.md) sahip e-posta hesabı

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi 

* Azure portal yerine Visual Studio 'yu kullanmak için, [Logic Apps birlikte çalışmak üzere Visual Studio 'yu ayarladığınızdan](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)emin olun.

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Toplu iş alıcısı oluştur

Bir toplu işe ileti gönderebilmeniz için önce bu toplu işin önce bu iletileri göndereceğiniz hedef olarak bulunması gerekir. İlk olarak, **Batch** tetikleyicisiyle başlayan "Batch alıcısı" mantıksal uygulamasını oluşturmanız gerekir. Bu şekilde, "Batch sender" mantıksal uygulamasını oluşturduğunuzda Batch alıcısı mantıksal uygulamasını seçebilirsiniz. Toplu iş alıcısı, bu iletilerin serbest bırakılması ve işlenmesi için belirtilen ölçütleriniz karşılanana kadar iletileri toplamaya devam eder. Batch alıcılarının toplu göndericiler hakkında herhangi bir şeyi bilmeleri gerekmiyorsa, Batch gönderenlerin iletileri gönderdikleri hedefi bilmeleri gerekir. 

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'da şu ada sahip bir mantıksal uygulama oluşturun: "batchalıcısı" 

2. Logic Apps tasarımcısında, mantıksal uygulama iş akışınızı başlatan **Batch** tetikleyicisini ekleyin. Arama kutusuna filtreniz olarak "Batch" yazın. Şu tetikleyiciyi seçin: **Batch iletileri**

   !["Batch iletileri" tetikleyicisi Ekle](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Batch alıcısı için aşağıdaki özellikleri ayarlayın: 

   | Özellik | Açıklama | 
   |----------|-------------|
   | **Toplu işlem modu** | **satır içi**- : toplu tetikleyici içindeki yayın ölçütlerini tanımlamak için <br>- **tümleştirme hesabı**: bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)aracılığıyla birden çok yayın ölçütü yapılandırması tanımlamak için. Tümleştirme hesabıyla, bu yapılandırmaların tümünü ayrı Logic Apps yerine tek bir yerde tutabilirsiniz. | 
   | **Toplu iş adı** | Toplu iş için bu örnekteki "TestBatch" olan ve yalnızca **satır içi** Batch modu için geçerli olan ad |  
   | **Yayın ölçütleri** | Yalnızca **satır içi** Batch modu için geçerlidir ve her toplu işi işlemeden önce yerine getirme ölçütlerini seçer: <p>- **ileti sayısı tabanlı**: Batch tarafından toplanan ileti sayısına göre Batch 'i serbest bırakın. <br>- **temel**: Bu toplu işlem tarafından toplanan tüm iletiler için toplu işi bayt cinsinden toplam boyuta göre serbest bırakın. <br>- **zamanlaması**: toplu işi, bir Aralık ve Sıklık belirten bir yinelenme zamanlaması temelinde serbest bırakın. Gelişmiş seçeneklerde Ayrıca bir saat dilimi seçebilir ve bir başlangıç tarihi ve saati sağlayabilirsiniz. <br>- **Tümünü Seç**: belirtilen tüm ölçütleri kullan. | 
   | **İleti sayısı** | Toplu işte toplanacak ileti sayısı (örneğin, 10 mesaj). Bir toplu işlemin sınırı 8.000 iletilerdir. | 
   | **Toplu iş boyutu** | Toplu işte toplanacak bayt cinsinden toplam boyut (örneğin, 10 MB). Bir toplu işlemin boyut sınırı 80 MB 'tır. | 
   | **Zamanlama** | Toplu yayınlar arasındaki Aralık ve sıklık (örneğin, 10 dakika). Minimum yinelenme 60 saniye veya 1 dakikadır. Kesirli dakikalar etkin bir şekilde 1 dakikaya yuvarlanır. Bir saat dilimi veya başlangıç tarihi ve saati belirtmek için **Gelişmiş seçenekleri göster**' i seçin. | 
   ||| 

   > [!NOTE]
   > 
   > Tetikleyici hala toplu halde gönderilmemiş iletiler içeriyorsa yayın ölçütünü değiştirirseniz, tetikleyici gönderilmemiş iletileri işlemeye yönelik güncelleştirilmiş yayın ölçütünü kullanır. 

   Bu örnek tüm kriterleri gösterir, ancak kendi testiniz için yalnızca bir ölçüt deneyin:

   ![Toplu tetikleyici ayrıntılarını sağlama](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Şimdi her toplu işi işleyen bir veya daha fazla eylem ekleyin. 

   Bu örnek için, Batch tetikleyicisi tetiklendiğinde e-posta gönderen bir eylem ekleyin. 
   Tetikleyici çalışır ve toplu işte 10 mesaj, 10 MB veya 10 dakikalık bir süre geçtikten sonra bir e-posta gönderir.

   1. Batch tetikleyicisi altında **yeni adım**' ı seçin.

   2. Arama kutusuna filtreniz olarak "e-posta gönder" yazın.
   E-posta sağlayıcınıza bağlı olarak bir e-posta Bağlayıcısı seçin.

      Örneğin, @outlook.com veya @hotmail.com gibi bir kişisel hesabınız varsa, Outlook.com bağlayıcısını seçin. 
      Gmail hesabınız varsa Gmail bağlayıcısını seçin. 
      Bu örnekte Office 365 Outlook kullanılmaktadır. 

   3. Şu eylemi seçin: **e-posta < e-posta*sağlayıcısı* gönder >**

      Örnek:

      ![E-posta sağlayıcınız için "e-posta gönder" eylemini seçin](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. İstenirse, e-posta hesabınızda oturum açın. 

6. Eklediğiniz eylemin özelliklerini ayarlayın.

   * **Alıcı** kutusuna alıcının e-posta adresini girin. 
   Test için kendi e-posta adresinizi kullanabilirsiniz.

   * **Konu** kutusunda, dinamik içerik listesi göründüğünde **bölüm adı** alanını seçin.

     ![Dinamik içerik listesinden "bölüm adı" öğesini seçin](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Daha sonra, toplu iş göndericisinin içinde, hedef toplu işi ileti gönderebileceğiniz mantıksal alt kümelere ayıran benzersiz bir bölüm anahtarı belirtebilirsiniz. 
     Her kümenin toplu gönderici mantıksal uygulaması tarafından oluşturulan benzersiz bir numarası vardır. 
     Bu özellik, birden çok alt küme içeren tek bir toplu işlem kullanmanıza ve her bir alt kümeyi sağladığınız adla tanımlamanıza olanak sağlar.

     > [!IMPORTANT]
     > Bölüm 5.000 ileti veya 80 MB 'lik bir sınıra sahiptir. Her iki koşul da karşılanıyorsa, tanımlanan yayın koşulunuz karşılanmadığında bile toplu işi serbest bırakabilir Logic Apps.

   * **Gövde** kutusunda, dinamik içerik listesi göründüğünde **ileti kimliği** alanını seçin. 

     Logic Apps tasarımcı, e-posta gönder eyleminin etrafında bir "for each" döngüsünü otomatik olarak ekler, çünkü bu eylem önceki eylemden çıktıyı bir toplu iş yerine bir koleksiyon olarak değerlendirir. 

     !["Gövde" için "Ileti kimliği" ni seçin](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Mantıksal uygulamanızı kaydedin. Artık bir toplu iş alıcısı oluşturdunuz.

    ![Mantıksal uygulamanızı kaydetme](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Visual Studio kullanıyorsanız, [Batch ahize mantıksal uygulamanızı Azure 'a dağıttığınızdan](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)emin olun. Aksi takdirde, Batch sender 'ı oluştururken Batch alıcısını seçemezsiniz.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Toplu gönderici oluştur

Şimdi Batch alıcı mantığı uygulamasına ileti gönderen bir veya daha fazla toplu iş gönderici mantığı uygulaması oluşturun. Her toplu iş göndericisine toplu iş alıcısı ve toplu iş adını, ileti içeriğini ve diğer ayarları belirtirsiniz. İsteğe bağlı olarak, bu anahtarla iletileri toplamak için toplu iş kümelerine bölmek üzere benzersiz bir bölüm anahtarı sağlayabilirsiniz. 

* Batch yayınınızı oluştururken zaten [Batch alıcılarınızı](#batch-receiver) oluşturduğunuzdan emin olun, var olan toplu alıcıyı hedef toplu iş olarak seçebilirsiniz. Batch alıcılarının toplu göndericiler hakkında herhangi bir şeyi bilmeleri gerekmiyorsa, toplu göndericiler iletilerin nereden gönderileceğini bilmelidir. 

* Batch alıcılarınızın ve toplu iş göndericisinin aynı Azure bölgesini *ve* Azure aboneliğini paylaştığından emin olun. Böyle bir işlem yoksa, Batch sender 'ı bir diğeri tarafından görülemeyen için oluşturduğunuzda Batch alıcısını seçemezsiniz.

1. Şu adla başka bir mantıksal uygulama oluşturun: "BatchSender"

   1. Arama kutusuna filtreniz olarak "yinelenme" yazın. 
   Bu tetikleyiciyi seçin: **yineleme-zamanlama**

      !["Yinelenme zamanlaması" tetikleyicisi ekleme](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Her dakika gönderen mantıksal uygulamayı çalıştıracak sıklığı ve aralığı ayarlayın.

      ![Yinelenme tetikleyicisi için sıklık ve aralığı ayarla](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Toplu iş 'e ileti göndermek için yeni bir eylem ekleyin.

   1. Yinelenme tetikleyicisi altında **yeni adım**' ı seçin.

   2. Arama kutusuna filtreniz olarak "Batch" yazın. 
   **Eylemler** listesini seçin ve ardından şu eylemi seçin: **batch tetikleyicisiyle Logic Apps iş akışı seçme-toplu Iş 'e ileti gönderme**

      !["Batch tetikleyicisiyle Logic Apps iş akışı Seç" i seçin](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Daha önce oluşturduğunuz Batch alıcısı mantıksal uygulamanızı seçin.

      !["Batch alıcısı" mantıksal uygulamasını seçin](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Liste ayrıca toplu tetikleyici içeren diğer tüm mantıksal uygulamaları da gösterir. 
      > 
      > Visual Studio kullanıyorsanız ve seçilecek toplu iş alıcılarını görmüyorsanız, Batch alıcılarınızı Azure 'a dağıttığınızı kontrol edin. Yapmadıysanız, [Batch ahize mantıksal uygulamanızı Azure 'a dağıtmayı](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)öğrenin. 

   4. Şu eylemi seçin: **Batch_messages-*Batch-alıcıdan* < >**

      ![Şu eylemi seçin: "Batch_messages-<-Logic-App >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Batch göndericisinin özelliklerini ayarlayın:

   | Özellik | Açıklama | 
   |----------|-------------| 
   | **Toplu iş adı** | Bu örnekte "TestBatch" olan alıcı mantıksal uygulaması tarafından tanımlanan toplu işlem adı <p>**Önemli**: Batch adı, çalışma zamanında onaylanır ve alıcı mantıksal uygulaması tarafından belirtilen adla eşleşmelidir. Toplu işlem adının değiştirilmesi toplu iş göndericisinin başarısız olmasına neden olur. | 
   | **İleti Içeriği** | Göndermek istediğiniz iletinin içeriği | 
   ||| 

   Bu örnek için, Batch 'e göndereceğiniz ileti içeriğine geçerli tarih ve saati ekleyen bu ifadeyi ekleyin:

   1. **Ileti içeriği** kutusunun içine tıklayın. 

   2. Dinamik içerik listesi göründüğünde **ifade**' ı seçin. 

   3. @No__t_0 ifadesini girin ve ardından **Tamam**' ı seçin. 

      !["Ileti Içeriği" içinde "Ifade" i seçin, "UtcNow ()" yazın ve "Tamam" ı seçin.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Artık toplu iş için bir bölüm ayarlayın. "Batchalıcısı" eyleminde, **Gelişmiş seçenekleri göster** ' i seçin ve aşağıdaki özellikleri ayarlayın:

   | Özellik | Açıklama | 
   |----------|-------------| 
   | **Bölüm adı** | Hedef Batch 'i mantıksal alt kümelere bölmek ve bu anahtara göre iletileri toplamak için kullanılacak isteğe bağlı benzersiz bölüm anahtarı | 
   | **İleti kimliği** | Boş olduğunda oluşturulmuş bir genel benzersiz tanımlayıcı (GUID) olan isteğe bağlı bir ileti tanımlayıcısı | 
   ||| 

   Bu örnekte, **bölüm adı** kutusuna bir ile beş arasında rastgele bir sayı üreten bir ifade ekleyin. **Ileti kimliği** kutusunu boş bırakın.
   
   1. Dinamik içerik listesinin görünmesi için **bölüm adı** kutusunun içine tıklayın. 

   2. Dinamik içerik listesinde **İfade**’yi seçin.
   
   3. @No__t_0 ifadesini girin ve ardından **Tamam**' ı seçin.

      ![Hedef Batch için bir bölüm ayarlama](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Bu **S_SAYI_ÜRET** işlevi bir ile beş arasında bir sayı üretir. 
      Bu nedenle bu toplu işi, bu ifadenin dinamik olarak ayarladığı beş sayılı bölüme böyor olursunuz.

5. Mantıksal uygulamanızı kaydedin. Gönderen mantıksal uygulamanız Şu örneğe benzer şekilde görünür:

   ![Gönderen mantıksal uygulamanızı kaydedin](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Mantıksal uygulamalarınızı test etme

Toplu işlem çözümünüzü test etmek için, mantıksal uygulamalarınızı birkaç dakika boyunca çalışır durumda bırakın. Kısa süre içinde, hepsi aynı bölüm anahtarına sahip olan beş grup ile e-posta almaya başlamanız gerekir.

Batch gönderici mantıksal uygulamanız her dakikada çalışır, bir ile beş arasında rastgele bir sayı üretir ve bu üretilen numarayı iletilerin gönderildiği hedef toplu iş için bölüm anahtarı olarak kullanır. Toplu iş her seferinde aynı bölüm anahtarına sahip beş öğe olduğunda, Batch alıcısı mantıksal uygulamanız ateşlenir ve her ileti için posta gönderir.

> [!IMPORTANT]
> Testi tamamladığınızda, BatchSender mantıksal uygulamasını, ileti göndermeyi durdurmak ve gelen kutunuzu aşırı yüklemeden kaçınmak için devre dışı bıraktığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* [Batch ve EDI iletileri gönder](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [JSON kullanarak mantıksal uygulama tanımlarında derleme](../logic-apps/logic-apps-author-definitions.md)
* [Azure Logic Apps ve Işlevleri ile Visual Studio 'da sunucusuz bir uygulama oluşturun](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Mantıksal uygulamalar için özel durum işleme ve hata günlüğü](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
