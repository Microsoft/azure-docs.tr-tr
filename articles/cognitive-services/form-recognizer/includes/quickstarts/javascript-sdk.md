---
title: 'Hızlı başlangıç: JavaScript için form tanıyıcı istemci kitaplığı'
description: Özel belgelerinizden anahtar/değer çiftlerini ve tablo verilerini çıkaran bir form işleme uygulaması oluşturmak için JavaScript için form tanıyıcı istemci Kitaplığı ' nı kullanın.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: feff8b003428fd61fba826d05f8212fa8d9788f9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601929"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Bu makaledeki kod, basitlik nedenlerle zaman uyumlu Yöntemler ve güvenli olmayan kimlik bilgileri depolaması kullanır. Aşağıdaki başvuru belgelerine bakın.

[Başvuru belgeleri](../../index.yml)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Node.js](https://nodejs.org/) geçerli sürümü
* Eğitim verileri kümesi içeren bir Azure Depolama Blobu. Eğitim veri kümesini birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../../build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **eğitme** klasörü altındaki dosyaları ( *sample_data.zip* indir ve Ayıkla) kullanabilirsiniz.
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" bir form tanıyıcı kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtarınızı ve uç noktanızı almak Için bir form tanıyıcı kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git**' i seçin.
  * Uygulamanızı form tanıyıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin.

```console
mkdir myapp && cd myapp
```

`npm init`Bir dosya ile bir düğüm uygulaması oluşturmak için komutunu çalıştırın `package.json` .

```console
npm init
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

`ai-form-recognizer`NPM paketini yüklerken:

```console
npm install @azure/ai-form-recognizer
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.

Adlı bir dosya oluşturun `index.js` , açın ve aşağıdaki kitaplıkları içeri aktarın:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)'da bulabilirsiniz.

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure portala gidin. **Önkoşul** bölümünde oluşturduğunuz form tanıyıcı kaynağı başarıyla dağıtıldı, **sonraki adımlar** altındaki **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz.
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

## <a name="object-model"></a>Nesne modeli

