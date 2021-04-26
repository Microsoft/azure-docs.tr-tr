---
title: 'Azure AD Connect: cihaz geri yazmayı etkinleştirme | Microsoft Docs'
description: Bu belgede Azure AD Connect kullanarak cihaz geri yazmayı etkinleştirme ayrıntıları
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973431"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: cihaz geri yazmayı etkinleştirme
> [!NOTE]
> Cihaz geri yazma için Azure AD Premium aboneliği gerekir.
> 
> 

Aşağıdaki belgelerde, Azure AD Connect cihaz geri yazma özelliğinin nasıl etkinleştirileceği hakkında bilgi verilmektedir. Aşağıdaki senaryolarda cihaz geri yazma kullanılır:

* [Karma sertifika güven dağıtımı kullanarak iş Için Windows Hello 'yu](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration) etkinleştir
* Cihazları ADFS (2012 R2 veya üzeri) korunan uygulamalara (bağlı olan taraf güvenleri) göre koşullu erişimi etkinleştirin.

Bu, uygulamalara erişimin yalnızca güvenilen cihazlara verilme konusunda ek güvenlik ve güvence sağlar. Koşullu erişim hakkında daha fazla bilgi için bkz. [koşullu erişim Ile riski yönetme](../conditional-access/overview.md) ve [Azure Active Directory cihaz kaydı kullanarak şirket Içi koşullu erişim ayarlama](../devices/overview.md).

> [!IMPORTANT]
> <li>Cihazların, kullanıcılarla aynı ormanda bulunması gerekir. Cihazların tek bir ormana geri yazılması gerektiğinden, bu özellik şu anda birden çok Kullanıcı ormanıyla bir dağıtımı desteklememektedir.</li>
> <li>Şirket içi Active Directory ormanına yalnızca bir cihaz kayıt yapılandırma nesnesi eklenebilir. Bu özellik, şirket içi Active Directory birden çok Azure AD dizini ile eşitlendiği bir topoloji ile uyumlu değildir.</li>

## <a name="part-1-install-azure-ad-connect"></a>1. kısım: yüklemeyi Azure AD Connect
Özel veya hızlı ayarları kullanarak Azure AD Connect yükler. Cihaz geri yazma 'yı etkinleştirmeden önce, Microsoft, tüm kullanıcılar ve gruplarla başarıyla eşitlenmiş olarak başlamasını önerir.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Bölüm 2: Azure AD Connect cihaz geri yazmayı etkinleştirme
1. Yükleme sihirbazını yeniden çalıştırın. Ek görevler sayfasından **cihaz seçeneklerini yapılandır** ' ı seçin ve **İleri**' ye tıklayın. 

    ![Cihaz seçeneklerini yapılandır](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Yeni cihaz yapılandırma seçenekleri yalnızca sürüm 1.1.819.0 ve daha yeni sürümlerde kullanılabilir.

2. Cihaz seçenekleri sayfasında **cihaz geri yazmayı Yapılandır**' ı seçin. Cihaz geri yazmayı **devre dışı bırakma** seçeneği, cihaz geri yazma özelliği etkinleştirilinceye kadar kullanılamaz. Sihirbazda bir sonraki sayfaya geçmek için **İleri** 'ye tıklayın.
    ![Cihaz işlemi seçin](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Geri yazma sayfasında, sağlanan etki alanını varsayılan cihaz geri yazma Ormanı olarak görürsünüz.
   ![Özel Install cihaz geri yazma hedefi ormanı](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Cihaz kapsayıcısı** sayfası, kullanılabilir iki seçenekten birini kullanarak Active Directory hazırlama seçeneği sağlar:

    a. **Kuruluş Yöneticisi kimlik bilgilerini sağlayın**: cihazların geri yazılması gereken orman için Kuruluş Yöneticisi kimlik bilgileri sağlanmışsa Azure AD Connect, cihaz geri yazma yapılandırması sırasında ormanı otomatik olarak hazırlar.

    b. **PowerShell betiğini indir**: Azure AD Connect otomatik olarak, cihaz geri yazma için Active Directory 'yi hazırlayabileceğiniz bir PowerShell betiği oluşturur. Azure AD Connect ' de kuruluş yöneticisi kimlik bilgilerinin sağlanması durumunda, PowerShell betiğini indirmek önerilir. İndirilen PowerShell betiğini, cihazların geri yazılacağı ormanın kurumsal yöneticisine **CreateDeviceContainer.ps1** sağlayın.
    ![Active Directory ormanını hazırlama](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Active Directory ormanını hazırlamaya yönelik aşağıdaki işlemler gerçekleştirilir:
    * Zaten mevcut değilse, CN = cihaz kayıt yapılandırması, CN = Services, CN = Configuration, [Forest-DN] altında yeni kapsayıcılar ve nesneler oluşturur ve yapılandırır.
    * Zaten mevcut değilse, CN = RegisteredDevices, [Domain-DN] altında yeni kapsayıcılar ve nesneler oluşturur ve yapılandırır. Bu kapsayıcıda cihaz nesneleri oluşturulacak.
    * Active Directory cihazları yönetmek için Azure AD bağlayıcı hesabında gerekli izinleri ayarlar.
    * Birden fazla ormana Azure AD Connect yüklense bile, yalnızca bir ormanda çalışması gerekir.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Cihazların Active Directory eşitlenecek olduğunu doğrulama
Cihazın geri yazma özelliği artık düzgün şekilde çalışıyor olmalıdır. Cihaz nesnelerinin AD 'ye geri yazılması 3 saate kadar sürebileceğini unutmayın.  Cihazlarınızın doğru şekilde eşitlendiğinden emin olmak için eşitleme kuralları tamamlandıktan sonra aşağıdakileri yapın:

1. Active Directory Yönetim Merkezi'ni başlatın.
2. RegisteredDevices, federe olan etki alanı içinde genişletin.

   ![Active Directory Yönetim Merkezi Kayıtlı cihazlar](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Geçerli kayıtlı cihazlar burada listelenecektir.

   ![Active Directory Yönetim Merkezi Kayıtlı cihazlar listesi](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Koşullu erişimi etkinleştir
Bu senaryoyu etkinleştirmeye yönelik ayrıntılı yönergeler, [Azure Active Directory cihaz kaydı kullanarak şirket Içi koşullu erişim ayarları](../devices/overview.md)dahilinde kullanılabilir.

## <a name="troubleshooting"></a>Sorun giderme
### <a name="the-writeback-checkbox-is-still-disabled"></a>Geri yazma onay kutusu hala devre dışı
Yukarıdaki adımları izlemediğiniz halde cihaz geri yazma onay kutusu etkinleştirilmemişse, aşağıdaki adımlar, Box etkinleştirilmeden önce yükleme sihirbazının doğrulanması sırasında size rehberlik edecektir.

İlk şey:

* Cihazların bulunduğu ormanın, cihaz nesnesi ve ilişkili özniteliklerin mevcut olması için orman şemasının Windows 2012 R2 düzeyine yükseltilmesi gerekir.
* Yükleme Sihirbazı zaten çalışıyorsa, değişiklikler algılanmaz. Bu durumda, Yükleme Sihirbazı 'nı tamamlayıp yeniden çalıştırın.
* Başlatma betiği içinde sağladığınız hesabın gerçekten Active Directory Bağlayıcısı tarafından kullanılan doğru Kullanıcı olduğundan emin olun. Bunu doğrulamak için aşağıdaki adımları izleyin:
  * Başlat menüsünde, **eşitleme hizmeti**' ni açın.
  * **Bağlayıcılar** sekmesini açın.
  * Active Directory Domain Services türü ile bağlayıcıyı bulun ve seçin.
  * **Eylemler** altında **Özellikler**' i seçin.
  * **Active Directory ormana Bağlan**' a gidin. Bu ekranda belirtilen etki alanı ve Kullanıcı adının, betiğe sunulan hesapla eşleştiğinden emin olun.
    ![Eşitleme Service Manager bağlayıcı hesabı](./media/how-to-connect-device-writeback/connectoraccount.png)

Active Directory yapılandırmayı doğrulayın:

* Cihaz kayıt hizmeti 'nin yapılandırma adlandırma bağlamı altında aşağıdaki konumda (CN = DeviceRegistrationService, CN = cihaz kayıt hizmetleri, CN = cihaz kayıt yapılandırması, CN = Services, CN = Configuration) bulunduğunu doğrulayın.

![Yapılandırma ad alanındaki DeviceRegistrationService sorunlarını giderme](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Yapılandırma ad alanını arayarak yalnızca bir yapılandırma nesnesi olduğunu doğrulayın. Birden fazla varsa, yinelemeyi silin.

![Sorun giderme, yinelenen nesneleri arama](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Cihaz kayıt hizmeti nesnesinde, msDS-DeviceLocation özniteliğinin mevcut olduğundan ve bir değere sahip olduğundan emin olun. Bu konumu arar ve objectType msDS-DeviceContainer ile bulunduğundan emin olun.

![Sorun giderme, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Sorun giderme, RegisteredDevices nesne sınıfı](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Active Directory Bağlayıcısı tarafından kullanılan hesabın, önceki adımda bulunan kayıtlı cihazlar kapsayıcısında gerekli izinlere sahip olduğunu doğrulayın. Bu, bu kapsayıcıda beklenen izinlerdir:

![Sorun giderme, kapsayıcıda izinleri doğrulama](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Active Directory hesabının CN = cihaz kayıt yapılandırması, CN = Services, CN = yapılandırma nesnesi üzerinde izinlere sahip olduğunu doğrulayın.

![Sorun giderme, cihaz kaydı yapılandırmasındaki izinleri doğrulama](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ek Bilgi
* [Koşullu erişimle risk yönetimi](../conditional-access/overview.md)
* [Azure Active Directory Cihaz Kaydı kullanarak şirket içi koşullu erişimi ayarlama](../devices/overview.md)

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.