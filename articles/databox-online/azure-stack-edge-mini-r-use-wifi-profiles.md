---
title: Azure Stack Edge Mini R cihazlarıyla Wi-Fi profillerini kullanma
description: Yüksek güvenlikli kurumsal ağlarda ve kişisel ağlarda Azure Stack Edge Mini R cihazları için Wi-Fi profillerinin nasıl oluşturulacağını açıklar.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050674"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Azure Stack Edge Mini R cihazlarıyla Wi-Fi profillerini kullanma

Bu makalede, Azure Stack Edge Mini ve cihazlarınızla kablosuz ağ (Wi-Fi) profillerinin nasıl kullanılacağı açıklanır.

Wi-Fi profilini nasıl hazırlayacağınız, kablosuz ağın türüne bağlıdır:

- Ev ağı veya Wi-Fi açık etkin nokta gibi Wi-Fi korumalı erişim 2 (WPA2)-kişisel ağ üzerinde, diğer cihazlarla kullandığınız parolayla mevcut bir kablosuz profili indirebilir ve kullanabilirsiniz.

- Yüksek güvenlikli bir kurumsal ortamda, cihazınıza bir WPA2-kurumsal ağ üzerinden erişirsiniz. Bu tür bir ağ üzerinde, her istemci bilgisayar ayrı bir Wi-Fi profile sahip olur ve sertifikalar aracılığıyla kimlik doğrulaması yapılır. Gerekli yapılandırmayı öğrenmek için ağ yöneticinizle birlikte çalışmanız gerekir.

Her iki ağ türü için de daha sonra profil gereksinimleri tartışılacak.

Her iki durumda da, profili cihazıyla test etmeden veya kullanmadan önce, profilin kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak çok önemlidir.

## <a name="about-wi-fi-profiles"></a>Wi-Fi profilleri hakkında

Wi-Fi profili, Azure Stack Edge Mini R cihazınızı kablosuz ağa bağlamak için gereken SSID (hizmet kümesi tanımlayıcısı veya **ağ adı**), parola anahtarı ve güvenlik bilgilerini içerir.

Aşağıdaki kod örneği, tipik bir kablosuz ağla kullanılacak bir profilin temel ayarlarını göstermektedir:

* `SSID` ağ adıdır.

* `name` Wi-Fi bağlantısı için Kullanıcı dostu addır. Bu, kullanıcıların cihazındaki kullanılabilir bağlantılara gözatarken göreceği addır.

