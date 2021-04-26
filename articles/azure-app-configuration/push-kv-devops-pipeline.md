---
title: Ayarları Azure Pipelines ile uygulama yapılandırmasına gönderme
description: Anahtar değerlerini bir uygulama yapılandırma deposuna göndermek için Azure Pipelines kullanmayı öğrenin
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: e1a4fb52a5f9622758e9ed805bf9380f5f608870
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068298"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Ayarları Azure Pipelines ile uygulama yapılandırmasına gönderme

[Azure uygulama yapılandırma gönderim](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) görevi, anahtar değerlerini bir yapılandırma dosyasından uygulama yapılandırma deponuza gönderir. Bu görev, artık uygulama yapılandırma deposundaki ayarları ve uygulama yapılandırma deposuna gönderme ayarlarını çektiğinizde, işlem hattı içinde tam daire işlevlerini sağlar.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Uygulama yapılandırma kaynağı- [Azure Portal](https://portal.azure.com)ücretsiz olarak bir tane oluşturun.
- Azure DevOps projesi- [ücretsiz olarak bir tane oluşturun](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure uygulama yapılandırma gönderme görevi- [Visual Studio Market](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)ücretsiz indirin.

## <a name="create-a-service-connection"></a>Hizmet bağlantısı oluşturma

Bir [hizmet bağlantısı](/azure/devops/pipelines/library/service-endpoints) Azure DevOps projenizden Azure aboneliğinizdeki kaynaklara erişmenizi sağlar.

1. Azure DevOps 'da, hedef işlem hattınızı içeren projeye gidin ve sol alttaki **Proje ayarlarını** açın.
1. İşlem **hatları** altında **hizmet bağlantıları** ' nı seçin ve sağ üst köşedeki **yeni hizmet bağlantısı** ' nı seçin.
1. **Azure Resource Manager** seçin.
![Ekran görüntüsünde yeni hizmet bağlantısı açılır listesinden Azure Resource Manager seçme gösterilmektedir.](./media/new-service-connection.png)
1. **Kimlik doğrulama yöntemi** Iletişim kutusunda **hizmet sorumlusu (otomatik)** seçeneğini belirleyin.
    > [!NOTE]
    > **Yönetilen kimlik** kimlik doğrulaması şu anda uygulama yapılandırma görevi için desteklenmiyor.
1. Aboneliğinizi ve kaynağı girin. Hizmet bağlantınıza bir ad verin.

Hizmet bağlantınız oluşturuldığına göre, kendisine atanan hizmet sorumlusunun adını bulun. Sonraki adımda bu hizmet sorumlusuna yeni bir rol ataması ekleyeceksiniz.

1. **Proje ayarları**  >  **hizmet bağlantıları**' na gidin.
1. Önceki bölümde oluşturduğunuz hizmet bağlantısını seçin.
1. **Hizmet sorumlusunu Yönet**' i seçin.
1. Listelenen **görünen adı** dikkate alın.
![Ekran görüntüsü hizmet sorumlusu görünen adını gösterir.](./media/service-principal-display-name.png)

## <a name="add-role-assignment"></a>Rol ataması ekle

Görevin uygulama yapılandırma deposuna erişebilmesi için görev içinde kullanılan kimlik bilgilerine uygun uygulama yapılandırma rolü atamalarını atayın.

1. Hedef uygulama yapılandırma deponuza gidin. 
1. Sol tarafta **erişim denetimi (IAM)** seçeneğini belirleyin.
1. Sağ tarafta **rol atamaları Ekle** düğmesine tıklayın.
![Ekran görüntüsü rol atamaları Ekle düğmesini gösterir.](./media/add-role-assignment-button.png)
1. **Rol** altında, **uygulama yapılandırma veri sahibi**' nı seçin. Bu rol, görevin uygulama yapılandırma deposundan okuma ve yazma yapmasına izin verir. 
1. Önceki bölümde oluşturduğunuz hizmet bağlantısıyla ilişkili hizmet sorumlusunu seçin.
![Ekran görüntüsü rol ataması Ekle iletişim kutusunu gösterir.](./media/add-role-assignment.png)

  
## <a name="use-in-builds"></a>Derlemelerde kullan

Bu bölümde, Azure DevOps derleme işlem hattında Azure uygulama yapılandırma gönderim görevinin nasıl kullanılacağı ele alınmaktadır.

1. **Ardışık** düzen işlem hatları ' na tıklayarak derleme ardışık düzeni sayfasına gidin  >  . Derleme işlem hatları için belgeler [burada](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)bulunabilir.
      - Yeni bir derleme işlem hattı oluşturuyorsanız, işlemin son adımında, **Gözden geçirme** sekmesinde, işlem hattının sağ tarafında bulunan **yardımcıyı göster** ' i seçin.
      ![Ekran görüntüsü yeni bir işlem hattı için yardımcıyı göster düğmesini gösterir.](./media/new-pipeline-show-assistant.png)
      - Varolan bir yapı ardışık düzeni kullanıyorsanız, sağ üst köşedeki **Düzenle** düğmesine tıklayın.
      ![Ekran görüntüsü, mevcut bir işlem hattının Düzenle düğmesini gösterir.](./media/existing-pipeline-show-assistant.png)
1. **Azure uygulama yapılandırma gönderme** görevini arayın.
![Ekran görüntüsü arama kutusuna Azure uygulama yapılandırma gönderim ile Görev Ekle iletişim kutusunu gösterir.](./media/add-azure-app-configuration-push-task.png)
1. Yapılandırma dosyasından uygulama yapılandırma deposuna anahtar değerlerini göndermek için gereken parametreleri yapılandırın. Parametrelerin açıklamaları aşağıdaki **Parametreler** bölümünde ve her parametrenin yanındaki araç ipuçlarında kullanılabilir.
![Ekran görüntüsü, uygulama yapılandırma gönderme görevi parametrelerini gösterir.](./media/azure-app-configuration-push-parameters.png)
1. Bir derlemeyi kaydedin ve kuyruğa alın. Yapı günlüğünde, görevin yürütülmesi sırasında oluşan tüm arızalar görüntülenir.

## <a name="use-in-releases"></a>Sürümlerde kullan

Bu bölümde, Azure uygulama yapılandırma gönderme görevinin bir Azure DevOps yayın işlem hatlarında nasıl kullanılacağı ele alınacaktır.

1. İşlem hattı Yayınları ' **nı seçerek yayın** ardışık düzeni sayfasına gidin  >  . Yayın işlem hatlarına yönelik belgeler [burada](/azure/devops/pipelines/release)bulunabilir.
1. Mevcut bir yayın işlem hattını seçin. Yoksa, yeni bir tane oluşturmak için **+ Yeni** ' yi seçin.
1. Yayın işlem hattını düzenlemek için sağ üst köşedeki **Düzenle** düğmesini seçin.
1. **Görevler** açılan menüsünde, görevi eklemek istediğiniz **aşamayı** seçin. Aşamalar hakkında daha fazla bilgiyi [burada](/azure/devops/pipelines/release/environments)bulabilirsiniz.
![Ekran görüntüsü görevler açılan menüsünde seçilen aşamayı gösterir.](./media/pipeline-stage-tasks.png)
1. **+** Yeni bir görev eklemek Istediğiniz işin ileri ' ye tıklayın.
![Ekran görüntüsü işin yanındaki artı düğmesini gösterir.](./media/add-task-to-job.png)
1. **Görev Ekle** iletişim kutusunda, arama kutusuna **Azure Uygulama yapılandırması gönderme** yazın ve seçin.
1. Anahtar değerlerinizi yapılandırma dosyanızdaki uygulama yapılandırma deponuza göndermek için görev içinde gerekli parametreleri yapılandırın. Parametrelerin açıklamaları aşağıdaki **Parametreler** bölümünde ve her parametrenin yanındaki araç ipuçlarında kullanılabilir.
1. Bir yayını kaydedin ve kuyruğa alın. Yayın günlüğünde, görevin yürütülmesi sırasında karşılaşılan tüm arızalar görüntülenir.

## <a name="parameters"></a>Parametreler

Aşağıdaki parametreler uygulama yapılandırma gönderme görevi tarafından kullanılır:

- **Azure aboneliği**: kullanılabilir Azure hizmeti bağlantılarınızı içeren bir açılır liste. Kullanılabilir Azure hizmeti bağlantıları listenizi güncelleştirmek ve yenilemek için, TextBox 'ın sağ tarafındaki **Azure aboneliğini yenile** düğmesine basın.
- **Uygulama yapılandırma adı**: seçili abonelik altında kullanılabilir yapılandırma depolarınızı yükleyen bir açılan liste. Kullanılabilir yapılandırma depoları listenizi güncelleştirmek ve yenilemek için, TextBox 'ın sağ tarafındaki **uygulama yapılandırma adını Yenile** düğmesine basın.
- **Yapılandırma dosyası yolu**: yapılandırma dosyanızın yolu. **Yapılandırma dosyası yolu** parametresi dosya deposunun kökünde başlar. Yapılandırma dosyası seçmek için yapı yapıtına göz atabilirsiniz. ( `...` TextBox 'ın sağ tarafındaki düğme). Desteklenen dosya biçimleri şunlardır: YAML, JSON, Özellikler. JSON biçiminde örnek bir yapılandırma dosyası aşağıda verilmiştir.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Ayırıcı**:. JSON ve. yml dosyalarını düzleştirmek için kullanılan ayırıcı.
- **Derinlik**:. JSON ve. yml dosyalarının ne kadar düzleştirilmesini sağlayacak derinlik.
- **Ön ek**: uygulama yapılandırma deposuna gönderilen her bir anahtarın başlangıcına eklenen bir dize.
- **Etiket**: uygulama yapılandırma deposundaki etiket olarak her anahtar değerine eklenen bir dize.
- **Içerik türü**: uygulama yapılandırma deposu içindeki içerik türü olarak her anahtar değerine eklenen bir dize.
- **Etiketler**: `{"tag1":"val1", "tag2":"val2"}` uygulama yapılandırma deponuza gönderilen her anahtar değerine eklenen etiketleri TANıMLAYAN biçiminde bir JSON nesnesi.
- **Depodaki tüm diğer Key-Values Belirtilen önek ve etiketle Sil**: varsayılan değer **işaretli** değildir.
  - **Checked**: yapılandırma dosyasından yeni anahtar değerleri göndermeden önce belirtilen önek ve etiketle eşleşen uygulama yapılandırma deposundaki tüm anahtar değerlerini kaldırır.
  - **İşaretlenmemiş**: yapılandırma dosyasındaki tüm anahtar değerlerini uygulama yapılandırma deposuna gönderir ve uygulama yapılandırma deposundaki diğer her şeyi bozulmadan bırakır.



## <a name="troubleshooting"></a>Sorun giderme

Beklenmeyen bir hata oluşursa, işlem hattı değişkeni olarak ayarlanarak hata ayıklama günlükleri etkinleştirilebilir `system.debug` `true` .

## <a name="faq"></a>SSS

**Birden çok yapılandırma dosyasını nasıl karşıya yükleyebilirim?**

Birden çok yapılandırma dosyasını uygulama yapılandırma deposuna göndermek için aynı işlem hattı içinde Azure uygulama yapılandırma gönderim görevinin birden fazla örneğini oluşturun.

**Anahtar değerlerini yapılandırma depoma göndermeye çalışırken neden 409 hatası alıyorum?**

Görev, uygulama yapılandırma deposunda kilitlenen bir anahtar değerini kaldırmaya veya üzerine yazmaya çalışırsa bir 409 çakışma hata iletisi oluşur.
