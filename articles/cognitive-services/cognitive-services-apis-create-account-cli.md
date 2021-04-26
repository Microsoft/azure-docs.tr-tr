---
title: Azure CLı kullanarak bilişsel hizmetler kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Azure komut satırı arabirimini kullanarak bir kaynağı oluşturup abone yaparak Azure bilişsel hizmetler 'i kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: bilişsel hizmetler, bilişsel zeka, bilişsel çözümler, AI Hizmetleri
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789150"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Hızlı başlangıç: Azure Command-Line arabirimini (CLı) kullanarak bilişsel hizmetler kaynağı oluşturma

Azure [komut satırı arabirimi 'ni (CLI)](/cli/azure/install-azure-cli)kullanarak Azure bilişsel hizmetler 'i kullanmaya başlamak için bu hızlı başlangıcı kullanın.

Azure bilişsel hizmetler, REST API 'lerle bulut tabanlı hizmetlerdir ve geliştiricilerin doğrudan yapay zeka (AI) veya veri bilimi becerileri veya bilgi sahibi olmadan uygulamalara bilişsel zeka bilgileri oluşturmalarına yardımcı olacak istemci kitaplığı SDK 'Ları. Azure bilişsel hizmetler, geliştiricilerin kendi uygulamalarına kolayca bilişsel özellikler eklemesine, duymasına, konuşmasına, anlamasına ve hatta başlayabilmesine olanak tanıyan bilişsel çözümler sunar.

Bilişsel hizmetler, Azure aboneliğinizde oluşturduğunuz Azure [kaynakları](../azure-resource-manager/management/manage-resources-portal.md) tarafından temsil edilir. Kaynağı oluşturduktan sonra, uygulamalarınızın kimliğini doğrulamak için oluşturulan anahtarları ve uç noktayı kullanın.

Bu hızlı başlangıçta, Azure [komut satırı arabirimi 'ni (CLI)](/cli/azure/install-azure-cli)kullanarak Azure bilişsel hizmetler 'e kaydolmayı ve tek hizmet veya çok hizmet aboneliği olan bir hesabı oluşturmayı öğreneceksiniz. Bu hizmetler Azure [kaynakları](../azure-resource-manager/management/manage-resources-portal.md)tarafından temsil edilir ve Azure bilişsel hizmetler API'si bir veya daha fazlasına bağlanmanızı sağlar.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Önkoşullar

* Geçerli bir Azure aboneliği-ücretsiz olarak [bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services) .
* [Azure komut satırı arabirimi (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLı 'yı yükleyip oturum açın

[Azure CLI](/cli/azure/install-azure-cli)'yı yükler. CLı 'nın yerel yüklemesinde oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırın:

```azurecli-interactive
az login
```

Bu komutları tarayıcınızda çalıştırmak için yeşil **TRY It** düğmesini de kullanabilirsiniz.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Yeni bir Azure bilişsel hizmetler kaynak grubu oluşturma

Bilişsel hizmetler kaynağı oluşturmadan önce, kaynağı içermesi için bir Azure Kaynak grubunuz olmalıdır. Yeni bir kaynak oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olanı kullanma seçeneğiniz vardır. Bu makalede, yeni bir kaynak grubu oluşturma gösterilmektedir.

### <a name="choose-your-resource-group-location"></a>Kaynak grubu konumunuzu seçin

Bir kaynak oluşturmak için, aboneliğiniz için kullanılabilen Azure konumlarından birine ihtiyacınız olacaktır. [Az Account List-Locations](/cli/azure/account#az_account_list_locations) komutuyla kullanılabilir konumların bir listesini alabilirsiniz. Birçok farklı konumdan en bilişsel hizmetlere erişilebilir. Size en yakın olanı seçin ya da hizmet için hangi konumların kullanılabildiğini görün.

> [!IMPORTANT]
> * Azure bilişsel hizmetler 'i çağırırken ihtiyacınız olacak şekilde Azure konumunuzu unutmayın.
> * Bilişsel Hizmetlerin kullanılabilirliği bölgeye göre farklılık gösterebilir. Daha fazla bilgi için bkz. [bölgeye göre Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure konumunuz olduktan sonra, [az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak Azure CLI 'de yeni bir kaynak grubu oluşturun.

Aşağıdaki örnekte, Azure konumunu, `westus2` aboneliğiniz için kullanılabilen Azure konumlarından biriyle değiştirin.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Bilişsel Hizmetler kaynağı oluşturma

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Bilişsel hizmet ve fiyatlandırma katmanı seçin

Yeni bir kaynak oluştururken, kullanmak istediğiniz hizmetin "tür" i ve istediğiniz [fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/cognitive-services/) (veya SKU) bilmeniz gerekir. Kaynağı oluştururken bu ve diğer bilgileri parametre olarak kullanacaksınız.

### <a name="multi-service"></a>Çoklu hizmet

| Hizmet                    | Tip                      |
|----------------------------|---------------------------|
| Birden çok hizmet. Daha fazla bilgi için [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/) sayfasına bakın.            | `CognitiveServices`     |


> [!NOTE]
> Aşağıdaki bilişsel hizmetler 'in çoğunda hizmeti denemek için kullanabileceğiniz ücretsiz bir katman vardır. Ücretsiz katmanı kullanmak için, `F0` kaynağınız için SKU olarak kullanın.

### <a name="vision"></a>Görsel

| Hizmet                    | Tip                      |
|----------------------------|---------------------------|
| Görüntü İşleme            | `ComputerVision`          |
| Özel Görüntü İşleme tahmin | `CustomVision.Prediction` |
| Özel Görüntü İşleme-eğitim   | `CustomVision.Training`   |
| Yüz                       | `Face`                    |
| Form Tanıma            | `FormRecognizer`          |
| Mürekkep Tanıma             | `InkRecognizer`           |

### <a name="speech"></a>Konuşma

| Hizmet            | Tip                 |
|--------------------|----------------------|
| Konuşma Hizmetleri    | `SpeechServices`     |
| Konuşma Tanıma | `SpeakerRecognition` |

### <a name="language"></a>Dil

| Hizmet            | Tip                |
|--------------------|---------------------|
| Form anlama | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| Soru-Cevap Oluşturucu          | `QnAMaker`          |
| Metin Analizi     | `TextAnalytics`     |
| Metin Çevirisi   | `TextTranslation`   |

### <a name="decision"></a>Karar

| Hizmet           | Tip               |
|-------------------|--------------------|
| Anomali Algılayıcısı  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Kişiselleştirme      | `Personalizer`     |

Mevcut bilişsel hizmet hizmeti "türlerinin" bir listesini [az biliveservices hesap listesi-tür](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) komutuyla bulabilirsiniz:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Kaynak grubunuza yeni bir kaynak ekleyin

Yeni bir bilişsel hizmetler kaynağı oluşturup abone olmak için [az biliveservices hesabı oluştur](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) komutunu kullanın. Bu komut, daha önce oluşturulan kaynak grubuna yeni bir faturalanabilir kaynak ekler. Yeni kaynağınızı oluştururken, kullanmak istediğiniz hizmetin "tür" değerini, fiyatlandırma katmanını (veya SKU 'su) ve bir Azure konumunu bilmeniz gerekir:

Anomali algılayıcısı için aşağıdaki komutla adlandırılmış bir F0 (ücretsiz) kaynağı oluşturabilirsiniz `anomaly-detector-resource` .

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Kaynağınızın anahtarlarını alın

Command-Line arabirimi (CLı) yerel yüklemenizde oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu kullanın.

```azurecli-interactive
az login
```

Bilişsel hizmet kaynağınızın anahtarlarını almak için [az biliveservices hesap anahtarları listesi](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) komutunu kullanın.

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
* Önceden tanımlı işlem sayısının maliyeti. Bu tutarın üzerine gitmek, hizmetinize ilişkin [fiyatlandırma ayrıntılarında](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) belirtilen ek bir ücretden oluşmasına neden olur.

## <a name="get-current-quota-usage-for-your-resource"></a>Kaynağınız için geçerli kota kullanımını alın

Bilişsel hizmet kaynağınızın kullanımını almak için [az biliveservices hesap listesi-kullanım](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) komutunu kullanın.

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
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Ayrıca bkz.

* Bilişsel hizmetlerle güvenli bir şekilde çalışmak için bkz. Azure bilişsel **[Hizmetler 'e yönelik kimlik doğrulama istekleri](authentication.md)** .
* Bilişsel hizmetler 'deki farklı kategorilerin listesini almak için bkz. **[Azure bilişsel hizmetler nedir?](./what-are-cognitive-services.md)** .
* Bilişsel hizmetlerin desteklediği doğal dillerin listesini görmek için bkz. **[doğal dil desteği](language-support.md)** .
* Bilişsel hizmetler 'in şirket içi hizmetlerini nasıl kullanacağınızı anlamak için bkz. bilişsel **[Hizmetler 'i kapsayıcı olarak kullanma](cognitive-services-container-support.md)** .
* Bilişsel hizmetler 'in kullanım maliyetini tahmin etmek için bilişsel **[Hizmetler 'in maliyetlerini planlayın ve yönetin](plan-manage-costs.md)** bölümüne bakın.
