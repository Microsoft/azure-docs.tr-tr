---
title: Azure Active Directory portalında silinen kullanıcıları (Önizleme) toplu geri yükleme | Microsoft Docs
description: Azure Active Directory 'de Azure AD Yönetim Merkezi 'nde toplu olarak silinen kullanıcıları geri yükleme
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174235"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Azure Active Directory silinen kullanıcıları toplu geri yükleme (Önizleme)

Azure Active Directory (Azure AD) toplu Kullanıcı oluşturma ve silme işlemlerini, konuklara toplu davet etmeyi ve kullanıcıların, grupların ve grup üyelerinin listesini indirmeyi destekler.

## <a name="to-bulk-restore-users"></a>Kullanıcıları toplu geri yüklemek için

1. Azure AD kuruluşunda Kullanıcı Yöneticisi olan bir hesapla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
1. Azure AD 'de @no__t **Kullanıcılar**' ı**seçin.**
1. **Silinen kullanıcılar** sayfasında, kullanıcıların geri yüklenecek GEÇERLI bir CSV dosyasını karşıya yüklemek için **toplu geri yükleme** ' yi seçin.

   ![Silinen kullanıcılar sayfasında toplu geri yükleme komutunu seçin](./media/users-bulk-restore/bulk-restore.png)

1. CSV dosyasını açın ve geri yüklemek istediğiniz her kullanıcı için bir satır ekleyin. Yalnızca **ObjectID**değeri gereklidir. Sonra dosyayı kaydedin.

   ![İçinde eklemek istediğiniz kullanıcıları listeettiğiniz yerel bir CSV dosyası seçin](./media/users-bulk-restore/upload-button.png)

1. **Toplu geri yükleme (Önizleme)** sayfasında, **CSV dosyanızı karşıya yükleyin**bölümünde dosyaya gidin. Dosyayı seçip **Gönder**' e TıKLADıĞıNıZDA, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandığında, **dosyanın başarıyla karşıya yüklendiğini**görürsünüz. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, kullanıcıları geri yükleyen Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. Geri yükleme işlemi tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

Hatalar varsa, sonuçlar dosyasını **toplu işlem sonuçları** sayfasında indirebilir ve görüntüleyebilirsiniz. Dosya her hatanın nedenini içerir.

## <a name="check-status"></a>Durumu Denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu Işlemler sonuçları sayfasında karşıya yükleme durumunu kontrol edin](./media/users-bulk-restore/bulk-center.png)

Daha sonra, geri yüklediğiniz kullanıcıların Azure portal veya PowerShell kullanarak Azure AD kuruluşunda mevcut olup olmadığını kontrol edebilirsiniz.

## <a name="view-restored-users-in-the-azure-portal"></a>Azure portal geri yüklenen kullanıcıları görüntüleme

1. Kuruluşunuzda Kullanıcı Yöneticisi olan bir hesapla [Azure AD Yönetim merkezinde oturum açın](https://aad.portal.azure.com) .
1. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Yönet**altında **Kullanıcılar**' ı seçin.
1. **Göster**altında, **tüm kullanıcılar** ' ı seçin ve geri yüklediğiniz kullanıcıların listelendiğini doğrulayın.

### <a name="view-users-with-powershell"></a>PowerShell ile kullanıcıları görüntüleme

Şu komutu çalıştırın:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Geri yüklediğiniz kullanıcıların listelendiğini görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcıları toplu içe aktarma](users-bulk-add.md)
- [Kullanıcıları toplu silme](users-bulk-delete.md)
- [Kullanıcı listesini indir](users-bulk-download.md)
