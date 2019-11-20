---
title: Şema örneğinden ortam oluşturma
description: İki kaynak grubu ayarlayan ve her biri için bir rol ataması yapılandıran bir şema tanımı oluşturmak için bir şema örneği kullanın.
ms.date: 03/05/2019
ms.topic: tutorial
ms.openlocfilehash: d23e9b7242c58e4da5fcfe5ef4d29d9f9df6f754
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960281"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Öğretici: bir şema örneğinden ortam oluşturma

Örnek planlar, Azure şemaları kullanılarak neler yapılabileceğini örnekler sağlar. Her biri belirli bir amaç veya amaca sahip bir örnektir, ancak kendileri tarafından tamamen bir ortam oluşturmaz. Her biri, dahil edilen yapıların, tasarımların ve parametrelerin çeşitli birleşimleri ile Azure şemaları kullanılarak araştırılacak başlangıç yeri olarak hazırlanmıştır.

Aşağıdaki öğreticide, planlar hizmetinin farklı yönlerini göstermek için RBAC şeması örneği **Ile kaynak grupları** kullanılmaktadır. Aşağıdaki adımlar ele alınmıştır:

> [!div class="checklist"]
> - Örnekten yeni bir şema tanımı oluşturun
> - Örnek kopyanızı **yayımlandı** olarak işaretleyin
> - Şema kopyanızı mevcut bir aboneliğe atama
> - Atama için dağıtılan kaynakları İncele
> - Kilitleri kaldırmak için şema atamasını kaldırma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için bir Azure aboneliği gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-blueprint-definition-from-sample"></a>Örnekten şema tanımı oluştur

İlk olarak, şema örneğini uygulayın. İçeri aktarma, ortamınızda örneğe bağlı olarak yeni bir şema oluşturur.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Soldaki **Başlarken** sayfasında, şema _Oluştur_altında **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında RBAC şeması örneği **ile kaynak gruplarını** bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneği _hakkında temel bilgileri_ girin:

   - **Şema adı**: şema örneğinin kopyasına bir ad verin. Bu öğreticide,- _role-atamaları ile birlikte iki-RGS_adı kullanacağız.
   - **Tanım konumu**: üç noktayı kullanın ve örnek kopyanızı kaydetmek için yönetim grubunu veya aboneliği seçin.

1. Sayfanın üst kısmındaki _yapıtlar_ sekmesini veya sonraki: sayfanın en altındaki **yapıtları** seçin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Bu örnek, _prodrg_ ve _preprodrg_görünen adları ile iki kaynak grubu tanımlar. Her kaynak grubunun son adı ve konumu, şema atama sırasında ayarlanır. _Prodrg_ kaynak grubuna _katkıda_ bulunan rolü atanır ve _Preprodrg_ kaynak grubuna _sahip_ ve _Okuyucular_ rolleri atanır. Tanımda atanan roller statiktir, ancak rol atanmış kullanıcı, uygulama veya Grup, şema atama sırasında ayarlanır.

1. Şema örneğini gözden geçirmeyi bitirdiğinizde **Taslağı kaydet** ' i seçin.

Bu adım, seçili yönetim grubunda veya abonelikte örnek şema tanımının bir kopyasını oluşturur. Kayıtlı şema tanımı, sıfırdan oluşturulan herhangi bir şema gibi yönetilir. Örneği, gereken sayıda yönetim grubunuza veya aboneliğine kaydedebilirsiniz. Ancak, her kopyaya benzersiz bir ad verilmelidir.

Şema **tanımını kaydetme başarılı oldu** Portal bildirimi göründüğünde, sonraki adıma geçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı Yayımla

Şema örneğinin kopyası artık ortamınızda oluşturulmuştur. **Taslak** modunda oluşturulur ve atanmadan ve dağıtılmadan önce **yayımlanmaları** gerekir. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir. Bu öğreticide hiçbir değişiklik yapmayacağız.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. _İki-RGS-rol-atamaları_ şema tanımını bulmak için filtreleri kullanın ve ardından bunu seçin.

1. Sayfanın üst kısmındaki şemayı **Yayımla** ' yı seçin. Sağdaki yeni bölmede, şema örneğinin kopyası için **Sürüm** _1,0_ olarak sağlayın. Daha sonra bir değişiklik yaparsanız, bu özellik için faydalıdır. "RBAC şeması örneği ile kaynak gruplarından yayımlanan Ilk sürüm" gibi **değişiklik notları** sağlayın. Ardından sayfanın alt kısmında **Yayımla** ' yı seçin.

