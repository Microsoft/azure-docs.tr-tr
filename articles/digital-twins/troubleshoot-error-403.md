---
title: 'Azure dijital TWINS isteği şu durumla başarısız oldu: 403 (yasak)'
description: "' Hizmet isteği için nedenler ve çözümler başarısız oldu. Durum: 403 (yasak) ' Azure dijital TWINS üzerinde."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100545709"
---
# <a name="service-request-failed-status-403-forbidden"></a>Hizmet isteği başarısız oldu. Durum: 403 (yasak)

Bu makalede, hizmet isteklerinden Azure dijital TWINS 'e 403 hatası almaya yönelik nedenler ve çözüm adımları açıklanır. 

## <a name="symptoms"></a>Belirtiler

Bu hata, kimlik doğrulaması gerektiren birçok hizmet isteği türünde gerçekleşebilir. Etkisi, API isteğinin başarısız olması ve bir hata durumu döndürmektir `403 (Forbidden)` .

## <a name="causes"></a>Nedenler

### <a name="cause-1"></a>Neden #1

Çoğu zaman bu hata, hizmet için Azure rol tabanlı erişim denetimi (Azure RBAC) izinlerinizin doğru ayarlanmadığını gösterir. Azure dijital TWINS örneği için birçok eylem, **yönetmeye çalıştığınız örnekte** *Azure Digital TWINS veri sahibi* rolüne sahip olmanızı gerektirir. 

### <a name="cause-2"></a>Neden #2

Bir [uygulama kaydıyla](how-to-create-app-registration.md)kimlik doğrulayan Azure dijital TWINS ile iletişim kurmak için bir istemci uygulaması kullanıyorsanız, bu hata, uygulama Kaydlarınızın Azure dijital TWINS hizmeti için ayarlanmış izinlere sahip olmadığı için ortaya çıkabilir.

Uygulama kaydı, Azure dijital TWINS API 'Leri için yapılandırılmış erişim izinlerine sahip olmalıdır. Daha sonra, istemci uygulamanızın uygulama kaydıyla ilgili kimlik doğrulaması yapıldığında, uygulama kaydının yapılandırıldığı izinler verilir.

## <a name="solutions"></a>Çözümler

### <a name="solution-1"></a>Çözüm #1

İlk çözüm, Azure kullanıcısının yönetmeye çalıştığınız örnekte _**Azure Digital TWINS veri sahibi**_ rolüne sahip olduğunu doğrulamadır. Bu role sahip değilseniz, ayarlayın.

Bu rolün öğesinden farklı olduğunu unutmayın...
* Önizleme sırasında bu rolün önceki adı, *Azure dijital TWINS sahibi (Önizleme)* (rol aynıdır, ancak ad değişmiştir)
* Tüm Azure aboneliğinde *sahip* rolü. Azure *dijital TWINS veri sahibi* , Azure dijital TWINS 'in içindeki bir roldür ve bu tek Azure dijital TWINS örneği kapsamına alınır.
* Azure dijital TWINS 'de *sahip* rolü. Bunlar, iki ayrı Azure dijital TWINS yönetim rolleridir ve *Azure dijital TWINS veri sahibi* , yönetim için kullanılması gereken roldür.

#### <a name="check-current-setup"></a>Geçerli kurulumu denetleyin

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Sorunları giderme 

Bu rol ataması yoksa, Azure **aboneliğinizde** sahip rolü olan birinin Azure **dijital** TWINS örneğinde Azure dijital *TWINS veri sahibi* rolünü vermesi için aşağıdaki komutu çalıştırması gerekir. 

Abonelikte bir sahibiyseniz, bu komutu kendiniz çalıştırabilirsiniz. Değilseniz, bu komutu sizin adınıza çalıştırmak için bir sahibine başvurun.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Bu rol gereksinimi ve atama süreci hakkında daha fazla bilgi için bkz. *nasıl yapılır: bir örnek ve kimlik doğrulaması ayarlama (CLI veya portal)*. [](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)

Bu rol ataması zaten varsa *ve* bir istemci uygulamasının kimliğini doğrulamak Için BIR Azure AD uygulama kaydı kullanıyorsanız, bu çözüm 403 sorununu gidermezse sonraki çözüme devam edebilirsiniz.

### <a name="solution-2"></a>Çözüm #2

İstemci uygulamasının kimliğini doğrulamak için bir Azure AD uygulama kaydı kullanıyorsanız, olası ikinci çözüm, uygulama kaydının Azure Digital TWINS hizmeti için yapılandırılmış izinlere sahip olduğunu doğrulamadır. Bunlar yapılandırılmazsa, bunları ayarlayın.

#### <a name="check-current-setup"></a>Geçerli kurulumu denetleyin

İzinlerin doğru yapılandırılıp yapılandırılmadığını denetlemek için Azure portal [Azure AD uygulama kaydına genel bakış sayfasına](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) gidin. Bu sayfaya Portal arama çubuğunda *uygulama kayıtları* arayarak ulaşabilirsiniz.

Aboneliğinizde oluşturulmuş tüm uygulama kayıtlarını görmek için *tüm uygulamalar* sekmesine geçin.

Listede yeni oluşturduğunuz uygulama kaydını görmeniz gerekir. Ayrıntılarını açmak için seçin.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure portal Uygulama kayıtları sayfası":::

İlk olarak, Azure dijital TWINS izinleri ayarlarının kayıt üzerinde doğru şekilde ayarlandığını doğrulayın. Bunu yapmak için, menü çubuğundan *bildirim* ' ı seçerek uygulama kaydının bildirim kodunu görüntüleyin. Kod penceresinin en altına kaydırın ve altındaki bu alanları bulun `requiredResourceAccess` . Değerler aşağıdaki ekran görüntüsünde olanlarla eşleşmelidir:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure AD uygulama kaydı bildiriminin Portal görünümü":::

Ardından, bu uygulama kaydının Azure dijital TWINS için okuma/yazma izinleri içerdiğini doğrulamak için menü çubuğunda *API izinleri* ' ni seçin. Şöyle bir giriş görmeniz gerekir:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure dijital TWINS için ' okuma/yazma erişimi ' gösteren Azure AD uygulama kaydı için API izinlerinin Portal görünümü":::

#### <a name="fix-issues"></a>Sorunları giderme

Bunlardan herhangi biri açıklanenden farklı görünüyorsa, [*nasıl yapılır: uygulama kaydı oluşturma*](how-to-create-app-registration.md)bölümünde uygulama kaydı ayarlama yönergelerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yeni bir Azure dijital TWINS örneği oluşturma ve kimlik doğrulama için kurulum adımlarını okuyun:
* [*Nasıl yapılır: örnek ve kimlik doğrulaması ayarlama (CLı)*](how-to-set-up-instance-cli.md)

Azure dijital TWINS üzerinde güvenlik ve izinler hakkında daha fazla bilgi edinin:
* [*Kavramlar: Azure dijital TWINS çözümleri için güvenlik*](concepts-security.md)
