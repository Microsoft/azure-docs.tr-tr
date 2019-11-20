---
title: Mantıksal uygulama sınama sürücüsü | Azure Marketi
description: Yalnızca Azure 'un dışındaki bir Dynamics AX/CRM örneğiyle veya başka bir kaynakla bağlanan test sürücüleri oluşturmayı açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 87170344f7467770829cbd8ba7d8936a9e39c0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824596"
---
<a name="logic-app-test-drive"></a>Mantıksal uygulama sınama sürücüsü
====================

Bu makale, AppSource 'ta teklifleri olan ve bir Dynamics AX/CRM örneğiyle ya da yalnızca Azure dışındaki başka bir kaynakla bağlanan test sürücüleri oluşturmak isteyen yayımcılar içindir.

<a name="how-to-build-a-logic-app-test-drive"></a>Mantıksal uygulama test sürücüsü oluşturma
-----------------------------------

Logic app test sürücüleri için test sürücüsü belgeleri şu anda hala GitHub 'da [işlemler](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) ve [müşteri katılımı](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)için, daha fazla bilgi edinmek için buraya gidin.

<a name="how-to-publish-a-test-drive"></a>Test sürücüsü yayımlama
---------------------------

Test sürücünüz oluşturuldığına göre, bu bölümde test sürücünüzü başarıyla yayımlamanız için gereken her bir alan gösterilmektedir.

![Sınama sürücüsü özelliğini etkinleştir](./media/azure-resource-manager-test-drive/howtopub1.png)

İlk ve en önemli alan, formu doldurmanız için gereken tüm alanları içeren formun test edilip edilmeyeceğini değiştirmek isteyip istemediğinizi belirtir. **Hayır ' ı seçtiğinizde,** form devre dışı bırakılır ve test sürücüsü devre dışı olarak yeniden yayımlarsanız, test sürücünüz üretimden kaldırılır.

*Note*: kullanıcılar tarafından etkin olarak kullanılan test sürücüleri varsa, bu test sürücüleri oturumunun süresi dolana kadar çalışmaya devam eder.

### <a name="details"></a>Ayrıntılar

Doldurulacak sonraki bölüm, test sürücünüzün Teklifinizle ilgili ayrıntılardır.

![Test sürücüsü ayrıntıları](./media/azure-resource-manager-test-drive/howtopub2.png)

**Açıklama-** *[gerekli alan]* Bu, test sürücünüzde nelerin olduğuna ilişkin ana açıklamayı yazacağınız yerdir. Müşteri, test sürücünüzün ürününüz hakkında hangi senaryolarda ele alınacaktır? bölümünü okumak için buraya gelir. 

**Kullanıcı el ile-** *[gerekli alan]* Bu, test sürücüsü deneyiminizin ayrıntılı yönergedir. Müşteri bunu açar ve kendi test sürücüleri boyunca ne yapmak istediğinizi tam olarak ele alabilir. Bu içeriğin anlaşılması ve izlenmesi oldukça önemlidir! (Bir. PDF dosyası olmalıdır)

