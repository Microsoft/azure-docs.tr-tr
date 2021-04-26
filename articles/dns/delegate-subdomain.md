---
title: Bir alt etki alanı verme-Azure DNS
description: Bu öğrenme yoluyla bir Azure DNS alt etki alanı için temsilci seçmeye başlayın.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011549"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS bir alt etki alanı verme

DNS alt etki alanı atamak için Azure portal kullanabilirsiniz. Örneğin, contoso.com etki alanına sahipseniz, *mühendislik* adlı bir alt etki alanını contoso.com bölgesinden ayrı olarak yönetebilmeniz için farklı bir bölgeye atayabilirsiniz.

İsterseniz, [Azure PowerShell](delegate-subdomain-ps.md)kullanarak bir alt etki alanı için temsilci seçebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure DNS bir alt etki alanı atamak için, önce genel etki alanınızı Azure DNS olarak vermelisiniz. Ad sunucularınızı temsilci olarak yapılandırma hakkında yönergeler için bkz. [Azure DNS etki alanı verme](./dns-delegate-domain-azure-dns.md) . Etki alanınız Azure DNS bölgenize atandıktan sonra, alt etki alanınızı temsil edebilirsiniz.

> [!NOTE]
> Bu makalenin tamamında bir örnek olarak Contoso.com kullanılır. contoso.com yerine kendi etki alanı adınızı yazın.

## <a name="create-a-zone-for-your-subdomain"></a>Alt etki alanı için bir bölge oluşturun

İlk olarak, **mühendislik** alt etki alanı için bölgeyi oluşturun.

1. Azure portal **kaynak oluştur**' u seçin.
2. Arama kutusuna **DNS** yazın ve **DNS bölgesi**' ni seçin.
3. **Oluştur**’u seçin.
4. **DNS bölgesi oluştur** bölmesinde **ad** metin kutusuna **Engineering.contoso.com** yazın.
5. Bölgeniz için kaynak grubunu seçin. Benzer kaynakları birlikte tutmak için üst bölgeyle aynı kaynak grubunu kullanmak isteyebilirsiniz.
6. **Oluştur**’a tıklayın.
7. Dağıtım başarılı olduktan sonra yeni bölgeye gidin.

## <a name="note-the-name-servers"></a>Ad sunucularına göz önünde

Ardından, mühendislik alt etki alanı için dört ad sunucusu ' nu aklınızda yapın.

**Mühendislik** bölgesi bölmesinde, bölgenin dört ad sunucusuna göz önünde. Bu ad sunucularını daha sonra kullanacaksınız.

## <a name="create-a-test-record"></a>Test kaydı oluşturma

Test için kullanılacak **bir** kayıt oluşturun. Örneğin, bir **www** a kaydı oluşturun ve bunu bir **10.10.10.10** IP adresiyle yapılandırın.

## <a name="create-an-ns-record"></a>NS kaydı oluşturma

Ardından, **mühendislik** bölgesi için bir ad sunucusu (NS) kaydı oluşturun.

1. Üst etki alanı için bölgeye gidin.
2. **+ Kayıt kümesi** seçeneğini belirleyin.
3. **Kayıt kümesi Ekle** bölmesinde, **ad** metin kutusuna **mühendislik** yazın.
4. **Tür** için **NS**' yi seçin.
5. **Ad sunucusu**' nun altında, daha önce **mühendislik** bölgesinden kaydettiğiniz dört ad sunucusunu girin.
6. **Tamam**'a tıklayın.

## <a name="test-the-delegation"></a>Temsilciyi test etme

Temsilciyi test etmek için nslookup 'ı kullanın.

1. PowerShell penceresini açın.
2. Komut isteminde şunu yazın: `nslookup www.engineering.contoso.com.`
3. **10.10.10.10** adresini gösteren yetkili olmayan bir yanıt almanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure 'da barındırılan hizmetler için ters DNS yapılandırmayı](dns-reverse-dns-for-azure-services.md)öğrenin.