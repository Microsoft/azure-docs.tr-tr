---
title: 'Hızlı başlangıç: Başlarken'
description: Bu hızlı başlangıçta, IoT dağıtımı için Defender için temel iş akışını anlama hakkında bilgi edinebilirsiniz.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750455"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Hızlı başlangıç: IoT için Defender 'ı kullanmaya başlama

Bu makalede IoT için Azure Defender 'ı kurmak için uygulayabileceğiniz adımlara genel bakış sunulmaktadır. İşlem şunları gerektirir:

- IoT Portal için Azure Defender 'da aboneliğinizi ve Sensörlerinizi kaydedin.
- Algılayıcısı ve şirket içi yönetim konsolu yazılımını yükler.
- Algılayıcı ve yönetim konsolunun ilk etkinleştirmesini gerçekleştirin.

## <a name="permission-requirements"></a>İzin gereksinimleri

Bazı kurulum adımları için belirli kullanıcı izinleri gerekir.

Algılayıcı ve yönetim konsolunu etkinleştirmek, SSL/TLS sertifikalarını yüklemek ve yeni parolalar oluşturmak için Yönetici Kullanıcı izinleri gerekir.

Aşağıdaki tabloda IoT Portal araçları için Azure Defender 'a yönelik kullanıcı erişim izinleri açıklanmaktadır:

| İzin | Güvenlik okuyucusu | Güvenlik yöneticisi | Abonelik Katılımcısı | Abonelik sahibi |
|--|--|--|--|--|
| Ayrıntıları ve erişim yazılımlarını, etkinleştirme dosyalarını ve tehdit bilgileri paketlerini görüntüleyin  | ✓ | ✓ | ✓ | ✓ |
| Bir algılayıcı ekleme  |  |  ✓ | ✓ | ✓ |
| Fiyatlandırmayı Güncelleştir  |  |  ✓ | ✓ | ✓ |
| Parolayı kurtar  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Çözüm altyapısını tanımla

**Ağ Kurulum gereksinimlerinizi açıklığa kavuşturun**

Ağ mimarinizi, izlenen bant genişliğini ve diğer ağ ayrıntılarını araştırın. Daha fazla bilgi için bkz. [IoT Ağ kurulumu Için Azure Defender](how-to-set-up-your-network.md).

**Ağ yükünü işlemek için hangi sensörler ve Yönetim Konsolu gereçlerinin gerektiğini açıklığa kavuşturun**

IoT için Azure Defender hem fiziksel hem de sanal dağıtımları destekler. Fiziksel dağıtımlar için çeşitli Sertifikalı gereçler satın alabilirsiniz. Daha fazla bilgi için bkz. [gerekli gereçlerini tanımla](how-to-identify-required-appliances.md).

İzlenecek yaklaşık cihaz sayısını hesaplamanıza önerilir. Daha sonra, Azure aboneliğinizi portala kaydettiğinizde, bu numarayı girmeniz istenir. Numaralar, 1.000 saniyelik aralıklarla eklenebilir. İzlenen cihazların sayıları, *kaydedilmiş cihazlar* olarak adlandırılır.

## <a name="register-with-azure-defender-for-iot"></a>IoT için Azure Defender ile kaydolun

Kayıt şunları içerir:

- IoT için Azure aboneliklerinizi Defender 'a ekleme.
- Kaydedilmiş cihazları tanımlama.
- Şirket içi yönetim konsolu için bir etkinleştirme dosyası indiriliyor.

Kaydolmak için:

1. IoT portalı için Azure Defender ' a gidin.

1. **Yerleşik abonelik**' ı seçin.

1. **Fiyatlandırma** sayfasında bir abonelik seçin ya da yeni bir tane oluşturun ve kaydedilmiş cihazların sayısını ekleyin.

1. **Şirket içi yönetim konsolunu indirin** sekmesini seçin ve indirilen etkinleştirme dosyasını kaydedin. Bu dosya, tanımladığınız toplam kaydedilmiş cihazları içerir. Bu dosya, ilk oturum açma işleminden sonra yönetim konsoluna yüklenir.

