---
title: Horizon API’si
description: Bu kılavuzda, yaygın olarak kullanılan ufuk yöntemleri açıklanmaktadır.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786796"
---
# <a name="horizon-api"></a>Horizon API’si 

Bu kılavuzda, yaygın olarak kullanılan ufuk yöntemleri açıklanmaktadır.

### <a name="getting-more-information"></a>Daha fazla bilgi edinme

Ufku ve IoT Platformu için Defender ile çalışma hakkında daha fazla bilgi için aşağıdaki bilgilere bakın:

- Ufuk açık geliştirme ortamı (ODE) SDK 'Sı için, IoT temsilcisine yönelik Defender ile görüşün.
- Destek ve sorun giderme bilgileri için, iletişim kurun <support@cyberx-labs.com> .

- IoT için Defender konsolunda bulunan IoT için Defender Kullanıcı Kılavuzu ' na erişmek için, :::image type="icon" source="media/references-horizon-api/profile.png"::: **Kullanıcı kılavuzunu indir**' i seçin.


## `horizon::protocol::BaseParser`

Tüm eklentiler için Özet. Bu iki yöntemden oluşur:

- Yukarıda tanımlanan eklenti filtrelerini işlemek için. Bu şekilde, ayrıştırıcıyla nasıl iletişim kuracağını bilir.
- Gerçek verileri işlemek için.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Eklenti için çağrılan ilk işlev, bir çözümleyici 'nin onu tanıması ve kaydetmesi için bir örnek oluşturur.

### <a name="parameters"></a>Parametreler 

Yok.

### <a name="return-value"></a>Döndürülen değer

Ayrıştırıcı örneğinize shared_ptr.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Bu işlev, yukarıda kayıtlı her eklenti için çağırılır. 

Çoğu durumda bu boş olur. Ufkın hatalı olduğunu öğrenmesi için bir özel durum oluşturur.

### <a name="parameters"></a>Parametreler 

- Size kaydolmak isteyen başka bir eklenti üzerinde config.jstanımlanan dissect_as yapısını içeren bir harita.

### <a name="return-value"></a>Döndürülen değer 

Kayıt uint64_t bir türde işlenen uint64_t dizisi. Bu, haritada, değerlerinin uin64_t olacağı bir bağlantı noktası listenizin bulunduğu anlamına gelir.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Main işlevi. Özellikle, her yeni paket, ayrıştırıcıya her ulaştığında eklenti mantığı. Bu işlev çağrılacaktır, paket işlemeyle ilgili her şey burada yapılmalıdır.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Bu işlev farklı iş parçacıklarından çağrılabilmesi için, eklenti iş parçacığı güvenli olmalıdır. Yığın üzerinde her şeyi tanımlamak iyi bir yaklaşım olabilir.

### <a name="parameters"></a>Parametreler

- Verilerin depolanmasından ve ILayer ve alanlar gibi SDK ile ilgili nesnelerin oluşturulmasıyla ilgili SDK denetim birimi.
- Ham paketin verilerini okumak için yardımcı. Üzerinde config.jstanımladığınız bayt sırasıyla zaten ayarlanmış.

### <a name="return-value"></a>Döndürülen değer 

İşlemin sonucu. Bu, *başarılı*, *Hatalı biçimlendirilmiş* ya da *Sanity* olabilir.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

İşlemi Sanitation hatası olarak işaretler. Bu, paketin geçerli protokol tarafından tanınmaması anlamına gelir ve aynı filtrelere kaydolduysanız, ufuk bunu diğer ayrıştırıcılara iletmelidir.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Oluşturucu

### <a name="parameters"></a>Parametreler 

- Üzerinde config.jstanımlandığı şekilde günlük için ufuk tarafından kullanılan hata kodunu tanımlar.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Hatalı biçimlendirilmiş sonuç, paketi protokolizin olarak zaten tanıdık, ancak bazı doğrulamalar yanlış oldu (ayrılmış bitler açık veya bir alan eksik).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Oluşturucu

### <a name="parameters"></a>Parametreler  

- config.json öğesinde tanımlandığı şekilde hata kodu.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Başarılı işleme işlemini bilgilendirir. Başarılı olduğunda, paket kabul edildi, veriler bize aittir ve tüm veriler ayıklandı.

## `horizon::protocol::SuccessResult()`

Oluşturucu. Temel başarılı bir sonuç oluşturuldu. Bu, paket ile ilgili olan yönü veya diğer meta verileri bilmiyor demektir.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Oluşturucu.

### <a name="parameters"></a>Parametreler 

- Tanımlanmışsa paketin yönü. Değerler *istek* veya *Yanıt* olabilir.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Oluşturucu.

### <a name="parameters"></a>Parametreler

- Paketin yönü, tanımlandığımızda *istek*, *Yanıt* olabilir.
- Uyarılarına. Bu olaylar başarısız olmayacaktır, ancak ufku bildirilecek.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Oluşturucu.

### <a name="parameters"></a>Parametreler 

-  Uyarılarına. Bu olaylar başarısız olmayacaktır, ancak ufku bildirilecek.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Dize tabanlı başvuruyu bir alan adına dönüştürür (örneğin, function_code) HorizonID.

