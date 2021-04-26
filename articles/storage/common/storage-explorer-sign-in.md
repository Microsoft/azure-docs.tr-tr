---
title: Azure Depolama Gezgini oturum açma | Microsoft Docs
description: Azure Depolama Gezgini oturum açma belgeleri
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568726"
---
# <a name="sign-in-to-storage-explorer"></a>Depolama Gezgini oturum açın

Oturum açma, Azure depolama kaynaklarınıza Depolama Gezgini erişmek için önerilen yoldur. Oturum açarak, RBAC ve Gen2 POSIX ACL 'Leri gibi Azure AD tarafından desteklenen izinlerden yararlanabilirsiniz. 

## <a name="how-to-sign-in"></a>Oturum açma

Depolama Gezgini oturum açmak için **Bağlan iletişim kutusunu** açın. Sol taraftaki dikey araç çubuğundan **Bağlan iletişim kutusunu** açabilir veya **Hesap panelinde** **Hesap Ekle...** seçeneğine tıklayabilirsiniz.

İletişim kutusunu açtıktan sonra, bağlanmak istediğiniz kaynak türü olarak **abonelik** ' ı seçin ve **İleri**' ye tıklayın.

Şimdi hangi Azure ortamını imzalamak istediğinizi seçmeniz gerekir. Azure veya Azure Çin gibi bilinen ortamların herhangi birinden seçim yapabilir veya kendi ortamınızı ekleyebilirsiniz. Ortamınız seçildikten sonra **İleri**' ye tıklayın.

Bu noktada, işletim sistemi **varsayılan Web tarayıcınız** başlatılır ve oturum açma sayfası açılır. En iyi sonuçlar için, Depolama Gezgini kullandığınız veya en azından beklenen MFA 'yı gerçekleştirene kadar bu tarayıcı penceresini açık bırakın. Oturum açmayı bitirdiğinizde Depolama Gezgini ' ye dönebilirsiniz.

## <a name="managing-accounts"></a>Hesapları yönetme

Oturum açmış olduğunuz Azure hesaplarını **Hesap panelinden** yönetebilir ve kaldırabilirsiniz. Sol taraftaki dikey araç çubuğunda **hesapları Yönet** düğmesine tıklayarak **Hesap panelini** açabilirsiniz.

**Hesap panelinde** , oturum açmış olduğunuz hesapları görürsünüz. Her hesap altında şunları yapmanız gerekir:
- Hesabın ait olduğu kiracılar
- Her kiracı için, erişim sahibi olduğunuz abonelikler

Varsayılan olarak, Depolama Gezgini yalnızca giriş kiracınızda oturum açar. Başka bir kiracıdan abonelikleri ve kaynakları görüntülemek istiyorsanız, bu kiracıyı etkinleştirmeniz gerekir. Bir kiracıyı etkinleştirmek için yanındaki onay kutusunu işaretleyin. Bir kiracı ile çalışmayı tamamladıktan sonra devre dışı bırakmak için onay kutusunun işaretini kaldırabilirsiniz. Ana kiracınızı devre dışı bırakamazsınız.

Bir kiracıyı etkinleştirdikten sonra, Depolama Gezgini abonelik yükleyebilmesi veya kaynak kaynaklarına erişebilmek için kimlik bilgilerinizi yeniden girmeniz gerekebilir. Kimlik bilgilerinizi yeniden girmeniz, genellikle Multi-Factor Authentication (MFA) gibi koşullu erişim (CA) ilkesi nedeniyle oluşur. Daha önce başka bir kiracı için MFA yapmış olsanız da bunu yine de yapmanız gerekebilir. Kimlik bilgilerinizi yeniden girmek için **kimlik bilgilerini yeniden girin...** seçeneğine tıklamanız yeterlidir. Ayrıca, aboneliklerin neden yükleyemediğini tam olarak görmek için **hata ayrıntıları...** seçeneğine de tıklayabilirsiniz.

Abonelikleriniz yüklendikten sonra, onay kutularını işaretleyerek veya denetleyerek hangi filtreleri filtrelemek istediğinizi seçebilirsiniz.

Tüm Azure hesabınızı kaldırmak istiyorsanız hesabın yanındaki **Kaldır** ' a tıklayın.

## <a name="changing-where-sign-in-happens"></a>Oturum açma işleminin nerede olacağını değiştirme

Varsayılan olarak oturum açma, işletim sisteminin **varsayılan Web tarayıcısında** gerçekleşir. Varsayılan Web tarayıcınızla oturum açmak, MFA gibi CA ilkeleri aracılığıyla güvenli hale getirilmiş kaynaklara erişme şeklini kolaylaştırır. Bazı nedenlerle işletim sisteminin **varsayılan Web tarayıcısı** çalışmıyorsa, oturum açma işlemini Depolama Gezgini veya nasıl gerçekleştireceğini değiştirebilirsiniz.

**Ayarlar**  >  **uygulama**  >  **oturum açma** bölümünde, **oturum açma** ayarına bakın. Üç seçenek vardır:
- **Varsayılan Web tarayıcısı**: oturum açma, işletim sisteminin **varsayılan Web tarayıcınızda** gerçekleşir. Bu seçenek önerilir.
- **Tümleşik oturum açma**: oturum açma, Depolama Gezgini bir pencerede gerçekleşir. Aynı anda birden çok Microsoft hesabı (MSAs) ile oturum açmaya çalışıyorsanız bu seçenek yararlı olabilir. Bu seçeneği belirlerseniz bazı CA ilkeleriyle ilgili sorunlarla karşılaşabilirsiniz.
- **Cihaz kodu akışı**: Depolama Gezgini bir tarayıcı penceresine girebileceğiniz bir kod verecektir. Bu seçenek önerilmez. Cihaz kod akışı, birçok CA ilkesiyle uyumlu değildir.

## <a name="troubleshooting-sign-in-issues"></a>Oturum açma sorunlarını giderme

Oturum açarken sorun yaşıyorsanız veya oturum açtıktan sonra bir Azure hesabıyla ilgili sorun yaşıyorsanız, [Depolama Gezgini sorun giderme kılavuzunun oturum açma bölümüne](./storage-explorer-troubleshooting.md#sign-in-issues)bakın.
