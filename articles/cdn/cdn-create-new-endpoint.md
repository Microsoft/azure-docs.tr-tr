---
title: Hızlı Başlangıç - Azure CDN profili ve uç noktası oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, yeni bir CDN profili ve uç noktası oluşturularak Azure CDN’nin nasıl etkinleştirileceği gösterilmektedir.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 2a3325217c1ec854e4f6cef3facce5580fb06a57
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594005"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Hızlı Başlangıç: Bir Azure CDN profili ve uç noktası oluşturma
Bu hızlı başlangıçta, yeni bir CDN profili ve CDN uç noktası oluşturarak Azure Content Delivery Network’ü (CDN) etkinleştireceksiniz. Bir profil ve uç nokta oluşturduktan sonra müşterilerinize içerik sunmaya başlayabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç amaçları doğrultusunda, kaynak konak adı için kullandığınız *mystorageacct123* adlı bir depolama hesabı oluşturmuş olmanız gerekir. Daha fazla bilgi için bkz. [Azure CDN ile bir Azure depolama hesabını tümleştirme](cdn-create-a-storage-account-with-cdn.md).

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma
Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Yeni bir CDN uç noktası oluşturma

CDN profili oluşturduktan sonra bir uç nokta oluşturmak için bunu kullanabilirsiniz.

1. Azure portalında, panonuzda oluşturduğunuz CDN profilini seçin. Bulamazsanız, **Tüm hizmetler**’i ve sonra **CDN profilleri**’ni seçin. **CDN profilleri** sayfasında, kullanmak istediğiniz profili seçin. 
   
    CDN profili sayfası görüntülenir.

2. **Uç nokta**’yı seçin.
   
    ![CDN profili](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    **Uç nokta ekleyin** bölmesi görünür.

3. Uç noktası ayarları için aşağıdaki tabloda belirtilen değerleri kullanın:

    | Ayar | Value |
    | ------- | ----- |
    | **Name** | Uç nokta konak adınız için *my-endpoint-123* değerini girin. Bu ad küresel olarak benzersiz olmalıdır. Daha önceden kullanılmışsa farklı bir ad girebilirsiniz. Bu ad, _&lt;uç nokta adı&gt;_ .azureedge.net etki alanındaki önbelleğe alınmış kaynaklarınıza erişmek için kullanılır.|
    | **Kaynak türü** | **Depolama**’yı seçin. | 
    | **Kaynak konak adı** | Konak adınız için *mystorageacct123.blob.core.windows.net* değerini girin. Bu ad küresel olarak benzersiz olmalıdır. Daha önceden kullanılmışsa farklı bir ad girebilirsiniz. |
    | **Kaynak yolu** | Boş bırakın. |
    | **Kaynak barındırma üst bilgisi** | Varsayılan olarak oluşturulan değeri değiştirmeyin. |  
    | **Protokolü** | Varsayılan **HTTP** ve **HTTPS** seçeneklerini seçili şekilde bırakın. |
    | **Kaynak bağlantı noktası** | Varsayılan bağlantı noktası değerlerini değiştirmeyin. | 
    | **Şunun için iyileştirildi:** | Varsayılan **Genel web teslimatı** seçimini değiştirmeyin. |

    ![Uç nokta ekleyin bölmesi](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Yeni uç nokta oluşturmak için **Ekle**’yi seçin.
   
   Uç nokta oluşturulduktan sonra, profile yönelik uç noktalar listesinde görünür.
    
   ![CDN uç noktası](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Kaydın yayılması zaman alacağından, uç nokta hemen kullanılabilir olmaz: 
   - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
   - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
   - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 90 dakika içinde tamamlanır. 

## <a name="clean-up-resources"></a>Kaynakları temizleme
Önceki adımlarda, bir kaynak grubunda CDN profili ve bir uç nokta oluşturdunuz. [Sonraki adımlara](#next-steps) gidip uç noktanıza nasıl özel etki alanı ekleneceğini öğrenmek istiyorsanız bu kaynakları kaydedin. Ancak ileride bu kaynakları kullanmayı düşünmüyorsanız, kaynak grubunu silerek bunları silebilir, böylece ek ücretleri önleyebilirsiniz:

1. Azure portalındaki sol menüden **Kaynak grupları**’nı seçin ve sonra **my-resource-group-123** seçeneğini belirleyin.

2. **Kaynak grubu** sayfasında **Kaynak grubunu sil**’i seçin, metin kutusuna *my-resource-group-123* değerini girin ve **Sil**’i seçin.

    Bu işlem, bu hızlı başlangıçta oluşturduğunuz kaynak grubunu, profili ve uç noktayı siler.

## <a name="next-steps"></a>Sonraki adımlar
CDN uç noktanıza özel etki alanı ekleme hakkında bilgi edinmek için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md)


