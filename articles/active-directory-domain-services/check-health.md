---
title: Azure Active Directory Domain Services durumunu denetleme | Microsoft Docs
description: Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanının sistem durumunu denetleme ve Azure portal kullanarak durum iletilerini anlama hakkında bilgi edinin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620333"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanının durumunu denetleme

Azure Active Directory Domain Services (Azure AD DS), yönetilen etki alanını sağlıklı ve güncel tutmak için bazı arka plan görevleri çalıştırır. Bu görevler, yedeklemeleri almayı, güvenlik güncelleştirmelerini uygulamayı ve Azure AD 'den verileri eşitlemeyi içerir. Azure AD DS yönetilen etki alanı ile ilgili sorunlar varsa, bu görevler başarıyla tamamlanmayabilir. Sorunları gözden geçirmek ve çözmek için Azure portal kullanarak yönetilen bir etki alanının sistem durumunu kontrol edebilirsiniz.

Bu makalede, Azure AD DS sistem durumunu görüntüleme ve gösterilen bilgileri ya da Uyarıları anlama konusu gösterilmektedir.

## <a name="view-the-health-status"></a>Sistem durumunu görüntüleme

Yönetilen bir etki alanının sistem durumu Azure portal kullanılarak görüntülenir. Son yedekleme zamanı ve Azure AD ile eşitleme hakkında bilgi, yönetilen etki alanının sistem durumuyla ilgili bir sorunu belirten uyarılarla birlikte görülebilir. Yönetilen bir etki alanının sistem durumunu görüntülemek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin.
1. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Azure AD DS kaynak penceresinin sol tarafında **sistem durumu**' nu seçin. Aşağıdaki örnek ekran görüntüsünde sağlıklı olarak yönetilen bir etki alanı ve son yedekleme ve Azure AD eşitleme 'nin durumu gösterilmektedir:

    ![Azure Active Directory Domain Services durumunu gösteren Azure portal durum sayfasına genel bakış](./media/check-health/health-page.png)

Sistem durumu sayfasının *en son değerlendirilen* zaman damgası, yönetilen etki alanının en son ne zaman denetlendiğini gösterir. Yönetilen bir etki alanının sistem durumu her saat değerlendirilir. Yönetilen bir etki alanında herhangi bir değişiklik yaparsanız, güncelleştirilmiş sistem durumunu görüntülemek için bir sonraki değerlendirme döngüsüne kadar bekleyin.

Sağ üst köşedeki durum, yönetilen etki alanının genel durumunu gösterir. Durum, etki alanındaki tüm mevcut uyarıları çarpanlar. Aşağıdaki tabloda, kullanılabilir durum göstergelerinin ayrıntıları verilmiştir:

| Durum | Simge | Açıklama |
| --- | :----: | --- |
| Çalışma | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Yönetilen etki alanı doğru şekilde çalışıyor ve kritik veya uyarı uyarıları yok. Etki alanında bilgilendirici uyarılar olabilir. |
| İlgilenilmesi gerekiyor (uyarı) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Yönetilen etki alanında kritik uyarı yok, ancak giderilmesi gereken bir veya daha fazla uyarı uyarısı var. |
| İlgilenilmesi gerekiyor (kritik) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Yönetilen etki alanında giderilmesi gereken bir veya daha fazla kritik uyarı vardır. Ayrıca uyarı ve/veya bilgilendirici uyarılarınız olabilir. |
| Dağıtılmasını | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Yönetilen etki alanı dağıtılıyor. |

## <a name="understand-monitors-and-alerts"></a>İzleyicileri ve Uyarıları anlama

Yönetilen bir etki alanının sistem durumu, iki tür bilgi *İzleyicisi* ve *Uyarı* gösterir. İzleyiciler, temel arka plan görevlerinin tamamlandığı zamanı gösterir. Uyarılar, yönetilen etki alanının kararlılığını geliştirmek için bilgi veya öneriler sağlar.

### <a name="monitors"></a>İzleyiciler

