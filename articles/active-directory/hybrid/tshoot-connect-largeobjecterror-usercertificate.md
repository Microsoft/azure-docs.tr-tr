---
title: UserCertificate özniteliği nedeniyle Azure AD Connect LargeObject hataları | Microsoft Docs
description: Bu konuda, userCertificate özniteliğinin neden olduğu LargeObject hatalarına yönelik düzeltme adımları sağlanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861757"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect Sync: Kullanıcısertifikası özniteliği nedeniyle LargeObject hatalarını Işleme

Azure AD, **userCertificate** özniteliğinde en fazla **15** sertifika değeri sınırı uygular. Azure AD Connect, 15 ' ten fazla değeri olan bir nesneyi Azure AD 'ye dışarı aktardığında, Azure AD şu iletiyle birlikte bir **LargeObject** hatası döndürür:

>*"Sağlanan nesne çok büyük. Bu nesnedeki öznitelik değerlerinin sayısını kırpın. İşlem bir sonraki eşitleme çevriminde yeniden denenecek... "*

LargeObject hatası başka AD özniteliklerinin kaynaklanmış olabilir. Bunun gerçekten userCertificate özniteliği nedeniyle kaynaklanmadığını doğrulamak için, şirket içi AD 'de veya [Synchronization Service Manager meta](./how-to-connect-sync-service-manager-ui-mvsearch.md)veri deposu aramasında nesneye karşı doğrulamanız gerekir.

Kiracınızdaki nesnelerin listesini LargeObject hatalarıyla birlikte almak için aşağıdaki yöntemlerden birini kullanın:

 * Kiracınız eşitleme için Azure AD Connect Health etkinleştirilmişse, sağlanan [eşitleme hata raporuna](./how-to-connect-health-sync.md) başvurabilirsiniz.
 
 * Her eşitleme döngüsünün sonunda gönderilen Dizin Eşitleme hataları için bildirim e-postası, LargeObject hatalarıyla birlikte nesnelerin listesini içerir. 
 * Azure AD 'ye en son dışarı aktarma işlemine tıkladığınızda, [Synchronization Service Manager işlemler sekmesi](./how-to-connect-sync-service-manager-ui-operations.md) LargeObject hatalarıyla birlikte nesnelerin listesini görüntüler.
 
## <a name="mitigation-options"></a>Risk azaltma seçenekleri
LargeObject hatası çözümlenene kadar, aynı nesneye yapılan diğer öznitelik değişiklikleri Azure AD 'ye verilemez. Hatayı gidermek için aşağıdaki seçenekleri göz önünde bulundurun:

 * Azure AD Connect 1.1.524.0 veya sonraki bir sürüme yükseltin. Azure AD Connect Build 1.1.524.0 içinde, kullanıma hazır eşitleme kuralları, özniteliklerin 15 ' ten fazla değeri varsa userCertificate ve Usersmmecertificate özniteliklerini dışarı aktarmaya yönelik olarak güncelleştirilmiştir. Azure AD Connect yükseltme hakkında daha fazla bilgi için, [önceki bir sürümden en son sürüme yükseltme Azure AD Connect](./how-to-upgrade-previous-version.md)makalesine başvurun.

 * **15 ' ten fazla sertifika değeri olan nesnelerin gerçek değerleri yerine null değer** veren Azure AD Connect bir **giden eşitleme kuralı** uygulayın. Bu seçenek, 15 ' ten fazla değere sahip nesneler için Azure AD 'ye aktarılmasını istemediğiniz sertifika değerlerinden herhangi birinin gerekli olmadığı durumlarda uygundur. Bu eşitleme kuralının nasıl uygulanacağı hakkında daha fazla bilgi için, [userCertificate özniteliğinin dışarı aktarılmasını sınırlamak üzere eşitleme kuralını uygulama](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)başlıklı sonraki bölüme bakın.

 * Kuruluşunuz tarafından artık kullanılmayan değerleri kaldırarak şirket içi AD nesnesindeki (15 veya daha az) sertifika değerlerinin sayısını azaltın. Bu öznitelik, blok özniteliği, zaman aşımına uğramamış veya kullanılmayan sertifikalardan kaynaklanmamışsa uygundur. Şirket içi AD 'niz içinde, süre sonu sertifikaları bulma, yedekleme ve silmeye yardımcı olması için [burada kullanılabilen PowerShell betiğini](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) kullanabilirsiniz. Sertifikaları silmeden önce, kuruluşunuzdaki ortak anahtar altyapısı yöneticileriyle doğrulamanız önerilir.

 * UserCertificate özniteliğinin Azure AD 'ye aktarılmasını hariç tutmak için Azure AD Connect yapılandırın. Genel olarak, öznitelik Microsoft Online Services tarafından belirli senaryoları etkinleştirmek için kullanılabilir olduğundan bu seçeneği önermiyoruz. Özellikle:
    * Kullanıcı nesnesindeki userCertificate özniteliği, Exchange Online ve Outlook istemcileri tarafından ileti imzalama ve şifreleme için kullanılır. Bu özellik hakkında daha fazla bilgi edinmek için [ileti imzalama ve şifreleme için makale/MIME](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption)makalesine başvurun.

    * Bilgisayar nesnesindeki userCertificate özniteliği, Azure AD tarafından Windows 10 Şirket içi etki alanına katılmış cihazların Azure AD 'ye bağlanmasına izin vermek için kullanılır. Bu özellik hakkında daha fazla bilgi edinmek için lütfen [Windows 10 için etki alanına katılmış cihazları Azure AD 'ye bağlama](../devices/hybrid-azuread-join-plan.md)makalesine başvurun.

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>UserCertificate özniteliğinin dışarı aktarılmasını sınırlamak için eşitleme kuralı uygulama
UserCertificate özniteliğinden kaynaklanan LargeObject hatasını çözümlemek için, **15 ' ten fazla sertifika değerine sahip nesneler için gerçek değerler yerine null değer** veren Azure AD Connect bir giden eşitleme kuralı uygulayabilirsiniz. Bu bölümde, **Kullanıcı** nesneleri için eşitleme kuralını uygulamak için gereken adımlar açıklanmaktadır. Bu adımlar, **iletişim** ve **bilgisayar** nesneleri için uyarlanmıştır.

