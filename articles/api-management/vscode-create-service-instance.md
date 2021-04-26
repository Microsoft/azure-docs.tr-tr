---
title: Visual Studio Code kullanarak bir Azure API Management örneği oluşturma | Microsoft Docs
description: Azure API Management örneği oluşturmak için Visual Studio Code.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 3105b6f34d7ece81e8145fdd9e89568e66360ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649521"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak yeni bir Azure API Management hizmet örneği oluşturma

Azure API Management (APIM), kuruluşların kendi veri ve hizmet potansiyellerini ortaya çıkarmak üzere API’leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. APIM, herhangi bir yerde barındırılan mevcut arka uç hizmetleri için modern API ağ geçitleri oluşturmanıza ve yönetmenize olanak sağlar. Daha fazla bilgi için [Genel Bakış](api-management-key-concepts.md) konusuna bakın.

Bu hızlı başlangıçta, Visual Studio Code için *Azure API Management uzantısı* kullanılarak yeni bir API Management örneği oluşturma adımları açıklanmaktadır. Uzantıyı, API Management Örneğinizde ortak yönetim işlemleri gerçekleştirmek için de kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ayrıca, aşağıdakileri yüklediğinizden emin olun:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Visual Studio Code için Azure API Management uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Visual Studio Code başlatın ve Azure uzantısını açın. (Etkinlik çubuğunda Azure simgesini görmüyorsanız *azure API Management* uzantısının etkinleştirildiğinden emin olun.)

Bir tarayıcı penceresi başlatmak ve Microsoft hesabı oturum açmak için **Azure 'Da oturum aç...** seçeneğini belirleyin.

![VS Code için API Management uzantısından Azure 'da oturum açın](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>API Management hizmeti oluşturma

Microsoft hesabı oturum açtıktan sonra *Azure: API Management* Explorer bölmesi Azure aboneliklerinizi listeler.

Kullanmak istediğiniz aboneliğe sağ tıklayın ve **Azure 'da API Management oluştur**' u seçin.

![VS Code API Management Sihirbazı oluşturma](./media/vscode-create-service-instance/vscode-apim-create.png)

Açılan bölmede, yeni API Management örneği için bir ad sağlayın. Azure 'da genel olarak benzersiz olmalıdır ve 1-50 alfasayısal karakter ve/veya kısa çizgi içermelidir ve bir harfle başlayıp alfasayısal ile bitmelidir.

Belirtilen adla yeni bir API Management örneği (ve üst kaynak grubu) oluşturulacak. Varsayılan olarak, örnek, *Tüketim* SKU 'su ile *Batı ABD* bölgesinde oluşturulur.

> [!TIP]
> *Azure API Management uzantısı ayarlarında* **Gelişmiş oluşturmayı** etkinleştirirseniz, API Management örneğinizi dağıtmak için bir [API Management SKU 'su](https://azure.microsoft.com/pricing/details/api-management/), [Azure bölgesi](https://status.azure.com/en-us/status)ve bir [kaynak grubu](../azure-resource-manager/management/overview.md) da belirtebilirsiniz.
>
> *Tüketim* SKU 'sunun sağlanması bir dakikadan kısa sürer, ancak diğer SKU 'ların oluşturulması genellikle 30-40 dakika sürer.

Bu noktada, ilk API 'nizi içeri aktarıp yayımlamaya hazırsınız. Bunu yapabilir ve ayrıca Visual Studio Code uzantısı içinde ortak API Management işlemleri gerçekleştirebilirsiniz. Daha fazla bilgi için [öğreticiye](visual-studio-code-tutorial.md) bakın.

![VS Code API Management uzantı bölmesinde yeni oluşturulan API Management örneği](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [API Management hizmetini](get-started-create-service-instance.md#clean-up-resources) ve kaynak grubunu silmek için sağ tıklayıp **portalda aç** ' ı seçerek API Management örneğini kaldırın.

Alternatif olarak, **API Management Sil** ' i seçerek yalnızca API Management örneğini silebilirsiniz (Bu işlem, kaynak grubunu silmez).

![VS Code API Management örneğini Sil](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [API Management uzantısını kullanarak API 'Leri içeri aktarma ve yönetme](visual-studio-code-tutorial.md)