Bu adım, şema 'in bir aboneliğe atanmasını olanaklı kılar. Yayımlandıktan sonra değişiklikler yine de yapılabilir. Aynı şema tanımının farklı sürümleri arasındaki farkları izlemek için ek değişiklikler yeni bir **Sürüm** değeriyle yayımlamayı gerektirir.

Şema **tanımını yayımlama başarılı oldu** Portal bildirimi göründüğünde bir sonraki adıma geçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan**sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adım, her bir şema örneğinin kopyasının her dağıtımını yapmak için parametrelerin sağlandığı yerdir.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. _İki-RGS-rol-atamaları_ şema tanımını bulmak için filtreleri kullanın ve ardından bunu seçin.

1. Şema tanım sayfasının en üstünde şema **ata** ' yı seçin.

1. Şema atamasının parametre değerlerini sağlayın:

   - Temel Bilgiler

     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreleri kullanarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, şema tanımının adına göre önceden doldurulur.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).
       Bu öğretici için _Doğu ABD 2_' yi seçin.
     - Şema **tanımı sürümü**: örnek şema tanımının **yayınlanmış** sürüm _1,0_ ' i seçin.

   - Kilit ataması

     _Salt okuma_ şeması kilit modunu seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan _sistem atanmış_ seçeneğini bırakın. Daha fazla bilgi için bkz. [Yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlanan yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../concepts/parameters.md#dynamic-parameters) . Her yapıt için, parametre değerini **değer** sütununda tanımlananla ayarlayın. `{Your ID}`için Azure kullanıcı hesabınızı seçin.

     |Yapıt adı|Yapıt türü|Parametre adı|Değer|Açıklama|
     |-|-|-|-|-|
     |ProdRG kaynak grubu|Kaynak grubu|Ad|Üretim RG|İlk kaynak grubunun adını tanımlar.|
     |ProdRG kaynak grubu|Kaynak grubu|Konum|Batı ABD 2|İlk kaynak grubunun konumunu ayarlar.|
     |Katılımcı|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İlk kaynak grubu içinde _katkıda_ bulunan rol atamasını hangi kullanıcı veya gruba veririm tanımlar.|
     |PreProdRG kaynak grubu|Kaynak grubu|Ad|Ön üretim RG|İkinci kaynak grubunun adını tanımlar.|
     |PreProdRG kaynak grubu|Kaynak grubu|Konum|Batı ABD|İkinci kaynak grubunun konumunu ayarlar.|
     |Sahip|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İkinci kaynak grubu içinde _sahip_ rolü atamasını hangi kullanıcı veya gruba veririm tanımlar.|
     |Okuyucular|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İkinci kaynak grubu içinde _Okuyucular_ rolü atamasını hangi kullanıcı veya gruba veririm tanımlar.|

1. Tüm parametreler girildikten sonra sayfanın alt kısmındaki **ata** ' yı seçin.

Bu adım, tanımlı kaynakları dağıtır ve seçili **kilit atamasını**yapılandırır. Blueprint kilitlerinin uygulanması 30 dakika kadar sürebilir.

Şema **tanımı başarılı oldu** Portal bildirimi seçildikten sonra bir sonraki adıma geçin.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Atama tarafından dağıtılan kaynakları İncele

Şema ataması, şema tanımında tanımlanan yapıtları oluşturur ve izler. Nesnelerin durumunu şema atama sayfasından görebilir ve kaynaklara doğrudan bakarak erişebilirsiniz.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **atanan** şemalar sayfasını seçin. ------------------ _Role-_ ----------

   Bu sayfadan, atamanın başarılı olduğunu ve oluşturulan kaynakların listesini şema Lock durumuyla birlikte görebiliriz. Atama güncelleştirilirse, **atama işlemi** açılır listesi her tanım sürümünün dağıtımıyla ilgili ayrıntıları gösterir. Oluşturulan her kaynak, tıklanmış ve bu kaynaklar özellik sayfasını açacak.

1. **Üretim RG** kaynak grubunu seçin.

   Kaynak grubunun adının, yapıt görünen adı _Prodrg_değil, **Üretim** için olduğunu görüyoruz. Bu ad, şema ataması sırasında ayarlanan değerle eşleşir.

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını ve ardından **rol atamaları** sekmesini seçin.

   Burada, hesabınıza _Bu kaynağın_kapsamında _katkıda_ bulunan rolü verildiğini görüyoruz. ----------------- --------------- _Rol atamaları_ Bu izinler, yapılandırılmış şema kilitleri olan kaynakları yönetmek için de kullanılır.

1. Azure portal içerik haritasında, bir sayfa geri **gitmek için----** ------------- ------------

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını ve ardından **rol atamaları** sekmesini seçin.

   Burada, hesabınıza hem _sahip_ hem de _okuyucu_ rollerinin verildiğini, her ikisi de _Bu kaynağın_kapsamını görürsünüz. Şema atamasının aynı zamanda ilk kaynak grubu gibi _sahip_ rolü de vardır.

1. **Atamaları Reddet** sekmesini seçin.

   Şema ataması, _salt okuma_ şeması kilit modunu zorlamak için dağıtılan kaynak grubunda bir [reddetme ataması](../../../role-based-access-control/deny-assignments.md) oluşturdu. Reddetme ataması, _rol atamaları_ sekmesinde uygun haklara sahip birinin belirli eylemleri almasını engeller. Reddetme ataması _tüm sorumluları_etkiler.

1. Reddetme atamasını seçin, ardından sol taraftaki **Izinleri reddedildi** sayfasını seçin.

   Reddetme ataması **\*** ve **eylem** yapılandırmasıyla tüm Işlemleri engellemektedir, ancak **NotActions**aracılığıyla **\*/Read** 'i dışlayarak okuma erişimine izin verir.

1. Azure portal içerik haritasında, **ön üretim RG-Access Control (IAM)** seçeneğini belirleyin. Ardından sol taraftaki **genel bakış** sayfasını ve ardından **kaynak grubunu sil** düğmesini seçin. Silmeyi onaylamak için _Preüretim RG_ adını girin ve bölmenin altındaki **Sil** ' i seçin.

   Portal bildirim **silme kaynak grubu ön üretim RG başarısız oldu** . Hata, hesabınız kaynak grubunu silme iznine sahip olsa da, şema atama tarafından erişim reddedilir. Şema atama sırasında _yalnızca okuma_ şeması kilit modunu seçtiğinizi unutmayın. Şema Lock, izin olan bir hesabın, hatta _sahibi_, kaynağın silinmesini engeller. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

Bu adımlar, kaynaklarımızın tanımlandığı şekilde oluşturulduğunu ve şema kilitleri izin içeren bir hesaptan bile istenmeyen silme işlemini engellediğini gösterir.

## <a name="unassign-the-blueprint"></a>Şema atamasını kaldırma

Son adım, şema 'in ve dağıtıldığı kaynakların atanmasını kaldırmakta.
Atamanın kaldırılması dağıtılan yapıtları kaldırmaz.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **atanan** şemalar sayfasını seçin. ------------------ _Role-_ ----------

1. Sayfanın üst kısmındaki **şema atamasını Kaldır** düğmesini seçin. Onay iletişim kutusunda uyarıyı okuyun ve **Tamam**' ı seçin.

   Şema ataması kaldırıldığında, şema kilitleri da kaldırılır. Oluşturulan kaynaklar, izinleri olan bir hesap tarafından yeniden silinir.

1. Azure menüsünden **kaynak grupları** ' nı seçin ve ardından **Üretim RG**' yi seçin.

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını ve ardından **rol atamaları** sekmesini seçin.

Her kaynak grubu için güvenlik, dağıtılan rol atamalarına hala sahiptir, ancak şema atamasının artık _sahip_ erişimi yoktur.

Şema **atamasını kaldırma başarılı oldu** Portal bildirimi göründüğünde bir sonraki adıma geçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyle işiniz bittiğinde aşağıdaki kaynakları silin:

- Kaynak grubu _Üretim RG_
- Kaynak grubu _ön üretim RG_
- Blueprint Definition _iki-RGS-rol-atamalar_

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin
- [Statik ve dinamik parametreleri](../concepts/parameters.md) kullanmayı anlayın
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin
- [Şema sıralamasını](../concepts/sequencing-order.md) özelleştirmeyi öğrenin
- [Var olan atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin