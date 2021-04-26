---
title: Azure Percept DK ve Azure Percept Audio ile bir ses Yardımcısı oluşturma
description: Azure Percept DK 'ye kod içermeyen bir konuşma çözümü oluşturma ve dağıtma hakkında bilgi edinin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023172"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Azure Percept DK ve Azure Percept Audio ile bir ses Yardımcısı oluşturma

Bu öğreticide, Azure Percept DK ve Azure Percept sesinizle birlikte kullanmak üzere bir şablondan bir ses Yardımcısı oluşturacaksınız. Ses Yardımcısı tanıtımı, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) içinde çalışır ve sesli kontrollü bir sanal nesne seçimi içerir. Bir nesneyi denetlemek için, bir kelime veya kısa bir ifade olan ve ardından bir komut gelen anahtar kelimeniz söyleyin. Her şablon belirli bir komut kümesine yanıt verir.

Bu kılavuz, cihazlarınızı ayarlama, bir ses Yardımcısı ve gerekli [konuşma Hizmetleri](../cognitive-services/speech-service/overview.md) kaynaklarını oluşturma, sesli yardımcınızı test etme, anahtar kelimenizi yapılandırma ve özel anahtar sözcükler oluşturma sürecinde size yol gösterecektir.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- Azure Percept sesi
- 3,5 mm ses jakına bağlanabilecek konuşmacı veya kulaklıklar (isteğe bağlı)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): devkit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve devkit 'e IoT Hub
- [Azure Percept ses kurulumu](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Kullanılabilir bir şablon kullanarak bir ses Yardımcısı oluşturma

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'ya gidin.

1. **Tanıtımlar & öğreticiler** sekmesini açın.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Azure portal giriş sayfasının ekran görüntüsü.":::

1. **Konuşma öğreticileri ve tanıtımları** altında **Ses Yardımcısı şablonlarını dene** ' ye tıklayın. Bu, ekranınızın sağ tarafında bir pencere açar.

1. Pencerede şunları yapın:

    1. **IoT Hub** açılır menüsünde, devkit 'in bağlı olduğu IoT Hub 'ını seçin.

    1. **Cihaz** açılan menüsünde devkit ' i seçin.

    1. Kullanılabilir sesli yardımcı şablonlarından birini seçin.

    1. **Bu proje için koşullara & koşulları kabul ediyorum** onay kutusuna tıklayın.

    1. **Oluştur**’a tıklayın.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Sesli yardım şablonu oluşturma ekranının ekran görüntüsü.":::

1. **Oluştur**'a tıkladıktan sonra, Portal konuşma teması kaynağınızı oluşturmak için başka bir pencere açar. Pencerede şunları yapın:

    1. **Abonelik** kutusunda Azure aboneliğinizi seçin.

    1. **Kaynak grubu** açılır menüsünden tercih ettiğiniz kaynak grubunu seçin. Sesli Yardımcınızla birlikte kullanmak üzere yeni bir kaynak grubu oluşturmak isterseniz, açılan menü altında **Oluştur** ' a tıklayın ve istemleri izleyin.

    1. **Uygulama ön eki** için bir ad girin. Bu, projeniz ve özel komut adınız için ön ek olacaktır.

    1. **Bölge** altında, kaynakların dağıtılacağı bölgeyi seçin.

    1. **Lusıs tahmin fiyatlandırma katmanı** altında **Standart** ' ı seçin (ücretsiz katman konuşma isteklerini desteklemez).

    1. **Oluştur** düğmesine tıklayın. Ses Yardımcısı uygulamasına yönelik kaynaklar aboneliğinize dağıtılır.

        > [!WARNING]
        > Portalın kaynağı dağıtma işlemi tamamlanana kadar **pencereyi kapatmayın.** Pencereyi erken kapatmak ses Yardımcısı 'nın beklenmedik davranışına neden olabilir. Kaynağınız dağıtıldıktan sonra tanıtım görüntülenir.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Abonelik ve kaynak grubu seçim penceresinin ekran görüntüsü.":::

## <a name="test-out-your-voice-assistant"></a>Ses yardımcınızı test edin

Sesli yardımcınızla etkileşim kurmak için anahtar sözcüğünü ve ardından bir komutu söyleyin. Bu anahtar sözcüğünü tanırken, cihaz bir CHIME (konuşmacı veya kulaklık bağlantısı varsa duyabiliyor) ve LED 'Leri mavi olarak yanıp söndürmez. Komutlarınız işlenirken LED 'ler Yuya geçiş yapar. Sesinizin komutuna olan yanıtı, tanıtım penceresinde metin olarak yazdırılır ve konuşmacı/kulaklığınızla sessiz olarak gönderilir. Varsayılan anahtar sözcüğü ( **özel anahtar sözcüğünün** yanında listelenir) "bilgisayar" olarak ayarlanır ve her şablonda, demo penceresindeki sanal nesnelerle etkileşime girebilmeniz için bir dizi uyumlu komut vardır. Örneğin, Sanal TV 'yi açmak için "bilgisayar, TV 'yi aç" dizliği veya sağlık tanıtıcılarını kullanıyorsanız

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Hastanlik tanıtım penceresinin ekran görüntüsü.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Hospititesi ve sağlık demo komutları

Hem sağlık hem de barındırma tanıtımları, ile etkileşime girebilmeniz için Sanal TV 'ler, ışıklar, hastanlar ve terstatlar vardır. Aşağıdaki komutlar (ve ek Çeşitlemeler) desteklenir:

* "Işıkları açma/kapatma."
* "TV 'yi açma/kapatma."
* "AC 'yi açma/kapatma."
* "Pencereleri açma/kapatma."
* "Sıcaklığın X derece olarak ayarlanması." (X, istenen sıcaklığa sahiptir, örn. 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Sağlık tanıtım penceresinin ekran görüntüsü.":::

### <a name="automotive-demo-commands"></a>Oto için tanıtım komutları

Oto ve ile etkileşime girebilen bir sanal bilgisayar Warmer, Defroster ve termostat gösterimi. Aşağıdaki komutlar (ve ek Çeşitlemeler) desteklenir:

* "Erteleme 'yi açın/kapatın."
* "Koltuk ve devre dışı bırak."
* "Sıcaklığın X derece olarak ayarlanması." (X, istenen sıcaklığa sahiptir, örn. 75.)
* "Sıcaklığın Y derecesini artırma/azaltma."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Oto için tanıtım penceresinin ekran görüntüsü.":::

### <a name="inventory-demo-commands"></a>Envanter tanıtım komutları

Inventory demo, sanal bir envanter uygulamasıyla birlikte etkileşimde bulunmak için bir sanal mavi, sarı ve yeşil kutu seçimi içerir. Aşağıdaki komutlar (ve ek Çeşitlemeler) desteklenir:

* "X kutularını Ekle/Kaldır." (X kutu sayısıdır, örneğin, 4.)
* "Siparişi/sevk X kutuları."
* "Stokta kaç kutu var?"
* "Y kutularını say." (Y, örneğin sarı.) kutularının rengidir.
* "Her şeyi stoğa gönder."


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Envanter tanıtım penceresinin ekran görüntüsü.":::

## <a name="configure-your-keyword"></a>Anahtar keliinizi yapılandırın

Sesli yardım uygulamanız için anahtar sözcüğünü özelleştirebilirsiniz.

1. Demo penceresinde **özel anahtar sözcük** ' ın yanındaki **Değiştir** ' e tıklayın.

1. Kullanılabilir anahtar sözcüklerden birini seçin. Örnek anahtar sözcüklerin bir seçimi ve oluşturduğunuz özel anahtar sözcüklerden birini seçebileceksiniz.

1. **Kaydet**’e tıklayın.

### <a name="create-a-custom-keyword"></a>Özel anahtar sözcük oluşturma

Ses uygulamanız için kendi anahtar keliinizi oluşturabilirsiniz. Özel anahtar kelimeniz için eğitim yalnızca birkaç dakika içinde tamamlanabilir.

1. Tanıtım penceresinin üst kısmındaki **+ özel anahtar sözcük oluştur** ' a tıklayın. 

1. İstediğiniz anahtar sözcüğünü girin, bu tek bir sözcük veya kısa bir ifade olabilir.

1. **Konuşma** kaynağınızı seçin (Bu, demo penceresinde **özel komut** ' nin yanında listelenir ve uygulama ön ekini içerir).

1. **Kaydet**’e tıklayın. 

## <a name="create-a-custom-command"></a>Özel komut oluşturma

Portal, var olan konuşma kaynaklarıyla özel komutlar oluşturmaya yönelik işlevler de sağlar. "Özel komut", var olan uygulama içinde belirli bir komut değil, Ses Yardımcısı uygulamasının kendisini ifade eder. Özel bir komut oluşturarak, [konuşma Studio](https://speech.microsoft.com/)'da daha fazla geliştirme yapmanız gereken yeni bir konuşma projesi oluşturuyorsunuz.

Tanıtım penceresi içinden yeni bir özel komut oluşturmak için, sayfanın üst kısmındaki **+ özel komut oluştur** ' a tıklayın ve aşağıdakileri yapın:

1. Özel Komutunuz için bir ad girin.

1. Projeniz için bir açıklama girin (isteğe bağlı).

1. Tercih ettiğiniz dili seçin.

1. Konuşma kaynağınızı seçin.

1. LUSıS kaynağınızı seçin.

1. LUSıS Authoring Resource ' i seçin veya yeni bir tane oluşturun.

1. **Oluştur**’a tıklayın.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Özel komutlar oluşturma penceresinin ekran görüntüsü.":::

Özel bir komut oluşturduktan sonra daha fazla geliştirme için [konuşma Studio](https://speech.microsoft.com/) 'ya gitmeniz gerekir. Konuşma Studio 'Yu açıp özel komutunuz listede görmüyorsanız, şu adımları izleyin:

1. Azure Percept Studio 'daki sol taraftaki menü panelinde, **AI projeleri** altında **konuşma** ' ya tıklayın.

1. **Komutlar** sekmesini seçin.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Düzenlemek için kullanılabilen özel komutların listesinin ekran görüntüsü.":::

1. Geliştirmek istediğiniz özel komutu seçin. Bu, projeyi konuşma Studio 'da açar.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Konuşma Studio giriş ekranının ekran görüntüsü.":::

Özel komutlar geliştirme hakkında daha fazla bilgi için lütfen [konuşma hizmeti belgelerine](../cognitive-services/speech-service/custom-commands.md)bakın.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Ses Yardımcısı oluşturuldu ancak komutlara yanıt vermiyor

Interposer panosundaki LED ışıklarını kontrol edin:

* Üç adet düz mavi ışık, Ses Yardımcısı 'nın hazırlandığını ve anahtar sözcüğü beklediğini gösterir.
* Orta LED (L02) ise, devkit 'in başlatmayı tamamladığı ve bir anahtar sözcükle yapılandırılması gerekir.
* Orta ışığı (L02) yanıp sönüyorsa, ses SoM 'u henüz başlatmayı tamamlamamıştır. Başlatmanın tamamlanması birkaç dakika sürebilir.

LED göstergeleri hakkında daha fazla bilgi için lütfen [LED makalesine](./audio-button-led-behavior.md)bakın.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Ses Yardımcısı, konuşma Studio 'da oluşturulan özel bir anahtar sözcüğe yanıt vermiyor

Konuşma modülünün güncel olmadığı durumlarda bu durum oluşabilir. Konuşma modülünü en son sürüme güncelleştirmek için aşağıdaki adımları izleyin:

1. Azure Percept Studio giriş sayfasındaki sol taraftaki menü panelinde **cihazlar** ' a tıklayın.

1. Cihazınızı bulun ve seçin.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio 'daki cihaz listesinin ekran görüntüsü.":::

1. Cihaz penceresinde **konuşma** sekmesini seçin.

1. Konuşma modülü sürümünü denetleyin. Bir güncelleştirme varsa, sürüm numarasının yanında bir **Güncelleştir** düğmesi görürsünüz.

1. Konuşma Modülü güncelleştirmesini dağıtmak için **Güncelleştir** ' e tıklayın. Güncelleştirme işleminin tamamlanabilmesi için genellikle 2-3 dakika sürer.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ses Yardımcısı uygulamanızla çalışmayı tamamladıktan sonra, bu öğretici sırasında dağıttığınız konuşma kaynaklarını temizlemek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com), sol menü panelinden **kaynak grupları** ' nı seçin veya arama çubuğuna yazın.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Sol menü paneli ve kaynak gruplarını gösteren Azure portal giriş sayfasının ekran görüntüsü.":::

1. Kaynak grubunuzu seçin.

1. Uygulamanızın ön ekini içeren altı kaynak seçin ve üstteki menü panelinde **Sil** simgesine tıklayın.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Silinmek üzere seçilen konuşma kaynaklarının ekran görüntüsü.":::

1. Silmeyi onaylamak için, onay kutusuna **Evet** yazın, doğru kaynakları seçtiğinizden emin olun ve **Sil**' e tıklayın.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Silme onayı penceresinin ekran görüntüsü.":::

> [!WARNING]
> Bu işlem, sildiğiniz konuşma kaynaklarıyla oluşturulan tüm özel anahtar sözcükleri kaldırır ve ses Yardımcısı tanıtımı artık çalışmaz.

## <a name="next-steps"></a>Sonraki Adımlar

Kod içermeyen bir konuşma çözümü oluşturduğunuza göre, Azure Percept DK için [kod içermeyen bir Vision çözümü](./tutorial-nocode-vision.md) oluşturmayı deneyin.