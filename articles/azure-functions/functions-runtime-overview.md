---
title: Azure İşlevleri Çalışma Zamanı genel bakış | Microsoft Docs
description: Azure İşlevleri Çalışma Zamanı önizlemeye genel bakış
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: e67041ca78ba328fad132cc556b12d780eb9b318
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095970"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure İşlevleri Çalışma Zamanı genel bakış (Önizleme)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure İşlevleri Çalışma Zamanı (Önizleme), şirket içi Azure Işlevleri programlama modelinin basitliğini ve esnekliğini avantajlarından yararlanmanıza yönelik yeni bir yol sağlar. Azure Işlevleri ile aynı açık kaynaklı köklere göre geliştirilen Azure İşlevleri Çalışma Zamanı, bulut hizmeti olarak neredeyse özdeş bir geliştirme deneyimi sağlamak için şirket içinde dağıtılır.

![Azure İşlevleri Çalışma Zamanı Preview Portal][1]

Azure İşlevleri Çalışma Zamanı, buluta işlemeden önce Azure Işlevleri ile karşılaşmanız için bir yol sağlar. Bu şekilde, oluşturduğunuz kod varlıkları, geçiş yaparken bulut ile birlikte alınabilir.  Çalışma zamanı, toplu işlemleri fazla gece çalıştırmak için şirket içi bilgisayarlarınızın yedek işlem gücünü kullanma gibi yeni seçenekleri de açar. Ayrıca kuruluşunuzdaki cihazları, hem şirket içinde hem de bulutta diğer sistemlere koşullu olarak göndermek için de kullanabilirsiniz.

Azure İşlevleri Çalışma Zamanı iki parçadan oluşur:

* Azure İşlevleri Çalışma Zamanı yönetim rolü
* Azure İşlevleri Çalışma Zamanı çalışan rolü

## <a name="azure-functions-management-role"></a>Azure Işlevleri yönetim rolü

Azure Işlevleri yönetim rolü, şirket içi işlevlerinizin yönetimi için bir konak sağlar. Bu rol aşağıdaki görevleri gerçekleştirir:

* Azure Işlevleri Yönetim Portalı barındırırken, [Azure Portal](https://portal.azure.com)gördüğünüz aynı bir değer vardır. Portal, işlevlerinizi Azure portal benzer şekilde geliştirmenize olanak tanıyan tutarlı bir deneyim sunar.
* İşlevleri birden çok Işlev çalışanı arasında dağıtma.
* Yayımlama profilini indirerek ve içeri aktararak işlevlerinizi doğrudan Microsoft Visual Studio yayımlayabilmeniz için bir yayımlama uç noktası sağlama.

## <a name="azure-functions-worker-role"></a>Azure Işlevleri çalışan rolü

Azure Işlevleri çalışan rolleri Windows kapsayıcılarında dağıtılır ve işlev kodunuzun çalıştırıldığı yerdir.  Kuruluşunuz genelinde birden fazla çalışan rolü dağıtabilirsiniz ve bu seçenek müşterilerin yedek işlem gücünü kullanabileceği bir anahtar yoludur.  Birçok kuruluşta yedek bilgi işlemin mevcut olduğu bir örnek, makineler sürekli olarak desteklenmektedir ancak büyük süreler boyunca kullanılmaz.

## <a name="minimum-requirements"></a>Minimum gereksinimler

Azure İşlevleri Çalışma Zamanı kullanmaya başlamak için, bir SQL Server örneğine erişimi olan Windows Server 2016 veya Windows 10 Creators Update içeren bir makineniz olmalıdır.

## <a name="next-steps"></a>Sonraki Adımlar

[Azure işlevleri çalışma zamanı önizlemeyi](https://aka.ms/azafrdoc) yükler

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
