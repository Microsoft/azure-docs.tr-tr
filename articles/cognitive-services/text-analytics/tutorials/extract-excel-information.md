---
title: Metin Analizi ve güç otomatikleştirme kullanarak Excel 'de bilgi Ayıkla
titleSuffix: Azure Cognitive Services
description: Metin Analizi ve güç otomatikleştirme kullanarak Excel metnini kod yazmak zorunda kalmadan nasıl ayıklayacağınızı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93359910"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Metin Analizi ve güç otomatikleştirme kullanarak Excel 'de bilgi Ayıkla 

Bu öğreticide, kod yazmak zorunda kalmadan bir Excel elektronik tablosunda metin ayıklamak için Power otomatikleştiren bir akış oluşturacaksınız. 

Bu akış, bir grup karmaşık hakkında raporlanan sorunların bir elektronik tablosunu alır ve bunları iki kategoride sınıflandırır: sıhhi tesisat ve diğer. Ayrıca, onları gönderen kiracıların adlarını ve telefon numaralarını da ayıklar. Son olarak, akış bu bilgileri Excel sayfasına ekler. 

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Akış oluşturmak için güç otomatikleştirmesini kullanma
> * OneDrive Iş 'ten Excel verilerini karşıya yükleme
> * Excel 'den metin Ayıkla ve Metin Analizi API'si gönderin 
> * Bir Excel sayfasını güncelleştirmek için API 'deki bilgileri kullanın.

## <a name="prerequisites"></a>Önkoşullar

