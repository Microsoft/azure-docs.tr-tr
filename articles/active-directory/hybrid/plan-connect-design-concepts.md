---
title: 'Azure AD Connect: Tasarım kavramları | Microsoft Docs'
description: Bu konuda, belirli uygulama tasarım alanlarının ayrıntıları verilmektedir
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135749"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Tasarım kavramları
Bu belgenin amacı, Azure AD Connect uygulama tasarımı sırasında düşünülmesi gereken alanların tanımlanmasıdır. Bu belge, bazı alanlarda ayrıntılı bir bakış ve bu kavramlar diğer belgelerde kısaca açıklanmıştır.

## <a name="sourceanchor"></a>sourceAnchor
Sourcetutturucu özniteliği bir *nesne ömrü boyunca sabit bir öznitelik*olarak tanımlanır. Bir nesneyi şirket içinde ve Azure AD 'de aynı nesne olarak benzersiz şekilde tanımlar. Özniteliği de **ImmutableID** olarak adlandırılır ve iki ad de birbirinin yerine kullanılır.

"Değiştirilemez" sözcüğünün sabit sözcüğü, bu belge için önemlidir. Bu özniteliğin değeri ayarlandıktan sonra değiştirilelemediğinden, senaryonuzu destekleyen bir tasarım seçmek önemlidir.

Özniteliği aşağıdaki senaryolar için kullanılır:

* Yeni bir eşitleme altyapısı sunucusu derlendikten veya bir olağanüstü durum kurtarma senaryosundan sonra yeniden oluşturulduğunda, bu öznitelik Azure AD 'deki mevcut nesneleri şirket içi nesnelerle bağlar.
* Yalnızca bulut kimliğinden eşitlenmiş bir kimlik modeline geçtiğinizde, bu öznitelik nesnelerin Azure AD 'deki mevcut nesneleri şirket içi nesnelerle "sabit eşleştirme" olarak değiştirmesine izin verir.
* Federasyon kullanıyorsanız, bu öznitelik, bir kullanıcıyı benzersiz şekilde tanımlamak için, talep içinde **userPrincipalName** ile birlikte kullanılır.

Bu konu, yalnızca kullanıcılarla ilişkili olan Sourcetutturucu hakkında konuşur. Aynı kurallar tüm nesne türleri için geçerlidir, ancak bu sorun yalnızca kullanıcılar içindir.

### <a name="selecting-a-good-sourceanchor-attribute"></a>İyi bir Sourcetutturucu özniteliği seçme
Öznitelik değeri aşağıdaki kurallara uymalıdır:

* 60 karakterden kısa bir süre
  * -Z, A-Z veya 0-9 olmayan karakterler kodlanır ve 3 karakter olarak sayılır
* Özel bir karakter içermez: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > () '; : , [ ] " \@ _
* Genel olarak benzersiz olmalıdır
* Bir dize, tamsayı veya ikili olmalıdır
* Bu değişiklik değiştirebildiğinden, kullanıcının adına dayanmamalıdır
* Büyük/küçük harfe duyarlı olmamalıdır ve büyük/küçük harfe göre değişebilen değerlerden kaçının
* Nesne oluşturulduğunda atanmalıdır

Seçili Sourcetutturucu dize türünde değilse, özel bir karakter göründüğünden emin olmak için Base64Encode öznitelik değerini Azure AD Connect. ADFS 'den başka bir federasyon sunucusu kullanıyorsanız, sunucunuzun özniteliği de Base64Encode olduğundan emin olun.

Sourcetutturucu özniteliği büyük/küçük harfe duyarlıdır. "Johntikan" değeri "johntikan" ile aynı değildir. Ancak, yalnızca büyük/küçük harf farklılığı olan iki farklı nesneniz olmamalıdır.

Şirket içinde tek bir ormanınızda kullanmanız gereken öznitelik **Objectguıd**'dir. Ayrıca, Azure AD Connect ' de Express ayarlarını ve ayrıca DirSync tarafından kullanılan özniteliği kullandığınızda kullanılan özniteliktir.

Birden çok ormanınız varsa ve kullanıcıları ormanlar ve etki alanları arasında hareket etmeyin, **Objectguıd** bu durumda bile kullanmak için iyi bir özniteliktir.

