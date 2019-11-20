---
title: Azure NetApp Files kaynak sağlayıcısı hatalarını giderme | Microsoft Docs
description: Ortak Azure NetApp Files kaynak sağlayıcısı hatalarına yönelik nedenler, çözümler ve geçici çözümleri açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597200"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Azure NetApp Files Kaynak Sağlayıcısı hatalarını giderme 

Bu makalede ortak Azure NetApp Files kaynak sağlayıcısı hataları, nedenleri, çözümleri ve geçici çözümleri (varsa) açıklanmaktadır.

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Ortak Azure NetApp Files kaynak sağlayıcısı hataları

***BareMetalTenantId değiştirilemez.***  

Bu hata, bir birimi güncelleştirmeye veya düzeltme ekine çalıştığınızda ve `BaremetalTenantId` özelliği değiştirilmiş bir değere sahip olduğunda oluşur.

* Neden:   
Bir birimi güncelleştirmeye çalışıyorsunuz ve `BaremetalTenantId` özelliği Azure 'da depolanan değerden farklı bir değere sahip.
* Çözümden   
Patch ve Update (put) isteğine `BaremetalTenantId` eklemeyin. Alternatif olarak, `BaremetalTenantId` istekte aynı olduğundan emin olun.

***ServiceLevel değiştirilemez.***  

Bu hata, kapasite havuzunda zaten birimler varsa, farklı bir hizmet düzeyiyle bir kapasite havuzunu güncelleştirmeyi veya düzeltme ekini çalıştırmayı denediğinizde oluşur.

* Neden:   
Havuz birimler içerdiğinde bir kapasite havuzu hizmet düzeyini güncelleştirmeye çalışıyorsunuz.
* Çözümden   
Kapasite havuzundaki tüm birimleri silin ve ardından hizmet düzeyini değiştirin.
* Sorunu   
Başka bir kapasite havuzu oluşturun ve ardından yeni kapasite havuzunda birimleri tekrar oluşturun.

***PoolId değiştirilemez***  

Bu hata, değiştirilen bir `PoolId` özelliği ile bir kapasite havuzunu güncelleştirmeyi veya düzeltme ekini çalıştırmayı denediğinizde oluşur.

* Neden:   
Bir kapasite havuzunu `PoolId` özelliğini güncelleştirmeye çalışıyorsunuz. @No__t_0 özelliği salt okunurdur ve değiştirilemez.
* Çözümden   
Patch ve Update (put) isteğine `PoolId` eklemeyin.  Alternatif olarak, `PoolId` istekte aynı olduğundan emin olun.

***CreationToken değiştirilemiyor.***

Bu hata, birim oluşturulduktan sonra dosya yolunu (`CreationToken`) değiştirmeye çalıştığınızda oluşur. Birim oluşturulduğunda dosya yolu (`CreationToken`) ayarlanmalıdır ve daha sonra değiştirilemez.

* Neden:   
Dosya yolunu (`CreationToken`), desteklenen bir işlem olmayan birim oluşturulduktan sonra değiştirmeye çalışıyorsunuz. 
* Çözümden   
Dosya yolunu değiştirmek gerekmiyorsa, hata iletisini kapatmak için istekten parametreyi kaldırmayı göz önünde bulundurun.
* Sorunu   
Dosya yolunu (`CreationToken`) değiştirmeniz gerekiyorsa, yeni bir dosya yolu ile yeni bir birim oluşturabilir ve sonra verileri yeni birime geçirebilirsiniz.

***CreationToken en az 16 karakter uzunluğunda olmalıdır.***

Dosya yolu (`CreationToken`) uzunluk gereksinimini karşılamadığında bu hata oluşur. Dosya yolunun uzunluğu en az bir karakter uzunluğunda olmalıdır.

* Neden:   
Dosya yolu boş.  API 'yi kullanarak bir birim oluşturduğunuzda, oluşturma belirteci gereklidir. Azure portal kullanıyorsanız, dosya yolu otomatik olarak oluşturulur.
* Çözümden   
Dosya yolu (`CreationToken`) olarak en az bir karakter girin.

***Etki alanı adı değiştirilemez.***

Bu hata, Active Directory etki alanı adını değiştirmeyi denediğinizde oluşur.

* Neden:   
Etki alanı adı özelliğini güncelleştirmeye çalışıyorsunuz.
* Çözümden    
Hiçbiri. Etki alanı adını değiştiremezsiniz.
* Sorunu   
Active Directory yapılandırmasını kullanarak tüm birimleri silin. Sonra Active Directory yapılandırmasını silip birimleri yeniden oluşturun.

***Nesne ExportPolicy. Rules [Ruleındex] için yinelenen değer hatası.***

Bu hata, dışarı aktarma ilkesi benzersiz bir dizinle tanımlanmadığında oluşur. Dışarı aktarma ilkelerini tanımladığınızda, tüm dışa aktarma ilkesi kurallarının 1 ile 5 arasında benzersiz bir dizini olmalıdır.

