---
title: Azure CLı kullanarak bilişsel hizmetler kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Azure komut satırı arabirimini kullanarak bir kaynağı oluşturup abone yaparak Azure bilişsel hizmetler 'i kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: d715252f16a34f2261c108ebd3ff5d1f6f3424d8
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887925"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Azure komut satırı arabirimi 'ni (CLı) kullanarak bilişsel hizmetler kaynağı oluşturma

Azure [komut satırı arabirimi 'ni (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)kullanarak Azure bilişsel hizmetler 'i kullanmaya başlamak için bu hızlı başlangıcı kullanın. Bilişsel hizmetler, Azure aboneliğinizde oluşturduğunuz Azure [kaynakları](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) tarafından temsil edilir. Kaynağı oluşturduktan sonra, uygulamalarınızın kimliğini doğrulamak için oluşturulan anahtarları ve uç noktayı kullanın. 


Bu hızlı başlangıçta, Azure [komut satırı arabirimi 'ni (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)kullanarak Azure bilişsel hizmetler 'e kaydolmayı ve tek hizmet veya çok hizmet aboneliği olan bir hesabı oluşturmayı öğreneceksiniz. Bu hizmetler Azure [kaynakları](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)tarafından temsil edilir ve Azure bilişsel hizmetler API'si bir veya daha fazlasına bağlanmanızı sağlar.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Ön koşullar

* Geçerli bir Azure aboneliği-ücretsiz olarak [bir tane oluşturun](https://azure.microsoft.com/free/) .
* [Azure komut satırı arabirimi (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLı 'yı yükleyip oturum açın 

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)’yi yükleyin. CLı 'nın yerel yüklemesinde oturum açmak için [az Login](https://docs.microsoft.com/cli/azure/reference-index#az-login) komutunu çalıştırın:

```console
az login
```

Bu komutları tarayıcınızda çalıştırmak için yeşil **TRY It** düğmesini de kullanabilirsiniz.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Yeni bir Azure bilişsel hizmetler kaynak grubu oluşturma

Bilişsel hizmetler kaynağı oluşturmadan önce, kaynağı içermesi için bir Azure Kaynak grubunuz olmalıdır. Yeni bir kaynak oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olanı kullanma seçeneğiniz vardır. Bu makalede, yeni bir kaynak grubu oluşturma gösterilmektedir.

### <a name="choose-your-resource-group-location"></a>Kaynak grubu konumunuzu seçin

Bir kaynak oluşturmak için, aboneliğiniz için kullanılabilen Azure konumlarından birine ihtiyacınız olacaktır. [Az Account List-Locations](/cli/azure/account#az-account-list-locations) komutuyla kullanılabilir konumların bir listesini alabilirsiniz. Birçok farklı konumdan en bilişsel hizmetlere erişilebilir. Size en yakın olanı seçin ya da hizmet için hangi konumların kullanılabildiğini görün.

> [!IMPORTANT]
> * Azure bilişsel hizmetler 'i çağırırken ihtiyacınız olacak şekilde Azure konumunuzu unutmayın.
> * Bilişsel Hizmetlerin kullanılabilirliği bölgeye göre farklılık gösterebilir. Daha fazla bilgi için bkz. [bölgeye göre Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure konumunuz olduktan sonra, [az Group Create](/cli/azure/group#az-group-create) komutunu kullanarak Azure CLI 'de yeni bir kaynak grubu oluşturun.

Aşağıdaki örnekte, Azure location `westus2`, aboneliğiniz için kullanılabilen Azure konumlarından biriyle değiştirin.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Bilişsel Hizmetler kaynağı oluşturma

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Bilişsel hizmet ve fiyatlandırma katmanı seçin

Yeni bir kaynak oluştururken, kullanmak istediğiniz hizmetin "tür" i ve istediğiniz [fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/cognitive-services/) (veya SKU) bilmeniz gerekir. Kaynağı oluştururken bu ve diğer bilgileri parametre olarak kullanacaksınız.

### <a name="multi-service"></a>Çoklu hizmet

| Hizmet                    | denetlenmesi                      |
|----------------------------|---------------------------|
| Birden çok hizmet. Daha fazla bilgi için [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/) sayfasına bakın.            | `CognitiveServices`     |


> [!NOTE]
> Aşağıdaki bilişsel hizmetler 'in çoğunda hizmeti denemek için kullanabileceğiniz ücretsiz bir katman vardır. Ücretsiz katmanı kullanmak için, kaynağınız için SKU olarak `F0` kullanın.

### <a name="vision"></a>Görsel

| Hizmet                    | denetlenmesi                      |
|----------------------------|---------------------------|
| Görüntü İşleme            | `ComputerVision`          |
| Özel Görüntü İşleme tahmin | `CustomVision.Prediction` |
| Özel Görüntü İşleme-eğitim   | `CustomVision.Training`   |
| Yüz Tanıma API'si                   | `Face`                    |
| Form Tanıma            | `FormRecognizer`          |
| Mürekkep Tanıma             | `InkRecognizer`           |

### <a name="search"></a>Arama

| Hizmet            | denetlenmesi                  |
|--------------------|-----------------------|
| Bing Otomatik Öneri   | `Bing.Autosuggest.v7` |
| Bing Özel Arama | `Bing.CustomSearch`   |
| Bing Varlık Arama | `Bing.EntitySearch`   |
| Bing Arama        | `Bing.Search.v7`      |
| Bing Yazım Denetimi   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Konuşma

| Hizmet            | denetlenmesi                 |
|--------------------|----------------------|
| Konuşma Hizmetleri    | `SpeechServices`     |
| Konuşma Tanıma | `SpeakerRecognition` |

### <a name="language"></a>Dil

| Hizmet            | denetlenmesi                |
|--------------------|---------------------|
| Form anlama | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| Soru-Cevap Oluşturucu          | `QnAMaker`          |
| Metin Analizi     | `TextAnalytics`     |
| Metin Çevirisi   | `TextTranslation`   |

### <a name="decision"></a>Karar

| Hizmet           | denetlenmesi               |
|-------------------|--------------------|
| Anomali Algılayıcısı  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Kişiselleştirme      | `Personalizer`     |

Mevcut bilişsel hizmet hizmeti "türlerinin" bir listesini [az biliveservices hesap listesi-tür](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) komutuyla bulabilirsiniz:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Kaynak grubunuza yeni bir kaynak ekleyin

Yeni bir bilişsel hizmetler kaynağı oluşturup abone olmak için [az biliveservices hesabı oluştur](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) komutunu kullanın. Bu komut, daha önce oluşturulan kaynak grubuna yeni bir faturalanabilir kaynak ekler. Yeni kaynağınızı oluştururken, kullanmak istediğiniz hizmetin "tür" değerini, fiyatlandırma katmanını (veya SKU 'su) ve bir Azure konumunu bilmeniz gerekir:

Anomali algılayıcısı için aşağıdaki komutla birlikte `anomaly-detector-resource` adlı bir F0 (ücretsiz) kaynağı oluşturabilirsiniz.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Kaynağınızın anahtarlarını alın

Komut satırı arabirimi (CLı) yerel yüklemenizde oturum açmak için [az Login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) komutunu kullanın.

```console
az login
```

Bilişsel hizmet kaynağınızın anahtarlarını almak için [az biliveservices hesap anahtarları listesi](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) komutunu kullanın.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Fiyatlandırma katmanları ve faturalandırma

Fiyatlandırma katmanları (ve faturalandırılan miktar), kimlik doğrulama bilgilerinizi kullanarak göndereceğiniz işlem sayısına bağlıdır. Her fiyatlandırma katmanı şunları belirtir:
* saniye başına izin verilen en fazla işlem sayısı (TPS).
* fiyatlandırma katmanında etkinleştirilen hizmet özellikleri.
* Önceden tanımlanmış işlem miktarının maliyeti. Bu tutarın üzerine gitmek, hizmetinize ilişkin [fiyatlandırma ayrıntılarında](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) belirtilen ek bir ücretden oluşmasına neden olur.

## <a name="get-current-quota-usage-for-your-resource"></a>Kaynağınız için geçerli kota kullanımını alın

Bilişsel hizmet kaynağınızın kullanımını almak için [az biliveservices hesap listesi-kullanım](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) komutunu kullanın.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler kaynağını temizleyip kaldırmak istiyorsanız, veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, grupta yer alan diğer tüm kaynakları da siler.

Kaynak grubunu ve ilişkili kaynaklarını kaldırmak için az Group Delete komutunu kullanın.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Ayrıca bkz.

* [Azure bilişsel hizmetler 'e yönelik isteklerin kimliğini doğrulama](authentication.md)
* [Azure bilişsel hizmetler nedir?](Welcome.md)
* [Doğal dil desteği](language-support.md)
* [Docker kapsayıcı desteği](cognitive-services-container-support.md)
