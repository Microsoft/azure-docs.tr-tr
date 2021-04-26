---
title: Mevcut ayrılmış bir SQL havuzunu geri yükleme
description: Mevcut ayrılmış bir SQL havuzunu geri yüklemek için nasıl yapılır Kılavuzu.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567987"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Mevcut ayrılmış bir SQL havuzunu geri yükleme

Bu makalede, Azure SYNAPSE Analytics 'te Azure portal ve SYNAPSE Studio kullanarak mevcut bir adanmış SQL havuzunu geri yüklemeyi öğreneceksiniz. Bu makale hem geri yüklemeler hem de coğrafi geri yüklemeler için geçerlidir. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>SYNAPSE Studio aracılığıyla mevcut bir adanmış SQL havuzunu geri yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. SYNAPSE çalışma alanınıza gidin. 
3. Başlarken-> Open SYNAPSE Studio 'yu açın, **Aç**' ı seçin.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Sol taraftaki Gezinti bölmesinde **veri**' yi seçin.
5. **Havuzları Yönet**' i seçin. 
6. Yeni bir adanmış SQL havuzu oluşturmak için **+ Yeni** ' yi seçin. 
7. Ek ayarlar sekmesinde, geri yüklenecek bir geri yükleme noktası seçin. 

    Coğrafi geri yükleme gerçekleştirmek istiyorsanız, kurtarmak istediğiniz çalışma alanını ve adanmış SQL havuzunu seçin. 

8. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları** seçeneklerinden birini belirleyin. 

    ![Geri yükleme noktaları](../media/sql-pools/restore-point.PNG)

    Adanmış SQL havuzunda otomatik geri yükleme noktaları yoksa, birkaç saat bekleyin veya geri yüklemeden önce Kullanıcı tanımlı geri yükleme noktası oluşturun. User-Defined geri yükleme noktaları için mevcut bir tane seçin veya yeni bir tane oluşturun.

    Bir coğrafi yedeklemeyi geri yüklüyorsanız, kaynak bölgede yer alan çalışma alanını ve geri yüklemek istediğiniz adanmış SQL havuzunu seçmeniz yeterlidir. 

9. **Gözden geçir + Oluştur**’u seçin.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Mevcut bir adanmış SQL havuzunu Azure portal aracılığıyla geri yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Geri yüklemek istediğiniz adanmış SQL havuzuna gidin.
3. Genel Bakış dikey penceresinin en üstünde **geri yükle**' yi seçin.

    ![ Geri Yüklemeye Genel Bakış](../media/sql-pools/restore-sqlpool-01.png)

4. **Otomatik geri yükleme noktaları** veya **Kullanıcı tanımlı geri yükleme noktaları** seçeneklerinden birini belirleyin. 

    Adanmış SQL havuzunda otomatik geri yükleme noktaları yoksa, birkaç saat bekleyin veya geri yüklemeden önce Kullanıcı tanımlı geri yükleme noktası oluşturun. 

    Coğrafi geri yükleme gerçekleştirmek istiyorsanız, kurtarmak istediğiniz çalışma alanını ve adanmış SQL havuzunu seçin. 

5. **Gözden geçir + Oluştur**’u seçin.

## <a name="next-steps"></a>Sonraki Adımlar

- [Bir geri yükleme noktası oluşturma](sqlpool-create-restore-point.md)
