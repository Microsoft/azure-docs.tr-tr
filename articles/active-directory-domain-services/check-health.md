---
title: Azure Active Directory Domain Services durumunu denetleme | Microsoft Docs
description: Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanının sistem durumunu denetleme ve Azure portal kullanarak durum iletilerini anlama hakkında bilgi edinin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 50b142acb457d16abeb24f22d56b653a38aca76d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898269"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanının durumunu denetleme

Azure Active Directory Domain Services (Azure AD DS), yönetilen etki alanını sağlıklı ve güncel tutmak için bazı arka plan görevleri çalıştırır. Bu görevler, yedeklemeleri almayı, güvenlik güncelleştirmelerini uygulamayı ve Azure AD 'den verileri eşitlemeyi içerir. Azure AD DS yönetilen etki alanı ile ilgili sorunlar varsa, bu görevler başarıyla çalışmayabilir. Sorunları gözden geçirmek ve çözümlemek için Azure portal kullanarak Azure AD DS yönetilen bir etki alanının sistem durumunu kontrol edebilirsiniz.

Bu makalede, Azure AD DS sistem durumunun nasıl görüntüleneceği ve gösterilen bilgilerin veya uyarıların nasıl anlaşılması gösterilmektedir.

## <a name="view-the-health-status"></a>Sistem durumunu görüntüleme

Azure AD DS yönetilen bir etki alanının sistem durumu Azure portal kullanılarak görüntülenir. Son yedekleme zamanı ve Azure AD ile eşitleme hakkında bilgi, yönetilen etki alanının sistem durumuyla ilgili bir sorunu belirten uyarılarla birlikte görülebilir. Azure AD DS yönetilen bir etki alanının sistem durumunu görüntülemek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services**seçin.
1. *Contoso.com*gibi Azure AD DS yönetilen etki alanınızı seçin.
1. Azure AD DS kaynak penceresinin sol tarafında **sistem durumu**' nu seçin. Aşağıdaki örnek ekran görüntüsünde, sağlıklı bir Azure AD DS yönetilen etki alanı ve son yedekleme ve Azure AD eşitleme 'nin durumu gösterilmektedir:

    ![Azure Active Directory Domain Services durumunu gösteren Azure portal durum sayfasına genel bakış](./media/check-health/health-page.png)

Sistem durumu sayfasının *en son değerlendirilen* zaman damgası, Azure AD DS yönetilen etki alanının son ne zaman denetlendiğini gösterir. Azure AD DS yönetilen bir etki alanının sistem durumu her saat değerlendirilir. Azure AD DS yönetilen bir etki alanında herhangi bir değişiklik yaparsanız, güncelleştirilmiş sistem durumunu görüntülemek için bir sonraki değerlendirme döngüsüne kadar bekleyin.

Sağ üst köşedeki durum, Azure AD DS yönetilen etki alanının genel durumunu gösterir. Durum, etki alanındaki tüm mevcut uyarıları çarpanlar. Aşağıdaki tabloda, kullanılabilir durum göstergelerinin ayrıntıları verilmiştir:

| Durum | Simge | Açıklama |
| --- | :----: | --- |
| Çalışıyor | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS yönetilen etki alanı düzgün çalışıyor ve kritik veya uyarı uyarıları yok. Etki alanında bilgilendirici uyarılar olabilir. |
| İlgilenilmesi gerekiyor (uyarı) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS yönetilen etki alanında kritik uyarı yoktur, ancak giderilmesi gereken bir veya daha fazla uyarı uyarısı vardır. |
| İlgilenilmesi gerekiyor (kritik) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS yönetilen etki alanında giderilmesi gereken bir veya daha fazla kritik uyarı vardır. Ayrıca uyarı ve/veya bilgilendirici uyarılarınız olabilir. |
| Dağıtılıyor | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Azure AD DS etki alanı dağıtılıyor. |

## <a name="understand-monitors-and-alerts"></a>İzleyicileri ve Uyarıları anlama

Azure AD DS yönetilen bir etki alanının sistem durumu, iki tür bilgi İzleyicisi ve uyarı gösterir. İzleyiciler, temel arka plan görevlerinin tamamlandığı zamanı gösterir. Uyarılar, yönetilen etki alanının kararlılığını geliştirmek için bilgi veya öneriler sağlar.

