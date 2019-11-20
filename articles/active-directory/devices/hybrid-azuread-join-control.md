---
title: Karma Azure AD katılımı denetimi-Azure AD
description: Tek seferde tüm kuruluş genelinde etkinleştirmeden önce karma Azure AD JOIN 'in denetimli bir doğrulamasını yapmayı öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67a73ca47811e7275a6f2177573e10a09b230df
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073620"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Hibrit Azure AD’ye katılıma yönelik denetimli doğrulama

Tüm önkoşulların yerine Windows cihazları otomatik olarak Azure AD kiracınızda cihaz olarak kaydedilir. Azure AD 'deki bu cihaz kimliklerinin durumu karma Azure AD katılımı olarak adlandırılır. Bu makalede ele alınan kavramlar hakkında daha fazla bilgi [Azure Active Directory içinde cihaz yönetimine giriş](overview.md) ve [hibrit Azure Active Directory JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md).

Kuruluşlar, karma Azure AD JOIN 'in tüm kuruluşları genelinde tek seferde etkinleştirmeden önce denetimli bir doğrulama yapmak isteyebilir. Bu makalede, karma Azure AD JOIN 'in denetimli doğrulamasının nasıl yapılacağı açıklanmaktadır.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows geçerli cihazlarda karma Azure AD JOIN 'in denetimli doğrulaması

Windows masaüstü işletim sistemi çalıştıran cihazlarda, desteklenen sürüm Windows 10 yıldönümü Güncelleştirmesi (sürüm 1607) veya daha sonraki bir sürümdür. En iyi uygulama olarak Windows 10 ' un en son sürümüne yükseltin.

Windows geçerli cihazlarda karma Azure AD JOIN 'in denetimli bir doğrulamasını yapmak için şunları yapmanız gerekir:

1. Varsa, hizmet bağlantı noktası (SCP) girişini Active Directory (AD) öğesinden temizle
1. Grup ilkesi nesnesi (GPO) kullanarak etki alanına katılmış bilgisayarlarınızda SCP için istemci tarafı kayıt defteri ayarını yapılandırma
1. AD FS kullanıyorsanız, GPO kullanarak AD FS sunucunuzdaki SCP için istemci tarafı kayıt defteri ayarını da yapılandırmanız gerekir.  
1. Cihaz eşitlemesini etkinleştirmek için Azure AD Connect [eşitleme seçeneklerini de özelleştirmeniz](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) gerekebilir. 


### <a name="clear-the-scp-from-ad"></a>AD 'den SCP 'YI Temizleme

AD 'deki SCP nesnelerini değiştirmek için Active Directory Hizmetleri arabirimleri Düzenleyicisi 'Ni (ADSI düzenleme) kullanın.

1. Şirket Yöneticisi olarak ve yönetim iş istasyonundan veya bir etki alanı denetleyicisinden **ADSI düzenleme** masaüstü uygulamasını başlatın.
1. Etki alanınız için **yapılandırma adlandırma bağlamına** bağlanın.
1. CN = **Configuration, DC = contoso, DC = com** > **cn = Services** > **CN = cihaz kayıt yapılandırması** ' na gidin
1. **CN = cihaz kayıt yapılandırması** altındaki yaprak nesnesine sağ tıklayın ve **Özellikler** ' i seçin
   1. **Öznitelik Düzenleyicisi** penceresinde **anahtar sözcükler** ' i seçin ve **Düzenle** ' ye tıklayın.
   1. **Azureadıd** ve **azureadname** değerlerini (tek seferde bir kez) seçip **Kaldır** ' a tıklayın.
1. **ADSI düzenlemesini** kapat


### <a name="configure-client-side-registry-setting-for-scp"></a>SCP için istemci tarafı kayıt defteri ayarını yapılandırma

Cihazlarınızın kayıt defterinde bir SCP girişi yapılandırmak için bir kayıt defteri ayarı dağıtmak üzere bir grup ilkesi nesnesi (GPO) oluşturmak için aşağıdaki örneği kullanın.

1. Grup ilkesi bir Yönetim Konsolu açın ve etki alanında yeni bir grup ilkesi nesnesi oluşturun.
   1. Yeni oluşturduğunuz GPO 'YU bir ad sağlayın (örneğin, ClientSideSCP).
1. GPO 'YU düzenleyin ve şu yolu bulun: **bilgisayar yapılandırması** > **tercihleri** > **Windows ayarları** > **kayıt defteri**
1. Kayıt defterine sağ tıklayıp **yeni** > **kayıt defteri öğesi** ' ni seçin
   1. **Genel** sekmesinde, aşağıdakileri yapılandırın
      1. Eylem: **Güncelleştir**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Anahtar yolu: **Software\microsoft\windows\currentversion\cdj\aad**
      1. Değer adı: **Tenantıd**
      1. Değer türü: **REG_SZ**
      1. Değer verileri: Azure AD örneğinizin GUID veya **DIZIN kimliği** (bu değer **Azure portal** > **Azure ACTIVE DIRECTORY** > **Özellikler** > **dizin kimliği**) içinde bulunabilir
   1. **Tamam**’a tıklayın.
1. Kayıt defterine sağ tıklayıp **yeni** > **kayıt defteri öğesi** ' ni seçin
   1. **Genel** sekmesinde, aşağıdakileri yapılandırın
      1. Eylem: **Güncelleştir**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Anahtar yolu: **Software\microsoft\windows\currentversion\cdj\aad**
      1. Değer adı: **TenantName**
      1. Değer türü: **REG_SZ**
      1. Değer verileri: AD FS gibi federe ortam kullanıyorsanız, doğrulanmış **etki alanı adınız** . Yönetilen ortam kullanıyorsanız, `contoso.onmicrosoft.com` doğrulanmış **etki alanı adınız** veya onmicrosoft.com etki alanı adınız
   1. **Tamam**’a tıklayın.
1. Yeni oluşturulan GPO için Düzenleyiciyi Kapat
1. Yeni oluşturulan GPO 'YU, denetimli dağıtım popülasyona ait olan, etki alanına katılmış bilgisayarları içeren istenen OU 'ya bağlayın

### <a name="configure-ad-fs-settings"></a>AD FS ayarlarını yapılandırma

AD FS kullanıyorsanız, önce yukarıda bahsedilen yönergeleri kullanarak istemci tarafı SCP 'yi yapılandırmanız gerekir, ancak GPO 'YU AD FS sunucularınız ile bağlantılandırın. SCP nesnesi, cihaz nesneleri için yetki kaynağını tanımlar. Şirket içi veya Azure AD olabilir. AD FS için yapılandırıldığında, cihaz nesneleri kaynağı Azure AD olarak oluşturulur.

> [!NOTE]
> AD FS sunucularınızda istemci tarafı SCP 'YI yapılandıradıysanız, cihaz kimliklerinin kaynağı şirket içi olarak kabul edilir. ADFS daha sonra, ADFS cihaz kaydının "Maximumınactivedays" özniteliğinde tanımlanan bir süre sonra şirket içi dizinden cihaz nesnelerini silmeye başlayacaktır. ADFS cihaz kayıt nesneleri [Get-AdfsDeviceRegistration cmdlet 'i](https://docs.microsoft.com/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)kullanılarak bulunabilir.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows alt düzey cihazlarda karma Azure AD JOIN 'in denetimli doğrulaması

Windows alt düzey cihazlarını kaydettirmek için kuruluşların Microsoft Indirme Merkezi ' nde [Windows 10 olmayan bilgisayarlar Için microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) yüklemesi gerekir.

Paketi, [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)gibi bir yazılım dağıtım sistemi kullanarak dağıtabilirsiniz. Paket, sessiz parametre ile standart sessiz yükleme seçeneklerini destekler. Configuration Manager geçerli dalı, tamamlanan kayıtları izleme özelliği gibi önceki sürümlerden daha fazla avantaj sunar.

Yükleyici, kullanıcı bağlamında çalışan sistemde zamanlanmış bir görev oluşturur. Görev, Kullanıcı Windows 'da oturum açtığında tetiklenir. Bu görev, Azure AD ile kimlik doğrulamasından geçtikten sonra, Kullanıcı kimlik bilgileriyle cihazı Azure AD ile sessizce birleştirir.

Cihaz kaydını denetlemek için Windows Installer paketini seçili Windows alt düzey cihaz grubunuza dağıtmanız gerekir.

> [!NOTE]
> AD 'de bir SCP yapılandırılmamışsa, grup ilkesi nesnesi (GPO) kullanarak etki alanına katılmış bilgisayarlarınızda [SCP için istemci tarafı kayıt defteri ayarını yapılandırma](#configure-client-side-registry-setting-for-scp)konusunda açıklandığı gibi aynı yaklaşımı izlemeniz gerekir.


Her şeyin beklendiği gibi çalıştığını doğruladıktan sonra, [Azure AD Connect kullanarak SCP 'yi yapılandırarak](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)Windows geçerli ve alt düzey cihazlarınızın geri KALANıNı Azure AD 'ye otomatik olarak kaydedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Hibrit Azure Active Directory JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
