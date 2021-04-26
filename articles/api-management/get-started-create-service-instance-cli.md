---
title: Hızlı başlangıç-CLı kullanarak Azure API Management örneği oluşturma
description: Azure CLı kullanarak yeni bir Azure API Management hizmet örneği oluşturun.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775879"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak yeni bir Azure API Management hizmet örneği oluşturma

Azure API Management (APIM), kuruluşların kendi veri ve hizmet potansiyellerini ortaya çıkarmak üzere API’leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. APıM, her yerde barındırılan mevcut arka uç hizmetleri için modern API ağ geçitleri oluşturmanıza ve yönetmenize olanak sağlar. Daha fazla bilgi için bkz. [Genel Bakış](api-management-key-concepts.md).

Bu hızlı başlangıçta, Azure CLI 'de [az APIM](/cli/azure/apim) komutlarını kullanarak yeni bir API Management örneği oluşturma adımları açıklanmaktadır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.11.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Tüm Azure kaynakları gibi Azure API Management örnekleri bir kaynak grubuna dağıtılmalıdır. Kaynak grupları, ilgili Azure kaynaklarını düzenlemenizi ve yönetmenizi sağlar.

İlk olarak, aşağıdaki [az Group Create](/cli/azure/group#az_group_create) komutuyla Orta ABD konumunda *myresourcegroup* adlı bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Yeni hizmet oluşturma

Artık bir kaynak grubunuz olduğuna göre, bir API Management hizmet örneği oluşturabilirsiniz. [Az APIM Create](/cli/azure/apim#az_apim_create) komutunu kullanarak bir tane oluşturun ve bir hizmet adı ve Yayımcı Ayrıntıları sağlayın. Hizmet adı Azure içinde benzersiz olmalıdır. 

Aşağıdaki örnekte, hizmet adı için *uygulamaım* kullanılır. Adı benzersiz bir değer olarak güncelleştirin. Ayrıca API yayımcısının kuruluşunun adını ve bildirimleri alacak e-posta adresini güncelleştirin. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Varsayılan olarak, komut, Azure API Management değerlendirmek için ekonomik bir seçenek olan geliştirici katmanında örneği oluşturur. Bu katman üretim kullanımı için değildir. API Management katmanlarını ölçeklendirme hakkında daha fazla bilgi için bkz. [yükseltme ve ölçeklendirme](upgrade-and-scale.md). 

> [!TIP]
> Bu katmanda bir API Management hizmeti oluşturmak ve etkinleştirmek için 30 ila 40 dakika sürebilir. Önceki komut, `--no-wait` hizmet oluşturulduğunda komutun hemen döndürdüğü şekilde seçeneğini kullanır.

[Az APIM Show](/cli/azure/apim#az_apim_show) komutunu çalıştırarak dağıtım durumunu denetleyin:

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

İlk olarak, çıkış aşağıdakine benzer ve `Activating` durumu gösterir:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Etkinleştirildikten sonra durum olur `Online` ve hizmet örneği bir ağ geçidi adresine ve genel IP adresine sahiptir. Şimdilik, bu adresler herhangi bir içerik sunmaz. Örnek:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

API Management hizmet örneğiniz çevrimiçi olduğunda, bunu kullanmaya hazırsınız demektir. [Ilk API 'nizi içeri aktarma ve yayımlama](import-and-publish.md)öğreticisiyle başlayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu ve API Management hizmet örneğini kaldırabilirsiniz.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk API’nizi içeri aktarma ve yayımlama](import-and-publish.md)
