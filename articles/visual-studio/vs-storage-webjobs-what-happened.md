---
title: WebJob projem (Visual Studio Azure Storage) için ne oldu?
description: Visual Studio bağlı hizmetleri kullanılarak bir depolama hesabına bağlandıktan sonra bir Azure WebJob projesinde ne olduğunu açıklar
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298736"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>WebJob projem için ne oldu (Visual Studio Azure Storage bağlı hizmeti)?
## <a name="references-added"></a>Başvurular eklendi
Azure depolama NuGet paketi Visual Studio projenize eklendi veya güncelleştirildi.  
Bu paket, aşağıdaki .NET başvurularını ekler:

* **Microsoft. Data. Edm**
* **Microsoft. Data. OData**
* **Microsoft. Data. Services. Client**
* **Microsoft. WindowsAzure. ConfigurationManager**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. Data**
* **System. uzamsal**

## <a name="connection-string-for-azure-storage-added"></a>Azure depolama için bağlantı dizesi eklendi
Projenizin App. config dosyasında, **AzureWebJobsStorage** ve **AzureWebJobsDashboard** girdileri seçili depolama hesabının bağlantı dizesi ve anahtarıyla güncelleştirildi.

Daha fazla bilgi için bkz. [Azure WebJobs belge kaynakları](https://go.microsoft.com/fwlink/?linkid=390226).

