---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534276"
---
## <a name="export-an-api-definition"></a>API tanımını dışarı aktarma
İşleviniz için bir Openapı tanımı [oluşturun, bir işlev Için openapı tanımı oluşturun](../articles/azure-functions/functions-openapi-definition.md). Bu işlemin bir sonraki adımında, PowerApps ve Microsoft Flow bunu özel bir API 'de kullanabilmesi için API tanımını dışarı aktarmaları.

> [!IMPORTANT]
> PowerApps ve Microsoft Flow kiracılarınız için kullandığınız kimlik bilgileriyle Azure 'da oturum açmanız gerektiğini unutmayın. Bu, Azure 'un özel API oluşturmasını ve PowerApps ve Microsoft Flow için kullanılabilir olmasını sağlar.

1. [Azure Portal](https://portal.azure.com), **platform özellikleri** > **API tanımı**> işlev uygulamanızın adını ( **işlev gösterimi-enerji**gibi) tıklatın.

    ![API tanımı](media/functions-export-api-definition/api-definition.png)

1. **PowerApps + Flow 'A Aktar öğesine**tıklayın.

    ![API tanımı kaynağı](media/functions-export-api-definition/export-api-1.png)

1. Sağ bölmede, tabloda belirtilen ayarları kullanın.

    |Ayar|Açıklama|
    |--------|------------|
    |**Dışarı aktarma modu**|Özel API 'yi otomatik olarak oluşturmak için **Express** ' i seçin. **El ile** ' yı seçtiğinizde API tanımını dışarı aktarır, ancak bunu PowerApps 'e aktarmanız ve el ile Microsoft Flow gerekir. Daha fazla bilgi için bkz. [PowerApps ve Microsoft Flow 'e aktarma](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Ortam**|Özel API 'nin kaydedilmesi gereken ortamı seçin. Daha fazla bilgi için bkz. [ortamlara genel bakış (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) veya [ortamlara genel bakış (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Özel API adı**|Gibi `Turbine Repair`bir ad girin.|
    |**API anahtarı adı**|Uygulama ve akış oluşturucuların özel API Kullanıcı arabiriminde göreceği adı girin. Örneğin yararlı bilgiler içerdiğine unutmayın.|
 
    ![PowerApps ve Microsoft Flow’a dışarı aktarma](media/functions-export-api-definition/export-api-2.png)

1. **Tamam**'ı tıklatın. Özel API artık kurulmuştur ve belirlediğiniz ortama eklenir.