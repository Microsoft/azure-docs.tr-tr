---
title: Azure AD Connect ve kullanıcı gizliliği | Microsoft Docs
description: Bu belgede, Azure AD Connect ile GDPR zorlubir pozisyon elde edileceği açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997707"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Kullanıcı gizliliği ve Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Bu makale Azure AD Connect ve kullanıcı gizliliği ile ilgilidir.  Azure AD Connect Health ve kullanıcı gizliliği hakkında bilgi için [buradaki](reference-connect-health-user-privacy.md)makaleye bakın.

Azure AD Connect yüklemeleri için Kullanıcı gizliliğini iki şekilde geliştirebilirsiniz:

1.  İstek üzerine, bir kişiye ait verileri ayıklar ve bu kişiden verileri yüklemelerinden kaldırın
2.  Hiçbir veri 48 saatten daha fazla korunmayacağından emin olun.

Azure AD Connect ekibi, uygulamanız ve bakımını yapmak çok daha kolay olduğundan ikinci seçeneği önerir.

Bir Azure AD Connect eşitleme sunucusu aşağıdaki Kullanıcı Gizlilik verilerini depolar:
1.  **Azure AD Connect veritabanındaki** bir kişi hakkındaki veriler
2.  **Windows olay günlüğü** dosyalarındaki, bir kişi hakkında bilgi içerebilecek veriler
3.  Bir kişi hakkında içerebileceği **Azure AD Connect yükleme günlük dosyalarındaki** veriler

Azure AD Connect müşteriler Kullanıcı verilerini kaldırırken aşağıdaki yönergeleri kullanmalıdır:
1.  Azure AD Connect yükleme günlük dosyalarını içeren klasörün içeriğini düzenli aralıklarla silin – en az 48 saat
2.  Bu ürün, olay günlükleri de oluşturabilir.  Olay günlükleri günlükleri hakkında daha fazla bilgi edinmek için lütfen [belgelere](/windows/win32/wes/windows-event-log)bakın.

Bir kişiyle ilgili veriler, kullanıcının kaynaklandığı kaynak sistemden kaldırıldığında Azure AD Connect veritabanından otomatik olarak kaldırılır. Yöneticilerden gelen özel bir eylemin GDPR uyumlu olması gerekmez.  Ancak, Azure AD Connect verilerinin veri kaynağınıza en az iki günde eşitlendiğinden emin olmanız gerekir.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Azure AD Connect yükleme günlük dosyası klasörü içeriğini silme
**PersistedState.Xml** dosyası dışında **c:\programdata\aadconnect** klasörünün içeriğini düzenli olarak denetleyin ve silin. Bu dosya, önceki Azure yüklemesinin durumunu korur ve yükseltme yüklemesi gerçekleştirildiğinde kullanılır. Bu dosya, bir kişi hakkında hiç veri içermiyor ve silinmemelidir.

>[!IMPORTANT]
>PersistedState.xml dosyasını silmeyin.  Bu dosya, Kullanıcı bilgisi içermez ve önceki yüklemenin durumunu korur.

Bu dosyaları Windows Gezgini 'ni kullanarak gözden geçirebilir ve silebilirsiniz ya da gerekli eylemleri gerçekleştirmek için aşağıdaki gibi bir komut dosyası kullanabilirsiniz:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Bu betiği her 48 saatte bir çalışacak şekilde zamanlayın
Komut dosyasını her 48 saatte bir çalışacak şekilde zamanlamak için aşağıdaki adımları kullanın.

1.  Betiği **&#46;ps1** uzantılı bir dosyaya kaydedin, ardından Denetim Masası 'nı açın ve **sistemler ve güvenlik**' e tıklayın.
    ![Sistem](./media/reference-connect-user-privacy/gdpr2.png)

2.  Yönetimsel Araçlar başlığı altında, **görevleri zamanla**' ya tıklayın.
    ![Görev](./media/reference-connect-user-privacy/gdpr3.png)
3.  Görev Zamanlayıcı, **görev zamanlama kitaplığı** ' na sağ tıklayın ve **temel görev oluştur...** öğesine tıklayın.
4.  Yeni görevin adını girip **İleri**' ye tıklayın.
5.  Görev tetikleyicisi için **günlük** ' i seçin ve **İleri**' ye tıklayın.
6.  Yinelenme **sayısını 2 gün** olarak ayarlayıp **İleri**' ye tıklayın.
7.  Eylem olarak **bir program Başlat** ' ı seçin ve **İleri**' ye tıklayın.
8.  Program/betik için kutuya **PowerShell** yazın ve **bağımsız değişken Ekle (isteğe bağlı)** kutusunda, daha önce oluşturduğunuz betiğin tam yolunu girin ve ardından **İleri**' ye tıklayın.
9.  Sonraki ekranda, oluşturmak üzere olduğunuz görevin bir özeti gösterilir. Görevi oluşturmak için değerleri doğrulayıp **son** ' a tıklayın.



## <a name="next-steps"></a>Sonraki adımlar
* [Güven Merkezi 'nde Microsoft gizlilik ilkesini gözden geçirme](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health ve kullanıcı gizliliği](reference-connect-health-user-privacy.md)