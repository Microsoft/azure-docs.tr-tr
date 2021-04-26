---
title: Azure Active Directory portalında kullanıcıları toplu silme | Microsoft Docs
description: Azure Active Directory Azure Yönetim merkezinde toplu olarak kullanıcıları silme
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
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574472"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory Kullanıcıları toplu silme

Azure Active Directory (Azure AD) portalını kullanarak, kullanıcıları toplu olarak silmek için bir virgülle ayrılmış değerler (CSV) dosyası kullanarak çok sayıda üyeyi bir gruba kaldırabilirsiniz.

## <a name="understand-the-csv-template"></a>CSV şablonunu anlama

Azure AD kullanıcılarını toplu olarak başarıyla silmenizi sağlamak için CSV şablonunu indirin ve girin. İndirmediğiniz CSV şablonu şu örnekteki gibi görünebilir:

![Her satır ve sütunun amacını ve değerlerini açıklayan karşıya yükleme ve çağrı aşımları için elektronik tablo](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV şablonu yapısı

İndirilen bir CSV şablonundaki satırlar aşağıdaki gibidir:

- **Sürüm numarası**: sürüm numarasını içeren ilk satır, KARŞıYA yükleme CSV 'ye eklenmelidir.
- **Sütun başlıkları**: sütun başlıklarının biçimi &lt; *öğe adı* &gt; [PropertyName] &lt; *gerekli veya boş* &gt; . Örneğin, `User name [userPrincipalName] Required`. Şablonun bazı eski sürümlerinde hafif Çeşitlemeler bulunabilir.
- **Örnekler satırı**: şablona her sütun için kabul edilebilir değer örneklerinin bir satırını ekledik. Örnekler satırını kaldırmalı ve kendi girişlerinizin yerine değiştirmelisiniz.

### <a name="additional-guidance"></a>Ek yönergeler

- Karşıya yükleme şablonunun ilk iki satırı kaldırılmamalıdır veya değiştirilmemelidir veya karşıya yükleme işlenemiyor.
- Önce gerekli sütunlar listelenir.
- Şablona yeni sütun eklenmesini önermiyoruz. Eklediğiniz tüm ek sütunlar yoksayılır ve işlenmez.
- CSV şablonunun en son sürümünü mümkün olduğunca sık indirmeniz önerilir.

## <a name="to-bulk-delete-users"></a>Kullanıcıları toplu olarak silmek için

1. Kuruluşunuzda Kullanıcı Yöneticisi olan bir hesapla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
1. Azure AD 'de **Kullanıcılar**  >  **toplu silme**' yi seçin.
1. Kullanıcı özelliklerinden oluşan geçerli bir CSV dosyası almak için, **Kullanıcı toplu silme** sayfasında **İndir** ' i seçin.

   ![Silmek istediğiniz kullanıcıları listeettiğiniz yerel bir CSV dosyası seçin](./media/users-bulk-delete/bulk-delete.png)

1. CSV dosyasını açın ve silmek istediğiniz her kullanıcı için bir satır ekleyin. Yalnızca **Kullanıcı asıl adı** değeri gereklidir. Ardından dosyayı kaydedin.

   ![CSV dosyası, silinecek kullanıcıların adlarını ve kimliklerini içerir](./media/users-bulk-delete/delete-csv-file.png)

1. **Toplu Kullanıcı silme** sayfasında, **CSV dosyanızı karşıya yükleyin** bölümünde dosyaya gidin. Dosyayı seçip Gönder ' e tıkladığınızda, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandığında, **dosyanın başarıyla karşıya yüklendiğini** görürsünüz. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, kullanıcıları silen Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. Silme işlemi tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

Hatalar varsa, sonuçlar dosyasını **toplu işlem sonuçları** sayfasında indirebilir ve görüntüleyebilirsiniz. Bu dosyada her hatanın nedeni belirtilir.

## <a name="check-status"></a>Durumu kontrol etme

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları** sayfasında görebilirsiniz.

   [![Toplu Işlemler sonuçları sayfasında silme durumu ' nu kontrol edin.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Bundan sonra, sildiğiniz kullanıcıların Azure AD kuruluşunda Azure portal veya PowerShell kullanarak var olup olmadığını kontrol edebilirsiniz.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal silinen kullanıcıları doğrulama

1. Kuruluşta Kullanıcı Yöneticisi olan bir hesapla Azure portal oturum açın.
1. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. **Göster** altında yalnızca **tüm kullanıcılar** ' ı seçin ve sildiğiniz kullanıcıların artık listelenmediğini doğrulayın.

### <a name="verify-deleted-users-with-powershell"></a>Silinen kullanıcıları PowerShell ile doğrulama

Şu komutu çalıştırın:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Sildiğiniz kullanıcıların artık listelenmediğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Toplu Kullanıcı ekleme](users-bulk-add.md)
- [Kullanıcı listesini indir](users-bulk-download.md)
- [Kullanıcıları toplu geri yükleme](users-bulk-restore.md)
