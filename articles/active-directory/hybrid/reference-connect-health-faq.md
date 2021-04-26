---
title: Azure Active Directory Connect Health SSS-Azure | Microsoft Docs
description: Bu SSS Azure AD Connect Health hakkındaki soruları yanıtlar. Bu SSS; faturalama modeli, özellikler, sınırlamalar ve destek dahil olmak üzere hizmetin kullanımı hakkındaki soruları kapsar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368005"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health sık sorulan sorular
Bu makale, Azure Active Directory (Azure AD) Connect Health hakkında sık sorulan soruların (SSS) yanıtlarını içerir. Bu SSS, faturalandırma modeli, yetenekler, sınırlamalar ve destek dahil olmak üzere hizmetin nasıl kullanılacağına ilişkin soruları kapsar.

## <a name="general-questions"></a>Genel sorular
**S: birden çok Azure AD dizinini yönetiyorum. Nasıl yaparım? Azure Active Directory Premium birine geçiş yapılsın mı?**

Farklı Azure AD kiracılar arasında geçiş yapmak için sağ üst köşedeki şu anda oturum açmış olan **Kullanıcı adını** seçin ve ardından uygun hesabı seçin. Hesap burada listelenmiyorsa **oturumu** Kapat ' ı seçin ve oturum açmak için Azure Active Directory Premium (P1 veya P2) etkin olan dizinin genel yönetici kimlik bilgilerini kullanın.

**S: Azure AD Connect Health tarafından desteklenen kimlik rollerinin sürümü nedir?**

Aşağıdaki tabloda roller ve desteklenen işletim sistemi sürümleri listelenmiştir.

