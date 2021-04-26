---
title: Var olan bir ön kapıya bir kök veya tepesinde etki alanı ekleme-Azure portal
description: Azure portal kullanarak var olan bir ön kapıya bir kök veya tepesinde etki alanı eklemeyi öğrenin.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646347"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Front Door’unuza kök veya apex etki alanı ekleme
Azure ön kapısı, özel etki alanlarını eklemek için etki alanı sahipliğini doğrulamak üzere CNAME kayıtlarını kullanır. Ön kapı, ön kapı profilinizle ilişkili olan ön uç IP adresini kullanıma sunmaz. Bu nedenle, sizin amacınızı Azure ön kapısına eklemek istiyorsanız tepesinde etki alanınızı bir IP adresine eşleyemezsiniz.

DNS protokolü, CNAME kayıtlarının tepesinde bölgesinde atanmasını engeller. Örneğin, etki alanınız ise `contoso.com` ; IÇIN CNAME kayıtları oluşturabilirsiniz, `somelabel.contoso.com` ancak kendısı için CNAME oluşturamazsınız `contoso.com` . Bu kısıtlama, Azure ön kapısının arkasında yük dengeli uygulamalara sahip olan uygulama sahipleri için bir sorun gösterir. Bir ön kapı profili kullanmak için bir CNAME kaydı oluşturulması gerektiğinden, bölge tepesinde ön kapı profilini işaret etmek mümkün değildir.

Bu sorun, Azure DNS diğer ad kayıtları kullanılarak çözülebilir. CNAME kayıtlarının aksine, diğer ad kayıtları tepesinde bölgesinde oluşturulur. Uygulama sahipleri, bölge tepesinde kaydını ortak uç noktalara sahip bir ön kapılı profile işaret etmek için bunu kullanabilir. Uygulama sahipleri, DNS bölgesi içindeki diğer etki alanı için kullanılan aynı ön kapı profilini işaret. Örneğin, `contoso.com` ve `www.contoso.com` aynı ön kapı profilini işaret edebilir. 

Tepesinde veya kök etki alanınızı ön kapı profilinize eşleme, temelde CNAME düzleştirme veya DNS izleme gerektirir. DNS sağlayıcının bir IP adresine çıkana kadar CNAME girişini yinelemeli olarak çözümleyen bir mekanizma. Bu işlevsellik, ön kapı uç noktaları için Azure DNS desteklenir. 

> [!NOTE]
> CNAME düzleştirme veya DNS ile izleme desteği sunan başka DNS sağlayıcıları da vardır, ancak Azure ön kapısı, kendi etki alanlarını barındırmak için müşterilerinin Azure DNS kullanılmasını önerir.

Ön kapıya bir tepesinde etki alanı eklemek ve bunu TLS sonlandırmasına yönelik sertifikayla ilişkilendirerek HTTPS 'yi etkinleştirmek için Azure portal kullanabilirsiniz. Tepesinde etki alanları, kök veya Naked etki alanları olarak da adlandırılır.

## <a name="create-an-alias-record-for-zone-apex"></a>Bölge tepesinde için diğer ad kaydı oluşturma

1. Etki alanının eklendi olması için **Azure DNS** yapılandırma açın.

1. Bölge tepesinde için kayıt oluşturun veya düzenleyin.

1. Kayıt **türünü** *bir* kayıt olarak seçin ve ardından **diğer ad kayıt kümesi** için *Evet* ' i seçin. **Diğer ad türü** *Azure kaynağı* olarak ayarlanmalıdır.

1. Ön kapı profilinizin barındırıldığı Azure aboneliğini seçin. Ardından **Azure Kaynak** açılan listesinden ön kapı kaynağını seçin.

1. Değişikliklerinizi göndermek için **Tamam ' ı** seçin.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Bölge tepesinde için diğer ad kaydı":::

1. Yukarıdaki adım, ön kapılarınızın kaynağını gösteren bir bölge tepesinde kaydı ve ayrıca `afdverify.contosonews.com` ön kapı profilinizde etki alanını eklemek için kullanılacak BIR CNAME kaydı eşlemesi ' afdverify ' (örnek-) oluşturur.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Özel etki alanını ön kapıya ekleme

1. Ön kapı Tasarımcısı sekmesinde, ön uç konaklar bölümünde ' + ' simgesi ' ni seçerek yeni bir özel etki alanı ekleyin.

1. Özel ana bilgisayar adı alanına kök veya tepesinde etki alanı adını girin, örneğin `contosonews.com` .

1. Etki alanından ön kapıya yönelik CNAME eşlemesi doğrulandıktan sonra, özel etki alanını eklemek için **Ekle** ' yi seçin.

1. Değişiklikleri göndermek için **Kaydet** ' i seçin.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Özel etki alanı menüsü":::

## <a name="enable-https-on-your-custom-domain"></a>Özel etki alanınız üzerinde HTTPS 'yi etkinleştirme

1. Eklenen özel etki alanını ve **özel etki alanı https** bölümünü seçin, durumu **etkin** olarak değiştirin.

1. *' Kendi sertifikamı kullan '* olarak **sertifika yönetimi türünü** seçin.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Özel etki alanı HTTPS ayarları":::    

   > [!WARNING]
   > Ön kapı tarafından yönetilen sertifika yönetimi türü, tepesinde veya kök etki alanları için şu anda desteklenmiyor. Ön kapıya yönelik bir tepesinde veya kök etki alanında HTTPS 'yi etkinleştirmek için kullanılabilen tek seçenek, Azure Key Vault barındırılan özel TLS/SSL sertifikanızı kullanmaktır.

1. Bir sonraki adıma geçmeden önce, ön kapıya Kullanıcı arabiriminde belirtildiği şekilde anahtar kasanıza erişmek için doğru izinleri oluşturduğunuzdan emin olun.

1. Geçerli aboneliğinizden bir **Key Vault hesabı** seçin ve ardından doğru sertifikayla eşlenecek uygun **gizli** ve **gizli sürümü** seçin.

1. Seçimi kaydetmek için **Güncelleştir** ' i seçin ve ardından **Kaydet**' i seçin.

1. Birkaç dakika sonra **Yenile** ' yi seçin ve ardından sertifika sağlama ilerleme durumunu görmek için özel etki alanını yeniden seçin. 

> [!WARNING]
> Tepesinde etki alanınız için uygun yönlendirme kuralları oluşturduğunuzdan veya etki alanını mevcut yönlendirme kurallarına eklemiş olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