Kullanıcıları ormanlar ve etki alanları arasında taşırsanız, taşıma sırasında kullanıcılardan değişmez veya taşınabilecek bir öznitelik bulmanız gerekir. Önerilen bir yaklaşım yapay bir öznitelik oluşturmaktır. GUID gibi görünen bir şeyi tutan bir öznitelik uygun olacaktır. Nesne oluşturma sırasında kullanıcı üzerinde yeni bir GUID oluşturulur ve damgalı olur. Bu değeri **Objectguıd** öğesine göre oluşturmak ve Ekle ' de seçili özniteliği güncelleştirmek için, eşitleme altyapısı sunucusunda özel bir eşitleme kuralı oluşturulabilir. Nesneyi taşıdığınızda, bu değerin içeriğini de kopyalamadığınızdan emin olun.

Başka bir çözüm, değişmediğini bildiğiniz mevcut bir özniteliği seçeceğdir. Yaygın olarak kullanılan öznitelikler **EmployeeID**içerir. Harf içeren bir özniteliği düşünüyorsanız, (büyük/küçük harf ve küçük harf) özniteliğin değeri için değişiklik yapabilme olanağı olmadığından emin olun. Kullanılması gereken hatalı öznitelikler kullanıcı adına sahip olan öznitelikleri içerir. Evlilik veya Divorce içinde, bu öznitelik için izin verilmeyen adın değiştirilmesi beklenir. Bu Ayrıca, **userPrincipalName**, **mail**ve **targetAddress** gibi özniteliklerin Azure AD Connect Yükleme sihirbazında seçim yapmak için de mümkün olmasa da bir neden olur. Bu özniteliklere Ayrıca, sourcetutturucu\@içinde izin verilmeyen "" karakteri de bulunur.

### <a name="changing-the-sourceanchor-attribute"></a>Sourcetutturucu özniteliğini değiştirme
Sourcetutturucu öznitelik değeri, nesne Azure AD 'de oluşturulduktan ve kimlik eşitlendikten sonra değiştirilemez.

Bu nedenle, Azure AD Connect için aşağıdaki kısıtlamalar geçerlidir:

