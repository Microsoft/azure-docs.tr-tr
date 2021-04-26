---
title: Anahtar sözcük hızlı başlangıç-konuşma hizmeti oluşturma
titleSuffix: Azure Cognitive Services
description: Cihazınız her zaman bir anahtar sözcüğü (veya tümceciği) dinler. Kullanıcı anahtar sözcüğünü söyyorsa, cihaz sonraki tüm sesleri buluta gönderir ve Kullanıcı konuşmayı durduruncaya kadar. Anahtar keliinizi özelleştirmek, cihazınızı ayırt etmenin ve markanızı güçlendirmenin etkili bir yoludur.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021958"
---
# <a name="get-started-with-custom-keyword"></a>Özel Anahtar Sözcük ile çalışmaya başlama

Bu hızlı başlangıçta, konuşma Studio ve konuşma SDK 'sını kullanarak özel anahtar sözcüklerle çalışmanın temellerini öğreneceksiniz. Anahtar sözcüğü, ürününüzün ses özellikli olmasını sağlayan bir kelime veya kısa bir tümceciktir. Anahtar sözcük modellerini, konuşma Studio 'da oluşturursunuz ve ardından, uygulamalarınızda konuşma SDK 'Sı ile kullandığınız bir model dosyasını dışarı aktarabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımlarda bir konuşma aboneliği ve konuşma SDK 'Sı gerekir. Henüz bir aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](overview.md#try-the-speech-service-for-free). SDK 'yı almak için platformunuzun [Install Guide](quickstarts/setup-platform.md) bölümüne bakın.

## <a name="create-a-keyword-in-speech-studio"></a>Konuşma Studio 'da anahtar sözcük oluşturma

Özel bir anahtar sözcüğü kullanabilmeniz için, [konuşma Studio](https://aka.ms/sdsdk-speechportal)'Daki [özel anahtar](https://aka.ms/sdsdk-wakewordportal) sözcük sayfasını kullanarak bir anahtar sözcük oluşturmanız gerekir. Anahtar sözcük sağlamadıktan sonra, `.table` konuşma SDK 'sı ile kullanabileceğiniz bir dosya oluşturur.

> [!IMPORTANT]
> Özel anahtar sözcük modelleri ve elde edilen `.table` dosyalar **yalnızca** konuşma Studio 'da oluşturulabilir.
> SDK 'dan veya REST çağrılarıyla özel anahtar sözcükler oluşturamazsınız.

1. [Konuşma Studio](https://aka.ms/sdsdk-speechportal) 'ya gidin ve **oturum açın** veya henüz bir konuşma aboneliğiniz yoksa, [**abonelik oluştur**](https://go.microsoft.com/fwlink/?linkid=2086754)' u seçin.

1. [Özel anahtar sözcük](https://aka.ms/sdsdk-wakewordportal) sayfasında **Yeni bir proje** oluşturun. 

1. Bir **ad**, isteğe bağlı bir **Açıklama** girin ve dili seçin. Dil başına bir proje gerekir ve şu anda dille sınırlı destek vardır `en-US` .

    ![Anahtar sözcük projenizi açıkla](media/custom-keyword/custom-kws-portal-new-project.png)

1. Listeden projenizi seçin. 

    ![Anahtar sözcük projenizi seçin](media/custom-keyword/custom-kws-portal-project-list.png)

1. Yeni bir anahtar sözcük modeli oluşturmak için **modeli eğitme**' ye tıklayın.

1. Model için bir **ad** , isteğe bağlı bir **Açıklama** ve istediğiniz **anahtar sözcüğü** girin ve ardından **İleri**' ye tıklayın. Etkin anahtar sözcük seçme [yönergelerine](./custom-keyword-overview.md#choose-an-effective-keyword) bakın.

    ![Anahtar keliinizi girin](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portal, anahtar kelime için aday söylenişleri oluşturur. Oynat düğmelerine tıklayarak her bir adayı dinleyin ve yanlış olan herhangi bir söylenin yanındaki denetimleri kaldırın. Yalnızca güzel Söylenişler denetlendikten sonra, anahtar sözcük modelini oluşturmaya başlamak için **eğitme** ' ye tıklayın. 

    ![Doğru pronounciations seçtiğiniz yeri gösteren ekran görüntüsü.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Modelin oluşturulması otuz dakika kadar sürebilir. Model tamamlandığında anahtar sözcük listesi **Işlemden** **başarılı** olarak değişecektir. Sonra dosyayı indirebilirsiniz.

    ![Anahtar keliinizi gözden geçirin](media/custom-keyword/custom-kws-portal-download-model.png)

1. İndirilen dosya bir `.zip` Arşiv. Arşivi ayıklayın ve uzantılı bir dosya görürsünüz `.table` . Bu, sonraki bölümde SDK ile birlikte kullandığınız dosyadır. bu nedenle yolunu aklınızda olduğunuzdan emin olun. dosya adı, anahtar kelimesinin adını yansıtır, örneğin bir anahtar sözcük **etkinleştirme cihazı** dosya adına sahiptir `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>SDK ile anahtar sözcük modeli kullanma

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Konuşma CIHAZLARı SDK hızlı başlangıç](./speech-devices-sdk-quickstart.md?pivots=platform-android)ile özel anahtar keliinizi test edin.