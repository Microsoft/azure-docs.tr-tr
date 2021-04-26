---
title: 'Öğretici: yeni ortama Blueprint örneği'
description: Bu öğreticide, iki kaynak grubu ayarlayan ve her biri için bir rol ataması yapılandıran bir şema tanımı oluşturmak üzere bir şema örneği kullanırsınız.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915566"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Öğretici: bir şema örneğinden ortam oluşturma

Örnek planlar, Azure şemaları kullanılarak neler yapılabileceğini örnekler sağlar. Her biri belirli bir amaç veya amaca sahip bir örnektir, ancak kendileri tarafından tamamen bir ortam oluşturmaz. Her biri, dahil edilen yapıların, tasarımların ve parametrelerin çeşitli birleşimleri ile Azure şemaları kullanılarak araştırılacak başlangıç yeri olarak hazırlanmıştır.

Aşağıdaki öğreticide, Azure şemaları hizmetinin farklı yönlerini göstermek için RBAC şeması örneği **Ile kaynak grupları** kullanılmaktadır. Aşağıdaki adımlar ele alınmıştır:

> [!div class="checklist"]
> - Örnekten yeni bir şema tanımı oluşturun
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın
> - Atama için dağıtılan kaynakları İncele
> - Kilitleri kaldırmak için şema atamasını kaldırma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için bir Azure aboneliği gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-blueprint-definition-from-sample"></a>Örnekten şema tanımı oluştur

İlk olarak, şema örneğini uygulayın. İçeri aktarma, ortamınızda örneğe bağlı olarak yeni bir şema oluşturur.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında RBAC şeması örneği **ile kaynak gruplarını** bulun ve seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: şema örneğinin kopyasına bir ad verin. Bu öğreticide,- _role-atamaları ile birlikte iki-RGS_ adı kullanacağız.
   - **Tanım konumu**: üç noktayı kullanın ve örnek kopyanızı kaydetmek için yönetim grubunu veya aboneliği seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Bu örnek, _prodrg_ ve _preprodrg_ görünen adları ile iki kaynak grubu tanımlar. Her kaynak grubunun son adı ve konumu, şema atama sırasında ayarlanır. _Prodrg_ kaynak grubuna _katkıda_ bulunan rolü atanır ve _Preprodrg_ kaynak grubuna _sahip_ ve _Okuyucular_ rolleri atanır. Tanımda atanan roller statiktir, ancak rol atanmış kullanıcı, uygulama veya Grup, şema atama sırasında ayarlanır.

1. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

Bu adım, seçili yönetim grubunda veya abonelikte örnek şema tanımının bir kopyasını oluşturur. Kayıtlı şema tanımı, sıfırdan oluşturulan herhangi bir şema gibi yönetilir. Örneği, gereken sayıda yönetim grubunuza veya aboneliğine kaydedebilirsiniz. Ancak, her kopyaya benzersiz bir ad verilmelidir.

Şema **tanımını kaydetme başarılı oldu** Portal bildirimi göründüğünde, sonraki adıma geçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir. Bu öğreticide hiçbir değişiklik yapmayacağız.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. _İki-RGS-rol-atamaları_ şema tanımını bulmak için filtreleri kullanın ve ardından bunu seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni bölmede, şema örneğinin kopyası için **Sürüm** _1,0_ olarak sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "RBAC şeması örneği ile kaynak gruplarından yayımlanan Ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

Bu adım, şema 'in bir aboneliğe atanmasını olanaklı kılar. Yayımlandıktan sonra değişiklikler yine de yapılabilir. Aynı şema tanımının farklı sürümleri arasındaki farkları izlemek için ek değişiklikler yeni bir **Sürüm** değeriyle yayımlamayı gerektirir.

Şema **tanımını yayımlama başarılı oldu** Portal bildirimi göründüğünde bir sonraki adıma geçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. _İki-RGS-rol-atamaları_ şema tanımını bulmak için filtreleri kullanın ve ardından bunu seçin.

1. Şema tanımı sayfasının en üstünde **Şema ata**’yı seçin.

