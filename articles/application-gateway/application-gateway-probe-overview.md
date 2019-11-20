---
title: Azure Application Gateway için durum izlemeye genel bakış
description: Azure Application Gateway, arka uç havuzundaki tüm kaynakların sistem durumunu izler ve havuzdan sağlıksız olarak kabul edilen kaynakları otomatik olarak kaldırır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 2938665aa0c0a3df66b6ddcfd1c8c5fbc4598319
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130687"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway sistem durumu izlemeye genel bakış

Azure Application Gateway, arka uç havuzundaki tüm kaynakların sistem durumunu izler ve havuzdan sağlıksız olarak kabul edilen kaynakları otomatik olarak kaldırır. Application Gateway, sağlıksız örnekleri izlemeye devam eder ve mevcut olduktan sonra sistem durumu araştırmalarını yanıtladıktan sonra yeniden sağlıklı arka uç havuzuna ekler. Application Gateway, arka uç HTTP ayarlarında tanımlanan aynı bağlantı noktasıyla sistem durumu araştırmalarını gönderir. Bu yapılandırma, araştırmanın, müşterilerin arka uca bağlanmak için kullandığı bağlantı noktasını test edilmesini sağlar.

![Application Gateway araştırma örneği][1]

Varsayılan sistem durumu araştırması izlemeyi kullanmanın yanı sıra, sistem durumu araştırmasını uygulamanızın gereksinimlerine uyacak şekilde de özelleştirebilirsiniz. Bu makalede, hem varsayılan hem de özel sistem durumu araştırmaları ele alınmıştır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Varsayılan sistem durumu araştırması

Herhangi bir özel araştırma yapılandırması ayarlamazsanız, uygulama ağ geçidi varsayılan bir sistem durumu araştırmasını otomatik olarak yapılandırır. İzleme davranışı, arka uç havuzu için yapılandırılmış IP adreslerine bir HTTP isteği getirerek işe yarar. Varsayılan yoklamalar için arka uç http ayarları HTTPS için yapılandırılmışsa, araştırma arka uçların durumunu test etmek için HTTPS kullanır.

