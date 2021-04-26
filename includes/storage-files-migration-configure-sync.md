---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7aa3867fdc5de320c47a15737b655b8032f402a6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075617"
---
Bu adım, önceki adımlarda Windows Server Örneğiniz üzerinde ayarladığınız tüm kaynak ve klasörler ile birlikte çalışır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Depolama eşitleme hizmeti kaynağınızı bulun.
1. Her Azure dosya paylaşımının depolama eşitleme hizmeti kaynağı içinde yeni bir *eşitleme grubu* oluşturun. Azure Dosya Eşitleme terminolojisinde Azure dosya paylaşımında, eşitleme grubu oluşturma ile açıkladığınız eşitleme topolojisinde bir *bulut uç noktası* olur. Eşitleme grubunu oluştururken, burada eşitlenecek dosya kümesini tanımak üzere buna tanıdık bir ad verin. Azure dosya paylaşımında eşleşen bir ad ile başvurulduğunuzdan emin olun.
1. Eşitleme grubu oluşturulduktan sonra, eşitleme grupları listesinde bir satır görüntülenir. Eşitleme grubunun içeriğini göstermek için adı (bir bağlantı) seçin. Azure dosya paylaşımınızı **bulut uç noktaları** altında görürsünüz.
1. **+ Sunucu uç noktası Ekle** düğmesini bulun. Sağladığınız yerel sunucudaki klasörü, bu *sunucu uç noktasının* yolu olacak.
