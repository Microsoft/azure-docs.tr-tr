---
title: FHıR için Azure API 'sindeki $export komutunu çağırarak dışarı aktarmayı yürütme
description: Bu makalede $export kullanarak FHıR verilerinin nasıl dışarı aktarılacağı açıklanır
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559205"
---
# <a name="how-to-export-fhir-data"></a>FHıR verilerini dışa aktarma


Toplu dışa aktarma özelliği, verilerin fhır [belirtimine](https://hl7.org/fhir/uv/bulkdata/export/index.html)göre fhır sunucusundan dışarı aktarılmasını sağlar. 

$Export kullanmadan önce, FHıR için Azure API 'sinin onu kullanmak üzere yapılandırıldığından emin olmak isteyeceksiniz. Dışarı aktarma ayarlarını yapılandırmak ve Azure depolama hesabı oluşturmak için [verileri dışarı aktarma sayfasını](configure-export-data.md)inceleyin.

## <a name="using-export-command"></a>$export komutu kullanma

Dışarı aktarma işlemi için Azure API 'sını yapılandırdıktan sonra, verileri hizmetten dışarı aktarmak için $export komutunu kullanabilirsiniz. Veriler, dışarı aktarmayı yapılandırırken belirttiğiniz depolama hesabında depolanır. FHıR sunucusunda $export komutu çağırmayı öğrenmek için [HL7 fhır $Export belirtiminde](https://hl7.org/Fhir/uv/bulkdata/export/index.html)belgeleri okuyun.


**İşlemler hatalı durumda takıldı**

Bazı durumlarda, bir işin kötü bir durumda takılmasına yönelik bir potansiyel olur. Bu durum özellikle depolama hesabı izinleri düzgün kurulmamış ise oluşabilir. Dışa aktarma işleminin başarılı olup olmadığını doğrulamak için bir yol, karşılık gelen kapsayıcı (yani ndjson) dosyalarının var olup olmadığını görmek için depolama hesabınızı denetlemektedir. Bunlar yoksa ve çalışan başka bir dışarı aktarma işi yoksa, geçerli işin hatalı durumda takılmasına bir olasılık vardır. İptal isteği göndererek dışarı aktarma işini iptal etmeli ve işi yeniden kuyruğa almaya çalışın. Hatalı bir durumda dışarı aktarma için varsayılan çalışma süresi 10 dakikadır ve yeni bir işe geçiş yapmadan veya dışarı aktarmayı yeniden denemeyecektir. 

FHıR Için Azure API aşağıdaki düzeylerde $export destekler:
* [Sistem](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Hasta](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Hastalar grubu *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -fhır için Azure API 'si tüm ilgili kaynakları dışa aktarır ancak grubun özelliklerini dışarı aktarmaz: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Veriler verildiğinde, her kaynak türü için ayrı bir dosya oluşturulur. İçe aktarılmış dosyaların çok büyük olmamasını sağlamak için. Tek bir içe aktarılmış dosyanın boyutu 64 MB 'tan büyük olduktan sonra yeni bir dosya oluşturacağız. Sonuç olarak, her bir kaynak türü için birden çok dosya elde edebilirsiniz (yani, hasta-1. ndjson, hasta-2. ndjson). 


> [!Note] 
> `Patient/$export` ve `Group/[ID]/$export` kaynak birden fazla kaynağın bir bölmesinde ise veya birden çok grupta yer alıyorsa yinelenen kaynakları dışarı aktarabilirsiniz.

Ayrıca, aktarım sırasında konum üst bilgisi tarafından döndürülen URL aracılığıyla dışa aktarma durumunun denetlenmesi, gerçek dışa aktarma işinin iptal edilmesi ile birlikte desteklenir.

### <a name="exporting-fhir-data-to-adls-gen2"></a>FHıR verilerini ADLS 2. dışa aktarma

Şu anda ADLS 2. etkin depolama hesapları için aşağıdaki sınırlamalara sahip $export destekliyoruz:

- Kullanıcı [hiyerarşik ad alanlarından](../../storage/blobs/data-lake-storage-namespace.md)yararlanamaz, ancak kapsayıcı içindeki belirli bir alt dizine dışarı aktarmayı hedeflemek için bir yol yoktur. Yalnızca belirli bir kapsayıcıyı hedefleyebilme olanağı sunuyoruz (her dışarı aktarma için yeni bir klasör oluşturacağız).
- Dışarı aktarma işlemi tamamlandıktan sonra, aynı kapsayıcıya yönelik sonraki dışarı aktarmalar yeni oluşturulan bir klasörde yer aldığı için hiçbir şeyi bu klasöre yeniden aktardık.


## <a name="settings-and-parameters"></a>Ayarlar ve parametreler

### <a name="headers"></a>Üst Bilgiler
$Export işleri için ayarlanması gereken iki gerekli üst bilgi parametresi vardır. Değerler geçerli [$Export belirtimi](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)tarafından tanımlanır.
* **Accept** -Application/fhir + JSON
* **Tercih** -Yanıtla-zaman uyumsuz

### <a name="query-parameters"></a>Sorgu parametreleri
FHıR için Azure API 'SI aşağıdaki sorgu parametrelerini destekler. Bu parametrelerin tümü isteğe bağlıdır:

|Sorgu parametresi        | FHIR belirtimi tarafından tanımlandı mı?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Yes | Şu anda, FHıR belirtimine uyum sağlamak için üç değeri desteklemektedir: Application/fhır + ndjson, Application/ndjson veya yalnızca ndjson. Tüm dışarı aktarma işleri döndürülecek `ndjson` ve geçirilen değerin kod davranışı üzerinde hiçbir etkisi olmayacaktır. |
| \_getirildikten | Yes | Yalnızca belirtilen süreden bu yana değiştirilen kaynakları dışarı aktarmanız sağlar |
| \_türüyle | Yes | Hangi kaynak türlerinin ekleneceğini belirtmenize izin verir. Örneğin, \_ tür = hasta yalnızca hasta kaynaklarını döndürür|
| \_tür filtresi | Yes | Daha ayrıntılı filtreleme istemek için, \_ tür parametresiyle birlikte TypeFilter kullanabilirsiniz \_ . _TypeFilter parametresinin değeri, sonuçları daha fazla kısıtlayan FHıR sorgularının virgülle ayrılmış listesidir |
| \_kapsayıcı | No |  Verilerin verilmesi gereken yapılandırılmış depolama hesabı içindeki kapsayıcıyı belirtir. Bir kapsayıcı belirtilmişse, veriler bu kapsayıcıya ada sahip yeni bir klasörde verilir. Kapsayıcı belirtilmemişse, zaman damgası ve iş KIMLIĞI kullanılarak yeni bir kapsayıcıya aktaralınacaktır. |

> [!Note]
> Yalnızca, FHıR için Azure API 'SI ile aynı abonelikte bulunan depolama hesaplarının, $export işlemler için hedef olarak kayıtlı olmasına izin verilir.

## <a name="secure-export-to-azure-storage"></a>Azure depolama 'ya güvenli dışarı aktarma

FHıR için Azure API, güvenli bir dışarı aktarma işlemini destekler. Güvenli bir dışarı aktarma çalıştırmak için bir seçenek, Azure API 'si için Azure API ile ilişkili belirli IP adreslerinin Azure depolama hesabına erişmesine izin verişdedir. Depolama hesabının, FHıR için Azure API 'sinden aynı veya farklı bir konumda olmasına bağlı olarak, konfigürasyonlar farklı olur.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Azure depolama hesabı farklı bir bölgedeyse

Portaldan Azure Storage hesabının **ağını** seçin. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure depolama ağ ayarları." lightbox="media/export-data/storage-networking.png":::
   
**Seçili ağlar**'ı seçin. Güvenlik Duvarı bölümünde, **adres aralığı** kutusunda IP adresini belirtin. İnternet 'ten veya şirket içi ağlarınızdan erişime izin vermek için IP aralıkları ekleyin. IP adresini, FHıR hizmeti için Azure API 'sinin sağlandığı Azure bölgesi için aşağıdaki tabloda bulabilirsiniz.

|**Azure Bölgesi**         |**Genel IP Adresi** |
|:----------------------|:-------------------|
| Doğu Avustralya       | 20.53.44.80       |
| Orta Kanada       | 20.48.192.84      |
| Central US           | 52.182.208.31     |
| Doğu ABD              | 20.62.128.148     |
| Doğu ABD 2            | 20.49.102.228     |
| EUAP Doğu ABD 2       | 20.39.26.254      |
| Almanya Kuzey        | 51.116.51.33      |
| Almanya Orta Batı | 51.116.146.216    |
| Doğu Japonya           | 20.191.160.26     |
| Güney Kore - Orta        | 20.41.69.51       |
| Orta Kuzey ABD     | 20.49.114.188     |
| Kuzey Avrupa         | 52.146.131.52     |
| Güney Afrika - Kuzey   | 102.133.220.197   |
| Orta Güney ABD     | 13.73.254.220     |
| Güneydoğu Asya       | 23.98.108.42      |
| İsviçre Kuzey    | 51.107.60.95      |
| Güney Birleşik Krallık             | 51.104.30.170     |
| Batı Birleşik Krallık              | 51.137.164.94     |
| Orta Batı ABD      | 52.150.156.44     |
| West Europe          | 20.61.98.66       |
| Batı ABD 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Azure depolama hesabı aynı bölgedeyse

Yapılandırma işlemi, 100.64.0.0/10 yerine CıDR biçimindeki belirli bir IP adresi aralığı kullanıldığı sürece yukarıdaki ile aynıdır. 100.64.0.0 – 100.127.255.255 içeren IP adresi aralığının belirtilmesi nedeni, hizmet tarafından kullanılan gerçek IP adresinin değiştiğinden, ancak her bir $export isteği için Aralık dahilinde olacaktır.

> [!Note] 
> Bunun yerine 10.0.2.0/24 aralığı içindeki özel bir IP adresi kullanılabilir olabilir. Bu durumda $export işlemi başarılı olmaz. $Export isteği yeniden deneyebilir, ancak bir dahaki sefer 100.64.0.0/10 aralığı içinde bir IP adresinin kullanılacağının garantisi yoktur. Bu, tasarıma göre bilinen ağ davranışıdır. Diğer bir seçenek de depolama hesabını farklı bir bölgede yapılandırmaktır.
    
## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, $export komutu kullanarak FHıR kaynaklarını dışarı aktarmayı öğrendiniz. Daha sonra, önceden tanımlanmış verileri dışarı aktarma hakkında bilgi edinmek için bkz.:
 
>[!div class="nextstepaction"]
>[Belirtilen verileri dışarı aktarma](de-identified-export.md)
