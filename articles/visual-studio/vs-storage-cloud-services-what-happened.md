---
title: Bulut hizmeti projem 'e ne oldu?
description: Visual Studio bağlı hizmetleri kullanarak bir Azure depolama hesabına bağlandıktan sonra bir bulut hizmetleri projesinde ne olacağını açıklar.
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298770"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Bulut hizmetleri projem 'e ne oldu (Visual Studio Azure Storage bağlı hizmeti)?
## <a name="references-added"></a>Başvurular eklendi
Azure depolama NuGet paketi Visual Studio projenize eklendi.  
Bu paket, aşağıdaki .NET başvurularını ekler:

* **Microsoft. Data. Edm**
* **Microsoft. Data. OData**
* **Microsoft. Data. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. Data**
* **System. uzamsal**

## <a name="connection-string-for-azure-storage-added"></a>Azure depolama için bağlantı dizesi eklendi
Öğeler seçili depolama hesabının bağlantı dizesi ve anahtarıyla oluşturuldu. Aşağıdaki dosyalarda değişiklikler yapılmıştır:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. Local. cscfg**

