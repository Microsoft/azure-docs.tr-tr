---
title: Bilinen sorunlar-Azure dijital TWINS
description: Azure dijital TWINS ile ilgili bilinen sorunları algılamayı ve azaltıcı yardım alın.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 641b44a5e21e6646c07e6e1511e1c4ff01707f79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434109"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure dijital TWINS 'de bilinen sorunlar

Bu makalede, Azure dijital TWINS ile ilişkili bilinen sorunlar hakkında bilgi sağlanır.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 istemci hatası: Cloud Shell içinde bozuk Istek"

**Sorun açıklaması:** ' Da çalışan Cloud Shell komutları *https://shell.azure.com* zaman zaman "400 Istemci hatası: URL Için hatalı istek: http://localhost:50342/oauth2/token " ve ardından tam yığın izleme tarafından hata vererek başarısız olabilir.

| Bu, beni etkiler mi? | Nedeni | Çözüm |
| --- | --- | --- |
| &nbsp;Azure &nbsp; dijital &nbsp; TWINS 'de, bu, aşağıdaki komut gruplarını etkiler:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Bu Cloud Shell, bilinen bir sorunun sonucudur: [*Cloud Shell Token alma işlemi, 400 Istemci hatasıyla başarısız oluyor: Hatalı istek*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Bu, Azure Digital TWINS örnek kimlik doğrulama belirteçleri ve Cloud Shell varsayılan [yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) tabanlı kimlik doğrulaması ile ilgili bir sorun oluşturur. <br><br>Bu, veya komut gruplarından Azure dijital TWINS komutlarını `az dt` etkilemez `az dt endpoint` , çünkü bu, Cloud Shell yönetilen kimlik doğrulamasıyla ilgili bir sorun olmayan farklı bir kimlik doğrulama belirteci (Azure Resource Manager göre) kullanır. | Bunu çözmek için bir yol, `az login` Cloud Shell ' de komutu yeniden çalıştırmak ve sonraki oturum açma adımlarını tamamlıyor. Bu, oturumunuzu yönetilen kimlik kimlik doğrulamasından geçecek, bu da kök sorunu önleyen bir şekilde yapar. Bundan sonra, komutunu yeniden çalıştırmanız gerekir.<br><br>Alternatif olarak, Azure portal Cloud Shell bölmesini açabilir ve Cloud Shell işinizi buradan tamamlayabilirsiniz.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure portal simge çubuğundaki Cloud Shell simgesinin görüntüsü" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Son olarak, Azure CLı komutlarını yerel olarak çalıştırabilmeniz için başka bir çözüm, [Azure CLI](/cli/azure/install-azure-cli) 'yi makinenize yüklemektir. Yerel CLı bu sorunla karşılaşmaz. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Komut dosyası kurulumundan sonra eksik rol ataması

**Sorun açıklaması:** Bazı kullanıcılar [*nasıl yapılır: bir örnek ve kimlik doğrulaması (komut dosyası) ayarlama*](how-to-set-up-instance-scripted.md)rol atama bölümüyle ilgili sorunlar yaşayabilir. Betik hata göstermez, ancak *Azure Digital TWINS veri sahibi* rolü kullanıcıya başarıyla atanmaz ve bu sorun, diğer kaynakları daha fazla kaynak oluşturma yeteneğini etkiler.

