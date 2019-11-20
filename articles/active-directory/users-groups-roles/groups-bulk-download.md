---
title: Azure Active Directory portalında grupların bir listesini indirin | Microsoft Docs
description: Azure Active Directory içindeki Azure Yönetim Merkezi 'nde Grup özelliklerini toplu olarak indirin.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517143"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Azure Active Directory bir grup listesini toplu indirme (Önizleme)

Azure Active Directory (Azure AD) portalını kullanarak, kuruluşunuzdaki tüm grupların listesini bir virgülle ayrılmış değerler (CSV) dosyasına toplu olarak indirebilirsiniz.

## <a name="to-download-a-list-of-groups"></a>Grupların listesini indirmek için

1. [Azure Portal,](https://portal.azure.com) kuruluştaki bir yönetici hesabıyla oturum açın.
1. Azure AD 'de grupları  > **indirme** **grupları '** nı seçin.
1. Grupları **indirme** sayfasında, gruplarınızı listelemek için **Başlat** ' ı seçin.

   ![Grupları indir komutu tüm gruplar sayfasında bulunur](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>İndirme durumunu denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu işlem sonuçları sayfasında, toplu istek durumu gösterilir](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Hizmet sınırlarını toplu indirme

Bir grup listesini indirmek için her toplu etkinlik, bir saate kadar çalıştırılabilir. Bu, en az 300.000 grubun bir listesini indirmenizi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Grup üyelerini toplu kaldırma](groups-bulk-remove-members.md)
- [Bir grubun üyelerini indir](groups-bulk-download-members.md)
