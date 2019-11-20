---
title: Desteklenmeyen dil dağıtımları-özel çevirici
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler özel çeviricisinde desteklenmeyen dil çiftlerinin nasıl dağıtılacağı gösterilir.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837444"
---
# <a name="unsupported-language-deployments"></a>Desteklenmeyen dil dağıtımları

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Microsoft Translator hub 'ının yakında kullanımdan kaldırılması sayesinde Microsoft, hub aracılığıyla dağıtılmış olan tüm modellerin dağıtımını kaldırır. Çoğu, dil çiftleri özel çevirmende desteklenmeyen hub 'da dağıtılan modellerdir.  Bu durumda olan kullanıcıların içeriğini çevirmek için bir yanıt olmaması gerekmez.

Artık, desteklenmeyen modellerinizi özel çevirici aracılığıyla dağıtmanıza izin veren bir işlemdir.  Bu işlem, en son v3 API 'sini kullanarak içerik çevirmeye devam etmenize olanak sağlar.  Bu modeller, dağıtımını kaldırmak seçinceye kadar veya dil çiftinin özel çevirmende kullanılabilir hale gelene kadar barındırılacak.  Bu makalede, desteklenmeyen dil çiftlerine sahip modelleri dağıtma işlemi açıklanmaktadır.

## <a name="prerequisites"></a>Ön koşullar

Modellerinizin dağıtıma aday olması için aşağıdaki ölçütleri karşılaması gerekir:
* Modeli içeren proje, geçiş aracı kullanılarak hub 'dan özel çevirmene geçirilmiş olmalıdır.  Projeleri ve çalışma alanlarını geçirme işlemi [burada](how-to-migrate.md)bulunabilir.
* Geçiş gerçekleştiğinde modelin dağıtılmış durumda olması gerekir.  
* Modelin dil çifti, özel çevirmende desteklenmeyen bir dil çifti olmalıdır.  Bir dilin Ingilizce veya Ingilizce tarafından desteklendiği, ancak çiftin kendisi Ingilizce, desteklenmeyen dil dağıtımları için aday olan dil çiftleri.  Örneğin, Fransızca ile Almanca arasında bir çift dil çifti için bir hub modeli, Ingilizce ve Ingilizce 'ye Fransızca 'nın desteklenen dil çifti olmasına rağmen desteklenmeyen bir dil çifti olarak değerlendirilir.

## <a name="process"></a>Süreç
Dağıtım için aday olan hub 'dan modelleri geçirdikten sonra, çalışma alanınızın **Ayarlar** sayfasına gidip, **Desteklenmeyen bir çevirici hub 'ı** ile ilgili bir bölümü görebileceğiniz sayfanın sonuna kadar kaydırma yaparak bunları bulabilirsiniz.  Bu bölüm yalnızca yukarıda bahsedilen önkoşulları karşılayan projeleriniz varsa görüntülenir.

![Hub 'dan geçiş yapma](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

**Desteklenmeyen çevirici hub 'ı** seçim sayfasında, **istenmeyen traçler** sekmesi dağıtım için uygun olan modelleri içerir.  Dağıtmak istediğiniz modelleri seçin ve bir istek gönderebilirsiniz.   30 Nisan dağıtım son tarihinden önce, dağıtım için istediğiniz kadar model seçebilirsiniz.
 
![Hub 'dan geçiş yapma](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Gönderildikten sonra, model artık **istenmeyen insanlar** sekmesinde kullanılabilir olmayacaktır ve bunun yerine **istenen seyahat** sekmesinde görüntülenir.  İstediğiniz zaman istediğiniz zaman izleyebilirsiniz.

![Hub 'dan geçiş yapma](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Sırada ne var?

Dağıtım için seçtiğiniz modeller, hub kullanımdan çıkarıldı ve tüm modeller dağıtıldıktan sonra kaydedilir.  Desteklenmeyen modellerin dağıtımına yönelik istekleri göndermek 24 Mayıs 'a kadar.  Bu modelleri 15 Haziran 'da, Translator v3 API aracılığıyla erişilebilecek bir noktada dağıtacağız.  Bunlara ek olarak, 1 Temmuz 'dan itibaren v2 API 'SI üzerinden de kullanılabilir.  

Hub 'ın kullanımdan kaldırılması sırasında önemli tarihler hakkında daha fazla bilgi için [buraya](https://www.microsoft.com/translator/business/hub/)bakın.
Dağıtıldıktan sonra, normal barındırma ücretleri uygulanır.  Ayrıntılar için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .  

Standart özel çevirici modellerinden farklı olarak, hub modelleri yalnızca tek bir bölgede kullanıma sunulacaktır; bu nedenle çok bölgeli barındırma ücretleri uygulanmaz.  Dağıtıldıktan sonra, geçirilen özel çevirmen projesi aracılığıyla hub modelinizi dilediğiniz zaman dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir modeli eğitme](how-to-train-model.md).
- Dağıtılmış özel çeviri modelinizi [Microsoft Translator metin çevirisi API'si v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)aracılığıyla kullanmaya başlayın.
