---
title: "Öğretici: Azure Sentinel 'de Otomasyon kuralları ile PlayBook 'ları kullanma"
description: Olay yanıtınızı otomatikleştirecek ve güvenlik tehditlerini düzeltmenize yardımcı olması için Azure Sentinel 'teki Otomasyon kurallarıyla birlikte PlayBook 'ları kullanmanıza yardımcı olması için bu öğreticiyi kullanın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600698"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Öğretici: Azure Sentinel 'de Otomasyon kuralları ile PlayBook 'ları kullanma

Bu öğreticide, olay yanıtınızı otomatikleştirecek ve Azure Sentinel tarafından algılanan güvenlik tehditlerini düzeltmenize olanak sağlamak için PlayBook 'ları Otomasyon kurallarıyla birlikte nasıl kullanacağınız gösterilmektedir. Bu öğreticiyi tamamladığınızda şunları yapabilirsiniz:

> [!div class="checklist"]
>
> * Otomasyon kuralı oluşturma
> * PlayBook oluşturma
> * Bir PlayBook 'a eylem ekleme
> * Tehdit yanıtını otomatikleştirmek için bir Otomasyon kuralına veya bir analiz kuralına PlayBook iliştirme

## <a name="what-are-automation-rules-and-playbooks"></a>Otomasyon kuralları ve PlayBook 'lar nelerdir?

Otomasyon kuralları Azure Sentinel 'de olayları önceliklendirmenize yardımcı olur. Bunları, doğru personele otomatik olarak olay atamak, gürültülü olayları veya bilinen hatalı pozitif sonuçları kapatmak, önem derecesini değiştirmek ve Etiketler eklemek için kullanabilirsiniz. Bunlar ayrıca, olaylara yanıt olarak PlayBook 'ları çalıştırabileceğiniz bir mekanizmadır.

PlayBook 'lar, bir uyarı veya olaya yanıt olarak Azure Sentinel 'ten çalıştırılabilen yordamlar koleksiyonudur. Bir PlayBook, yanıtınızı otomatik hale getirmeye ve düzenlemeye yardımcı olabilir ve belirli uyarılar veya olaylar oluşturulduğunda, sırasıyla bir analiz kuralına veya bir Otomasyon kuralına iliştirilerek otomatik olarak çalışacak şekilde ayarlanabilir. Ayrıca, isteğe bağlı olarak el ile çalıştırılabilir.

Azure Sentinel 'de playbooks, [Azure Logic Apps](../logic-apps/logic-apps-overview.md)yerleşik olan iş akışlarını temel alır. Bu, tüm güç, özelleştirme ve yerleşik şablonları Logic Apps almanızı sağlayan bir deyişle. Her bir PlayBook, ait olduğu belirli bir abonelik için oluşturulur, ancak **PlayBook** 'lar, seçilen tüm aboneliklerde kullanılabilen tüm PlayBook 'ları görüntüler.

> [!NOTE]
> PlayBook 'lar Azure Logic Apps kullandığından, ek ücretler uygulanabilir. Daha fazla ayrıntı için [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) fiyatlandırma sayfasını ziyaret edin.

Örneğin, riskli olabilecek kullanıcıların ağınızı ve bilgi çalmasını önlemek istiyorsanız, güvenliği aşılmış kullanıcıları algılayan kurallar tarafından oluşturulan olaylara otomatik, atmanın yanıtı oluşturabilirsiniz. Aşağıdaki eylemleri alan bir PlayBook oluşturarak başlayabilirsiniz:

1. PlayBook bir Otomasyon kuralı tarafından bir olay geçirerek çağrıldığında PlayBook, [ServiceNow](/connectors/service-now/) veya DIĞER bir It anahtar sistemi sisteminde bir bilet açar.

1. Güvenlik analistlerinizin olayın farkında olduğundan emin olmak için [Microsoft ekiplerinde](/connectors/teams/) veya [bolluk](/connectors/slack/) 'de güvenlik işlemleri kanalınıza bir ileti gönderir.

1. Ayrıca, olaydaki tüm bilgileri, bir e-posta iletisinde, kıdemli ağ yöneticinize ve güvenlik yöneticinize gönderir. E-posta iletisi, **Engelle** ve **Yoksay** Kullanıcı seçeneği düğmelerini içerecektir.

1. PlayBook, yöneticilerden bir yanıt alınana kadar bekler, ardından sonraki adımlarla devam eder.

1. Yöneticiler **Engelle** seçeneğini belirlerseniz, kullanıcıyı devre dışı bırakmak IÇIN Azure AD 'ye ve IP adresini engellemek için bir güvenlik duvarına bir komut gönderir.

1. Yöneticiler **Yoksay**' ı seç, PlayBook Azure Sentinel 'deki olayı ve ServiceNow ' daki bileti kapatır.

