---
title: Azure Service Fabric CLı-sfctl olayları | Microsoft Docs
description: Service Fabric CLı sfctl olayları komutlarını açıklar.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 858fd1971a22b1db2d243838558c3792d3a60cc9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901982"
---
# <a name="sfctl-events"></a>sfctl etkinlikleri
Olayları olay deposundan al (EventStore hizmeti zaten yüklüyse).

EventStore sistem hizmeti, > = 6.4 çalıştıran herhangi bir SFRP kümesine bir yapılandırma yükseltmesi aracılığıyla eklenebilir. Lütfen https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore\: Şu URL 'yi kontrol edin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Tüm uygulamalar-listeler | Uygulamalarla ilgili tüm olayları alır. |
| All-Nodes-List | Tüm düğümlerle ilgili olayları alır. |
| Tüm bölümler-listeler | Tüm bölümlerle ilgili olayları alır. |
| All-Services-List | Hizmetlerle ilgili tüm olayları alır. |
| Uygulama-liste | Uygulamayla ilgili olayları alır. |
| Küme listesi | Kümeyle ilgili tüm olayları alır. |
| düğüm listesi | Düğümle ilgili olayları alır. |
| Bölüm-tüm çoğaltmalar-liste | Bir bölüm için çoğaltmalarla ilgili tüm olayları alır. |
| Bölüm listesi | Bölümle ilgili olayları alır. |
| Bölüm-çoğaltma-liste | Bölüm çoğaltmayla ilgili olayları alır. |
| hizmet listesi | Hizmetle ilgili olayları alır. |

## <a name="sfctl-events-all-applications-list"></a>sfctl olayları tümü-uygulamalar-liste
Uygulamalarla ilgili tüm olayları alır.

Yanıt, ApplicationEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl olayları All-Nodes-List
Tüm düğümlerle ilgili olayları alır.

Yanıt, NodeEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl olayları tümü-bölümler-liste
Tüm bölümlerle ilgili olayları alır.

Yanıt, PartitionEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-all-services-list"></a>sfctl olayları All-Services-List
Hizmetlerle ilgili tüm olayları alır.

Yanıt, ServiceEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-application-list"></a>sfctl olayları uygulama-liste
Uygulamayla ilgili olayları alır.

Yanıt, ApplicationEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-cluster-list"></a>sfctl olayları kümesi-liste
Kümeyle ilgili tüm olayları alır.

Yanıt, ClusterEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-node-list"></a>sfctl olayları düğüm-listesi
Düğümle ilgili olayları alır.

Yanıt, NodeEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --Node-Name [gerekli] | Düğümün adı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl olayları bölümü-tümü-çoğaltmalar-listesi
Bir bölüm için çoğaltmalarla ilgili tüm olayları alır.

Yanıt, ReplicaEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-partition-list"></a>sfctl olayları bölümü-liste
Bölümle ilgili olayları alır.

Yanıt, PartitionEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl olayları bölümü-çoğaltma-listesi
Bölüm çoğaltmayla ilgili olayları alır.

Yanıt, ReplicaEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-events-service-list"></a>sfctl olayları hizmeti-liste
Hizmetle ilgili olayları alır.

Yanıt, ServiceEvent nesnelerinin listesidir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --bitiş-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun bitiş zamanı. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --başlangıç-saat-UTC [gerekli] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ içindeki bir arama sorgusunun başlangıç saati. |
| --Events-Types-Filter | Bu, yalnızca yanıta dahil edilecek FabricEvents türlerini belirten, virgülle ayrılmış bir dizedir. |
| --exclude-Analysis-Events | Bu parametre true ise, analiz Sısevents 'nin alınmasını devre dışı bırakır. |
| --Skip-bağıntı-arama | Bu parametre, true ise CorrelatedEvents bilgisinin aranmasını devre dışı bırakır. Aksi halde, her FabricEvent içindeki CorrelationEvents işleme ve HasCorrelatedEvents alanı doldurulur. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