### <a name="monitors"></a>İzleyiciler

İzleyiciler, düzenli olarak denetlenen bir Azure AD DS yönetilen etki alanının alanlarıdır. Azure AD DS yönetilen etki alanı için etkin uyarılar varsa, bu durum izleyicilerinden birinin bir sorunu rapor vermesine neden olabilir. Azure AD Domain Services Şu anda aşağıdaki alanların izleyicilerini izler:

* Yedekle
* Azure AD ile eşitleme

#### <a name="backup-monitor"></a>Yedekleme İzleyicisi

Yedekleme İzleyicisi, Azure AD DS yönetilen etki alanının otomatik düzenli yedeklerinin başarıyla çalıştırıldığını denetler. Aşağıdaki tabloda, kullanılabilir yedekleme İzleyicisi durumunun ayrıntıları verilmiştir:

| Ayrıntı değeri | Açıklama |
| --- | --- |
| Hiç yedeklenmedi | Bu durum, yeni Azure AD DS yönetilen etki alanları için normaldir. İlk yedeklemenin Azure AD DS yönetilen etki alanı dağıtıldıktan sonra 24 saat oluşturulması gerekir. Bu durum devam ederse, [bir Azure destek isteği açın][azure-support]. |
| Son yedekleme 1-14 gün önce gerçekleştirildi | Bu zaman aralığı, yedekleme izleyicisinin beklenen durumudur. Otomatikleştirilmiş düzenli yedeklemeler bu dönemde gerçekleşmelidir. |
| Son yedekleme 14 günden daha önce alındı. | İki haftadan daha uzun bir zaman aralığı, otomatik normal yedeklemelerle ilgili bir sorun olduğunu gösterir. Etkin kritik uyarılar Azure AD DS yönetilen etki alanının yedeklenmesini engelleyebilir. Azure AD DS yönetilen etki alanı için etkin uyarıları çözün. Yedekleme izleyicisi daha sonra son yedeği raporlamak için durumu güncelleştirmezse [bir Azure destek isteği açın][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD İzleyicisi ile eşitleme

Azure AD DS yönetilen bir etki alanı Azure Active Directory ile düzenli aralıklarla eşitlenir. Kullanıcı ve grup nesnelerinin sayısı ve son eşitlemeden sonra Azure AD dizininde yapılan değişikliklerin sayısı, eşitlemenin ne kadar sürdüğünü etkiler. Azure AD DS yönetilen etki alanı en son üç gün önce eşitlendiğinde, etkin uyarıları denetleyin ve çözün. Eşitleme izleyicisi daha sonra son eşitleme göstermek için durumu güncelleştirmezse [bir Azure destek isteği açın][azure-support].

### <a name="alerts"></a>Uyarılar

Azure AD DS yönetilen bir etki alanındaki, hizmetin düzgün çalışması için giderilmesi gereken sorunlar için uyarılar oluşturulur. Her uyarı sorunu açıklar ve sorunu çözmek için belirli adımları özetleyen bir URL sağlar. Olası uyarılar ve çözümleri hakkında daha fazla bilgi için bkz. [sorun giderme uyarıları](troubleshoot-alerts.md).

Sistem durumu uyarıları aşağıdaki önem derecelerine göre kategorize edilir:

 * **Kritik uyarılar** , Azure AD DS yönetilen etki alanını ciddi bir şekilde etkileyen sorunlardır. Bu uyarıların hemen giderilmesi gerekir. Azure platformu, sorunlar çözülene kadar yönetilen etki alanını izleyemez, yönetemez, düzeltme eki ve eşitleyemiyor.
 * **Uyarı uyarıları** , sorun devam ederse Azure AD DS yönetilen etki alanı işlemlerini etkileyebilecek sorunları bilgilendirir. Bu uyarılar, yönetilen etki alanının güvenliğini sağlamaya yönelik öneriler de sunmaktadır.
 * **Bilgilendirici uyarılar** , Azure AD DS yönetilen etki alanını olumsuz yönde etkilemeyecek bildirimlerdir. Bilgilendirici uyarılar, yönetilen etki alanında neler olduğunu gösteren bazı Öngörüler sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Sistem durumu sayfasında gösterilen uyarılar hakkında daha fazla bilgi için bkz. [yönetilen etki alanındaki uyarıları çözümleme][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