* Neden:   
Tanımlanan dışarı aktarma ilkesi, ilke kuralları verme gereksinimini karşılamıyor. En düşük ve beş dışarı aktarma ilkesi kuralı için en fazla bir dışarı aktarma ilkesi kuralına sahip olmanız gerekir.
* Çözümden   
Dizinin zaten kullanılmadığından ve 1 ile 5 aralığında olduğundan emin olun.
* Sorunu   
Ayarlamaya çalıştığınız kural için farklı bir dizin kullanın.

***{Action} {resourceTypeName} hatası***

Bu hata, diğer hata işleme bir kaynakta eylem gerçekleştirirken hatayı işleyemediğinde görüntülenir.   ' Error ' metnini içerir. @No__t_0 (`getting`, `creating`, `updating` veya `deleting`) olabilir.  @No__t_0 `resourceTypeName` (örneğin, `netAppAccount`, `capacityPool`, `volume`, vb.).

* Neden:   
Bu hata, nedeni bilinen işlenmemiş bir özel durumdur.
* Çözümden   
Günlüklerde ayrıntılı nedeni raporlamak için Azure Destek Merkezi 'ne başvurun.
* Sorunu   
Hiçbiri.

***Dosya yolu adı yalnızca harf, sayı ve kısa çizgi (""-"") içerebilir.***