Form tanıyıcı ile iki farklı istemci türü oluşturabilirsiniz. Birincisi, `FormRecognizerClient` hizmeti tanınan form alanları ve içerikleri için sorgulamak üzere kullanılır. İkincisi, `FormTrainingClient` tanımayı geliştirmek için kullanabileceğiniz özel modeller oluşturmak ve yönetmek için kullanılır.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` için işlem sağlar:

* Özel formlarınızı çözümlemek için eğitilen özel modeller kullanarak form alanlarını ve içeriği tanıma. Bu değerler bir nesne koleksiyonunda döndürülür `RecognizedForm` .
* Bir modeli eğitme gerekmeden tablolar, satırlar ve sözcükler dahil form içeriğini tanıma. Form içeriği bir nesne koleksiyonunda döndürülür `FormPage` .
* Form tanıyıcı hizmetinde önceden eğitilen bir model kullanarak ABD giriş, iş kartı, fatura ve kimlik belgelerinin ortak alanlarını tanıyor.

### <a name="formtrainingclient"></a>Formtraıningclient

`FormTrainingClient` için işlem sağlar:

* Özel modellerinizde bulunan tüm alanları ve değerleri çözümlemek için özel modelleri eğitme. `CustomFormModel`Modelin analiz edileceği form türlerini ve her form türü için ayıklanacak alanları gösteren bir döndürülür. Daha fazla ayrıntı için, [etiketli model eğitiminde hizmetin belgelerine](#train-a-model-without-labels) _bakın_ .
* Özel formlarınızı etiketleyerek belirttiğiniz belirli alanları ve değerleri çözümlemek için özel modeller eğitimi yapın. , `CustomFormModel` Modelin ayıklanacağı alanları ve her bir alan için tahmini doğruluğu belirten bir döndürülür. Eğitim veri kümesine etiket uygulama hakkında daha ayrıntılı bir açıklama için, [etiketli model eğitiminde hizmetin belgelerine](#train-a-model-with-labels) bakın.
* Hesabınızda oluşturulan modelleri yönetme.
* Özel bir modeli bir form tanıyıcı kaynağından diğerine kopyalama.

> [!NOTE]
> Modeller ayrıca [form tanıyıcı etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi kullanılarak eğitilmiş olabilir.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, JavaScript için form tanıyıcı istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Düzeni çözümle](#analyze-layout)
* [Alındıları analiz etme](#analyze-receipts)
* [İş kartlarını çözümle](#analyze-business-cards)
* [Faturaları analiz etme](#analyze-invoices)
* [Kimlik belgelerini analiz etme](#analyze-identity-documents)
* [Özel bir modeli eğitme](#train-a-custom-model)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerinizi yönetin](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Tanımladığınız abonelik değişkenlerini kullanarak istemci nesnesinin kimliğini doğrular. Bir `AzureKeyCredential` nesnesi kullanacaksınız, gerekırse API anahtarını yeni istemci nesneleri oluşturmadan güncelleştirebilirsiniz. Ayrıca bir eğitim istemci nesnesi de oluşturacaksınız.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Test için varlıkları al

Ayrıca, eğitim ve test verileriniz için URL 'lere başvurular eklemeniz gerekecektir.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 'SI alımı":::
* Aşağıdaki örneklerde bulunan ( [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/assets)üzerinde de mevcuttur) ve giriş görüntülerini kullanarak, blob depolamada tek BIR BELGENIN SAS URL 'sini almak için yukarıdaki adımları kullanabilirsiniz.

## <a name="analyze-layout"></a>Düzeni çözümle

Belge içindeki tabloları, satırları ve sözcükleri, bir modeli eğmenize gerek kalmadan analiz etmek için form tanıyıcısı 'nı kullanabilirsiniz. Düzen ayıklama hakkında daha fazla bilgi için bkz. [Düzen kavramsal Kılavuzu](../../concept-layout.md). Belirli bir URI 'deki bir dosyanın içeriğini çözümlemek için `beginRecognizeContentFromUrl` yöntemini kullanın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> Ayrıca, yerel bir dosyadan içerik alabilirsiniz. **Beginrecognizecontent** gibi [Formrecognizerclient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Alındıları analiz etme

Bu bölümde, önceden eğitilen bir makbuz modeli kullanılarak ABD makbuzlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Makbuz analizi hakkında daha fazla bilgi için bkz. [alındılar kavramsal Kılavuzu](../../concept-receipts.md).

Bir URI 'den alındıları çözümlemek için `beginRecognizeReceiptsFromUrl` yöntemini kullanın. Aşağıdaki kod, belirtilen URI 'ye bir alındısı işler ve ana alanları ve değerleri konsola yazdırır.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Ayrıca, yerel alındı görüntülerini analiz edebilirsiniz. **BeginRecognizeReceipts** gibi [Formrecognizerclient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>İş kartlarını çözümle

Bu bölümde, önceden eğitilen bir model kullanarak Ingilizce iş kartlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. İş kartı analizi hakkında daha fazla bilgi için bkz. [iş kartları kavramsal Kılavuzu](../../concept-business-cards.md).

Bir URL 'den iş kartlarını çözümlemek için `beginRecognizeBusinessCardsFromURL` yöntemini kullanın.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> Ayrıca, yerel iş kartı görüntülerini çözümleyebilirsiniz. Bkz. **Beginrecognizebusinesscarlar** gibi [Formrecognizerclient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) yöntemleri. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 'daki örnek koda bakın.

## <a name="analyze-invoices"></a>Faturaları analiz etme

Bu bölümde, önceden eğitilen bir model kullanılarak satış faturalarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Fatura analizi hakkında daha fazla bilgi için bkz. [Fatura kavramsal Kılavuzu](../../concept-invoices.md).

Bir URL 'den faturaları çözümlemek için `beginRecognizeInvoicesFromUrl` yöntemini kullanın.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> Ayrıca, yerel iş kartı görüntülerini çözümleyebilirsiniz. Bkz. **Beginrecognizeınices** gibi [Formrecognizerclient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) yöntemleri. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 'daki örnek koda bakın.

## <a name="analyze-identity-documents"></a>Kimlik belgelerini analiz etme

Bu bölümde, form tanıyıcı önceden oluşturulmuş KIMLIK modelini kullanarak devlet tarafından verilen tanımlama belgelerinden önemli bilgilerin nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Fatura analizi hakkında daha fazla bilgi için bkz. [önceden oluşturulmuş kimlik modelimiz kavramsal Kılavuzu](../../concept-identification-cards.md).

Bir URL 'den kimlik belgelerini çözümlemek için yöntemini kullanın `beginRecognizeIdDocumentsFromUrl` .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Özel bir modeli eğitme

Bu bölümde, bir modelin kendi verilerinize nasıl eğulacağı gösterilmektedir. Eğitilen bir model, özgün form belgesindeki anahtar/değer ilişkilerini içeren yapılandırılmış verileri çıktısını alabilir. Modeli eğdikten sonra, bunu test edebilir ve yeniden eğitebilir ve sonunda, gereksinimlerinize göre daha fazla formdan verileri güvenilir bir şekilde çıkarmak için kullanabilirsiniz.

> [!NOTE]
> Modelleri, [form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi ile de eğitebilirsiniz.

### <a name="train-a-model-without-labels"></a>Etiketler olmadan bir modeli eğitme

Eğitim belgelerini el ile etiketlemeden özel formlarınızda bulunan tüm alanları ve değerleri çözümlemek için özel modeller eğitme.

Aşağıdaki işlev belirli bir belge kümesi üzerinde bir model alır ve modelin durumunu konsola yazdırır.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Çıktı

Bu, [JavaScript SDK 'sında](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer)kullanılabilen eğitim verileriyle eğitilen bir modelin çıktıdır. Bu örnek çıkış okunabilirlik için kesildi.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Etiketler içeren bir modeli eğitme

Ayrıca, eğitim belgelerini el ile etiketleyerek özel modeller de eğitebilirsiniz. Etiketlerle eğitim, bazı senaryolarda daha iyi performansa yol açar. Etiketlerle eğitebilmeniz için, `\<filename\>.pdf.labels.json` eğitim belgelerinin yanı sıra BLOB depolama kapsayıcıda özel etiket bilgi dosyalarına () sahip olmanız gerekir. [Form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md) , bu etiket dosyalarını oluşturmanıza yardımcı olmak için bir kullanıcı arabirimi sağlar. Bunu yaptıktan sonra, `beginTraining` `uselabels` parametresini olarak ayarlanan parametre ile çağırabilirsiniz `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Çıktı