### <a name="parameters"></a>Parametreler 

- Dönüştürülecek dize.

### <a name="return-value"></a>Döndürülen değer

- Dizeden oluşturuldu HorizonID.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Yeni bir katman oluşturur, böylece ufuk, eklentinin bazı verileri depolamak istediğini bilecektir. Bu, kullanmanız gereken temel depolama birimidir.

### <a name="return-value"></a>Döndürülen değer

Oluşturulan katmana yönelik bir başvuru, bu sayede veri ekleyebilirsiniz.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Örneğin, ILayer gibi farklı nesnelerdeki alanları oluşturmaktan sorumlu alan yönetimi nesnesini alır.

### <a name="return-value"></a>Döndürülen değer

Yöneticiye bir başvuru.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

İstenen KIMLIĞE sahip katmanda 64 bitten oluşan yeni bir sayısal alan oluşturur.

### <a name="parameters"></a>Parametreler 

- Daha önce oluşturduğunuz katman.
- **HORIZON_FIELD** makro tarafından oluşturulan HorizonID.
- Depolamak istediğiniz ham değer.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

İstenen KIMLIĞE sahip katmanda yeni bir dize alanı oluşturur. Bellek taşınır, bu nedenle dikkatli olun. Bu değeri yeniden kullanamazsınız.

### <a name="parameters"></a>Parametreler  

- Daha önce oluşturduğunuz katman.
- **HORIZON_FIELD** makro tarafından oluşturulan HorizonID.
- Depolamak istediğiniz ham değer.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Katmanda, istenen KIMLIĞE sahip yeni bir ham değer (bayt dizisi) alanı oluşturur. Bellek taşınır, bu nedenle dikkatli olun, bu değeri yeniden kullanamazsınız.

### <a name="parameters"></a>Parametreler

- Daha önce oluşturduğunuz katman.
- **HORIZON_FIELD** makro tarafından oluşturulan HorizonID.
- Depolamak istediğiniz ham değer.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

İstenen KIMLIĞE sahip belirtilen türdeki katmanda bir dizi değeri (dizi) alanı oluşturur.

### <a name="parameters"></a>Parametreler

- Daha önce oluşturduğunuz katman.
- **HORIZON_FIELD** makro tarafından oluşturulan HorizonID.
- Dizi içinde depolanacak değerlerin türü.

### <a name="return-value"></a>Döndürülen değer

Değerlerin ekleneceği bir diziye başvuru.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Daha önce oluşturulan diziye yeni bir tamsayı değeri ekler.

### <a name="parameters"></a>Parametreler

- Daha önce oluşturulan dizi.
- Dizide depolanacak ham değer.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Daha önce oluşturulan diziye yeni bir dize değeri ekler. Bellek taşınır, bu nedenle dikkatli olun, bu değeri yeniden kullanamazsınız.

### <a name="parameters"></a>Parametreler

- Daha önce oluşturulan dizi.
- Dizide depolanacak ham değer.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Daha önce oluşturulan diziye yeni bir ham değer ekler. Bellek taşınır, bu nedenle dikkatli olun, bu değeri yeniden kullanamazsınız.

### <a name="parameters"></a>Parametreler

- Daha önce oluşturulan dizi.
- Dizide depolanacak ham değer.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Arabelleğin en az X bayt içerdiğini denetler.

### <a name="parameters"></a>Parametreler

Mevcut olması gereken bayt sayısı.

### <a name="return-value"></a>Döndürülen değer

Arabellek en az X bayt içeriyorsa doğru. Aksi takdirde, `False` .

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Bayt sırasına göre arabelleğin Uint8 değerini (1 bayt) okur.

### <a name="return-value"></a>Döndürülen değer

Arabellekten okunan değer.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Bayt sırasına göre arabelleğinden uo değeri (2 bayt) okur.

### <a name="return-value"></a>Döndürülen değer

Arabellekten okunan değer.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Bayt sırasına göre arabellekteki uint32 değerini (4 bayt) okur.

### <a name="return-value"></a>Döndürülen değer

Arabellekten okunan değer.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Bayt sırasına göre ara değerden uint64 değerini (8 bayt) okur.

### <a name="return-value"></a>Döndürülen değer

Arabellekten okunan değer.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Önceden ayrılmış belleğe, belirtilen boyutta okur, aslında verileri bellek bölgenize kopyalayacaktır.

### <a name="parameters"></a>Parametreler 

- Verilerin kopyalanacağı bellek bölgesi.
- Bu parametre, bellek bölgesinin boyutu için kaç bayt kopyalanacağını de tanımlamış.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Arabellekteki bir dizeye okur.

### <a name="parameters"></a>Parametreler 

- Okunması gereken bayt sayısı.

### <a name="return-value"></a>Döndürülen değer

Dizenin bellek bölgesine başvuru.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Arabellekte kaç Byte kaldığını belirtir.

### <a name="return-value"></a>Döndürülen değer

Arabelleğin kalan boyutu.

## `void horizon::general::IDataBuffer::skip(size_t)`

Arabellekte X baytı atlar.

### <a name="parameters"></a>Parametreler

- Atlanacak bayt sayısı.
