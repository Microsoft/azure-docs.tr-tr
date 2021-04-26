---
title: Azure dijital TWINS gezgin kimlik doğrulama hatası
description: "' Kimlik doğrulaması başarısız oldu. ' için nedenler ve çözümler Azure Digital TWINS Explorer 'da."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495088"
---
# <a name="authentication-failed"></a>Kimlik doğrulama başarısız

Bu makalede, yerel makinenizde [Azure Digital TWINS gezgin](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) örneğini çalıştırırken bir ' kimlik doğrulama başarısız oldu ' hatası almaya yönelik nedenler ve çözüm adımları açıklanır. 

## <a name="symptoms"></a>Belirtiler

Azure Digital TWINS gezgin uygulamasını ayarlarken ve çalıştırırken, uygulama ile kimlik doğrulamaya çalışır ve aşağıdaki hata iletisiyle karşılanır:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital TWINS Explorer 'da şu metinle bir hata iletisinin ekran görüntüsü: kimlik doğrulaması başarısız oldu. Uygulamayı yerel olarak çalıştırıyorsanız, lütfen ana makinenizde Azure 'da oturum açtığınızdan veya bir komut isteminde ' az login' komutunu çalıştırarak, Visual Studio 'da oturum açarak veya VS Code veya ortam değişkenlerini ayarlayarak emin olun. Daha fazla bilgiye ihtiyacınız varsa lütfen Benioku dosyasına bakın veya Azure. Identity belgelerindeki DefaultAzureCredential arama yapın. Bulutta barındırılan ADT-Explorer çalıştırıyorsanız lütfen barındırma Azure işlevinizde sistem tarafından atanan bir yönetilen kimliğe sahip olduğundan emin olun. Daha fazla bilgi için Benioku dosyasına bakın.":::

## <a name="causes"></a>Nedenler

### <a name="cause-1"></a>Neden #1

Azure Digital TWINS gezgin uygulaması [](/dotnet/api/azure.identity.defaultazurecredential) `Azure.Identity` , yerel ortamınızda kimlik bilgilerini arayacak olan DefaultAzureCredential (kitaplığın parçası) kullanır.

Hata metni olduğu gibi, seçmek için yerel kimlik bilgileri sağlamadıysanız bu hata ortaya çıkabilir `DefaultAzureCredential` .

Azure Digital TWINS Explorer ile yerel kimlik bilgilerini kullanma hakkında daha fazla bilgi için Azure Digital TWINS hızlı başlangıcı ' nın [*Yerel Azure kimlik bilgilerini ayarlama*](quickstart-adt-explorer.md#set-up-local-azure-credentials) bölümüne bakın *: örnek bir senaryoyu inceleyin*.

### <a name="cause-2"></a>Neden #2

Bu hata, Azure hesabınızda Azure dijital TWINS örneğiniz için gerekli Azure rol tabanlı erişim denetimi (Azure RBAC) izinlerinin ayarlanmış olması halinde de oluşabilir. Örneğinizdeki verilere erişebilmek için, okumak veya yönetmek istediğiniz örnekte **Azure Digital TWINS veri okuyucusu** veya **Azure dijital TWINS veri sahibi** rolüne sahip olmanız gerekir. 

Azure dijital TWINS 'deki güvenlik ve roller hakkında daha fazla bilgi için bkz. [*Kavramlar: Azure dijital TWINS çözümleri Için güvenlik*](concepts-security.md).

## <a name="solutions"></a>Çözümler

### <a name="solution-1"></a>Çözüm #1

İlk olarak, uygulamaya gerekli kimlik bilgilerini sağladığınızdan emin olun.

#### <a name="provide-local-credentials"></a>Yerel kimlik bilgilerini belirtin

`DefaultAzureCredential` Yerel bir Azure oturum açma bilgileri kullanarak hizmetin kimliğini doğrular. Azure hesabınızda yerel bir [Azure CLI](/cli/azure/install-azure-cli) penceresinde veya Visual Studio veya Visual Studio Code oturum açarak Azure kimlik bilgilerinizi sağlayabilirsiniz.

Kabul edilen kimlik bilgileri türlerini ve `DefaultAzureCredential` denendikleri sırayı, [DefaultAzureCredential Için Azure kimlik belgeleri](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)' nde görüntüleyebilirsiniz.

Doğru Azure hesabında zaten yerel olarak oturum açtıysanız ve sorun çözümlenmiyorsa, sonraki çözüme geçin.

### <a name="solution-2"></a>Çözüm #2

Verilerini okumaya çalışıyorsanız Azure kullanıcısının Azure dijital TWINS örneğinde Azure Digital TWINS **veri okuyucusu** rolüne veya verileri yönetmeye çalışıyorsanız örnekteki **Azure Digital TWINS veri sahibi** rolüne sahip olduğunu doğrulayın.

Bu rolün öğesinden farklı olduğunu unutmayın...
* Önizleme sırasında bu rolün önceki adı, *Azure dijital TWINS sahibi (Önizleme)* (rol aynıdır, ancak ad değişmiştir)
* Tüm Azure aboneliğinde *sahip* rolü. Azure *dijital TWINS veri sahibi* , Azure dijital TWINS 'in içindeki bir roldür ve bu tek Azure dijital TWINS örneği kapsamına alınır.
* Azure dijital TWINS 'de *sahip* rolü. Bunlar, iki ayrı Azure dijital TWINS yönetim rolleridir ve *Azure dijital TWINS veri sahibi* , yönetim için kullanılması gereken roldür.

 Bu role sahip değilseniz, sorunu çözmek için bu rolü ayarlayın.

#### <a name="check-current-setup"></a>Geçerli kurulumu denetleyin

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Sorunları giderme 

Bu rol ataması yoksa, Azure **aboneliğinizde** sahip rolü olan birinin Azure **dijital TWINS örneğinde** uygun rolü vermesi için aşağıdaki komutu çalıştırması gerekir. 

Abonelikte bir sahibiyseniz, bu komutu kendiniz çalıştırabilirsiniz. Değilseniz, bu komutu sizin adınıza çalıştırmak için bir sahibine başvurun. Rol adı, düzenleme erişimi veya **Azure dijital TWINS veri okuyucusu** için okuma erişimi Için **Azure dijital TWINS veri sahibidir** .

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Bu rol gereksinimi ve atama süreci hakkında daha fazla bilgi için bkz. *nasıl yapılır: bir örnek ve kimlik doğrulaması ayarlama (CLI veya portal)*. [](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)

## <a name="next-steps"></a>Sonraki adımlar

Yeni bir Azure dijital TWINS örneği oluşturma ve kimlik doğrulama için kurulum adımlarını okuyun:
* [*Nasıl yapılır: örnek ve kimlik doğrulaması ayarlama (CLı)*](how-to-set-up-instance-cli.md)

Azure dijital TWINS üzerinde güvenlik ve izinler hakkında daha fazla bilgi edinin:
* [*Kavramlar: Azure dijital TWINS çözümleri için güvenlik*](concepts-security.md)
