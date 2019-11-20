---
title: Azure portal kullanarak bir Azure blok zinciri hizmeti üyesi oluşturma
description: Bir blok zinciri üyesi oluşturmak için Azure blok zinciri hizmetini kullanın.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: c87f9a044d6655b2062f2c2657d3b465699e0dd2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329252"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi oluşturma

Azure blok zinciri hizmeti, iş mantığınızı akıllı bir sözleşme içinde yürütebilmeniz için bir blok zinciri platformudur. Bu hızlı başlangıçta, Azure portal kullanarak bir blok zinciri üyesi oluşturarak nasıl başlacağınız gösterilmektedir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Blok zinciri üyesi oluşturma

Yeni veya mevcut bir konsorsiyda çekirdek defter protokolünü çalıştıran bir blok zinciri üyesi oluşturun.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.
1. **Blok zinciri** > **Azure blok zinciri hizmeti**' ni seçin.
1. Şablonu doldurun.

    ![Hizmet oluştur](./media/create-member/create-member.png)

    Ayar | Açıklama
    --------|------------
    Blok zinciri üyesi | Azure blok zinciri hizmeti blok zinciri üyesini tanımlayan benzersiz bir ad seçin. Blok zinciri üye adı yalnızca küçük harf ve sayı içerebilir. İlk karakter bir harf olmalıdır. Değer 2 ila 20 karakter uzunluğunda olmalıdır.
    Abonelik | Hizmetiniz için kullanmak istediğiniz Azure aboneliğini seçin. Birden fazla aboneliğiniz varsa kaynağın faturalandığı aboneliği seçin.
    Kaynak grubu | Yeni bir kaynak grubu adı veya aboneliğinizde var olan bir kaynak grubu.
    Bölge | Bu, konsorsiyumun tüm üyeleri için aynı olmalıdır.
    Üye hesabı parolası | Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız.
    Konsorsiyum adı | Yeni bir konsorsiyum için benzersiz bir ad girin. Bir toplantıya bir davet aracılığıyla katılırken, bu değer katıldığınız konsorsiyumun değeridir.
    Açıklama | Konsorsiyumun açıklaması.
    Protokol |  Önizleme, çekirdek protokolünü destekler.
    Fiyatlandırma | Yeni hizmetinizin düğüm yapılandırması. **Standart**' ı seçin. 2 Doğrulayıcı düğümleri ve 1 işlem düğümü varsayılan ayarlardır.
    İşlem düğümü parolası | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın.

1. Hizmeti sağlamak için **Oluştur** ' u seçin. Sağlama yaklaşık 10 dakika sürer.
1. Dağıtım işlemini izlemek için araç çubuğunda **Bildirimler** ' i seçin.
1. Dağıtımdan sonra, blok zinciri üyesine gidin.

**Genel bakış**' ı seçtiğinizde, hizmetinize ilişkin temel bilgileri rootcontract adresi ve üye hesabı da dahil olmak üzere görüntüleyebilirsiniz.

![Blok zinciri üyesine genel bakış](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç veya öğretici için oluşturduğunuz üyeyi kullanabilirsiniz. Artık gerekli değilse, Azure blok zinciri hizmeti tarafından oluşturduğunuz `myResourceGroup` kaynak grubunu silerek kaynakları silebilirsiniz.

Kaynak grubunu silmek için:

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin.
2. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [MetaMask kullanarak akıllı bir sözleşmeyi bağlama ve dağıtma](connect-metamask.md)