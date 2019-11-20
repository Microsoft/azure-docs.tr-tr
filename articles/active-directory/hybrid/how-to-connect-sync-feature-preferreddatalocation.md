---
title: 'Azure AD Connect: Office 365 kaynakları için tercih edilen veri konumunu yapılandırın'
description: Azure Active Directory Connect Sync ile Office 365 kullanıcı kaynaklarınızı kullanıcıya nasıl yakın bir şekilde koyabileceğinizi açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50cb5a76c6b19668fc23147244d65a0d996ebf90
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033730"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect eşitleme: Office 365 kaynakları için tercih edilen veri konumunu yapılandırın
Bu konunun amacı, Azure Active Directory (Azure AD) Connect Sync içinde tercih edilen veri konumu için özniteliği yapılandırma konusunda size yol gösterir. Birisi Office 365 ' de çok coğrafi bölge özellikleri kullandığında, bu özniteliği kullanıcının Office 365 verilerinin coğrafi konumunu belirlemek için kullanırsınız. (Hüküm *bölgesi* ve *coğrafi* , birbirlerinin yerine kullanılır.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Tercih edilen veri konumu eşitlemesini etkinleştir
Varsayılan olarak, kullanıcılarınız için Office 365 kaynakları, Azure AD kiracınızla aynı coğrafi bölgede bulunur. Örneğin, kiracınız Kuzey Amerika, kullanıcıların Exchange posta kutuları da Kuzey Amerika de bulunur. Çok uluslu bir kuruluş için bu ideal olmayabilir.

**Preferreddatalocation**özniteliğini ayarlayarak bir kullanıcının coğrafi bölge tanımlayabilirsiniz. Kullanıcının posta kutusu ve OneDrive gibi Office 365 kaynaklarına, kullanıcıyla aynı coğrafi bölgede ve kuruluşunuzun tamamı için tek bir kiracıya sahip olabilirsiniz.

> [!IMPORTANT]
> Çoklu coğrafi bölge, şu anda etkin bir Kurumsal Anlaşma ve en az 500 Office 365 hizmet aboneliği olan müşteriler tarafından kullanılabilir. Ayrıntılar için lütfen Microsoft temsilcinize başvurun.
>
>

Office 365 için tüm coğrafyalar 'lar [, verilerinizin nerede bulunduğu](https://aka.ms/datamaps)konusunda bulunabilir.

Office 365 ' deki coğrafyalar çok coğrafi bölge için kullanılabilir:

| Coğrafi | preferredDataLocation değeri |
| --- | --- |
| Asya Pasifik | ATLANAN |
| Avustralya | AU |
| Kanada | ERIŞEBILIRSINIZ |
| Avrupa Birliği | EUR |
| Fransa | FRA |
| Hindistan | BUL |
| Japonya | JPN |
| Güney Kore | KOR |
| Güney Afrika | ZAF |
| Birleşik Arap Emirlikleri | DEPOLANIR |
| Birleşik Krallık | GBR |
| Amerika Birleşik Devletleri | NAM |

* Coğrafi bölge bu tabloda listelenmediyse (örneğin, Güney Amerika), birden çok coğrafi bölge için kullanılamaz.

* Office 365 iş yüklerinin hepsi, kullanıcının coğrafi bölge ayarlama kullanımını desteklemez.

### <a name="azure-ad-connect-support-for-synchronization"></a>Eşitleme için Azure AD Connect desteği

Azure AD Connect, 1.1.524.0 ve üzeri sürümlerde **Kullanıcı** nesneleri Için **preferreddatalocation** özniteliği eşitlemesini destekler. Bu avantajlar şunlardır:

* Azure AD Bağlayıcısı 'ndaki nesne türü **Kullanıcı** şeması, **preferreddatalocation** özniteliğini içerecek şekilde genişletilir. Öznitelik türü, tek değerli dize.
* Meta veri deposundaki **kişinin** nesne türü şeması, **preferreddatalocation** özniteliğini içerecek şekilde genişletilir. Öznitelik türü, tek değerli dize.

Varsayılan olarak, **Preferreddatalocation** eşitleme için etkin değildir. Bu özellik daha büyük kuruluşlar için tasarlanmıştır. Ayrıca, şirket içi Active Directory **Preferreddatalocation** özniteliği bulunmadığından kullanıcılarınız için Office 365 coğrafi bölge 'sini tutacak bir öznitelik tanımlamalısınız. Bu, her kuruluş için farklı olacaktır.

