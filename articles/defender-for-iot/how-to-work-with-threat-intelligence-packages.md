---
title: Tehdit zekası verilerini güncelleştirme
description: Tehdit bilgileri veri paketi, her bir IoT sürümü için yeni bir Defender ile veya yayınlar arasında gerekliyse sağlanır.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750473"
---
# <a name="threat-intelligence-research-and-packages"></a>Tehdit bilgileri araştırması ve paketleri #
## <a name="overview"></a>Genel Bakış ##

Microsoft 'taki güvenlik ekipleri, özel ICS tehdit zekasını ve güvenlik açığı araştırmasını çalıştırır. Bu takımlar, MSTIK (Microsoft Threat Intelligence Center), DART (Microsoft algılama ve yanıt ekibi), DCU (dijital Crimes birimi) ve Bölüm 52 (ICS 'ye özgü sıfır gün, ters mühendislik kötü amaçlı yazılım, kampanyalar ve duyuru işlemleri)

Takımlar, Microsoft 'a güvenlik algılama, analiz ve yanıt sağlar:

- Bulut altyapısı ve hizmetleri.
- Geleneksel ürün ve cihazlar.
- Dahili kurumsal kaynaklar.

Güvenlik ekipleri şu avantajları elde edebilir:

- Bilinen ve ilgili tehditlere karşı koruma.
- Önceliklendirme ve önceliklendirmenize yardımcı olan Öngörüler.
- Tehditler etkilenmeden önce tam olarak tüm tehditlerin anlaşılmasıdır.
- Daha ilgili, doğru ve işlem yapılabilir veriler.

Bu zeka, Microsoft Platform analizlerini zenginleştirme ve şirketin yönetilen hizmetlerini olay yanıtı ve ihlal araştırması için destekleyen bağlamsal bilgiler sağlar. Tehdit bilgileri paketleri, imzaları (kötü amaçlı yazılım imzaları dahil), Ckları ve diğer güvenlik içeriğini içerir.

## <a name="when-are-packages-delivered"></a>Paketler ne zaman dağıtılır ##

Tehdit zekası paketleri ayda yaklaşık bir kez veya daha sık bir şekilde sağlanır. Yeni paketlerle ilgili duyurular şu kaynaktan bulunabilir: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Tehdit zekası paketlerini sensörlerinize güncelleştirme ##

Tehdit zekası paketlerini sensörlerinize güncelleştirmek için üç seçenek mevcuttur:

- Program IoT için Defender tarafından teslim edildiği sürece paketleri sensöre otomatik olarak gönderin.
- Tehdit zekası paketini gereken şekilde sensöre el ile gönderin.
- Bir paket indirin ve sonra bir sensöre veya birden çok sensöre yükleyin.

IoT güvenlik okuyucusu için Defender izinleri olan kullanıcılar otomatik olarak paketleri algılayıcılarla el ile gönderebilir.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Tehdit zekası güncelleştirmelerini sensörlerden otomatik olarak gönder ###

Tehdit bilgileri paketleri, bir IoT için Defender tarafından yayımlandıklarında otomatik olarak *bulut bağlantılı* sensörler için güncelleştirilir. **Otomatik tehdit bilgileri güncelleştirmeleri** seçeneği etkinken, buluta bağlı sensörinizi ekleyerek otomatik paket güncelleştirme ' ye emin olun. Daha fazla bilgi için bkz. [algılayıcı ekleme](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Tehdit zekası güncelleştirmelerini sensörlerden el ile gönderin ###

*Bulut bağlantılı* sensörleriniz, tehdit bilgileri paketleriyle otomatik olarak güncelleştirilir. Ancak, daha fazla koruyucu bir yaklaşım almak isterseniz, IoT portalı için Azure Defender 'dan yalnızca gerekli olduğunu hissettiğinizde sensörlerden gelen paketleri gönderebilirsiniz.
Bu, bir paketin ne zaman yükleneceğini, indirmenize ve sonra Sensörlerinizi yüklemeye gerek kalmadan denetlemenize olanak tanır.

**Paketleri el ile göndermek için:**

1. IoT **siteleri ve sensör** Için Azure Defender sayfasına gidin.
1. Bir algılayıcı için üç nokta (...) simgesini seçin ve ardından **tehdit zekası güncelleştirmesini gönder**' i seçin. **Tehdit bilgileri güncelleştirme durumu** alanı güncelleştirme ilerlemesini görüntüler.

