---
title: Azure portalını kullanarak el ile API ekleme  | Microsoft Docs
description: Bu öğreticide, el ile API eklemek için API Management’ın (APIM) nasıl kullanılacağı gösterilir.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183856"
---
# <a name="add-an-api-manually"></a>El ile API ekleme

Bu makaledeki adımlar, Azure portalını kullanarak API Management (APIM) örneğine nasıl el ile API ekleneceğini gösterir. Boş bir API oluşturup el ile tanımlamanızı gerektirecek yaygın bir senaryo, API’nin sahte bir kopyasını oluşturmak istediğiniz durumlardır. Bir API’nin sahte bir kopyasını oluşturma hakkında ayrıntılı bilgi için bkz. [Sahte API yanıtları](mock-api-responses.md).

Mevcut bir API’yi içeri aktarmak istiyorsanız [ilgili konular](#related-topics) bölümüne bakın.

Bu makalede boş bir API oluşturacak ve [httpbin.org](https://httpbin.org)’u (genel bir test hizmeti) bir arka uç API’si olarak ekleyeceğiz.

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı doldurun: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Bir API oluşturma

1. Azure portal API Management hizmetinize gidin ve menüden **API 'leri** seçin.
2. Soldaki menüden **+ API Ekle**'yi seçin.
3. Listeden **Boş API**’yi seçin.  
    ![Boş API](media/add-api-manually/blank-api.png)  
4. API için ayarları girin. Ayarlar, [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md#import-and-publish-a-backend-api) öğreticisinde açıklanmıştır.
5. **Oluştur**’u seçin.

Bu noktada, arka uç API 'nizin işlemlerine eşlenen API Management hiçbir işlem yok. Arka uçtan açığa çıkarılan ancak API Management aracılığıyla kullanıma sunulan bir işlem çağırırsanız, **404** alırsınız.

>[!NOTE] 
> Varsayılan olarak, bir API eklediğinizde, bir arka uç hizmetine bağlı olsa bile APıM, izin verene kadar herhangi bir işlem sergilemez. Arka uç hizmetinizin bir işlemine izin vermek için, arka uç işlemiyle eşleşen bir APıM işlemi oluşturun.

## <a name="add-and-test-an-operation"></a>İşlem ekleme ve test etme

Bu bölümde, arka uçtaki "http://httpbin.org/get" işlemiyle eşlemek üzere nasıl bir "/get" işlemi ekleneceği gösterilir.

### <a name="add-an-operation"></a>Bir işlem ekleme

1. Önceki adımda oluşturduğunuz API’yi seçin.
2. **+ İşlem Ekle**’ye tıklayın.
3. **URL** bölümünde **GET** seçeneğini belirleyip kaynak bölümüne "*/get*" değerini girin.
4. **Görünen ad** alanına "*FetchData*" değerini girin.
5. **Kaydet**’i seçin.

### <a name="test-an-operation"></a>İşlemleri test etme

Azure portalında işlemi test edin. Alternatif olarak, **Geliştirici portalında** da test edebilirsiniz.

1. **Test** sekmesini seçin.
2. **FetchData** seçeneğini belirleyin.
3. **Gönder**’e basın.

"http://httpbin.org/get" işleminin oluşturduğu yanıt görünür. İşlemlerinizi dönüştürmek istiyorsanız bkz. [API'nizi dönüştürme ve koruma](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Parametreli işlem ekleme ve test etme

Bu bölümde, nasıl parametre alan bir işlem ekleneceği gösterilir. Bu durumda, işlemi "http://httpbin.org/status/200" ile eşliyoruz.

### <a name="add-the-operation"></a>İşlemi ekleme

1. Önceki adımda oluşturduğunuz API’yi seçin.
2. **+ İşlem Ekle**’ye tıklayın.
3. **URL** bölümünde **GET** seçeneğini belirleyip kaynak bölümüne "*/status/{code}*" değerini girin. İsteğe bağlı olarak, bu parametre ile ilişkili bazı bilgiler sağlayabilirsiniz. Örneğin, **TYPE** alanına "*Number*" değerini, **VALUES** alanına "*200*" (varsayılan) değerini girin.
4. **Görünen ad** alanına "GetStatus" değerini girin.
5. **Kaydet**’i seçin.

### <a name="test-the-operation"></a>İşlemi test etme 

Azure portalında işlemi test edin.  Alternatif olarak, **Geliştirici portalında** da test edebilirsiniz.

1. **Test** sekmesini seçin.
2. **GetStatus** seçeneğini belirleyin. Varsayılan olarak, kod değeri "*200*" olarak ayarlanır. Diğer değerleri test etmek için değiştirebilirsiniz. Örneğin, "*418*" yazın.
3. **Gönder**’e basın.

    "http://httpbin.org/status/200" işleminin oluşturduğu yanıt görünür. İşlemlerinizi dönüştürmek istiyorsanız bkz. [API'nizi dönüştürme ve koruma](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
