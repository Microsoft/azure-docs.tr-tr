---
title: Yerleşik bir kök veya apex etki alanı için Azure portalını kullanarak mevcut bir ön kapısı
description: Bilgi nasıl eklemek için var olan bir ön kapısı Azure portalını kullanarak bir kök veya apex etki.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605786"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Yerleşik bir kök veya apex etki alanında, ön kapısı
Azure ön kapısı CNAME kayıtları, özel etki alanlarını ekleme için etki alanı sahipliğini doğrulamak için kullanır. Ayrıca, ön kapısı profilinizle ilişkili ön uç IP adresi ön kapısı ortaya çıkarmıyor ve amaç için yerleşik Azure ön kapısı etkinleştirilmişse bu nedenle, Tepe etki alanınız bir IP adresi için eşlenemiyor.

DNS protokolü, bölge tepesinde CNAME kayıtları atanmasını engeller. Örneğin, etki alanınız varsa `contoso.com`; CNAME kayıtları için oluşturabileceğiniz `somelabel.contoso.com`; ancak için CNAME oluşturamazsınız `contoso.com` kendisi. Bu kısıtlama, yük dengeli uygulamalarda arkasında Azure ön kapısı sahip uygulama sahipleri için bir sorunu gösterir. Bir ön kapısı profili kullanarak bir CNAME kaydı oluşturulmasını gerektirdiğinden, bölge tepesinde ön kapısı profilde işaret etmek mümkün değildir.

Bu sorun, Azure DNS diğer ad kayıtlarını kullanan çözülür. CNAME kayıtları aksine bölgenin tepesinde diğer ad kayıtlarını oluşturulur ve uygulama sahipleri kendi bölge tepesinde kayıt ortak Uç noktalara sahip bir ön kapısı profiline işaret edecek şekilde kullanabilirsiniz. Uygulama sahibi kendi DNS bölgesi içinde başka bir etki alanı için kullanılan aynı ön kapısı profilini gelin. Örneğin, `contoso.com` ve `www.contoso.com` aynı ön kapısı profiline işaret edebilir. 

Apex veya kök etki alanı ön kapısı profilinizi temel eşleme CNAME düzleştirme veya birleştirme izleme, DNS, bir IP adresi sayısına ulaşana kadar DNS CNAME girişi sağlayıcısı yinelemeli olarak nerede çözümler bir mekanizma olduğu gerektirir. Bu işlev için ön kapı uç noktaları Azure DNS tarafından desteklenir. 

> [!NOTE]
> Ancak, DNS arama, kendi etki alanı barındırma için müşterileri için Azure DNS'yi kullanarak Azure ön kapısı önerir veya CNAME düzleştirme destekleyen diğer DNS sağlayıcıları de vardır.

Azure portalında yerleşik bir tepe etki alanı, ön kapısı kullanın ve HTTPS üzerinde SSL sonlandırma için bir sertifika ile ilişkilendirerek etkinleştirin. Apex etki alanları, kök veya çıplak etki alanları olarak da adlandırılır.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Ön kapısı profiliniz için bir diğer ad kaydı oluşturma
> * Kök etki alanı için ön kapı Ekle
> * Kök etki alanı üzerinde HTTPS'yi ayarlama

> [!NOTE]
> Bu öğreticide, oluşturulan bir ön kapısı profili zaten sahip olmasını gerektirir. Diğer öğreticiler gibi bakın [hızlı başlangıç: Bir ön kapı oluşturmak](./quickstart-create-front-door.md) veya [ön kapısı HTTP ile HTTPS'ye yönlendirmeyi oluşturma](./front-door-how-to-redirect-https.md) kullanmaya başlamak için.

## <a name="create-an-alias-record-for-zone-apex"></a>Bölge tepesinde için bir diğer ad kaydı oluşturun

1. Açık **Azure DNS** yapılacak etki alanı için yapılandırma.
2. Oluşturma veya kaydı bölge tepesinde düzenleyin.
3. Kaydı seçin **türü** olarak _A_ kaydedin ve ardından _Evet_ için **diğer ad kayıt kümesi**. **Diğer ad türü** ayarlanmalıdır _Azure kaynak_.
4. Ön kapısı profilinizi barındırıldığı Azure aboneliğini seçin ve ardından ön kapısı kaynak seçin **Azure kaynak** açılır.
5. Tıklayın **Tamam** yaptığınız değişiklikleri göndermek için.

    ![Bölge tepesinde için diğer ad kaydı](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Yukarıdaki adım ön kapısı kaynağınızı ve ayrıca bir CNAME kaydı eşlemesini 'afdverify' işaret eden bir bölge tepesinde kayıt oluşturur (örnek - `afdverify.contosonews.com`) için `afdverify.<name>.azurefd.net` olacak ön kapısı profilinizde etki alanı ekleme için kullanılan.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Yerleşik özel etki alanınızı ön kapısı

1. Ön kapısı Tasarımcısı sekmesindeki tıklayarak '+' simgesi ön uç konaklardaki bölümünde yeni bir özel etki alanı eklemek için.
2. Kök veya apex etki alanı adı özel ana bilgisayar adı alanına örnek `contosonews.com`.
3. Etki alanı arasında CNAME eşlemesi için ön kapısı doğrulandıktan sonra tıklayarak **Ekle** özel etki alanı eklemek için.
4. Tıklayın **Kaydet** değişiklikleri göndermek için.

![Özel etki alanı menüsü](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Özel etki alanınızda HTTPS'yi etkinleştirme

1. Eklenmiş olan özel bir etki alanında bulunan ve bölümünde **özel etki alanı HTTPS**, durumunu değiştir **etkin**.
2. Seçin **Sertifika Yönetim türü** için _'kendi Sertifikamı Kullan'_ .

> [!WARNING]
> Apex veya kök etki alanı için ön kapı yönetilen Sertifika Yönetim türü şu anda desteklenmiyor. Ön kapısı apex veya kök etki alanı üzerinde HTTPS'yi etkinleştirmek için kullanılabilecek tek seçenek, Azure Key Vault üzerinde barındırılan kendi özel SSL sertifikası kullanıyor.

3. Kurulum, anahtar kasası sonraki adıma devam etmeden önce kullanıcı arabiriminde belirtildiği gibi erişmek ön kapı için doğru izinlere sahip olun.
4. Seçin bir **anahtar kasası hesabı** geçerli abonelik ve ardından uygun **gizli** ve **gizli dizi sürümü** doğru sertifikaya eşlemek için.
5. Tıklayarak **güncelleştirme** seçimi kaydedin ve ardından **Kaydet**.
6. Tıklayın **Yenile** sonra birkaç dakika ve ardından tekrar sertifika hazırlama işleminizin ilerleme durumunu görmek için özel etki alanı üzerinde. 

> [!WARNING]
> Apex etki alanınız için uygun yönlendirme kuralları oluşturduğunuz veya etki alanı için var olan yönlendirme kurallarını eklenen emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.