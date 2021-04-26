---
title: 'Hızlı başlangıç: DNS bölgesi ve kaydı oluşturma-Azure portal'
titleSuffix: Azure DNS
description: Azure portal kullanarak Azure DNS bölgesi ve kaydı oluşturma hakkında bilgi edinmek için bu adım adım hızlı başlangıç kılavuzunu kullanın.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: d08c5768c2c400ff3d842f58081a58708bfc28db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537780"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure DNS bir bölge ve kayıt oluşturma

Azure DNS'yi, genel etki alanınızdaki ana bilgisayar adlarını çözümleyecek şekilde yapılandırabilirsiniz. Örneğin, *contoso.xyz* etki alanı adını bir etki alanı adı kaydedicisinde satın aldıysanız, *contoso.xyz* etki alanını barındıracak ve *`www.contoso.xyz`* Web sunucunuzun veya web uygulamanızın IP adresine çözülecek Azure DNS yapılandırabilirsiniz.

Bu hızlı başlangıçta, bir sınama etki alanı oluşturacak ve ardından *www* 'ı *10.10.10.10* IP adresine çözümlemek için bir adres kaydı oluşturacaksınız.

>[!IMPORTANT]
>Bu hızlı başlangıçtaki tüm adlar ve IP adresleri, gerçek dünyada senaryoları temsil etmediği örneklerdir.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Tüm Portal adımları için [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

Bir DNS bölgesi, bir etki alanı için DNS girdilerini içerir. Etki alanınızı Azure DNS barındırılmaya başlamak için, bu etki alanı adı için bir DNS bölgesi oluşturun. 

**DNS bölgesi oluşturmak için:**

1. Sol üst köşedeki **kaynak oluştur**' u ve ardından **ağ**' ı ve ardından **DNS bölgesi**' ni seçin.

1. **DNS bölgesi oluştur** sayfasında, aşağıdaki değerleri yazın veya seçin:

   - **Ad**: Bu hızlı başlangıç örneği için *contoso.xyz* yazın. DNS bölgesi adı, Azure DNS sunucularında önceden yapılandırılmamış herhangi bir değer olabilir. Gerçek dünyada etki alanı adı kayıt kuruluşunuzdan satın aldığınız etki alanını kullanmanız gerekir.
   - **Kaynak grubu**: **Yeni oluştur**' u seçin, *myresourcegroup* girin ve **Tamam**' ı seçin. Kaynak grubu adı, Azure aboneliği dahilinde benzersiz olmalıdır. 

1. **Oluştur**’u seçin.

   ![DNS bölgesi](./media/dns-getstarted-portal/openzone650.png)

Bölgenin oluşturulması birkaç dakika sürebilir.

## <a name="create-a-dns-record"></a>DNS kaydı oluşturma

DNS girişleri veya DNS bölgesi içinde etki alanınız için kayıtlar oluşturun. Bir ana bilgisayar adını bir IPv4 adresine çözümlemek için yeni bir adres kaydı veya ' A ' kaydı oluşturun.

**' A ' kaydı oluşturmak için:**

1. Azure portal, **tüm kaynaklar** altında, **myresourcegroup** kaynak grubundaki **contoso.xyz** DNS bölgesini açın. Daha kolay bulmak için **ada göre filtrele** kutusuna *contoso.xyz* yazabilirsiniz.

1. **DNS bölgesi** sayfasının en üstünde **+ kayıt kümesi**' ni seçin.

1. **Kayıt kümesi Ekle** sayfasında aşağıdaki değerleri yazın veya seçin:

   - **Ad**: *www* yazın. Kayıt adı, belirtilen IP adresine çözümlemek istediğiniz ana bilgisayar adıdır.
   - **Yazın**: **bir** seçin. ' A ' kayıtları en sık kullanılan, ancak posta sunucuları (' MX '), IP V6 adresleri (' AAAA ') ve benzeri diğer kayıt türleri vardır. 
   - **TTL**: tür *1*. DNS isteğinin *yaşam süresi* , DNS sunucularının ve istemcilerinin bir yanıtı ne kadar süreyle önbellekte isteyekullanabileceğinizi belirtir.
   - **TTL birimi**: **saatleri** seçin. Bu, **TTL** değeri için zaman birimidir. 
   - **IP adresi**: Bu hızlı başlangıç örneği için *10.10.10.10* yazın. Bu değer, kayıt adının olarak çözümlediği IP adresidir. Gerçek dünya senaryosunda, web sunucunuz için genel IP adresini girersiniz.

Bu hızlı başlangıç yalnızca hızlı test amaçlı olduğundan, bir etki alanı ad kaydedicisinde Azure DNS ad sunucularını yapılandırmaya gerek yoktur. Gerçek bir üretim etki alanı ile Internet üzerindeki herkesin, Web sunucunuza veya uygulamanıza bağlanmak için ana bilgisayar adını çözümlemesine isteyeceksiniz. Ad sunucusu kayıtlarını Azure DNS ad sunucularıyla değiştirmek için etki alanı adı kaydedicinizi ziyaret edeceksiniz. Daha fazla bilgi için bkz. [öğretici: etki alanınızı Azure DNS barındırın](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Ad çözümlemesini test etme

Test ' A ' kaydıyla bir test DNS bölgesine sahip olduğunuza göre, ad çözümlemesini *nslookup* adlı bir araçla test edebilirsiniz. 

**DNS ad çözümlemesini test etmek için:**

1. Azure portal, **tüm kaynaklar** altında, **myresourcegroup** kaynak grubundaki **contoso.xyz** DNS bölgesini açın. Daha kolay bulmak için **ada göre filtrele** kutusuna *contoso.xyz* yazabilirsiniz.

1. Ad sunucusu adlarından birini **genel bakış** sayfasındaki ad sunucusu listesinden kopyalayın. 

   ![bölge](./media/dns-getstarted-portal/viewzonens500.png)

1. Bir komut istemi açın ve aşağıdaki komutu çalıştırın:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Örnek:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Aşağıdaki ekrana benzer bir şey görmeniz gerekir:

   ![Ekran görüntüsü, bir n s arama komutu ve sunucu, adres, ad ve adres değerlerini içeren bir komut istemi penceresi gösterir.](media/dns-getstarted-portal/nslookup.PNG)

**Www \. contoso.xyz** ana bilgisayar adı, yalnızca yapılandırdığınız gibi **10.10.10.10** olarak çözümlenir. Bu sonuç, ad çözümlemenin doğru çalıştığını doğrular. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, **Myresourcegroup** kaynak grubunu silerek bunları kaldırın. **Myresourcegroup** kaynak grubunu açın ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanında web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)
