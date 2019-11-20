---
title: "Öğretici: Etki alanınızı ve alt etki alanınızı Azure DNS'de barındırma"
description: Bu öğreticide Azure DNS'yi DNS bölgelerinizi barındıracak şekilde yapılandırmayı öğreneceksiniz.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 99a3ca0115611f45ed080c39767d13e087b8efb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464228"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Öğretici: Azure DNS’te etki alanınızı barındırma

Azure DNS'yi DNS etki alanınızı barındırmak ve DNS kayıtlarınızı yönetmek için kullanabilirsiniz. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz.

Örneğin, contoso.net etki alanını bir etki alanı adı kayıt şirketinden satın aldığınızı ve Azure DNS'de contoso.net adlı bir bölge oluşturduğunuzu varsayalım. Etki alanının sahibi olduğunuzdan, kayıt şirketiniz size etki alanınız için ad sunucusu (NS) kayıtlarını yapılandırma seçeneğini sunar. Kayıt kuruluşu bu NS kayıtlarını .net üst alanında depolar. Dünyanın her yerindeki Internet kullanıcıları, contoso.net 'deki DNS kayıtlarını çözümlemeye çalıştıklarında Azure DNS bölgesindeki etki alanına yönlendirilir.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir DNS bölgesi oluşturun.
> * Ad sunucularının bir listesini alın.
> * Etki alanını devredebilirsiniz.
> * Temsilinin çalıştığını doğrulayın.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure DNS barındırabilmeniz için, test etmek için kullanılabilir bir etki alanı adına sahip olmanız gerekir. Bu etki alanı üzerinde tam denetime sahip olmanız gerekir. Tam denetim, etki alanı için ad sunucusu (NS) kayıtlarını ayarlama olanağını kapsar.

Bu öğretici için kullanılan örnek etki alanı contoso.net ' dir, ancak kendi etki alanı adınızı kullanır.

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

1. Bir DNS bölgesi oluşturmak için [Azure Portal](https://portal.azure.com/) gidin. **DNS bölgelerini**arayın ve seçin.

   ![DNS bölgesi](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. **DNS bölgesi oluştur**' u seçin.
1. **DNS bölgesi oluştur** sayfasında aşağıdaki değerleri girin ve **Oluştur**’u seçin:

   | **Ayar** | **Değer** | **Ayrıntılar** |
   |---|---|---|
   |**Ad**|[etki alanı adınız] |Satın aldığınız etki alanı adı. Bu öğreticide örnek olarak contoso.net kullanılmıştır.|
   |**Abonelik**|[Aboneliğiniz]|Bölgenin oluşturulacağı bir abonelik seçin.|
   |**Kaynak grubu**|**Yeni oluştur:** contosoRG|Bir kaynak grubu oluşturun. Kaynak grubu adı, seçtiğiniz abonelik içinde benzersiz olmalıdır.<br>Kaynak grubunun konumunu ifade eder ve DNS bölgesini etkilemez. DNS bölgesi konumu her zaman "geneldir" olur ve gösterilmez.|
   |**Konum**|Doğu ABD||

## <a name="retrieve-name-servers"></a>Ad sunucularını alma

DNS bölgenizi Azure DNS'ye devretmeden önce, bölgenizin ad sunucularını bilmeniz gerekir. Azure DNS, her bölge oluşturmada bir havuzdan ad sunucuları ayırır.

1. Oluşturulan DNS bölgesiyle, Azure Portal **Sık Kullanılanlar** bölmesinde, **Tüm kaynaklar**’ı seçin. **Tüm kaynaklar** sayfasında DNS bölgenizi seçin. Seçtiğiniz abonelikte zaten çeşitli kaynaklar varsa, uygulama ağ geçidine kolayca erişebilmek için etki alanı adınızı **ada göre filtrele** kutusuna girebilirsiniz. 

1. DNS bölgesi sayfasından ad sunucularını alın. Bu örnekte, contoso.net bölgesine *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* ve *ns4-01.azure-dns.info* ad sunucuları atanmıştır:

   ![Ad sunucularının listesi](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS, bölgenizdeki yetkili NS kayıtlarını atanan ad sunucularını içerecek şekilde otomatik olarak oluşturur.

## <a name="delegate-the-domain"></a>Etki alanını devretme

Artık DNS bölgesi oluşturulduğuna ve ad sunucularınız olduğuna göre, üst etki alanını Azure DNS ad sunucularıyla güncelleştirmeniz gerekir. Her kayıt şirketi, bir etki alanının ad sunucusu kayıtlarını değiştirmek için kendi DNS yönetim araçlarına sahiptir. 

1. Kayıt şirketinin DNS yönetim sayfasında NS kayıtlarını düzenleyin ve NS kayıtlarını Azure DNS ad sunucularıyla değiştirin.

1. Azure DNS için bir etki alanı temsilcisi seçtiğinizde, Azure DNS tarafından sağlanan ad sunucularını kullanmanız gerekir. Etki alanınız adının ne olursa olsun, tüm dört ad sunucularını kullanın. Etki alanı temsili, etki alanınız ile aynı üst düzey etki alanını kullanmak için bir ad sunucusu gerektirmez.

> [!NOTE]
> Ad sunucusu adreslerini kopyalarken adresinin sonundaki noktayı da kopyaladığınızdan emin olun. Sondaki nokta bir tam etki alanı adının sonuna gösterir. NS adı sonunda yoksa, bazı kayıt şirketlerinde dönemi ekler. DNS RFC ile uyumlu olmak için sondaki dönemi dahil edin.

Kendi bölgenizdeki ad sunucularını kullanan Temsilciler (bazen *Gösterim ad sunucuları*olarak adlandırılır) Azure DNS 'de Şu anda desteklenmemektedir.

## <a name="verify-the-delegation"></a>Temsilciyi doğrulama

Temsilciyi tamamladıktan sonra, bölgeniz için yetki başlangıcı (SOA) kaydını sorgulamak üzere *nslookup* gibi bir araç kullanarak çalıştığını doğrulayabilirsiniz. SOA kaydı, bölge oluşturulurken otomatik olarak oluşturulur. Çalışıp çalışmadığını başarıyla doğrulayabilmeniz için, temsilciyi tamamladıktan sonra 10 dakika veya daha fazla beklemeniz gerekebilir. Değişikliklerin DNS sisteminde yayılması daha uzun sürebilir.

Azure DNS ad sunucularını belirtmeniz gerekmez. Temsilci seçimi doğru ayarlanmışsa normal DNS çözümleme işlemi ad sunucularını otomatik olarak bulur.

1. Bir komut isteminden, aşağıdaki örneğe benzer bir Nslookup komutu girin:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Yanıtlarınızın aşağıdaki nslookup çıktısına benzer göründüğünü doğrulayın:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçmeyi düşünüyorsanız **contosoRG** kaynak grubunu tutabilirsiniz. Aksi halde **contosoRG** kaynak grubunu silerek bu öğreticide oluşturulan kaynakları silebilirsiniz.

- **ContosoRG** kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, etki alanınız için bir DNS bölgesi oluşturdunuz ve Azure DNS için temsilciniz. Azure DNS ve web uygulamaları hakkında daha fazla bilgi için web uygulaması öğreticileriyle devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Özel etki alanında bir web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)