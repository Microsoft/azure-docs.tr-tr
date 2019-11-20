---
title: ASP.NET projeme ne oldu?
description: Visual Studio bağlı hizmetleri kullanarak bir ASP.NET projesine Azure Storage eklendikten sonra ne olacağını açıklar.
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300047"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>ASP.NET projem 'e ne oldu (Visual Studio Azure Storage bağlı hizmeti)?
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
Projenizin Web. config dosyasında, seçili depolama hesabının bağlantı dizesi ve anahtarıyla bir öğe oluşturuldu.

Daha fazla bilgi için bkz. [ASP.net](https://www.asp.net).

