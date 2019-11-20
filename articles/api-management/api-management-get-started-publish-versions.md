---
title: Azure API Management’ı kullanarak API’nizin sürümlerini yayımlama | Microsoft Docs
description: API Management’ta API’nizin birden fazla sürümünü yayımlamayı öğrenmek için bu öğreticideki adımları uygulayın.
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
ms.openlocfilehash: d0c837ac376f9d496baaf1ed67d0ec80c996804a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176824"
---
# <a name="publish-multiple-versions-of-your-api"></a>API'nizin birden fazla sürümünü yayımlama 

API’nizin tüm çağıranlarının tam olarak aynı sürümü kullanmasını sağlamak bazen kullanışlı olmayabilir. Çağıranlar sonraki bir sürüme yükseltmek istediğinde, bunu anlaşılması kolay bir yaklaşımı kullanarak yapabilmek ister. Bunu Azure API Management’taki **sürümleri** kullanarak gerçekleştirmek mümkündür. Daha fazla bilgi için bkz. [Sürümler ve düzeltmeler](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir sürümü mevcut bir API’ye ekleme
> * Sürüm şeması seçme
> * Sürümü bir ürüne ekleme
> * Sürümü görüntülemek için geliştirici portalına göz atma

![Geliştirici portalında gösterilen sürüm](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="add-a-new-version"></a>Yeni bir sürüm ekleme

![API Bağlam menüsü - sürüm ekleme](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. API listesinden **Tanıtım Konferans API’sini** seçin.
2. Bunun yanındaki bağlam menüsünü ( **...** ) seçin.
3. **+ Sürüm Ekle** seçeneğini belirleyin.

> [!TIP]
> Sürümler yeni bir API’yi ilk kez oluşturduğunuzda da etkinleştirilebilir. Bunun için **API Ekle** ekranında **Bu API’nin sürümü oluşturulsun mu?** seçeneğini belirleyin.

## <a name="choose-a-versioning-scheme"></a>Sürüm oluşturma düzeni seçme

Azure API Management, çağıranlara istedikleri API sürümünü belirtme olanağını nasıl sunacağınızı seçmenizi sağlar. Kullanılacak API sürümünü bir **sürüm oluşturma düzeni** seçerek belirtirsiniz. Bu düzen **yol, üst bilgi veya sorgu dizesi** olabilir. Aşağıdaki örnekte, sürüm oluşturma düzeni için yol kullanılmaktadır.

![Sürüm ekle ekranı](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. **Sürüm oluşturma düzeniniz** olarak **yol** seçeneğini belirlenmiş halde bırakın.
2. **Ad** alanına **demo-conference-api-v1** yazın.

    > [!NOTE]
    > Sürüm aslında API'nin düzeltmesine dayanan yeni bir API'dir. **Ad**, yeni API’nin adıdır ve API Management örneği genelinde benzersiz olmalıdır.

3. **Sürüm tanımlayıcısı** alanına **v1** yazın.

    > [!TIP]
    > Sürüm oluşturma şeması olarak **üst bilgi** veya **sorgu dizesi** seçeneğini belirlerseniz üst bilgi veya sorgu dizesi parametresinin adı gibi bir ek değer belirtmeniz gerekir.

4. Yeni sürümünüzü ayarlamak için **Oluştur**’u seçin.
5. API listesindeki **Demo Konferans API’sinin** altında artık iki ayrı API görürsünüz: **Özgün** ve **v1**.

    ![Azure portalında bir API altında listelenen sürümler](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Sürüm bilgisi olmayan bir API’ye sürüm eklerseniz, varsayılan URL’de yanıt veren bir **Özgün** sürüm otomatik olarak oluşturulur. Bu, mevcut çağıranların sürüm ekleme işleminden olumsuz yönde etkilenmemesini sağlar. Başlangıçta etkinleştirilen sürümlerle yeni bir API oluşturursanız Özgün sürüm oluşturulmaz.

6. Artık **Özgün** sürümden ayrı bir API olarak **v1** sürümünü düzenleyip yapılandırabilirsiniz. Bir sürümde yapılan değişiklikler diğer bir sürümü etkilemez.

## <a name="add-the-version-to-a-product"></a>Sürümü bir ürüne ekleme

Çağıranların yeni sürümü görmesi için sürümün **ürüne** eklenmesi gerekir.

![API Management Ürünleri](media/api-management-getstarted-publish-versions/08-AddMultipleVersions-03-AddVersionToProduct.png)

1. Klasik dağıtım modeli sayfasından **Ürünler** seçeneğini belirleyin.
2. **Sınırsız**’ı seçin.
3. **API’ler** seçeneğini belirleyin.
4. **Add (Ekle)** seçeneğini belirleyin.
5. **Demo Konferans API’si, Sürüm v1** seçeneğini belirleyin.
6. **Seç**'e tıklayın.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Sürümü görüntülemek için geliştirici portalına göz atma

1. Üstteki menüden **Geliştirici Portalı**’nı seçin.
2. **API 'leri** seçin ve **Tanıtım Konferansı API 'sine**tıklayın.
3. API adının yanında birden fazla sürümü olan bir açılan menü görmeniz gerekir.
4. **v1**’i seçin.
5. Listedeki ilk işlemin **İstek URL'sine** dikkat edin. Bu, API URL’si yolunun **v1** içerdiğini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir sürümü mevcut bir API’ye ekleme
> * Sürüm şeması seçme 
> * Sürümü bir ürüne ekleme
> * Sürümü görüntülemek için geliştirici portalına göz atma

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Geliştirici portalı sayfalarının stilini özelleştirme](api-management-customize-styles.md)