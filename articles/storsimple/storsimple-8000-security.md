---
title: StorSimple 8000 serisi güvenlik | Microsoft Docs
description: Şirket içindeki ve buluttaki StorSimple hizmetinizi, cihazınızı ve verilerinizi koruyan güvenlik ve gizlilik özelliklerini açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 31c432b884ce92c508dd7f893c12ba13acff28e8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963526"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple güvenliği ve veri koruması

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

Güvenlik, özellikle teknoloji gizli veya özel verilerle kullanıldığında yeni bir teknolojiyi benimseme konusunda önemli bir konudur. Farklı teknolojileri değerlendirirken, veri koruma için riskleri ve maliyetleri de göz önünde bulundurmanız gerekir. Microsoft Azure StorSimple, veri koruma için hem güvenlik hem de gizlilik çözümü sağlar ve şunları sağlamaya yardımcı olur:

* **Gizlilik** – verilerinizi yalnızca yetkili varlıkları görüntüleyebilir.
* **Bütünlük** – yalnızca yetkili varlıklar, verilerinizi değiştirebilir veya silebilir.

Microsoft Azure StorSimple çözümü, birbirleriyle etkileşime geçen dört ana bileşenden oluşur:

* **Microsoft Azure içinde barındırılan storsimple Aygıt Yöneticisi hizmeti** : StorSimple cihazını yapılandırmak ve sağlamak için kullandığınız yönetim hizmeti.
* **StorSimple cihazı** : veri merkezinizde yüklü fiziksel bir cihaz. Veri üreten tüm konaklar ve istemciler StorSimple cihazına bağlanır ve cihaz verileri yönetir ve uygun şekilde Azure bulutuna taşımaktadır.
* **Cihaza bağlı istemciler/konaklar** : altyapınızda StorSimple cihazına bağlanan ve korunması gereken veriler üreten istemciler.
* **Bulut depolama** – Azure bulutunda verilerin depolandığı konum.

Aşağıdaki bölümlerde, bu bileşenlerin her birinin ve bunlarda depolanan verilerin korunmasına yardımcı olan StorSimple güvenlik özellikleri açıklanır. Ayrıca, Microsoft Azure StorSimple güvenliğine ve bunlara karşılık gelen yanıtlara sahip olabileceğiniz soruların bir listesini de içerir.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Aygıt Yöneticisi hizmeti koruması

StorSimple Aygıt Yöneticisi hizmeti, Microsoft Azure barındırılan ve kuruluşunuzun temin sahip olduğu tüm StorSimple cihazlarını yönetmek için kullanılan bir yönetim hizmetidir. Bir Web tarayıcısı üzerinden Azure portal oturum açmak için kuruluş kimlik bilgilerinizi kullanarak StorSimple Aygıt Yöneticisi hizmetine erişebilirsiniz.

StorSimple Aygıt Yöneticisi hizmetine erişim, kuruluşunuzun StorSimple içeren bir Azure aboneliğine sahip olmasını gerektirir. Aboneliğiniz, Azure portalında erişebildiğiniz özellikleri yönetir. Kuruluşunuzun bir Azure aboneliği yoksa ve bunlar hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure 'a kuruluş olarak kaydolma](../active-directory/fundamentals/sign-up-organization.md).

StorSimple Aygıt Yöneticisi hizmeti Azure 'da barındırıldığından Azure Güvenlik özellikleri tarafından korunur. Microsoft Azure tarafından sağlanan güvenlik özellikleri hakkında daha fazla bilgi için [Microsoft Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/security/)’ne gidin.

## <a name="storsimple-device-protection"></a>StorSimple cihaz koruması

StorSimple cihazı, yedekli denetleyiciler ve otomatik yük devretme özellikleri ile birlikte katı hal sürücüleri (SSD 'Ler) ve sabit disk sürücüleri (HDD 'Ler) içeren bir şirket içi karma depolama aygıtıdır. Denetleyiciler, daha az sıklıkta kullanılan verileri buluta taşırken, depolama katmanlamasını, şu anda yerel depolama (StorSimple cihazında veya şirket içi sunucular) üzerinde yerel depolama alanında (ya da etkin) verileri yerleştirerek yönetir.

