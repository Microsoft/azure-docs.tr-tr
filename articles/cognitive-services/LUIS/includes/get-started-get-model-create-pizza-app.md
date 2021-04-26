---
title: include dosyası
description: include dosyası
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: a021b7c5b26e1bca1e3f623ad762c2f3ee8d6020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97820755"
---
Pizza uygulamasını oluşturun.

1. Dosya için GitHub sayfasını açmak üzere [pizza-app-for-luis-v6.json '](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) u seçin `pizza-app-for-luis.json` .
1. **Ham** düğmesine sağ tıklayın veya uzun dokunduktan sonra **Bağlantıyı farklı kaydet** ' i seçerek bilgisayarınıza kaydedin `pizza-app-for-luis.json` .
1. [Lui portalında](https://www.luis.ai)oturum açın.
1. [Uygulamalarım](https://www.luis.ai/applications)' ı seçin.
1. **Uygulamalarım** sayfasında, **konuşma için + yeni uygulama**' yı seçin.
1. **JSON olarak Içeri aktar**' ı seçin.
1. **Yeni uygulama al** Iletişim kutusunda **Dosya Seç** düğmesini seçin.
1. `pizza-app-for-luis.json`İndirdiğiniz dosyayı seçin ve **Aç**' ı seçin.
1. **Yeni uygulama Içeri aktarma** Iletişim kutusu **adı** alanına, pizza uygulamanız için bir ad girin ve ardından **bitti** düğmesini seçin.

Uygulama içeri aktarılacak.

**Etkin BIR Luo uygulamasının nasıl oluşturulacağı hakkında** bir iletişim kutusu görürseniz, iletişim kutusunu kapatın.

## <a name="train-and-publish-the-pizza-app"></a>Pizza uygulamasını eğitme ve yayımlama

Pizza uygulamasındaki amaçları içeren bir liste ile **amaçlar** sayfasını görmeniz gerekir.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Pizza uygulamasına bir yazma kaynağı ekleme

1. **Yönet**' i seçin.
1. **Azure Kaynakları**’nı seçin.
1. **Yazma kaynağı**' nı seçin.
1. **Yazma kaynağını Değiştir**' i seçin.

Bir yazma kaynağınız varsa, yazma kaynağınızın **kiracı adı**, **abonelik adı** ve **Luo kaynak adı** ' nı girin.

Bir yazma kaynağınız yoksa:

1. **Yeni kaynak oluştur**' u seçin.
1. Bir **kiracı adı**, **kaynak adı**, **abonelik adı** ve **Azure Kaynak grubu adı** girin.

Pizza uygulamanız artık kullanıma hazırdır.

## <a name="record-the-access-values-for-your-pizza-app"></a>Pizza uygulamanız için erişim değerlerini kaydetme

Yeni pizza uygulamanızı kullanmak için, pizza uygulamanızın uygulama KIMLIĞI, yazma anahtarı ve yazma uç noktasına ihtiyacınız olacaktır. Tahminlere ulaşmak için, ayrı tahmin uç noktanız ve tahmin anahtarınız olması gerekir.

Bu değerleri bulmak için:

1. **Amaçlar** sayfasından **Yönet**' i seçin.
1. **Uygulama ayarları** sayfasında, **uygulama kimliğini** kaydedin.
1. **Azure Kaynakları**’nı seçin.
1. **Yazma kaynağı**' nı seçin.
1. **Kaynak** ve **tahmin kaynakları** sekmelerinde, **birincil anahtarı** kaydedin. Bu değer, yazma anahtarınıza ait.
1. **Uç nokta URL 'sini** kaydedin. Bu değer, yazma uç noktanıza ait.