* Sourcetutturucu özniteliği yalnızca ilk yükleme sırasında ayarlanabilir. Yükleme sihirbazını yeniden çalıştırırsanız, bu seçenek salt okunurdur. Bu ayarı değiştirmeniz gerekiyorsa, öğesini kaldırmanız ve yeniden yüklemeniz gerekir.
* Başka bir Azure AD Connect sunucusu yüklerseniz, daha önce kullanılan Sourcetutturucu özniteliğini seçmeniz gerekir. Daha önce DirSync 'i kullanıyor ve Azure AD Connect 'a taşırsanız, DirSync tarafından kullanılan özniteliği olduğundan **Objectguıd** kullanmanız gerekir.
* Nesne Azure AD 'ye aktarıldıktan sonra Sourcetutturucu değeri değiştirilirse Azure AD Connect eşitleme bir hata oluşturur ve bu nesne üzerinde daha fazla değişikliğe izin vermez, sorun düzeltilmez ve kaynak Direktörü için Sourcetutturucu yeniden değişir Iz.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Ms-DS-ımıbu GUID 'i Sourcetutturucu olarak kullanma
Varsayılan olarak, Azure AD Connect (sürüm 1.1.486.0 ve daha eski) Sourcetutturucu özniteliği olarak Objectguıd kullanır. Objectguıd, sistem tarafından oluşturulmuştur. Şirket içi AD nesneleri oluştururken değerini belirtemezsiniz. [Kaynak](#sourceanchor)bağlantısı bölümünde açıklandığı gibi, sourcetutturucu değerini belirtmeniz gereken senaryolar vardır. Senaryolar sizin için geçerliyse, Sourcetutturucu özniteliği olarak yapılandırılabilir bir AD özniteliği (örneğin, ms-DS-ıımıguıd) kullanmanız gerekir.

Azure AD Connect (sürüm 1.1.524.0 ve sonrası) artık ms-DS-ımıbu GUID 'in Sourcetutturucu özniteliği olarak kullanılmasını kolaylaştırır. Bu özelliği kullanırken, Azure AD Connect eşitleme kurallarını otomatik olarak yapılandırır:

1. Kullanıcı nesneleri için Sourcetutturucu özniteliği olarak ms-DS-ımıbu GUID kullanın. Objectguıd, diğer nesne türleri için kullanılır.

2. Ms-DS-ımcıcıve Guid özniteliği doldurulmayan tüm şirket içi ad kullanıcı nesneleri için, Azure AD Connect objectGUID değerini şirket içi Active Directory ms-DS-ımı Ms-DS-ımıbu Guid özniteliği doldurulduktan sonra, Azure AD Connect sonra nesneyi Azure AD 'ye dışarı aktarır.

>[!NOTE]
> Şirket içi AD nesnesi Azure AD Connect içeri aktarıldıktan sonra (diğer bir deyişle, AD Bağlayıcısı alanına içeri aktarılır ve meta veri deposuna yansıtılmışsa), Sourcetutturucu değerini artık değiştiremezsiniz. Belirli bir şirket içi AD nesnesi için Sourcetutturucu değerini belirtmek için, Azure AD Connect ' a aktarılmadan önce ms-DS-ımısıs özniteliğini yapılandırın.

### <a name="permission-required"></a>İzin gerekiyor
Bu özelliğin çalışması için, şirket içi Active Directory ile eşitlenmek üzere kullanılan AD DS hesabına, şirket içi Active Directory için ms-DS-ımizlerizerguid özniteliği için yazma izni verilmelidir.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Yeni yükleme, ' me GUID özelliğini etkinleştirme
Yeni yükleme sırasında, IBU GUID kullanımını Sourcetutturucu olarak etkinleştirebilirsiniz. Bu bölümde, Ayrıntılar bölümünde hem hızlı hem de özel yükleme ele alınmaktadır.

  >[!NOTE]
  > Azure AD Connect (1.1.524.0 ve sonrası) yalnızca daha yeni sürümleri, yeni yükleme sırasında kaynak Bağlayıcısı olarak IBU GUID kullanımını destekler.

### <a name="how-to-enable-the-consistencyguid-feature"></a>' Me GUID özelliğini etkinleştirme

#### <a name="express-installation"></a>Hızlı yükleme
Express modundaki Azure AD Connect yüklerken, Azure AD Connect Sihirbazı, aşağıdaki mantığı kullanarak Sourcetutturucu özniteliği olarak kullanılacak en uygun AD özniteliğini otomatik olarak belirler:

* İlk olarak, Azure AD Connect Sihirbazı, önceki Azure AD Connect yüklemesinde (varsa) Sourcetutturucu özniteliği olarak kullanılan AD özniteliğini almak için Azure AD kiracınızı sorgular. Bu bilgiler varsa Azure AD Connect aynı AD özniteliğini kullanır.

  >[!NOTE]
  > Azure AD Connect (1.1.524.0 ve sonrası) yalnızca daha yeni sürümleri, yükleme sırasında kullanılan Sourcetutturucu özniteliğiyle ilgili bilgileri Azure AD kiracınızda depolar. Azure AD Connect eski sürümleri değildir.

* Kullanılan Sourcetutturucu özniteliğiyle ilgili bilgiler yoksa, sihirbaz şirket içi Active Directory ms-DS-ımıbu GUID özniteliğinin durumunu denetler. Öznitelik, dizindeki herhangi bir nesnede yapılandırılmamışsa, sihirbaz, Sourcetutturucu özniteliği olarak ms-DS-ımıbu GUID ' i kullanır. Öznitelik, dizindeki bir veya daha fazla nesnede yapılandırılmışsa, sihirbaz özniteliği diğer uygulamalar tarafından kullanılır ve Sourcetutturucu özniteliği olarak uygun değildir...

* Bu durumda, sihirbaz, Sourcetutturucu özniteliği olarak Objectguıd kullanmaya geri döner.

* Sourcetutturucu özniteliğine karar verdikten sonra sihirbaz, bilgileri Azure AD kiracınızda depolar. Bilgiler daha sonra Azure AD Connect yüklemesi tarafından kullanılacaktır.

Express yüklemesi tamamlandıktan sonra sihirbaz, kaynak bağlantısı özniteliği olarak hangi özniteliğin çekilileceğini bildirir.

![Sihirbaz, Sourcetutturucu için AD özniteliğini çekildi olarak bilgilendirir](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Özel yükleme
Özel modlu Azure AD Connect yüklerken, Azure AD Connect Sihirbazı Sourcetutturucu özniteliği yapılandırılırken iki seçenek sunar:

![Özel yükleme-Sourcetutturucu yapılandırması](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Ayar | Açıklama |
| --- | --- |
| Kaynak bağlantısını benim için Azure yönetsin | Azure AD’nin sizin için özniteliği seçmesini istiyorsanız bu seçeneği belirleyin. Bu seçeneği belirlerseniz, Azure AD Connect Sihirbazı [hızlı yükleme sırasında kullanılan Sourcetutturucu öznitelik seçim mantığını](#express-installation)uygular. Hızlı yüklemeye benzer şekilde, sihirbaz özel yükleme tamamlandıktan sonra kaynak bağlantısı özniteliği olarak hangi özniteliğin çekilileceğini bildirir. |
| Belirli bir öznitelik | SourceAnchor özniteliği olarak mevcut bir AD özniteliğini belirtmek istiyorsanız bu seçeneği belirleyin. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Şu GUID özelliğini etkinleştirme-mevcut dağıtım
Kaynak Bağlayıcısı özniteliği olarak Objectguıd kullanan mevcut bir Azure AD Connect dağıtımınız varsa, bunun yerine, bu değeri, bu tür bir.

>[!NOTE]
> Azure AD Connect (1.1.552.0 ve sonrası) yalnızca daha yeni sürümleri, objectGUID 'ten kaynak bağlantısı özniteliği olarak, objectGUID 'e geçiş yapmayı destekler.

Kaynak bağlayıcı özniteliği olarak Objectguıd 'den bir kaynak bağlantı özniteliği olarak

1. Azure AD Connect Sihirbazı 'nı başlatın ve görevler ekranına gitmek için **Yapılandır** ' a tıklayın.

2. **Kaynak bağlantısını yapılandır** görev seçeneğini belirleyin ve **İleri**' ye tıklayın.

   ![Mevcut dağıtım için bu GUID 'yi etkinleştir-2. adım](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Azure AD yönetici kimlik bilgilerinizi girip **İleri**' ye tıklayın.

4. Azure AD Connect Sihirbazı, şirket içi Active Directory ms-DS-Imos, GUID özniteliğinin durumunu analiz eder. Öznitelik, dizindeki herhangi bir nesnede yapılandırılmamışsa, başka hiçbir uygulamanın Şu anda özniteliğini kullanmamasının ve bunu kaynak bağlantısı özniteliği olarak kullanması açısından güvenli olduğunu Azure AD Connect. Devam etmek için **İleri** 'yi tıklatın.

   ![Mevcut dağıtım için bu GUID 'yi etkinleştir-adım 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Yapılandırmaya **hazırlanma** ekranında, yapılandırma değişikliğini yapmak için **Yapılandır** ' a tıklayın.

   ![Mevcut dağıtım için ilereşifreli GUID 'yi etkinleştir-5. adım](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Yapılandırma tamamlandıktan sonra sihirbaz, ms-DS-ıminguıd 'in artık kaynak bağlantı özniteliği olarak kullanıldığını gösterir.

   ![Var olan dağıtım için bu GUID 'yi etkinleştir-6. adım](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Çözümleme sırasında (4. adım), öznitelik dizindeki bir veya daha fazla nesnede yapılandırılmışsa, sihirbaz özniteliği başka bir uygulama tarafından kullanılır ve aşağıdaki diyagramda gösterildiği gibi bir hata döndürür. Bu hata, birincil Azure AD Connect sunucunuzda bir daha önce, daha önce, hazırlama sunucunuzda de aynı şekilde yapmaya çalıştığınız durumlarda meydana gelir.

![Mevcut dağıtım için bu GUID 'yi etkinleştir-hata](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Özniteliğin diğer mevcut uygulamalar tarafından kullanılmadığından eminseniz, Azure AD Connect Sihirbazı 'nı **/Skipldapsearch** anahtarıyla yeniden başlatarak hatayı bastırın. Bunu yapmak için komut isteminde aşağıdaki komutu çalıştırın:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>AD FS veya üçüncü taraf Federasyon yapılandırmasındaki etki
Şirket içi AD FS dağıtımını yönetmek için Azure AD Connect kullanıyorsanız, Azure AD Connect talep kurallarını otomatik olarak kaynak Bağlayıcısı ile aynı AD özniteliğini kullanacak şekilde güncelleştirir. Bu, ADFS tarafından oluşturulan ImmutableID talebinin Azure AD 'ye aktarılmış Sourcetutturucu değerleriyle tutarlı olmasını sağlar.

Azure AD Connect dışında AD FS yönetiyorsanız veya kimlik doğrulaması için üçüncü taraf Federasyon sunucuları kullanıyorsanız, ImmutableID talebi için talep kurallarını el ile güncelleştirmeniz gerekir, burada açıklandığı gibi, Azure AD 'ye aktarılmış Sourcetutturucu değerleriyle tutarlı olmalıdır. Makale bölümü [AD FS talep kurallarını değiştirme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Sihirbaz, yükleme tamamlandıktan sonra aşağıdaki uyarıyı döndürür:

![Üçüncü taraf Federasyon yapılandırması](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Mevcut dağıtıma yeni dizinler ekleniyor
Azure AD Connect, bu GUID özelliğini etkin olarak dağıttığınızı ve şimdi dağıtıma başka bir dizin eklemek istediğinizi varsayalım. Dizini eklemeye çalıştığınızda, Azure AD Connect Sihirbazı dizindeki ms-DS-ımlarımguıd özniteliğinin durumunu denetler. Öznitelik, dizindeki bir veya daha fazla nesnede yapılandırılmışsa, sihirbaz özniteliği diğer uygulamalar tarafından kullanılır ve aşağıdaki diyagramda gösterildiği gibi bir hata döndürür. Özniteliğin mevcut uygulamalar tarafından kullanılmadığından eminseniz, yukarıda açıklandığı gibi belirtilen **/Skipldapsearch** anahtarıyla Azure AD Connect Sihirbazı 'nı yeniden başlatarak veya daha fazla bilgi için desteğe başvurmanız gerekirse hatayı bastırın .

![Mevcut dağıtıma yeni dizinler ekleniyor](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD oturum açma
Şirket içi dizininizi Azure AD ile tümleştirirken, eşitleme ayarlarının kullanıcının kimlik doğrulaması yöntemini nasıl etkileyebileceğini anlamak önemlidir. Azure AD, kullanıcının kimliğini doğrulamak için userPrincipalName (UPN) kullanır. Ancak, kullanıcılarınızı eşitlediğinizde, userPrincipalName değeri için kullanılacak özniteliği seçmeniz gerekir.

### <a name="choosing-the-attribute-for-userprincipalname"></a>UserPrincipalName özniteliği seçiliyor
Azure 'da kullanılmak üzere UPN değerinin sağlanması için özniteliği seçerken şunlardan emin olunması gerekir

* Öznitelik değerleri UPN söz dizimine (RFC 822) uygundur, bu, Kullanıcı adı\@biçiminde olmalıdır.
* Değer içindeki sonek, Azure AD 'de doğrulanmış özel etki alanlarından biriyle eşleşiyor

Hızlı Ayarlar ' da öznitelik için kabul edilen seçenek userPrincipalName ' dir. UserPrincipalName özniteliği kullanıcılarınızın Azure 'da oturum açmasını istediğiniz değeri içermiyorsa, **özel yükleme**' yi seçmeniz gerekir.

### <a name="custom-domain-state-and-upn"></a>Özel etki alanı durumu ve UPN
UPN soneki için doğrulanmış bir etki alanı olduğundan emin olmak önemlidir.

John, contoso.com 'deki bir kullanıcı. Kullanıcıları Azure AD Directory contoso.onmicrosoft.com ile eşitledikten sonra Azure 'da\@oturum açmak için John 'ın şirket içi UPN John contoso.com kullanmasını istiyorsunuz. Bunu yapmak için, kullanıcıları eşitlemeye başlayabilmeniz için önce Azure AD 'de özel etki alanı olarak contoso.com eklemeniz ve doğrulamanız gerekir. John 'un UPN soneki, örneğin contoso.com, Azure AD 'de doğrulanmış bir etki alanıyla eşleşmezse Azure AD, UPN sonekini contoso.onmicrosoft.com ile değiştirir.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Azure AD için yönlendirilebilir olmayan şirket içi etki alanları ve UPN
Bazı kuruluşların contoso. Local gibi yönlendirilebilir olmayan etki alanları ya da contoso gibi basit tek etiketli etki alanları vardır. Azure AD 'de yönlendirilemeyen bir etki alanını doğrulayameyebilirsiniz. Azure AD Connect, Azure AD 'de yalnızca doğrulanmış bir etki alanıyla eşitlenebilir. Bir Azure AD dizini oluşturduğunuzda, Azure AD 'niz için varsayılan etki alanı haline gelen bir yönlendirilebilir etki alanı oluşturur. Örneğin, contoso.onmicrosoft.com. Bu nedenle, varsayılan onmicrosoft.com etki alanına eşitlemek istemediğiniz durumlarda böyle bir senaryoda, diğer yönlendirilebilir etki alanını doğrulamak gerekli hale gelir.

Etki alanlarını ekleme ve doğrulama hakkında daha fazla bilgi için, [Azure Active Directory için özel etki alanı adınızı ekleme](../active-directory-domains-add-azure-portal.md) konusunu okuyun.

Azure AD Connect, yönlendirilemeyen bir etki alanı ortamında çalışıp çalışmadığını algılar ve hızlı ayarlar ile ilerlemeniz için size uygun şekilde uyarı verebilir. Yönlendirilemeyen bir etki alanında çalışıyorsanız, büyük olasılıkla kullanıcıların UPN 'sine yönlendirilemeyen son ekler de vardır. Örneğin, contoso. Local altında çalıştırıyorsanız, Azure AD Connect hızlı ayarları kullanmak yerine özel ayarları kullanmanızı önerir. Özel ayarları kullanarak, kullanıcılar Azure AD ile eşitlendikten sonra Azure 'da oturum açmak için UPN olarak kullanılması gereken özniteliği belirtebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
