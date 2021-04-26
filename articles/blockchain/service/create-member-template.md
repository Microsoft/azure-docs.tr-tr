---
title: Azure Resource Manager şablonu kullanarak Azure blok zinciri hizmeti üyesi oluşturma
description: Azure Resource Manager şablonu kullanarak Azure blok zinciri hizmeti üyesini oluşturmayı öğrenin.
services: azure-resource-manager
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: c49e45ae84d58d62460d493887e2a4e78e40ba32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536305"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure blok zinciri hizmeti üyesi oluşturma

Bu hızlı başlangıçta, yeni bir blok zinciri üyesi ve konsorsiyunuzu bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure blok zinciri hizmetine dağıtırsınız. Bir Azure blok zinciri hizmeti üyesi, özel bir konsorsiyum blok zinciri ağında bir blok zinciri düğümüdür. Bir üyeyi sağlarken bir konsorsiyum ağı oluşturabilir veya katabilirsiniz. Bir konsorsiyum ağı için en az bir üyeye ihtiyacınız vardır. Katılımcılar tarafından gereken blok zinciri üyelerinin sayısı senaryonuza bağlıdır. Konsorsiyum katılımcıları bir veya daha fazla blok zinciri üyesine sahip olabilir veya diğer katılımcılarla üyeleri paylaşabilir. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/) alınmıştır.

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Şablonda tanımlanan Azure kaynakları:

* [**Microsoft. Blockzincirine/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki bağlantıyı seçerek Azure’da oturum açıp bir şablon açın.

    [![Azure’a dağıtın](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Azure blok zinciri hizmeti üyesine yönelik ayarları belirtin.

    Ayar | Açıklama
    --------|------------
    Abonelik | Hizmetiniz için kullanmak istediğiniz Azure aboneliğini seçin. Birden fazla aboneliğiniz varsa kaynağın faturalandığı aboneliği seçin.
    Kaynak grubu | Yeni bir kaynak grubu adı oluşturun veya aboneliğinizden mevcut bir tane seçin.
    Region | Kaynak grubu oluşturmak için bir bölge seçin. Konsorsiyumun tüm üyeleri aynı konumda olmalıdır. Dağıtım için kullanılabilir konumlar şunlardır *westeurope, eastus, Güneydoğu, westeurope, northeurope, westus2* ve *japaneast*. Özellikler, bazı bölgelerde kullanılamayabilir. Azure blok zinciri Veri Yöneticisi şu Azure bölgelerinde kullanılabilir: Doğu ABD ve Batı Avrupa.
    BC üye adı | Azure blok zinciri hizmeti üyesi için benzersiz bir ad seçin. Blok zinciri üye adı yalnızca küçük harf ve sayı içerebilir. İlk karakter harf olmalıdır. Değer 2 ila 20 karakter uzunluğunda olmalıdır.
    Konsorsiyum adı | Benzersiz bir ad girin. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).
    Üye parolası | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın.
    Konsorsiyum yönetimi hesabı parolası | Konsorsiyum hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Bu, konsorsiyum yönetimi için kullanılır.
    SKU katmanı | Yeni hizmetinizin fiyatlandırma katmanı. **Standart** ve **temel** katmanlar arasında seçim yapın. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Ayrıca, blok Veri Yöneticisi Zinciri kullanıyorsanız veya yüksek hacimli özel işlemler gönderiyorsanız *Standart* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.
    SKU adı | Yeni hizmetinizin düğüm yapılandırması ve maliyeti. Standart için, temel ve **S0** için **B0** kullanın.
    Konum | Üyenin oluşturulacağı bir konum seçin. Varsayılan olarak, kaynak grubu konumu kullanılır `[resourceGroup().location]` . Konsorsiyumun tüm üyeleri aynı konumda olmalıdır. Dağıtım için kullanılabilir konumlar şunlardır *westeurope, eastus, Güneydoğu, westeurope, northeurope, westus2* ve *japaneast*. Özellikler, bazı bölgelerde kullanılamayabilir. Azure blok zinciri Veri Yöneticisi şu Azure bölgelerinde kullanılabilir: Doğu ABD ve Batı Avrupa.

1. Şablonu doğrulamak ve dağıtmak için **gözden geçir + oluştur** ' u seçin.

  Azure portal, şablonu dağıtmak için burada kullanılır. Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan Azure blok zinciri hizmeti üyesinin ayrıntılarını görüntülemek için Azure portal kullanabilirsiniz. Portalda, Azure blok zinciri hizmeti üyesini içeren kaynak grubuna gidin. Oluşturduğunuz blok zinciri üyesini seçin.

![Azure portal dağıtılan Azure blok zinciri üyesine genel bakış ayrıntıları](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç veya öğretici için oluşturduğunuz blok zinciri üyesini kullanabilirsiniz. Artık gerekli değilse, hızlı başlangıç için oluşturduğunuz kaynak grubunu silerek kaynakları silebilirsiniz.

Kaynak grubunu silmek için:

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin.
2. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti üyesi ve yeni bir konsorsiyu dağıttınız. Azure blok zinciri hizmeti üyesine eklemek için Ethereum için Azure blok zinciri geliştirme seti 'ni kullanmak üzere bir sonraki hızlı başlangıcı deneyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetine bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
