---
title: ASP.NET 5 projem 'e ne oldu (Visual Studio bağlı hizmetler)
description: Visual Studio ASP.NET 5 projesinde, Visual Studio bağlı hizmetleri kullanılarak bir Azure depolama hesabına bağlandıktan sonra ne olacağı açıklanmaktadır
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300007"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>ASP.NET 5 projem 'e ne oldu (Visual Studio Azure Storage bağlı Hizmetleri)?
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

Ayrıca, **Microsoft. Framework. Configuration. JSON** adlı NuGet paketi eklenmiştir.

## <a name="connection-string-for-azure-storage-added"></a>Azure depolama için bağlantı dizesi eklendi
Projenizin config. json dosyasında, seçili depolama hesabının bağlantı dizesi ve anahtarıyla bir öğe oluşturuldu.

Daha fazla bilgi için bkz. [ASP.NET 5](https://www.asp.net/vnext).

