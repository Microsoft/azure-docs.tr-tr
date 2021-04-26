---
title: Azure Pipelines ile, çekme ayarları fromo uygulama yapılandırması
description: Anahtar değerlerini bir uygulama yapılandırma deposuna çekmek için Azure Pipelines kullanmayı öğrenin
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056796"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines ile uygulama yapılandırmasına yönelik çekme ayarları

[Azure uygulama yapılandırma](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) görevi, uygulama yapılandırma deponuzdan anahtar değerlerini çeker ve bunları sonraki görevler tarafından tüketilen Azure işlem hattı değişkenleri olarak ayarlar. Bu görev, anahtar değerlerini bir yapılandırma dosyasından uygulama yapılandırma deponuza ileten [Azure uygulama yapılandırma gönderim](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) görevini tamamlar. Daha fazla bilgi için bkz. [Azure Pipelines ayarları uygulama yapılandırmasına gönderme](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Uygulama yapılandırma deposu- [Azure Portal](https://portal.azure.com)ücretsiz olarak bir tane oluşturun.
- Azure DevOps projesi- [ücretsiz olarak bir tane oluşturun](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure uygulama yapılandırma görevi- [Visual Studio Market](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)ücretsiz indirin.  

## <a name="create-a-service-connection"></a>Hizmet bağlantısı oluşturma

Bir [hizmet bağlantısı](/azure/devops/pipelines/library/service-endpoints) Azure DevOps projenizden Azure aboneliğinizdeki kaynaklara erişmenizi sağlar.

1. Azure DevOps 'da, hedef işlem hattınızı içeren projeye gidin ve sol alttaki **Proje ayarlarını** açın.
1. İşlem **hatları** altında **hizmet bağlantıları**' nı seçin.
1. Mevcut hizmet bağlantılarınız yoksa, ekranın ortasında **hizmet bağlantısı oluştur** düğmesine tıklayın. Aksi takdirde, sayfanın sağ üst köşesindeki **yeni hizmet bağlantısı** ' na tıklayın.
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

## <a name="add-role-assignment"></a>Rol ataması ekle

Görevin uygulama yapılandırma deposuna erişebilmesi için, görev içinde kullanılan hizmet bağlantısına uygun uygulama yapılandırma rolünü atayın.

1. Hedef uygulama yapılandırma deponuza gidin. Bir uygulama yapılandırma deposu ayarlamaya yönelik bir anlatım için bkz. Azure Uygulama yapılandırması hızlı başlangıçlarından birinde [uygulama yapılandırma deposu oluşturma](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) .
1. Sol tarafta **erişim denetimi (IAM)** seçeneğini belirleyin.
1. Sağ tarafta **rol atamaları Ekle** düğmesine tıklayın.
![Ekran görüntüsü rol atamaları Ekle düğmesini gösterir. ](./media/add-role-assignment-button.png) ..
1. **Rol** altında **uygulama yapılandırma veri okuyucusu**' nu seçin. Bu rol, görevin uygulama yapılandırma deposundan okumasına izin verir. 
1. Önceki bölümde oluşturduğunuz hizmet bağlantısıyla ilişkili hizmet sorumlusunu seçin.
![Ekran görüntüsü rol ataması Ekle iletişim kutusunu gösterir.](./media/add-role-assignment-reader.png)

> [!NOTE]
> Uygulama yapılandırmasındaki Azure Key Vault başvurularını çözümlemek için, hizmet bağlantısına aynı zamanda başvurulan Azure anahtar kasalarında gizli dizileri okumak için de izin verilmelidir.
  
## <a name="use-in-builds"></a>Derlemelerde kullan

Bu bölümde, Azure DevOps derleme işlem hattında Azure uygulama yapılandırma görevinin nasıl kullanılacağı ele alınmaktadır.

1. **Ardışık** düzen işlem hatları ' na tıklayarak derleme ardışık düzeni sayfasına gidin  >  . Derleme ardışık düzeni belgeleri için bkz.  [ilk işlem hattınızı oluşturma](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Yeni bir derleme işlem hattı oluşturuyorsanız, işlemin son adımında, **Gözden geçirme** sekmesinde, işlem hattının sağ tarafında bulunan **yardımcıyı göster** ' i seçin.
      ![Ekran görüntüsü yeni bir işlem hattı için yardımcıyı göster düğmesini gösterir.](./media/new-pipeline-show-assistant.png)
      - Varolan bir yapı ardışık düzeni kullanıyorsanız, sağ üst köşedeki **Düzenle** düğmesine tıklayın.
      ![Ekran görüntüsü, mevcut bir işlem hattının Düzenle düğmesini gösterir.](./media/existing-pipeline-show-assistant.png)
1. **Azure uygulama yapılandırma** görevini arayın.
![Ekran görüntüsü arama kutusuna Azure Uygulama yapılandırması ile Görev Ekle iletişim kutusunu gösterir.](./media/add-azure-app-configuration-task.png)
1. Uygulama yapılandırma deposundan anahtar değerlerini çekmek üzere görev için gerekli parametreleri yapılandırın. Parametrelerin açıklamaları, aşağıdaki **Parametreler** bölümünde ve her parametrenin yanındaki araç ipuçlarında bulunur.
      - **Azure abonelik** parametresini, önceki adımda oluşturduğunuz hizmet bağlantısının adına ayarlayın.
      - **Uygulama yapılandırma adını** uygulama yapılandırma deponuzın kaynak adı olarak ayarlayın.
      - Kalan parametreler için varsayılan değerleri bırakın.
![Ekran görüntüsü uygulama yapılandırma görevi parametrelerini gösterir.](./media/azure-app-configuration-parameters.png)
1. Bir derlemeyi kaydedin ve kuyruğa alın. Yapı günlüğünde, görevin yürütülmesi sırasında oluşan tüm arızalar görüntülenir.

## <a name="use-in-releases"></a>Sürümlerde kullan

Bu bölümde, Azure DevOps sürüm ardışık düzeninde Azure uygulama yapılandırma görevinin nasıl kullanılacağı ele alınacaktır.

1. İşlem hattı Yayınları ' **nı seçerek yayın** ardışık düzeni sayfasına gidin  >  . Yayın işlem hattı belgeleri için bkz. [yayın işlem hatları](/azure/devops/pipelines/release).
1. Mevcut bir yayın işlem hattını seçin. Bir tane yoksa yeni bir işlem hattı oluşturmak için yeni işlem **hattı** ' na tıklayın.
1. Yayın işlem hattını düzenlemek için sağ üst köşedeki **Düzenle** düğmesini seçin.
1. **Görevler** açılan menüsünde, görevi eklemek istediğiniz **aşamayı** seçin. Aşamalar hakkında daha fazla bilgiyi [burada](/azure/devops/pipelines/release/environments)bulabilirsiniz.
![Ekran görüntüsü görevler açılan menüsünde seçilen aşamayı gösterir.](./media/pipeline-stage-tasks.png)
1. **+** Yeni bir görev eklemek Istediğiniz işin ileri ' ye tıklayın.
![Ekran görüntüsü işin yanındaki artı düğmesini gösterir.](./media/add-task-to-job.png)
1. **Azure uygulama yapılandırma** görevini arayın.
![Ekran görüntüsü arama kutusuna Azure Uygulama yapılandırması ile Görev Ekle iletişim kutusunu gösterir.](./media/add-azure-app-configuration-task.png)
1. Anahtar değerlerinizi uygulama yapılandırma deponuzdan çekmek için görev içinde gerekli parametreleri yapılandırın. Parametrelerin açıklamaları, aşağıdaki **Parametreler** bölümünde ve her parametrenin yanındaki araç ipuçlarında bulunur.
      - **Azure abonelik** parametresini, önceki adımda oluşturduğunuz hizmet bağlantısının adına ayarlayın.
      - **Uygulama yapılandırma adını** uygulama yapılandırma deponuzın kaynak adı olarak ayarlayın.
      - Kalan parametreler için varsayılan değerleri bırakın.
1. Bir yayını kaydedin ve kuyruğa alın. Yayın günlüğünde, görevin yürütülmesi sırasında karşılaşılan tüm arızalar görüntülenir.

## <a name="parameters"></a>Parametreler

Aşağıdaki parametreler Azure uygulama yapılandırma görevi tarafından kullanılır:

- **Azure aboneliği**: kullanılabilir Azure hizmeti bağlantılarınızı içeren bir açılır liste. Kullanılabilir Azure hizmeti bağlantıları listenizi güncelleştirmek ve yenilemek için, TextBox 'ın sağ tarafındaki **Azure aboneliğini yenile** düğmesine basın.
- **Uygulama yapılandırma adı**: seçili abonelik altında kullanılabilir yapılandırma depolarınızı yükleyen bir açılan liste. Kullanılabilir yapılandırma depoları listenizi güncelleştirmek ve yenilemek için, TextBox 'ın sağ tarafındaki **uygulama yapılandırma adını Yenile** düğmesine basın.
- **Anahtar filtresi**: filtre, Azure Uygulama yapılandırmasından hangi anahtar değerlerinin istendiğini belirlemek için kullanılabilir. * Değeri, tüm anahtar değerlerini seçer. Hakkında daha fazla bilgi için bkz. [sorgu anahtarı değerleri](concept-key-value.md#query-key-values).
- **Etiket**: uygulama yapılandırma deposundan anahtar değerleri seçerken hangi etiketin kullanılması gerektiğini belirtir. Etiket sağlanmazsa, etiketi olmayan anahtar değerleri alınacaktır. Şu karakterlere izin verilmez:, *.
- **Kırpma anahtarı ön eki**: değişken olarak ayarlamadan önce uygulama yapılandırma anahtarlarından kırpılacak bir veya daha fazla ön ek belirtir. Birden çok önek, yeni satır karakteriyle ayrılabilir.

## <a name="use-key-values-in-subsequent-tasks"></a>Sonraki görevlerde anahtar değerlerini kullanma

Uygulama yapılandırmasından getirilen anahtar değerleri, ortam değişkenleri olarak erişilebilen işlem hattı değişkenleri olarak ayarlanır. Ortam değişkeninin anahtarı, belirtilmişse öneki kırpdıktan sonra uygulama yapılandırmasından alınan anahtar-değer anahtarıdır.

Örneğin, sonraki bir görev bir PowerShell betiği çalıştırıyorsa, şunun gibi ' myBuildSetting ' anahtarına sahip bir anahtar değeri tüketebilir:
```powershell
echo "$env:myBuildSetting"
```
Ve değer konsola yazdırılır.

> [!NOTE]
> Uygulama Yapılandırması içindeki Azure Key Vault başvuruları çözümlenir ve [gizli değişkenler](/azure/devops/pipelines/process/variables#secret-variables)olarak ayarlanır. Azure işlem hatları 'nda gizli değişkenler, günlükte maskelenir. Görevlere ortam değişkenleri olarak geçirilmez ve bunun yerine giriş olarak geçirilmesi gerekir. 

## <a name="troubleshooting"></a>Sorun giderme

Beklenmeyen bir hata oluşursa, işlem hattı değişkeni olarak ayarlanarak hata ayıklama günlükleri etkinleştirilebilir `system.debug` `true` .

## <a name="faq"></a>SSS

**Nasıl yaparım? birden çok anahtardan ve etiketlerden yapılandırmadan mi oluştur?**

Yapılandırmanın çoklu etiketlerden oluşturulması gerekebilecek durumlar vardır. Örneğin, varsayılan ve dev. Bu senaryoyu uygulamak için tek bir işlem hattında birden çok uygulama yapılandırma görevi kullanılabilir. Daha sonraki bir adımda bir görev tarafından getirilen anahtar değerleri, önceki adımlardan gelen değerlerin yerini alır. Yukarıda bahsedilen örnekte, ikinci bir görev dev etiketiyle anahtar değerlerini seçerken varsayılan etiketle anahtar değerlerini seçmek için bir görev kullanılabilir. Geliştirme etiketine sahip anahtarlar, varsayılan etiketle aynı anahtarları geçersiz kılar.