1. Şema ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler

     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreler kullanılarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, şema tanımının adına göre önceden doldurulur.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).
       Bu öğretici için _Doğu ABD 2_' yi seçin.
     - Şema **tanımı sürümü**: örnek şema tanımının **yayınlanmış** sürüm _1,0_ ' i seçin.

   - Kilit ataması

     _Salt okuma_ şeması kilit modunu seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan _sistem atanmış_ seçeneğini bırakın. Daha fazla bilgi için bkz. [Yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../concepts/parameters.md#dynamic-parameters) . Her yapıt için, parametre değerini **değer** sütununda tanımlananla ayarlayın. İçin `{Your ID}` , Azure kullanıcı hesabınızı seçin.

     |Yapıt adı|Yapıt türü|Parametre adı|Değer|Açıklama|
     |-|-|-|-|-|
     |ProdRG kaynak grubu|Kaynak grubu|Name|Üretim RG|İlk kaynak grubunun adını tanımlar.|
     |ProdRG kaynak grubu|Kaynak grubu|Konum|Batı ABD 2|İlk kaynak grubunun konumunu ayarlar.|
     |Katılımcı|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İlk kaynak grubu içinde _katkıda_ bulunan rol atamasını hangi kullanıcı veya gruba veririm tanımlar.|
     |PreProdRG kaynak grubu|Kaynak grubu|Name|Ön üretim RG|İkinci kaynak grubunun adını tanımlar.|
     |PreProdRG kaynak grubu|Kaynak grubu|Konum|Batı ABD|İkinci kaynak grubunun konumunu ayarlar.|
     |Sahip|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İkinci kaynak grubu içinde _sahip_ rolü atamasını hangi kullanıcı veya gruba veririm tanımlar.|
     |Okuyucular|Rol ataması|Kullanıcı veya Grup|{KIMLIĞINIZ}|İkinci kaynak grubu içinde _Okuyucular_ rolü atamasını hangi kullanıcı veya gruba veririm tanımlar.|

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin.

Bu adım, tanımlı kaynakları dağıtır ve seçili **kilit atamasını** yapılandırır. Blueprint kilitlerinin uygulanması 30 dakika kadar sürebilir.

Şema **tanımı başarılı oldu** Portal bildirimi seçildikten sonra bir sonraki adıma geçin.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Atama tarafından dağıtılan kaynakları İncele

Şema ataması, şema tanımında tanımlanan yapıtları oluşturur ve izler. Nesnelerin durumunu şema atama sayfasından görebilir ve kaynaklara doğrudan bakarak erişebilirsiniz.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **atanan** şemalar sayfasını seçin. ------------------ _Role-_ ----------

   Bu sayfadan, atamanın başarılı olduğunu ve oluşturulan kaynakların listesini şema Lock durumuyla birlikte görebiliriz. Atama güncelleştirilirse, **atama işlemi** açılır listesi her tanım sürümünün dağıtımıyla ilgili ayrıntıları gösterir. Oluşturulan listelenen her kaynak seçilebilir ve bu kaynaklar özellik sayfasını açabilir.

1. **Üretim RG** kaynak grubunu seçin.

   Kaynak grubunun adının, yapıt görünen adı _Prodrg_ değil, **Üretim** için olduğunu görüyoruz. Bu ad, şema ataması sırasında ayarlanan değerle eşleşir.

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını ve ardından **rol atamaları** sekmesini seçin.

   Burada, hesabınıza _Bu kaynağın_ kapsamında _katkıda_ bulunan rolü verildiğini görüyoruz. -----------------  --------------- _Rol atamaları_ Bu izinler, yapılandırılmış şema kilitleri olan kaynakları yönetmek için de kullanılır.

1. Azure portal içerik haritasında, bir sayfa geri **gitmek için----** -------------  ------------

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını ve ardından **rol atamaları** sekmesini seçin.

   Burada, hesabınıza hem _sahip_ hem de _okuyucu_ rollerinin verildiğini, her ikisi de _Bu kaynağın_ kapsamını görürsünüz. Şema atamasının aynı zamanda ilk kaynak grubu gibi _sahip_ rolü de vardır.

1. **Atamaları Reddet** sekmesini seçin.

   Şema ataması, _salt okuma_ şeması kilit modunu zorlamak için dağıtılan kaynak grubunda bir [reddetme ataması](../../../role-based-access-control/deny-assignments.md) oluşturdu. Reddetme ataması, _rol atamaları_ sekmesinde uygun haklara sahip birinin belirli eylemleri almasını engeller. Reddetme ataması _tüm sorumluları_ etkiler.

1. Reddetme atamasını seçin, ardından sol taraftaki **Izinleri reddedildi** sayfasını seçin.

   Reddetme ataması **\* *_ ve _* eylem** yapılandırmasıyla tüm işlemleri engellemektedir, ancak **NotActions** aracılığıyla **\* /Read** 'i dışlayarak okuma erişimine izin verir.

1. Azure portal içerik haritasında, **ön üretim RG-Access Control (IAM)** seçeneğini belirleyin. Ardından sol taraftaki **genel bakış** sayfasını ve ardından **kaynak grubunu sil** düğmesini seçin. Silmeyi onaylamak için _Preüretim RG_ adını girin ve bölmenin altındaki **Sil** ' i seçin.

   Portal bildirim **silme kaynak grubu ön üretim RG başarısız oldu** . Hata, hesabınız kaynak grubunu silme iznine sahip olsa da, şema atama tarafından erişim reddedilir. Şema atama sırasında _yalnızca okuma_ şeması kilit modunu seçtiğinizi unutmayın. Şema Lock, izin olan bir hesabın, hatta _sahibi_, kaynağın silinmesini engeller. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

Bu adımlar, kaynaklarımızın tanımlandığı şekilde oluşturulduğunu ve şema kilitleri izin içeren bir hesaptan bile istenmeyen silme işlemini engellediğini gösterir.

## <a name="unassign-the-blueprint"></a>Şema atamasını kaldırma

Son adım, şema 'in ve dağıtıldığı kaynakların atanmasını kaldırmakta.
Atamanın kaldırılması dağıtılan yapıtları kaldırmaz.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

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

Bu öğreticide, örnek tanımdan yeni bir şema oluşturmayı öğrendiniz. Azure şemaları hakkında daha fazla bilgi edinmek için şema yaşam döngüsü makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Şema yaşam döngüsü hakkında bilgi edinin](../concepts/lifecycle.md)