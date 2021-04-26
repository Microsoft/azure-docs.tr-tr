---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028553"
---
Bu örnek, bir cep telefonuna SMS iletileri göndermek için [Twilio](https://www.twilio.com/) hizmetinin kullanılmasını içerir. Azure Işlevleri, [Twilio bağlama](../articles/azure-functions/functions-bindings-twilio.md)aracılığıyla Twilio desteğini zaten içerir ve örnek bu özelliği kullanır.

İhtiyacınız olan ilk şey bir Twilio hesabıdır. Bir ücretsiz oluşturabilirsiniz https://www.twilio.com/try-twilio . Bir hesabınız olduğunda, işlev uygulamanıza aşağıdaki üç **uygulama ayarını** ekleyin.

| Uygulama ayarı adı | Değer açıklaması |
| - | - |
| **TwilioAccountSid**  | Twilio hesabınızın SID 'SI |
| **TwilioAuthToken**   | Twilio hesabınız için kimlik doğrulama belirteci |
| **TwilioPhoneNumber** | Twilio hesabınızla ilişkilendirilen telefon numarası. Bu, SMS mesajları göndermek için kullanılır. |