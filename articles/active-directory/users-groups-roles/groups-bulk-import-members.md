---
title: Gruba üye eklemek için toplu içeri aktarma karşıya yükleme-Azure Active Directory | Microsoft Docs
description: Grup üyelerini Azure Active Directory Yönetim merkezinde toplu olarak ekleyin.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517135"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory Grup üyelerini Toplu içe aktarma (Önizleme)

Azure Active Directory (Azure AD) portalını kullanarak Grup üyelerini toplu olarak içeri aktarmak için bir virgülle ayrılmış değerler (CSV) dosyası kullanarak gruba çok sayıda üye ekleyebilirsiniz.

## <a name="to-bulk-import-group-members"></a>Grup üyelerini toplu olarak içeri aktarmak için

1. [Azure Portal,](https://portal.azure.com) kuruluştaki bir kullanıcı yönetici hesabıyla oturum açın. Grup sahipleri ayrıca sahip oldukları grupların üyelerini toplu olarak içeri aktarabilir.
1. Azure AD 'de **gruplar**  > **tüm gruplar**' ı seçin.
1. Üyeler eklemekte olduğunuz grubu açın ve ardından **Üyeler**' i seçin.
1. **Üyeler** sayfasında, **üyeleri içeri aktar**' ı seçin.
1. **Toplu içeri aktarma Grup üyeleri (Önizleme)** sayfasında, gerekli Grup ÜYESI özellikleriyle CSV dosya şablonunu almak için **İndir** ' i seçin.

    ![Üyeleri Içeri aktar komutu, grubun profil sayfasında bulunur](./media/groups-bulk-import-members/import-panel.png)

1. CSV dosyasını açın ve gruba aktarmak istediğiniz her grup üyesi için bir satır ekleyin (gerekli değerler **üye nesne kimliği** veya **Kullanıcı asıl adı**). Ardından dosyayı kaydedin.

   ![CSV dosyası, içeri aktarılacak üyelerin adlarını ve kimliklerini içerir](./media/groups-bulk-import-members/csv-file.png)

1. **Toplu içeri aktarma Grup üyeleri (Önizleme)** sayfasında, **CSV dosyanızı karşıya yükleyin**bölümünde dosyasına gidin. Dosyayı seçtiğinizde, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandığında, toplu içeri aktarma sayfası **dosyayı başarıyla karşıya yüklendi**olarak görüntüler. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, grup üyelerini gruba aktaran Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. İçeri aktarma işlemi tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

## <a name="check-import-status"></a>İçeri aktarma durumunu denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu işlem sonuçları sayfasında, toplu istek durumu gösterilir](./media/groups-bulk-import-members/bulk-center.png)

Toplu işlemdeki her bir satır öğesi hakkında ayrıntılar için **# Success**, **# Failure**veya **Total Requests** sütunlarının altındaki değerleri seçin. Hatalar oluştuysa, başarısızlık nedenleri listelenecektir.

## <a name="bulk-import-service-limits"></a>Toplu içeri aktarma hizmeti sınırları

Grup üyelerinin bir listesini içeri aktarmaya yönelik her toplu etkinlik, bir saate kadar çalıştırılabilir. Bu, en az 40.000 üye listesinin içe aktarılması işlemini izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

- [Grup üyelerini toplu kaldırma](groups-bulk-remove-members.md)
- [Bir grubun üyelerini indir](groups-bulk-download-members.md)
- [Tüm grupların bir listesini indir](groups-bulk-download.md)