Yalnızca yetkili StorSimple cihazlarının Azure aboneliğinizde oluşturduğunuz StorSimple Aygıt Yöneticisi hizmetine katılmasına izin verilir. Bir cihazı yetkilendirmek için hizmet kayıt anahtarını sağlayarak StorSimple Aygıt Yöneticisi hizmetine kaydolmanız gerekir. Hizmet kayıt anahtarı Azure portal oluşturulan 128 bitlik bir rastgele anahtardır.

![Hizmet kayıt anahtarı](./media/storsimple-security/ServiceRegistrationKey.png)

Hizmet kayıt anahtarı alma hakkında bilgi edinmek için 2. [adıma gidin: Hizmet kayıt anahtarını](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)alın.

Hizmet kayıt anahtarı 100 + karakter içeren uzun bir anahtardır. Anahtarı kopyalayabilir ve güvenli bir konumdaki bir metin dosyasına kaydedebilirsiniz. böylece, gerektiğinde ek cihazları yetkilendirmek için kullanabilirsiniz. İlk cihazınızı kaydettikten sonra hizmet kayıt anahtarı kaybolursa, StorSimple Aygıt Yöneticisi hizmetinden yeni bir anahtar oluşturabilirsiniz. Bu, mevcut cihazların çalışmasını etkilemez.

Bir cihaz kaydedildikten sonra, Microsoft Azure iletişim kurmak için belirteçleri kullanır. Hizmet kayıt anahtarı cihaz kaydından sonra kullanılmaz.

> [!NOTE]
> Her kullanım sonrasında hizmet kayıt anahtarını yeniden oluşturmanız önerilir.


## <a name="protect-your-storsimple-solution-via-passwords"></a>StorSimple çözümünüzü parolalar aracılığıyla koruma

Parolalar, bilgisayar güvenliğinin önemli bir yönüdür ve verilerinizin yalnızca yetkili kullanıcıların erişimine açık olduğundan emin olmak için StorSimple çözümünde yaygın olarak kullanılır. StorSimple aşağıdaki parolaları yapılandırmanıza olanak sağlar:

* StorSimple cihaz yöneticisi parolası
* Çekişme El Sıkışma Kimlik Doğrulama Protokolü (CHAP) Başlatıcı ve hedef parolalar
* StorSimple Snapshot Manager parolası

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>StorSimple için Windows PowerShell ve StorSimple cihaz yöneticisi parolası

StorSimple için Windows PowerShell, StorSimple cihazını yönetmek için kullanabileceğiniz bir komut satırı arabirimidir. StorSimple için Windows PowerShell, cihazınızı kaydetmenize, cihazınızda ağ arabirimini yapılandırmanıza, belirli güncelleştirme türlerini yüklemenize, destek oturumuna erişerek cihazınızda sorun gidermenize ve cihaz durumunu değiştirmenize olanak tanıyan özellikler içerir. Cihazdaki seri konsoluna bağlanarak veya Windows PowerShell uzaktan iletişimini kullanarak StorSimple için Windows PowerShell erişebilirsiniz.

PowerShell uzaktan iletişimi, HTTPS veya HTTP üzerinden yapılabilir. HTTPS üzerinden uzaktan yönetim etkinleştirilirse, uzak yönetim sertifikasını cihazdan indirmeniz ve uzak istemciye yüklemeniz gerekir. PowerShell uzaktan iletişim hakkında daha fazla bilgi için, [StorSimple cihazınıza uzaktan bağlanma](storsimple-8000-remote-connect.md)konusuna gidin.

Cihaza bağlanmak için StorSimple için Windows PowerShell kullandıktan sonra cihazda oturum açmak için Cihaz Yöneticisi parolasını sağlamanız gerekir.

![Cihaz yöneticisi parolası](./media/storsimple-security/DeviceAdminPW.png)

Aşağıdaki en iyi yöntemleri göz önünde bulundurun:

