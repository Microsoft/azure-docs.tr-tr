---
title: Azure portalı ile sahte API yanıtları | Microsoft Docs
description: Bu öğreticide API Management (APIM) kullanarak bir API üzerinde sahte yanıt döndürmesini sağlayacak bir ilke ayarlama işlemi gösterilmektedir. Bu yöntem, arka ucun gerçek yanıtlar göndermek için kullanılamadığı durumlarda geliştiricilerin API Management örneğinde uygulama ve test işlemlerine devam etmesini sağlar.
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 6841695cca5d3864e6823085520d8e9162e54043
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067936"
---
# <a name="mock-api-responses"></a>Sahne API yanıtları

Arka uç API'leri bir APIM API’sine aktarılabilir veya el ile oluşturulup yönetilebilir. Bu öğreticideki adımlar, APIM kullanarak boş bir API oluşturma ve el ile yönetme işlemini gösterir. Bu öğreticide bir API üzerinde sahte yanıt döndürmesini sağlayacak bir ilke ayarlama işlemi gösterilmektedir. Bu yöntem, arka ucun gerçek yanıtlar göndermek için kullanılamadığı durumlarda bile geliştiricilerin APIM örneğinde uygulama ve test işlemlerine devam etmesini sağlar. Sahte yanıtlar oluşturabilmek birkaç senaryoda yararlı olabilir:

+ İlk olarak API cephesi tasarlanıp arka uç uygulaması daha sonra geldiğinde. Veya arka uç paralel olarak geliştirildiğinde.
+ Arka uç geçici olarak çalışır durumda olmadığında veya ölçeklenemediğinde.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Test API’si oluşturma 
> * Test API’sine işlem ekleme
> * Sahte yanıt vermeyi etkinleştirme
> * Sahte API’yi test etme

![Sahte işlem yanıtı](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ [Azure API Management'ta ilke kavramını](api-management-howto-policies.md) anlayın.
+ Aşağıdaki hızlı başlangıcı doldurun: [Azure API Management örneği oluşturun](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Test API’si oluşturma 

Bu bölümdeki adımlar arka uç olmadan boş bir API oluşturmayı gösterir. Ayrıca, API’ye bir işlem eklemeyi gösterir. Bu bölümdeki adımları tamamladıktan sonra işlem çağrılırsa bir hata oluşur. "Sahte yanıt vermeyi etkinleştirme" bölümündeki adımları tamamladıktan sonra herhangi bir hata almazsınız.

![Boş API oluşturma](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. **API Management** hizmetinden **API’ler**’i seçin.
2. Soldaki menüden **+ API Ekle**'yi seçin.
3. Listeden **Boş API**’yi seçin.
4. **Görünen ad** için "*Test API*" yazın.
5. **Ürünler** için "*Sınırsız*" yazın.
6. **Oluştur**’u seçin.

## <a name="add-an-operation-to-the-test-api"></a>Test API’sine işlem ekleme

![API'ye işlem ekleme](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Önceki adımda oluşturduğunuz API’yi seçin.
2. **+ İşlem Ekle**’ye tıklayın.

    | Ayar             | Değer                             | Açıklama                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Görünen ad**    | *Test çağrısı*                       | **Geliştirici portalında** görüntülenen ad.                                                                                                                                       |
    | **URL** (HTTP fiili) | GET                               | Önceden tanımlanmış HTTP fiillerinden birini seçebilirsiniz.                                                                                                                                         |
    | **URL**             | */test*                           | API için bir URL yolu.                                                                                                                                                                       |
    | **Açıklama**     |                                   | **Geliştirici portalında** bu API’yi kullanan geliştiricilere belge sağlamak için kullanılan işlemin bir açıklamasını girin.                                                    |
    | **Sorgu** sekmesi       |                                   | Sorgu parametreleri ekleyebilirsiniz. Bir ad ve açıklama sağlamaya ek olarak bu parametreye atanabilecek değerleri belirtebilirsiniz. Varsayılan olarak işaretlenebilecek değerlerde biri (isteğe bağlı). |
    | **İstek** sekmesi     |                                   | İstek içerik türleri, örnekler ve şemalar tanımlayabilirsiniz.                                                                                                                                  |
    | **Yanıt** sekmesi    | Bu tablodan sonraki adımlara bakın. | Yanıt durum kodları, içerik türleri, örnekler ve şemalar tanımlayın.                                                                                                                           |

3. URL, Görünen ad ve Açıklama alanlarının altında bulunan **Yanıt** sekmesini seçin.
4. **+ Yanıt ekle**’ye tıklayın.
5. Listeden **200 Tamam**’ı seçin.
6. Sağ taraftaki **Gösterimler** başlığının altında **+ Gösterim ekle**’yi seçin.
7. Arama kutusuna "*application/json*" yazın ve **application/json** içerik türünü seçin.
8. **Örnek** metin kutusuna `{ "sampleField" : "test" }` girin.
9. **Oluştur**’u seçin.

## <a name="enable-response-mocking"></a>Sahte yanıt vermeyi etkinleştirme

![Sahte yanıt vermeyi etkinleştirme](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. "Test API’si oluşturma" adımında oluşturduğunuz API’yi seçin.
2. Eklediğiniz test işlemini seçin.
3. Sağdaki pencerede **Tasarım** sekmesine tıklayın.
4. **Gelen işlem** penceresinde **+İlke ekle**'ye tıklayın.
5. Galeriden **Sahte yanıtlar** kutucuğunu seçin.

    ![Sahte yanıtlar ilke kutucuğu](./media/mock-api-responses/mock-responses-policy-tile.png)

6. **API Management yanıtı** metin kutusuna **200 OK, application/json** yazın. Bu seçim, API’nizin önceki bölümde tanımladığınız yanıt örneğini döndürmesi gerektiğini gösterir.

    ![Sahte yanıt vermeyi etkinleştirme](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. **Kaydet**’e tıklayın.

## <a name="test-the-mocked-api"></a>Sahte API’yi test etme

![Sahte API'yi test etme](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. "Test API’si oluşturma" adımında oluşturduğunuz API’yi seçin.
2. **Test** sekmesini açın.
3. **Test çağrısı** API’sinin seçili olduğundan emin olun.

    > [!TIP]
    > **Sahte işlem etkin** metnini içeren sarı çubuk, API Management’tan döndürülen yanıtların bir sahte işlem ilkesi gönderdiğini ve gerçek bir arka uç yanıtı göndermediğini gösterir.

4. Bir test çağrısı yapmak için **Gönder**’i seçin.
5. **HTTP yanıtı**, öğreticinin ilk bölümde örnek olarak sağlanan JSON’u görüntüler.

    ![Sahte yanıt vermeyi etkinleştirme](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Test API’si oluşturma
> * Test API’sine işlem ekleme
> * Sahte yanıt vermeyi etkinleştirme
> * Sahte API’yi test etme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
