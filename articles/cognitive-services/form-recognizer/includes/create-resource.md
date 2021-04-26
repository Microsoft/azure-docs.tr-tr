---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467303"
---
Azure portal gidin ve <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" Yeni bir form tanıyıcı kaynağı oluşturun " target="_blank"> Yeni bir form tanıyıcı kaynağı oluşturun </a> . **Oluştur** bölmesinde aşağıdaki bilgileri sağlayın:

| Proje ayrıntıları   | Açıklama   |
|--|--|
| **Abonelik** | Erişim izni verilen Azure aboneliğini seçin. |
| **Kaynak grubu** | Kaynağı içerecek [Azure Kaynak grubu](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) . Yeni bir grup oluşturabilir veya önceden var olan bir gruba ekleyebilirsiniz. |
| **Bölge** | Bilişsel hizmet örneğinizin konumu. Farklı konumlar gecikme gösterebilir, ancak kaynağınızın çalışma zamanı kullanılabilirliğini etkilemez. |
| **Ad** | Kaynağınız için açıklayıcı bir ad. Örneğin, *Mynameformtanıyıcı* gibi açıklayıcı bir ad kullanmanızı öneririz. |
| **Fiyatlandırma katmanı** | Kaynağınızın maliyeti seçtiğiniz fiyatlandırma katmanına ve kullanımınıza bağlıdır. Daha fazla bilgi için bkz. API [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Genellikle Azure portal bir bilişsel hizmet kaynağı oluşturduğunuzda, çok sunuculu bir abonelik anahtarı (birden çok bilişsel hizmetler genelinde kullanılan) veya tek servis abonelik anahtarı (yalnızca belirli bir bilişsel hizmet ile kullanılır) oluşturma seçeneğiniz vardır. Ancak şu anda form tanıyıcı, çok hizmet aboneliğine dahil değildir.

Form tanıyıcı kaynağınız dağıtımı bitirdiğinde portalda **tüm kaynaklar** listesinden bulun ve seçin. Anahtarınız ve uç noktanız, kaynak yönetimi altında kaynağın anahtar ve uç nokta sayfasında yer alır. Bundan sonra her ikisini de geçici bir konuma kaydedin.
