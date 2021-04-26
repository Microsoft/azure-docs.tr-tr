---
title: Azure blok zinciri hizmeti üyesi oluşturma-Azure portal
description: Azure portal kullanarak bir blok zinciri Konsorsiyumu için Azure blok zinciri hizmeti üyesi oluşturun.
ms.reviewer: ravastra
ms.date: 07/16/2020
ms.topic: quickstart
ms.custom:
- references_regions
- mode-portal
ms.openlocfilehash: 9fe8b7856a99930c16b4173e432fa0b30642582c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536200"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalı kullanarak Azure Blok Zinciri Hizmeti blok zinciri üyesi oluşturma

Bu hızlı başlangıçta, Azure blok zinciri hizmeti 'nde Azure portal kullanarak yeni bir blok zinciri üyesi ve konsorsiyu dağıtırsınız.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Yok.

## <a name="create-a-blockchain-member"></a>Blok zinciri üyesi oluşturma

Bir Azure blok zinciri hizmeti üyesi, özel bir konsorsiyum blok zinciri ağında bir blok zinciri düğümüdür. Bir üyeyi sağlarken bir konsorsiyum ağı oluşturabilir veya katabilirsiniz. Bir konsorsiyum ağı için en az bir üyeye ihtiyacınız vardır. Katılımcılar tarafından gereken blok zinciri üyelerinin sayısı senaryonuza bağlıdır. Konsorsiyum katılımcıları bir veya daha fazla blok zinciri üyesine sahip olabilir veya diğer katılımcılarla üyeleri paylaşabilir. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.
1. **Blok zinciri**  >  **Azure blok zinciri hizmeti (Önizleme)** öğesini seçin.

    ![Hizmet oluştur](./media/create-member/create-member.png)

    Ayar | Açıklama
    --------|------------
    Abonelik | Hizmetiniz için kullanmak istediğiniz Azure aboneliğini seçin. Birden fazla aboneliğiniz varsa kaynağın faturalandığı aboneliği seçin.
    Kaynak grubu | Yeni bir kaynak grubu adı oluşturun veya aboneliğinizden mevcut bir tane seçin.
    Region | Üyeyi oluşturmak için bir bölge seçin. Konsorsiyumun tüm üyeleri aynı konumda olmalıdır. Özellikler, bazı bölgelerde kullanılamayabilir. Azure blok zinciri Veri Yöneticisi şu Azure bölgelerinde kullanılabilir: Doğu ABD ve Batı Avrupa.
    Protokol | Şu anda Azure blok zinciri hizmeti önizlemesi çekirdek protokolünü destekliyor.
    Konsorsiyum | Yeni bir konsorsiyum için benzersiz bir ad girin. Bir toplantıya bir davet aracılığıyla katılırken katıldığınız Konsorsiyumu seçin. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).
    Name | Azure blok zinciri hizmeti üyesi için benzersiz bir ad seçin. Blok zinciri üye adı yalnızca küçük harf ve sayı içerebilir. İlk karakter harf olmalıdır. Değer 2 ila 20 karakter uzunluğunda olmalıdır.
    Üye hesabı parolası | Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız.
    Fiyatlandırma | Yeni hizmetinizin düğüm yapılandırması ve maliyeti. **Standart** ve **temel** katmanlar arasında seçim yapmak için **değişiklik** bağlantısını seçin. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Ayrıca, blok Veri Yöneticisi Zinciri kullanıyorsanız veya yüksek hacimli özel işlemler gönderiyorsanız *Standart* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.
    Düğüm parolası | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın.

1. Ayarlarınızı doğrulamak için **gözden geçir + oluştur** ' u seçin. Hizmeti sağlamak için **Oluştur** ' u seçin. Sağlama yaklaşık 10 dakika sürer.
1. Dağıtım işlemini izlemek için araç çubuğunda **Bildirimler** ' i seçin.
1. Dağıtımdan sonra, blok zinciri üyesine gidin.

**Genel bakış**' ı seçtiğinizde, hizmetinize ilişkin temel bilgileri rootcontract adresi ve üye hesabı da dahil olmak üzere görüntüleyebilirsiniz.

![Blok zinciri üyesine genel bakış](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç veya öğretici için oluşturduğunuz üyeyi kullanabilirsiniz. Artık gerekli değilse, `myResourceGroup` hızlı başlangıç için oluşturduğunuz kaynak grubunu silerek kaynakları silebilirsiniz.

Kaynak grubunu silmek için:

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin.
2. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti üyesi ve yeni bir konsorsiyu dağıttınız. Azure blok zinciri hizmeti üyesine eklemek için Ethereum için Azure blok zinciri geliştirme seti 'ni kullanmak üzere bir sonraki hızlı başlangıcı deneyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetine bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