> [!IMPORTANT]
> Azure AD, Azure AD PowerShell kullanılarak **bulut Kullanıcı nesnelerindeki** **preferreddatalocation** özniteliğinin doğrudan yapılandırılmasını sağlar. Azure AD artık **eşitlenmiş kullanıcı nesnelerinde** **PREFERREDDATALOCATION** özniteliğinin Azure AD PowerShell kullanılarak doğrudan yapılandırılmasına izin vermez. Bu özniteliği **eşitlenmiş kullanıcı nesnelerinde**yapılandırmak için Azure AD Connect kullanmanız gerekir.

Eşitlemeyi etkinleştirmeden önce:

* Kaynak öznitelik olarak kullanılacak şirket içi Active Directory özniteliğine karar verin. **Tek değerli dize**türünde olmalıdır. Aşağıdaki adımlarda, **Extensionattributes** bir tane kullanılır.
* Azure AD PowerShell 'i kullanarak Azure AD 'deki mevcut **eşitlenmiş kullanıcı nesnelerinde** **preferreddatalocation** özniteliğini daha önce yapılandırdıysanız, öznitelik değerlerini, içindeki karşılık gelen **Kullanıcı** nesnelerine geri almalısınız Şirket içi Active Directory.

    > [!IMPORTANT]
    > Bu değerleri geri bağlantı noktasından çıkardıysanız, **Preferreddatalocation** özniteliği için eşitleme ETKINLEŞTIRILDIĞINDE Azure AD 'deki mevcut öznitelik değerlerini kaldırır Azure AD Connect.

* Kaynak özniteliğini Şu anda en az birkaç şirket içi Active Directory kullanıcı nesnesinden yapılandırın. Bunu daha sonra doğrulamak için kullanabilirsiniz.

Aşağıdaki bölümlerde, **Preferreddatalocation** özniteliğinin eşitlemesini etkinleştirme adımları sağlanmaktadır.

> [!NOTE]
> Adımlar, tek ormanlı topolojiyle ve özel eşitleme kuralları olmadan bir Azure AD dağıtımı bağlamında açıklanmıştır. Çok ormanlı topolojiniz varsa, özel eşitleme kuralları yapılandırılmışsa veya bir hazırlama sunucusuna sahipseniz, adımları uygun şekilde ayarlamanız gerekir.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1\. adım: Eşitleme zamanlayıcısını devre dışı bırakın ve devam eden bir eşitleme olmadığını doğrulayın
Azure AD 'ye aktarılan istenmeyen değişikliklerden kaçınmak için, eşitleme kurallarını güncelleştirme ortasındayken hiçbir eşitlemenin gerçekleşmediğinden emin olun. Yerleşik eşitleme zamanlayıcısını devre dışı bırakmak için:

1. Azure AD Connect sunucusunda bir PowerShell oturumu başlatın.
2. Şu cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi devre dışı `Set-ADSyncScheduler -SyncCycleEnabled $false`bırak:.
3. **Eşitleme hizmeti**'ni **Başlat** > ' a giderek **Synchronization Service Manager** başlatın.
4. **İşlemler** sekmesini seçin ve *sürmekte*olan bir işlem olmadığından emin olun.

