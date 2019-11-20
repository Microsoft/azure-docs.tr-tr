---
title: Grup üyeliği listesini toplu indirme-Azure Active Directory Portal | Microsoft Docs
description: Azure Yönetim Merkezi 'nde toplu olarak Kullanıcı ekleyin.
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
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517166"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Azure Active Directory bir grubun (Önizleme) üyelerini toplu indirin

Azure Active Directory (Azure AD) portalını kullanarak, kuruluşunuzdaki bir grubun üyelerini bir virgülle ayrılmış değerler (CSV) dosyasına toplu olarak indirebilirsiniz.

## <a name="to-bulk-download-group-membership"></a>Grup üyeliğini toplu olarak indirmek için

1. [Azure Portal,](https://portal.azure.com) kuruluştaki bir kullanıcı yönetici hesabıyla oturum açın. Grup sahipleri, sahip oldukları grupların üyelerini de toplu olarak indirebilir.
1. Azure AD 'de **gruplar**  > **tüm gruplar**' ı seçin.
1. Üyeliğini indirmek istediğiniz grubu açın ve ardından **Üyeler**' i seçin.
1. **Üyeler** sayfasında, grup üyelerini listelemek üzere bir CSV dosyası indirmek Için **üyeleri indir** ' i seçin.

   ![Üyeleri Indir komutu, grubun profil sayfasında bulunur](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>İndirme durumunu denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu işlem sonuçları sayfasında, toplu istek durumu gösterilir](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Hizmet sınırlarını toplu indirme

Grup üyelerinin bir listesini indirmek için her toplu etkinlik, bir saate kadar çalıştırılabilir. Bu, en az 500.000 üyenin bir listesini indirmenizi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Grup üyelerini Toplu içe aktarma](groups-bulk-import-members.md)
- [Grup üyelerini toplu kaldırma](groups-bulk-download-members.md)