> [!IMPORTANT]
> Null değeri dışarı aktarmak, daha önce Azure AD 'ye başarıyla dışarı aktarılan sertifika değerlerini kaldırır.

Adımlar şu şekilde özetlenebilir:

1. Eşitleme zamanlayıcısını devre dışı bırakın ve devam eden bir eşitleme olmadığından emin olun.
3. UserCertificate özniteliği için varolan giden eşitleme kuralını bulun.
4. Gerekli giden eşitleme kuralını oluşturun.
5. LargeObject hatası ile var olan bir nesnede yeni eşitleme kuralını doğrulayın.
6. Yeni eşitleme kuralını, LargeObject hatası ile kalan nesnelere Uygula.
7. Azure AD 'ye Aktarılmayı bekleyen beklenmeyen bir değişiklik olmadığını doğrulayın.
8. Değişiklikleri Azure AD 'ye aktarın.
9. Eşitleme zamanlayıcısını yeniden etkinleştirin.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Adım 1. Eşitleme zamanlayıcısını devre dışı bırakın ve devam eden bir eşitleme olmadığını doğrulayın
İstenmeyen değişikliklerin Azure AD 'ye aktarılmasını önlemek için yeni bir eşitleme kuralı uygulama ortasında olduğunuzda hiçbir eşitlemenin olmadığından emin olun. Yerleşik eşitleme zamanlayıcısını devre dışı bırakmak için:
1. Azure AD Connect sunucusunda PowerShell oturumu başlatın.

2. Cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi devre dışı bırak: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Yukarıdaki adımlar, yerleşik Scheduler ile Azure AD Connect yalnızca daha yeni sürümler (1.1. xxx. x) için geçerlidir. Windows Görev Zamanlayıcı kullanan Azure AD Connect eski sürümlerini (1.0. xxx. x) kullanıyorsanız veya düzenli aralıklarla eşitleme tetiklemeniz için kendi özel zamanlayıcısını kullanıyorsanız (ortak değil), bunları uygun şekilde devre dışı bırakmanız gerekir.

1. → Eşitleme hizmeti ' ne giderek **Synchronization Service Manager** başlatın.

1. **İşlemler** sekmesine gidin ve durumu *"sürüyor* " olan hiçbir işlem olmadığını onaylayın.

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Adım 2. UserCertificate özniteliği için mevcut giden eşitleme kuralını bul
Kullanıcı nesneleri için userCertificate özniteliğini Azure AD 'ye aktarmak üzere etkin ve yapılandırılmış olan bir eşitleme kuralı olmalıdır. **Öncelik** ve **kapsam filtresi** yapılandırmasını öğrenmek için bu eşitleme kuralını bulun:

