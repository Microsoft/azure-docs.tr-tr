---
title: Azure API Management eski Geliştirici Portalında sayfa stilini özelleştirme | Microsoft Docs
description: Azure API Management geliştirici portalının stil öğelerini özelleştirmek için bu hızlı başlangıçtaki adımları izleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 779d1228393cdc88f88820d8a335cb2e32424c14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176867"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Geliştirici portalı sayfalarının stilini özelleştirme

Azure API Management geliştirici portalını özelleştirmenin en yaygın üç yolu vardır:
 
* [Statik sayfaların ve sayfa düzeni öğelerinin içeriğini düzenleme](api-management-modify-content-layout.md)
* Geliştirici portalının tamamında sayfa öğeleri için kullanılan stilleri güncelleştirme (bu kılavuzda açıklanmıştır)
* [Portal tarafından oluşturulan sayfalar için kullanılan şablonları değiştirme](api-management-developer-portal-templates.md) (örneğin, API belgeleri, ürünler, kullanıcı kimlik doğrulaması)

Bu makalede, eski **Geliştirici** portalının sayfalarında öğelerin stilini özelleştirmeyi ve değişikliklerinizi görüntülemeyi öğreneceksiniz.

![stili özelleştir](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Geliştirici portalını özelleştirme

1. **Genel Bakış**’ı seçin.
2. **Genel bakış** penceresinin en üstündeki **Geliştirici Portalı (eski)** düğmesine tıklayın.
3. Ekranın sol üst tarafında, iki boya fırçasından oluşan bir simge görürsünüz. Portal özelleştirme menüsünü açmak için bu simgenin üzerine gelin.

    ![stili özelleştir](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Stil özelleştirme bölmesini açmak için menüden **Stiller**’i seçin.

    **Stilleri**’i kullanarak özelleştirebileceğiniz tüm öğeler sayfada görüntülenir
5. **Geliştirici portalı görünümünü özelleştirmek için değişken değerleri değiştirin:** alanında "headings-color" değerini girin.

    Sayfada **\@başlıkları-rengi** öğesi görünür. Bu değişken metnin rengini denetler.

    ![stili özelleştir](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. **\@başlıkları-renk** değişkeni için alana tıklayın. 
    
    Renk seçici açılır menüsü açılır.
7. Renk seçici açılır menüsünden yeni bir renk seçin.

    > [!TIP]
    > Tüm değişiklikler için gerçek zamanlı önizleme kullanılabilir. Özelleştirme bölmesinin yukarısında bir ilerleme göstergesi görünür. Birkaç saniye sonra üst bilgi metni rengi yeni seçilen renge değişir.

8. Özelleştirme bölmesi menüsünde sol alt tarafta **Yayımla**’yı seçin.
9. Değişiklikleri genel olarak kullanılabilir yapmak için **Özelleştirmeleri yayımla**’yı seçin.

## <a name="view-your-change"></a>Değişikliğinizi görüntüleme

1. Geliştirici portalına gidin.
2. Yaptığınız değişikliği görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Şablonları kullanarak Azure API Management geliştiricisi portalı nasıl özelleştirebileceğinizi de](api-management-developer-portal-templates.md) öğrenmek isteyebilirsiniz.