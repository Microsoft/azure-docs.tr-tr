---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011935"
---
Paylaşılan erişim imzası (SAS) Depolama hesabınızdaki kapsayıcılara ve bloblara sınırlı erişim sağlamanıza olanak sağlar. SAS oluşturduğunuzda, bir istemcinin hangi Azure depolama kaynakları erişimine izin verileceğini, bu kaynaklarda sahip oldukları izinleri ve SAS 'ın ne kadar süreyle geçerli olduğunu de içeren kısıtlamalarını belirtirsiniz.

Her SAS bir anahtarla imzalanır. SAS 'yi iki şekilde imzalayabilirsiniz:

- Azure Active Directory (Azure AD) kimlik bilgileri kullanılarak oluşturulmuş bir anahtarla oluşturulur. Azure AD kimlik bilgileriyle imzalanan SAS, *Kullanıcı temsili* SAS 'dir.
- Depolama hesabı anahtarıyla. Depolama hesabı anahtarıyla bir *HIZMET SAS* ve *Hesap SAS* 'si imzalanır.