| Bu, beni etkiler mi? | Nedeni | Çözüm |
| --- | --- | --- |
| Rol atamalarınızın betiği çalıştırdıktan sonra başarıyla ayarlandığını anlamak için, kurulum makalesinin [*Kullanıcı rolü atamasını doğrula*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) bölümüne ait yönergeleri izleyin. Kullanıcılarınız bu rolle gösterilmezse, bu sorun sizi etkiler. | Kişisel bir [Microsoft hesabı (MSA)](https://account.microsoft.com/account)ile oturum açan kullanıcılar için, kullanıcının oturum açma e-postalarından sizin gibi komutları TANıMLAYAN asıl kimliğiniz, kullanıcının rolü doğru şekilde atamak için bulmasını ve kullanmasını zorlaştırıyor. | Çözümlemek için, [CLI yönergelerini](how-to-set-up-instance-cli.md#set-up-user-access-permissions) veya [Azure Portal yönergelerini](how-to-set-up-instance-portal.md#set-up-user-access-permissions)kullanarak rol atamasını el ile ayarlayabilirsiniz. |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure 'da etkileşimli tarayıcı kimlik doğrulamasıyla ilgili sorun. Identity 1.2.0

**Sorun açıklaması:** Azure **[. Identity](/dotnet/api/azure.identity) kitaplığı**'Nın **1.2.0** sürümünü kullanarak Azure dijital TWINS uygulamalarınıza kimlik doğrulama kodu yazarken [ınteractivebrowsercredential](/dotnet/api/azure.identity.interactivebrowsercredential) yöntemiyle ilgili sorunlarla karşılaşabilirsiniz. Bu, bir tarayıcı penceresinde kimlik doğrulamaya çalışırken "Azure. Identity. AuthenticationFailedException" hata yanıtı olarak sunulur. Tarayıcı penceresi tamamen başlayamayabilir veya kullanıcının kimlik doğrulamasını başarıyla başarabilirken, istemci uygulaması yine de hata vererek başarısız olabilir.

| Bu, beni etkiler mi? | Nedeni | Çözüm |
| --- | --- | --- |
| &nbsp;Etkilenen &nbsp; Yöntem &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; aşağıdaki makalelerde kullanılır:<br><br>[*Öğretici: istemci uygulamasını kodlayın*](tutorial-code.md)<br><br>[*Nasıl yapılır: uygulama kimlik doğrulama kodunu yazma*](how-to-authenticate-client.md)<br><br>[*Nasıl yapılır: Azure dijital TWINS API 'Leri ve SDK 'Larını kullanma*](how-to-use-apis-sdks.md) | Bazı kullanıcılar, kitaplığın sürüm **1.2.0** Bu soruna sahipti `Azure.Identity` . | Çözümlemek için uygulamalarınızı [daha sonraki bir sürümünü](https://www.nuget.org/packages/Azure.Identity) kullanacak şekilde güncelleştirin `Azure.Identity` . Kitaplık sürümünü güncelleştirdikten sonra, tarayıcı yüklenmesi ve beklendiği gibi kimlik doğrulaması yapılmalıdır. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure 'da varsayılan Azure kimlik bilgisi kimlik doğrulamasıyla sorun. Identity 1.3.0

**Sorun açıklaması:** **[Azure. Identity](/dotnet/api/azure.identity) kitaplığı**'nın **1.3.0** sürümünü kullanarak kimlik doğrulama kodu yazarken, bazı kullanıcılar bu Azure dijital TWINS belgeleri genelinde birçok örnekte kullanılan [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) yöntemiyle ilgili sorunlarla karşılaşmıştır. Bu, kod kimlik doğrulamaya çalıştığında "Azure. Identity. AuthenticationFailedException: SharedTokenCacheCredential kimlik doğrulaması başarısız oldu" hata yanıtı olarak sunulur.

| Bu, beni etkiler mi? | Nedeni | Çözüm |
| --- | --- | --- |
| `DefaultAzureCredential` , bu hizmet için kimlik doğrulaması içeren belge örneklerinin çoğunda kullanılır. Kitaplığı sürüm 1.3.0 ile kullanarak kimlik doğrulama kodu yazıyorsanız `DefaultAzureCredential` `Azure.Identity` ve bu hata iletisini görüyorsanız, bu sizi etkiler. | Bunun nedeni, ile ilgili bazı yapılandırma sorununa neden olur `Azure.Identity` . | Bu `SharedTokenCacheCredential` , şu anda açık olan bu [DefaultAzureCredential sorunu](https://github.com/Azure/azure-sdk/issues/1970) bölümünde açıklandığı gibi, kimlik bilgilerinizin dışında tutulacak bir stratejidir `Azure.Identity` .<br>Başka bir seçenek de Uygulamanızı `Azure.Identity` , [Sürüm 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3)gibi önceki bir sürümünü kullanacak şekilde değiştirmek. Bu, Azure dijital TWINS üzerinde işlevsel bir etkiye sahip değildir ve bu nedenle de kabul edilen bir çözümdür. |

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS üzerinde güvenlik ve izinler hakkında daha fazla bilgi edinin:
* [*Kavramlar: Azure dijital TWINS çözümleri için güvenlik*](concepts-security.md)