PlayBook 'u tetiklemek için, bu olaylar oluşturulduğunda çalışan bir Otomasyon kuralı oluşturacaksınız. Bu kural şu adımları ele alır:

1. Kural, olay durumunu **etkin** olarak değiştirir.

1. Bu olay türünü yöneten analist 'e olayı atar.

1. "Güvenliği aşılmış Kullanıcı" etiketini ekler.

1. Son olarak, yeni oluşturduğunuz PlayBook 'u çağırır. ([Bu adım Için özel izinler gereklidir](automate-responses-with-playbooks.md#incident-creation-automated-response).)

PlayBook 'lar, yukarıdaki örnekte olduğu gibi, bir eylem olarak çalışan Otomasyon kuralları oluşturarak olaylara yanıt olarak otomatik şekilde çalıştırılabilir. Ayrıca, uyarı oluşturulduğunda analiz kuralına otomatik olarak bir veya daha fazla PlayBook çalıştırması bildirerek, uyarılara yanıt olarak otomatik olarak çalıştırılabilirler. 

Ayrıca, seçili bir uyarının yanıtı olarak isteğe bağlı olarak el ile bir PlayBook çalıştırmayı tercih edebilirsiniz.

Azure Sentinel 'de [Otomasyon kurallarını](automate-incident-handling-with-automation-rules.md) ve [PlayBook](automate-responses-with-playbooks.md) 'ları kullanarak tehdit yanıtını otomatikleştirmeye yönelik daha kapsamlı ve ayrıntılı bir giriş alın.

> [!IMPORTANT]
>
> - PlayBook 'lar için **Otomasyon kuralları** ve **olay tetikleyicisi** 'Nin kullanımı Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="create-a-playbook"></a>PlayBook oluşturma

Azure Sentinel 'de yeni bir PlayBook oluşturmak için aşağıdaki adımları izleyin:

### <a name="prepare-the-playbook-and-logic-app"></a>PlayBook ve Logic App 'i hazırlama

1. **Azure Sentinel** gezinti menüsünde **Otomasyon**' u seçin.

1. Üstteki menüde **Oluştur** ve **Yeni PlayBook Ekle**' yi seçin.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Yeni bir PlayBook ekleyin":::

    Yeni bir tarayıcı sekmesi açılır ve bu işlem, **mantıksal uygulama oluşturma** Sihirbazı ' na götürür.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Mantıksal uygulama oluşturma":::

1. **Aboneliğinizi** ve **kaynak grubunuzu** girip bir **mantıksal uygulama adı** altında PlayBook 'a bir ad verin.

1. **Bölge** Için mantıksal uygulama bilgilerinizin depolanacağı Azure bölgesini seçin.

1. Bu PlayBook 'un, tanılama amacıyla etkinliğini izlemek isterseniz, **Log Analytics 'ı etkinleştir** onay kutusunu işaretleyin ve **Log Analytics çalışma alanı** adınızı girin.

1. PlayBook uygulamanıza Etiketler uygulamak istiyorsanız, **>ileri** ' ye tıklayın (Otomasyon kuralları tarafından uygulanan etiketlere bağlı değildir). [Etiketler hakkında daha fazla bilgi edinin](../azure-resource-manager/management/tag-resources.md)). Aksi takdirde, **gözden geçir + oluştur**' a tıklayın. Verdiğiniz ayrıntıları onaylayın ve **Oluştur**' a tıklayın.

1. PlayBook oluşturuluyor ve dağıtılıyor olsa da (Bu işlem birkaç dakika sürer), **Microsoft. EmptyWorkflow** adlı bir ekrana yönlendirilirsiniz. "Dağıtımınız tamamlandığında" iletisi göründüğünde **Kaynağa Git ' e tıklayın.**

1. İş akışını tasarlamaya başlayabileceğiniz yeni PlayBook [Logic Apps tasarımcısına](../logic-apps/logic-apps-overview.md)yönlendirilirsiniz. Kısa bir giriş videosu ve bazı yaygın olarak kullanılan mantıksal uygulama Tetikleyicileri ve şablonları içeren bir ekran görürsünüz. Logic Apps ile PlayBook oluşturma hakkında [daha fazla bilgi edinin](../logic-apps/logic-apps-create-logic-apps-from-templates.md) .

1. **Boş mantıksal uygulama** şablonunu seçin.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Logic Apps tasarımcı şablonu Galerisi":::

### <a name="choose-the-trigger"></a>Tetikleyiciyi seçin

Her PlayBook bir tetikleyiciyle başlamalıdır. Tetikleyici, PlayBook 'u başlatacak eylemi ve PlayBook 'un almasını beklediği şemayı tanımlar.

