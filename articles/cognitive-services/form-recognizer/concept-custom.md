---
title: Özel modeller-form tanıyıcı
titleSuffix: Azure Cognitive Services
description: Form tanıyıcı API özel modelleriyle ilgili kavramların kullanımı ve limitlerini öğrenin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 9d339d02310fddc3e5fd463f7d37e253614dba17
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067597"
---
# <a name="form-recognizer-custom-models"></a>Form tanıyıcı özel modeller

Form tanıyıcı, formlarınızın ve belgelerinizdeki verileri çözümlemek ve ayıklamak için gelişmiş makine öğrenimi teknolojisini kullanır. Form tanıyıcı modeli, belirli içeriğinizi analiz etmek için başvuru olarak kullanılan ayıklanan verilerin bir gösterimidir. İki tür form tanıyıcı modeli vardır:

* **Özel modeller**. Form tanıyıcı özel modelleri, işlerinize özgü _formlardan_ ayıklanan verileri temsil eder. Özel modeller farklı form verilerinizi analiz etmek için eğitilmiş olmalıdır.

* **Önceden oluşturulmuş modeller**. Form tanıyıcı Şu anda _alındılar, iş kartları, kimlik kartları_ ve _faturalar_ için önceden oluşturulmuş modelleri desteklemektedir. Önceden oluşturulmuş modeller belge görüntülerinin bilgilerini algılayıp ayıklar ve ayıklanan verileri yapılandırılmış bir JSON çıktısına geri döndürür.

## <a name="what-does-a-custom-model-do"></a>Özel bir model ne yapar?

Form tanıyıcı sayesinde, kullanım örneklerinizle ilgili olan formlardan bilgi ayıklayabilecek bir model eğitebilirsiniz. Başlamak için yalnızca aynı form türüne beş örnek gerekir. Özel modeliniz etiketli veri kümeleri ile veya etiketsiz eğitilmiş olabilir.

## <a name="create-use-and-manage-your-custom-model"></a>Özel modelinizi oluşturma, kullanma ve yönetme

Yüksek düzeyde, özel modelinizi oluşturma, eğitim ve kullanma adımları aşağıdaki gibidir:

> [!div class="nextstepaction"]
>[&#120783;. Eğitim veri kümenizi birleştirme](build-training-data-set.md#custom-model-input-requirements)

Özel bir model oluşturmak, eğitim veri kümenizi oluşturmaya başlar. Örnek veri kümeniz için aynı türde en az beş tamamlanmış form gerekir. Bunlar farklı dosya türleri olabilir ve hem metin hem de el yazısı içerebilir. Formlarınızın aynı belge türünde olması ve form tanıyıcı için [giriş gereksinimlerini](build-training-data-set.md#custom-model-input-requirements) izlemesi gerekir.  

> [!div class="nextstepaction"]
> [&#120784;. Eğitim veri kümenizi karşıya yükleyin](build-training-data-set.md#upload-your-training-data)

Eğitim verilerinizi bir Azure Blob depolama kapsayıcısına yüklemeniz gerekir. Bir kapsayıcı ile Azure depolama hesabı oluşturmayı bilmiyorsanız, *bkz* . [Azure Portal için Azure Storage hızlı](../../storage/blobs/storage-quickstart-blobs-portal.md)başlangıcı. Hizmeti denemek için ücretsiz fiyatlandırma katmanını (F0) kullanın ve daha sonra üretim için ücretli bir katmana yükseltin.  

> [!div class="nextstepaction"]
>[&#120785;. Özel modelinizi eğitme](quickstarts/client-library.md#train-a-custom-model)

Modelinizi etiketli veri kümeleri [olmadan](quickstarts/client-library.md#train-a-model-without-labels) veya [bunlarla](quickstarts/client-library.md#train-a-model-with-labels) eğitebilirsiniz. Etiketsiz veri kümeleri, ek insan girişi olmadan önemli bilgileri algılamak ve tanımlamak için yalnızca Düzen API 'sine güvenir. Etiketli veri kümeleri de Düzen API 'sini kullanır, ancak belirli Etiketler ve alan konumlarınız gibi ek insan girişi dahildir. Hem etiketli hem de etiketsiz verileri kullanmak için, etiketli eğitim verileri için aynı türde en az beş tamamlanmış form ile başlayın ve ardından etiketli verileri gerekli veri kümesine ekleyin.  

>[!div class="nextstepaction"]
>[&#120786;. Özel modelinizle belge çözümleme](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Eğitim veri kümesinin parçası olmayan bir form kullanarak yeni eğitilen modeli test edin. Özel modelinizin performansını geliştirmek için daha fazla eğitim yapmaya devam edebilirsiniz.  

> [!div class="nextstepaction"]
>[&#120787;. Özel modellerinizi yönetin](quickstarts/client-library.md#manage-custom-models)

Dilediğiniz zaman, aboneliğinizdeki tüm özel modellerin listesini görüntüleyebilir, belirli bir özel model hakkında bilgi alabilir veya hesabınızdan özel bir modeli silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

API başvuru belgelerimizi inceleyerek form tanıyıcı istemci kitaplığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Form tanıyıcı API başvurusu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
>