Bu, [JavaScript SDK 'sında](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)kullanılabilen eğitim verileriyle eğitilen bir modelin çıktıdır. Bu örnek çıkış okunabilirlik için kesildi.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Formları özel bir model ile analiz etme

Bu bölümde, kendi formlarınız ile eğitilen modeller kullanılarak özel form türlerinizde anahtar/değer bilgilerinin ve diğer içeriklerin nasıl ayıklanacağı gösterilmektedir.

> [!IMPORTANT]
> Bu senaryoyu uygulamak için, KIMLIĞINI aşağıdaki yönteme geçirebilmeniz için zaten bir model eğitilmeniz gerekir. [Modeli eğitme](#train-a-model-without-labels) bölümüne bakın.

`beginRecognizeCustomFormsFromUrl`Yöntemini kullanacaksınız. Döndürülen değer bir `RecognizedForm` nesne koleksiyonudur: gönderilen belgedeki her sayfa için bir tane.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Yerel dosyaları da analiz edebilirsiniz. **Beginrecognizeccustomforms** gibi [Formrecognizerclient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 'daki örnek koda bakın.

### <a name="output"></a>Çıktı

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Özel modellerinizi yönetin

Bu bölümde, hesabınızda depolanan özel modellerin nasıl yönetileceği gösterilmektedir. Aşağıdaki kod, örnek olarak tek bir işlevde tüm model yönetimi görevlerini yapar.

### <a name="get-number-of-models"></a>Model sayısını Al

Aşağıdaki kod bloğu, hesabınızdaki geçerli model sayısını alır.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>Hesaptaki modellerin listesini al

Aşağıdaki kod bloğu, hesabınızdaki kullanılabilir modellerin tam listesini, modelin ne zaman oluşturulduğu ve geçerli durumu hakkında bilgi içerir.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Çıktı

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Sayfaya göre model kimliklerinin listesini al

Bu kod bloğu model ve model kimliklerinin sayfalandırılmış bir listesini sağlar.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Çıktı

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Modeli KIMLIĞE göre al

Aşağıdaki işlev bir model KIMLIĞI alır ve eşleşen model nesnesini alır. Bu işlev varsayılan olarak çağrılmaz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Kaynak hesabındaki bir modeli silme

Ayrıca, KIMLIĞINE başvurarak hesabınızdan bir modeli silebilirsiniz. Bu işlev, belirtilen KIMLIĞE sahip modeli siler. Bu işlev varsayılan olarak çağrılmaz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Çıktı

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Sorun giderme

### <a name="enable-logs"></a>Günlükleri etkinleştirme

Bu kitaplığı kullanırken hata ayıklama günlüklerini görmek için aşağıdaki ortam değişkenini ayarlayabilirsiniz.

```console
export DEBUG=azure*
```

Günlüklerin nasıl etkinleştirileceği hakkında daha ayrıntılı yönergeler için bkz. [ @azure/logger paket belgeleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, modelleri farklı yollarla eğitmek ve analiz etmek için, tanıyıcı JavaScript istemci kitaplığı formunu kullandınız. Daha sonra, daha iyi eğitim veri kümesi oluşturma ve daha doğru modeller üretme hakkında ipuçları edinin.

> [!div class="nextstepaction"]
> [Eğitim verileri kümesi oluşturma](../../build-training-data-set.md)

## <a name="see-also"></a>Ayrıca bkz.

* [Form Tanıma nedir?](../../overview.md)
* Bu kılavuzun örnek kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)' da bulunabilir.
