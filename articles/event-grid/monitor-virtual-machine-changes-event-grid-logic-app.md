---
title: Sanal makine değişikliklerini izleme-Azure Event Grid & Logic Apps
description: Azure Event Grid ve Logic Apps kullanarak sanal makinelerde (VM) değişiklik olup olmadığını denetleyin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: 5d852378812d8e69480ceb2c5dcea95f1d5f3770
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488614"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Öğretici: Azure Event Grid ve Logic Apps kullanarak sanal makine değişikliklerini Izleme

Azure kaynaklarında veya üçüncü taraf kaynaklarda gerçekleşen belirli olayları izlemek ve yanıtlamak için, en az kod kullanan bir [mantıksal uygulama](../logic-apps/logic-apps-overview.md) oluşturarak görevleri otomatik hale getirebilir ve çalıştırabilirsiniz. Bu kaynaklar, olayları bir [Azure olay kılavuzunda](../event-grid/overview.md)yayımlayabilir. Olay kılavuzu da bu olayları uç nokta olarak kuyruk, web kancası veya [olay hub’ları](../event-hubs/event-hubs-what-is-event-hubs.md) olan abonelere gönderir. Abone olarak, mantıksal uygulamanız görevleri gerçekleştirmek için otomatik iş akışları çalıştırmadan önce olay kılavuzlarından bu olayları bekleyebilir.

Örneğin, yayımcıların Azure Event Grid hizmeti üzerinden abonelere gönderebileceği bazı olaylar şunlardır:

* Kaynağı oluşturun, okuyun, güncelleştirin veya silin. Örneğin, Azure aboneliğinizde ücretlendirmeye neden olabilecek ve faturanızı etkileyebilecek değişiklikleri izleyebilirsiniz.

* Bir Azure aboneliğine kişi ekleyin veya kaldırın.

* Uygulamanız belirli bir eylemi gerçekleştirir.

* Bir kuyrukta yeni bir ileti görüntülenir.

Bu öğretici, bir sanal makinede yapılan değişiklikleri izleyen ve bu değişiklikler hakkında e-posta gönderen bir mantıksal uygulama oluşturur. Bir Azure kaynağı için bir mantıksal uygulama oluşturduğunuzda, bu kaynaktan olayların bir mantık kılavuzu üzerinden mantıksal uygulamaya akışı yapılır. Öğretici size bu mantıksal uygulamayı oluşturma sürecinde yardımcı olur:

![Genel bakış - Olay kılavuzu ve mantıksal uygulama ile sanal makineyi izleme](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir olay kılavuzundan olayları izleyen bir mantıksal uygulama oluşturun.
> * Özellikle sanal makine değişikliklerini izleyen bir koşul ekleyin.
> * Sanal makineniz değiştiğinde e-posta gönderin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Office 365 Outlook, Outlook.com veya Gmail gibi bildirimlerin gönderilmesi için Logic Apps tarafından desteklenen bir e-posta sağlayıcısından e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](/connectors/).

  Bu öğretici, Office 365 Outlook hesabını kullanır. Farklı bir e-posta hesabı kullanırsanız genel adımlar aynı kalır, ancak kullanıcı arabiriminiz biraz farklı görünebilir.

