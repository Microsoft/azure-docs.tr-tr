---
title: Hızlı başlangıç-Azure Iletişim hizmetlerinde kaynak oluşturma ve yönetme
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, ilk Azure Iletişim Hizmetleri kaynağınızı oluşturmayı ve yönetmeyi öğreneceksiniz.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.openlocfilehash: aabb8bdf4105702aa623c45bc291770b05b8279e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726780"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Hızlı başlangıç: Iletişim Hizmetleri kaynaklarını oluşturma ve yönetme

İlk Iletişim Hizmetleri kaynağınızı sağlayarak Azure Iletişim Hizmetleri ile çalışmaya başlayın. İletişim Hizmetleri kaynakları, [Azure Portal](https://portal.azure.com) veya .NET Yönetim SDK 'sı ile sağlanabilir. Yönetim SDK 'Sı ve Azure portal, Azure 'un dağıtım ve yönetim hizmeti [Azure Resource Manager](../../azure-resource-manager/management/overview.md), kaynaklarınızı ve arabirimlerinizi oluşturmanıza, yapılandırmanıza, güncelleştirmenize ve silmesine izin verir. SDK 'larda kullanılabilen tüm işlevler Azure portal kullanılabilir. 


> [!WARNING]
> Iletişim Hizmetleri birden çok coğrafi konumda kullanıma sunulurken, bir telefon numarası alabilmek için kaynağın bir veri konumunun ' US ' olarak ayarlanmış olması gerekir. Ayrıca, genel önizleme sırasında iletişim kaynaklarının farklı bir aboneliğe aktarılamayacağını unutmayın.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>Bağlantı Dizeleriniz ve hizmet uç noktalarınıza erişin

Bağlantı dizeleri Iletişim Hizmetleri SDK 'larının Azure 'a bağlanmasına ve kimlik doğrulamasından geçmesini sağlar. Iletişim Hizmetleri bağlantı Dizeleriniz ve hizmet uç noktalarınıza Azure portal veya program aracılığıyla Azure Resource Manager API 'lerle erişebilirsiniz.

Iletişim Hizmetleri kaynağınız üzerinde gezindikten sonra, gezinti menüsünden **anahtarlar** ' ı seçin ve Iletişim hizmeti SDK 'ları tarafından kullanım için **bağlantı dizesini** veya **uç nokta** değerlerini kopyalayın. Birincil ve ikincil anahtarlara erişiminizin olduğunu unutmayın. Bu, Iletişim Hizmetleri kaynaklarınıza üçüncü tarafa veya hazırlama ortamına geçici erişim sağlamak istediğiniz senaryolarda yararlı olabilir.

:::image type="content" source="./media/key.png" alt-text="Iletişim Hizmetleri anahtar sayfasının ekran görüntüsü.":::

Ayrıca, kaynak grubunuz veya belirli bir kaynak için anahtarlar gibi Azure CLı kullanarak da önemli bilgilere erişebilirsiniz. 

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 'yı yükleyip oturum açmak için aşağıdaki komutu kullanın. Azure hesabınızla bağlantı kurmak için kimlik bilgilerinizi sağlamanız gerekir.
```azurecli
az login
```

Artık kaynaklarınızla ilgili önemli bilgilere erişebilirsiniz.
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

Belirli bir abonelik seçmek isterseniz, bayrağı da belirtebilir ```--subscription``` ve ABONELIK kimliğini sağlayabilirsiniz.
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>Bağlantı dizenizi depolayın

İletişim Hizmetleri SDK 'Ları Iletişim hizmetlerine yapılan istekleri yetkilendirmek için bağlantı dizelerini kullanır. Bağlantı dizenizi depolamak için çeşitli seçenekleriniz vardır:

* Masaüstünde veya cihazda çalışan bir uygulama, bağlantı dizesini bir **app.config** veya **web.config** dosyasında saklayabilir. Bağlantı dizesini bu dosyalardaki **appSettings** bölümüne ekleyin.
* Azure App Service çalışan bir uygulama, bağlantı dizesini [App Service uygulama ayarlarında](../../app-service/configure-common.md)saklayabilir. Bağlantı dizesini Portal içindeki uygulama ayarları sekmesinin **bağlantı dizeleri** bölümüne ekleyin.
* Bağlantı dizenizi [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md)' de saklayabilirsiniz.
* Uygulamanızı yerel olarak çalıştırıyorsanız, Bağlantı dizenizi bir ortam değişkeninde depolamak isteyebilirsiniz.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Bağlantı dizenizi bir ortam değişkeninde depolayın

Bir ortam değişkenini yapılandırmak için, bir konsol penceresi açın ve aşağıdaki sekmelerden işletim sisteminizi seçin. `<yourconnectionstring>`Gerçek bağlantı dizeniz ile değiştirin.

#### <a name="windows"></a>[Windows](#tab/windows)

Bir konsol penceresi açın ve aşağıdaki komutu girin:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Ortam değişkenini ekledikten sonra, konsol penceresi de dahil olmak üzere ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatmanız gerekebilir. Örneğin, düzenleyiciniz olarak Visual Studio kullanıyorsanız, örneği çalıştırmadan önce Visual Studio 'Yu yeniden başlatın.

#### <a name="macos"></a>[macOS](#tab/unix)

**. Zshrc**'nizi düzenleyin ve ortam değişkenini ekleyin:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source ~/.zshrc` çalıştırın. IDE 'niz açıkken ortam değişkenini oluşturduysanız, değişkene erişmek için düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekebilir.

#### <a name="linux"></a>[Linux](#tab/linux)

**.Bash_profile** düzenleyin ve ortam değişkenini ekleyin:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source ~/.bash_profile` çalıştırın. IDE 'niz açıkken ortam değişkenini oluşturduysanız, değişkene erişmek için düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekebilir.

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

Kaynak silme işleminin ardından kaynağınız için atanan telefon numaranız varsa, telefon numaraları otomatik olarak kaynağından aynı anda serbest bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrenirsiniz:

> [!div class="checklist"]
> * İletişim Hizmetleri kaynağı oluşturma
> * Kaynak Coğrafya ve etiketlerini yapılandırma
> * Bu kaynağın anahtarlarına erişin
> * Kaynağı Sil

> [!div class="nextstepaction"]
> [İlk Kullanıcı erişim belirteçlerinizi oluşturma](access-tokens.md)