* Uzaktan yönetim varsayılan olarak kapalıdır. StorSimple Aygıt Yöneticisi hizmetini etkinleştirmek için kullanabilirsiniz. En iyi güvenlik uygulaması olarak, uzaktan erişim yalnızca gerçekten gerekli olduğu süre boyunca etkinleştirilmelidir.
* Parolayı değiştirirseniz, tüm uzaktan erişim kullanıcılarına beklenmedik bir bağlantı kaybı yaşamamasını sağlayacak şekilde bildirimde bulunduğundan emin olun.
* StorSimple Aygıt Yöneticisi hizmeti mevcut parolaları alamıyor: yalnızca bunları sıfırlayabilir. Unutursa parolayı sıfırlamanız gerekmez diye tüm parolaları güvenli bir yerde depolamanızı öneririz. Parolayı sıfırlamanız gerekiyorsa, sıfırlamadan önce tüm kullanıcıları bilgilendirdiğinizden emin olun.

Cihaza bir seri bağlantı kullanarak Windows PowerShell arabirimine erişebilirsiniz. Ayrıca, daha fazla güvenlik sağlayan HTTP veya HTTPS kullanarak uzaktan da erişebilirsiniz. HTTPS, seri veya HTTP bağlantısından daha yüksek düzeyde güvenlik sağlar. Ancak HTTPS kullanmak için, öncelikle cihaza erişecek istemci bilgisayara bir sertifika yüklemeniz gerekir. Uzaktan erişim sertifikasını StorSimple Aygıt Yöneticisi hizmetindeki cihaz yapılandırması sayfasından indirebilirsiniz. Uzaktan erişim sertifikası kaybolursa, yeni bir sertifika indirip uzaktan yönetimi kullanma yetkisine sahip tüm istemcilere yaymalısınız.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Çekişme El Sıkışma Kimlik Doğrulama Protokolü (CHAP) Başlatıcı ve hedef parolalar

CHAP, StorSimple cihazı tarafından uzak istemcilerin kimliğini doğrulamak için kullanılan bir kimlik doğrulama şemadır. Doğrulama, paylaşılan bir parolayı temel alır. CHAP tek yönlü (tek yönlü) veya karşılıklı (çift yönlü) olabilir. Tek yönlü CHAP ile hedef (StorSimple cihazı) bir başlatıcısının (ana bilgisayar) kimliğini doğrular. Karşılıklı veya ters CHAP, hedefin başlatıcıyı kimliğini doğrulamasını ve ardından başlatıcıya hedefin kimliğini doğrulamasını gerektirir. StorSimple, her iki yöntemi de kullanacak şekilde yapılandırılabilir.

CHAP 'yi yapılandırırken aşağıdakilere dikkat edin:

* CHAP Kullanıcı adı 233 karakterden daha azını içermelidir.
* CHAP parolası 12 ile 16 karakter arasında olmalıdır. Daha uzun bir Kullanıcı adı veya parola kullanmaya çalışılması, Windows ana bilgisayarında bir kimlik doğrulama hatasına neden olur.
* Hem CHAP başlatıcısı hem de CHAP hedefi için aynı parolayı kullanamazsınız.
* Parolayı ayarladıktan sonra değişiklik yapılabilir, ancak alınamaz. Parola değiştirilirse, tüm uzaktan erişim kullanıcılarını StorSimple cihazına başarıyla bağlanabilmeleri için bilgilendirdiğinizden emin olun.

CHAP ve StorSimple çözümünüz için nasıl yapılandırılacağı hakkında daha fazla bilgi için, [StorSimple cihazınız IÇIN CHAP yapılandırma](storsimple-8000-configure-chap.md)bölümüne gidin.

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager parolası

StorSimple Snapshot Manager, uygulamayla tutarlı yedeklemeler oluşturmak için birim grupları ve Windows Birim Gölge Kopyası Hizmeti kullanan bir Microsoft Yönetim Konsolu (MMC) ek bileşenidir. Ayrıca, StorSimple Snapshot Manager kullanarak yedekleme zamanlamaları oluşturabilir, birimleri kopyalayabilir veya geri yükleyebilirsiniz.

Bir cihazı StorSimple Snapshot Manager kullanacak şekilde yapılandırdığınızda, StorSimple Snapshot Manager parolasını sağlamanız gerekecektir. Bu parola öncelikle kayıt sırasında StorSimple için Windows PowerShell ayarlanır. Ayrıca, StorSimple Aygıt Yöneticisi hizmetinden parola ayarlanabilir ve değiştirilebilir. Bu parola, StorSimple Snapshot Manager cihazın kimliğini doğrular.

