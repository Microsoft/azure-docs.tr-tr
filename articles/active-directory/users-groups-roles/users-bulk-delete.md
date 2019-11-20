---
title: Azure Active Directory portalında kullanıcıları toplu silme (Önizleme) | Microsoft Docs
description: Azure Active Directory Azure Yönetim merkezinde toplu olarak kullanıcıları silme
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174366"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory 'da kullanıcıları toplu silme (Önizleme)

Azure Active Directory (Azure AD) portalını kullanarak, kullanıcıları toplu olarak silmek için bir virgülle ayrılmış değerler (CSV) dosyası kullanarak çok sayıda üyeyi bir gruba kaldırabilirsiniz.

## <a name="to-bulk-delete-users"></a>Kullanıcıları toplu olarak silmek için

1. Kuruluşunuzda Kullanıcı Yöneticisi olan bir hesapla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
1. Azure AD 'de **kullanıcılar** > **toplu silme**' yı seçin.
1. Kullanıcı özelliklerinden oluşan geçerli bir CSV dosyası almak için, **Kullanıcı toplu silme** sayfasında **İndir** ' i seçin.

   ![Silmek istediğiniz kullanıcıları listeettiğiniz yerel bir CSV dosyası seçin](./media/users-bulk-delete/bulk-delete.png)

1. CSV dosyasını açın ve silmek istediğiniz her kullanıcı için bir satır ekleyin. Yalnızca **Kullanıcı asıl adı**değeri gereklidir. Sonra dosyayı kaydedin.

   ![CSV dosyası, silinecek kullanıcıların adlarını ve kimliklerini içerir](./media/users-bulk-delete/delete-csv-file.png)

1. **Toplu Kullanıcı (Önizleme)** sayfasında, **CSV dosyanızı karşıya yükleyin**bölümünde dosyaya gidin. Dosyayı seçip Gönder ' e tıkladığınızda, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandığında, **dosyanın başarıyla karşıya yüklendiğini**görürsünüz. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, kullanıcıları silen Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. Silme işlemi tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

Hatalar varsa, sonuçlar dosyasını **toplu işlem sonuçları** sayfasında indirebilir ve görüntüleyebilirsiniz. Dosya her hatanın nedenini içerir.

## <a name="check-status"></a>Durumu Denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu Işlemler sonuçları sayfasında karşıya yükleme durumunu kontrol edin](./media/users-bulk-delete/bulk-center.png)

Bundan sonra, sildiğiniz kullanıcıların Azure AD kuruluşunda Azure portal veya PowerShell kullanarak var olup olmadığını kontrol edebilirsiniz.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal silinen kullanıcıları doğrulama

1. Kuruluşta Kullanıcı Yöneticisi olan bir hesapla Azure portal oturum açın.
1. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Yönet**altında **Kullanıcılar**' ı seçin.
1. **Göster**altında yalnızca **tüm kullanıcılar** ' ı seçin ve sildiğiniz kullanıcıların artık listelenmediğini doğrulayın.

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
