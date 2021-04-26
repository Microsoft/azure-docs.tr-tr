---
title: Grubun üyelerini eklemek veya oluşturmak için toplu karşıya yükleme-Azure Active Directory | Microsoft Docs
description: Grup üyelerini Azure Active Directory Yönetim merkezinde toplu olarak ekleyin.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547755"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Azure Active Directory Grup üyelerini toplu olarak ekle

Azure Active Directory (Azure AD) portalını kullanarak Grup üyelerini toplu olarak içeri aktarmak için bir virgülle ayrılmış değerler (CSV) dosyası kullanarak gruba çok sayıda üye ekleyebilirsiniz.

## <a name="understand-the-csv-template"></a>CSV şablonunu anlama

Azure AD grup üyelerini toplu olarak başarıyla eklemek için toplu karşıya yükleme CSV şablonunu indirin ve girin. CSV şablonunuz şu örnekteki gibi görünebilir:

![Her satır ve sütunun amacını ve değerlerini açıklayan karşıya yükleme ve çağrı aşımları için elektronik tablo](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV şablonu yapısı

İndirilen bir CSV şablonundaki satırlar aşağıdaki gibidir:

- **Sürüm numarası**: sürüm numarasını içeren ilk satır, KARŞıYA yükleme CSV 'ye eklenmelidir.
- **Sütun başlıkları**: sütun başlıklarının biçimi &lt; *öğe adı* &gt; [PropertyName] &lt; *gerekli veya boş* &gt; . Örneğin, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Şablonun bazı eski sürümlerinde hafif Çeşitlemeler bulunabilir. Grup üyeliği değişiklikleri için hangi tanımlayıcıyı kullanacağınızı seçebilirsiniz: üye nesne KIMLIĞI veya Kullanıcı asıl adı.
- **Örnekler satırı**: şablona her sütun için kabul edilebilir değer örneklerinin bir satırını ekledik. Örnekler satırını kaldırmalı ve kendi girişlerinizin yerine değiştirmelisiniz.

### <a name="additional-guidance"></a>Ek yönergeler

- Karşıya yükleme şablonunun ilk iki satırı kaldırılmamalıdır veya değiştirilmemelidir veya karşıya yükleme işlenemiyor.
- Önce gerekli sütunlar listelenir.
- Şablona yeni sütun eklenmesini önermiyoruz. Eklediğiniz tüm ek sütunlar yoksayılır ve işlenmez.
- CSV şablonunun en son sürümünü mümkün olduğunca sık indirmeniz önerilir.
- Dosyayı başarıyla karşıya yüklemek için en az iki kullanıcının UPN veya nesne kimliği ekleyin.

## <a name="to-bulk-import-group-members"></a>Grup üyelerini toplu olarak içeri aktarmak için

1. [Azure Portal,](https://portal.azure.com) kuruluştaki bir kullanıcı yönetici hesabıyla oturum açın. Grup sahipleri ayrıca sahip oldukları grupların üyelerini toplu olarak içeri aktarabilir.
1. Azure AD 'de **gruplar**  >  **tüm gruplar**' ı seçin.
1. Üyeler eklemekte olduğunuz grubu açın ve ardından **Üyeler**' i seçin.
1. **Üyeler** sayfasında, **üyeleri içeri aktar**' ı seçin.
1. **Grup üyelerini toplu içeri aktar** sayfasında, gerekli Grup ÜYESI özellikleriyle CSV dosya şablonunu almak için **İndir** ' i seçin.

    ![Üyeleri Içeri aktar komutu, grubun profil sayfasında bulunur](./media/groups-bulk-import-members/import-panel.png)

1. CSV dosyasını açın ve gruba aktarmak istediğiniz her grup üyesi için bir satır ekleyin (gerekli değerler **üye nesne kimliği** veya **Kullanıcı asıl adı**). Ardından dosyayı kaydedin.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV dosyası alınacak üyelerin adlarını ve kimliklerini içerir":::

1. **Toplu içeri aktarma grubu üyeleri** sayfasında, **CSV dosyanızı karşıya yükleyin** bölümünde dosyaya gidin. Dosyayı seçtiğinizde, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandığında, toplu içeri aktarma sayfası **dosyayı başarıyla karşıya yüklendi** olarak görüntüler. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, grup üyelerini gruba aktaran Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. İçeri aktarma işlemi tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

## <a name="check-import-status"></a>İçeri aktarma durumunu denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları** sayfasında görebilirsiniz.

[![Toplu Işlemler sonuçları sayfasında durumu kontrol edin.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Toplu işlemdeki her bir satır öğesi hakkında ayrıntılar için **# Success**, **# Failure** veya **Total Requests** sütunlarının altındaki değerleri seçin. Hatalar oluştuysa, başarısızlık nedenleri listelenecektir.

## <a name="bulk-import-service-limits"></a>Toplu içeri aktarma hizmeti sınırları

Grup üyelerinin bir listesini içeri aktarmaya yönelik her toplu etkinlik, bir saate kadar çalıştırılabilir. Bu, en çok 40.000 üyenin listesinin içe aktarılması işlemini sunar.

## <a name="next-steps"></a>Sonraki adımlar

- [Grup üyelerini toplu kaldırma](groups-bulk-remove-members.md)
- [Bir grubun üyelerini indir](groups-bulk-download-members.md)
- [Tüm grupların bir listesini indir](groups-bulk-download.md)