**Test sürücüsü tanıtım videosu-** Kullanıcı kılavuzuna benzer\] önerilen \[, test sürücüsü deneyiminizin video öğreticisini eklemek en iyisidir. Müşteri, test sürüşü öncesinde veya sırasında bunu izleyebilir ve test sürücüleri genelinde ne yapmak istediğinizi tam olarak ele alabilir. Bu içeriğin anlaşılması ve izlenmesi oldukça önemlidir!

- Videonuzun **ad** başlığı
- **Bağlantı** -YouTube veya Vimeo 'dan gömülü bir URL olmalıdır. Gömülü URL 'yi alma hakkında örnek aşağıda verilmiştir:
- **Küçük resim** -yüksek kaliteli bir görüntü (533x324) piksel olmalıdır. Buradaki test sürücüsü deneyiminizin bir kısmının ekran görüntüsünü almanız önerilir.

Aşağıda, bu alanların test sürücüsü deneyimi sırasında müşteriniz için nasıl gösterileceği gösterilmektedir.

![Sınama sürücüsü alanları görünümü ve kullanım](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknik yapılandırma

Doldurulacak sonraki bölüm, test sürücüsü mantıksal uygulamanızı yapılandırdığınız ve özellikle test sürücü örneklerinizin nasıl çalıştığını tanımlayabileceğiniz yerdir.

![Sınama sürücüsü teknik yapılandırma](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Bölge** -  *[gerekli alan]* seçtiğiniz bölge, test sürücüsü mantıksal uygulama kaynaklarınızın nerede dağıtıldığını seçtiğiniz yerdir.

    *Note:* Mantıksal uygulamanızda bir bölgede depolanan özel kaynaklar varsa bölgenin burada seçildiğinden emin olun. Bunu yapmanın en iyi yolu, **mantıksal uygulamanızı portalda Azure aboneliğinizde yerel olarak dağıtmaktır ve** buraya yazmadan önce çalıştığını doğrular.

- **En fazla eşzamanlı test sürücüsü** -  *[gerekli alan]* zaten dağıtılmış ve seçilen bölge başına erişim bekleyen test sürücüsü örneklerinin sayısı. Müşteriler, bir dağıtımı beklemek yerine bu test sürücülerine anında erişebilir.

    *Note:* N her bir öğrencinin bir test sürücüsüne sahip olmasını istediğiniz bir Web semineri/sınıfı çalıştırıyorsanız, N sayıda sıcak örnek ile yayımlanması önerilir ve sonra normal sık erişimli örneklerinizi yeniden yayımlamak için sınıfı açıldıktan sonra.

- Sınama sürücüsü **süresi (saat)-** *[gerekli alan]* test sürücüsünün ne kadar süreyle etkin kalacağı süre (saat \#). Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer.

- **Azure Kaynak grubu adı-** *[gerekli alan]* mantıksal uygulama test sürücülerinizin kaydedildiği kaynak grubu adına yazın.

- **Mantıksal uygulama adı ata-** *[gerekli alan]* bir kullanıcı tarafından alınmadan önce test sürücüsünde bir kullanıcıyı atamak için kullanılan mantıksal uygulamada yazma, bu mantıksal uygulamanın adına buradan yazma. Bu dosyanın yukarıdaki kaynak grubuna kaydedildiğinden emin olun.

- **Mantıksal uygulama adı sağlamayı kaldırma-** *[gerekli alan]* test sürücüsünde oluşturulan tüm kaynakları hazırlamanızı sağlamak için mantıksal uygulama adına yazın. Bu dosyanın yukarıdaki kaynak grubuna kaydedildiğinden emin olun.

- **Erişim bilgileri-** *[gerekli alan]* bir müşteri test sürücüsünü aldıktan sonra, bunlara erişim bilgileri sunulur. Bu yönergeler, test sürücünüzün Kaynak Yöneticisi şablonundaki yararlı çıkış parametrelerini paylaşmaktır. Çıkış parametrelerini dahil etmek için çift küme ayraçları kullanın (örneğin, **{{outputname}}** ) ve konuma doğru eklenecektir. (Bu, ön uçta işlemek için HTML dize biçimlendirmesi önerilir).

### <a name="test-drive-deployment-subscription-details"></a>Test sürücüsü dağıtım aboneliği ayrıntıları

Doldurulacak son bölüm, Azure aboneliğinizi ve Azure Active Directory (AD) bağlantısını kurarak test sürücüleri otomatik olarak dağıtabilmelidir.

![Test sürücüsü dağıtım aboneliği ayrıntıları](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure ABONELIK kimliği** *[gerekli alan]* bu, Azure hizmetlerine ve Azure Portal erişim izni verir. Abonelik, kaynak kullanımının raporlandığı ve hizmetlerin faturalandırılabildiği yerdir. Yalnızca test sürücüleri için **ayrı** bir Azure aboneliğiniz yoksa, lütfen devam edin ve bir tane yapın. Azure portal ' de oturum açarak ve sol taraftaki menüdeki aboneliklerde gezinerek Azure abonelik kimliklerini bulabilirsiniz.
(Örnek: "a83645ac-1234-5AB6-345-1h234g764ghty")

![Azure Abonelikleri](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD KIRACı kimliği** *[gerekli alan]* zaten kullanılabilir bir kiracı Kimliğiniz varsa, AŞAĞıDAKI özellikleri\> Dizin kimliğinde bulabilirsiniz.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Aksi takdirde, Azure Active Directory yeni bir kiracı oluşturun.

![Azure Active Directory özellikleri ekranı](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Azure Active Directory kiracılar](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD UYGULAMASı ID** *[gerekli alan]* sonraki adım yeni bir uygulama oluşturmak ve kaydetmek. Bu uygulamayı, test sürücü örneğiniz üzerinde işlem gerçekleştirmek için kullanacağız.

1. Yeni oluşturulan dizine veya zaten var olan dizine gidin ve filtre bölmesinde Azure Active Directory ' yi seçin.
2. "Uygulama kayıtları" araması yapın ve "Ekle" ye tıklayın
3. Bir uygulama adı girin.
4. "Web uygulaması/API" olarak türü seçin
5. Oturum açma URL 'SI içinde herhangi bir değer sağlayın, bu alanı kullanmaya\'kazandı.
6. Oluştur ' a tıklayın.
7. Uygulama oluşturulduktan sonra Özellikler ' e gidin\> uygulamayı çok kiracılı olarak ayarlayın ve Kaydet ' e basın.

Kaydet’e tıklayın. Son adım, bu kayıtlı uygulamanın uygulama KIMLIĞINI alıp buradaki test sürücüsü alanına yapıştırmaktır.

![Azure Active Directory uygulama KIMLIĞI](./media/azure-resource-manager-test-drive/subdetails7.png)

Aboneliğe dağıtmak üzere uygulamayı kullandığımızda, uygulamayı aboneliğe katkıda bulunan olarak eklememiz gerekiyor. Bunlar için yönergeler aşağıda verilmiştir:

1. Abonelikler dikey penceresine gidin ve yalnızca test sürücüsü için kullandığınız uygun aboneliği seçin.
1. **Erişim denetimi (IAM)** öğesine tıklayın.
1. **Rol atamaları** sekmesine tıklayın.  ![Azure Active Directory, yeni bir Access Control sorumlusu ekleme](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. **Rol ataması Ekle**' ye tıklayın.
1. Rolü **katkıda bulunan**olarak ayarlayın.
1. Azure AD uygulamasının adını yazın ve rolü atamak için uygulamayı seçin.
    ![Azure Active Directory izinler](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. **Kaydet** düğmesine tıklayın.

**Azure AD uygulaması anahtar-** *[gerekli alan]* son alan, kimlik doğrulama anahtarı oluşturmak için kullanılır. Anahtarlar ' ın altında bir anahtar açıklaması ekleyin, süresi hiçbir zaman dolmayacak şekilde ayarlayın ve ardından Kaydet ' i seçin. Bir zaman aşımına uğramaması **önemlidir** , bu, üretim ortamında test sürücünüzü bozacaktır. Bu değeri kopyalayın ve gerekli test sürücüsü alanına yapıştırın.

![Azure Active Directory Keys bölümü](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Şu anda Base64 kodlamalı bir anahtar oluşturmadığı için Azure uygulama kaydı önizlemesi 'ni kullanamazsınız.


<a name="next-steps"></a>Sonraki adımlar
----------

Tüm test sürücüsü alanlarınızı doldurduktan sonra, teklifinizi gözden geçirin ve yeniden **yayımlayın** . Sınama sürücünüz sertifika sürecini geçtikten sonra teklifinizin **Önizlemedeki** müşteri deneyimini kapsamlı bir şekilde test etmeniz gerekir. Kullanıcı arabiriminde bir test sürücüsü başlatın ve test sürücülerinizin tamamen doğru şekilde dağıtıldığını doğrulayın.

Test sürücüsünün müşterileriniz için sağlandıkları bir bölümünü silmeyin, bu nedenle test sürücüsü hizmeti bu kaynak gruplarını bir müşterinin tamamlanmasının ardından otomatik olarak temizler.

Önizleme teklifinizi rahat hissettiğinizde, şimdi **canlı çalışmaya devam**edersiniz! Teklif yayımlandıktan sonra, uçtan uca deneyimle tamamen emin olmak için Microsoft 'tan son bir gözden geçirme süreci vardır. Teklifin reddedilme bir nedeni varsa, teklifiniz için ne yapmanız gerektiğini açıklayan, mühendisiniz için mühendislik kişiye bir bildirim göndereceğiz.

Daha fazla sorunuz varsa, sorun giderme önerisi aranıyor veya test sürücünüzü daha başarılı hale getirmek istiyorsanız lütfen [SSS, sorun giderme & En Iyi Yöntemler](./marketing-and-best-practices.md)bölümüne gidin.