* Profil, ağ aralığı () içinde olduğunda bilgisayarı kablosuz ağa otomatik olarak bağlanacak şekilde yapılandırılır `connectionMode`  =  `auto` .

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Wi-Fi profili ayarları hakkında daha fazla bilgi için, bkz. [Windows 10 ve daha yeni cihazlar için Wi-Fi ayarları ekleme](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)bölümündeki **Kurumsal profil** ve bkz. [Cisco Wi-Fi profilini yapılandırma](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Azure Stack Edge Mini R cihazında kablosuz bağlantıları etkinleştirmek için cihazınızda Wi-Fi bağlantı noktasını yapılandırıp Wi-Fi profillerini cihaza eklersiniz. Bir kuruluş ağında, sertifikaları cihaza de yüklersiniz. Daha sonra cihaz için yerel Web kullanıcı arabiriminden bir kablosuz ağa bağlanabilirsiniz. Daha fazla bilgi için [Azure Stack Edge Mini R 'nizin kablosuz bağlantısını yönetme](./azure-stack-edge-mini-r-manage-wifi.md)makalesine bakın.

## <a name="profile-for-wpa2---personal-network"></a>WPA2-Kişisel ağ profili

Ana ağ veya Wi-Fi açık etkin nokta gibi Wi-Fi korumalı erişim 2 (WPA2)-kişisel ağda, birden çok cihaz aynı profili ve aynı parolayı kullanabilir. Ev ağınızda, cep telefonunuz ve dizüstü bilgisayarınız ağa bağlanmak için aynı kablosuz profili ve parolayı kullanır.

Örneğin, bir Windows 10 istemcisi sizin için bir çalışma zamanı profili oluşturabilir. Kablosuz ağda oturum açtığınızda, Wi-Fi parola istenir ve bu parolayı girdikten sonra bağlanırsınız. Bu ortamda sertifika gerekmez.

Bu ağ türünde, dizüstü bilgisayarınızdan bir Wi-Fi profilini dışa aktarabilir ve ardından Azure Stack Edge Mini R cihazınıza ekleyebilirsiniz. Yönergeler için aşağıdaki [Wi-Fi profilini dışarı aktarma](#export-a-wi-fi-profile)bölümüne bakın.

> [!IMPORTANT]
> Azure Stack Edge Mini R cihazınız için bir Wi-Fi profili oluşturmadan önce, kuruluşunuzun kablosuz ağ güvenlik gereksinimlerini öğrenmek için ağ yöneticinize başvurun. Kablosuz ağın gereksinimleri karşılayıp karşılamadığını öğrenene kadar cihazınızda herhangi bir Wi-Fi profilini test etmeniz veya kullanmamanız gerekir.

## <a name="profiles-for-wpa2---enterprise-network"></a>WPA2-kurumsal ağ profilleri

Kablosuz korumalı erişim 2 (WPA2)-kurumsal ağda, Azure Stack Edge Mini R cihazınızı ağa bağlamak için gereken Wi-Fi profili ve sertifikayı almak için ağ yöneticinizle birlikte çalışmanız gerekir.

Yüksek güvenlikli ağlarda Azure cihazı, Genişletilebilir kimlik doğrulama Protocol-Transport Katmanı Güvenliği (EAP-TLS) ile korunan Genişletilebilir Kimlik Doğrulama Protokolü (PEAP) kullanabilir. EAP-TLS ile PEAP makine kimlik doğrulamasını kullanır: istemci ve sunucu kimlikleri birbirlerine kimliklerini doğrulamak için sertifikaları kullanır.

> [!NOTE]
> * PEAP Microsoft Çekişme El Sıkışma Kimlik Doğrulama Protokolü sürüm 2 (PEAP MSCHAPv2) kullanan kullanıcı kimlik doğrulaması Azure Stack Edge Mini R cihazlarında desteklenmez.
> * Azure Stack Edge Mini R işlevselliğine erişebilmek için EAP-TLS kimlik doğrulaması gerekir. Active Directory kullanarak ayarladığınız bir kablosuz bağlantı çalışmaz.

Ağ Yöneticisi, her bilgisayar için benzersiz bir Wi-Fi profili ve bir istemci sertifikası oluşturacaktır. Ağ Yöneticisi, her cihaz veya paylaşılan sertifika için ayrı bir sertifika kullanıp kullanmayacağınızı belirler.

Çalışma alanında birden fazla fiziksel konumda çalışıyorsanız, ağ yöneticisinin kablosuz bağlantılarınız için birden fazla siteye özgü Wi-Fi profili ve sertifikası sağlaması gerekebilir.

Bir kurumsal ağda, ağ yöneticinizin sağladığı Wi-Fi profillerindeki ayarları değiştirmenizi öneririz. Yapmak isteyebileceğiniz tek ayarlama otomatik bağlantı ayarlarına yönelik olur. Daha fazla bilgi için bkz. Windows 10 ve daha yeni cihazlar için Wi-Fi ayarlarındaki [temel profil](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) .

Yüksek güvenlikli bir kurumsal ortamda, var olan bir kablosuz ağ profilini şablon olarak kullanabilirsiniz:

* Şirket kablosuz ağ profilini iş bilgisayarınızdan indirebilirsiniz. Yönergeler için aşağıdaki [Wi-Fi profilini dışarı aktarma](#export-a-wi-fi-profile)bölümüne bakın.

* Kuruluşunuzdaki diğer kişiler Azure Stack Edge Mini R cihazlarına zaten bir kablosuz ağ üzerinden bağlanıyorsa, Wi-Fi profilini cihazlarından indirebilirler. Yönergeler için bkz. [Wi-Fi profilini indirme](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Bir Wi-Fi profilini dışarı aktarma

Bilgisayarınızdaki Wi-Fi arabirimine yönelik bir profili dışarı aktarmak için şu adımları uygulayın:

1. Bilgisayarınızdaki kablosuz profilleri görmek için, **Başlat** menüsünde **komut istemi** ' ni (cmd.exe) açın ve şu komutu girin:

   `netsh wlan show profiles`

   Çıkış şuna benzer olacaktır:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Bir profili dışarı aktarmak için aşağıdaki komutu girin:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Örneğin, aşağıdaki komut ContosoFTINET profilini, adlı kullanıcının downloads klasörüne XML biçiminde kaydeder `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Cihaza sertifika, Wi-Fi profili ekleme

İhtiyaç duyduğunuz Wi-Fi profilleri ve sertifikalara sahip olduğunuzda, kablosuz bağlantılar için Azure Stack Edge Mini R cihazınızı yapılandırmak için aşağıdaki adımları uygulayın:

1. WPA2 kurumsal bir ağ için, [yükleme sertifikalarındaki](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)yönergenizle izleyerek gerekli sertifikaları cihaza yükleyin.

1. Wi-Fi profillerini mini R cihazına yükleyin ve ardından [Ekle, Wi-Fi profiline bağlanma](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)bölümündeki kılavuzu izleyerek bu cihaza bağlanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Mini R için ağı yapılandırmayı](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)öğrenin.
- [Azure Stack Edge Mini R 'nizin Wi-Fi yönetme](azure-stack-edge-mini-r-manage-wifi.md)hakkında bilgi edinin.