![Synchronization Service Manager ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>2\. adım: Kaynak özniteliğini şirket içi Active Directory bağlayıcı şemasına ekleyin
Azure AD özniteliklerinin hepsi şirket içi Active Directory bağlayıcı alanına aktarılmaz. Varsayılan olarak eşitlenmemiş bir özniteliği kullanmayı seçtiyseniz, içeri aktarmanız gerekir. Kaynak özniteliğini içeri aktarılan özniteliklerin listesine eklemek için:

1. Synchronization Service Manager **Bağlayıcılar** sekmesini seçin.
2. Şirket içi Active Directory Bağlayıcısı ' na sağ tıklayın ve **Özellikler**' i seçin.
3. Açılır iletişim kutusunda, **öznitelikleri Seç** sekmesine gidin.
4. Kullanmak üzere seçtiğiniz kaynak özniteliğin öznitelik listesinde işaretli olduğundan emin olun. Öznitelerinizi görmüyorsanız **Tümünü göster** onay kutusunu işaretleyin.
5. Kaydetmek için **Tamam**' ı seçin.

![Synchronization Service Manager ve Özellikler iletişim kutusunun ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3\. adım: Azure AD bağlayıcı şemasına **Preferreddatalocation** ekleme
Varsayılan olarak, **Preferreddatalocation** ÖZNITELIĞI Azure ad bağlayıcı alanına aktarılmaz. İçeri aktarılan öznitelikler listesine eklemek için:

1. Synchronization Service Manager **Bağlayıcılar** sekmesini seçin.
2. Azure AD Bağlayıcısı ' na sağ tıklayın ve **Özellikler**' i seçin.
3. Açılır iletişim kutusunda, **öznitelikleri Seç** sekmesine gidin.
4. Listeden **Preferreddatalocation** özniteliğini seçin.
5. Kaydetmek için **Tamam**' ı seçin.

![Synchronization Service Manager ve Özellikler iletişim kutusunun ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>4\. Adım: Gelen eşitleme kuralı oluşturma
Gelen eşitleme kuralı, öznitelik değerinin şirket içi Active Directory kaynak özniteliğinden metadize 'ye akmasını sağlar.

1. Eşitleme kuralları**düzenleyicisini** **Başlat** > ' a giderek **eşitleme kuralları düzenleyicisini** başlatın.
2. Arama filtresi **yönünü** **gelen**olarak ayarlayın.
3. Yeni bir gelen kuralı oluşturmak için **Yeni kural ekle**' yi seçin.
4. **Açıklama** sekmesinde aşağıdaki yapılandırmayı sağlayın:

    | Öznitelik | Value | Ayrıntılar |
    | --- | --- | --- |
    | Name | *Bir ad belirtin* | Örneğin, "AD 'den Içinde – Kullanıcı preferredDataLocation" |
    | Açıklama | *Özel bir açıklama sağlayın* |  |
    | Bağlı sistem | *Şirket içi Active Directory bağlayıcısını seçme* |  |
    | Bağlı sistem nesne türü | **Kullanıcısını** |  |
    | Meta veri deposu nesne türü | **Kişiler** |  |
    | Bağlantı Türü | **Birleştir** |  |
    | Öncellik | *1 – 99 arasında bir sayı seçin* | 1 – 99 özel eşitleme kuralları için ayrılmıştır. Başka bir eşitleme kuralı tarafından kullanılan bir değer seçmeyin. |

5. Tüm nesneleri dahil etmek için **kapsam filtresini** boş tutun. Azure AD Connect dağıtımınıza göre kapsam filtresini ince ayar gerekebilir.
6. **Dönüştürme sekmesine**gidin ve aşağıdaki dönüştürme kuralını uygulayın:

    | Akış türü | Hedef öznitelik | Source | Bir kez Uygula | Birleştirme türü |
    | --- | --- | --- | --- | --- |
    |Doğrudan | preferredDataLocation | Kaynak özniteliğini seçin | Olmayan | Güncelleştirme |

7. Gelen kuralı oluşturmak için **Ekle**' yi seçin.

![Gelen eşitleme kuralı oluştur ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>5\. Adım: Giden eşitleme kuralı oluşturma
Giden eşitleme kuralı, öznitelik değerinin meta veri deposundaki Azure AD 'de **Preferreddatalocation** özniteliğine akmasını sağlar:

1. **Eşitleme kuralları düzenleyicisine**gidin.
2. Arama filtresi **yönünü** **giden**olarak ayarlayın.
3. **Yeni kural ekle**' yi seçin.
4. **Açıklama** sekmesinde aşağıdaki yapılandırmayı sağlayın:

    | Öznitelik | Value | Ayrıntılar |
    | ----- | ------ | --- |
    | Name | *Bir ad belirtin* | Örneğin, "Azure AD 'ye kadar – Kullanıcı preferredDataLocation" |
    | Açıklama | *Bir açıklama girin* ||
    | Bağlı sistem | *Azure AD bağlayıcısını seçin* ||
    | Bağlı sistem nesne türü | **Kullanıcısını** ||
    | Meta veri deposu nesne türü | **Kişiler** ||
    | Bağlantı Türü | **Birleştir** ||
    | Öncellik | *1 – 99 arasında bir sayı seçin* | 1 – 99 özel eşitleme kuralları için ayrılmıştır. Başka bir eşitleme kuralı tarafından kullanılan bir değer seçmeyin. |

5. **Kapsam filtresi** sekmesine gidin ve iki yan tümce içeren tek bir kapsam filtresi grubu ekleyin:

    | Öznitelik | Operator | Value |
    | --- | --- | --- |
    | sourceObjectType | SIFIRA | Kullanıcı |
    | Cloudana kopyalı | NOT QUAL | Doğru |

    Kapsam filtresi, bu giden eşitleme kuralının hangi Azure AD nesnelerini uygulanacağını belirler. Bu örnekte, "Azure AD – Kullanıcı kimliği" OOB (kullanıma hazır) eşitleme kuralında aynı kapsam filtresini kullanırız. Eşitleme kuralının şirket içi Active Directory eşitlenmemiş **Kullanıcı** nesnelerine uygulanmasını önler. Azure AD Connect dağıtımınıza göre kapsam filtresini ince ayar gerekebilir.

6. **Dönüştürme** sekmesine gidin ve aşağıdaki dönüştürme kuralını uygulayın:

    | Akış türü | Hedef öznitelik | Source | Bir kez Uygula | Birleştirme türü |
    | --- | --- | --- | --- | --- |
    | Doğrudan | preferredDataLocation | preferredDataLocation | Olmayan | Güncelleştirme |

7. Giden kuralı oluşturmak için **Ekle** ' ye kapatın.

![Giden eşitleme kuralı oluştur ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>6\. Adım: Tam eşitleme döngüsünü Çalıştır
Genel olarak, tam eşitleme çevrimi gereklidir. Bunun nedeni, hem Active Directory hem de Azure AD Bağlayıcısı şemasına yeni öznitelikler eklemiş olmanız ve özel eşitleme kuralları sunmanız olabilir. Azure AD 'ye vermeden önce değişiklikleri doğrulayın. Tam eşitleme döngüsünü oluşturan adımları el ile çalıştırırken, değişiklikleri doğrulamak için aşağıdaki adımları kullanabilirsiniz.

1. Şirket içi Active Directory Bağlayıcısı üzerinde **tam içeri aktarma** Çalıştır:

   1. Synchronization Service Manager **işlemler** sekmesine gidin.
   2. Şirket **içi Active Directory Bağlayıcısı**' na sağ tıklayın ve **Çalıştır**' ı seçin.
   3. İletişim kutusunda **tam Içeri aktarma**' yı seçin ve **Tamam**' ı seçin.
   4. İşlemin tamamlanmasını bekleyin.

      > [!NOTE]
      > Kaynak özniteliği içeri aktarılan öznitelikler listesine zaten dahil edil, şirket içi Active Directory Bağlayıcısı üzerinde tam içeri aktarma işlemini atlayabilirsiniz. Diğer bir deyişle, bu makalede daha önce 2. adım sırasında herhangi bir değişiklik yapmanız gerekmez.

2. Azure AD Bağlayıcısı 'nda **tam içeri aktarma** Çalıştır:

   1. **Azure AD Bağlayıcısı**' na sağ tıklayın ve **Çalıştır**' ı seçin.
   2. İletişim kutusunda **tam Içeri aktarma**' yı seçin ve **Tamam**' ı seçin.
   3. İşlemin tamamlanmasını bekleyin.

3. Mevcut bir **Kullanıcı** nesnesi üzerinde eşitleme kuralı değişikliklerinin doğrulanması.

   Şirket içi Active Directory ve Azure AD 'den **Preferreddatalocation** kaynak özniteliği, ilgili bağlayıcı alanına aktarılmışsa. Tam eşitleme adımına geçmeden önce, şirket içi Active Directory bağlayıcı alanındaki mevcut bir **Kullanıcı** nesnesi üzerinde önizleme yapın. Seçtiğiniz nesnenin kaynak özniteliği doldurulmuş olmalıdır. Meta veri deposunda doldurulan **Preferreddatalocation** ile başarılı bir önizleme, eşitleme kurallarını doğru bir şekilde yapılandırdığınıza yönelik iyi bir göstergedir. Önizlemenin nasıl yapılacağı hakkında daha fazla bilgi için bkz. [değişikliği doğrulama](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Şirket içi Active Directory Bağlayıcısı üzerinde **tam eşitleme** çalıştırın:

   1. Şirket **içi Active Directory Bağlayıcısı**' na sağ tıklayın ve **Çalıştır**' ı seçin.
   2. İletişim kutusunda **tam eşitleme**' yi seçin ve **Tamam**' ı seçin.
   3. İşlemin tamamlanmasını bekleyin.

5. Azure AD 'ye **bekleyen dışarı aktarmaları** doğrulayın:

   1. **Azure AD Bağlayıcısı**' na sağ tıklayın ve **bağlayıcı alanı ara**' yı seçin.
   2. **Bağlayıcı alanını ara** iletişim kutusunda:

        a. **Kapsamı** **bekleyen dışarı aktarma**olarak ayarlayın.<br>
        b. **Ekle, Değiştir ve Sil**dahil olmak üzere üç onay kutusunu seçin.<br>
        c. Aktarılacak değişikliklerle nesne listesini görüntülemek için **Ara**' yı seçin. Belirli bir nesne için değişiklikleri incelemek için, nesnesine çift tıklayın.<br>
        d. Değişikliklerin beklendiğini doğrulayın.

6. **Azure AD Bağlayıcısı** 'Nda **dışarı aktarma** çalıştırma

   1. **Azure AD Bağlayıcısı**' na sağ tıklayın ve **Çalıştır**' ı seçin.
   2. **Bağlayıcıyı Çalıştır** Iletişim kutusunda **dışarı aktar**' ı seçin ve **Tamam**' ı seçin.
   3. İşlemin tamamlanmasını bekleyin.

> [!NOTE]
> Adımların Azure AD Bağlayıcısı üzerinde tam eşitleme adımını veya Active Directory Bağlayıcısı üzerindeki dışarı aktarma adımını dahil ettiğini fark edebilirsiniz. Bu adımlar gerekli değildir, çünkü öznitelik değerleri şirket içi Active Directory yalnızca Azure AD 'ye akar.

## <a name="step-7-re-enable-sync-scheduler"></a>7\. Adım: Eşitleme zamanlayıcısını yeniden etkinleştir
Yerleşik eşitleme zamanlayıcısını yeniden etkinleştirin:

1. Bir PowerShell oturumu başlatın.
2. Şu cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi yeniden etkinleştirin:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8\. Adım: Sonucu doğrulama
Şimdi yapılandırmayı doğrulamak ve kullanıcılarınız için etkinleştirmek zaman alabilir.

1. Coğrafi bölge 'yi bir kullanıcının seçili özniteliğine ekleyin. Kullanılabilir coğrafyalar listesi bu tabloda bulunabilir.  
![Bir kullanıcıya eklenen AD özniteliğinin ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Özniteliğin Azure AD 'ye eşitlenmesini bekleyin.
3. Exchange Online PowerShell 'i kullanarak posta kutusu bölgesinin doğru şekilde ayarlandığını doğrulayın.  
![Exchange Online PowerShell ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Kiracınızın bu özelliği kullanabilmesi için işaretlenip işaretlenmediğini varsayarsak, posta kutusu doğru coğrafi bölge 'ye taşınır. Bu, posta kutusunun bulunduğu sunucu adına bakarak doğrulanabilir.
4. Bu ayarın birçok posta kutusu üzerinde etkili olduğunu doğrulamak için [TechNet galerisinde](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)betiği kullanın. Bu betik Ayrıca tüm Office 365 veri merkezlerinin sunucu ön eklerinin bir listesini içerir ve hangi coğrafi bölge içinde bulunur. Posta kutusunun konumunu doğrulamak için önceki adımda başvuru olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Office 365 ' de çok coğrafi bölge hakkında daha fazla bilgi edinin:

* [Ignite 'de çok coğrafi olan oturumlar](https://aka.ms/MultiGeoIgnite)
* [OneDrive 'da çoklu coğrafi konum](https://aka.ms/OneDriveMultiGeo)
* [SharePoint Online 'da çok coğrafi bölge](https://aka.ms/SharePointMultiGeo)

Eşitleme altyapısında yapılandırma modeli hakkında daha fazla bilgi edinin:

* [Bildirim temelli sağlamayı anlamak](concept-azure-ad-connect-sync-declarative-provisioning.md)için yapılandırma modeli hakkında daha fazla bilgi edinin.
* [Bildirim temelli sağlama Ifadelerini anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)bölümünde ifade dili hakkında daha fazla bilgi edinin.

Genel Bakış konuları:

* [Azure AD Connect eşitlemesi: Eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