#### <a name="change-the-threat-intelligence-update-mode"></a>Tehdit zekası güncelleştirme modunu değiştirme ####

İlk ekleme işleminden sonra algılayıcı tehdit bilgileri güncelleştirme modunu değiştirebilirsiniz.

**Güncelleştirme modunu değiştirmek için:**

1. Bir algılayıcı için üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.
1. **Otomatik tehdit bilgileri güncelleştirmelerini** etkinleştirin veya devre dışı bırakın.

### <a name="download-packages-and-upload-to-sensors"></a>Paketleri indir ve sensörlerden yükle ###

Paketler, IoT portalı için Defender 'dan indirilebilir ve bireysel sensörlerden el ile yüklenebilir. Şirket içi yönetim konsolu Sensörlerinizi yönetirse, tehdit zekaları paketlerini yönetim konsoluna indirebilir ve aynı anda birden çok sensöre gönderebilirsiniz.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="IoT portalı için Azure Defender aracılığıyla güncelleştirmeleri indirin.":::

Bu seçenek hem *bulut bağlantılı* hem de *yerel olarak yönetilen* sensörlerle kullanılabilir.

**Tek bir sensöre yüklemek için:**

1. IoT **güncelleştirmeleri** Için Azure Defender sayfasına gidin.

2. **Tehdit zekası** paketini indirin ve kaydedin.

3. Algılayıcı konsolunda oturum açın.

4. Yan menüde **sistem ayarları**' nı seçin.

5. **Tehdit zekası verilerini** seçin ve ardından **Güncelleştir**' i seçin.

6. Yeni paketi karşıya yükleyin.

**Aynı anda birden çok sensöre yüklemek için:**

1. IoT **güncelleştirmeleri** Için Azure Defender sayfasına gidin.

2. **Tehdit zekası** paketini indirin ve kaydedin.

3. Yönetim konsolunda oturum açın.

4. Yan menüde **sistem ayarları**' nı seçin.

5. **Algılayıcı altyapısı yapılandırma** bölümünde, güncelleştirilmiş paketleri alması gereken sensörlerini seçin.  

6. **Tehdit zekası verilerini seçin** bölümünde artı işaretini ( **+** ) seçin.

7. Paketi karşıya yükleyin.

## <a name="review-package-update-status-on-the-sensor"></a>Sensördeki paket güncelleştirme durumunu gözden geçirme ##

Paket güncelleştirme durumu ve sürüm bilgileri, algılayıcı **sistem ayarları**, **tehdit** bilgileri bölümünde görüntülenir.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Buluta bağlı sensörler için paket bilgilerini gözden geçirme ##

Bulut bağlantılı sensörleriniz için tehdit zekası paketleri hakkında aşağıdaki bilgileri gözden geçirin:

- Paket sürümü yüklendi
- Tehdit bilgileri güncelleştirme modu
- Tehdit zekası güncelleştirme durumu

Tehdit zekası bilgilerini gözden geçirmek için:

1. IoT **siteleri ve sensör** Için Azure Defender sayfasına gidin.
1. Her sensörde yüklü **tehdit zekası sürümünü** gözden geçirin. Sürüm adlandırma, paketin, IoT için Defender tarafından oluşturulduğu güne göre belirlenir.
1. **Tehdit zekası modunu** gözden geçirin. *Otomatik* , yeni kullanılabilir paketlerin, bir IoT için Defender tarafından yayımlandıklarında sensörler üzerine otomatik olarak yükleneceğini gösterir. *El ile* , yeni kullanılabilir paketleri gerektiği gibi doğrudan sensöre gönderebileceğini belirtir.
1. **Tehdit zekası güncelleştirme durumunu** gözden geçirin. Aşağıdaki durumlar görüntülenebilir:

- Başarısız
- Devam Ediyor
- Güncelleştirme var
- Tamam

Bulut bağlantılı tehdit bilgileri güncelleştirmeleri başarısız olursa, **konum ve algılayıcılar** sayfasındaki **algılayıcı durumu** ve **son bağlantılı UTC** sütunlarında bağlantı bilgilerini gözden geçirin. 

## <a name="see-also"></a>Ayrıca bkz.

[Bir algılayıcı ekleme](getting-started.md#onboard-a-sensor)

[Yönetim konsolundan algılayıcıları yönetme](how-to-manage-sensors-from-the-on-premises-management-console.md)