1. → Eşitleme kuralları Düzenleyicisi ' ne giderek **eşitleme kuralları düzenleyicisini** başlatın.

2. Arama filtrelerini aşağıdaki değerlerle yapılandırın:

    | Öznitelik | Değer |
    | --- | --- |
    | Yön |**Giden** |
    | MV nesne türü |**Kişi** |
    | Bağlayıcı |*Azure AD bağlayıcısının adı* |
    | Bağlayıcı nesne türü |**kullanıcısını** |
    | MV özniteliği |**userCertificate** |

3. Kullanıcı nesneleri için userCertficiate özniteliğini dışarı aktarmak üzere Azure AD bağlayıcısına OOB (kullanıma hazır) eşitleme kuralları kullanıyorsanız, *"AAD – User ExchangeOnline"* kuralını geri almalısınız.
4. Bu eşitleme kuralının **Öncelik** değerini aklınızda edin.
5. Eşitleme kuralını seçin ve **Düzenle**' ye tıklayın.
6. *"Ayrılmış kural onayını Düzenle"* açılır Iletişim kutusunda **Hayır**' a tıklayın. (Endişelenmeyin, bu eşitleme kuralında hiçbir değişiklik yapmayız).
7. Düzenleme ekranında **kapsam filtresi** sekmesini seçin.
8. Kapsam filtresi yapılandırması ' nı aklınızda edin. OOB eşitleme kuralını kullanıyorsanız, aşağıdakiler de dahil olmak üzere **iki yan tümce içeren bir kapsam filtresi grubu** olmalıdır:

    | Öznitelik | İşleç | Değer |
    | --- | --- | --- |
    | sourceObjectType | SıFıRA | Kullanıcı |
    | Cloudana kopyalı | Not QUAL | Doğru |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3. Adım Gerekli giden eşitleme kuralını oluşturun
Yeni eşitleme kuralı, mevcut eşitleme kuralına göre aynı **kapsam filtresine** ve **daha yüksek önceliğe** sahip olmalıdır. Bu, yeni eşitleme kuralının varolan eşitleme kuralıyla aynı nesne kümesine uygulanmasını sağlar ve userCertificate özniteliği için mevcut eşitleme kuralını geçersiz kılar. Eşitleme kuralını oluşturmak için:
1. Eşitleme kuralları Düzenleyicisi 'nde **Yeni kural ekle** düğmesine tıklayın.
2. **Açıklama sekmesinde** aşağıdaki yapılandırmayı sağlayın:

    | Öznitelik | Değer | Ayrıntılar |
    | --- | --- | --- |
    | Name | *Bir ad belirtin* | Örneğin, *"AAD 'Den dışarı-userCertificate Için özel geçersiz kılma"* |
    | Description | *Bir açıklama girin* | Örneğin, *"userCertificate özniteliğinde 15 ' ten fazla değer varsa, verileri dışarı aktarın."* |
    | Bağlı sistem | *Azure AD bağlayıcısını seçin* |
    | Bağlı sistem nesne türü | **kullanıcısını** | |
    | Meta veri deposu nesne türü | **kişiler** | |
    | Bağlantı türü | **Join** | |
    | Önceliği | *1-99 arasında bir sayı seçti* | Seçilen sayı varolan eşitleme kuralı tarafından kullanılmamalıdır ve var olan eşitleme kuralına göre daha düşük bir değere (ve bu nedenle daha yüksek önceliğe) sahip olmalıdır. |

3. **Kapsam filtresi** sekmesine gidin ve var olan eşitleme kuralının kullandığı kapsam filtresini uygulayın.
4. **Ekleme kuralları** sekmesini atlayın.
5. Aşağıdaki yapılandırmayı kullanarak yeni bir dönüşüm eklemek için **dönüşümler** sekmesine gidin:

    | Öznitelik | Değer |
    | --- | --- |
    | Akış Türü |**Expression** |
    | Target özniteliği |**userCertificate** |
    | Kaynak özniteliği |*Aşağıdaki Ifadeyi kullanın*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Eşitleme kuralını oluşturmak için **Ekle** düğmesine tıklayın.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4. Adım: LargeObject hatası ile var olan bir nesnede yeni eşitleme kuralını doğrulama
