---
title: Kişisel verileri - Azure Active Directory Uygulama proxy'si Kaldır | Microsoft Docs
description: Azure Active Directory Uygulama proxy'si için cihazlarında yüklü bağlayıcıların kişisel verileri kaldırın.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebb2a38e520c988ee7ca9a234aadd6ae2de4f0cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807746"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Azure Active Directory Uygulama proxy'si için kişisel verilerini kaldırma

Azure Active Directory Uygulama Proxy bağlayıcıları cihazlarınızda kişisel verileri olabileceğini anlamına gelir, cihazlarınıza yükleyebilir gerektirir. Bu makalede gizlilik geliştirmek için bu kişisel verileri silmek adımlar sağlar.

## <a name="where-is-the-personal-data"></a>Kişisel verileri nerede?

Aşağıdaki günlük türlerine kişisel veri yazmak uygulama proxy'si mümkündür:

- Bağlayıcısı olay günlükleri
- Windows olay günlükleri

## <a name="remove-personal-data-from-windows-event-logs"></a>Windows olay günlükleri kişisel verilerini kaldırma

Veri saklama için Windows olay günlüklerini yapılandırma hakkında daha fazla bilgi için bkz: [olay günlüğü ayarları](https://technet.microsoft.com/library/cc952132.aspx). Windows olay günlükleri hakkında bilgi edinmek için [kullanarak Windows olay günlüğü](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Bağlayıcısı olay günlüklerinden kişisel verilerini kaldırma

Uygulama Ara sunucusu günlükleri kişisel verileriniz yoksa emin olmak için şunlardan birini yapabilirsiniz:

- Silme veya gerekli değilse, verileri görüntüleme veya
- Günlüğünü devre dışı bırakın

Bağlayıcısı olay günlüklerinden kişisel verileri kaldırmak için aşağıdaki bölümleri kullanın. Bağlayıcıyı yüklediğiniz tüm cihazlar için temizleme işlemini tamamlamanız gerekir.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Görüntülemek veya belirli verileri dışarı aktarma

Görüntülemek veya belirli verileri dışarı aktarma için her bağlayıcı günlüklerinin ilgili girişlere arayın. Günlükleri şu adreste bulunabilir `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Günlükleri metin dosyaları olduğundan, kullanabileceğiniz [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) bir kullanıcıyla ilişkili metin girişleri aranacak.  

Kişisel verileri bulmak için kullanıcı kimliği için günlük dosyalarını arayın.

Kerberos kısıtlanmış temsil, kullanıcı adı türü bu bileşenler için arama kullanan bir uygulama tarafından günlüğe kaydedilen kişisel verileri bulmak için:

- Şirket içi kullanıcı asıl adı
- Kullanıcı adı, kullanıcı asıl adının parçası
- Kullanıcı adı, şirket içi kullanıcı asıl adının parçası
- Şirket içi Güvenlik Hesapları Yöneticisi (SAM) hesap adı

### <a name="delete-specific-data"></a>Özel verileri Sil

Belirli veri silmek için:

1. Yeni bir günlük dosyası oluşturmak için Microsoft Azure AD uygulama ara sunucusu Bağlayıcısı hizmeti yeniden başlatın. Yeni bir günlük dosyası, eski günlük dosyaları değiştirmek veya silmek sağlar. 
1. İzleyin [görünümü veya dışarı aktarma belirli veri](#view-or-export-specific-data) işlem açıklanan daha önce silinmesi gereken bilgi bulunamıyor. Tüm bağlayıcı günlüklerini arayın.
1. Ya da ilgili günlük dosyalarını veya kişisel verileri içeren alanları seçerek silebilirsiniz. Artık ihtiyacınız yoksa tüm eski günlük dosyaları silebilirsiniz.

### <a name="turn-off-connector-logs"></a>Bağlayıcı oturumunu kapatıp açın

Connector günlükleri kişisel verilerini içermediğinden emin olmak için bir oturum oluşturmayı devre dışı bırak seçenektir. Bağlayıcı günlükleri oluşturmaya durdurmak için aşağıdaki vurgulanmış satırından kaldırmak `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`.

![Vurgulanmış kodu kaldırmak için bir kod parçacığı gösterir](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Sonraki adımlar

Uygulama proxy'si genel bakış için bkz. [güvenli uzaktan erişim sağlamak şirket içi uygulamalara](application-proxy.md).