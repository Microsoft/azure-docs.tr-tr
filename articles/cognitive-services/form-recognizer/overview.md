---
title: Form Tanıma nedir?
titleSuffix: Azure Cognitive Services
description: Azure form tanıyıcı hizmeti, form belgelerinizden anahtar/değer çiftlerini ve tablo verilerini tanımlamanızı ve ayıklamanızı, Ayrıca satış alındıları ve iş kartlarından önemli bilgileri ayıklamanızı sağlar.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Otomatik veri işleme, belge işleme, otomatik veri girişi, form işleme
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518238"
---
# <a name="what-is-form-recognizer"></a>Form Tanıma nedir?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure form tanıyıcı, makine öğrenimi teknolojisini kullanarak otomatik veri işleme yazılımı oluşturmanıza imkan tanıyan bir bilişsel hizmettir. Belgelerinizden metin, anahtar/değer çiftleri, seçim işaretleri, tablolar ve yapıyı belirleyip ayıklayın &mdash; . hizmet, özgün dosyadaki ilişkileri, sınırlayıcı kutuları, güvenilirliği ve daha fazlasını içeren yapılandırılmış verileri çıktı olarak verir. El ile müdahale veya kapsamlı veri bilimi uzmanlığı olmadan, belirli içeriğinize kolayca doğru sonuçlar elde edersiniz. Uygulamalarınızda veri girişini otomatikleştirmek ve belge arama olanaklarınızı zenginleştirmek için form tanıyıcısı 'nı kullanın.

Form tanıyıcı özel belge işleme modellerinden, faturalar, alındılar, kimlikler ve iş kartları ve düzen modeli için önceden oluşturulmuş modellerden oluşur. Karmaşıklığı azaltmak ve iş akışınız veya uygulamanızla bütünleştirmek için bir REST API veya istemci kitaplığı SDK 'Ları kullanarak form tanıyıcı modellerini çağırabilirsiniz.

Bu belge aşağıdaki makale türlerini içerir:  

* [**Hızlı**](quickstarts/client-library.md) başlangıçlarda, hizmette istek yapma konusunda size kılavuzluk eden başlangıç yönergeleri bulunur.  
* [**Nasıl yapılır kılavuzlarında**](build-training-data-set.md) , hizmetin daha belirli veya özelleştirilmiş yollarla kullanılmasına ilişkin yönergeler bulunur.  
* [**Kavramlar**](concept-layout.md) , hizmet işlevselliği ve özelliklerinin ayrıntılı açıklamalarını sağlar.  
* [**Öğreticiler**](tutorial-bulk-processing.md) daha fazla iş çözümlerinde hizmeti bir bileşen olarak nasıl kullanacağınızı gösteren kılavuzlardır.  

## <a name="form-recognizer-features"></a>Form tanıyıcı özellikleri

Form tanıyıcı ile, bu özelliklerle form verilerini kolayca ayıklayabilir ve analiz edebilirsiniz:

* **[Düzen API 'si](#layout-api)** -metin, seçim işaretleri ve tablo yapılarını, belgelerden sınırlayıcı kutu koordinatlarıyla birlikte ayıklayın.
* **[Özel modeller](#custom-models)** -metin, anahtar/değer çiftleri, seçim işaretleri ve formlardan tablo verilerini ayıklar. Bu modeller kendi verileriniz ile eğitilmiş olduğundan, formlarınıza göre uyarlanmıştır.

* **[Önceden oluşturulmuş modeller](#prebuilt-models)** -önceden oluşturulmuş modeller kullanarak benzersiz belge türlerinden verileri ayıklayın. Şu anda aşağıdaki önceden oluşturulmuş modeller mevcuttur

  * [Faturalar](./concept-invoices.md)
  * [Satış alındıları](./concept-receipts.md)
  * [Kartvizitler](./concept-business-cards.md)
  * [Kimlik (ID) kartları](./concept-identification-cards.md)


## <a name="get-started"></a>başlarken

Düzen, önceden oluşturulmuş modeller denemek ve belgeleriniz için özel bir model geliştirmek üzere örnek form tanıyıcı aracını kullanın. Form tanıyıcı hizmetini denemek için bir Azure aboneliğine ([**ücretsiz olarak bir tane oluşturun**](https://azure.microsoft.com/free/cognitive-services)) ve bir [**form tanıyıcı kaynak**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) uç noktası ve anahtarına ihtiyacınız olacaktır.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

> [!div class="nextstepaction"]
> [Form tanıyıcıyı deneyin](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Form tanıyıcıyı deneyin](https://fott.azurewebsites.net/)

---
Belgelerinizden veri çıkarmaya başlamak için [istemci kitaplığı/REST API hızlı](./quickstarts/client-library.md) başlangıcı ' nı izleyin. Teknolojiyi öğrenirken ücretsiz hizmeti kullanmanızı öneririz. Ücretsiz sayfa sayısının ayda 500 ile sınırlı olduğunu unutmayın.

Kullanmaya başlamak için REST örneklerini (GitHub) de kullanabilirsiniz. 

* Belgelerden metin, seçim işaretleri ve tablo yapısını Ayıkla
  * [Düzen verilerini ayıklama-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Özel modeller eğitme ve form verilerini ayıklama
  * [Etiketler olmadan eğitme-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Etiketlerle eğitme-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Faturalardan verileri Ayıkla
  * [Fatura verilerini Ayıkla-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Satış makbuzlarından veri ayıklama
  * [Alma verilerini Ayıkla-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Kartvizitlerden veri ayıklama
  * [İş kartı verilerini Ayıkla-Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>REST API 'Leri gözden geçirme

Modelleri eğitmek ve formlardan yapılandırılmış verileri ayıklamak için aşağıdaki API 'Leri kullanacaksınız.

|Ad |Açıklama |
|---|---|
| **Düzeni çözümle** | Belgedeki metin, seçim işaretleri, tablolar ve yapıyı ayıklamak için akış olarak geçirilmiş bir belgeyi çözümleme |
| **Özel modeli eğitme**| Formlarınızı aynı türden beş form kullanarak analiz etmek için yeni bir model eğitme. _Uselabelfile_ parametresini, `true` el ile etiketlenmiş verileri eğit olacak şekilde ayarlayın. |
| **Formu çözümle** |Metin, anahtar/değer çiftleri ve tablolardaki tabloları özel modelinize çıkarmak için akış olarak geçirilmiş bir formu analiz edin.  |
| **Faturayı çözümle** | Ana bilgileri, tabloları ve diğer fatura metinlerini ayıklamak için bir faturayı çözümleyin.|
| **Okundu bilgisi** | Anahtar bilgilerini ve diğer makbuz metnini ayıklamak için bir makbuz belgesi çözümleyin.|
| **Analiz KIMLIĞI** | Anahtar bilgilerini ve diğer tanımlama kartı metnini ayıklamak için bir KIMLIK kartı belgesini çözümleyin.|
| **Iş kartını çözümle** | Anahtar bilgileri ve metin ayıklamak için bir iş kartını analiz edin.|

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

Daha fazla bilgi edinmek için [REST API başvuru belgelerini](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) inceleyin. API 'nin önceki bir sürümüne alışkın değilseniz, son değişiklikler hakkında bilgi edinmek için [Yenilikler](./whats-new.md) makalesine bakın.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Daha fazla bilgi edinmek için [REST API başvuru belgelerini](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) inceleyin. API 'nin önceki bir sürümüne alışkın değilseniz, son değişiklikler hakkında bilgi edinmek için [Yenilikler](./whats-new.md) makalesine bakın.

---

## <a name="layout-api"></a>Düzen API 'SI

Form tanıyıcı, yüksek tanımlı optik karakter tanıma (OCR) ve belgelerden gelişmiş bir derin öğrenme modeli kullanarak metin, seçim işaretlerini ve tablo yapısını (metinle ilişkili satır ve sütun numaralarını) ayıklayabilir. Daha fazla bilgi için bkz. [Düzen](./concept-layout.md) kavramsal Kılavuzu.

:::image type="content" source="./media/tables-example.jpg" alt-text="tablolar örneği" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Özel modeller

Form tanıyıcı özel modelleri kendi verilerinize eğitme ve başlamak için yalnızca beş örnek giriş formunuz olması gerekir. Eğitilen bir belge işleme modeli, özgün form belgesindeki ilişkileri içeren yapılandırılmış verileri çıktısını alabilir. Modeli eğdikten sonra, bunu test edebilir ve yeniden eğitebilir ve sonunda, gereksinimlerinize göre daha fazla formdan verileri güvenilir bir şekilde çıkarmak için kullanabilirsiniz.

Özel modeller eğlendirmede aşağıdaki seçenekleriniz vardır: etiketli verilerle ve etiketli veriler olmadan eğitim.

### <a name="train-without-labels"></a>Etiketler olmadan eğitme

Form tanıyıcı, formlarınızın alanları ile girişleri arasındaki düzeni ve ilişkileri anlamak için denetimsiz öğrenme kullanır. Giriş formlarınızı gönderdiğinizde, algoritma formları türe göre kümeler, hangi anahtarların ve tabloların mevcut olduğunu bulur ve değerleri anahtarlar ve girdilerle tablolarla ilişkilendirir. Etiketleri olmayan eğitim, el ile veri etiketleme veya yoğun kodlama ve bakım gerektirmez ve bu yöntemi önce denemeniz önerilir.

Eğitim belgelerinizi nasıl toplayacağınız hakkında ipuçları için bkz. [eğitim verileri kümesi oluşturma](./build-training-data-set.md) .

### <a name="train-with-labels"></a>Etiketlerle eğitme

Etiketli verilerle eğitedığınızda, model, sağladığınız etiketli formları kullanarak ilgilendiğiniz değerleri ayıklamak için denetimli öğrenimi kullanır. Etiketlendirilmiş veriler daha iyi performanslı modellerle sonuçlanır ve anahtarlar olmadan karmaşık formlarla veya formlarla çalışan modeller oluşturabilir.

Form tanıyıcı, yazdırılan ve el yazısı metin öğelerinin beklenen boyutlarını ve konumlarını öğrenmek ve tabloları ayıklamak için [Düzen API](#layout-api) 'sini kullanır. Ardından, belgelerde anahtar/değer ilişkilerini ve tabloları öğrenmek için Kullanıcı tarafından belirtilen etiketleri kullanır. Yeni bir modeli eğitmek ve model doğruluğunu artırmak için gerektiğinde daha fazla etiketli veri eklemek için aynı türde (aynı yapıyla) beş el ile etiketlenmiş beş form kullanmanızı öneririz. Form tanıyıcı, bir modelin denetimli öğrenme yeteneklerini kullanarak anahtar değer çiftlerini ve tabloları ayıklamasını sağlar. 

[Etiketlerle eğitme ile çalışmaya başlama](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Önceden oluşturulmuş modeller

Form tanıyıcı ayrıca benzersiz form türlerinin otomatik veri işlemeye yönelik önceden oluşturulmuş modeller de içerir.

### <a name="prebuilt-invoice-model"></a>Önceden oluşturulmuş fatura modeli

Önceden oluşturulmuş fatura modeli, verileri çeşitli biçimlerdeki faturalardan ayıklar ve yapılandırılmış verileri döndürür. Bu model, fatura KIMLIĞI, müşteri ayrıntıları, satıcı ayrıntıları, sevk yeri, fatura, toplam, vergi, alt toplam, satır öğeleri ve daha fazlası gibi önemli bilgileri ayıklar. Ayrıca, önceden oluşturulmuş fatura modeli, faturadaki tüm metin ve tabloları çözümlemek ve döndürmek için eğitilmiş olur. Daha fazla bilgi için [faturalara](./concept-invoices.md) kavramsal kılavuza bakın.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="örnek fatura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Önceden oluşturulmuş makbuz modeli

Önceden oluşturulmuş makbuz modeli, Avustralya, Kanada, Büyük Britanya, Hindistan ve &mdash; Restoranlar, gaz istasyonları, perakende vb. tarafından kullanılan türden Birleşik Devletler İngilizce satış alındılarını okumak için kullanılır. Bu model, işlemin saati ve tarihi, ticari bilgiler, vergiler, satır öğeleri, toplamlar ve daha fazlası gibi önemli bilgileri ayıklar. Ayrıca, önceden oluşturulmuş makbuz modeli, bir Makbuzdaki tüm metni çözümlemek ve döndürmek için eğitilmiş olur. Daha fazla bilgi için bkz. [alındılar](./concept-receipts.md) kavramsal Kılavuzu.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="örnek alındısı" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Önceden oluşturulmuş kimlik (ID) kartları modeli

Kimlik (ID) kartları modeli, dünya genelinde Passport ve ABD sürücü lisanslarından önemli bilgileri ayıklamanızı sağlar. Belge KIMLIĞI, Doğum süresi, Doğum tarihi, süre sonu, ad, ülke, bölge, makine tarafından okunabilen bölge ve daha fazlası gibi verileri ayıklar. Daha fazla bilgi için bkz. [tanımlama (kimlik) kartlar](./concept-identification-cards.md) kavramsal Kılavuzu.

:::image type="content" source="./media/overview-id.jpg" alt-text="örnek tanımlama kartı" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Önceden oluşturulmuş Iş kartları modeli

Iş kartları modeli, kişinin adı, iş unvanı, adres, e-posta, şirket ve telefon numarası gibi bilgileri, iş kartlarından Ingilizce olarak ayıklamanızı sağlar. Daha fazla bilgi için bkz. [iş kartları](./concept-business-cards.md) kavramsal Kılavuzu.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="örnek iş kartı" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Giriş gereksinimleri

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker kapsayıcılarını kullanarak şirket içinde dağıtma

Şirket içinde API özelliklerini dağıtmak için [form tanıyıcı kapsayıcıları (Önizleme) kullanın](form-recognizer-container-howto.md) . Bu Docker kapsayıcısı, uyumluluk, güvenlik veya diğer işletimsel nedenlerle hizmeti verilerinize yaklaştırmanızı sağlar.

## <a name="service-availability-and-redundancy"></a>Hizmet kullanılabilirliği ve artıklığı

### <a name="is-form-recognizer-service-zone-resilient"></a>Form tanıyıcı hizmeti bölgesi-dayanıklı mı?

Evet. Form tanıyıcı hizmeti varsayılan olarak bölge esnektir.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Form tanıyıcı hizmetini bölge-dayanıklı olarak yapılandırmak Nasıl yaparım??

Bölge dayanıklılığı sağlamak için hiçbir müşteri yapılandırması gerekmez. Form tanıyıcı kaynakları için bölge esnekliği, varsayılan olarak kullanılabilir ve hizmet tarafından yönetilir.

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Tüm bilişsel hizmetlerde olduğu gibi, form tanıyıcı hizmetini kullanan geliştiriciler müşteri verilerinde Microsoft ilkeleriyle uyumlu olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Form tanıyıcı hizmeti hakkında daha fazla bilgi edinmek için çevrimiçi aracımızı ve hızlı başlangıcı deneyin.

* [**Form tanıyıcı aracı**](https://fott-preview.azurewebsites.net/)
* [**İstemci kitaplığı ve REST API hızlı başlangıç**](quickstarts/client-library.md)
