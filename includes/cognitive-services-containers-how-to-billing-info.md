---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704190"
---
Kapsayıcıya sorguları için kullanılan bir Azure kaynak fiyatlandırma katmanını faturalandırılır `<ApiKey>`.

Azure Bilişsel hizmetler kapsayıcıları, kullanım ölçümü için fatura uç noktasına bağlı olmadan çalıştırmak için lisanslı değil. Kapsayıcılar, her zaman faturalandırma bilgileri fatura uç noktası ile iletişim kurmak etkinleştirmeniz gerekir. Bilişsel hizmetler kapsayıcıları analiz ediliyor, metin ve görüntü gibi müşteri verilerini Microsoft'a gönderme. 

### <a name="connect-to-azure"></a>Azure'a Bağlanma

Kapsayıcıyı çalıştırmak için fatura bağımsız değişken değerlerini gerekir. Bu değerler, fatura uç noktaya bağlanmak kapsayıcı sağlar. Kapsayıcı yaklaşık her 10 ila 15 dakika kullanım raporları. Kapsayıcı izin verilen zaman penceresi içinde Azure'a bağlanamazsa kapsayıcı çalışmaya devam eder, ancak fatura uç noktayı geri yüklenene kadar sorgular görmese. Bağlantı 10 kez aynı zaman aralığında 10-15 dakika denenir. Bağlantı kurulamıyor fatura uç nokta 10 içinde çalışır, kapsayıcı çalışmayı durdurur. 

### <a name="billing-arguments"></a>Faturalandırma bağımsız değişkenleri

İçin `docker run` kapsayıcı başlatmak için komut geçerli değerlerle şunlardan üçünü belirtilmesi gerekir:

| Seçenek | Açıklama |
|--------|-------------|
| `ApiKey` | Fatura bilgileri izlemek için kullanılan Bilişsel hizmetler kaynağı API anahtarı.<br/>Bu seçeneğin değeri, belirtilen sağlanan kaynak için bir API anahtarı ayarlanmalıdır `Billing`. |
| `Billing` | Bilişsel hizmetler kaynağın faturalandırma bilgileri izlemek için kullanılan uç nokta.<br/>Bu seçeneğin değeri, sağlanan bir Azure kaynak URI'sini uç noktasına ayarlamanız gerekir.|
| `Eula` | Kapsayıcı lisansını kabul gösterir.<br/>Bu seçenek değeri ayarlanmalıdır **kabul**. |


