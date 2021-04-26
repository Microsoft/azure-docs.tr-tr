---
title: Örnek ayarlama ve kimlik doğrulaması (CLI)
titleSuffix: Azure Digital Twins
description: CLı kullanarak Azure dijital TWINS hizmeti örneğini ayarlama bölümüne bakın
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2969ee5339306e30044a6a80260f912d45d0c8a2
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105073"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure dijital TWINS örneği ve kimlik doğrulaması (CLı) ayarlama

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Bu makalede, örneği oluşturma ve kimlik doğrulamasını ayarlama dahil olmak üzere **Yeni bir Azure dijital TWINS örneği ayarlama** adımları ele alınmaktadır. Bu makaleyi tamamladıktan sonra, programlamaya başlamak için kullanabileceğiniz bir Azure dijital TWINS örneğine sahip olursunuz.

Bu makalenin bu sürümü, tek tek, CLı kullanılarak bu adımları el ile bir arada ilerler.
* Azure portal kullanarak bu adımları el ile almak için, bu makalenin Portal sürümüne bakın: [*nasıl yapılır: bir örnek ve kimlik doğrulaması (portal) ayarlama*](how-to-set-up-instance-portal.md).
* Bir dağıtım betiği örneği kullanarak otomatikleştirilmiş bir kurulum aracılığıyla çalıştırmak için, bu makalenin betikleştirilmiş sürümüne bakın: [*nasıl yapılır: örnek ve kimlik doğrulaması ayarlama (komut dosyalı)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell oturum ayarlama
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure dijital TWINS örneğini oluşturma

Bu bölümde, Cloud Shell komutunu kullanarak **Azure Digital TWINS 'in yeni bir örneğini oluşturacaksınız** . Şunları sağlamanız gerekir:
* Örneğin dağıtılacağı kaynak grubu. Zaten var olan bir kaynak grubunuz yoksa şu komutla bir tane oluşturabilirsiniz:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Dağıtım için bir bölge. Azure dijital TWINS 'i destekleyen bölgeleri görmek için [*bölgeye göre kullanılabilen Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)ziyaret edin.
* Örneğiniz için bir ad. Aboneliğiniz zaten belirtilen adı kullanan bölgede başka bir Azure dijital TWINS örneğine sahipse, farklı bir ad seçmeniz istenir.

Örneği oluşturmak için aşağıdaki komutta bu değerleri kullanın:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Başarıyı doğrulama ve önemli değerleri toplama

Örnek başarılı bir şekilde oluşturulduysa Cloud Shell sonucu, oluşturduğunuz kaynakla ilgili bilgiler olarak aşağıdaki gibi görünür:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Kaynak grubu ve Azure dijital TWINS örneğinin başarıyla oluşturulmasıyla Komut penceresi":::

Azure dijital TWINS örneğinin **ana bilgisayar** adı, **adı** ve **kaynak grubu** 'ndan çıkış ' a bakın. Bunlar, kimlik doğrulama ve ilgili Azure kaynaklarını ayarlamak için Azure dijital TWINS örneğinizle çalışmaya devam ederken ihtiyacınız olabilecek tüm önemli değerlerdir. Diğer kullanıcılar örnekle programlama yapacaktır, bu değerleri bunlarla paylaşmanız gerekir.

> [!TIP]
> Bu özellikleri, örneğinizin tüm özellikleriyle birlikte, çalıştırarak dilediğiniz zaman görebilirsiniz `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Artık hazır bir Azure dijital TWINS örneğiniz var. Daha sonra, uygun Azure Kullanıcı izinlerini yönetmek için size izin verirsiniz.

## <a name="set-up-user-access-permissions"></a>Kullanıcı erişim izinlerini ayarlama

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Rolü atamak için aşağıdaki komutu kullanın (Azure aboneliğinde [yeterli izinlere](#prerequisites-permission-requirements) sahip bir kullanıcı tarafından çalıştırılması gerekir). Bu komut, rolün atanması gereken kullanıcı için Azure AD hesabındaki *Kullanıcı asıl adını* geçirmeniz gerekir. Çoğu durumda, bu, Azure AD hesabındaki kullanıcının e-postasına göre eşleşir.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Bu komutun sonucu oluşturulan rol ataması hakkında bilgi verilir.

> [!NOTE]
> Bu komut, CLı 'nin **grafik veritabanında kullanıcı veya hizmet sorumlusu bulamadığını** söyleyen bir hata döndürürse:
>
> Bunun yerine kullanıcının *nesne kimliğini* kullanarak rolü atayın. Bu, kişisel [Microsoft hesaplarındaki (MSAs)](https://account.microsoft.com/account)kullanıcılar için gerçekleşebilir. 
>
> Kullanıcı hesabını seçmek ve ayrıntılarını açmak için [Azure Active Directory kullanıcıların Azure Portal sayfasını](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) kullanın. Kullanıcının *ObjectID*'yi kopyalayın:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="' Nesne KIMLIĞI ' alanındaki GUID 'YI vurgulamak Azure portal Kullanıcı sayfasının görünümü" lightbox="media/includes/user-id.png":::
>
> Ardından, yukarıdaki parametresi için kullanıcının *nesne kimliğini* kullanarak rol atama listesi komutunu tekrarlayın `assignee` .

### <a name="verify-success"></a>Başarıyı doğrula

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Artık bir Azure dijital TWINS örneğiniz var ve bunu yönetmek için izinler atandı.

## <a name="next-steps"></a>Sonraki adımlar

Azure Digital TWINS CLı komutlarını kullanarak örneğiniz için tek REST API çağrılarını test edin: 
* [az DT Reference](/cli/azure/dt)
* [*Nasıl yapılır: Azure dijital TWINS CLı 'sını kullanma*](how-to-use-cli.md)

Ya da bkz. kimlik doğrulama kodu ile bir istemci uygulamasını örneğinize bağlama:
* [*Nasıl yapılır: uygulama kimlik doğrulama kodunu yazma*](how-to-authenticate-client.md)