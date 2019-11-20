---
title: StorSimple Sanal dizileri için yeni kimlik doğrulaması | Microsoft Docs
description: Hizmetiniz için AAD tabanlı kimlik doğrulaması kullanmayı, yeni kayıt anahtarı oluşturmayı ve cihazların el ile kaydedilmesini gerçekleştirme işlemini açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 723d5e969ba2f635724ffa50d562a7abaf936dcf
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517130"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>StorSimple için yeni kimlik doğrulamasını kullanın

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple Aygıt Yöneticisi hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple Sanal dizilerine bağlanır. StorSimple Aygıt Yöneticisi hizmeti, StorSimple cihazınızda hizmetin kimliğini doğrulamak için bir Access Control hizmeti (ACS) kullandı. ACS mekanizması yakında kullanımdan kalkmış ve bir Azure Active Directory (AAD) kimlik doğrulaması ile değiştirilmeyecektir.

Bu makalede yer alan bilgiler yalnızca StorSimple 1200 serisi sanal dizileri için geçerlidir. Bu makalede AAD kimlik doğrulaması ve ilişkili yeni hizmet kayıt anahtarı ayrıntıları ve StorSimple cihazlarına uygun şekilde güvenlik duvarı kurallarında yapılan değişiklikler açıklanmaktadır.

AAD kimlik doğrulaması, güncelleştirme 1 veya üzerini çalıştıran StorSimple Sanal dizilerinde (model 1200) oluşur.

AAD kimlik doğrulamasının tanıtımı nedeniyle, değişiklikler şu şekilde gerçekleşir:

- Güvenlik duvarı kuralları için URL desenleri.
- Hizmet kayıt anahtarı.

Bu değişiklikler, aşağıdaki bölümlerde ayrıntılı olarak ele alınmıştır.

## <a name="url-changes-for-aad-authentication"></a>AAD kimlik doğrulaması için URL değişiklikleri

Hizmetin AAD tabanlı kimlik doğrulaması kullandığından emin olmak için tüm kullanıcıların güvenlik duvarı kurallarında yeni kimlik doğrulama URL 'Lerini içermesi gerekir.

StorSimple Sanal dizisi kullanıyorsanız, güvenlik duvarı kurallarında aşağıdaki URL 'nin eklendiğinden emin olun:

| URL kalıbı                         | Bulut | Bileşen/Işlevsellik         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure genel |AAD kimlik doğrulama hizmeti      |
| `https://login.microsoftonline.us` | ABD Devleti |AAD kimlik doğrulama hizmeti      |

StorSimple Sanal dizilerinin URL desenlerinin tüm listesi için, [güvenlik duvarı kuralları Için URL desenleri](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)' ne gidin.