1. Arama çubuğunda Azure Sentinel ' i arayın. Sonuçlarda göründüğünde **Azure Sentinel** ' i seçin.

1. Elde edilen **Tetikleyiciler** sekmesinde, Azure Sentinel tarafından sunulan iki tetikleyiciyi görürsünüz:
    - Bir Azure Sentinel uyarısına yanıt tetiklendiğinde
    - Azure Sentinel olay oluşturma kuralı tetiklenmesi

   Oluşturmakta olduğunuz PlayBook türüyle eşleşen tetikleyiciyi seçin.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="PlayBook için bir tetikleyici seçin":::

### <a name="add-actions"></a>Eylem ekleme

Şimdi PlayBook 'u çağırdığınızda ne olacağını tanımlayabilirsiniz. **Yeni adım**' ı seçerek eylemler, mantıksal koşullar, döngüler veya anahtar durum koşulları ' nı ekleyebilirsiniz. Bu seçim, tasarımcıda etkileşim kurmak için bir sistem veya uygulama seçebileceğiniz ya da ayarlanacak bir koşulla seçebileceğiniz yeni bir çerçeve açar. Çerçevenin en üstündeki arama çubuğuna sistem veya uygulamanın adını girin ve ardından kullanılabilir sonuçlar ' ı seçin.

Bu adımların her birinde, herhangi bir alana tıkladığınızda iki menü içeren bir panel görüntülenir: **dinamik içerik** ve **ifade**. **Dinamik içerik** menüsünde, içerilen tüm varlıkların değerleri ve öznitelikleri dahil olmak üzere, PlayBook 'a geçirilen uyarının veya olayın özniteliklerine başvurular ekleyebilirsiniz. Yeni bir işlev kitaplığı arasından, adımlarınızı daha fazla mantık eklemek için **ifade** menüsünden seçebilirsiniz.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Mantıksal uygulama tasarımcısı":::

Bu ekran görüntüsünde, bu belgenin başındaki örnekte açıklanan PlayBook 'u oluştururken ekleyeceğiniz eylemler ve koşullar gösterilir. Tek fark burada gösterilen PlayBook, **olay tetikleyicisi** yerine **uyarı tetikleyicisi** kullanıyorsunuz. Bu, bir Otomasyon kuralından değil, doğrudan bir analiz kuralından bu PlayBook 'u çağıracaksınız demektir. Bir PlayBook 'u çağırma yöntemlerinin her ikisi de aşağıda açıklanmıştır.

## <a name="automate-threat-responses"></a>Tehdit yanıtlarını otomatikleştirin

PlayBook 'u oluşturdunuz ve tetikleyiciyi tanımladınız, koşulları ayarladı ve kendisi ve üretecektir. Şimdi, çalıştırılacağı ölçütü belirlemeniz ve bu ölçütler karşılandığında onu çalıştıracak Otomasyon mekanizmasını ayarlamanız gerekir.

### <a name="respond-to-incidents"></a>Olaylara yanıt verme

Olay oluşturulduğunda çalışacak bir [Otomasyon kuralı](automate-incident-handling-with-automation-rules.md) oluşturarak bir **olayı** yanıtlamak için PlayBook kullanın ve bunu açıp PlayBook 'ı çağırır.

Bir Otomasyon kuralı oluşturmak için:

1. Azure Sentinel gezinti menüsündeki **Otomasyon** dikey penceresinde, üst menüden **Oluştur** ' u ve ardından **Yeni kural ekle**' yi seçin.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Yeni kural ekle":::

1. **Yeni otomasyon kuralı oluştur** paneli açılır. Kuralınız için bir ad girin.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Otomasyon kuralı oluşturma":::

1. Otomasyon kuralının yalnızca belirli analiz kurallarında etkili olmasını istiyorsanız, **IF Analytics kural adı** koşulunu değiştirerek hangilerinin olduğunu belirtin.

1. Bu Otomasyon kuralının etkinleştirmesinin bağımlı olmasını istediğiniz diğer koşulları ekleyin. **Koşul Ekle** ' ye tıklayın ve açılan listeden koşullar ' ı seçin. Koşulların listesi, uyarı ayrıntısı ve varlık tanımlayıcı alanları tarafından doldurulur.

1. Bu Otomasyon kuralının geçirmesine istediğiniz eylemleri seçin. Kullanılabilir eylemler, **sahip ata**, **Durumu Değiştir**, **önem derecesi Değiştir**, **Etiketler Ekle** ve **PlayBook**'u içerir. İstediğiniz sayıda eylem ekleyebilirsiniz.

