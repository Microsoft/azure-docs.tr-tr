---
title: Mevcut bir Azure CDN uç noktasına bir kök veya tepesinde etki alanı ekleme-Azure portal
description: Azure portal kullanarak mevcut bir Azure CDN uç noktasına bir kök veya tepesinde etki alanı eklemeyi öğrenin.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370512"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Mevcut bir Azure CDN uç noktasına kök veya tepesinde etki alanı ekleme

Azure CDN, özel etki alanlarını eklemek için etki alanı sahipliğini doğrulamak üzere CNAME kayıtlarını kullanır. CDN, CDN profiliniz ile ilişkili ön uç IP adresini kullanıma sunmaz. Amacınızı Azure CDN eklemek istiyorsanız tepesinde etki alanınızı bir IP adresiyle eşleyemezsiniz.

DNS protokolü, CNAME kayıtlarının tepesinde bölgesinde atanmasını engeller. Örneğin, etki alanınız ise `contoso.com` ; IÇIN CNAME kayıtları oluşturabilirsiniz, `somelabel.contoso.com` ancak kendısı için CNAME oluşturamazsınız `contoso.com` . Bu kısıtlama, Azure CDN arkasındaki yük dengeli uygulamalara sahip olan uygulama sahipleri için bir sorun gösterir. CDN profilinin kullanılması için CNAME kaydı oluşturulması gerektiğinden, tepesinde bölgesinden CDN profilini işaret etmek mümkün değildir.

Bu sorun, Azure DNS diğer ad kayıtları kullanılarak çözümlenir. CNAME kayıtlarının aksine, diğer ad kayıtları tepesinde bölgesinde oluşturulur. Uygulama sahipleri, bölge tepesinde kayıtlarını ortak uç noktalara sahip bir CDN profiline işaret etmek için bunu kullanabilir. Uygulama sahipleri, DNS bölgesi içindeki diğer etki alanı için kullanılan aynı CDN profilini işaret. Örneğin, `contoso.com` ve `www.contoso.com` aynı CDN profilini işaret edebilir. 

Tepesinde veya kök etki alanınızı CDN profiliniz ile eşlemek için CNAME düzleştirme veya DNS işlemi gerçekleştiriliyor gerekir. DNS sağlayıcının bir IP adresine çıkana kadar CNAME girişini yinelemeli olarak çözümleyen bir mekanizma. Bu işlevsellik, CDN uç noktaları için Azure DNS desteklenir. 

> [!NOTE]
> Ayrıca, CNAME düzleştirme veya DNS ile izleme desteği sunan başka DNS sağlayıcıları da vardır Azure CDN, kendi etki alanlarını barındırmak için müşterilerinin Azure DNS kullanılmasını önerir.

Azure portal kullanarak CDN 'nize bir tepesinde etki alanı ekleyebilir ve bunu TLS sonlandırmasına yönelik sertifikayla ilişkilendirerek HTTPS 'yi etkinleştirebilirsiniz. Tepesinde etki alanları, kök veya Naked etki alanları olarak da adlandırılır.

## <a name="create-an-alias-record-for-zone-apex"></a>Bölge tepesinde için diğer ad kaydı oluşturma

1. Etki alanının eklendi olması için **Azure DNS** yapılandırma açın.

2. **+ Kayıt kümesi** seçeneğini belirleyin.

3. **Kayıt kümesi Ekle**' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ------|
    | Ad | Girin **@** . |
    | Tür | **Bir** seçin. |
    | Diğer ad kayıt kümesi | **Evet**’i seçin. |
    | Diğer ad türü | **Azure kaynağı**' nı seçin. |
    | Abonelik seçin | Aboneliğinizi seçin. |
    | Azure kaynağı | CDN uç noktanızı seçin. |

4. **TTL** için değer girin.

5. Değişikliklerinizi göndermek için **Tamam ' ı** seçin.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Bölge tepesinde için diğer ad kaydı":::

6. Yukarıdaki adım, CDN kaynağınız üzerine işaret eden bir Zone tepesinde kaydı oluşturacaktır. Bir CNAME kaydı eşleme **cdnverify** , CDN profilinizde etki alanını ekleme için kullanılır.
    1. Örnek, **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Özel etki alanını CDN 'nize ekleme

Özel etki alanınızı kaydettikten sonra CDN uç noktanıza ekleyebilirsiniz. 

1. [Azure portalında](https://portal.azure.com/) oturum açın ve bir özel etki alanına eşlemek istediğiniz uç noktayı içeren CDN profiline göz atın.
    
2. **CDN profili** sayfasında, özel etki alanı ile ilişkilendirilecek CDN uç noktasını seçin.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN uç noktası seçimi" border="true":::
    
3. **+ Özel etki alanı**' nı seçin. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Özel etki alanı Ekle düğmesi" border="true":::

4. **Özel etki alanı Ekle**' de, **uç nokta ana bilgisayar adı** önceden doldurulmuştur ve CDN uç nokta URL 'nizden türetilir: **\<endpoint-hostname>** . azureedge.net. Bu değer değiştirilemez.

5. **Özel ana bilgisayar adı** IÇIN, CNAME kaydlarınızın kaynak etki alanı olarak kullanılacak özel kök veya tepesinde etki alanınızı girin. 
    1. Örneğin, **contoso.com**. **Cdnverify alt etki alanı adını kullanmayın**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Özel etki alanı Ekle" border="true":::

6. **Add (Ekle)** seçeneğini belirleyin.

   Azure, girdiğiniz özel etki alanı adı için CNAME kaydının bulunduğunu doğrular. CNAME doğruysa, özel etki alanınız doğrulanır. 

   Yeni özel etki alanı ayarlarının tüm CDN kenar düğümlerine yayılması biraz sürebilir: 
    - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
    - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
    - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır.   

## <a name="enable-https-on-your-custom-domain"></a>Özel etki alanınız üzerinde HTTPS 'yi etkinleştirme

Azure CDN için özel etki alanında HTTPS 'yi etkinleştirme hakkında daha fazla bilgi için bkz [. Öğretici: Azure CDN özel etki alanında https yapılandırma](cdn-custom-ssl.md)

## <a name="next-steps"></a>Sonraki adımlar

- [CDN uç noktası oluşturmayı](cdn-create-new-endpoint.md)öğrenin.
