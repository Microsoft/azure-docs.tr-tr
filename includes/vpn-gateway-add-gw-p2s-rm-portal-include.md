---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268887"
---
1. Portalda, sol tarafta **+ Kaynak oluştur**’a tıklayın ve arama alanına ‘Sanal Ağ Geçidi’ yazın. Arama sonuçlarında **Sanal ağ geçidi** seçeneğini bulun ve girişe tıklayın. **Sanal ağ geçidi** sayfasında **Oluştur**' a tıklayın. Bu işlem **Sanal ağ geçidi oluşturma** sayfasını açar.

   ![Sanal ağ geçidi sayfası alanı oluştur](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Sanal ağ geçidi sayfası alanı oluştur")

   ![Sanal ağ geçidi sayfası alanı oluştur](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Sanal ağ geçidi sayfası alanı oluştur")
2. **Sanal ağ geçidi oluştur** sayfasında, sanal ağ geçidinize ait değerleri girin.

   **Proje ayrıntıları**

   - **Abonelik**: açılan listeden kullanmak istediğiniz aboneliği seçin.
   - **Kaynak grubu**: Bu sayfada Sanal ağınızı seçtiğinizde bu ayar tekrar doldurulur.

   **Örnek ayrıntıları**

   - **Ad**: Ağ geçidinizi adlandırın. Ağ geçidinizi adlandırma ağ geçidi alt ağını adlandırmayla aynı değildir. Bu ad, oluşturduğunuz ağ geçidi nesnesinin adıdır.
   - **Bölge**: Bu kaynağı oluşturmak istediğiniz bölgeyi seçin. Ağ geçidinin bölgesi, sanal ağ ile aynı olmalıdır.
   - **Ağ geçidi türü**: **VPN**’i seçin. VPN ağ geçitleri, **VPN** sanal ağ geçidi türünü kullanır. 
   - **VPN türü**: Yapılandırmanızla ilgili belirtilen VPN türünü seçin. Çoğu yapılandırma, Yol Tabanlı bir VPN türü gerektirir.
   - **SKU**: Açılır listeden ağ geçidi SKU’sunu seçin. Açılır listede sıralanan SKU’lar seçtiğiniz VPN türüne bağlıdır. Ağ geçidi SKU’ları hakkında bilgi için bkz. [Ağ geçidi SKU’ları](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Sanal ağ**: Bu ağ geçidini eklemek istediğiniz sanal ağı seçin.

      **Ağ geçidi alt ağ adres aralığı**: Bu alan yalnızca, seçtiğiniz sanal ağ bir ağ geçidi alt ağına sahip değilse görüntülenir. Zaten bir ağ geçidi alt ağınız yoksa, aralığı girin. Mümkünse, Aralık/27 veya daha büyük (/26,/25 vb.) aralığı yapın

   **Genel IP adresi**: Bu ayar, VPN ağ geçidiyle ilişkilendirilen genel IP adresi nesnesini belirtir. VPN ağ geçidi oluşturulduğunda genel IP adresi bu nesneye dinamik olarak atanır. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

     - **Genel IP adresi**: **Yeni oluştur** seçili kalsın.
     - **Genel IP adresi adı**: metin kutusuna, genel IP adresi örneğiniz için bir ad yazın.
     - **Atama**: VPN Gateway yalnızca dinamik destekler.

   **Etkin-etkin mod**: yalnızca etkin-etkin ağ geçidi yapılandırması oluşturuyorsanız **etkin-etkin modunu etkinleştir** ' i seçin. Aksi takdirde bu ayarı seçmeyin.

   Yapılandırmanız için **BGP ASN yapılandır** ayarı özellikle gerekli değilse bu ayarı işaretlemeyin. Bu ayar gerekliyse varsayılan ASN değeri 65515’tir ancak bu değiştirilebilir.

3. Doğrulamayı çalıştırmak için **gözden geçir + oluştur** ' a tıklayın. Doğrulama başarılı olduktan sonra, VPN ağ geçidini dağıtmak için **Oluştur** ' a tıklayın. Bir ağ geçidinin, tam olarak oluşturulması ve dağıtılması 45 dakika sürebilir. Dağıtım durumunu ağ geçidinizin Genel Bakış sayfasında görebilirsiniz.

Ağ geçidi oluşturulduktan sonra, portalda sanal ağa bakarak bu ağ geçidine atanmış IP adresini görüntüleyebilirsiniz. Ağ geçidi bağlı bir cihaz gibi görüntülenir.