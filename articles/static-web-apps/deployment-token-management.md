---
title: Azure statik Web Apps dağıtım belirteçlerini sıfırlama (Önizleme)
description: Azure statik Web Apps sitesinde belirteçleri sıfırlama
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746523"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Azure statik Web Apps dağıtım belirteçlerini sıfırlama (Önizleme)

Yeni bir Azure statik Web Apps sitesi oluşturduğunuzda, Azure dağıtım sırasında uygulamayı tanımlamak için kullanılan bir belirteç oluşturur. Sağlama sırasında, bu belirteç GitHub deposunda bir gizli dizi olarak depolanır. Bu makalede, bu belirtecin nasıl kullanılacağı ve yönetileceği açıklanmaktadır.

Genellikle dağıtım belirteci hakkında endişelenmeniz gerekmez, ancak belirteci almanız veya sıfırlamanız gerekebilecek bazı nedenler aşağıda verilmiştir.

* **Belirteç uzlaşması**: bir dış tarafa açık olması durumunda belirtecinizi sıfırlayın.
* **Ayrı bir GitHub deposundan dağıtma**: ayrı bir GitHub deposundan el ile dağıtıyorsanız, yeni depoda dağıtım belirtecini ayarlamanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

- Azure statik Web Apps ile yapılandırılmış mevcut bir GitHub deposu.
- Bkz. bir tane yoksa, [ilk statik uygulamanızı oluşturma](getting-started.md) .

## <a name="reset-a-deployment-token"></a>Dağıtım belirtecini sıfırlama

1. Azure statik Web Apps sitenizin _genel bakış_ sayfasında **dağıtım belirtecini Yönet** bağlantısına tıklayın.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Dağıtım belirtecini yönetme":::

1. **Belirteci Sıfırla** düğmesine tıklayın.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Dağıtım belirteci sıfırlanıyor":::

1. _Dağıtım belirteci_ alanında yeni bir belirteç görüntülendikten sonra, **Panoya Kopyala** simgesine tıklayarak belirteci kopyalayın.


## <a name="update-a-secret-in-the-github-repository"></a>GitHub deposunda bir gizli anahtarı güncelleştirme

Otomatik dağıtımı çalışır durumda tutmak için bir belirteci sıfırladıktan sonra, ilgili GitHub deposundaki yeni değeri ayarlamanız gerekir.

1. GitHub 'daki projenizin deposuna gidin ve **Ayarlar** sekmesine tıklayın.
1. **Gizlilikler** menü öğesine tıklayın. _AZURE_STATIC_WEB_APPS_API_TOKEN_ adlı statik Web uygulaması sağlama sırasında oluşturulan gizli dizi bulacaksınız... _Depo gizli_ dizileri bölümünde.

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Depo gizli dizileri listeleme":::

    > [!NOTE]
    > Azure statik Web Apps sitesini bu deponun birden çok dallarına göre oluşturduysanız, birden çok _AZURE_STATIC_WEB_APPS_API_TOKEN_ görürsünüz... Bu listedeki gizli diziler. Statik Web Apps sitesinin _genel bakış_ sekmesinde _iş akışını Düzenle_ alanında listelenen dosya adını eşleştirerek doğru olanı seçin.

1. Düzenleyiciyi açmak için **Güncelleştir** düğmesine tıklayın.
1. Dağıtım belirtecinin değerini _değer_ alanına **yapıştırın** .
1. **Gizli anahtarı Güncelleştir** öğesine tıklayın.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Depo gizli anahtarı güncelleştiriliyor":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Statik bir site oluşturucusundan yayımlama](publish-gatsby.md)