1. Bir **PlayBook Çalıştır** eylemi eklerseniz, kullanılabilir playbooks açılır listesinden seçmeniz istenir. Yalnızca **olay tetikleyicisi** ile başlayan PlayBook 'lar Otomasyon kurallarından çalıştırılabilir, bu nedenle yalnızca listede görünürler.

    > [!IMPORTANT]
    > Azure Sentinel 'de, Otomasyon kurallarından PlayBook 'ları çalıştırmak için açık izinler verilmelidir. Açılan listede bir Playbook "gri" görünürse, Sentinel 'in bu PlayBook 'un kaynak grubu için izne sahip olmadığı anlamına gelir. İzinleri atamak için **PlayBook Izinlerini Yönet** bağlantısına tıklayın.
    > Açılan **Izinleri Yönet** panelinde, çalıştırmak istediğiniz PlayBook 'ları içeren kaynak gruplarının onay kutularını Işaretleyin ve **Uygula**' ya tıklayın.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="İzinleri yönetme":::
    > - Kendinize Azure Sentinel izinleri vermek istediğiniz herhangi bir kaynak grubunda **sahip izinleriniz olmalıdır** ve çalıştırmak istediğiniz PlayBook 'ları içeren herhangi bir kaynak grubunda **Logic App katılımcısı** rolüne sahip olmanız gerekir.
    > - Çok kiracılı bir dağıtımda, çalıştırmak istediğiniz PlayBook farklı bir kiracıda varsa, PlayBook 'u PlayBook kiracısında çalıştırmak için Azure Sentinel iznini vermeniz gerekir.
    >    1. Playbooks ' kiracısındaki Azure Sentinel gezinti menüsünden **Ayarlar**' ı seçin.
    >    1. **Ayarlar** dikey penceresinde, **Ayarlar** sekmesini ve ardından **PlayBook izinleri** Genişleticisi ' ni seçin.
    >    1. Yukarıda bahsedilen **Izinleri Yönet** panelini açmak Için **izinleri Yapılandır** düğmesine tıklayın ve burada açıklandığı gibi devam edin.

1. Otomasyon kuralınız için bir sona erme tarihi ayarlayın.

1. Bu kuralın Otomasyon kuralları dizisinin nerede çalışacağını **belirlemekte kullanılacak bir** sayı girin.

1. **Uygula**’ya tıklayın. İşiniz bitti!

Otomasyon kuralları oluşturmanın [diğer yollarını bulur](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) .

### <a name="respond-to-alerts"></a>Uyarılara yanıt verme

Bir **analiz kuralı** oluşturarak veya mevcut bir tane düzenleyerek, uyarı oluşturulduğunda çalıştıran bir **uyarıya** yanıt vermek için bir PlayBook kullanın ve bu durumda, bir [analiz kuralı sihirbazında](tutorial-detect-threats-custom.md)otomatik bir yanıt olarak PlayBook 'u seçebilirsiniz.

1. Azure Sentinel gezinti menüsündeki **analiz** dikey penceresinde, yanıtı otomatikleştirmek istediğiniz analiz kuralını seçin ve Ayrıntılar bölmesinde **Düzenle** ' ye tıklayın.

1. **Analiz Kuralı Sihirbazı-varolan kuralı Düzenle** sayfasında **Otomatik Yanıt** sekmesini seçin.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Otomatik yanıt sekmesi":::

1. Açılır listeden Playbook ' ı seçin. Birden fazla PlayBook seçebilirsiniz, ancak yalnızca **uyarı tetikleyicisi** kullanan PlayBook 'ları kullanılabilir.

1. **İnceleme ve oluşturma** sekmesinde **Kaydet**' i seçin.

## <a name="run-a-playbook-on-demand"></a>İsteğe bağlı bir playbook çalıştırma

Ayrıca, isteğe bağlı olarak bir PlayBook çalıştırabilirsiniz.

 > [!NOTE]
 > Yalnızca **uyarı tetikleyicisi** kullanan PlayBook 'ları isteğe bağlı olarak çalıştırılabilir.

Bir PlayBook 'u isteğe bağlı olarak çalıştırmak için:

1. **Olaylar** sayfasında bir olay seçin ve **tüm ayrıntıları görüntüle**' ye tıklayın.

2. **Uyarılar** sekmesinde, PlayBook 'u çalıştırmak istediğiniz uyarıya tıklayın ve sağa doğru bir şekilde ilerleyin ve **PlayBook 'ları görüntüle** ' ye tıklayın ve abonelik üzerindeki kullanılabilir PlayBook 'ları listesinden **çalıştırmak** için bir PlayBook seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, tehditlere yanıt vermek için Azure Sentinel 'de PlayBook 'ları ve Automation kurallarını nasıl kullanacağınızı öğrendiniz. 
- Azure Sentinel kullanarak [tehditleri](hunting.md) nasıl önceden yapabileceğinizi öğrenin.