![StorSimple Snapshot Manager parolası](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple Snapshot Manager parolası 14 ile 15 karakter arasında olmalıdır ve büyük harf, küçük harf, sayısal ve özel karakterlerden oluşan 3 veya daha fazla bir bileşim içermelidir. StorSimple Snapshot Manager parolasını ayarladıktan sonra değişiklik yapılabilir ancak alınamaz. Parolayı değiştirirseniz, tüm uzak kullanıcılara bildirimde bulunduğundan emin olun.

StorSimple Snapshot Manager hakkında daha fazla bilgi için bkz. [storsimple Snapshot Manager nedir?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Parola en iyi uygulamaları

StorSimple parolalarının sağlam ve iyi korunan olduğundan emin olmak için aşağıdaki yönergeleri kullanmanızı öneririz:

* Parolalarınızı her üç ayda bir değiştirin. Parolaların değiştirilmesi yıllık olarak zorlanır.
* Güçlü parolalar kullanın. Daha fazla bilgi için daha [güçlü parolalar oluşturma ve bunları koruma](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)bölümüne gidin.
* Farklı erişim mekanizmaları için her zaman farklı parolalar kullanın; belirttiğiniz parolaların her biri benzersiz olmalıdır.
* StorSimple cihazına erişim yetkisi olmayan herkesle parola paylaşmayın.
* Parola biçimindeki başkalarının veya ipucunun önünde bir parola hakkında konuşmayın.
* Bir hesap veya parolanın tehlikede olduğunu düşünüyorsanız, olay bilgilerini bilgi güvenliği bölümünüze bildirin.
* Tüm parolaları gizli, gizli bilgiler olarak değerlendirin. 

## <a name="storsimple-data-protection"></a>StorSimple veri koruma

Bu bölümde, yoldaki ve depolanmış verilerdeki verileri koruyan StorSimple güvenlik özellikleri açıklanmaktadır.

Diğer bölümlerde açıklandığı gibi, parolalar, StorSimple çözümünüze erişebilmek için kullanıcıların kimliğini yetkilendirmek ve kimliklerini doğrulamak için kullanılır. Diğer bir güvenlik değerlendirmesi, depolama sistemleri arasında aktarılırken ve depolanırken verileri yetkisiz kullanıcılardan koruyor. Aşağıdaki bölümlerde StorSimple ile sunulan veri koruma özellikleri açıklanır.

> [!NOTE]
> Yinelenenleri kaldırma, StorSimple cihazında ve Microsoft Azure depolama alanında depolanan veriler için ek koruma sağlar. Veriler yinelenirse, veri nesneleri eşlemek ve bunlara erişmek için kullanılan meta verilerden ayrı olarak depolanır: birim yapısına, dosya sistemine veya dosya adına göre verileri yeniden oluşturmak için kullanılabilir depolama düzeyi bağlamı yoktur.


## <a name="protect-data-flowing-through-the-service"></a>Hizmet üzerinden akan verileri koruma

StorSimple Aygıt Yöneticisi hizmetinin birincil amacı, StorSimple cihazını Yönetve yapılandırmaya sağlamaktır. StorSimple Aygıt Yöneticisi hizmeti Microsoft Azure çalışır. Cihaz yapılandırma verilerini girmek için Azure portal kullanır ve ardından Microsoft Azure StorSimple Aygıt Yöneticisi hizmetini kullanarak verileri cihaza gönderir. StorSimple, Azure hizmeti güvenliğinin tehlikeye girmesi, depolanan bilgilerin güvenliğinin aşılmasına yol gerektirmediğinden emin olmak için asimetrik anahtar çiftleri sistemini kullanır.

![Uçuş sırasında veri şifrelemesi](./media/storsimple-security/DataEncryption.png)

Asimetrik anahtar sistemi, hizmet üzerinden akan verilerin şu şekilde korunmasına yardımcı olur:

1. Asimetrik ortak ve özel anahtar çifti kullanan bir veri şifreleme sertifikası cihazda oluşturulur ve verileri korumak için kullanılır. Anahtarlar, ilk cihaz kaydedildiğinde oluşturulur.
2. Veri şifreleme sertifika anahtarları, kayıt sırasında ilk cihaz tarafından rastgele oluşturulan güçlü bir 128 bitlik anahtar olan hizmet veri şifreleme anahtarı tarafından korunan bir kişisel bilgi değişimi (. pfx) dosyasına aktarılmakta.
3. Sertifikanın ortak anahtarı, StorSimple Aygıt Yöneticisi hizmeti için güvenli şekilde kullanılabilir hale getirilir ve özel anahtar cihazda kalır.
4. Hizmetin girildiği veriler ortak anahtar kullanılarak şifrelenir ve cihazda depolanan özel anahtar kullanılarak şifresi çözülür ve Azure hizmetinin cihaza akan verilerin şifresini çözemeyeceğinden emin olur.

Hizmet veri şifreleme anahtarı yalnızca hizmete kayıtlı olan ilk cihazda oluşturulur. Hizmette kayıtlı olan tüm sonraki cihazların aynı hizmet veri şifreleme anahtarını kullanması gerekir.

> [!IMPORTANT]
> Hizmet veri şifreleme anahtarının bir kopyasını oluşturmak ve güvenli bir konuma kaydetmek çok önemlidir. Hizmet veri şifreleme anahtarının bir kopyası, yetkili bir kişi tarafından erişilebilmesi ve cihaz yöneticisine kolayca iletilebilmeleri için bu şekilde depolanmalıdır.
> 
> Hizmet veri şifreleme anahtarı kaybolursa, bir Microsoft destek sorumlusu, çevrimiçi durumda en az bir cihazınız olması şartıyla bu hizmeti almanıza yardımcı olabilir. Hizmet veri şifreleme anahtarını alındıktan sonra değiştirmenizi öneririz.

Hizmet veri şifreleme anahtarını ve ilgili veri şifreleme sertifikasını değiştirmek için, [StorSimple aygıt yöneticisi hizmetiniz için hizmet veri şifreleme anahtarını değiştirme](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)bölümündeki adımları izleyin. Şifreleme anahtarlarının değiştirilmesi, tüm cihazların yeni anahtarla güncelleştirilmesini gerektirir. Bu nedenle, tüm cihazlar çevrimiçi olduğunda anahtarı değiştirmenizi öneririz. Cihazlar çevrimdışıysa, anahtarları farklı bir zamanda değiştirilebilir. Güncel olmayan anahtarları olan cihazlar yine de yedeklemeler çalıştırabilecektir, ancak anahtar güncelleştirilene kadar verileri geri yükleyemeyecektir.

Hizmet veri şifreleme anahtarı ve veri şifreleme sertifikasının kullanım süreleri dolmaz. Bununla birlikte, anahtar güvenliğinin aşılmasına engel olmak için hizmet veri şifreleme anahtarını yıllık olarak değiştirmenizi öneririz.

## <a name="protect-data-at-rest"></a>Bekleyen verileri koruma

StorSimple cihazı, kullanım sıklığına bağlı olarak, verileri katmanda yerel olarak ve bulutta depolayarak verileri yönetir. Cihaza bağlı olan tüm konak makineler, verileri cihaza gönderir ve daha sonra verileri buluta doğru şekilde taşımış olur. Veriler, Internet üzerinden güvenli bir şekilde cihazdan buluta aktarılır. Her cihazda, bu cihazdaki tüm paylaşılan birimleri yüzey eden bir Iscsı hedefi vardır. Bulut depolamaya gönderilmeden önce tüm veriler şifrelenir. 

![Bulut depolama şifreleme anahtarı](./media/storsimple-security/CloudStorageEncryption.png)

StorSimple, verilerin güvenliğini ve bütünlüğünü buluta taşımadığından emin olmak için aşağıdaki gibi bulut depolama şifreleme anahtarlarını tanımlamanızı sağlar:

* Bir birim kapsayıcısı oluşturduğunuzda bulut depolama şifreleme anahtarını belirtirsiniz. Anahtar daha sonra değiştirilemez veya eklenemez.
* Bir birim kapsayıcısındaki tüm birimler aynı şifreleme anahtarını paylaşır. Belirli bir birim için farklı bir şifreleme biçimi istiyorsanız, bu birimi barındırmak için yeni bir birim kapsayıcısı oluşturmanızı öneririz.
* StorSimple Aygıt Yöneticisi hizmetine bulut depolama şifreleme anahtarını girdiğinizde, anahtar, hizmet veri şifreleme anahtarının ortak bölümü kullanılarak şifrelenir ve ardından cihaza gönderilir.
* Bulut depolama şifreleme anahtarı, hizmette herhangi bir yere depolanmaz ve yalnızca cihaz tarafından bilinir.
* Bir bulut depolama şifreleme anahtarı belirtmek isteğe bağlıdır. Konakta şifrelenmiş verileri cihaza gönderebilirsiniz.

### <a name="additional-security-best-practices"></a>Ek en iyi güvenlik uygulamaları

* Bölünmüş trafik: tamamen ayrılmış bir ağ dağıtarak ve fiziksel yalıtımın bir seçenek olmadığı VLAN 'Lar kullanarak, kurumsal LAN 'daki Kullanıcı trafiğinizi Iscsı SAN 'larınızı yalıtın. Iscsı depolaması için adanmış bir ağ, iş açısından kritik verilerinizin güvenliğini ve performansını garanti eder. Depolama ve kullanıcı trafiğinin kurumsal bir LAN üzerinden karıştırılması önerilmez ve gecikme süresini artırabilir ve ağ arızalarına neden olabilir.
* Konak tarafı ağ güvenliği için TCP/IP boşaltma altyapısını (TOE) destekleyen ağ arabirimlerini kullanın. TOE, ağ bağdaştırıcısında TCP 'yi işleyerek CPU yükünü azaltır.

## <a name="protect-data-via-storage-accounts"></a>Depolama hesapları aracılığıyla verileri koruma

Her Microsoft Azure aboneliği, bir veya daha fazla depolama hesabı oluşturabilir. (Depolama hesabı, Azure bulutu 'nda depolanan verilerle çalışmak için benzersiz bir ad alanı sağlar.) Bir depolama hesabına erişim, bu depolama hesabıyla ilişkili abonelik ve erişim anahtarları tarafından denetlenir.

Bir depolama hesabı oluşturduğunuzda Microsoft Azure, bir tane, StorSimple cihazı depolama hesabına eriştiğinde kimlik doğrulama için kullanılan 2 512 bitlik depolama erişim anahtarları oluşturur. Bu anahtarların yalnızca birinin kullanımda olduğunu unutmayın. Diğer anahtar, ayrılmış olarak tutulur ve anahtarları düzenli aralıklarla döndürmenize olanak tanır. Anahtarları döndürmek için ikincil anahtarı etkin hale getirin ve ardından birincil anahtarı silebilirsiniz. Daha sonra bir sonraki döndürme sırasında kullanılmak üzere yeni bir anahtar oluşturabilirsiniz. (Güvenlik nedenleriyle birçok veri merkezi için anahtar döndürme gerekir.)

Anahtar dönüşü için aşağıdaki en iyi yöntemleri izlemeniz önerilir:

* Depolama hesabınızın yetkisiz kullanıcılar tarafından erişilmediğinden emin olmak için depolama hesabı anahtarlarını düzenli olarak döndürmeniz gerekir.
* Düzenli olarak, Azure yöneticiniz, depolama hesabına doğrudan erişmek için Azure portal depolama bölümünü kullanarak birincil veya ikincil anahtarı değiştirmeli veya yeniden üretmelidir.

## <a name="protect-data-via-encryption"></a>Şifreleme yoluyla verileri koruma

StorSimple, StorSimple çözümünüzün bileşenleri arasında depolanan veya seyahat eden verileri korumak için aşağıdaki şifreleme algoritmalarını kullanır.

| Algoritma | Anahtar uzunluğu | Protokoller/uygulamalar/açıklamalar |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v 1.5, cihaza gönderilen yapılandırma verilerini şifrelemek için Azure portal tarafından kullanılır: Örneğin, depolama hesabı kimlik bilgileri, StorSimple cihaz yapılandırması ve bulut depolama şifreleme anahtarları. |
| AES |256 |CBC ile AES, hizmet veri şifreleme anahtarının ortak bölümünü StorSimple cihazından Azure portal gönderilmeden önce şifrelemek için kullanılır. Veri, bulut depolama hesabına gönderilmeden önce verileri şifrelemek için StorSimple cihazı tarafından da kullanılır. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance güvenliği

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Kişisel bilgileri yönetme

Fiziksel ve sanal serinin her ikisi için de StorSimple Aygıt Yöneticisi, aşağıdaki anahtar örneklerinde kişisel bilgileri toplar:

- Kullanıcıların e-posta adresinin yapılandırıldığı Kullanıcı ayarlarını uyarır. Bu bilgiler yönetici tarafından görüntülenebilir ve temizlenir. Bu hem StorSimple 8000 serisi cihazları hem de StorSimple Sanal dizileri için geçerlidir.
  * StorSimple 8000 serisinin ayarlarını görüntülemek ve temizlemek için, [StorSimple uyarılarını görüntüleme ve yönetme](storsimple-8000-manage-alerts.md#configure-alert-settings) bölümündeki adımları izleyin.
  * StorSimple Sanal dizisinin ayarlarını görüntülemek ve temizlemek için, [StorSimple uyarılarını görüntüleme ve yönetme](storsimple-virtual-array-manage-alerts.md#configure-alert-settings) bölümündeki adımları izleyin.
- Paylaşımlar üzerinde bulunan verilere erişebilen kullanıcılar. Paylaşma verilerine erişebilen kullanıcıların listesi görüntülenir ve görüntülenebilir. Bu liste paylaşımlar silindiğinde de silinir. Bu yalnızca StorSimple Sanal dizileri için geçerlidir.
  * Bir paylaşıma erişebilen veya silen Kullanıcı listesini görüntülemek için, [StorSimple Sanal dizisindeki paylaşımları yönetme](storsimple-virtual-array-manage-shares.md) bölümündeki adımları izleyin.

Daha fazla bilgi için, [Güven Merkezi](https://www.microsoft.com/trustcenter)’nde Microsoft Gizlilik ilkesini gözden geçirin.

## <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

Güvenlik ve Microsoft Azure StorSimple ilgili bazı sorular ve yanıtlar aşağıda verilmiştir.

**S:** Hizmetimin güvenliği aşılmış. Sonraki adımlarım ne olmalıdır?

**C:** Katmanlama verileri için kullanılan depolama hesabı için hizmet veri şifreleme anahtarını ve depolama hesabı anahtarlarını hemen değiştirmelisiniz. Yönergeler için şuraya gidin:

* [Hizmet veri şifreleme anahtarını değiştirme](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Depolama hesaplarının anahtar dönüşü](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**S:** Hizmet kayıt anahtarı isteyen yeni bir StorSimple cihazım var. Nasıl yaparım? almak mı istiyorsunuz?

**C:** Bu anahtar, StorSimple Aygıt Yöneticisi hizmetini ilk oluşturduğunuzda oluşturulmuştur. Cihaza bağlanmak için StorSimple Aygıt Yöneticisi hizmetini kullandığınızda, hizmet kayıt anahtarını görüntülemek veya yeniden oluşturmak için hizmet hızlı başlangıç sayfasını kullanabilirsiniz. Yeni bir hizmet kayıt anahtarı oluşturulması, var olan kayıtlı cihazları etkilemez. Yönergeler için şuraya gidin:

* [Hizmet kayıt anahtarını görüntüleyin veya yeniden oluşturun](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**S:** Hizmet veri şifreleme anahtarımı kaybettim. Ne yapmalıyım?

**C:** Microsoft Destek'e başvurun. Cihazınızda bir destek oturumunda oturum açabilir ve anahtarı almanıza yardımcı olur (en az bir cihaz çevrimiçi olarak sunulur). Hizmet veri şifreleme anahtarını elde ettikten hemen sonra, yeni anahtarın yalnızca sizin için bilindiğinden emin olmak için bunu değiştirmelisiniz. Yönergeler için şuraya gidin:

* [Hizmet veri şifreleme anahtarını değiştirme](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**S:**  Hizmet veri şifreleme anahtarı değişikliği için bir cihaz yetkilendirdim, ancak anahtar değiştirme işlemini başlatmadınız. Ne yapmalıyım?

**C:** Zaman aşımı süresi dolmuşsa, hizmet veri şifreleme anahtarı değişikliği için cihazı yeniden Yetkilendir ve işlemi yeniden başlatmanız gerekir.

**S:**  Hizmet veri şifreleme anahtarını değiştirdim, ancak diğer cihazları 4 saat içinde güncelleştirebildim. Yeniden başlatmak zorunda mıyım?

**C:** 4 saatlik zaman dilimi yalnızca değişikliği başlatıyor. Yetkili StorSimple cihazında güncelleştirme işlemini başlattıktan sonra, tüm cihazlar güncelleştirilene kadar yetkilendirme geçerlidir.

**S:** StorSimple yöneticimiz şirketten ayrıldı. Ne yapmalıyım?

**C:** StorSimple cihazına erişime izin veren parolaları değiştirin ve sıfırlayın ve yeni bilgilerin yetkisiz personel tarafından bilinmediğinden emin olmak için hizmet veri şifreleme anahtarını değiştirin. Yönergeler için şuraya gidin:

* [StorSimple parolalarınızı değiştirmek için StorSimple Aygıt Yöneticisi hizmetini kullanın](storsimple-8000-change-passwords.md)
* [Hizmet veri şifreleme anahtarını değiştirme](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [StorSimple cihazınız için CHAP yapılandırma](storsimple-8000-configure-chap.md)

**S:** StorSimple cihazına bağlanan bir konağa StorSimple Snapshot Manager parolasını sağlamak istiyorum, ancak parola kullanılamıyor. Ne yapabilirim?

**C:** Parolayı unuttuysanız yeni bir tane oluşturmanız gerekir. Ardından, var olan tüm kullanıcılara parolanın değiştirildiğini ve yeni parolayı kullanmak üzere istemcilerini güncelleştirmesi gerektiğini bildirin. Yönergeler için şuraya gidin:

* [StorSimple Snapshot Manager parolasını değiştirme](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Bir cihazın kimliğini doğrulama](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**S:** StorSimple için Windows PowerShell uzaktan erişim sertifikası cihazda değiştirildi. Uzaktan erişim istemcilerimi güncelleştirmek Nasıl yaparım? mı?

**C:** StorSimple Aygıt Yöneticisi hizmetinden yeni sertifikayı indirebilir ve sonra uzaktan erişim istemcilerinizin sertifika deposunda yüklü olmasını sağlayabilirsiniz. Yönergeler için şuraya gidin:

* [Import-Certificate cmdlet 'i](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**S:** StorSimple Aygıt Yöneticisi hizmeti tehlikeye atılırsa My Data Protected mi?

**C:** Hizmet yapılandırma verileri, bir Web tarayıcısında görüntülediğinizde ortak anahtarınızla her zaman şifrelenir. Hizmetin özel anahtara erişimi olmadığından, hizmet hiçbir veri göremez. StorSimple Aygıt Yöneticisi hizmeti tehlikeye atılırsa, StorSimple Aygıt Yöneticisi hizmetinde hiçbir anahtar depolanmadığı için hiçbir etkisi olmaz.

**S:** Veri şifreleme sertifikasına birisi erişim kazandığında, verilerim tehlikeye girebilir mi?

**C:** Microsoft Azure, müşterinin veri şifreleme anahtarını (. pfx dosyası) şifrelenmiş bir biçimde depolar. . Pfx dosyası şifrelendiğinden ve StorSimple hizmeti. pfx dosyasının şifresini çözmek için hizmet veri şifreleme anahtarına sahip olmadığından, yalnızca. pfx dosyasına erişim elde etmek herhangi bir gizli dizi sunmaz.

**S:** Bir kamu varlığı verileri Microsoft 'a isterse ne olur?

**C:** Tüm veriler hizmette şifrelendiğinden ve özel anahtar cihazla tutulduğundan, kamu varlığı müşteriden veri sormalıdır.



## <a name="next-steps"></a>Sonraki adımlar

[StorSimple cihazınızı dağıtın](storsimple-8000-deployment-walkthrough-u2.md).

