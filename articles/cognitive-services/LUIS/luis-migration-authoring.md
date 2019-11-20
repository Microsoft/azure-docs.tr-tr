---
title: Yazma için Azure kaynağına geçiş
titleSuffix: Azure Cognitive Services
description: Azure yazma kaynak anahtarına geçiş yapın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 86a7575c173b4515eaf0f690bcd00c6fb70ee581
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492725"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure Kaynak yazma anahtarına geçiş

Language Understanding (LUSıS) yazma kimlik doğrulaması, bir e-posta hesabından Azure kaynağına değişti. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="why-migrate"></a>Neden geçirilir?

Yazma için bir Azure kaynağı kullanmak, yazma erişimini denetlemek için kaynağın sahibi olarak size izin verir. Farklı yazar gruplarını yönetmek için yazma kaynakları oluşturabilir ve ad verebilirsiniz. 

Örneğin, farklı Üyeler ile yazma işlemi yaptığınız 2 tür lusıs uygulamanız varsa, iki farklı yazma kaynağı oluşturabilir ve katkıda bulunanlar atayabilirsiniz. Azure Authoring Resource, yetkilendirmeyi denetler. 

> [!Note]
> Geçişten önce, ortak yazarlar _ortak çalışanlar_olarak bilinir. Geçişten sonra, aynı işlevsellik için _katılımcı_ Azure rolü kullanılır.

## <a name="what-is-migrating"></a>Ne geçiriliyor?

Geçişe şunlar dahildir:

* LULAR, sahipler ve katkıda bulunanlar tüm kullanıcıları.
* **Tüm** uygulamalar.
* **Tek yönlü** geçiş.

Sahip, geçirilecek uygulamaların bir alt kümesini seçemez ve işlem geri alınamaz. 

Geçiş şu değildir: 

* Ortak çalışanları toplayan ve otomatik olarak taşınan veya Azure yazma kaynağına ekleyen bir işlem. Uygulamanın sahibi olarak bu adımı gerçekleştirmeniz gerekir. Bu adım uygun kaynak için izinleri gerektirir.
* Tahmin çalışma zamanı kaynağı oluşturma ve atama işlemi. Bir tahmin çalışma zamanı kaynağına ihtiyacınız varsa, bu [ayrı bir işlemdir](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ve değiştirilmez. 

## <a name="how-are-the-apps-migrating"></a>Uygulamalar nasıl taşınıyor?

[LUA portalı](https://www.luis.ai) , geçiş işlemini sağlar. 

Şu durumlarda geçiş yapmanız istenir:

* Yazma için e-posta kimlik doğrulama sisteminde uygulamalarınız var.
* Uygulama sahibisiniz. 

Pencereyi iptal ederek geçiş işlemini erteleyebilirsiniz. Geçiş yapılıncaya veya geçiş son tarihi geçirilmeden, düzenli aralıklarla geçiş yapmanız istenir. Geçiş işlemini üst gezinti çubuğunun kilit simgesinden başlatabilirsiniz.

## <a name="migration-for-the-app-owner"></a>Uygulama sahibi için geçiş

### <a name="before-you-migrate"></a>Geçirmeden önce

* **Isteğe bağlı**olarak, her uygulamayı dışarı aktararak ve dışarı aktarma [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)'sini kullanarak, bu uygulamaları lusıs portalının uygulamalar listesinden yedekleyin.
* **Isteğe bağlı**olarak, her bir uygulamanın ortak kayıt listesini kaydedin. Bu e-posta listesi, geçiş işleminin bir parçası olarak sağlanır.
* **Gerekli**, bir [Azure aboneliğinizin](https://azure.microsoft.com/free/)olması gerekir. Abonelik işleminin bir parçası faturalandırma bilgileri gerektirir. Ancak, LUSıS kullandığınızda ücretsiz (`F0`) fiyatlandırma katmanını kullanabilirsiniz. 

**Lusıs uygulamanızı yazma**`F0` katmanı tarafından belirtilen ücretsizdir. [Fiyatlandırma katmanları hakkında daha fazla](luis-boundaries.md#key-limits)bilgi edinin.

Azure aboneliğiniz yoksa [kaydolun](https://azure.microsoft.com/free/). 

### <a name="migration-steps"></a>Geçiş adımları

[Bu geçiş adımlarını](luis-migration-authoring-steps.md)izleyin.

### <a name="after-you-migrate"></a>Geçişinizden sonra 

Geçiş işleminden sonra, tüm LUO uygulamalarınız tek bir LUıN yazma kaynağına atanır.

_LUA portalında_daha fazla yazma kaynağı oluşturabilir ve **> Azure kaynaklarını yönet** sayfasından atayabilirsiniz. 

Kaynak için **Access Control (IAM)** sayfasında _Azure Portal_yazma kaynağına katkıda bulunanlar ekleyebilirsiniz. Daha fazla bilgi için bkz. [katılımcı erişimi ekleme](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portal|Amaç|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Tahmin ve yazma kaynakları oluşturun.<br>* Katkıda bulunanlar ata.|
|[LUIS](https://www.luis.ai)|* Yeni yazma kaynaklarına geçirin.<br>* **Manage-> Azure Resources** sayfasından tahmine ve yazma kaynaklarını uygulamalara atayın veya atamayı yapın.| 

## <a name="migration-for-the-app-contributor"></a>Uygulama katılımcısı için geçiş

Her LUıN kullanıcısının, ortak çalışanlar/katkıda bulunanlar dahil olmak üzere geçirilmesi gerekir. 

### <a name="before-the-app-is-migrated"></a>Uygulama geçirilmeden önce

Ortak çalışan bir uygulamayı dışarı aktarmayı tercih edebilir ve ardından uygulamayı LUSıS 'e geri aktarabilirsiniz. İçeri aktarma işlemi, sahip olduğunuz yeni bir uygulama KIMLIĞIYLE yeni bir uygulama oluşturur.

### <a name="after-the-app-is-migrated"></a>Uygulama geçirildikten sonra

Uygulama sahibinin [e-postanızı Azure yazma kaynağına ortak çalışan olarak eklemesi](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)gerekir. 

Geçiş işleminden sonra, sahip olduğunuz tüm uygulamalar, LUı portalının **uygulamalarım** sayfasında bulunur.  

## <a name="troubleshooting"></a>Sorun giderme

LUSıS yazma anahtarları yalnızca, geçiş işlemi tamamlandıktan sonra LUO portalında görünür. LUSıS CLı gibi yazma anahtarlarını oluşturursanız, kullanıcının geçiş işlemini tamamlaması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulamanızı bir yazma kaynağına geçirme](luis-migration-authoring-steps.md)