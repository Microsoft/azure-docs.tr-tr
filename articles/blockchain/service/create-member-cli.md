---
title: Azure blok zinciri hizmeti üyesi oluşturma-Azure CLı
description: Azure CLı kullanarak bir blok zinciri Konsorsiyumu için Azure blok zinciri hizmeti üyesi oluşturun.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: da099aa75168e52d0024f4a4381c77b3e9ce67ab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876661"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure blok zinciri hizmeti blok zinciri üyesi oluşturma

Bu hızlı başlangıçta Azure CLı kullanarak Azure blok zinciri hizmeti 'ne yeni bir blok zinciri üyesi ve konsorsiyu dağıtırsınız.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Yok.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.51 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'yı yüklemek](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

1. Oturum açın.

    CLI’nın yerel yüklemesini kullanıyorsanız [az login](/cli/azure/reference-index#az_login) komutunu kullanarak oturum açın.

    ```azurecli
    az login
    ```

    Terminalinizde görüntülenen adımları uygulayarak kimlik doğrulama işlemini tamamlayın.

1. Azure CLI uzantısını yükleyin.

    Azure CLI için uzantı başvurularıyla çalışırken önce uzantıyı yüklemeniz gerekir.  Azure CLI uzantıları, henüz temel CLI’nın parçası olarak gönderilmeyen deneysel ve ön sürüm komutlarına erişmenize olanak sağlar.  Güncelleştirme ve kaldırma da dahil olmak üzere uzantılar hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile uzantıları kullanma](/cli/azure/azure-cli-extensions-overview).

    Aşağıdaki komutu çalıştırarak [Azure blok zinciri hizmeti uzantısını](/cli/azure/blockchain) yükler:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Bir kaynak grubu oluşturun.

    Tüm Azure kaynakları gibi Azure blok zinciri hizmeti 'nin bir kaynak grubuna dağıtılması gerekir. Kaynak grupları, ilgili Azure kaynaklarını düzenlemenizi ve yönetmenizi sağlar.

    Bu hızlı başlangıç için, _eastus_ konumunda aşağıdaki [az Group Create](/cli/azure/group#az_group_create) komutuyla _myresourcegroup_ adlı bir kaynak grubu oluşturun:

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Blok zinciri üyesi oluşturma

Bir Azure blok zinciri hizmeti üyesi, özel bir konsorsiyum blok zinciri ağında bir blok zinciri düğümüdür. Bir üyeyi sağlarken bir konsorsiyum ağı oluşturabilir veya katabilirsiniz. Bir konsorsiyum ağı için en az bir üyeye ihtiyacınız vardır. Katılımcılar tarafından gereken blok zinciri üyelerinin sayısı senaryonuza bağlıdır. Konsorsiyum katılımcıları bir veya daha fazla blok zinciri üyesine sahip olabilir veya diğer katılımcılarla üyeleri paylaşabilir. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).

Geçirmeniz gereken birkaç parametre ve özellik vardır. Örnek parametreleri değerlerinizle değiştirin.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. Önceki bölümde oluşturduğunuz kaynak grubunu kullanın.
| **ada** | Azure blok zinciri hizmeti blok zinciri üyesini tanımlayan benzersiz bir ad. Ad, genel uç nokta adresi için kullanılır. Örneğin, `myblockchainmember.blockchain.azure.com`.
| **konumuna** | Blok zinciri üyesinin oluşturulduğu Azure bölgesi. Örneğin, `westus2`. Kullanıcılarınıza veya diğer Azure uygulamalarınıza en yakın konumu seçin. Özellikler, bazı bölgelerde kullanılamayabilir. Azure blok zinciri Veri Yöneticisi şu Azure bölgelerinde kullanılabilir: Doğu ABD ve Batı Avrupa.
| **parola** | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın.
| **Protocol** | Blok zinciri protokolü. Şu anda *çekirdek* protokol destekleniyor.
| **unun** | Katılacak veya oluşturulacak konsorsiyumun adı. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).
| **Konsorsiyum yönetimi-hesap-parola** | Konsorsiyum hesabı parolası üye hesap parolası olarak da bilinir. Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız.
| **isteyin** | Katman türü. *Standart* veya *temel*. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Ayrıca, blok Veri Yöneticisi Zinciri kullanıyorsanız veya yüksek hacimli özel işlemler gönderiyorsanız *Standart* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.

Blok zinciri üyesini ve destekleyici kaynakları oluşturmak yaklaşık 10 dakika sürer.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç veya öğretici için oluşturduğunuz blok zinciri üyesini kullanabilirsiniz. Artık gerekli değilse, `myResourceGroup` hızlı başlangıç için oluşturduğunuz kaynak grubunu silerek kaynakları silebilirsiniz.

Kaynak grubunu ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti üyesi ve yeni bir konsorsiyu dağıttınız. Azure blok zinciri hizmeti üyesine eklemek için Ethereum için Azure blok zinciri geliştirme seti 'ni kullanmak üzere bir sonraki hızlı başlangıcı deneyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetine bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
