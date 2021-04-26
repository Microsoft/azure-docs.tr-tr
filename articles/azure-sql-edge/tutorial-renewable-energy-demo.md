---
title: Azure SQL Edge 'i contoso rüzgar grubundaki türbınes 'de dağıtma
description: Bu öğreticide, bir contoso rüzgar grubundaki türbinler için Azure SQL Edge 'i uyandırma algılaması için kullanacaksınız.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723482"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Daha akıllı yenilenebilir kaynaklar oluşturmak için Azure SQL Edge kullanma

Bu Azure SQL Edge tanıtımı, oluşturucuyu içeren veri işleme için SQL DB Edge kullanan bir rüzgar türbin grubu olan contoso yenilenebilecek bir enerji tabanlıdır. 

Bu demo, cihazda algılanan rüzgar turbulence nedeniyle bir uyarının çözümlenmesinde size yol gösterecektir. Bir modeli eğitecaksınız ve algılanan rüzgar uyanmasını düzeltecektir ve sonuç olarak güç çıkışını en uygun hale getirecek şekilde SQL DB Edge 'e dağıtırsınız.

Azure SQL Edge-Channel 9 ' da yenilenebilecek enerji tanıtımı videosu:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Yerel bilgisayarınızda tanıtımı ayarlama
Git, tanıtımın tüm dosyalarını yerel bilgisayarınıza kopyalamak için kullanılır. 

1. Git 'i [buradan](https://git-scm.com/download)yüklersiniz.
2. Bir komut istemi açın ve deponun indirileceği bir klasöre gidin. 
3. Komutu verin https://github.com/microsoft/sql-server-samples.git .
4. Deponun kopyalandığı konumdaki **' SQL-Server-samples\samples\demos\azure-SQL-Edge-demos\Wind Turbin Demo '** öğesine gidin.
5. Demo ortamını ayarlamak ve tanıtımı yürütmek için README.md içindeki yönergeleri izleyin.