* Tek başına kendi Azure Kaynak grubunda olan bir [sanal makine](https://azure.microsoft.com/services/virtual-machines) . Daha önce yapmadıysanız, [VM oluşturma öğreticisi](../virtual-machines/windows/quick-create-portal.md)aracılığıyla bir sanal makine oluşturun. Sanal makinenin olayları yayımlaması için, [başka bir işlem yapmanız gerekmez](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Boş mantıksal uygulama oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Ana Azure menüsünden **kaynak oluştur** > **tümleştirme** > **mantıksal uygulama**' yı seçin.

   ![Mantıksal uygulama oluşturma](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. **Mantıksal uygulama**altında, mantıksal uygulama kaynağınız hakkında bilgi sağlayın. İşiniz bittiğinde **Oluştur**’u seçin.

   ![Mantıksal uygulama ayrıntılarını sağlayın](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Ad** | Evet | <*Logic-App-adı*> | Mantıksal uygulamanız için benzersiz bir ad sağlayın. |
   | **Abonelik** | Evet | <*Azure-subscription-name*> | Bu öğreticideki tüm hizmetler için aynı Azure aboneliğini seçin. |
   | **Kaynak grubu** | Evet | <*Azure-Resource-group*> | Mantıksal uygulamanızın Azure Kaynak grubu adı, bu öğreticideki tüm hizmetler için seçim yapabilirsiniz. |
   | **Konum** | Evet | *Azure-region*> < | Bu öğreticideki tüm hizmetler için aynı bölgeyi seçin. |
   |||

1. Azure mantıksal Uygulamanızı dağıttıktan sonra, Logic Apps tasarımcısında tanıtım videosu ve yaygın olarak kullanılan Tetikleyiciler içeren bir sayfa görüntülenir. Video ve tetikleyicileri kaydırın.

1. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   ![Mantıksal uygulama şablonu seçin](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps tasarımcı artık mantıksal uygulamanızı başlatmak için kullanabileceğiniz [*Tetikleyicileri*](../logic-apps/logic-apps-overview.md#logic-app-concepts) gösterir. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde ya da belirli bir koşul karşılandığında tetiklenen bir tetikleyiciyle başlamalıdır. Tetikleyici her tetiklendiğinde, Azure Logic Apps mantıksal uygulamanızı çalıştıran bir iş akışı örneği oluşturur.

## <a name="add-an-event-grid-trigger"></a>Event Grid tetikleyicisi ekleme

Şimdi, sanal makinenizin kaynak grubunu izlemek için kullandığınız Event Grid tetikleyicisini ekleyin.

1. Tasarımcıda arama kutusuna filtreniz olarak `event grid` girin. Tetikleyiciler listesinden, **bir kaynak olayı ne zaman** tetiklenir ' ı seçin.

   ![Bu tetikleyiciyi seçin: "bir kaynak olayında"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. İstendiğinde, Azure hesabı kimlik bilgilerinizle Azure Event Grid için oturum açın. Azure aboneliğinizle ilişkili Azure Active Directory kiracıyı gösteren **kiracı** listesinde, doğru kiracının göründüğünden emin olun, örneğin:

   ![Azure kimlik bilgilerinizle oturum açın](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > @outlook.com veya @hotmail.com gibi kişisel bir Microsoft hesabında oturum açtıysanız, Event Grid tetikleyicisi doğru görüntülenmeyebilir. Geçici bir çözüm olarak, [hizmet sorumlusu Ile Bağlan](../active-directory/develop/howto-create-service-principal-portal.md)' ı seçin veya Azure aboneliğinizle ilişkili Azure Active Directory bir üyesi olarak kimlik doğrulaması yapın; Örneğin, *Kullanıcı adı*@emailoutlook.onmicrosoft.com.

1. Şimdi, mantıksal uygulamanızı yayımcıdaki olaylara abone olur. Aşağıdaki tabloda açıklandığı gibi olay Aboneliğinizle ilgili ayrıntıları sağlayın, örneğin:

   ![Olay aboneliğinin ayrıntılarını sağlayın](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Özellik | Gerekli | Değer | Açıklama |
   | -------- | -------- | ----- | ----------- |
   | **Abonelik** | Evet | <*olayı-yayımcı-Azure-abonelik-adı*> | *Olay yayımcıyla*ilişkili Azure aboneliğinin adını seçin. Bu öğreticide, sanal makineniz için Azure abonelik adını seçin. |
   | **Kaynak Türü** | Evet | <*olayı-yayımcı-Azure-Resource-type*> | Olay Yayımcısı için Azure Kaynak türünü seçin. Azure Kaynak türleri hakkında daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md). Bu öğretici için Azure kaynak gruplarını izlemek üzere `Microsoft.Resources.ResourceGroups` değerini seçin. |
   | **Kaynak Adı** |  Evet | <*olayı-yayımcı-Azure-Kaynak-adı*> | Olay Yayımcısı için Azure Kaynak adı ' nı seçin. Bu liste, seçtiğiniz kaynak türüne göre farklılık gösterir. Bu öğreticide, sanal makinenizi içeren Azure Kaynak grubunun adını seçin. |
   | **Olay türü öğesi** |  Hayır | <*olay türleri*> | Filtre uygulamak ve olay kılavuzunuzda göndermek için bir veya daha fazla belirli olay türü seçin. Örneğin, isteğe bağlı olarak, kaynakların ne zaman değiştirildiğini veya silindiğini saptamak için bu olay türlerini ekleyebilirsiniz: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Daha fazla bilgi için şu konulara bakın: <p><p>[kaynak grupları için - Azure Event Grid olay şeması](../event-grid/event-schema-resource-groups.md) <br>[olay filtrelemeyi anlamak](../event-grid/event-filtering.md) -  <br>[Event Grid Için filtre olayları](../event-grid/how-to-filter-events.md) -  |
   | İsteğe bağlı özellikler eklemek için **yeni parametre Ekle**' yi seçin ve ardından istediğiniz özellikleri seçin. | Hayır | {bkz. açıklamalar} | * **önek filtresi**: Bu öğretici için bu özelliği boş bırakın. Varsayılan davranış tüm değerlerle eşleşir. Ancak filtre olarak bir ön ek dizesi (örneğin belirli bir kaynak için bir yol ve bir parametre) belirtebilirsiniz. <p>* **sonek filtresi**: Bu öğretici için bu özelliği boş bırakın. Varsayılan davranış tüm değerlerle eşleşir. Ancak yalnızca belirli dosya türlerini istediğinizde filtre olarak bir sonek dizesi (örneğin dosya adı uzantısı) belirtebilirsiniz. <p>* **abonelik adı**: Bu öğretici için, olay aboneliğiniz için benzersiz bir ad sağlayabilirsiniz. |
   |||

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin. Mantıksal uygulamanızdaki bir eylemin ayrıntılarını daraltmak ve gizlemek için eylemin başlık çubuğunu seçin.

   ![Mantıksal uygulamanızı kaydetme](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Mantıksal uygulamanızı bir olay kılavuzu tetikleyicisiyle kaydettiğinizde, Azure seçtiğiniz kaynağa mantıksal uygulamanız için otomatik olarak bir olay aboneliği oluşturur. Bu nedenle kaynak bir olayı olay kılavuzuna yayımladığında, bu olay kılavuzu otomatik olarak olayı mantıksal uygulamanıza gönderir. Bu olay mantıksal uygulamanızı tetikler ve ardından sonraki adımlarda yapılandıracağınız iş akışının bir örneğini oluşturur ve çalıştırır.

Mantıksal uygulamanız artık canlı ve olay kılavuzundan olayları dinliyor ancak siz eylemleri iş akışına ekleyene kadar herhangi bir işlem yapmayacak.

## <a name="add-a-condition"></a>Koşul ekleme

Mantıksal uygulamanızı yalnızca belirli bir olay veya işlem gerçekleştiğinde çalıştırmak istiyorsanız, `Microsoft.Compute/virtualMachines/write` işlemini denetleyen bir koşul ekleyin. Bu koşul true olduğunda, mantıksal uygulamanız size güncelleştirilen sanal makine hakkında ayrıntıları içeren bir e-posta gönderir.

1. Mantıksal uygulama Tasarımcısı ' nda, olay Kılavuzu tetikleyicisi altında **yeni adım**' ı seçin.

   !["Yeni adım" i seçin](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `condition` girin. Eylemler listesinden **koşul** eylemini seçin.

   ![Koşul ekleme](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic App Tasarımcısı iş akışınıza koşulun true veya false olmasına bağlı olarak izlenecek eylem yolları dahil boş bir koşul ekler.

   ![Boş bir koşul görüntülenir](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Koşul başlığını `If a virtual machine in your resource group has changed`olarak yeniden adlandırın. Koşulun başlık çubuğunda üç nokta ( **...** ) düğmesini seçin ve **Yeniden Adlandır**' ı seçin.

   ![Koşulu yeniden adlandırma](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. `operationName` özelliğinin `Microsoft.Compute/virtualMachines/write` işleme eşit olduğu bir `data` nesnesi için olay `body` denetleyen bir koşul oluşturun. [Event Grid olay şeması](../event-grid/event-schema.md) hakkında daha fazla bilgi edinin.

   1. İlk satırda **Ve** başlığının altındaki sol kutunun içine tıklayın. Görüntülenen dinamik içerik listesinde **ifade**' yi seçin.

      ![İfade düzenleyicisini açmak için "Expression" ı seçin](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. İfade düzenleyicisinde, tetikleyiciden işlem adını döndüren bu ifadeyi girin ve **Tamam**' ı seçin:

      `triggerBody()?['data']['operationName']`

      Örneğin:

      ![İşlem adını ayıklamak için ifade girin](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Ortadaki kutuda **eşittir** işlecini tutun.

   1. Sağ kutuda, izlemek istediğiniz özel işlem olan bu değeri girin:

      `Microsoft.Compute/virtualMachines/write`

   Tamamlanmış koşulunuz Şu örneğe benzer şekilde görünür:

   ![İşlemi karşılaştıran tamamlandı koşulu](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Tasarım görünümünden Kod görünümüne geçiş yapar ve Tasarım görünümüne geri döndüğünüzde, koşulda belirttiğiniz ifade **Data. OperationName** belirtecine dönüşür:

   ![Koşuldaki belirteçler çözümlendi](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Mantıksal uygulamanızı kaydedin.

## <a name="send-email-notifications"></a>E-posta bildirimleri gönderme

Şimdi, belirtilen koşul doğru olduğunda e-posta alabilmeniz için bir [*eylem*](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin.

1. Koşulun **true ise** kutusunda **Eylem Ekle**' yi seçin.

   ![Koşul true olduğunda kullanılacak eylemi ekleyin](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `send an email` girin. E-posta sağlayıcınıza uygun bağlayıcıyı bulun ve seçin. Ardından bağlayıcı için "e-posta gönder" eylemini seçin. Örneğin:

   * Azure iş veya okul hesabı için Office 365 Outlook bağlayıcısını seçin.

   * Kişisel Microsoft hesapları için Outlook.com bağlayıcısını seçin.

   * Gmail hesapları için Gmail bağlayıcısını seçin.

   Bu öğretici, Office 365 Outlook Bağlayıcısı ile devam eder. Farklı bir sağlayıcı kullanıyorsanız, adımlar aynı kalır, ancak kullanıcı arabirimi biraz farklı görünebilir.

   !["E-posta gönder" eylemini seçin](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. E-posta sağlayıcınız için zaten bir bağlantınız yoksa, kimliğinizi doğrulamanız istendiğinde e-posta hesabınızda oturum açın.

1. E-posta gönder eylemini şu başlığa yeniden adlandırın: `Send email when virtual machine updated`

1. Aşağıdaki tabloda belirtildiği gibi e-posta hakkında bilgi sağlayın:

   ![E-posta eylemi hakkında bilgi sağlayın](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > İş akışınızın önceki adımlarındaki çıktıyı seçmek için, dinamik içerik listesinin görünmesi için bir düzenleme kutusunun içine tıklayın veya **dinamik Içerik Ekle**' yi seçin. Daha fazla sonuç için, listedeki her bölüm için **daha fazla göster** ' i seçin. Dinamik içerik listesini kapatmak için **dinamik Içerik Ekle** ' yi tekrar seçin.

   | Özellik | Gerekli | Değer | Açıklama |
   | -------- | -------- | ----- | ----------- |
   | **Alıcı** | Evet | <*alıcı\@etki alanı*> | Alıcının e-posta adresi girin. Test için kendi e-posta adresinizi kullanabilirsiniz. |
   | **Konu** | Evet | `Resource updated:` **Konu** | E-posta konusunun içeriğini girin. Bu öğretici için, belirtilen metni girin ve olayın **Konu** alanını seçin. Burada, e-postanızın konusu güncelleştirilen kaynağın (sanal makine) adını içerir. |
   | **Gövde** | Evet | `Resource:` **konusu** <p>`Event type:` **olay türü**<p>`Event ID:` **kimliği**<p>`Time:` **Olay saati** | E-posta gövdesinin içeriğini girin. Bu öğreticide, belirtilen metni girip, e-postanız olay, olay türü, olay zaman damgası ve olay KIMLIĞINI tetikleyen kaynağı içermesi için olayın **konusunu**, **olay türünü**, **kimliği**ve **olay zamanı** alanlarını seçin. Update. Bu öğretici için kaynak, tetikleyicide seçilen Azure Kaynak grubudur. <p>İçeriğinize boş satır eklemek için Shift + Enter tuşlarını kullanın. |
   ||||

   > [!NOTE]
   > Bir diziyi temsil eden bir alan seçerseniz, tasarımcı eyleme otomatik olarak diziye başvuran bir **For each** döngüsü ekler. Bu şekilde mantıksal uygulamanız ilgili eylemi dizideki tüm öğeler için gerçekleştirir.

   Şimdi, e-posta eyleminiz bu örnekteki gibi görünebilir:

   ![E-postaya eklenecek çıkışları seçme](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Tamamlanmış mantıksal uygulamanız örnekteki gibi görünebilir:

   ![Tamamlanmış mantıksal uygulama](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Mantıksal uygulamanızı kaydedin. Mantıksal uygulamanızdaki her bir eylemin ayrıntılarını daraltmak ve gizlemek için eylemin başlık çubuğunu seçin.

   Mantıksal uygulamanız artık canlıdır ancak herhangi bir işlem gerçekleştirmeden önce sanal makinenizde yapılan değişiklikleri bekler. Mantıksal uygulamanızı şimdi test etmek için sonraki bölüme geçin.

## <a name="test-your-logic-app-workflow"></a>Mantıksal uygulama iş akışınızı test etme

1. Mantıksal uygulamanızın belirtilen olayları alıp almadığını denetlemek için, sanal makinenizi güncelleştirin.

   Örneğin, Azure portalında sanal makinenizi yeniden boyutlandırabilir veya [VM’nizi Azure PowerShell ile yeniden boyutlandırabilirsiniz](../virtual-machines/windows/resize-vm.md).

   Birkaç dakika sonra bir e-posta almanız gerekir. Örneğin:

   ![Sanal makine güncelleştirmesi hakkında e-posta](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Mantıksal uygulamanızın çalıştırmalarını ve tetikleme geçmişini gözden geçirmek için, mantıksal uygulama menüsünde **genel bakış**' ı seçin. Bir çalıştırma hakkında daha fazla ayrıntı görüntülemek için o çalıştırmaya ait satırı seçin.

   ![Mantıksal uygulama çalıştırma geçmişi](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Her bir adımın giriş ve çıkışlarını görüntülemek için gözden geçirmek istediğiniz adımı genişletin. Bu bilgiler mantıksal uygulamanızdaki sorunları tespit etmenize ve gidermenize yardımcı olabilir.

   ![Mantıksal uygulama çalıştırma geçmişi ayrıntıları](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Tebrikler, bir olay kılavuzuyla kaynak olaylarını izleyen ve bu olaylar gerçekleştiğinde size e-posta gönderen bir mantıksal uygulama oluşturdunuz. Ayrıca, süreçleri otomatik hale getiren iş akışlarını ne kadar kolay oluşturabileceğinizi ve sistemler ile bulut hizmetlerini tümleştirmeyi öğrendiniz.

Olay kılavuzları ve mantıksal uygulamalarla diğer yapılandırma değişikliklerini izleyebilirsiniz, örneğin:

* Bir sanal makine rol tabanlı erişim denetimi (RBAC) haklarını alır.
* Değişiklikler bir ağ arabirimi (NIC) üzerindeki bir ağ güvenlik grubunda (NSG) yapılır.
* Bir sanal makine için diskler eklenir veya kaldırılır.
* Bir sanal makine NIC’sine genel bir IP adresi atanır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide Azure aboneliğinize ücret uygulanmasına neden olan kaynaklar kullanılmakta ve eylemler gerçekleştirilmektedir. Bu nedenle öğreticiyi ve testlerinizi tamamladıktan sonra ücret uygulanmasını istemediğiniz kaynakları devre dışı bırakmayı veya silmeyi unutmayın.

* Çalışmanızı silmeden mantıksal uygulamanızı durdurmak için uygulamanızı devre dışı bırakın. Mantıksal uygulama menüsünde **genel bakış**' ı seçin. Araç çubuğunda **devre dışı bırak**' ı seçin.

  ![Mantıksal uygulamanızı kapatma](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Mantıksal uygulama menüsü görünmüyorsa Azure panosuna dönüp mantıksal uygulamanızı yeniden açmayı deneyin.

* Mantıksal uygulamanızı kalıcı olarak silmek için, mantıksal uygulama menüsünde **genel bakış**' ı seçin. Araç çubuğunda **Sil**' i seçin. Mantıksal uygulamanızı silmek istediğinizi onaylayın ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Event Grid ile özel olaylar oluşturma ve yönlendirme](../event-grid/custom-event-quickstart.md)
