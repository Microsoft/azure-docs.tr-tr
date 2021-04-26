---
title: IoT portalı için Defender 'daki algılayıcıları yönetme
description: IoT portalı için Defender 'da sensöri ekleme, görüntüleme ve yönetme hakkında bilgi edinin.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752723"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>IoT portalı için Defender 'daki algılayıcıları yönetme

Bu makalede, [IoT portalı Için Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)'da algılayıcıları ekleme, görüntüleme ve yönetme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="onboard-sensors"></a>Algılayıcı ekleme

IoT için Azure Defender ile kaydederek bir algılayıcı ekleme ve bir algılayıcı etkinleştirme dosyası indirme.

### <a name="register-the-sensor"></a>Algılayıcıyı Kaydet

Kaydolmak için:

1. [IoT portalındaki Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)'Daki **hoş geldiniz** sayfasına gidin.
1. **Yerleşik algılayıcı** seçin.
1. Bir algılayıcı adı oluşturun. Adının bir parçası olarak yüklediğiniz sensör IP adresini dahil etmenizi veya kolayca tanımlanabilir bir ad kullanmanızı öneririz. Bu, [IoT portalındaki Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 'daki kayıt adı ve algılayıcı konsolunda görüntülenecek olan dağıtılan algılayıcının IP 'si ile ilgili daha kolay izleme ve tutarlı adlandırma sağlar.
1. Algılayıcıyı bir Azure aboneliğiyle ilişkilendirin.
1. **Buluta bağlı** geçişi kullanarak bir algılayıcı bağlantı modu seçin. Geçiş açık ise, algılayıcı buluta bağlanır. Geçiş kapalıysa, algılayıcı yerel olarak yönetilir.

   - **Buluta bağlı algılayıcılar**: algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Uyarı bilgileri bir IoT Hub 'ı aracılığıyla dağıtılır ve Azure Sentinel gibi diğer Azure hizmetleriyle paylaşılabilir. Ayrıca, tehdit bilgileri paketleri IoT portalı için Azure Defender 'dan sensörlerden alınabilir. Buna karşılık, algılayıcı buluta bağlı değilse, Threat Intelligence paketlerini indirmeniz ve ardından bunları kurumsal sensörlerinize yüklemeniz gerekir. İş için Defender 'ın paketleri sensörlerden göndermeye izin vermek için, **Otomatik tehdit zekası güncelleştirmeleri** geçiş özelliğini etkinleştirin. Daha fazla bilgi için bkz. [Threat Intelligence Research and Packages](how-to-work-with-threat-intelligence-packages.md).
   Bu algılayıcı ve IoT portalı için Azure Defender arasında bir ağ geçidi olarak kullanılacak bir IoT Hub 'ı seçin. Bir site adı ve bölgesi tanımlayın. Açıklayıcı Etiketler de ekleyebilirsiniz. Site adı, bölge ve Etiketler, [siteler ve algılayıcılar sayfasında](#view-onboarded-sensors)açıklayıcı girişlerdir.

   - **Yerel olarak yönetilen sensörler**: algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Bir AIR-gapped ağında çalışıyorsanız ve birden çok yerel olarak yönetilen algılayıcı tarafından algılanan tüm bilgilerin birleştirilmiş bir görünümünü istiyorsanız, şirket içi yönetim konsoluyla çalışın.

   Buluta bağlı sensörler için, ekleme sırasında tanımlanan ad, algılayıcı konsolunda görünen addır. Bu adı konsolundan doğrudan değiştiremezsiniz. Yerel olarak yönetilen sensörler için, ekleme sırasında uygulanan AD Azure 'da depolanır, ancak algılayıcı konsolunda güncelleştirilebilen bir işlem olabilir.

### <a name="download-the-sensor-activation-file"></a>Algılayıcı etkinleştirme dosyasını indirin

Algılayıcı etkinleştirme dosyası, sensör yönetim moduyla ilgili yönergeleri içerir. Dağıttığınız her algılayıcı için benzersiz bir etkinleştirme dosyası indirirler. Algılayıcı konsolunda ilk kez oturum açan bir Kullanıcı, etkinleştirme dosyasını sensöre yükler.

Bir etkinleştirme dosyasını indirmek için:

1. **Algılayıcısı** ekleme sayfasında, **etkinleştirme dosyasını indir**' i seçin.
1. Dosyayı ilk kez algılayıcı konsolunda oturum açan kullanıcı için erişilebilir yapın.

## <a name="view-onboarded-sensors"></a>Eklendi sensörleri görüntüle

[IoT Portal Için Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)'da eklendi sensörleri hakkında önemli işletimsel bilgileri görüntüleyebilirsiniz.

1. **Siteleri ve Algılayıcılar ' ı** seçin. Sayfa, kaç sensörün eklendi, bulut bağlı ve yerel olarak yönetilen sensörların sayısını ve şunları gösterir:

- ekleme sırasında atanan algılayıcı adı.
- bağlantı türü (bulut bağlı veya yerel olarak yönetilen)
- sensörle ilişkilendirilen bölge.
- Yüklü algılayıcı sürümü
- Buluta yönelik algılayıcı bağlantısı durumu.
- Sensörin buluta bağlanırken algıladığı son zaman.

## <a name="manage-onboarded-sensors"></a>Eklendi sensörleri yönetme

Sensörlerden ilgili yönetim görevleri için, [IoT portalı Için Defender 'ı](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) kullanın.

Eklendi sensörleri **siteler ve algılayıcılar** sayfasında görüntülenebilir. Ayrıca, bu sayfadan algılayıcı bilgilerini düzenleyebilirsiniz.

### <a name="export-sensor-details"></a>Algılayıcı ayrıntılarını dışarı aktar

Eklendi algılayıcı bilgilerini dışarı aktarmak için, **siteler ve algılayıcılar** sayfasının en üstündeki **dışarı aktar** simgesini seçin.

### <a name="edit-sensor-zone-details"></a>Algılayıcı bölgesi ayrıntılarını Düzenle

Algılayıcı adını ve bölgesini düzenlemek için **siteler ve algılayıcılar** düzenleme seçeneklerini kullanın.

Düzenlemek için:

1. Düzenlemek istediğiniz algılayıcı için **üç nokta** (**...**) simgesini seçin.
1. **Düzenle**'yi seçin.
1. Algılayıcı bölgesini güncelleştirin veya yeni bir bölge oluşturun.

### <a name="delete-a-sensor"></a>Algılayıcıyı silme

Buluta bağlı bir algılayıcı silerseniz, bilgiler IoT Hub 'ına gönderilmez. Artık bunlarla çalışmadığınızda, yerel olarak bağlı sensörlerden silin.

Bir algılayıcıyı silmek için:

1. Silmek istediğiniz algılayıcı için üç nokta (**...**) simgesini seçin.
1. Silme işlemini onaylayın.

### <a name="reactivate-a-sensor"></a>Algılayıcıyı yeniden etkinleştirme 

Şunları yapmak istediğiniz için sensörinizi yeniden etkinleştirmeniz gerekebilir:

- **Yerel olarak yönetilen mod yerine buluta bağlı modda çalışın**: yeniden etkinleştirmeyi tamamladıktan sonra, algılayıcı algılamaları sensörde görüntülenir ve yeni algılanan uyarı bilgileri IoT Hub 'ı aracılığıyla gönderilir. Bu bilgiler, Azure Sentinel gibi diğer Azure hizmetleriyle paylaşılabilir.

- **Bulut bağlantılı mod yerine yerel olarak yönetilen modda çalışın**: yeniden etkinleştirmeyi tamamladıktan sonra, algılayıcı algılama bilgileri yalnızca sensörde görüntülenir.

- **Algılayıcıyı yeni bir IoT Hub 'ı Ile ilişkilendirin**: bunu yapmak için, algılayıcıyı yeni bir hub ile yeniden kaydedin ve ardından yeni bir etkinleştirme dosyası indirin.

Bir algılayıcıyı yeniden etkinleştirmek için:

1. [IoT portalı Için Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)'daki **siteler ve algılayıcılar** sayfasına gidin.

2. Yeni bir etkinleştirme dosyası yüklemek istediğiniz algılayıcıyı seçin.

3. Algılayıcıyı silin.

4. Başlangıç sayfasından **bir algılayıcı** Ekle ' ye tıklayarak algılayıcıyı yeni modda veya yeni bir IoT Hub 'ı ile yeniden ekleyin.

5. Etkinleştirme dosyasını indirin.

1. IoT algılayıcısı konsolunda Defender 'da oturum açın.

7. Algılayıcı konsolunda, **sistem ayarları** ' nı seçin ve yeniden **etkinleştirme**' yi seçin.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Algılayıcıyı yeniden etkinleştirmek için etkinleştirme dosyanızı karşıya yükleyin.":::

8. **Karşıya yükle** ' yi seçin ve ekleme algılayıcısı sayfasından kaydettiğiniz dosyayı seçin.

9. **Etkinleştir**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Algılayıcınızı etkinleştirme ve ayarlama](how-to-activate-and-set-up-your-sensor.md)