Aboneliği boşaltma hakkında daha fazla bilgi için bkz. [aboneliği](how-to-manage-subscriptions.md#offboard-a-subscription)kaldırma.

## <a name="install-and-set-up-the-on-premises-management-console"></a>Şirket içi yönetim konsolunu yükleyip kurma

Şirket içi yönetim konsolu gerecinizi aldıktan sonra:

- IoT portalı için Azure Defender 'dan ISO paketini indirin.
- Yazılımı yükler.
- İlk yönetim konsolu kurulumunu etkinleştirin ve gerçekleştirin.

Yüklemek ve ayarlamak için:

1. IoT portalı için **Defender 'dan Başlarken** ' i seçin.
1. **Şirket içi yönetim konsolu** sekmesini seçin.
1. Bir sürüm seçin ve **İndir**' i seçin.
1. Şirket içi yönetim konsolu yazılımını yükler. Daha fazla bilgi için bkz. [IoT yüklemesi Için Defender](how-to-install-software.md).
1. Yönetim konsolunu etkinleştirin ve ayarlayın. Daha fazla bilgi için bkz. Şirket [içi yönetim konsolunuzu etkinleştirme ve ayarlama](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Bir algılayıcı ekleme ##

IoT için Azure Defender ile kaydederek bir algılayıcı ekleme ve bir algılayıcı etkinleştirme dosyası indirme:

1. Bir algılayıcı adı tanımlayın ve bunu bir abonelikle ilişkilendirin.
1. Bir algılayıcı bağlantı modu seçin:

   - **Buluta bağlı sensörler**: algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Ayrıca, uyarı bilgileri bir IoT Hub 'ı aracılığıyla dağıtılır ve Azure Sentinel gibi diğer Azure hizmetleriyle de paylaşılabilir.  Ayrıca, Azure Defender for IoT portalından sensörlerinize otomatik olarak tehdit zekası paketleri göndermeyi de tercih edebilirsiniz. Daha fazla bilgi için bkz. [Threat Intelligence Research and Packages](how-to-work-with-threat-intelligence-packages.md).

   - **Yerel olarak yönetilen sensörler**: algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Bir AIR-gapped ağında çalışıyorsanız ve birden çok yerel olarak yönetilen algılayıcı tarafından algılanan tüm bilgilerin birleştirilmiş bir görünümünü istiyorsanız, şirket içi yönetim konsoluyla çalışın.

1. Bir algılayıcı etkinleştirme dosyası indirin.

Ekleme hakkında daha fazla bilgi için bkz. about [IoT Portal 'da algılayıcılar ekleme ve yönetme](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Algılayıcıyı kurma ve ayarlama

IoT portalı için Azure Defender 'dan ISO paketini indirin, yazılımı yükleyin ve algılayıcıyı ayarlayın.

1. IoT portalı için **Defender 'dan Başlarken** ' i seçin.

1. **Algılayıcıyı ayarla**' yı seçin.

1. Bir sürüm seçin ve **İndir**' i seçin.

1. Algılayıcı yazılımını yükler. Daha fazla bilgi için bkz. [IoT yüklemesi Için Defender](how-to-install-software.md).

1. Algılayıcıyı etkinleştirin ve ayarlayın. Daha fazla bilgi için bkz. [oturum açma ve algılayıcı etkinleştirme](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Algılayıcıları şirket içi yönetim konsoluna bağlama

Aşağıdakileri sağlamak için algılayıcılar 'ı yönetim konsoluna bağlayın:

- Sensörler, şirket içi yönetim konsoluna uyarı ve cihaz envanter bilgilerini gönderir.

- Şirket içi yönetim konsolu, algılayıcı yedeklemeleri gerçekleştirebilir, sensör algılayan uyarıları yönetebilir, algılayıcı bağlantılarını araştırabilir ve bağlı sensörlerdeki diğer etkinlikleri gerçekleştirebilirler.

Aynı ağları tek bir bölgede izlemek için birden çok algılayıcı gruplandırmalarını öneririz. Bunun yapılması, birden fazla sensör tarafından toplanan bilgileri içine alınır.

Daha fazla bilgi için bkz. [Şirket içi yönetim konsoluna sensörlerden bağlanma](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Azure Sentinel 'i uyarı bilgileriyle doldur (isteğe bağlı)

Azure Sentinel 'i yapılandırarak Azure Sentinel 'e uyarı bilgileri gönderin. Bkz. [IoT Için Defender 'daki verilerinizi Azure Sentinel 'e bağlama](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Sonraki adımlar ##

[IoT için Azure Defender 'a hoş geldiniz](overview.md)

[IoT mimarisi için Azure Defender](architecture.md)
