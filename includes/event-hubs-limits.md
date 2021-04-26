---
Başlık: içerme dosya açıklaması: içerme dosya hizmetleri: Olay-Hub yazarı: spelluru MS. Service: Event-hub MS. Topic: MS. Date: 03/31/2021 MS. Author: spelluru MS. Custom: "içerme dosyası", "FastTrack-Edit", "IoT", "Event-Hub"

---

Aşağıdaki tablolarda [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)'a özgü kotalar ve sınırlamalar sağlanmaktadır. Event Hubs fiyatlandırması hakkında daha fazla bilgi için bkz. [Event Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Tüm katmanlar için ortak sınırlar
Aşağıdaki sınırlar tüm katmanlarda ortaktır. 

| Sınır |  Notlar | Değer |
| --- |  --- | --- |
| Abonelik başına Event Hubs ad alanı sayısı |- |100 |
| Ad alanı başına Olay Hub 'ları sayısı | Yeni bir olay hub 'ı oluşturmaya yönelik sonraki istekler reddedilir. |10 |
| Bir olay hub 'ı adının boyutu |- | 256 karakter |
| Tüketici grubu adının boyutu | Kafka protokolü bir tüketici grubu oluşturulmasını gerektirmez. | <p>Kafka: 256 karakter</p><p>AMQP: 50 karakter |
| Tüketici grubu başına dönem olmayan alıcıların sayısı |- |5 |
| Ad alanı başına yetkilendirme kuralı sayısı | Yetkilendirme kuralı oluşturma için sonraki istekler reddedilir.|12 |
| GetRuntimeInformation metoduna yapılan çağrıların sayısı |  - | saniyede 50 | 
| Sanal ağ sayısı (VNet) | - | 128 | 
| IP yapılandırma kuralları sayısı | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Temel ve Standart katmanlar
Aşağıdaki tabloda, temel ve Standart katmanlar için farklı olabilecek sınırlar gösterilmektedir. 

| Sınır | Notlar | Temel | Standart |
|---|---|--|---|
| Event Hubs yayınının en büyük boyutu| &nbsp; | 256 KB | 1 MB |
| Olay Hub 'ı başına Tüketici grubu sayısı | &nbsp; |1 |20 |
| Ad alanı başına AMQP bağlantısı sayısı | Daha sonraki ek bağlantı istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. |100 |5.000|
| Olay verilerinin maksimum bekletme süresi | &nbsp; |1 gün |1-7 gün |
| En fazla üretilen iş birimi |Bu sınırın aşılması verilerinizin kısıtlanmasına neden olur ve [sunucu meşgul özel durumu](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)oluşturur. Standart katman için daha fazla sayıda üretilen iş birimi istemek üzere bir [destek isteği](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)dosyası. [Ek üretilen iş birimleri](../articles/event-hubs/event-hubs-auto-inflate.md) , taahhüt edilen satın alma esasına göre 20 blok halinde kullanılabilir. |20 | 20 | 
| Olay Hub 'ı başına bölüm sayısı | |32 | 32 | 

> [!NOTE]
>
> Olayları ayrı ayrı veya toplu şekilde yayımlayabilirsiniz. 
> Yayın sınırı (SKU 'ya göre), tek bir olay veya toplu iş olmasından bağımsız olarak geçerlidir. En büyük eşikten daha büyük yayımlama olayları reddedilir.

### <a name="dedicated-tier-vs-standard-tier"></a>Adanmış katman ve Standart katman karşılaştırması
Event Hubs Ayrılmış teklif, en az 4 saatlik kullanım ile sabit bir aylık fiyatla faturalandırılır. Adanmış katman, standart planın tüm özelliklerini sunar, ancak yoğun iş yükleri olan müşterilere yönelik kurumsal ölçekli kapasite ve sınırlara sahiptir. 

Azure portal kullanarak adanmış Event Hubs kümesi oluşturma hakkında bu [belgeye](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) başvurun.

| Özellik | Standart | Ayrılmış |
| --- |:---|:---|
| Bant genişliği | 20 s (40 'e kadar) | 20 cu düzeyinde kapsanır |
| Ad alanları |  Abonelik başına 100 | CU başına 50 (abonelik başına 100) |
| Event Hubs |  ad alanı başına 10 | ad alanı başına 1000 |
| Giriş olayları | Milyon olay başına ödeme | Dahil |
| İleti boyutu | 1.000.000 bayt | 1.000.000 bayt |
| Bölümler | Olay Hub 'ı başına 32 | Olay Hub 'ı başına 1024<br/>CU başına 2000 |
| Tüketici grupları | Olay Hub 'ı başına 20 | Her saniyedeki limit yok, Olay Hub 'ı başına 1000 |
| Aracılı bağlantılar | 1.000 dahil, en fazla 5.000 | 100 K dahil ve maks |
| İleti bekletme | 7 gün, TU başına 84 GB dahil | 90 gün, CU başına 10 TB dahildir |
| Yakalama | Saat başına ödeme | Dahil |


### <a name="schema-registry-limitations"></a>Şema kayıt defteri sınırlamaları

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Standart ve ayrılmış katmanlar için aynı olan sınırlar 
| Özellik | Sınır | 
|---|---|
| Şema grubu adının maksimum uzunluğu | 50 |  
| En fazla şema adı uzunluğu | 100 |    
| Şema başına bayt cinsinden boyut | 1 MB |   
| Şema grubu başına özellik sayısı | 1024 |
| Grup Özellik anahtarı başına bayt cinsinden boyut | 256 | 
| Grup Özellik değeri başına bayt cinsinden boyut | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Standart ve adanmış katmanlar için farklı sınırlar 

| Sınır | Standart | Ayrılmış | 
|---|---|--|
| Şema kayıt defterinin (ad alanı) mega bayt cinsinden boyutu | 25 |  1024 |
| Bir şema kayıt defterindeki veya ad alanındaki şema gruplarının sayısı | 1-varsayılan grup hariç | 1000 |
| Tüm şema gruplarındaki şema sürümlerinin sayısı | 25 | 10000 |