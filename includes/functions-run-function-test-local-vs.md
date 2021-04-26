---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701405"
---
1. İşlevinizi çalıştırmak için, Visual Studio 'da <kbd>F5</kbd> tuşuna basın. Araçların HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz gerekebilir. Yerel olarak bir işlev çalıştırdığınızda yetkilendirme düzeyleri hiçbir zaman zorlanmaz.

2. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![Azure yerel çalışma zamanı](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP isteğinin URL’sini tarayıcınızın adres çubuğuna yapıştırın. Sorgu dizesini `?name=<YOUR_NAME>` Bu URL 'ye ekleyin ve isteği çalıştırın. Aşağıdaki görüntüde, bu işlevin döndürdüğü yerel GET isteğine tarayıcıda yapılan yanıt gösterilmektedir: 

    ![Tarayıcıdaki işlev localhost yanıtı](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Hata ayıklamayı durdurmak için, <kbd></kbd> + Visual Studio 'da SHIFT<kbd>F5</kbd> tuşuna basın.
