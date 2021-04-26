---
title: 'Hızlı başlangıç: Python için form tanıyıcı istemci kitaplığı'
description: Özel belgelerinizden anahtar/değer çiftlerini ve tablo verilerini çıkaran bir form işleme uygulaması oluşturmak için Python için form tanıyıcı istemci Kitaplığı ' nı kullanın.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/09/2021
ms.author: lajanuar
ms.openlocfilehash: 606755333856f6dd97ab6c5158ac67f122a1237d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516469"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Bu makaledeki kod, basitlik nedenlerle zaman uyumlu Yöntemler ve güvenli olmayan kimlik bilgileri depolaması kullanır. Aşağıdaki başvuru belgelerine bakın.

[Başvuru belgeleri](/python/api/azure-ai-formrecognizer)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Paket (Pypı)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * Python yüklemeniz [PIP](https://pip.pypa.io/en/stable/)'yi içermelidir. Komut satırında komutunu çalıştırarak PIP 'nin yüklenip yüklenmediğini kontrol edebilirsiniz `pip --version` . Python 'un en son sürümünü yükleyerek PIP 'yi alın.
* Eğitim verileri kümesi içeren bir Azure Depolama Blobu. Eğitim veri kümesini birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../../build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **eğitme** klasörü altındaki dosyaları ( *sample_data.zip* indir ve Ayıkla) kullanabilirsiniz.
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" bir form tanıyıcı kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtarınızı ve uç noktanızı almak Için bir form tanıyıcı kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
  * Uygulamanızı form tanıyıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, form tanıyıcı istemci kitaplığı 'nın en son sürümünü ile yükleyebilirsiniz:

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> 3.1.0 B4 form tanıyıcı, en son SDK önizleme sürümüdür ve _API sürüm 2,1 Preview. 3 ' ü_ yansıtır.

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> Form tanıyıcı 3.0.0 SDK, API v 2.0 'ı yansıtır

---

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

Tercih ettiğiniz düzenleyicide veya IDE 'de yeni bir Python uygulaması oluşturun. Ardından aşağıdaki kitaplıkları içeri aktarın.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)'da bulabilirsiniz.

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Nesne modeli

Form tanıyıcı ile iki farklı istemci türü oluşturabilirsiniz. İlki, `form_recognizer_client` hizmeti form alanlarını ve içeriğini tanımak üzere sorgulamak için kullanılır. İkincisi, `form_training_client` tanımayı geliştirmek için kullanabileceğiniz özel modeller oluşturmak ve yönetmek için kullanılır. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` için işlem sağlar:

* Özel formlarınızı çözümlemek için eğitilen özel modeller kullanarak form alanlarını ve içeriği tanıma.
* Bir modeli eğitme gerekmeden tablolar, satırlar ve sözcükler dahil form içeriğini tanıma.
* Form tanıyıcı hizmetinde önceden eğitilen bir makbuz modeli kullanarak, genel alanları Makbuzlardan tanıyor.

### <a name="formtrainingclient"></a>Formtraıningclient

`form_training_client` için işlem sağlar:

* Özel modellerinizde bulunan tüm alanları ve değerleri çözümlemek için özel modelleri eğitme. Eğitim veri kümesi oluşturma hakkında daha ayrıntılı bir açıklama için, [etiketli model eğitiminde hizmetin belgelerine](#train-a-model-without-labels) bakın.
* Özel formlarınızı etiketleyerek belirttiğiniz belirli alanları ve değerleri çözümlemek için özel modeller eğitimi yapın. Eğitim veri kümesine etiket uygulama hakkında daha ayrıntılı bir açıklama için, [etiketli model eğitiminde hizmetin belgelerine](#train-a-model-with-labels) bakın.
* Hesabınızda oluşturulan modelleri yönetme.
* Özel bir modeli bir form tanıyıcı kaynağından diğerine kopyalama.

> [!NOTE]
> Modeller ayrıca [form tanıyıcı etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi kullanılarak eğitilmiş olabilir.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için form tanıyıcı istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Düzeni çözümle](#analyze-layout)
* [Alındıları analiz etme](#analyze-receipts)
* [İş kartlarını çözümle](#analyze-business-cards)
* [Faturaları analiz etme](#analyze-invoices)
* [Kimlik belgelerini analiz etme](#analyze-identity-documents)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel bir modeli eğitme](#train-a-custom-model)
* [Özel modellerinizi yönetin](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Düzeni çözümle](#analyze-layout)
* [Alındıları analiz etme](#analyze-receipts)
* [Özel bir modeli eğitme](#train-a-custom-model)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerinizi yönetin](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Burada, yukarıda tanımladığınız abonelik değişkenlerini kullanarak iki istemci nesnesinin kimlik doğrulamasını yapacaksınız. Gerektiğinde, API anahtarını yeni istemci nesneleri oluşturmadan güncelleştirebilmeniz için bir **AzureKeyCredential** nesnesi kullanacaksınız.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Test için varlıkları al

Eğitim ve test verileriniz için URL 'lere başvuru eklemeniz gerekir.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 'SI alımı":::

* Aşağıdaki örneklere dahil edilen örnek form ve alış görüntülerini kullanın ( [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) 'da da kullanılabilir) ve BLOB depolamada tek BIR BELGENIN SAS URL 'sini almak için yukarıdaki adımları kullanabilirsiniz. 

> [!NOTE]
> Bu kılavuzdaki kod parçacıkları, URL 'Ler tarafından erişilen uzak formları kullanır. Bunun yerine yerel form belgelerini işlemek istiyorsanız, [başvuru belgelerindeki](/python/api/azure-ai-formrecognizer) ilgili yöntemlere ve [örneklere](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)bakın.

## <a name="analyze-layout"></a>Düzeni çözümle

Belge içindeki tabloları, satırları ve sözcükleri, bir modeli eğmenize gerek kalmadan analiz etmek için form tanıyıcısı 'nı kullanabilirsiniz. Düzen ayıklama hakkında daha fazla bilgi için bkz. [Düzen kavramsal Kılavuzu](../../concept-layout.md).

Belirli bir URL 'deki bir dosyanın içeriğini çözümlemek için `begin_recognize_content_from_url` yöntemini kullanın. Döndürülen değer bir `FormPage` nesne koleksiyonudur: gönderilen belgedeki her sayfa için bir tane. Aşağıdaki kod bu nesneler boyunca yinelenir ve ayıklanan anahtar/değer çiftlerini ve tablo verilerini yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Ayrıca, yerel görüntülerden içerik alabilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın `begin_recognize_content` . Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-receipts"></a>Alındıları analiz etme

Bu bölümde, önceden eğitilen bir makbuz modeli kullanılarak ABD makbuzlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Makbuz analizi hakkında daha fazla bilgi için bkz. [alındılar kavramsal Kılavuzu](../../concept-receipts.md). Bir URL 'den alındıları çözümlemek için `begin_recognize_receipts_from_url` yöntemini kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Ayrıca, yerel alındı görüntülerini analiz edebilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın `begin_recognize_receipts` . Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>İş kartlarını çözümle

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, önceden eğitilen bir model kullanarak Ingilizce iş kartlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. İş kartı analizi hakkında daha fazla bilgi için bkz. [iş kartları kavramsal Kılavuzu](../../concept-business-cards.md). 

Bir URL 'den iş kartlarını çözümlemek için `begin_recognize_business_cards_from_url` yöntemini kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Ayrıca, yerel iş kartı görüntülerini çözümleyebilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın `begin_recognize_business_cards` . Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-invoices"></a>Faturaları analiz etme

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, önceden eğitilen bir model kullanılarak satış faturalarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Fatura analizi hakkında daha fazla bilgi için bkz. [Fatura kavramsal Kılavuzu](../../concept-invoices.md). 

Bir URL 'den faturaları çözümlemek için `begin_recognize_invoices_from_url` yöntemini kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Yerel fatura görüntülerini da analiz edebilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın `begin_recognize_invoices` . Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-identity-documents"></a>Kimlik belgelerini analiz etme

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, form tanıyıcı önceden oluşturulmuş KIMLIK modelini kullanarak devlet tarafından verilen tanımlama belgelerinden önemli bilgilerin nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Fatura analizi hakkında daha fazla bilgi için bkz. [önceden oluşturulmuş kimlik modelimiz kavramsal Kılavuzu](../../concept-identification-cards.md).

Bir URL 'den kimlik belgelerini çözümlemek için yöntemini kullanın `begin_recognize_id_documents_from_url` .

:::code language="python" source="~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py" id="snippet_id":::

> [!TIP]
> Ayrıca, kimlik belge görüntülerini çözümleyebilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true#methods) yöntemlerine _bakın_ `begin_recognize_id_documents` . _Ayrıca_ yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="train-a-custom-model"></a>Özel bir modeli eğitme

Bu bölümde, bir modelin kendi verilerinize nasıl eğulacağı gösterilmektedir. Eğitilen bir model, özgün form belgesindeki anahtar/değer ilişkilerini içeren yapılandırılmış verileri çıktısını alabilir. Modeli eğdikten sonra, bunu test edebilir ve yeniden eğitebilir ve sonunda, gereksinimlerinize göre daha fazla formdan verileri güvenilir bir şekilde çıkarmak için kullanabilirsiniz.

> [!NOTE]
> Modelleri, [form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi ile de eğitebilirsiniz.

### <a name="train-a-model-without-labels"></a>Etiketler olmadan bir modeli eğitme

Eğitim belgelerini el ile etiketlemeden özel formlarınızda bulunan tüm alanları ve değerleri çözümlemek için özel modeller eğitme.

Aşağıdaki kod, `begin_training` belirli bir belge kümesi üzerinde bir modeli eğitme işleviyle birlikte eğitim istemcisini kullanır. Döndürülen `CustomFormModel` nesne, modelin çözümleyebileceğiniz form türleri ve her form türünden ayıklayabileceğiniz alanlar hakkındaki bilgileri içerir. Aşağıdaki kod bloğu bu bilgileri konsola yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>Çıktı

Bu, [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)'da bulunan eğitim verileriyle eğitilen bir modelin çıktıdır.

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Etiketler içeren bir modeli eğitme

Ayrıca, eğitim belgelerini el ile etiketleyerek özel modeller de eğitebilirsiniz. Etiketlerle eğitim, bazı senaryolarda daha iyi performansa yol açar. Döndürülen, `CustomFormModel` modelin ayıklayabileceğiniz alanları, her bir alandaki tahmini doğruluğunu gösterir. Aşağıdaki kod bloğu bu bilgileri konsola yazdırır.

> [!IMPORTANT]
> Etiketlerle eğitebilmeniz için, `\<filename\>.pdf.labels.json` eğitim belgelerinin yanı sıra BLOB depolama kapsayıcıda özel etiket bilgi dosyalarına () sahip olmanız gerekir. [Form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md) , bu etiket dosyalarını oluşturmanıza yardımcı olmak için bir kullanıcı arabirimi sağlar. Bunu yaptıktan sonra `begin_training` işlevi, *use_training_labels* parametresi olarak ayarlanmış şekilde çağırabilirsiniz `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Çıktı

Bu, [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)'da bulunan eğitim verileriyle eğitilen bir modelin çıktıdır.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Formları özel bir model ile analiz etme

Bu bölümde, kendi formlarınız ile eğitilen modeller kullanılarak özel form türlerinizde anahtar/değer bilgilerinin ve diğer içeriklerin nasıl ayıklanacağı gösterilmektedir.

> [!IMPORTANT]
> Bu senaryoyu uygulamak için, KIMLIĞINI aşağıdaki yönteme geçirebilmeniz için zaten bir model eğitilmeniz gerekir. [Modeli eğitme](#train-a-model-without-labels) bölümüne bakın.

`begin_recognize_custom_forms_from_url`Yöntemini kullanacaksınız. Döndürülen değer bir `RecognizedForm` nesne koleksiyonudur: gönderilen belgedeki her sayfa için bir tane. Aşağıdaki kod, analiz sonuçlarını konsola yazdırır. Her tanınan alanı ve karşılık gelen değeri, Güvenirlik puanı ile birlikte yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Yerel görüntüleri de analiz edebilirsiniz. Gibi [Formrecognizerclient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın `begin_recognize_custom_forms` . Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

Önceki örnekteki modeli kullanarak aşağıdaki çıktı sağlanır.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```


## <a name="manage-your-custom-models"></a>Özel modellerinizi yönetin

Bu bölümde, hesabınızda depolanan özel modellerin nasıl yönetileceği gösterilmektedir.

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Formtanıyıcı kaynak hesabındaki modellerin sayısını denetleyin

Aşağıdaki kod bloğu, form tanıyıcı hesabınıza kaç modelin kaydedildiğini denetler ve hesap limitine göre karşılaştırır.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Çıktı

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Şu anda kaynak hesapta depolanan modelleri listeleyin

Aşağıdaki kod bloğu, hesabınızdaki geçerli modelleri listeler ve ayrıntılarını konsola yazdırır. Ayrıca ilk modele bir başvuru kaydeder.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Çıktı

Bu, sınama hesabı için bir örnek çıktıdır.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Modelin KIMLIĞINI kullanarak belirli bir modeli al

Aşağıdaki kod bloğu, önceki bölümden kaydedilen model KIMLIĞINI kullanır ve model hakkındaki ayrıntıları almak için onu kullanır.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Çıktı

Bu, önceki örnekte oluşturulan özel modelin örnek çıktıdır.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Kaynak hesabındaki bir modeli silme

Ayrıca, KIMLIĞINE başvurarak hesabınızdan bir modeli silebilirsiniz. Bu kod, önceki bölümde kullanılan modeli siler.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `python` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Sorun giderme

### <a name="general"></a>Genel

Form tanıyıcı istemci kitaplığı, [Azure Core](https://aka.ms/azsdk-python-azure-core)'da tanımlanan özel durumları yükseltir.

### <a name="logging"></a>Günlüğe Kaydetme

Bu kitaplık günlüğe kaydetmek için [Standart günlük kitaplığını](https://docs.python.org/3/library/logging.html) kullanır. HTTP oturumları (URL 'Ler, üstbilgiler vb.) hakkındaki temel bilgiler BILGI düzeyinde günlüğe kaydedilir.

İstek/yanıt gövdeleri ve redaksiyonu kaldırma üst bilgileri de dahil olmak üzere ayrıntılı hata ayıklama düzeyinde günlüğe kaydetme, `logging_enable` anahtar sözcük bağımsız değişkeni olan bir istemcide etkinleştirilebilir:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Benzer şekilde, `logging_enable` istemci için etkinleştirilmediğinde bile tek bir işlem için ayrıntılı günlük kaydını etkinleştirebilir:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, modelleri eğitmek ve formları farklı yollarla analiz etmek için tanıyıcı Python istemci kitaplığı formunu kullandınız. Daha sonra, daha iyi eğitim veri kümesi oluşturma ve daha doğru modeller üretme hakkında ipuçları edinin.

> [!div class="nextstepaction"]
> [Eğitim verileri kümesi oluşturma](../../build-training-data-set.md)

* [Form Tanıma nedir?](../../overview.md)
* Bu kılavuzun örnek kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)' da bulunabilir.
