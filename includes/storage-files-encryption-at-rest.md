---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011779"
---
Azure dosyalarında depolanan tüm veriler, Azure depolama hizmeti şifrelemesi (SSE) kullanılarak Rest 'te şifrelenir. Depolama hizmeti şifrelemesi Windows 'da BitLocker 'a benzer şekilde çalışır: veriler dosya sistemi düzeyinin altında şifrelenir. Veriler, Azure dosya paylaşımının dosya sisteminin altında, diske kodlandığı gibi şifrelendiğinden, Azure dosya paylaşımından okumak veya yazmak için istemcideki temel anahtara erişiminizin olması gerekmez. Bekleyen şifreleme hem SMB hem de NFS protokolleri için geçerlidir.

Varsayılan olarak, Azure dosyalarında depolanan veriler, Microsoft tarafından yönetilen anahtarlarla şifrelenir. Microsoft tarafından yönetilen anahtarlarla, Microsoft, verileri şifrelemek/şifrelerini çözmek için anahtarları tutar ve bunları düzenli olarak döndürmekten sorumludur. Ayrıca, kendi anahtarlarınızı yönetmeyi seçebilirsiniz ve bu da döndürme sürecinde denetim sağlar. Dosya paylaşımlarınızı müşteri tarafından yönetilen anahtarlarla şifrelemeyi seçerseniz, Azure dosyaları, istemcilerinizden okuma ve yazma isteklerini karşılamak üzere Anahtarlarınıza erişme yetkisine sahiptir. Müşteri tarafından yönetilen anahtarlarla, bu yetkilendirmeyi dilediğiniz zaman iptal edebilirsiniz, ancak Azure dosya paylaşımınızın SMB veya FileREST API 'SI aracılığıyla artık erişilemeyeceği anlamına gelir.

Azure dosyaları, Azure Blob depolama gibi diğer Azure depolama hizmetleriyle aynı şifreleme şemasını kullanır. Azure depolama hizmeti şifrelemesi (SSE) hakkında daha fazla bilgi edinmek için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).