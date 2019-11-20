---
title: Azure Active Directory portalında toplu Kullanıcı oluşturma (Önizleme) | Microsoft Docs
description: Azure Active Directory 'de Azure AD Yönetim Merkezi 'nde toplu olarak Kullanıcı ekleme
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174339"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Azure Active Directory 'da toplu Kullanıcı oluşturma (Önizleme)

Azure Active Directory (Azure AD) toplu Kullanıcı oluşturma ve silme işlemlerini, konuklara toplu davet etmeyi ve kullanıcıların, grupların ve grup üyelerinin listesini indirmeyi destekler.

## <a name="required-permissions"></a>Gerekli izinler

Yönetim Portalı 'nda kullanıcıları toplu olarak oluşturmak için, genel yönetici veya Kullanıcı Yöneticisi olarak oturum açmış olmanız gerekir.

## <a name="to-create-users-in-bulk"></a>Toplu olarak Kullanıcı oluşturmak için

1. Kuruluşunuzda Kullanıcı Yöneticisi olan bir hesapla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
1. Azure AD 'de **kullanıcılar** > **Toplu oluşturma**' yı seçin.
1. **Toplu kullanıcı oluştur** sayfasında, kullanıcı özelliklerinin geçerli bir virgülle ayrılmış değerler (CSV) dosyasını almak için **İndir** ' i seçin ve sonra oluşturmak istediğiniz kullanıcı ekleme ' yi ekleyin.

   ![İçinde eklemek istediğiniz kullanıcıları listeettiğiniz yerel bir CSV dosyası seçin](./media/users-bulk-add/upload-button.png)

1. CSV dosyasını açın ve oluşturmak istediğiniz her kullanıcı için bir satır ekleyin. Yalnızca **ad**, **Kullanıcı asıl adı**, **ilk parola** ve **blok oturum açma (Evet/Hayır)** değerleri bulunur. Sonra dosyayı kaydedin.

   ![CSV dosyası oluşturulacak kullanıcıların adlarını ve kimliklerini içerir](./media/users-bulk-add/add-csv-file.png)

1. **Toplu Kullanıcı oluşturma (Önizleme)** SAYFASıNDA, CSV dosyanızı karşıya yükleyin bölümünde dosyaya gidin. Dosyayı seçip **Gönder**' e TıKLADıĞıNıZDA, CSV dosyasının doğrulanması başlar.
1. Dosya içeriği doğrulandıktan sonra, **dosyanın başarıyla karşıya yüklendiğini**görürsünüz. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
1. Dosyanız doğrulamayı geçtiğinde, yeni kullanıcıları içeri aktaran Azure toplu işlemini başlatmak için **Gönder** ' i seçin.
1. İçeri aktarma işlemi tamamlandığında, toplu işlem iş durumunun bir bildirimini görürsünüz.

Hatalar varsa, sonuçlar dosyasını **toplu işlem sonuçları** sayfasında indirebilir ve görüntüleyebilirsiniz. Dosya her hatanın nedenini içerir.

## <a name="check-status"></a>Durumu Denetle

Tüm bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu Işlemler sonuçları sayfasında karşıya yükleme durumunu kontrol edin](./media/users-bulk-add/bulk-center.png)

Daha sonra, oluşturduğunuz kullanıcıların Azure portal veya PowerShell kullanarak Azure AD kuruluşunda mevcut olup olmadığını kontrol edebilirsiniz.

## <a name="verify-users-in-the-azure-portal"></a>Azure portal kullanıcıları doğrulama

1. Kuruluşunuzda Kullanıcı Yöneticisi olan bir hesapla [Azure AD Yönetim merkezinde oturum açın](https://aad.portal.azure.com) .
1. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Yönet**altında **Kullanıcılar**' ı seçin.
1. **Göster**altında, **tüm kullanıcılar** ' ı seçin ve oluşturduğunuz kullanıcıların listelendiğini doğrulayın.

### <a name="verify-users-with-powershell"></a>Kullanıcıları PowerShell ile doğrulama

Şu komutu çalıştırın:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Oluşturduğunuz kullanıcıların listelendiğini görmeniz gerekir.

## <a name="bulk-import-service-limits"></a>Toplu içeri aktarma hizmeti sınırları

Kullanıcıları oluşturmak için her toplu etkinlik, bir saate kadar çalıştırılabilir. Bu, en az 50.000 kullanıcının toplu oluşturulmasına izin verebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcıları toplu silme](users-bulk-delete.md)
- [Kullanıcı listesini indir](users-bulk-download.md)
- [Kullanıcıları toplu geri yükleme](users-bulk-restore.md)