Örneğin: 80 numaralı bağlantı noktasında HTTP ağ trafiği almak için uygulama ağ geçidinizi A, B ve C arka uç sunucularını kullanacak şekilde yapılandırırsınız. Varsayılan sistem durumu izlemesi, sağlıklı bir HTTP yanıtı için her 30 saniyede bir üç sunucuyu sınar. Sağlıklı bir HTTP yanıtının 200 ile 399 arasında bir [durum kodu](https://msdn.microsoft.com/library/aa287675.aspx) vardır.

Sunucu A için varsayılan araştırma denetimi başarısız olursa, Application Gateway onu arka uç havuzundan kaldırır ve ağ trafiği bu sunucuya akar. Varsayılan araştırma, her 30 saniyede bir sunucu denetlemeye devam eder. Sunucu A, varsayılan bir sistem durumu araştırmasının bir isteğine başarıyla yanıt verdiğinde, arka uç havuzuna sağlıklı olarak geri eklenir ve trafik sunucuya yeniden akar.

### <a name="probe-matching"></a>Araştırma eşleştirme

Varsayılan olarak, 200 ve 399 arasındaki durum koduna sahip HTTP (S) yanıtı sağlıklı olarak değerlendirilir. Özel sistem durumu araştırmalarının yanı sıra iki eşleşen ölçüt de desteklenir. Uygun ölçütler, sağlıklı bir yanıtın ne olduğunu belirten varsayılan yorumu isteğe bağlı olarak değiştirmek için kullanılabilir.

Aşağıdakiler eşleşen ölçütlerdir: 

- **Http yanıt durum kodu eşleşmesi** -Kullanıcı tarafından belirtilen http yanıt kodunu veya yanıt kodu aralıklarını kabul etmek için araştırma eşleştirme ölçütü. Virgülle ayrılmış tek tek yanıt durum kodları veya bir durum kodu aralığı desteklenir.
- Http yanıt gövdesi **eşleştirmesi** -http yanıt gövdesi ' ne ve Kullanıcı tarafından belirtilen dizeyle eşleşiyor görünen araştırma eşleştirme ölçütü. Eşleşme yalnızca yanıt gövdesinde Kullanıcı tarafından belirtilen dize olup olmadığını arar ve tam bir normal ifade eşleşmesi değildir.

`New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet 'i kullanılarak eşleşme ölçütü belirtilebilir.

Örneğin:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Eşleşme ölçütü belirtildiğinde, PowerShell 'de bir `-Match` parametresi kullanılarak araştırma yapılandırmasına eklenebilir.

### <a name="default-health-probe-settings"></a>Varsayılan durum araştırma ayarları

| Araştırma özelliği | Değer | Açıklama |
| --- | --- | --- |
| Araştırma URL 'SI |http://127.0.0.1:\<port\>/ |URL yolu |
| Interval |30 |Bir sonraki sistem durumu araştırması gönderilmeden önce beklenecek saniye cinsinden süre.|
| Zaman aşımı |30 |Uygulama ağ geçidinin araştırmayı sağlıksız olarak işaretlemeden önce yoklama yanıtı için bekleyeceği saniye cinsinden süre. Bir araştırma sağlıklı olarak döndürülürse, ilgili arka uç hemen sağlıklı olarak işaretlenir.|
| Sağlıksız eşik |3 |Düzenli durum araştırmasının başarısız olması durumunda kaç yoklamanın gönderileceğini yönetir. Bu ek durum araştırmaları, arka ucun hızlı bir şekilde durumunun belirlenmesi için hızlı bir şekilde gönderilir ve araştırma aralığını beklemez. Arka uç sunucusu, ardışık araştırma hatası sayısı uygun olmayan eşiğe ulaştığında aşağı olarak işaretlenir. |

> [!NOTE]
> Bağlantı noktası, arka uç HTTP ayarlarıyla aynı bağlantı noktasıdır.

Varsayılan araştırma, sistem durumunu belirlemede yalnızca http:\//127.0.0.1:\<bağlantı noktası\> arar. Sistem durumu araştırmasını özel bir URL 'ye gidecek veya diğer ayarları değiştirecek şekilde yapılandırmanız gerekiyorsa, özel araştırmaları kullanmanız gerekir.

### <a name="probe-intervals"></a>Araştırma aralıkları

Tüm Application Gateway örnekleri arka ucunu birbirinden bağımsız olarak araştırın. Aynı araştırma yapılandırması her bir Application Gateway örneği için geçerlidir. Örneğin, araştırma yapılandırması, her 30 saniyede bir sistem durumu araştırmaları göndermektir ve uygulama ağ geçidinde iki örnek varsa, her iki örnek de her ikisi de her 30 saniyede bir durum araştırması gönderir.

Ayrıca, birden çok dinleyici varsa, her dinleyici arka ucunu birbirinden bağımsız olarak yoklamalar. Örneğin, iki farklı bağlantı noktasında aynı arka uç havuzunu işaret eden iki dinleyici varsa (iki arka uç http ayarı ile yapılandırılır), her dinleyici aynı arka ucu bağımsız olarak yoklamakta. Bu durumda, iki dinleyici için her bir uygulama ağ geçidi örneğinden iki araştırma vardır. Bu senaryoda uygulama ağ geçidinin iki örneği varsa, arka uç sanal makinesi yapılandırılan yoklama aralığı başına dört araştırma görür.

## <a name="custom-health-probe"></a>Özel durum araştırması

Özel yoklamalar, sistem durumu izleme üzerinde daha ayrıntılı bir denetime sahip olmasını sağlar. Özel yoklamalar kullanırken, araştırma aralığını, test edilecek URL 'yi ve yolu ve arka uç havuzu örneğini sağlıksız olarak işaretlemeden önce kaç tane başarısız yanıt kabul edeceğini yapılandırabilirsiniz.

### <a name="custom-health-probe-settings"></a>Özel durum araştırma ayarları

Aşağıdaki tabloda özel bir sistem durumu araştırmasının özelliklerine ilişkin tanımlar verilmiştir.

| Araştırma özelliği | Açıklama |
| --- | --- |
| Ad |Araştırmanın adı. Bu ad, arka uç HTTP ayarlarındaki araştırmayı ifade etmek için kullanılır. |
| Protokol |Araştırmayı göndermek için kullanılan protokol. Araştırma, arka uç HTTP ayarlarında tanımlanan protokolü kullanır |
| Host |Araştırmanın gönderileceği ana bilgisayar adı. Yalnızca Application Gateway 'de birden çok site yapılandırıldığında uygulanabilir, aksi takdirde ' 127.0.0.1 ' kullanın. Bu değer, VM ana bilgisayar adından farklıdır. |
| Yol |Araştırmanın göreli yolu. Geçerli yol '/' öğesinden başlar. |
| Interval |Saniye cinsinden yoklama aralığı. Bu değer, iki ardışık yoklamalar arasındaki zaman aralığıdır. |
| Zaman aşımı |Saniye cinsinden araştırma zaman aşımı. Bu zaman aşımı süresi içinde geçerli bir yanıt alınmıyorsa, araştırma başarısız olarak işaretlenir.  |
| Sağlıksız eşik |Araştırma yeniden deneme sayısı. Arka uç sunucusu, ardışık araştırma hatası sayısı uygun olmayan eşiğe ulaştığında aşağı olarak işaretlenir. |

> [!IMPORTANT]
> Tek bir site için Application Gateway yapılandırıldıysa, varsayılan olarak, özel araştırmata yapılandırılmadığı sürece konak adı ' 127.0.0.1 ' olarak belirtilmelidir.
> Başvuru için bir özel araştırma \<protokol\>://\<ana bilgisayar\>:\<bağlantı noktası\>\<yol\>. Kullanılan bağlantı noktası, arka uç HTTP ayarlarında tanımlananla aynı bağlantı noktası olacaktır.

## <a name="nsg-considerations"></a>NSG konuları

Bir uygulama ağ geçidi alt ağında ağ güvenlik grubu (NSG) varsa, gelen trafik için uygulama ağ geçidi alt ağında 65503-65534 numaralı bağlantı noktası aralığı açılmalıdır. Bu bağlantı noktaları, arka uç sistem durumu API 'sinin çalışması için gereklidir.

Ayrıca, giden Internet bağlantısı engellenmeyebilir ve AzureLoadBalancer etiketinden gelen trafiğe izin verilmelidir.

## <a name="next-steps"></a>Sonraki adımlar
Application Gateway sistem durumu izleme hakkında bilgi aldıktan sonra, PowerShell ve Azure Resource Manager dağıtım modelini kullanarak Azure portal veya özel bir [sistem durumu araştırmasına](application-gateway-create-probe-ps.md) yönelik [özel bir sistem durumu araştırması](application-gateway-create-probe-portal.md) yapılandırabilirsiniz.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
