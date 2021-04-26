---
title: StorSimple parolalarınızı değiştirin | Microsoft Docs
description: StorSimple Snapshot Manager ve Cihaz Yöneticisi parolalarınızı değiştirmek için StorSimple Device Manager hizmetinin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023763"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>StorSimple parolalarınızı değiştirmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış
Azure portal **cihaz ayarları** seçeneği, storsimple Device Manager hizmeti tarafından yönetilen bir StorSimple cihazında yeniden yapılandırabilmeniz için tüm cihaz parametrelerini içerir. Bu öğreticide cihaz yöneticinize veya StorSimple Snapshot Manager parolanızı değiştirmek için **cihaz ayarları** altında **güvenlik** seçeneğini nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="change-the-device-administrator-password"></a>Cihaz yöneticisi parolasını değiştirme
StorSimple cihazına erişmek için Windows PowerShell arabirimini kullandığınızda, bir cihaz yöneticisi parolası girmeniz gerekir. İlk StorSimple cihazı bir hizmetle kaydedildiğinde, bu arabirimin varsayılan parolası *Parola1* olur. Verilerinizin güvenliği için kayıt işleminin sonunda bu parolayı değiştirmeniz gerekir. Bu parolayı değiştirmeden kayıt işleminden çıkabilirsiniz. Daha fazla bilgi için bkz. [3. Adım: cihazı StorSimple için Windows PowerShell aracılığıyla yapılandırma ve kaydetme](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Kayıt sırasında Windows PowerShell arabirimi aracılığıyla ilk olarak ayarlanan parola, Azure portal aracılığıyla daha sonra değiştirilebilir. Cihaz Yöneticisi parolasını değiştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-change-the-device-administrator-password"></a>Cihaz Yöneticisi parolasını değiştirmek için
1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın.

2. Cihazların tablolu listesinden, parolasını değiştirmek istediğiniz cihazı seçin ve tıklatın.

    ![StorSimple Device Manager hizmetini gösteren ekran görüntüsü. Yönetim altında cihazlar seçilidir. Cihaz listesinde, bir cihaz seçilidir.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **Ayarlar** dikey penceresinde **cihaz ayarları > güvenlik**' e gidin.

    ![Device Manager hizmetinin ayarlar dikey penceresini gösteren ekran görüntüsü. Cihaz ayarları altında güvenlik seçilidir.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. **Güvenlik ayarları** dikey penceresinde, Cihaz Yöneticisi parolasını değiştirmek için **parola** ' ya tıklayın.

    ![Güvenlik ayarları dikey penceresini gösteren ekran görüntüsü. Parola düğmesi vurgulanır.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. **Parola** dikey penceresinde 8 ile 15 arasında karakter içeren bir yönetici parolası sağlayın. Parola 3 veya daha fazla büyük harf, küçük harf, sayısal ve özel karakterlerden oluşan bir bileşim olmalıdır.

6. Parola’yı onaylayın.

    ![Parola dikey penceresini gösteren ekran görüntüsü. Cihaz yönetici parolası altında, yeni parola ve Parolayı Onayla kutuları doldurulur.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. **Kaydet** ' e tıklayın ve onay istendiğinde **Evet**' e tıklayın.

    ![Parola dikey penceresini gösteren ekran görüntüsü. Kaydet düğmesi vurgulanır.](./media/storsimple-8000-change-passwords/changepwd6.png)

Cihaz Yöneticisi parolasının Şimdi güncelleştirilmiş olması gerekir. Windows PowerShell arabirimine erişmek için bu değiştirilmiş parolayı kullanabilirsiniz.

## <a name="set-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager parolasını ayarlama
StorSimple Snapshot Manager yazılımı Windows ana bilgisayarınıza bulunur ve yöneticilerin yerel ve bulut anlık görüntüleri biçiminde StorSimple cihazınızın yedeklerini yönetmelerine olanak tanır.

StorSimple Snapshot Manager bir cihazı yapılandırırken, depolama cihazınızın kimlik doğrulaması için cihaz IP adresini ve parolasını sağlamanız istenir.

Azure portal aracılığıyla StorSimple Snapshot Manager için parola ayarlayabilir veya değiştirebilirsiniz. StorSimple Snapshot Manager parolasını ayarlamak veya değiştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager parolasını ayarlamak için
1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın.

2. Cihazların tablolu listesinden, StorSimple Snapshot Manager parolasını belirlemek veya değiştirmek istediğiniz cihaza tıklayın.

     ![StorSimple Device Manager hizmetini gösteren ekran görüntüsü. Yönetim altında cihazlar seçilidir. Cihaz listesinde, bir cihaz seçilidir.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **Ayarlar** dikey penceresinde **cihaz ayarları > güvenlik**' e gidin.

     ![Device Manager hizmetinin ayarlar dikey penceresini gösteren ekran görüntüsü. Cihaz ayarları altında güvenlik seçilidir.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. StorSimple Snapshot Manager parolasını ayarlamak veya değiştirmek için **güvenlik ayarları** dikey penceresinde **parola** ' ya tıklayın.

     ![Güvenlik ayarları dikey penceresini gösteren ekran görüntüsü. Parola düğmesi vurgulanır.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. **Parola** dikey penceresinde 14 veya 15 karakter olan bir parola girin. Parolanın 3 veya daha fazla büyük harf, küçük harf, sayısal ve özel karakter birleşimi içerdiğinden emin olun.

6. Parola’yı onaylayın.

     ![Parola dikey penceresini gösteren ekran görüntüsü. Snapshot Manager parola ' nın altında yeni parola ve Parolayı Onayla kutuları doldurulur.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. **Kaydet** ' e tıklayın ve onay istendiğinde **Evet**' e tıklayın.

     ![Parola dikey penceresini gösteren ekran görüntüsü. Kaydet düğmesi vurgulanır.](./media/storsimple-8000-change-passwords/changepwd6.png)

StorSimple Snapshot Manager parolası şimdi güncellenmelidir.

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple güvenliği](storsimple-8000-security.md)hakkında daha fazla bilgi edinin.
* [Cihaz yapılandırmanızı değiştirme](storsimple-8000-modify-device-config.md)hakkında daha fazla bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.