Bu, oluşturulan eşitleme kuralının diğer nesnelere uygulamadan önce LargeObject hatası ile mevcut bir AD nesnesi üzerinde doğru şekilde çalıştığını doğrulamadır:
1. Synchronization Service Manager **işlemler** sekmesine gidin.
2. Azure AD 'ye en son dışarı aktarma işlemini seçin ve LargeObject hataları olan nesnelerden birine tıklayın.
3.  Bağlayıcı alanı nesne özellikleri açılır ekranında, **Önizleme** düğmesine tıklayın.
4. Önizleme açılır ekranında **tam eşitleme** ' yi seçin ve **yürütme önizlemesi**' ye tıklayın.
5. Önizleme ekranını ve bağlayıcı alanı nesne özellikleri ekranını kapatın.
6. Synchronization Service Manager **Bağlayıcılar** sekmesine gidin.
7. **Azure AD** Bağlayıcısı ' na sağ tıklayın ve **Çalıştır...** ' ı seçin.
8. Bağlayıcıyı Çalıştır açılır penceresinde **dışarı aktarma** adımı ' nı seçin ve **Tamam**' ı tıklatın.
9. Azure AD 'ye dışarı aktarma işleminin tamamlanmasını bekleyin ve bu nesnede başka bir LargeObject hatası olmadığını doğrulayın.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5. Adım. Yeni eşitleme kuralını, LargeObject hatası ile kalan nesnelere Uygula
Eşitleme kuralı eklendikten sonra, AD Bağlayıcısı üzerinde tam eşitleme adımını çalıştırmanız gerekir:
1. Synchronization Service Manager **Bağlayıcılar** sekmesine gidin.
2. **Ad** bağlayıcısına sağ tıklayıp **Çalıştır...** öğesini seçin.
3. Bağlayıcıyı Çalıştır açılır penceresinde **tam eşitleme** adımı ' nı seçin ve **Tamam**' ı tıklatın.
4. Tam eşitleme adımının tamamlanmasını bekleyin.
5. Birden fazla AD bağlayıcılarınız varsa, kalan AD bağlayıcıları için yukarıdaki adımları tekrarlayın. Genellikle birden çok şirket içi dizinleriniz varsa birden çok bağlayıcı gereklidir.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6. Adım. Azure AD 'ye Aktarılmayı bekleyen beklenmeyen bir değişiklik olmadığını doğrulayın
1. Synchronization Service Manager **Bağlayıcılar** sekmesine gidin.
2. **Azure AD** Bağlayıcısı ' na sağ tıklayın ve **bağlayıcı alanı ara**' yı seçin.
3. Arama bağlayıcı alanı açılır penceresinde:
    1. Kapsamı **bekleyen dışarı aktarma** olarak ayarlayın.
    2. **Ekle**, **Değiştir** ve **Sil** dahil olmak üzere tüm 3 onay kutularını işaretleyin.
    3. Azure AD 'ye Aktarılmayı bekleyen değişikliklerle tüm nesneleri döndürmek için **Ara** düğmesine tıklayın.
    4. Beklenmeyen bir değişiklik olmadığını doğrulayın. Belirli bir nesne için değişiklikleri incelemek üzere nesnesine çift tıklayın.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7. Adım. Azure AD 'de yapılan değişiklikleri dışarı aktarma
Değişiklikleri Azure AD 'ye aktarmak için:
1. Synchronization Service Manager **Bağlayıcılar** sekmesine gidin.
2. **Azure AD** Bağlayıcısı ' na sağ tıklayın ve **Çalıştır...** ' ı seçin.
4. Bağlayıcıyı Çalıştır açılır penceresinde **dışarı aktarma** adımı ' nı seçin ve **Tamam**' ı tıklatın.
5. Azure AD 'ye dışarı aktarmanın tamamlanmasını bekleyin ve başka bir LargeObject hatası olmadığını doğrulayın.

### <a name="step-8-re-enable-sync-scheduler"></a>8. Adım Eşitleme zamanlayıcısını yeniden etkinleştir
Sorun çözümlendikten sonra, yerleşik eşitleme zamanlayıcısını yeniden etkinleştirin:
1. PowerShell oturumu başlatın.
2. Cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi yeniden etkinleştirin: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Yukarıdaki adımlar, yerleşik Scheduler ile Azure AD Connect yalnızca daha yeni sürümler (1.1. xxx. x) için geçerlidir. Windows Görev Zamanlayıcı kullanan Azure AD Connect eski sürümlerini (1.0. xxx. x) kullanıyorsanız veya düzenli aralıklarla eşitleme tetiklemeniz için kendi özel zamanlayıcısını kullanıyorsanız (ortak değil), bunları uygun şekilde devre dışı bırakmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