- Bir Microsoft Azure hesabı. [Ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/cognitive-services/) veya [oturum açın](https://portal.azure.com/).
- Bir Metin Analizi kaynağı. Bir tane yoksa, [Azure Portal bir tane oluşturabilir](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) ve ücretsiz katmanı kullanarak bu öğreticiyi tamamlayabilirsiniz.
- Kaydolma sırasında sizin için oluşturulan [anahtar ve uç nokta](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .
- Kiracı sorunlarını içeren bir elektronik tablo. GitHub 'da örnek veriler verilmiştir
- OneDrive iş ile Microsoft 365.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Excel dosyasını OneDrive Iş 'e ekleyin

Örnek Excel dosyasını [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)'dan indirin. Bu dosya OneDrive Iş hesabınızda depolanmalıdır.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel dosyasından örnekler.":::

Sorunlar ham metinde raporlanır. Kişinin adını ve telefon numarasını ayıklamak için Metin Analizi API'si adlı varlık tanımayı kullanacağız. Ardından akışta, sorunları kategorilere ayırmak için açıklamada "sıhhi tesisat" sözcüğü görünür. 

## <a name="create-a-new-power-automate-workflow"></a>Yeni bir Power Otomasyonu iş akışı oluşturma

[Power otomatikleştir sitesine](https://preview.flow.microsoft.com/)gidin ve oturum açın. Ardından **Oluştur** ve **zamanlanan akış** öğesine tıklayın.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Akış oluşturma ekranı.":::


**Zamanlanan akış oluştur** sayfasında, akışınızı aşağıdaki alanlarla başlatın:

|Alan |Değer  |
|---------|---------|
|**Akış adı**     | **Zamanlanan gözden geçirme** veya başka bir ad.         |
|**Başlatılıyor**     |  Geçerli tarih ve saati girin.       |
|**Yineleme sıklığı**     | **1 saat**        |

## <a name="add-variables-to-the-flow"></a>Akışa değişken ekleme

> [!NOTE]
> Tamamlanan akışın bir görüntüsünü görmek isterseniz, bunu [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)' dan indirebilirsiniz. 

Excel dosyasına eklenecek bilgileri temsil eden değişkenler oluşturun. **Yeni adım** ' a tıklayıp **başlatma değişkeni** için arama yapın. Dört değişken oluşturmak için bunu dört kez yapın.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Değişkenleri başlatın.":::

Aşağıdaki bilgileri oluşturduğunuz değişkenlere ekleyin. Excel dosyasının sütunlarını temsil eder. Herhangi bir değişken daraltılamışsa, bunları genişletmek için bunlara tıklayabilirsiniz.

| Eylem |Ad   | Tür | Değer |
|---------|---------|---|---|
| Değişkeni Başlat | var_person | Dize | Kişi |
| Değişken 2 ' i Başlat | var_phone | Dize | Phone_Number |
| Değişkeni Başlat 3 | var_plumbing | Dize | Sıhhi |
| Başlangıç değişkeni 4 | var_other | Dize | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="akış değişkenlerinde bulunan bilgiler":::

## <a name="read-the-excel-file"></a>Excel dosyasını okuyun

**Yeni adım** ' a tıklayın ve **Excel** yazıp Eylemler listesinden **bir tabloda bulunan liste satırları** ' nı seçin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Excel satırları ekleyin.":::

Bu eylemde bulunan alanları doldurarak Excel dosyasını akışa ekleyin. Bu öğreticide, dosyanın OneDrive Iş 'e yüklenmiş olması gerekir.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Excel satırlarını doldur":::

**Yeni adım** ' a tıklayın ve **her** eyleme bir Uygula ekleyin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Uygula komutu ekleyin.":::

**Önceki adımdan bir çıktı Seç**' e tıklayın. Görüntülenen dinamik içerik kutusunda **değer**' i seçin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel dosyasından çıkış ' ı seçin.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Metin Analizi API'si istek gönder

Henüz yapmadıysanız, Azure portal bir [metin analizi kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) oluşturmanız gerekir.

### <a name="create-a-text-analytics-connection"></a>Metin Analizi bağlantısı oluşturma

**Her birine Uygula**' da **Eylem Ekle**' ye tıklayın. Azure portal Metin Analizi kaynağınızın **anahtar ve uç nokta** sayfasına gidin ve metin analizi kaynağınızın anahtarını ve uç noktasını alın.

Flow 'da yeni bir Metin Analizi bağlantısı oluşturmak için aşağıdaki bilgileri girin.

> [!NOTE]
> Zaten bir Metin Analizi bağlantısı oluşturduysanız ve bağlantı ayrıntılarınızı değiştirmek istiyorsanız sağ üst köşedeki üç noktaya tıklayın ve **+ Yeni bağlantı ekle**' ye tıklayın.

| Alan           | Değer                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Bağlantı Adı | Metin Analizi kaynağınızın bağlantısı için bir ad. Örneğin, `TAforPowerAutomate`. |
| Hesap anahtarı     | Metin Analizi kaynağınız için anahtar.                                                                                   |
| Site URL 'SI        | Metin Analizi kaynağınız için uç nokta.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin.":::

## <a name="extract-the-excel-content"></a>Excel içeriğini Ayıkla 

Bağlantı oluşturulduktan sonra, **metin analizi** arayın ve **varlıklar**' ı seçin. Bu, sorunun açıklama sütunundan bilgi ayıklar.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Metin Analizi varlıkları ekleyin.":::

**Metin** alanına tıklayın ve görüntülenen dinamik Içerik penceresinden **Açıklama** ' yı seçin. `en`Dil için girin. (Dil görmüyorsanız Gelişmiş seçenekleri göster ' e tıklayın)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Metin Analizi ayarlarını ekleyin.":::


## <a name="extract-the-person-name"></a>Kişi adını Ayıkla

Sonra, Metin Analizi çıktısında kişi varlık türünü bulacağız. **Her birine Uygula** içinde, **Eylem Ekle**' ye tıklayın ve **her eylem için** başka bir uygulama oluşturun. Metin kutusunun içine tıklayın ve görüntülenen dinamik Içerik penceresinde **varlıklar** ' ı seçin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. iki":::

Yeni oluşturulan **her 2 eylemi için** , **Eylem Ekle**' ye tıklayın ve bir **koşul** denetimi ekleyin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. 03":::

Koşul penceresinde, ilk metin kutusuna tıklayın. Dinamik içerik penceresinde **varlık türü** araması yapın ve seçin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. 4":::

İkinci kutunun **değerine eşit** olduğundan emin olun. Ardından üçüncü kutuyu seçin ve `var_person` dinamik içerik penceresinde arama yapın. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. e":::

**Evet ise** , Excel 'de yazın ve ardından **satırı Güncelleştir**' i seçin.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. inç":::

Excel bilgilerini girin ve **anahtar sütununu**, **anahtar değerini** ve **PersonName** alanlarını güncelleştirin. Bu, API tarafından algılanan adı Excel sayfasına ekler. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. 7@@":::

## <a name="get-the-phone-number"></a>Telefon numarasını al

Ada tıklayarak **her 2 Için Uygula** eylemini en aza indirin. Ardından, daha önce olduğu gibi her eyleme başka bir **uygulama** ekleyin. Bu, **her 3 Için uygulanacak** olarak adlandırılır. Metin kutusunu seçin ve bu eylem için çıkış olarak **varlık** ekleyin. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. 240":::

**Her 3 Için geçerlidir** Içinde bir **koşul** denetimi ekleyin. **Koşul 2** olarak adlandırılır. İlk metin kutusunda, dinamik içerik penceresinden **varlık türünü** arayın ve ekleyin. Center kutusunun **değerine eşit** olduğundan emin olun. Ardından, doğru metin kutusuna girin `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. tuşlarına":::

**Evet ise** , **satır Güncelleştir** eylemini ekleyin. Daha sonra, Excel sayfasının telefon numaraları sütunu için yukarıda yaptığımız gibi bilgileri girin. Bu, API tarafından algılanan telefon numarasını Excel sayfasına ekler. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. (":::


## <a name="get-the-plumbing-issues"></a>Sıhhi tesisat sorunlarını alın

Ada tıklayarak **her 3 Için uygulanan uygulamayı** en aza indirin. Ardından, üst eylemde **her biri için** başka bir uygulama oluşturun. Metin kutusunu seçin ve dinamik içerik penceresinden bu eyleme çıkış olarak **varlık** ekleyin. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. üst":::


Daha sonra Flow, Excel tablo satırındaki sorun açıklamasının "sıhhi tesisat" sözcüğünü içerip içermesinin olup olmadığını kontrol eder. Yanıt Evet ise, IssueType sütununa "sıhhi tesisat" ekler. Aksi takdirde "Other" olarak girilecek.

**Her 4 Için Uygula** eyleminin Içinde bir **koşul** denetimi ekleyin. **Koşul 3** olarak adlandırılır. İlk metin kutusunda, dinamik içerik penceresini kullanarak Excel dosyasından açıklama ekleyin ve **Açıklama** ekleyin. Ortadaki kutunun **içerdiğinden** emin olun. Sonra sağ metin kutusunda bulun ve seçin `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. +":::


**Evet ise** , **Eylem Ekle**' ye tıklayın ve **bir satırı Güncelleştir**' i seçin. Daha sonra gibi bilgileri girin. IssueType sütununda, öğesini seçin `var_plumbing` . Bu, satıra bir "sıhhi tesisat" etiketi uygular.

**Koşul yoksa,** **Eylem Ekle**' ye tıklayın ve **bir satırı Güncelleştir**' i seçin. Daha sonra gibi bilgileri girin. IssueType sütununda, öğesini seçin `var_other` . Bu, satıra "diğer" etiketini uygular.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Akışlarınızın Metin Analizi kimlik bilgilerini ekleyin. hatası":::

## <a name="test-the-workflow"></a>İş akışını test etme

Ekranın sağ üst köşesinde **Kaydet**' e ve ardından **Sına**' ya tıklayın. **Tetikleme eylemini yapacağım ' ı** seçin. **& testi kaydet**' e tıklayın, **akışı çalıştırın**, sonra **bitti**.

Excel dosyası OneDrive hesabınızda güncelleştirilir. Aşağıdaki gibi görünür.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Güncelleştirilmiş Excel elektronik tablosu.":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Diğer çözümleri keşfet](../text-analytics-user-scenarios.md)
