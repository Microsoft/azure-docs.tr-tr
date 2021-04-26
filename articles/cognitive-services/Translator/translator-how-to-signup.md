---
title: Çevirmen kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler çevirmen kaynağı oluşturma ve abonelik anahtarı ve uç nokta URL 'SI alma işlemlerinin nasıl yapılacağı gösterilir.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101712938"
---
# <a name="create-a-translator-resource"></a>Çevirmen kaynağı oluşturma

Bu makalede, Azure portal bir Translator kaynağı oluşturmayı öğreneceksiniz. [Azure çevirmeni](translator-info-overview.md) , Azure bilişsel [Hizmetler](../what-are-cognitive-services.md) ailesinin REST API 'lerin bir parçası olan bulut tabanlı bir makine çevirisi hizmetidir. Azure kaynakları, oluşturduğunuz hizmetlerin örnekleridir. Azure hizmetlerine yönelik tüm API istekleri için bir **uç nokta** URL 'si ve erişim kimlik doğrulaması için salt okunurdur bir **abonelik anahtarı** gerekir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için etkin bir [**Azure hesabınız**](https://azure.microsoft.com/free/cognitive-services/)olması gerekir.  Henüz bir tane yoksa, [**12 aylık ücretsiz bir abonelik oluşturabilirsiniz**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Çevirmen kaynak türleri

Çevirmen hizmetine iki farklı kaynak türü aracılığıyla erişilebilir:

* **Tek hizmet** kaynak türleri tek BIR hizmet API anahtarına ve uç noktaya erişimi etkinleştirir.  

* Çoklu **hizmet** kaynak türleri, tek bir API anahtarı ve uç nokta kullanarak birden çok bilişsel hizmete erişim sağlar. Bilişsel hizmetler kaynağı şu anda aşağıdaki hizmetler için kullanılabilir:
  * Dil ([Translator](../translator/translator-info-overview.md), [Language Understanding (luu)](../luis/what-is-luis.md), [metin analizi](../text-analytics/overview.md))  
  * Vizyon ([görüntü işleme](../computer-vision/overview.md)), ([yüz](../face/overview.md))  
  * Karar ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Kaynağınız oluşturun

* Proje ayrıntılarınızı gerçekleştirmek için Azure portal [**çevirici oluştur**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) sayfasına doğrudan gidin.

* Proje ayrıntılarınızı gerçekleştirmek için Azure portal bilişsel [**Hizmetler oluşturma**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) sayfasına doğrudan gidin.

>[!TIP]
>İsterseniz, **oluşturma** işlemine aşağıdaki gibi başlamak Için Azure Portal giriş sayfasında başlayabilirsiniz:
>
> 1. [**Azure Portal**](https://ms.portal.azure.com/#home) giriş sayfasına gidin.
> 1. Azure hizmetleri menüsünden **kaynak oluşturma**  ➕ seçin.
>1. Market aramasını **Ara** kutusunda, **çevirmen** (tek hizmet kaynağı) veya bilişsel **Hizmetler** (çok hizmet kaynağı) girin ve seçin.  *Bkz* . [kaynak türünü seçme](#create-your-resource), yukarıdaki.
> 1. **Oluştur** ' u seçin ve proje ayrıntıları sayfasına yönlendirilirsiniz.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Projenizi ve örnek ayrıntılarınızı doldurun

1. **Abonelik**. Kullanılabilir Azure aboneliklerinizden birini seçin.

1. **Kaynak Grubu**. Seçtiğiniz Azure Kaynak grubu, yeni kaynağınız için bir sanal kapsayıcı olarak görev yapar. Yeni bir kaynak grubu oluşturabilir veya kaynağınızı aynı yaşam döngüsünü, izinleri ve ilkeleri paylaşan önceden var olan bir kaynak grubuna ekleyebilirsiniz.

1. **Kaynak bölgesi**. İşiniz veya uygulamanız için belirli bir bölge gerekmiyorsa **genel** ' i seçin. Çevirmen, bölgesel olmayan bir hizmettir; belirli bir Azure bölgesinin bağımlılığı yoktur. *Bkz* . [Azure 'da bölgeler ve kullanılabilirlik alanları](../../availability-zones/az-overview.md).

1. **Ad**. Kaynağınız için seçtiğiniz adı girin. Seçtiğiniz ad Azure içinde benzersiz olmalıdır.

> [!NOTE]
> Özel bir etki alanı uç noktası gerektiren bir Translator özelliği kullanıyorsanız, ad alanına girdiğiniz değer, uç nokta için özel etki alanı adı parametresi olur.

5. **Fiyatlandırma katmanı**. Gereksinimlerinizi karşılayan bir [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/cognitive-services/translator) seçin:

   * Her abonelik ücretsiz bir katmana sahiptir.
   * Ücretsiz katman, ücretli planlar ile aynı özelliklere ve işlevlere sahiptir ve süresi dolmaz.
   * Hesap başına yalnızca bir ücretsiz aboneliğe izin verilir.</li></ul>

1. Çoklu hizmet kaynağı oluşturduysanız, onay kutuları aracılığıyla ek kullanım ayrıntılarını onaylamanız gerekir.

1. **Gözden geçir + Oluştur**’u seçin.

1. Hizmet koşullarını gözden geçirin ve kaynağı dağıtmak için **Oluştur** ' u seçin.

1. Kaynağınız başarıyla dağıtıldıktan sonra **Kaynağa Git**' i seçin.

### <a name="authentication-keys-and-endpoint-url"></a>Kimlik doğrulama anahtarları ve uç nokta URL 'SI

Tüm bilişsel hizmetler API istekleri, kimlik doğrulaması için bir uç nokta URL 'SI ve salt okuma anahtarı gerektirir.

* **Kimlik doğrulama anahtarları**. Anahtarınız, çeviri hizmetine yapılan her istekte geçirilen benzersiz bir dizedir. Anahtarınızı bir sorgu dizesi parametresiyle veya HTTP istek üstbilgisinde belirterek geçirebilirsiniz.

* **Uç nokta URL 'si**. Belirli bir Azure bölgesine gerek duymadığınız takdirde API talebinizdeki genel uç noktasını kullanın. *Bkz* . [temel URL 'ler](reference/v3-0-reference.md#base-urls). Genel uç nokta URL 'SI `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Kimlik doğrulama Anahtarlarınızı ve uç noktanızı alın

1. Yeni kaynağınız dağıtıldıktan sonra **Kaynağa Git** ' i seçin veya doğrudan kaynak sayfanıza gidin.
1. Sol Rampadaki *kaynak yönetimi* altında **anahtarlar ve uç nokta**' ı seçin.
1. Abonelik Anahtarlarınızı ve uç nokta URL 'nizi, *Microsoft Notepad* gibi uygun bir konuma kopyalayıp yapıştırın.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Anahtar ve uç nokta al.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Bir kaynağı veya kaynak grubunu silme

> [!Warning]
> Bir kaynak grubunun silinmesi, grubun içerdiği tüm kaynakları da siler.

Bilişsel hizmetler veya çevirmen kaynağını kaldırmak için **Kaynağı silebilir** veya **kaynak grubunu silebilirsiniz**.

Kaynağı silmek için:

1. Azure portal kaynak grubunuza gidin.
1. Bitişik onay kutusunu seçerek silinecek kaynakları seçin.
1. Sağ kenardaki üstteki menüden **Sil** ' i seçin.
1. **Silinen kaynaklar** iletişim kutusunda *Evet* yazın.
1. **Sil**’i seçin.

Kaynak grubunu silmek için:

1. Azure portal kaynak grubunuza gidin.
1. Sol kenarın yakınında bulunan üstteki menü çubuğundan **kaynak grubunu sil** ' i seçin.
1. Kaynak grubu adını girip **Sil**' i seçerek silme isteğini onaylayın.

## <a name="how-to-get-started-with-translator"></a>Çevirmenle çalışmaya başlama

Hızlı Başlangıç bölümünde, Translator hizmetini REST API 'lerle nasıl kullanacağınızı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Çevirmenle çalışmaya başlama](quickstart-translator.md)

## <a name="more-resources"></a>Diğer kaynaklar

* [Microsoft Translator kodu örnekleri](https://github.com/MicrosoftTranslator).  Çok dilli çevirmen kodu örnekleri GitHub ' da kullanılabilir.
* [Microsoft Translator destek forumu](https://www.aka.ms/TranslatorForum)
* [Azure 'ı kullanmaya başlama (3 dakikalık video)](https://azure.microsoft.com/get-started/?b=16.24)