İzleyiciler, düzenli olarak denetlenen bir yönetilen etki alanının alanlarıdır. Yönetilen etki alanı için etkin bir uyarı varsa, bu durum izleyicilerinden birinin bir sorunu rapor vermesine neden olabilir. Azure AD DS Şu anda aşağıdaki alanların izleyicilerine sahiptir:

* Backup
* Azure AD ile eşitleme

#### <a name="backup-monitor"></a>Yedekleme İzleyicisi

Yedekleme İzleyicisi, yönetilen etki alanının otomatik düzenli yedeklerinin başarıyla çalıştırıldığını denetler. Aşağıdaki tabloda, kullanılabilir yedekleme İzleyicisi durumunun ayrıntıları verilmiştir:

| Ayrıntı değeri | Açıklama |
| --- | --- |
| Hiç yedeklenmedi | Bu durum, yeni yönetilen etki alanları için normaldir. Yönetilen etki alanı dağıtıldıktan sonra ilk yedeklemenin 24 saat oluşturulması gerekir. Bu durum devam ederse, [bir Azure destek isteği açın][azure-support]. |
| Son yedekleme 1-14 gün önce gerçekleştirildi | Bu zaman aralığı, yedekleme izleyicisinin beklenen durumudur. Otomatikleştirilmiş düzenli yedeklemeler bu dönemde gerçekleşmelidir. |
| Son yedekleme 14 günden daha önce alındı. | İki haftadan daha uzun bir zaman aralığı, otomatik normal yedeklemelerle ilgili bir sorun olduğunu gösterir. Etkin kritik uyarılar, yönetilen etki alanının yedeklenmesini engelliyor olabilir. Yönetilen etki alanı için tüm etkin uyarıları çözün. Yedekleme izleyicisi daha sonra son yedeği raporlamak için durumu güncelleştirmezse [bir Azure destek isteği açın][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD İzleyicisi ile eşitleme

Yönetilen bir etki alanı Azure Active Directory ile düzenli olarak eşitlenir. Kullanıcı ve grup nesnelerinin sayısı ve son eşitlemeden sonra Azure AD dizininde yapılan değişikliklerin sayısı, eşitlemenin ne kadar sürdüğünü etkiler. Yönetilen etki alanı en son üç gün önce eşitleniyorsa, etkin uyarıları denetleyin ve çözün. Eşitleme İzleyicisi, etkin uyarıları adresledikten sonra son eşitlemeyi göstermek için durumu güncelleştirmezse [bir Azure destek isteği açın][azure-support].

### <a name="alerts"></a>Uyarılar

Yönetilen bir etki alanındaki, hizmetin düzgün çalışması için giderilmesi gereken sorunlar için uyarılar oluşturulur. Her uyarı sorunu açıklar ve sorunu çözmek için belirli adımları özetleyen bir URL sağlar. Olası uyarılar ve çözümleri hakkında daha fazla bilgi için bkz. [sorun giderme uyarıları](troubleshoot-alerts.md).

Sistem durumu uyarıları aşağıdaki önem derecelerine göre kategorize edilir:

 * **Kritik uyarılar** , yönetilen etki alanını ciddi bir şekilde etkileyen sorunlardır. Bu uyarıların hemen giderilmesi gerekir. Azure platformu, sorunlar çözülene kadar yönetilen etki alanını izleyemez, yönetemez, düzeltme eki ve eşitleyemiyor.
 * **Uyarı uyarıları** , sorun devam ederse yönetilen etki alanı işlemlerini etkileyebilecek sorunları bilgilendirir. Bu uyarılar, yönetilen etki alanının güvenliğini sağlamaya yönelik öneriler de sunmaktadır.
 * **Bilgilendirici uyarılar** , yönetilen etki alanını olumsuz şekilde etkilemeyecek bildirimlerdir. Bilgilendirici uyarılar, yönetilen etki alanında neler olduğunu gösteren bazı Öngörüler sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Sistem durumu sayfasında gösterilen uyarılar hakkında daha fazla bilgi için bkz. [yönetilen etki alanındaki uyarıları çözümleme][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
