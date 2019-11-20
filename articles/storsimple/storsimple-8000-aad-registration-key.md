---
title: Azure 'da StorSimple 8000 Aygıt Yöneticisi hizmeti için yeni kimlik doğrulaması kullanma | Microsoft Docs
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 88badf6de5e7181763dd72c7a51262d16697bffd
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963483"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>StorSimple için yeni kimlik doğrulamasını kullanın

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

StorSimple Aygıt Yöneticisi hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple cihaza bağlanır. StorSimple Aygıt Yöneticisi hizmeti, StorSimple cihazınızda hizmetin kimliğini doğrulamak için bir Access Control hizmeti (ACS) kullandı. ACS mekanizması yakında kullanımdan kalkmış ve bir Azure Active Directory (AAD) kimlik doğrulaması ile değiştirilmeyecektir. Daha fazla bilgi için, ACS kullanımdan kaldırma ve AAD kimlik doğrulamasının kullanımı için aşağıdaki duyurulara gidin.

- [Azure ACS 'nin geleceği Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Microsoft Access Control Service yaklaşan değişiklikler](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Bu makalede AAD kimlik doğrulaması ve ilişkili yeni hizmet kayıt anahtarı ayrıntıları ve StorSimple cihazlarına uygun şekilde güvenlik duvarı kurallarında yapılan değişiklikler açıklanmaktadır. Bu makalede yer alan bilgiler yalnızca StorSimple 8000 serisi cihazlar için geçerlidir.

AAD kimlik doğrulaması, güncelleştirme 5 veya üzerini çalıştıran StorSimple 8000 serisi cihazında oluşur. AAD kimlik doğrulamasının tanıtımı nedeniyle, değişiklikler şu şekilde gerçekleşir:

- Güvenlik duvarı kuralları için URL desenleri.
- Hizmet kayıt anahtarı.

Bu değişiklikler, aşağıdaki bölümlerde ayrıntılı olarak ele alınmıştır.

## <a name="url-changes-for-aad-authentication"></a>AAD kimlik doğrulaması için URL değişiklikleri

Hizmetin AAD tabanlı kimlik doğrulaması kullandığından emin olmak için tüm kullanıcıların güvenlik duvarı kurallarında yeni kimlik doğrulama URL 'Lerini içermesi gerekir.

StorSimple 8000 serisini kullanıyorsanız, güvenlik duvarı kurallarında aşağıdaki URL 'nin eklendiğinden emin olun:

| URL kalıbı                         | Bulut | Bileşen/Işlevsellik         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure genel |AAD kimlik doğrulama hizmeti      |
| `https://login.microsoftonline.us` | ABD Devleti |AAD kimlik doğrulama hizmeti      |

StorSimple 8000 serisi cihazların URL desenlerinin tüm listesi için, [güvenlik duvarı kuralları Için URL desenleri](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)' ne gidin.

Kimlik doğrulama URL 'SI, kullanımdan kaldırma tarihinin ötesinde güvenlik duvarı kurallarında yer alıyorsa ve cihaz güncelleştirme 5 çalıştırıyorsa, kullanıcılar bir URL uyarısı görür. Kullanıcıların yeni kimlik doğrulama URL 'sini içermesi gerekir. Cihaz Güncelleştirme 5 ' ten önceki bir sürüm çalıştırıyorsa, kullanıcılar bir sinyal uyarısı görür. Her durumda, StorSimple cihazı hizmetle kimlik doğrulaması yapamaz ve hizmet cihazla iletişim kuramaz.

## <a name="device-version-and-authentication-changes"></a>Cihaz sürümü ve kimlik doğrulama değişiklikleri

StorSimple 8000 serisi bir cihaz kullanıyorsanız, çalıştırdığınız cihaz yazılımı sürümüne bağlı olarak hangi eylemin yapmanız gerektiğini belirlemek için aşağıdaki tabloyu kullanın.

| Cihazınız çalışıyorsa| Aşağıdaki eylemi gerçekleştirin                                    |
|--------------------------|------------------------|
| Güncelleştirme 5 veya üzeri ve cihaz çevrimdışı. <br> URL 'nin beyaz listelenmediğinden bir uyarı görürsünüz.|1. Güvenlik duvarı kurallarını kimlik doğrulama URL 'sini içerecek şekilde değiştirin. Bkz. [kimlik doğrulama URL 'leri](#url-changes-for-aad-authentication).<br>2. [HIZMETTEN AAD kayıt anahtarını alın](#aad-based-registration-keys).<br>3. [StorSimple 8000 serisi cihazının Windows PowerShell arabirimine bağlanın](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Windows `Redo-DeviceRegistration` PowerShell aracılığıyla cihazı kaydetmek için cmdlet 'ini kullanın. Önceki adımda aldığınız anahtarı sağlayın.|
| 5 veya üzeri sürümü ve cihazı çevrimiçi olarak güncelleştirin.| Eylem gerekmiyor.                                       |
| Güncelleştirme 4 veya daha önceki bir sürümü ve cihaz çevrimdışı. |1. Güvenlik duvarı kurallarını kimlik doğrulama URL 'sini içerecek şekilde değiştirin.<br>2. [Katalog sunucusu üzerinden güncelleştirme 5](storsimple-8000-install-update-5.md#download-updates-for-your-device)' i indirin.<br>3. [Düzeltme yöntemi aracılığıyla güncelleştirme 5](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix)' i uygulayın.<br>4. [HIZMETTEN AAD kayıt anahtarını alın](#aad-based-registration-keys).<br>5. [StorSimple 8000 serisi cihazının Windows PowerShell arabirimine bağlanın](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Windows `Redo-DeviceRegistration` PowerShell aracılığıyla cihazı kaydetmek için cmdlet 'ini kullanın. Önceki adımda aldığınız anahtarı sağlayın.|
| Güncelleştirme 4 veya daha önceki bir sürümü ve cihaz çevrimiçi. |Güvenlik duvarı kurallarını kimlik doğrulama URL 'sini içerecek şekilde değiştirin.<br> Azure portal aracılığıyla güncelleştirme 5 ' i yükler.              |
| Güncelleştirme 5 ' den önceki bir sürüme fabrika sıfırlaması.      |Cihaz eski yazılımları çalıştırırken Portal bir AAD tabanlı kayıt anahtarı gösterir. Cihazda güncelleştirme 4 veya daha önceki bir sürümü çalıştırıldığında önceki senaryodaki adımları izleyin.              |

## <a name="aad-based-registration-keys"></a>AAD tabanlı kayıt anahtarları

StorSimple 8000 serisi cihazlar için güncelleştirme 5 ' ten başlayarak yeni AAD tabanlı kayıt anahtarları kullanılır. StorSimple Aygıt Yöneticisi hizmetinizi cihaza kaydetmek için kayıt anahtarlarını kullanırsınız.

Güncelleştirme 4 veya daha önceki bir sürümü çalıştıran bir StorSimple 8000 serisi aygıtı kullanıyorsanız, yeni AAD hizmeti kayıt anahtarlarını kullanamazsınız (şimdi etkinleştirilmiş daha eski bir cihaz içerir).
Bu senaryoda, hizmet kayıt anahtarını yeniden oluşturmanız gerekir. Anahtarı yeniden oluşturduktan sonra, yeni anahtar, sonraki tüm cihazları kaydetmek için kullanılır. Eski anahtar artık geçerli değil.

- Yeni AAD kayıt anahtarının süresi 3 gün sonra dolar.
- AAD kayıt anahtarları yalnızca güncelleştirme 5 veya üzerini çalıştıran StorSimple 8000 serisi cihazlarla çalışır.
- AAD kayıt anahtarları ilgili ACS kayıt anahtarlarından daha uzun.

Bir AAD hizmeti kayıt anahtarı oluşturmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-generate-the-aad-service-registration-key"></a>AAD hizmeti kayıt anahtarını oluşturmak için

1. **StorSimple aygıt yöneticisi**,  **&gt; yönetim** **anahtarlarına**gidin. _Anahtarlar_aramak için arama çubuğunu da kullanabilirsiniz.
    
2. **Anahtar oluştur**' a tıklayın.

    ![Yeniden oluştur 'a tıklayın](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Yeni anahtarı kopyalayın. Eski anahtar artık çalışmayacak.

    ![Yeniden oluşturmayı Onayla](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > StorSimple 8000 serisi cihazınıza kayıtlı hizmette bir StorSimple Cloud Appliance oluşturuyorsanız, oluşturma işlemi devam ederken bir kayıt anahtarı oluşturmamayın. Oluşturma işleminin tamamlanmasını bekleyin ve ardından kayıt anahtarını oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple 8000 serisi cihazının](storsimple-8000-deployment-walkthrough-u2.md)nasıl dağıtılacağı hakkında daha fazla bilgi edinin.

