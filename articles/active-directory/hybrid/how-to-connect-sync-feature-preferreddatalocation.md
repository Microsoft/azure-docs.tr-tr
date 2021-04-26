---
title: 'Azure AD Connect: Microsoft 365 kaynaklar için tercih edilen veri konumunu yapılandırın'
description: Microsoft 365 kullanıcı kaynaklarınızın Azure Active Directory Connect eşitleme ile kullanıcıya nasıl yakın şekilde yerleştirileceğini açıklar.
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
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972768"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Eşitleme Azure Active Directory Connect: Microsoft 365 kaynaklar için tercih edilen veri konumunu yapılandırın
Bu konunun amacı, Azure Active Directory (Azure AD) Connect Sync içinde tercih edilen veri konumu için özniteliği yapılandırma konusunda size yol gösterir. Birisi Microsoft 365 çoklu coğrafi bölge özellikleri kullandığında, bu özniteliği kullanıcının Microsoft 365 verilerinin coğrafi konumunu belirlemek için kullanırsınız. (Hüküm *bölgesi* ve *coğrafi* , birbirlerinin yerine kullanılır.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Tercih edilen veri konumu eşitlemesini etkinleştir
Varsayılan olarak, kullanıcılarınız için Microsoft 365 kaynakları Azure AD kiracınızla aynı coğrafi bölgede bulunur. Örneğin, kiracınız Kuzey Amerika, kullanıcıların Exchange posta kutuları da Kuzey Amerika de bulunur. Çok uluslu bir kuruluş için bu ideal olmayabilir.

**Preferreddatalocation** özniteliğini ayarlayarak bir kullanıcının coğrafi bölge tanımlayabilirsiniz. Kullanıcının posta kutusu ve OneDrive gibi Microsoft 365 kaynaklarını kullanıcıyla aynı coğrafi bölgede ve kuruluşunuzun tamamı için tek bir kiracıya sahip olabilirsiniz.

> [!IMPORTANT]
> Çoklu coğrafi bölge, şu anda etkin bir Kurumsal Anlaşma ve en az 250 Microsoft 365 hizmetleri aboneliğine sahip olan müşteriler tarafından kullanılabilir. Ayrıntılar için lütfen Microsoft temsilcinize başvurun.
>
>

Microsoft 365 için tüm coğrafyalar 'lar [, verilerinizin nerede bulunduğu](/microsoft-365/enterprise/o365-data-locations)konusunda bulunabilir.

Microsoft 365 çoklu coğrafi bölge için kullanılabilen coğrafyalar şunlardır:

| Coğrafi Bölge | preferredDataLocation değeri |
| --- | --- |
| Asya Pasifik | Atlanan |
| Avustralya | Au |
| Kanada | ERIŞEBILIRSINIZ |
| Avrupa Birliği | EUR |
| Fransa | FRA |
| Hindistan | IND |
| Japonya | JPN |
| Güney Kore | KOR |
| Güney Afrika | ZAF |
| İsviçre | HARFI |
| Birleşik Arap Emirlikleri | DEPOLANıR |
| Birleşik Krallık | GBR |
| Birleşik Devletler | NAM |

* Coğrafi bölge bu tabloda listelenmediyse (örneğin, Güney Amerika), birden çok coğrafi bölge için kullanılamaz.

* Microsoft 365 iş yüklerinin hepsi, kullanıcının coğrafi bölge ayarlama kullanımını desteklemez.

### <a name="azure-ad-connect-support-for-synchronization"></a>Eşitleme için Azure AD Connect desteği

Azure AD Connect, 1.1.524.0 ve üzeri sürümlerde **Kullanıcı** nesneleri Için **preferreddatalocation** özniteliği eşitlemesini destekler. Özellikle:

* Azure AD Bağlayıcısı 'ndaki nesne türü **Kullanıcı** şeması, **preferreddatalocation** özniteliğini içerecek şekilde genişletilir. Öznitelik türü, tek değerli dize.
* Meta veri deposundaki **kişinin** nesne türü şeması, **preferreddatalocation** özniteliğini içerecek şekilde genişletilir. Öznitelik türü, tek değerli dize.

Varsayılan olarak, **Preferreddatalocation** eşitleme için etkin değildir. Bu özellik daha büyük kuruluşlar için tasarlanmıştır. Windows Server 2019 ' deki Active Directory şemasında, bu amaçla kullanmanız gereken bir **msDS-preferredDataLocation** özniteliği vardır. Active Directory şemasını güncelleştirmediyseniz ve bunu yapamadıysanız, kullanıcılarınız için Microsoft 365 coğrafi bölge tutacak bir öznitelik tanımlamalısınız. Bu, her kuruluş için farklı olacaktır.

> [!IMPORTANT]
> Azure AD, Azure AD PowerShell kullanılarak **bulut Kullanıcı nesnelerindeki** **preferreddatalocation** özniteliğinin doğrudan yapılandırılmasını sağlar. Bu özniteliği **eşitlenmiş kullanıcı nesnelerinde** yapılandırmak için Azure AD Connect kullanmanız gerekir.

Eşitlemeyi etkinleştirmeden önce:

* Active Directory şemasını 2019 olarak yükseltmişseniz, kaynak öznitelik olarak kullanılacak şirket içi Active Directory özniteliğine karar verin. **Tek değerli dize** türünde olmalıdır.
* Azure AD PowerShell 'i kullanarak Azure AD 'deki mevcut **eşitlenmiş kullanıcı nesnelerinde** **preferreddatalocation** özniteliğini daha önce yapılandırdıysanız, öznitelik değerlerini şirket içi Active Directory ilgili **Kullanıcı** nesnelerine geri almalısınız.

    > [!IMPORTANT]
    > Bu değerleri geri bağlantı noktasından çıkardıysanız, **Preferreddatalocation** özniteliği için eşitleme ETKINLEŞTIRILDIĞINDE Azure AD 'deki mevcut öznitelik değerlerini kaldırır Azure AD Connect.

* Kaynak özniteliğini Şu anda en az birkaç şirket içi Active Directory kullanıcı nesnesinden yapılandırın. Bunu daha sonra doğrulamak için kullanabilirsiniz.

Aşağıdaki bölümlerde, **Preferreddatalocation** özniteliğinin eşitlemesini etkinleştirme adımları sağlanmaktadır.

> [!NOTE]
> Adımlar, tek ormanlı topolojiyle ve özel eşitleme kuralları olmadan bir Azure AD dağıtımı bağlamında açıklanmıştır. Çok ormanlı topolojiniz varsa, özel eşitleme kuralları yapılandırılmışsa veya bir hazırlama sunucusuna sahipseniz, adımları uygun şekilde ayarlamanız gerekir.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. Adım: eşitleme zamanlayıcısını devre dışı bırakın ve devam eden bir eşitleme olmadığını doğrulayın
Azure AD 'ye aktarılan istenmeyen değişikliklerden kaçınmak için, eşitleme kurallarını güncelleştirme ortasındayken hiçbir eşitlemenin gerçekleşmediğinden emin olun. Yerleşik eşitleme zamanlayıcısını devre dışı bırakmak için:

1. Azure AD Connect sunucusunda bir PowerShell oturumu başlatın.
2. Şu cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi devre dışı bırak: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Eşitleme hizmeti 'ni **Başlat**' a giderek **Synchronization Service Manager** başlatın  >  .
4. **İşlemler** sekmesini seçin ve *sürmekte* olan bir işlem olmadığından emin olun.

![Synchronization Service Manager ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>2. Adım: Active Directory şemayı yenileme
Active Directory şemasını 2019 olarak güncelleştirdiyseniz ve bağlama şema uzantısından önce yüklendiyse, bağlama şema önbelleğinin güncelleştirilmiş Şeması yoktur. Ardından, Kullanıcı arabiriminde görünmesi için şemayı sihirbazdan yenilemeniz gerekir.

1. Azure AD Connect Sihirbazı 'nı masaüstünden başlatın.
2. **Dizin şemasını Yenile** seçeneğini belirleyin ve **İleri**' ye tıklayın.
3. Azure AD kimlik bilgilerinizi girip **İleri**' ye tıklayın.
4. **Dizin şemasını Yenile** sayfasında, tüm ormanların seçildiğinden emin olun ve **İleri**' ye tıklayın.
5. Tamamlandığında Sihirbazı kapatın.

![Bağlama sihirbazında Dizin şemasını yenileme ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>3. Adım: kaynak özniteliğini şirket içi Active Directory bağlayıcı şemasına ekleme
**Bu adım yalnızca, Connect 1.3.21 veya daha eski bir sürümü çalıştırırsanız gereklidir. 1.4.18 veya daha yeni bir sürümü kullanıyorsanız, 5. adıma atlayın.**  
Azure AD özniteliklerinin hepsi şirket içi Active Directory bağlayıcı alanına aktarılmaz. Varsayılan olarak eşitlenmemiş bir özniteliği kullanmayı seçtiyseniz, içeri aktarmanız gerekir. Kaynak özniteliğini içeri aktarılan özniteliklerin listesine eklemek için:

1. Synchronization Service Manager **Bağlayıcılar** sekmesini seçin.
2. Şirket içi Active Directory Bağlayıcısı ' na sağ tıklayın ve **Özellikler**' i seçin.
3. Açılır iletişim kutusunda, **öznitelikleri Seç** sekmesine gidin.
4. Kullanmak üzere seçtiğiniz kaynak özniteliğin öznitelik listesinde işaretli olduğundan emin olun. Öznitelerinizi görmüyorsanız **Tümünü göster** onay kutusunu işaretleyin.
5. Kaydetmek için **Tamam**' ı seçin.

!["Öznitelikler" listesi vurgulanmış Synchronization Service Manager ve Özellikler iletişim kutusunu gösteren ekran görüntüsü.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>4. Adım: Azure AD bağlayıcı şemasına **Preferreddatalocation** ekleme
**Bu adım yalnızca, Connect 1.3.21 veya daha eski bir sürümü çalıştırırsanız gereklidir. 1.4.18 veya daha yeni bir sürümü kullanıyorsanız, 5. adıma atlayın.**  
Varsayılan olarak, **Preferreddatalocation** ÖZNITELIĞI Azure ad bağlayıcı alanına aktarılmaz. İçeri aktarılan öznitelikler listesine eklemek için:

1. Synchronization Service Manager **Bağlayıcılar** sekmesini seçin.
2. Azure AD Bağlayıcısı ' na sağ tıklayın ve **Özellikler**' i seçin.
3. Açılır iletişim kutusunda, **öznitelikleri Seç** sekmesine gidin.
4. Listeden **Preferreddatalocation** özniteliğini seçin.
5. Kaydetmek için **Tamam**' ı seçin.

![Synchronization Service Manager ve Özellikler iletişim kutusunun ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>5. Adım: gelen eşitleme kuralı oluşturma
Gelen eşitleme kuralı, öznitelik değerinin şirket içi Active Directory kaynak özniteliğinden metadize 'ye akmasını sağlar.

1. Eşitleme kuralları düzenleyicisini **Başlat**' a giderek **eşitleme kuralları düzenleyicisini** başlatın  >  .
2. Arama filtresi **yönünü** **gelen** olarak ayarlayın.
3. Yeni bir gelen kuralı oluşturmak için **Yeni kural ekle**' yi seçin.
4. **Açıklama** sekmesinde aşağıdaki yapılandırmayı sağlayın:

    | Öznitelik | Değer | Ayrıntılar |
    | --- | --- | --- |
    | Name | *Bir ad belirtin* | Örneğin, "AD 'den Içinde – Kullanıcı preferredDataLocation" |
    | Description | *Özel bir açıklama sağlayın* |  |
    | Bağlı sistem | *Şirket içi Active Directory bağlayıcısını seçme* |  |
    | Bağlı sistem nesne türü | **Kullanıcı** |  |
    | Meta veri deposu nesne türü | **Kişi** |  |
    | Bağlantı türü | **Join** |  |
    | Önceliği | *1 – 99 arasında bir sayı seçin* | 1 – 99 özel eşitleme kuralları için ayrılmıştır. Başka bir eşitleme kuralı tarafından kullanılan bir değer seçmeyin. |

5. Tüm nesneleri dahil etmek için **kapsam filtresini** boş tutun. Azure AD Connect dağıtımınıza göre kapsam filtresini ince ayar gerekebilir.
6. **Dönüştürme sekmesine** gidin ve aşağıdaki dönüştürme kuralını uygulayın:

    | Akış türü | Target özniteliği | Kaynak | Bir kez Uygula | Birleştirme türü |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Kaynak özniteliğini seçin | İşaretlemeyin | Güncelleştir |

7. Gelen kuralı oluşturmak için **Ekle**' yi seçin.

![Gelen eşitleme kuralı oluştur ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>6. Adım: giden eşitleme kuralı oluşturma
Giden eşitleme kuralı, öznitelik değerinin meta veri deposundaki Azure AD 'de **Preferreddatalocation** özniteliğine akmasını sağlar:

1. **Eşitleme kuralları düzenleyicisine** gidin.
2. Arama filtresi **yönünü** **giden** olarak ayarlayın.
3. **Yeni kural ekle**' yi seçin.
4. **Açıklama** sekmesinde aşağıdaki yapılandırmayı sağlayın:

    | Öznitelik | Değer | Ayrıntılar |
    | ----- | ------ | --- |
    | Name | *Bir ad belirtin* | Örneğin, "Azure AD 'ye kadar – Kullanıcı preferredDataLocation" |
    | Description | *Bir açıklama girin* ||
    | Bağlı sistem | *Azure AD bağlayıcısını seçin* ||
    | Bağlı sistem nesne türü | **Kullanıcı** ||
    | Meta veri deposu nesne türü | **Kişi** ||
    | Bağlantı türü | **Join** ||
    | Önceliği | *1 – 99 arasında bir sayı seçin* | 1 – 99 özel eşitleme kuralları için ayrılmıştır. Başka bir eşitleme kuralı tarafından kullanılan bir değer seçmeyin. |

5. **Kapsam filtresi** sekmesine gidin ve iki yan tümce içeren tek bir kapsam filtresi grubu ekleyin:

    | Öznitelik | İşleç | Değer |
    | --- | --- | --- |
    | sourceObjectType | SıFıRA | Kullanıcı |
    | Cloudana kopyalı | Not QUAL | Doğru |

    Kapsam filtresi, bu giden eşitleme kuralının hangi Azure AD nesnelerini uygulanacağını belirler. Bu örnekte, "Azure AD – Kullanıcı kimliği" OOB (kullanıma hazır) eşitleme kuralında aynı kapsam filtresini kullanırız. Eşitleme kuralının, şirket içi Active Directory eşitlenmemiş **Kullanıcı** nesnelerine uygulanmasını önler. Azure AD Connect dağıtımınıza göre kapsam filtresini ince ayar gerekebilir.

6. **Dönüştürme** sekmesine gidin ve aşağıdaki dönüştürme kuralını uygulayın:

    | Akış türü | Target özniteliği | Kaynak | Bir kez Uygula | Birleştirme türü |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | İşaretlemeyin | Güncelleştir |

7. Giden kuralı oluşturmak için **Ekle** ' ye kapatın.

![Giden eşitleme kuralı oluştur ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>7. Adım: tam eşitleme döngüsünü çalıştırma
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

        a. **Kapsamı** **bekleyen dışarı aktarma** olarak ayarlayın.<br>
        b. **Ekle, Değiştir ve Sil** dahil olmak üzere üç onay kutusunu seçin.<br>
        c. Aktarılacak değişikliklerle nesne listesini görüntülemek için **Ara**' yı seçin. Belirli bir nesne için değişiklikleri incelemek için, nesnesine çift tıklayın.<br>
        d. Değişikliklerin beklendiğini doğrulayın.

6. **Azure AD Bağlayıcısı** 'Nda **dışarı aktarma** çalıştırma

   1. **Azure AD Bağlayıcısı**' na sağ tıklayın ve **Çalıştır**' ı seçin.
   2. **Bağlayıcıyı Çalıştır** Iletişim kutusunda **dışarı aktar**' ı seçin ve **Tamam**' ı seçin.
   3. İşlemin tamamlanmasını bekleyin.

> [!NOTE]
> Adımların Azure AD Bağlayıcısı üzerinde tam eşitleme adımını veya Active Directory Bağlayıcısı üzerindeki dışarı aktarma adımını dahil ettiğini fark edebilirsiniz. Bu adımlar gerekli değildir, çünkü öznitelik değerleri şirket içi Active Directory yalnızca Azure AD 'ye akar.

## <a name="step-8-re-enable-sync-scheduler"></a>8. Adım: eşitleme zamanlayıcısını yeniden etkinleştirin
Yerleşik eşitleme zamanlayıcısını yeniden etkinleştirin:

1. Bir PowerShell oturumu başlatın.
2. Şu cmdlet 'i çalıştırarak zamanlanmış eşitlemeyi yeniden etkinleştirin: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>9. Adım: sonucu doğrulama
Şimdi yapılandırmayı doğrulamak ve kullanıcılarınız için etkinleştirmek zaman alabilir.

1. Coğrafi bölge 'yi bir kullanıcının seçili özniteliğine ekleyin. Kullanılabilir coğrafyalar listesi bu tabloda bulunabilir.  
![Bir kullanıcıya eklenen AD özniteliğinin ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Özniteliğin Azure AD 'ye eşitlenmesini bekleyin.
3. Exchange Online PowerShell 'i kullanarak posta kutusu bölgesinin doğru şekilde ayarlandığını doğrulayın.  
![Exchange Online PowerShell ekran görüntüsü](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Kiracınızın bu özelliği kullanabilmesi için işaretlenip işaretlenmediğini varsayarsak, posta kutusu doğru coğrafi bölge 'ye taşınır. Bu, posta kutusunun bulunduğu sunucu adına bakarak doğrulanabilir.

## <a name="next-steps"></a>Sonraki adımlar

Microsoft 365 çoklu coğrafi bölge hakkında daha fazla bilgi edinin:

* [Ignite 'de çok coğrafi olan oturumlar](https://aka.ms/MultiGeoIgnite)
* [OneDrive 'da çoklu coğrafi konum](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [SharePoint Online 'da çok coğrafi bölge](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Eşitleme altyapısında yapılandırma modeli hakkında daha fazla bilgi edinin:

* [Bildirim temelli sağlamayı anlamak](concept-azure-ad-connect-sync-declarative-provisioning.md)için yapılandırma modeli hakkında daha fazla bilgi edinin.
* [Bildirim temelli sağlama Ifadelerini anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)bölümünde ifade dili hakkında daha fazla bilgi edinin.

Genel Bakış konuları:

* [Azure AD Connect eşitleme: eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)