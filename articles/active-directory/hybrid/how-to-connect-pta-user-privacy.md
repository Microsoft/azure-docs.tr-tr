---
title: Kullanıcı gizliliği ve Azure Active Directory geçişli kimlik doğrulaması | Microsoft Docs
description: Bu makale Azure Active Directory (Azure AD) geçişli kimlik doğrulaması ve GDPR uyumluluğu ile ilgilidir.
services: active-directory
keywords: Azure AD Connect geçişli kimlik doğrulaması, GDPR, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996585"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Kullanıcı Gizliliği ve Azure Active Directory Doğrudan Kimlik Doğrulaması


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Genel Bakış

Azure AD geçişli kimlik doğrulaması, kişisel veriler içerebilen aşağıdaki günlük türünü oluşturur:

- İzleme günlüğü dosyalarını Azure AD Connect.
- Kimlik doğrulama Aracısı izleme günlük dosyaları.
- Windows olay günlüğü dosyaları.

Geçişli kimlik doğrulaması için Kullanıcı gizliliğini iki şekilde geliştirebilirsiniz:

1.  İstek üzerine, bir kişiye ait verileri ayıklar ve bu kişiden verileri yüklemelerden kaldırın.
2.  Hiçbir veri 48 saatten daha fazla korunmayacağından emin olun.

Uygulamanız ve bakımının daha kolay olması için ikinci seçeneği kesinlikle öneririz. Her günlük türü için yönergeler aşağıda verilmiştir:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect izleme günlüğü dosyalarını sil

Bu eylem, GDPR tarafından kapsanan verileri oluşturabileceği için, **%ProgramData%\AADConnect** klasörünün içeriğini denetleyin ve bu klasörün izleme günlüğü içeriğini (**Trace- \* . log** Files) Azure AD Connect yükleme veya yükseltme ya da doğrudan kimlik doğrulama yapılandırmasını değiştirme gibi 48 saat içinde silin.

>[!IMPORTANT]
>Bu dosya, önceki Azure AD Connect yüklemesinin durumunu korumak için kullanıldığı ve bir yükseltme yüklemesi tamamlandığında kullanıldığı için bu klasördeki **PersistedState.xml** dosyasını silmeyin. Bu dosya asla bir kişiyle ilgili hiçbir veri içermez ve hiçbir şekilde silinmemelidir.

Bu izleme günlüğü dosyalarını Windows Gezgini 'ni kullanarak gözden geçirebilir ve silebilirsiniz ya da gerekli eylemleri gerçekleştirmek için aşağıdaki PowerShell betiğini kullanabilirsiniz:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Betiği, "ile bir dosyaya kaydedin. PS1 "uzantısı. Bu betiği gerektiği gibi çalıştırın.

İlgili Azure AD Connect GDPR gereksinimleri hakkında daha fazla bilgi edinmek için [Bu makaleye](reference-connect-user-privacy.md)bakın.

### <a name="delete-authentication-agent-event-logs"></a>Kimlik doğrulama Aracısı olay günlüklerini sil

Bu ürün, **Windows olay günlükleri** de oluşturabilir. Daha fazla bilgi edinmek için lütfen [Bu makaleyi](/windows/win32/wes/windows-event-log)okuyun.

Doğrudan kimlik doğrulama aracısıyla ilgili günlükleri görüntülemek için, sunucuda **Olay Görüntüleyicisi** uygulamayı açın ve **uygulama ve hizmet Logs\microsoft\azureadconnect\authenticationt\0\ yönetici**' nin altına bakın.

### <a name="delete-authentication-agent-trace-log-files"></a>Kimlik doğrulama Aracısı izleme günlüğü dosyalarını sil

**%ProgramData%\microsoft\azure AD Connect Authentication** 'ın \ izleme içeriğini düzenli olarak denetlemeniz ve bu klasörün içeriğini her 48 saatte bir silmeniz gerekir. 

>[!IMPORTANT]
>Kimlik doğrulama Aracısı hizmeti çalışıyorsa, klasördeki geçerli günlük dosyasını silemezsiniz. Yeniden denemeden önce hizmeti durdurun. Kullanıcı oturum açma hatalarından kaçınmak için, [yüksek kullanılabilirlik](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)Için doğrudan geçiş kimlik doğrulamasını yapılandırmış olmanız gerekir.

Bu dosyaları Windows Gezgini 'ni kullanarak gözden geçirebilir ve silebilirsiniz ya da gerekli eylemleri gerçekleştirmek için aşağıdaki betiği kullanabilirsiniz:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Bu betiği her 48 saatte bir çalışacak şekilde zamanlamak için şu adımları izleyin:

1.  Betiği, "ile bir dosyaya kaydedin. PS1 "uzantısı.
2.  **Denetim Masası 'nı** açın ve **sistem ve güvenlik**'e tıklayın.
3.  **Yönetimsel Araçlar** başlığı altında "**görevleri zamanla**" seçeneğine tıklayın.
4.  **Görev Zamanlayıcı**' de, "**görev zamanlama kitaplığı**" na sağ tıklayın ve "**temel görev oluştur...**" seçeneğine tıklayın.
5.  Yeni görevin adını girip **İleri**' ye tıklayın.
6.  **Görev tetikleyicisi** Için "**günlük**" i seçin ve **İleri**' ye tıklayın.
7.  Yinelemeyi iki güne ayarlayın ve **İleri**' ye tıklayın.
8.  Eylem olarak "**bir program Başlat**" ı seçin ve **İleri**' ye tıklayın.
9.  Program/betik için kutuya "**PowerShell**" yazın ve "**bağımsız değişken Ekle (isteğe bağlı)**" etiketli kutuda, daha önce oluşturduğunuz betiğin tam yolunu girin ve ardından **İleri**' ye tıklayın.
10. Sonraki ekranda, oluşturmak üzere olduğunuz görevin bir özeti gösterilir. Görevi oluşturmak için değerleri doğrulayıp **son** ' a tıklayın:
 
### <a name="note-about-domain-controller-logs"></a>Etki alanı denetleyicisi günlükleri hakkında

Denetim günlüğü etkinse, bu ürün etki alanı denetleyicileriniz için güvenlik günlükleri oluşturabilir. Denetim ilkelerini yapılandırma hakkında daha fazla bilgi edinmek için bu [makaleyi](/previous-versions/tn-archive/dd277403(v=technet.10))okuyun.

## <a name="next-steps"></a>Sonraki adımlar
* [Güven Merkezi 'nde Microsoft gizlilik ilkesini gözden geçirme](https://www.microsoft.com/trustcenter)
* [**Sorun giderme**](tshoot-connect-pass-through-authentication.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.