Bu hata, dosya yolu desteklenmeyen karakterler (örneğin, bir nokta ("."), virgül (","), alt çizgi ("_") veya dolar işareti ("$") içerdiğinde oluşur.

* Neden:   
Dosya yolu, desteklenmeyen karakterler (örneğin, bir nokta ("."), virgül (","), alt çizgi ("_") veya dolar işareti ("$") içeriyor.
* Çözümden   
Girdiğiniz dosya yolundan alfabetik harf, sayı veya kısa çizgi ("-") olmayan karakterleri kaldırın.
* Sorunu   
Yeni sözcüklerin başlangıcını göstermek için boşluk yerine bir alt çizgiyi bir tire ile değiştirebilir veya boşluk yerine büyük harfleri kullanabilirsiniz.  Örneğin, "yeni birim" yerine "NewVolume" kullanın.

***Filesystemıd değiştirilemez.***

@No__t_0 değiştirmeyi denediğinizde bu hata oluşur.  @No__t_0 değiştirmek desteklenen bir işlem değil. 

* Neden:   
Dosya sisteminin KIMLIĞI, birim oluşturulduğunda ayarlanır. `FileSystemId` daha sonra değiştirilemez.
* Çözümden   
Bir yama ve güncelleştirme (put) isteğine `FileSystemId` eklemeyin.  Alternatif olarak, `FileSystemId` istekte aynı olduğundan emin olun.

***Kimliği ' {String} ' olan ActiveDirectory yok.***

@No__t_0 bölümü, Active Directory bağlantısı için `ActiveDirectoryId` özelliğinde girdiğiniz değerdir.

* Neden:   
Active Directory yapılandırmasına sahip bir hesap oluşturduğunuzda, boş olması beklenen `ActiveDirectoryId` için bir değer girdiniz.
* Çözümden   
Oluşturma (put) isteğine `ActiveDirectoryId` eklemeyin.

***Geçersiz api-Version.***

API sürümü gönderilmedi ya da geçersiz bir değer içeriyor.

* Neden:   
Sorgu parametresindeki değer `api-version` geçersiz bir değer içeriyor.
* Çözümden   
Doğru API sürümü değerini kullanın.  Kaynak sağlayıcısı birçok API sürümünü destekler. Değer yyyy-aa-gg biçimindedir.

***@No__t_1 için geçersiz ' {Value} ' değeri alındı.***

Bu ileti `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv3` ve `Nfsv4` alanlarında bir hata olduğunu gösterir.

* Neden:   
Giriş doğrulama isteği şu alanlardan en az biri için başarısız oldu: `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv`3 ve `Nfsv4`.
* Çözümden   
Komut satırında tüm gerekli ve çakışmayan parametreleri ayarladığınızdan emin olun. Örneğin, hem `UnixReadOnly` hem de `UnixReadWrite` parametrelerini aynı anda ayarlayamazsınız.
* Sorunu   
Yukarıdaki çözüme bakın.

***VLAN {2} {1} {0} IP aralığı zaten kullanımda***

Bu hata, kullanılan IP aralıklarının iç kayıtlarının yeni atanan IP adresiyle bir çakışmaya sahip olması nedeniyle oluşur.

* Neden:   
Birim oluşturma için atanan IP adresi zaten kayıtlı.
Nedeni, daha önce başarısız bir birim oluşturma olabilir.
* Çözümden   
Azure Destek Merkezi 'ne başvurun.

***' {Property} ' için değer eksik.***

Bu hata, istekte gerekli bir özelliğin eksik olduğunu gösterir. {Property} dizesi eksik özelliğin adını içeriyor.

* Neden:   
Giriş doğrulama isteği, özelliklerden en az biri için başarısız oldu.
* Çözümden   
İstekte tüm gerekli ve çakışmayan özellikleri, özellikle de hata iletisinden ayarladığınızdan emin olun.

***Bağlamahedefleri değiştirilemez.***

Bu hata, bir Kullanıcı, birim bağlama hedefleri özelliğini güncelleştirmeye veya düzeltme ekine çalışırken oluşur.

* Neden:   
Volume `MountTargets` özelliğini güncelleştirmeye çalışıyorsunuz. Bu özelliğin değiştirilmesi desteklenmiyor.
* Çözümden   
Bir yama ve güncelleştirme (put) isteğine `MountTargets` eklemeyin.  Alternatif olarak, `MountTargets` istekte aynı olduğundan emin olun.

***Ad zaten kullanımda.***

Bu hata, kaynağın adının zaten kullanımda olduğunu gösterir.

* Neden:   
Mevcut bir kaynak için kullanılan bir ada sahip bir kaynak oluşturmaya çalışıyorsunuz.
* Çözümden   
Kaynağı oluştururken benzersiz bir ad kullanın.

***Dosya yolu zaten kullanımda.***

Bu hata, birim için dosya yolunun zaten kullanımda olduğunu gösterir.

* Neden:   
Mevcut bir birimle aynı olan bir dosya yolu ile bir birim oluşturmaya çalışıyorsunuz.
* Çözümden   
Birim oluştururken benzersiz bir dosya yolu kullanın.

***Ad çok uzun.***

Bu hata, kaynak adının maksimum uzunluk gereksinimini karşılamıyor olduğunu gösterir.

* Neden:   
Kaynak adı çok uzun.
* Çözümden   
Kaynak için daha kısa bir ad kullanın.

***Dosya yolu çok uzun.***

Bu hata, birimin dosya yolunun en fazla uzunluk gereksinimini karşılamıyor olduğunu gösterir.

* Neden:   
Birim dosya yolu çok uzun.
* Çözümden   
Daha kısa bir dosya yolu kullanın.

***Ad çok kısa.***

Bu hata, kaynak adının minimum uzunluk gereksinimini karşılamıyor olduğunu gösterir.

* Neden:   
Kaynak adı çok kısa.
* Çözümden   
Kaynak için daha uzun bir ad kullanın.

***Dosya yolu çok kısa.***

Bu hata, birim dosya yolunun en düşük uzunluk gereksinimini karşılamıyor olduğunu gösterir.

* Neden:   
Birim dosya yolu çok kısa.
* Çözümden   
Birim dosyası yolunun uzunluğunu artırın.

***Azure NetApp Files API 'sine ulaşılamıyor.***

Azure API 'SI, birimleri yönetmek için Azure NetApp Files API 'sini kullanır. Bu hata, API bağlantısıyla ilgili bir sorun olduğunu gösterir.

* Neden:   
Temeldeki API yanıt vermiyor ve bir iç hataya neden oldu. Bu hatanın geçici olması olasıdır.
* Çözümden   
Sorunun geçici olması olasıdır. İstek bir süre sonra başarılı olmalıdır.
* Sorunu   
Hiçbiri. Temel alınan API, birimleri yönetmek için gereklidir.

***' @No__t_1 ' için işlem sonucu kimliği bulunamadı.***

Bu hata, bir iç hatanın işlemin tamamlanmasını engellediğini gösterir.

* Neden:   
Bir iç hata oluştu ve işlemin tamamlanmasını engelledi.
* Çözümden   
Bu hatanın geçici olması olasıdır. Birkaç dakika bekleyip yeniden deneyin. Sorun devam ederse, teknik desteğe sorunu araştırmak için bir bilet oluşturun.
* Sorunu   
Birkaç dakika bekleyin ve sorunun devam edip etmediğini denetleyin.

***CIFS ve NFS protokol türlerini karıştırılamaz***

Bu hata, bir birim oluşturmaya çalışırken ve birim özelliklerinde hem CIFS (SMB) hem de NFS protokol türleri varsa oluşur.

* Neden:   
Birim özelliklerinde hem CIFS (SMB) hem de NFS protokol türleri kullanılır.
* Çözümden   
Protokol türlerinden birini kaldırın.
* Sorunu   
Protokol türü özelliğini boş veya null bırakın.

***Öğe sayısı: nesne için {value}: ExportPolicy. Rules [Ruleındex] En düşük değer olan aralığın dışında.***

Bu hata verme ilkesi kuralları minimum veya maksimum Aralık gereksinimini karşılamıyorsa oluşur. Dışarı aktarma ilkesini tanımlarsanız, en yüksek ve beş dışarı aktarma ilkesi kuralında en fazla bir dışarı aktarma ilkesi kuralına sahip olmalıdır.

* Neden:   
Tanımladığınız dışarı aktarma ilkesi gerekli aralığı karşılamıyor.
* Çözümden   
Dizinin zaten kullanılmadığından ve 1 ile 5 aralığında olduğundan emin olun.
* Sorunu   
Birimlerde dışarı aktarma ilkesi kullanılması zorunlu değildir. Verme ilkesi kurallarını kullanmanız gerekmiyorsa, bu ilkeyi tamamen atlayabilirsiniz.

***Yalnızca bir Active Directory 'ye izin verilir***

Bu hata, bir Active Directory yapılandırması oluşturmaya çalıştığınızda ve bölgede abonelik için zaten bir tane varsa oluşur. Birden fazla Active Directory yapılandırması oluşturmaya çalıştığınızda hata da oluşabilir.

* Neden:   
Bir Active Directory (güncelleştirme değil) oluşturmaya çalışıyorsunuz, ancak bir tane zaten var.
* Çözümden   
Active Directory yapılandırması kullanımda değilse, önce mevcut yapılandırmayı silip sonra oluşturma işlemini yeniden deneyebilirsiniz.
* Sorunu   
Hiçbiri. Yalnızca bir Active Directory izin verilir.

***' {Operation} ' işlemi desteklenmiyor.***

Bu hata, işlemin etkin abonelik veya kaynak için kullanılabilir olmadığını gösterir.

* Neden:   
İşlem abonelik veya kaynak için kullanılamıyor.
* Çözümden   
İşlemin doğru girildiğinden ve kullanmakta olduğunuz kaynak ve abonelik için kullanılabilir olduğundan emin olun.

***OwnerId değiştirilemez***

Birimin OwnerId özelliğini değiştirmeyi denediğinizde bu hata oluşur. OwnerId 'nin değiştirilmesi desteklenen bir işlem değil. 

* Neden:   
@No__t_0 özelliği birim oluşturulduğunda ayarlanır. Özellik daha sonra değiştirilemez.
* Çözümden   
Bir yama ve güncelleştirme (put) isteğine `OwnerId` eklemeyin. Alternatif olarak, `OwnerId` istekte aynı olduğundan emin olun.

***Üst havuz bulunamadı***

Bu hata, bir birim oluşturmaya çalıştığınızda ve birimi oluşturduğunuz kapasite havuzu bulunamadığı zaman oluşur.

* Neden:   
Birimin oluşturulduğu kapasite havuzu bulunamadı.
* Çözümden   
Büyük olasılıkla havuz tam olarak oluşturulmamış veya birim oluşturma sırasında zaten silinmiş.

***Bu kaynak türü için düzeltme eki işlemi desteklenmiyor.***

Bu hata, bağlama hedefini veya anlık görüntüsünü değiştirmeye çalıştığınızda oluşur.

* Neden:   
Bağlama hedefi oluşturulduğunda tanımlanır ve daha sonra değiştirilemez.
Anlık görüntüler değiştirilebilen özellikleri içermez.
* Çözümden   
Hiçbiri. Bu kaynaklarda değiştirilebilen hiçbir özellik yoktur.

***Toplam birim boyutu için havuz boyutu çok küçük.***

Bu hata, kapasite havuzu boyutunu güncelleştirirken oluşur ve boyut, bu kapasite havuzundaki tüm birimlerin toplam `usedBytes` değerinden daha küçüktür.  Bu hata, yeni bir birim oluştururken veya var olan bir birimi yeniden boyutlandırdığınızda veya yeni birim boyutu kapasite havuzundaki boş alanı aştığında de oluşabilir.

* Neden:   
Kapasite havuzunu kapasite havuzundaki tüm birimlerde usedBytes 'dan daha küçük bir boyuta güncelleştirmeye çalışıyorsunuz.  Veya kapasite havuzundaki boş alandan daha büyük bir birim oluşturmaya çalışıyorsunuz.  Alternatif olarak, bir birimi yeniden boyutlandırmaya çalışıyorsunuz ve yeni boyut kapasite havuzundaki boş alanı aşıyor.
* Çözümden   
Kapasite havuzu boyutunu daha büyük bir değere ayarlayın veya bir birim için daha küçük bir birim oluşturun.
* Sorunu   
Kapasite havuzu boyutunun bu boyuta güncelleştirilebilmesi için yeterli birimleri kaldırın.

***Özellik: anlık görüntü konumu birimle aynı olmalıdır***

Bu hata, anlık görüntünün sahibi olan birim dışında bir konum içeren bir anlık görüntü oluştururken oluşur.

* Neden:   
Anlık görüntü için location özelliğinde geçersiz değer.
* Çözümden   
Location özelliğinde geçerli bir dize ayarlayın.

***{ResourceType} adı, kaynak tanımlayıcı adı ile aynı olmalıdır.***

Bu hata, bir kaynak oluştururken oluşur ve ad özelliğini `resourceId` Name özelliğinden başka bir değerle doldurursunuz.

* Neden:   
Kaynak oluşturduğunuzda ad özelliğinde geçersiz değer.
* Çözümden   
Ad özelliğini boş bırakın veya `resourceId`, ad özelliği ile aynı değeri (son ters eğik çizgi "/" ve soru işareti "?" arasında) kullanmasına izin verin.

***Protokol türü {value} bilinmiyor***

Bu hata, bilinmeyen bir protokol türüne sahip bir birim oluştururken oluşur.  Geçerli değerler şunlardır "NFSv3", "NFSv4" ve "CIFS".

* Neden:   
Volume `protocolType` özelliğinde geçersiz bir değer ayarlamaya çalışıyorsunuz.
* Çözümden   
@No__t_0 için geçerli bir dize ayarlayın.
* Sorunu   
@No__t_0 null olarak ayarlayın.

***Protokol türleri değiştirilemez***

Bu hata, bir birim için `ProtocolType` güncelleştirmeyi veya yama yapmayı denediğinizde oluşur.  ProtocolType değiştirilirken desteklenen bir işlem değil.

* Neden:   
@No__t_0 özelliği birim oluşturulduğunda ayarlanır.  Güncelleştirilemez.
* Çözümden   
Hiçbiri.
* Sorunu   
Yeni protokol türleriyle başka bir birim oluşturun.

***{ResourceType} türünde kaynak oluşturmak, {parentResourceType} başına {resourceType} türündeki {Quota} kaynaklarının kotasını aşacak. Geçerli kaynak sayısı {currentCount}, yeni bir tane oluşturmadan önce lütfen bu türden bazı kaynakları silin.***

Bu hata, bir kaynak (`NetAppAccount`, `CapacityPool`, `Volume` veya `Snapshot`) oluşturmaya çalışırken oluşur, ancak kotayı sınırına ulaştı.

* Neden:   
Bir kaynak oluşturmaya çalışıyorsunuz, ancak kota sınırına ulaşıldı (örnek: abonelik başına `NetAppAccounts` veya `NetAppAccount` başına `CapacityPools`).
* Çözümden   
Kota sınırını artırın.
* Sorunu   
Aynı türdeki kullanılmayan kaynakları silin ve yeniden oluşturun.

***' {PropertyName} ' salt okunurdur özelliği için bir değer alındı.***

Bu hata, değiştirilemeyen bir özellik için bir değer tanımladığınızda oluşur. Örneğin, birim KIMLIĞINI değiştiremezsiniz.

* Neden:   
Değiştirilemeyen bir parametreyi (örneğin, birim KIMLIĞI) değiştirmeye çalışıyorsunuz.
* Çözümden   
Özelliği için bir değer değiştirmeyin.

***İstenen {Resource} bulunamadı.***

Bu hata, var olmayan bir kaynağa (örneğin, bir birim veya anlık görüntü) başvurulmasına çalıştığınızda oluşur. Kaynak silinmiş veya yanlış yazılmış bir kaynak adına sahip olabilir.

* Neden:   
Zaten silinmiş olan veya yanlış yazılmış bir kaynak adına sahip olan varolmayan bir kaynağa (örneğin, bir birim veya anlık görüntü) başvurılmaya çalışıyorsunuz.
* Çözümden   
Doğru Başvurulmuş olduğundan emin olmak için, yazım hataları isteğine bakın.
* Sorunu   
Yukarıdaki çözüm bölümüne bakın.

***' {VolumeServiceLevel} ' hizmet düzeyi, ' {poolServiceLevel} ' üst öğesinden daha yüksek***

Bu hata, bir birimi oluştururken veya güncelleştirirken oluşur ve hizmet düzeyini onu içeren KAPASİTE havuzundan daha yüksek bir düzeye ayarlamış olursunuz.

* Neden:   
Üst KAPASİTE havuzundan daha yüksek bir dereceli hizmet düzeyiyle birim oluşturmaya veya güncelleştirmeye çalışıyorsunuz.
* Çözümden   
Hizmet düzeyini, üst KAPASİTE havuzundan aynı veya daha düşük bir dereceye ayarlayın.
* Sorunu   
Birimi doğru hizmet düzeyiyle başka bir kapasite havuzunda oluşturun. Alternatif olarak, kapasite havuzundaki tüm birimleri silin ve kapasite havuzunun hizmet düzeyini daha yüksek bir dereceye ayarlayın.

***SMB sunucu adı, 10 karakterden uzun olamaz.***

Bu hata, bir hesap için Active Directory yapılandırması oluştururken veya güncelleştirirken oluşur.

* Neden:   
SMB sunucusu adının uzunluğu 10 karakteri aşıyor.
* Çözümden   
Daha kısa bir sunucu adı kullanın. En fazla 10 karakter uzunluğunda olur.
* Sorunu   
Hiçbiri.  Yukarıdaki çözüme bakın. 

***SubnetID değiştirilemez.***

Bu hata, birim oluşturulduktan sonra `subnetId` değiştirmeye çalıştığınızda oluşur.  birim oluşturulduğunda `SubnetId` ayarlanmalıdır ve daha sonra değiştirilemez.

* Neden:   
Birim oluşturulduktan sonra, desteklenmeyen bir işlem olmayan `subnetId` değiştirmeye çalışıyorsunuz. 
* Çözümden   
@No__t_0 değiştirmek gerekmiyorsa, hata iletisini kapatmak için istekten parametreyi kaldırmayı göz önünde bulundurun.
* Sorunu   
@No__t_0 değiştirmeniz gerekiyorsa, yeni bir `subnetId` yeni bir birim oluşturabilir ve sonra verileri yeni birime geçirebilirsiniz.

***SubnetID geçersiz biçimde.***

Bu hata yeni bir birim oluşturmaya çalıştığınızda oluşur, ancak `subnetId` bir alt ağ için `resourceId` değildir.

* Neden:   
Bu hata yeni bir birim oluşturmaya çalıştığınızda oluşur, ancak `subnetId` bir alt ağ için `resourceId` değildir. 
* Çözümden   
Kullanılan alt ağ için bir `resourceId` içerdiğinden emin olmak üzere `subnetId` değerini denetleyin.
* Sorunu   
Hiçbiri. Yukarıdaki çözüme bakın. 

***Alt ağda ' Microsoft. NetApp/Volumes ' temsili olmalıdır.***

Bu hata, bir birim oluştururken ve seçilen alt ağın `Microsoft.NetApp/volumes` için temsilci olmadığı durumlarda oluşur.

* Neden:   
Birim oluşturmaya çalıştınız ve `Microsoft.NetApp/volumes` için temsilci olmayan bir alt ağ seçtiniz.
* Çözümden   
@No__t_0 için temsilci atanmış başka bir alt ağ seçin.
* Sorunu   
Alt ağa doğru bir temsili ekleyin.

***Belirtilen kaynak türü bilinmiyor/uygulanamaz.***

Bu hata, uygulanabilir olmayan bir kaynak türü ya da bilinmeyen bir kaynak türü için bir ad denetimi istendiğinde oluşur.

* Neden:   
Bilinmeyen veya desteklenmeyen bir kaynak türü için ad denetimi istendi.
* Çözümden   
İsteği yaptığınız kaynağın desteklenip desteklenmediğini veya yazım hatası bulunmadığını denetleyin.
* Sorunu   
Yukarıdaki çözüme bakın.

***Bilinmeyen Azure NetApp Files hatası.***

Azure API 'SI, birimleri yönetmek için Azure NetApp Files API 'sini kullanır. Hata, API iletişimdeki bir sorunu gösterir.

* Neden:   
Temeldeki API bilinmeyen bir hata gönderiyor. Bu hatanın geçici olması olasıdır.
* Çözümden   
Sorunun geçici olması olasıdır ve isteğin bir süre sonra başarılı olması gerekir. Sorun devam ederse, sorunu araştırılması için bir destek bileti oluşturun.
* Sorunu   
Hiçbiri. Temel alınan API, birimleri yönetmek için gereklidir.

***Bilinmeyen ' {propertyName} ' özelliği için değer alındı.***

Bu hata, birim, anlık görüntü veya bağlama hedefi gibi bir kaynak için varolmayan Özellikler sağlandığında oluşur.

* Neden:   
İstek, her kaynakla kullanılabilecek bir özellik kümesine sahiptir. İstekte varolmayan özellikler ekleyemezsiniz.
* Çözümden   
Tüm özellik adlarının doğru yazıldığından ve Özellikler abonelik ve kaynak için kullanılabilir olduğundan emin olun.
* Sorunu   
Hataya neden olan özelliği ortadan kaldırmak için istekte tanımlanan özellik sayısını azaltın.

***Bu kaynak türü için güncelleştirme işlemi desteklenmiyor.***

Yalnızca birimler güncelleştirilebilen olabilir. Bu hata, desteklenmeyen bir güncelleştirme işlemini gerçekleştirmeye çalıştığınızda (örneğin, bir anlık görüntüyü güncelleştirirken) oluşur.

* Neden:   
Güncelleştirmeye çalıştığınız kaynak güncelleştirme işlemini desteklemiyor. Yalnızca birimlerde özellikleri değiştirilebilir.
* Çözümden   
Hiçbiri. Güncelleştirmeye çalıştığınız kaynak güncelleştirme işlemini desteklemiyor. Bu nedenle, değiştirilemez.
* Sorunu   
Bir birim için, güncelleştirmenin yerinde bulunduğu yeni bir kaynak oluşturun ve verileri geçirin.

***Birim başarılı olmayan bir havuzda oluşturulamaz.***

Bu hata, başarılı durumunda olmayan bir havuzda birim oluşturmaya çalıştığınızda oluşur. Büyük olasılıkla, kapasite havuzu için oluşturma işlemi bazı nedenlerle başarısız oldu.

* Neden:   
Yeni birimi içeren kapasite havuzu başarısız durumda.
* Çözümden   
Kapasite havuzunun başarıyla oluşturulup oluşturulmadığından ve başarısız durumda olmadığından emin olun.
* Sorunu   
Yeni bir kapasite havuzu oluşturun ve yeni havuzda birimi oluşturun.

***Birim oluşturuluyor ve şu anda silinemiyor.***

Bu hata, hala oluşturulmakta olan bir birimi silmeye çalıştığınızda oluşur.

* Neden:   
Birimi silmeye çalıştığınızda bir birim hala oluşturuluyor.
* Çözümden   
Birim oluşturma işlemi tamamlanana kadar bekleyin ve sonra silme işlemini yeniden deneyin.
* Sorunu   
Yukarıdaki çözüme bakın.

***Birim siliniyor ve şu anda silinemiyor.***

Bu hata, zaten silinmekte olan bir birimi silmeye çalıştığınızda oluşur.

* Neden:   
Birimi silmeye çalıştığınızda bir birim zaten silinmekte.
* Çözümden   
Geçerli silme işlemi tamamlanana kadar bekleyin.
* Sorunu   
Yukarıdaki çözüme bakın.

***Birim güncelleştiriliyor ve şu anda silinemiyor.***

Bu hata, güncelleştirilmekte olan bir birimi silmeye çalıştığınızda oluşur.

* Neden:   
Birimi silmeye çalıştığınızda bir birim güncelleştiriliyor.
* Çözümden   
Güncelleştirme işlemi tamamlanana kadar bekleyin ve sonra silme işlemini yeniden deneyin.
* Sorunu   
Yukarıdaki çözüme bakın.

***Birim bulunamadı veya başarıyla oluşturulmadı.***

Bu hata, birim oluşturma işlemi başarısız olduğunda ve birimi değiştirmeye veya birim için bir anlık görüntü oluşturmaya çalışırken oluşur.

* Neden:   
Birim yok veya oluşturma başarısız oldu.
* Çözümden   
Doğru birimi değiştirip, birim oluşturma işleminin başarılı olduğunu denetleyin. Ya da, bir anlık görüntü oluşturduğunuz birimin var olduğundan emin olun.
* Sorunu   
Hiçbiri.  Yukarıdaki çözüme bakın. 

***Belirtilen oluşturma belirteci zaten var***

Bu hata, bir birim oluşturmaya çalıştığınızda ve bir birimin zaten bulunduğu bir oluşturma belirteci (dışarı aktarma yolu) belirttiğinizde oluşur.

* Neden:   
Birim oluşturma sırasında belirttiğiniz oluşturma belirteci (dışarı aktarma yolu) zaten başka bir birimle ilişkili. 
* Çözümden   
Farklı bir oluşturma belirteci seçin.  Alternatif olarak, diğer birimi de silin.

***Belirtilen oluşturma belirteci ayrılmış***

Bu hata, bir birim oluşturmaya çalıştığınızda oluşur ve dosya yolu (oluşturma belirteci) olarak "varsayılan" veya "hiçbiri" değerini belirtmeniz gerekir.

* Neden:    
Bir birim oluşturmaya çalışıyorsunuz ve dosya yolu (oluşturma belirteci) olarak "default" veya "none" değerini belirtmeniz gerekir.
* Çözümden   
Farklı bir dosya yolu (oluşturma belirteci) seçin.
 
***Active Directory kimlik bilgileri kullanımda***

Bu hata, en az bir SMB biriminin hala bulunduğu bir hesaptan Active Directory yapılandırmasını silmeye çalıştığınızda oluşur.  SMB birimi, silmeye çalıştığınız Active Directory yapılandırması kullanılarak oluşturulmuştur.

* Neden:   
Active Directory yapılandırmasını bir hesaptan silmeye çalışıyorsunuz, ancak başlangıçta Active Directory yapılandırması kullanılarak oluşturulan en az bir SMB birimi hala var. 
* Çözümden   
İlk olarak, Active Directory yapılandırması kullanılarak oluşturulan tüm SMB birimlerini silin.  Sonra yapılandırma silme işlemini yeniden deneyin.

***Kimlik bilgileri kullanımda ise kuruluş birimi ataması değiştirilemez***

Bu hata, bir Active Directory yapılandırmanın kuruluş birimini değiştirmeyi denediğinizde oluşur, ancak en az bir SMB birimi hala mevcut.  SMB birimi, silmeye çalıştığınız bu Active Directory yapılandırması kullanılarak oluşturuldu.

* Neden:   
Active Directory yapılandırmanın kuruluş birimini değiştirmeye çalışıyorsunuz.  Ancak başlangıçta Active Directory yapılandırması kullanılarak oluşturulan en az bir SMB birimi hala var.
* Çözümden   
 İlk olarak, Active Directory yapılandırması kullanılarak oluşturulan tüm SMB birimlerini silin.  Sonra yapılandırma silme işlemini yeniden deneyin. 

***Active Directory güncelleştirme zaten devam ediyor***

Bu hata, bir düzenleme işleminin zaten devam ettiği bir Active Directory yapılandırmasını düzenlemeye çalıştığınızda oluşur.

* Neden:   
Bir Active Directory yapılandırmasını düzenlemeye çalışıyorsunuz, ancak başka bir düzenleme işlemi zaten devam ediyor.
* Çözümden   
Şu anda çalışan düzenleme işlemi tamamlanana kadar bekleyin.

***Önce seçilen kimlik bilgilerini kullanarak tüm birimleri Sil***

Bu hata, bir Active Directory yapılandırmasını silmeye çalıştığınızda oluşur, ancak en az bir SMB birimi hala mevcut.  SMB birimi, silmeye çalıştığınız Active Directory yapılandırması kullanılarak oluşturulmuştur.

* Neden:   
Bir Active Directory yapılandırmasını silmeye çalışıyorsunuz, ancak başlangıçta Active Directory yapılandırması kullanılarak oluşturulan en az bir SMB birimi hala var.
* Çözümden   
İlk olarak, Active Directory yapılandırması kullanılarak oluşturulan tüm SMB birimlerini silin.  Sonra yapılandırma silme işlemini yeniden deneyin. 

***Bölgede Active Directory kimlik bilgileri bulunamadı***

Bu hata, bir SMB birimi oluşturmaya çalıştığınızda oluşur, ancak bölge hesabına Active Directory yapılandırma eklenmez.

* Neden:   
Bir SMB birimi oluşturmaya çalışıyorsunuz, ancak hesaba Active Directory yapılandırma eklenmedi. 
* Çözümden   
SMB birimini oluşturmadan önce hesaba bir Active Directory yapılandırması ekleyin.

***DNS sunucusu sorgulanamadı. Ağ yapılandırmasının doğru olduğundan ve DNS sunucularının kullanılabilir olduğundan emin olun.***

Bu hata, bir SMB birimi oluşturmaya çalıştığınızda oluşur, ancak bir DNS sunucusuna (Active Directory yapılandırmanızda belirtilen) ulaşılamıyor. 

* Neden:   
Bir SMB birimi oluşturmaya çalışıyorsunuz, ancak bir DNS sunucusuna (Active Directory yapılandırmanızda belirtilen) ulaşılamıyor.
* Çözümden   
Active Directory yapılandırmanızı gözden geçirin ve DNS sunucusu IP adreslerinin doğru ve erişilebilir olduğundan emin olun.
DNS sunucusu IP adresleriyle ilgili bir sorun yoksa, erişimi engelleyen güvenlik duvarlarının olmadığını doğrulayın.

***Çok fazla eşzamanlı iş***

Bu hata, abonelik için şu anda üç anlık görüntü oluşturma işlemi devam ederken bir anlık görüntü oluşturmaya çalıştığınızda oluşur.

* Neden:   
Abonelik için diğer üç anlık görüntü oluşturma işlemi zaten devam ederken bir anlık görüntü oluşturmaya çalışıyorsunuz. 
* Çözümden   
Anlık görüntü oluşturma işlerinin tamamlanması birkaç saniye sürer.  Birkaç saniye bekleyip anlık görüntü oluşturma işlemini yeniden deneyin.

***Ek işler üretilemedi. Lütfen devam eden işlerin bitmesini bekleyin ve yeniden deneyin***

Bu hata, belirli koşullarda bir birimi oluşturmaya veya silmeye çalıştığınızda ortaya çıkabilir.

* Neden:   
Belirli koşullarda bir birimi oluşturmaya veya silmeye çalışıyorsunuz.
* Çözümden   
Bir dakika bekleyin ve işlemi yeniden deneyin.

***Birim, durumlar arasında zaten geçiyor***

Bu hata, şu anda geçiş durumunda olan bir birimi silmeye çalıştığınızda (yani, şu anda oluşturma, güncelleştirme veya silme durumunda) oluşabilir.

* Neden:   
Şu anda geçiş durumunda olan bir birimi silmeye çalışıyorsunuz.
* Çözümden   
Şu anda çalışan (durum geçişi) işlemi tamamlanana kadar bekleyin ve sonra işlemi yeniden deneyin.

***Kaynak birim anlık görüntüsünden yeni birim ayrılamadı***

 Bu hata, bir anlık görüntüden birim oluşturmaya çalıştığınızda ortaya çıkabilir.  

* Neden:   
Bir anlık görüntüden birim oluşturmaya çalışırsınız ve birim bir hata durumunda sona erer.
* Çözümden   
Birimi silin, sonra anlık görüntüden birim oluşturma işlemini yeniden deneyin.

 
## <a name="next-steps"></a>Sonraki adımlar

* [REST API Azure NetApp Files için geliştirme](azure-netapp-files-develop-with-rest-api.md)
