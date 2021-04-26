---
title: Azure Event Grid kullanarak olayları Auth0 'dan Azure 'a gönderme
description: Azure Event Grid ile Azure hizmetlerine olayları sonlandırma Auth0.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077872"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Azure Event Grid Auth0 ile tümleştirme

Bu makalede, bir Event Grid iş ortağı konusu oluşturarak Auth0 ve Azure hesaplarınızın nasıl bağlanacağı açıklanır.

Auth0 tarafından desteklenen olayların tam listesi için bkz. [Auth0 olay türü kodları](https://auth0.com/docs/logs/references/log-event-type-codes)

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Olayları Auth0 'dan Azure Event Grid gönder
Azure 'a Auth0 olayları göndermek için:

1. Event Grid kaynak sağlayıcısını etkinleştirme
1. Auth0 panosunda bir Auth0 partner konusu ayarlama
1. Azure 'da Iş ortağı konusunu etkinleştirin
1. Auth0 'ten olaylara abone olma

Bu kavramlar hakkında daha fazla bilgi için bkz. Event Grid [Kavramlar](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Event Grid kaynak sağlayıcısını etkinleştirme
Daha önce Event Grid kullanmadığınız takdirde, Event Grid Kaynak sağlayıcısını kaydetmeniz gerekir. Daha önce Event Grid kullandıysanız, sonraki bölüme atlayın.

Azure portalında:
1. Sol taraftaki menüden abonelikler ' i seçin
1. Event Grid için kullanmakta olduğunuz aboneliği seçin
1. Sol taraftaki menüde, Ayarlar ' ın altında, kaynak sağlayıcıları ' nı seçin.
1. Microsoft. EventGrid 'i bulun
1. Kaydol ' u seçin
1. Durumun kayıtlı olarak değiştiği emin olmak için Yenile

### <a name="set-up-an-auth0-partner-topic"></a>Bir Auth0 partner konusu ayarlama
Tümleştirme sürecinin bir parçası, bir olay kaynağı olarak kullanılmak üzere Auth0 ayarlanmesidir (Bu adım [Auth0 panonuzda](https://manage.auth0.com/)olur).

1. [Auth0 panosunda](https://manage.auth0.com/)oturum açın.
1. Günlüklere > akışlarına gidin.
1. + Akış oluştur ' a tıklayın.
1. Azure Event Grid ' yi seçin ve yeni Stream için benzersiz bir ad girin.
1. Azure abonelik KIMLIĞINIZI, Azure bölgenizi ve bir kaynak grubu adını sağlayarak olay kaynağını oluşturun. 
1. Kaydet’e tıklayın.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Azure 'da Auth0 Iş ortağı konusunu etkinleştirin
Azure 'da Auth0 konusunun etkinleştirilmesi, olayların Auth0 'ten Azure 'a akmasını sağlar.

1. Azure portalında oturum açın.
1. `Partner Topics`En üstte arama yapın ve hizmetler ' e tıklayın `Event Grid Partner Topics` .
1. Auth0 panonuzda oluşturduğunuz akışla eşleşen konuya tıklayın.
1. `Source`Auth0 hesabınızla eşleşen alanı doğrulayın.
1. Etkinleştir ' e tıklayın.

### <a name="subscribe-to-auth0-events"></a>Auth0 olaylarına abone olun

#### <a name="create-an-event-handler"></a>Olay işleyicisi oluşturma
Iş ortağı konu başlığını test etmek için bir olay işleyicisine ihtiyacınız vardır. Azure aboneliğinize gidin ve [Azure işlevi](custom-event-to-function.md)gibi bir [olay işleyicisi](event-handlers.md) olarak desteklenen bir hizmet çalıştırın.

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Auth0 Iş ortağınız konusuna abone olma
Auth0 Iş ortağınıza abone olmak, Auth0 olaylarınızın gitmesini istediğiniz Event Grid söylemenize olanak tanır.

1. Auth0 tümleştirmenizi için Iş ortağı konu dikey penceresinde, en üstteki + olay aboneliği ' ni seçin.
1. Olay aboneliği Oluştur sayfasında:
    1. Olay aboneliği için bir ad girin.
    1. Uç nokta türü için oluşturduğunuz Azure hizmetini veya Web kancasını seçin.
    1. Belirli bir hizmet için yönergeleri izleyin.
    1. Oluştur’a tıklayın.

## <a name="testing"></a>Test Etme
Azure ile Auth0 Iş ortağı konu tümleştirmesi, kullanıma hazırlanmalıdır.

### <a name="verify-the-integration"></a>Tümleştirmeyi doğrulama
Tümleştirmenin beklendiği gibi çalıştığını doğrulamak için:

1. Auth0 panosunda oturum açın.
1. Günlüklere > akışlarına gidin.
1. Event Grid akışa tıklayın.
1. Akışta bir kez durum sekmesine tıklayın. Akış etkin olmalıdır ve herhangi bir hata görmezseniz akış çalışır.

Olayların akışını görmek için [yayımlanacak bir olayı tetikleyen Auth0 eylemlerden herhangi birini](https://auth0.com/docs/logs/references/log-event-type-codes) çağırmayı deneyin.

## <a name="delivery-attempts-and-retries"></a>Teslim denemeleri ve yeniden denemeler
Auth0 olayları bir akış mekanizması aracılığıyla Azure 'a dağıtılır. Her olay, Auth0 ' de tetiklendiğinde gönderilir. Event Grid olayı alamadıysanız, Auth0 olayı teslim etmek için en fazla üç kez yeniden dener. Aksi halde, Auth0, sistem hatasını sisteminde teslim etmek için günlüğe kaydeder.

## <a name="next-steps"></a>Sonraki adımlar

- [Auth0 Iş ortağı konusu](auth0-overview.md)
- [İş ortağı konularına genel bakış](partner-events-overview.md)
- [Event Grid iş ortağı olun](partner-onboarding-overview.md)