|Rol| İşletim sistemi/sürüm|
|--|--|
|Active Directory Federasyon Hizmetleri (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Sürüm 1.0.9125 veya üzeri|
|Active Directory Etki Alanı Hizmetleri (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Windows Server Çekirdeği yüklemeleri desteklenmez.

Hizmet tarafından sunulan özelliklerin role ve işletim sistemine göre farklılık gösterebileceğini unutmayın. Diğer bir deyişle, tüm özellikler tüm işletim sistemi sürümleri için kullanılamayabilir. Ayrıntılar için bkz. Özellik açıklamaları.

**S: altyapımı izlemek için kaç lisansa ihtiyacım var?**

* İlk Connect Health Aracısı için en az bir Azure AD Premium (P1 veya P2) lisansı gerekir.
* Her ek kayıtlı aracı 25 ek Azure AD Premium (P1 veya P2) lisansı gerektirir.
* Aracı sayısı, tüm izlenen roller (AD FS, Azure AD Connect ve/veya AD DS) içinde kayıtlı olan aracıların toplam sayısına eşdeğerdir.
* AAD Connect Health lisanslama, lisansı belirli kullanıcılara atamanız gerekmez. Yalnızca gerekli sayıda lisansa sahip olmanız gerekir.

Lisanslama bilgileri de [Azure AD fiyatlandırma sayfasında](https://aka.ms/aadpricing)bulunur.

Örnek:

| Kayıtlı aracılar | Gerekli lisanslar | Örnek izleme yapılandırması |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect sunucusu |
| 2 | 26| 1 Azure AD Connect Server ve 1 etki alanı denetleyicisi |
| 3 | 51 | 1 Active Directory Federasyon Hizmetleri (AD FS) (AD FS) sunucu, 1 AD FS proxy ve 1 etki alanı denetleyicisi |
| 4 | 76 | 1 AD FS Server, 1 AD FS proxy ve 2 etki alanı denetleyicisi |
| 5 | 101 | 1 Azure AD Connect sunucu, 1 AD FS sunucu, 1 AD FS proxy ve 2 etki alanı denetleyicisi |

**S: Azure AD Connect Health Azure Almanya bulutunu destekliyor mu?**

[Eşitleme hataları raporu özelliği](how-to-connect-health-sync.md#object-level-synchronization-error-report)dışında Almanya bulutu 'nda Azure AD Connect Health desteklenmez.

| Roller | Özellikler | Almanya bulutu 'nda destekleniyor |
| ------ | --------------- | --- |
| Eşitleme için Health Connect | İzleme/öngörü/uyarılar/analiz | No |
|  | Eşitleme hata raporu | Yes |
| ADFS için Connect Health | İzleme/öngörü/uyarılar/analiz | No |
| EKLER için Connect Health | İzleme/öngörü/uyarılar/analiz | No |

Eşitleme için bağlantı durumunun aracı bağlantısının olduğundan emin olmak için lütfen [yükleme gereksinimini](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) uygun şekilde yapılandırın.

## <a name="installation-questions"></a>Yükleme soruları

**S: Azure AD Connect Health aracısını ayrı sunuculara yüklemenin etkisi nedir?**

Microsoft Azure AD Connect Health aracısını yükleme, AD FS, Web uygulaması ara sunucusu, Azure AD Connect (eşitleme) sunucuları, etki alanı denetleyicileri CPU, bellek tüketimi, ağ bant genişliği ve depolama açısından en düşük düzeydedir.

Aşağıdaki sayılar bir yaklaşık olarak verilmiştir:

* CPU tüketimi:% 1-5 artış.
* Bellek tüketimi: toplam sistem belleğinin %10 ' a kadar.

> [!NOTE]
> Aracı Azure ile iletişim kuramıyorsa, aracı verileri tanımlanan bir maksimum sınır için yerel olarak depolar. Aracı "önbelleğe alınmış" verilerin üzerine "en az son bakım" temelinde yazar.
>
>

* Azure AD Connect Health aracıları için yerel arabellek depolaması: ~ 20 MB.
* AD FS sunucular için, Azure AD Connect Health aracıların üzerine yazılmadan önce tüm denetim verilerini işlemesi için AD FS denetim kanalı için 1.024 MB (1 GB) disk alanı sağlamanızı öneririz.

**S: Azure AD Connect Health aracılarının yüklenmesi sırasında sunucularımı yeniden başlatmalıyım mıyım?**

Hayır. Aracıların yüklenmesi, sunucuyu yeniden başlatmanızı gerektirmez. Ancak, bazı önkoşul adımları yüklenirken sunucunun yeniden başlatılması gerekebilir.

Örneğin, Windows Server 2008 R2 'de, .NET 4,5 Framework 'ün yüklenmesi için sunucu yeniden başlatması gerekir.

**S: Azure AD Connect Health bir geçişli HTTP proxy 'si üzerinden çalışıyor mu?**

Evet. Devam eden işlemler için, durum aracısını giden HTTP isteklerini iletmek üzere bir HTTP proxy 'si kullanacak şekilde yapılandırabilirsiniz.
[Sistem durumu aracıları IÇIN http proxy 'yi yapılandırma](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)hakkında daha fazla bilgi edinin.

Aracı kaydı sırasında bir proxy yapılandırmanız gerekiyorsa, Internet Explorer proxy ayarlarınızı önceden değiştirmeniz gerekebilir.

1. Internet Explorer > **ayarları**  >  **Internet seçenekleri**  >  **bağlantıları**  >  **LAN ayarları**' nı açın.
2. **LAN için bir proxy sunucusu kullan**' ı seçin.
3. HTTP ve HTTPS/Secure için farklı proxy bağlantı noktalarına sahipseniz **Gelişmiş** ' i seçin.

**S: Azure AD Connect Health, HTTP proxy 'lerine bağlanırken temel kimlik doğrulamasını destekliyor mu?**

Hayır. Temel kimlik doğrulaması için rastgele bir Kullanıcı adı ve parola belirtme mekanizması şu anda desteklenmiyor.

**S: Azure AD Connect Health aracısının çalışması için hangi güvenlik duvarı bağlantı noktalarını açmanız gerekir?**

Güvenlik Duvarı bağlantı noktaları ve diğer bağlantı gereksinimlerinin listesi için [gereksinimler bölümüne](how-to-connect-health-agent-install.md#requirements) bakın.

**S: neden Azure AD Connect Health portalında aynı ada sahip iki sunucu görüyorum?**

Bir aracıyı sunucudan kaldırdığınızda, sunucu Azure AD Connect Health portalından otomatik olarak kaldırılmaz. Bir aracıyı sunucudan el ile kaldırırsanız veya sunucunun kendisini kaldırırsanız, Azure AD Connect Health portalından sunucu girişini el ile silmeniz gerekir.

Bir sunucuyu yeniden yansıedebilir veya aynı ayrıntılarla (makine adı gibi) yeni bir sunucu oluşturabilirsiniz. Azure AD Connect Health portalından zaten kayıtlı olan sunucuyu kaldırmadıysanız ve aracıyı yeni sunucuya yüklediyseniz, aynı ada sahip iki giriş görebilirsiniz.

Bu durumda, eski sunucuya ait girişi el ile silin. Bu sunucunun verileri güncel değil.

**S: Windows Server Core 'a Azure AD Connect sistem durumu aracısını yükleyebilir miyim?**

Hayır.  Sunucu Çekirdeği üzerine yükleme desteklenmiyor.

## <a name="health-agent-registration-and-data-freshness"></a>Sistem Durumu Aracısı kaydı ve veri yeniliği

**S: sistem durumu aracısı kayıt hatalarının genel nedenleri nelerdir ve sorunları nasıl giderebilirim?**

Sistem Durumu Aracısı, aşağıdaki olası nedenlerden dolayı kayıt gerçekleştiremeyebilir:

* Bir güvenlik duvarı trafiği engellediği için aracı gerekli uç noktalarla iletişim kuramıyor. Bu özellikle Web uygulaması ara sunucuları üzerinde ortaktır. Gerekli uç noktalara ve bağlantı noktalarına giden iletişimin izin verildiğinden emin olun. Ayrıntılar için [gereksinimler bölümüne](how-to-connect-health-agent-install.md#requirements) bakın.
* Giden iletişim, ağ katmanı tarafından bir TLS denetimine tabi. Bu, aracının kullandığı sertifikanın İnceleme sunucusu/varlıkla değiştirilmesini ve aracı kaydını tamamlamaya yönelik adımların başarısız olmasına neden olur.
* Kullanıcının aracının kaydını gerçekleştirme erişimi yok. Genel yöneticilerin varsayılan olarak erişimi vardır. Diğer kullanıcılara erişim yetkisi vermek için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) kullanabilirsiniz.

**S: "Sistem Sağlığı Hizmeti veri güncel değil." olarak uyarı alıyorum. Nasıl yaparım? sorun gidermi?**

Azure AD Connect Health, son iki saat içinde sunucudan tüm veri noktalarını almadığında uyarıyı oluşturur. [Daha fazla bilgi edinin](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>İşlem soruları
**S: Web uygulaması ara sunucusu sunucularında denetimi etkinleştirmem gerekir mi?**

Hayır, denetimin Web uygulaması ara sunucusu 'nda etkinleştirilmesi gerekmez.

**S: Azure AD Connect Health Uyarıları nasıl çözümlenir?**

Başarılı bir koşulda Azure AD Connect Health uyarılar çözüldü. Azure AD Connect Health aracıları hizmet için başarı koşullarını düzenli aralıklarla algılar ve bildirir. Birkaç uyarı için gizleme zaman tabanlıdır. Diğer bir deyişle, aynı hata durumu uyarı oluşturma işleminden 72 saat içinde gözlemlenmiyorsa, uyarı otomatik olarak çözümlenir.

**S: "test kimlik doğrulama Isteğinin (yapay Işlem) bir belirteci alamaması gerektiğini belirten bir uyarı alıyorum." Nasıl yaparım? sorun gidermi?**

AD FS için Azure AD Connect Health, bir AD FS sunucuya yüklenen sistem durumu Aracısı, sistem durumu Aracısı tarafından başlatılan yapay bir işlemin parçası olarak bir belirteci edinemediğinde bu uyarıyı oluşturur. Sistem Durumu Aracısı yerel sistem bağlamını kullanır ve kendi kendine bağlı olan taraf için bir belirteç almaya çalışır. Bu, AD FS, bir yakalama belirteçleri durumunda olduğundan emin olmak için bir catch-all sınamadır.

Çoğu zaman bu test başarısız olur çünkü sistem durumu Aracısı AD FS grubu adını çözemiyor. Bu durum, AD FS sunucuları bir ağ yükü dengeleyicileri arkasındaysa ve istek yük dengeleyicinin arkasındaki bir düğümden başlatıldığında (yük dengeleyicinin önünde olan normal bir istemcinin aksine) gerçekleşebilir. Bu, "C:\Windows\System32\drivers\etc" altında bulunan "konaklar" dosyası AD FS sunucusunun IP adresini veya AD FS grubu adı (sts.contoso.com gibi) için bir geri döngü IP adresi (127.0.0.1) içerecek şekilde güncelleştirilerek düzeltilebilir. Ana bilgisayar dosyasının eklenmesi, ağ çağrısını kısa devre dışı kalacak ve bu sayede sistem durumu aracısının belirteci almasını sağlar.

**S: makinelerimin son fidye yazılımı saldırıları için düzeltme eki UYGULAMADıĞıNı belirten bir e-posta aldım. Bu e-postayı neden aldım?**

Azure AD Connect Health hizmeti, gereken düzeltme eklerinin yüklendiğinden emin olmak için izlediği tüm makineleri taradı. En az bir makine kritik düzeltme eklerine sahip değilse, e-posta Kiracı yöneticilerine gönderilmiştir. Bu belirlenmesi için aşağıdaki mantık kullanılmıştır.
1. Makinede yüklü olan tüm düzeltmeleri bulun.
2. Tanımlı listedeki düzeltmelerin en az birinin mevcut olup olmadığını denetleyin.
3. Yanıt Evet ise, makine korunur. Aksi takdirde, makine saldırıya karşı risk altında olur.

Bu denetimi el ile gerçekleştirmek için aşağıdaki PowerShell betiğini kullanabilirsiniz. Yukarıdaki mantığı uygular.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**S: <i>Get-MsolDirSyncProvisioningError</i> PowerShell cmdlet 'i neden sonuçta daha az eşitleme hatası gösteriyor?**

<i>Get-MsolDirSyncProvisioningError</i> , yalnızca DirSync sağlama hataları döndürür. Bunun yanı sıra, Connect Health portalı dışarı aktarma hataları gibi diğer eşitleme hata türlerini de gösterir. Bu, Azure AD Connect Delta sonucuyla tutarlıdır. [Azure AD Connect Eşitleme hataları](./tshoot-connect-sync-errors.md)hakkında daha fazla bilgi edinin.

**S: ADFS denetimlerim neden üretilmiyor?**

Denetim günlüklerinin devre dışı durumda olmadığından emin olmak için lütfen <i>Get-AdfsProperties-AuditLevel</i> PowerShell cmdlet 'ini kullanın. [ADFS denetim günlükleri](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)hakkında daha fazla bilgi edinin. ADFS sunucusuna Gelişmiş denetim ayarları itilmesi durumunda, auditpol.exe olan tüm değişikliklerin üzerine yazılır (Eğer uygulama oluşturulmadıkça olay). Bu durumda, uygulama tarafından üretilen hataların ve başarısını günlüğe kaydetmek için lütfen yerel güvenlik ilkesini ayarlayın.

**S: Aracı sertifikası, süresi dolmadan önce otomatik olarak yenilenir mi?**
Aracı sertifikası, sona erme tarihinden itibaren otomatik olarak **6 ay** yenilenir. Yenilenmezse, lütfen aracının ağ bağlantısının kararlı olduğundan emin olun. Aracı hizmetlerini yeniden başlatın veya en son sürüme güncelleştirme de sorunu çözebilir.



## <a name="related-links"></a>İlgili bağlantılar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Aracısı yüklemesi](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health işlemler](how-to-connect-health-operations.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Eşitleme için Azure AD Connect Health kullanma](how-to-connect-health-sync.md)
* [Azure AD Connect Health'i AD DS ile Kullanma](how-to-connect-health-adds.md)
* [Azure AD Connect Health sürüm geçmişi](reference-connect-health-version-history.md)