Kimlik doğrulama URL 'SI, kullanımdan kaldırma tarihinin ötesinde güvenlik duvarı kurallarında yoksa, kullanıcılar StorSimple cihazının hizmetten kimlik doğrulayamadığından önemli bir uyarı görür. Hizmet cihazla iletişim kuramaz. Kullanıcılar bu uyarıyı görtiklerinde, yeni kimlik doğrulama URL 'sini içermesi gerekir. Uyarı hakkında daha fazla bilgi için, [StorSimple cihazınızı izlemek için uyarıları kullanma](storsimple-virtual-array-manage-alerts.md#networking-alerts)bölümüne gidin.

## <a name="device-version-and-authentication-changes"></a>Cihaz sürümü ve kimlik doğrulama değişiklikleri

StorSimple Sanal dizisi kullanıyorsanız, çalıştırmakta olduğunuz cihaz yazılımı sürümüne göre uygulamanız gereken eylemi belirlemek için aşağıdaki tabloyu kullanın.

| Cihazınız çalışıyorsa  | Aşağıdaki eylemi gerçekleştirin                                    |
|----------------------------|--------------------------------------------------------------|
| Güncelleştirme 1,0 veya üzeri ve çevrimdışı. <br> URL 'nin beyaz listelenmediğinden bir uyarı görürsünüz.| 1. Güvenlik duvarı kurallarını kimlik doğrulama URL 'sini içerecek şekilde değiştirin. Bkz. [kimlik doğrulama URL 'leri](#url-changes-for-aad-authentication). <br> 2. [HIZMETTEN AAD kayıt anahtarını alın](#aad-based-registration-keys). <br> 3. [Sanal dizinin Windows PowerShell arabirimine bağlanmak](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)için 1-5 adımlarını gerçekleştirin.<br> 4. Windows `Invoke-HcsReRegister` PowerShell aracılığıyla cihazı kaydetmek için cmdlet 'ini kullanın. Önceki adımda aldığınız anahtarı sağlayın.|
| Güncelleştirme 1,0 veya üzeri ve cihaz çevrimiçi.| Eylem gerekmiyor.                                       |
| Güncelleştirme 0,6 veya önceki bir sürümü ve cihaz çevrimdışı. | 1. [Güncelleştirme 1,0 ' yı katalog sunucusu üzerinden indirin](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Güncelleştirme 1,0 ' i yerel Web Kullanıcı arabirimi üzerinden uygulayın](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [HIZMETTEN AAD kayıt anahtarını alın](#aad-based-registration-keys). <br>4. [Sanal dizinin Windows PowerShell arabirimine bağlanmak](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)için 1-5 adımlarını gerçekleştirin.<br>5. Windows `Invoke-HcsReRegister` PowerShell aracılığıyla cihazı kaydetmek için cmdlet 'ini kullanın. Önceki adımda aldığınız anahtarı sağlayın.|
| Güncelleştirme 0,6 veya önceki bir sürümü ve cihaz çevrimiçi | Güvenlik duvarı kurallarını kimlik doğrulama URL 'sini içerecek şekilde değiştirin.<br> Güncelleştirme 1,0 ' yı Azure portal aracılığıyla yükler. |

## <a name="aad-based-registration-keys"></a>AAD tabanlı kayıt anahtarları

StorSimple Sanal dizileri için güncelleştirme 1,0 ' den başlayarak yeni AAD tabanlı kayıt anahtarları kullanılır. StorSimple Aygıt Yöneticisi hizmetinizi cihaza kaydetmek için kayıt anahtarlarını kullanırsınız.

Güncelleştirme 0,6 veya önceki bir sürümü çalıştıran bir StorSimple Sanal dizisi kullanıyorsanız, yeni AAD hizmeti kayıt anahtarlarını kullanamazsınız. Hizmet kayıt anahtarını yeniden oluşturmanız gerekiyor. Anahtarı yeniden oluşturduktan sonra, yeni anahtar, sonraki tüm cihazları kaydetmek için kullanılır. Eski anahtar artık geçerli değil.

- Yeni AAD kayıt anahtarının süresi 3 gün sonra dolar.
- AAD kayıt anahtarları yalnızca güncelleştirme 1 veya üzerini çalıştıran StorSimple 1200 serisi sanal dizileri ile çalışır. StorSimple 8000 serisi cihazından AAD kayıt anahtarı çalışmayacak.
- AAD kayıt anahtarları ilgili ACS kayıt anahtarlarından daha uzun.

Bir AAD hizmeti kayıt anahtarı oluşturmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-generate-the-aad-service-registration-key"></a>AAD hizmeti kayıt anahtarını oluşturmak için

1. **StorSimple aygıt yöneticisi**,  **&gt; yönetim** **anahtarlarına**gidin.
    
    ![Anahtarlara git](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. **Anahtar oluştur**' a tıklayın.

    ![Yeniden oluştur 'a tıklayın](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Yeni anahtarı kopyalayın. Eski anahtar artık çalışmaz.

    ![Yeniden oluşturmayı Onayla](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple Sanal dizisinin](storsimple-virtual-array-deploy1-portal-prep.md) nasıl dağıtılacağı hakkında daha fazla bilgi edinin
