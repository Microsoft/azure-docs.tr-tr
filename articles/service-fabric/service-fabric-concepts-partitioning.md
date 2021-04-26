---
title: Service Fabric Hizmetleri bölümlendirme
description: Durum bilgisiz ve durum bilgisi olan hizmetlerin Service Fabric nasıl bölümleyeceğinizi öğrenin
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97915904"
---
# <a name="partition-service-fabric-reliable-services"></a>Partition Service Fabric güvenilir hizmetleri
Bu makalede, Azure Service Fabric güvenilir Hizmetleri bölümlemeye yönelik temel kavramlara giriş sunulmaktadır. Bölümlendirme, veri ve işlem birlikte ölçeklenebilmesi için yerel makinelerde veri depolamayı mümkün bir şekilde sunar.

> [!TIP]
> Bu makaledeki kodun [tamamen bir örneği](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) GitHub 'da bulunabilir.

## <a name="partitioning"></a>Bölümleme
Bölümlendirme Service Fabric için benzersiz değil. Aslında, ölçeklenebilir hizmetler oluşturmanın temel bir düzendir. Daha geniş bir anlamda, ölçeklenebilirliği ve performansı geliştirmek için durum (veri) ve işlem süresini daha küçük erişilebilir birimlere bölme kavramı olarak bölümleme hakkında fikir veririz. Bir bölümlemenin iyi bilinen bir biçimi, parçalı olarak da bilinen [veri bölümleniyor][wikipartition].

### <a name="partition-service-fabric-stateless-services"></a>Durum bilgisi olmayan Service Fabric hizmetleri bölümü
Durum bilgisi olmayan hizmetler için bir bölümün bir veya daha fazla hizmet örneğini içeren bir mantıksal birim olduğunu düşünebilirsiniz. Şekil 1 ' de bir bölüm kullanarak bir kümede dağıtılmış beş örneğe sahip, durum bilgisi olmayan bir hizmet gösterilmektedir.

![Durum bilgisi olmayan hizmet](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Aslında iki tür durum bilgisi olmayan hizmet çözümü vardır. Birincisi, örneğin Azure SQL veritabanı 'ndaki bir veritabanında (oturum bilgilerini ve verileri depolayan bir Web sitesi gibi) durumu dışarıdan devam eden bir hizmettir. İkincisi, kalıcı bir durumu yönetmeyen yalnızca hesaplama Hizmetleri (Hesaplayıcı veya Image thumbnailing gibi) olur.

Her iki durumda da, durum bilgisi olmayan bir hizmetin bölümlenmesi çok nadir bir senaryodur--ölçeklenebilirlik ve kullanılabilirlik normalde daha fazla örnek eklenerek elde edilir. Durum bilgisi olmayan hizmet örnekleri için birden çok bölümü düşünmek istediğiniz tek zaman, özel yönlendirme isteklerini karşılamanız gerekir.

Örnek olarak, belirli bir aralıktaki kimlikleri olan kullanıcıların yalnızca belirli bir hizmet örneği tarafından sunulması gereken bir durum düşünün. Durum bilgisi olmayan bir hizmetin bölümlenmesi için başka bir örnek de gerçek bir arka uca (örneğin, SQL veritabanı 'nda parçalı bir veritabanı) sahip olduğunuzda ve hangi hizmet örneğinin veritabanına yazılması gerektiğini denetlemek istediğinizde veya arka uçta kullanılan aynı bölümleme bilgilerini gerektiren durum bilgisiz hizmeti içinde başka bir hazırlık çalışması gerçekleştirmek istediğinizde. Bu tür senaryolar aynı zamanda farklı yollarla çözülebilirler ve hizmet bölümlendirme gerektirmez.

Bu izlenecek yolun geri kalanı durum bilgisi olan hizmetlere odaklanır.

### <a name="partition-service-fabric-stateful-services"></a>Durum bilgisi olan Service Fabric hizmetleri bölümü
Service Fabric, bölüm durumu (veri) için birinci sınıf bir yol sunarak ölçeklenebilir durum bilgisi olmayan hizmetler geliştirmeyi kolaylaştırır. Kavramsal olarak, bir kümedeki düğümlerde dağıtılan ve dengeli olan [çoğaltmalar](service-fabric-availability-services.md) aracılığıyla yüksek düzeyde güvenilir bir ölçek birimi olarak durum bilgisi olan bir hizmetin bir bölümünü düşünebilirsiniz.

Service Fabric durum bilgisi olan hizmetlerin bağlamında bölümlendirme, belirli bir hizmet bölümünün, hizmetin tüm durumunun bir kısmından sorumlu olduğunu belirleme sürecinden başvurur. (Daha önce bahsedildiği gibi, bölüm bir [çoğaltmalar](service-fabric-availability-services.md)kümesidir). Service Fabric hakkında harika bir şey, bölümleri farklı düğümlere yerleştirmeişlerdir. Bu, düğümün kaynak sınırına büyümesine izin verir. Verilerin büyümesi, bölümlerin büyümesi ve Service Fabric düğümler arasında bölümleri yeniden dengeleyebilmesi. Bu, donanım kaynaklarının sürekli verimli kullanılmasını sağlar.

Size bir örnek vermek için, 5 düğümlü bir küme ve 10 bölüm ve üç çoğaltma hedefi olacak şekilde yapılandırılmış bir hizmetle başladığınızı varsayalım. Bu durumda Service Fabric, çoğaltmaları küme genelinde dengeleyebilir ve dağıtacağından düğüm başına iki birincil [çoğaltma](service-fabric-availability-services.md) ile sona erdir olur.
Artık kümeyi 10 düğüme ölçeklendirmeniz gerekiyorsa Service Fabric tüm 10 düğümlerdeki birincil [çoğaltmaları](service-fabric-availability-services.md) yeniden dengeleyebilmesini sağlar. Benzer şekilde, 5 düğüme geri ölçeklendirdiyseniz, Service Fabric 5 düğümdeki tüm çoğaltmaları yeniden dengeleyebilecek.  

Şekil 2 ' de, küme ölçeklendirmeden önce ve sonra 10 bölümden oluşan dağılım gösterilmektedir.

![Durum bilgisi olan hizmet](./media/service-fabric-concepts-partitioning/partitions.png)

Sonuç olarak, istemcilerden gelen istekler bilgisayarlara dağıtıldığı için ölçek genişletme gerçekleştirilir, uygulamanın genel performansı geliştirilmiştir ve veri öbeklerine erişim çekişmesi azalır.

## <a name="plan-for-partitioning"></a>Bölümlendirme planı
Bir hizmeti uygulamadan önce, ölçeğini genişletmek için gereken bölümleme stratejisini her zaman göz önünde bulundurmanız gerekir. Farklı yollar vardır, ancak bunların hepsi uygulamanın ulaşmak için ihtiyaç duymasına odaklanmaktadır. Bu makalenin bağlamında, daha önemli yollardan bazılarını ele alalım.

İlk adım olarak bölümlenmesi gereken durum yapısını düşünmek iyi bir yaklaşımdır.

Daha basit bir örnek alalım. Ülke genelinde yoklama için bir hizmet oluşturuyorsanız, ilçe içindeki her şehir için bir bölüm oluşturabilirsiniz. Ardından, söz konusu şehre karşılık gelen bölümdeki her kişi için oyları depolayabilmeniz gerekir. Şekil 3 ' te bulunan bir dizi kişiyi ve şehri gösterir.

![Basit bölüm](./media/service-fabric-concepts-partitioning/cities.png)

Şehirlerin popülasyonu büyük ölçüde farklılık gösterdiğinden, çok fazla veri (örneğin, Seattle) ve çok az duruma sahip diğer bölümleri (örn. Kirkland) içeren bazı bölümlerle karşılaşabilirsiniz. Bu nedenle, eşit miktarlarda eyalet içeren bölümler olmasının etkisi nedir?

Örnek hakkında daha fazla düşünün, Seattle için oyları tutan bölümün Kirkland bir günden daha fazla trafik edindiğinin kolayca görebilirsiniz. Varsayılan olarak, Service Fabric her düğümde aynı sayıda birincil ve ikincil çoğaltma olduğundan emin olur. Böylece daha az trafiğe sahip daha fazla trafiğe ve diğerlerine yönelik çoğaltmaları tutan düğümleri kullanabilirsiniz. Tercihen bunun gibi sık ve soğuk noktalardır.

Bunu önlemek için, bir görünüm bölümleme noktasından iki şey yapmanız gerekir:

* Durumu tüm bölümler arasında eşit olarak dağıtılacak şekilde bölümlemeye çalışın.
* Hizmet için çoğaltmalardan her birinden rapor yükleme. (Nasıl yapılır hakkında bilgi edinmek için [ölçümler ve yük](service-fabric-cluster-resource-manager-metrics.md)hakkında bu makaleye göz atın). Service Fabric, hizmetler tarafından tüketilen, bellek miktarı veya kayıt sayısı gibi bir yükü rapor etmek için yetenek sağlar. Service Fabric, bildirilen ölçümlere göre, bazı bölümlerin diğerlerinden daha yüksek yüklere hizmet verdiğini ve çoğaltmaları daha uygun düğümlere taşıyarak kümeyi yeniden dengeleyip genel olmayan hiçbir düğüm aşırı yüklenmediğini algılar.

Bazen, belirli bir bölümde ne kadar veri olacağını bilemezsiniz. Bu nedenle, genel bir öneri, verileri bölümler arasında eşit olarak yayılan bir bölümleme stratejisi benimseerek, raporlama yüküne göre her ikisini de yapmanız gerekir.  İlk yöntem, oylama örneğinde açıklanan durumların yapılmasını önler, ikincisi Access veya zaman içinde yük olarak geçici farklılıkları düzgünleştirmenize yardımcı olur.

Bölüm planlamasının başka bir yönü de başlamak için doğru bölüm sayısını seçmelidir.
Service Fabric perspektifinden, senaryolarınız için beklenenden daha fazla sayıda bölümden başlayıp başlatmalarını engelleyen hiçbir şey yoktur.
Aslında, en fazla bölüm sayısı geçerli bir yaklaşım olarak kabul edilir.

Nadir durumlarda, başlangıçta seçtiğiniz kadar çok bölüm gerektirebilir. Olgu sonrasında bölüm sayısını değiştirememiş gibi, aynı hizmet türünde yeni bir hizmet örneği oluşturma gibi bazı gelişmiş bölüm yaklaşımları uygulamanız gerekir. Ayrıca, istemci kodunuzun bakımını yapması gereken istemci tarafı bilgisine bağlı olarak, istekleri doğru hizmet örneğine yönlendiren bazı istemci tarafı Logic de uygulamanız gerekir.

Bölümleme planlaması için başka bir göz önünde bulundurmanız gereken bilgisayar kaynaklarıdır. Duruma erişilmesi ve depolanması gerektiğinden, şu adımları izlemeniz gerekir:

* Ağ bant genişliği sınırları
* Sistem belleği sınırları
* Disk depolama sınırları

Bu nedenle, çalışan bir kümede kaynak kısıtlamalarında çalıştırırsanız ne olur? Yanıt, yeni gereksinimlere uyum sağlamak için kümeyi ölçekleyebilir.

[Kapasite Planlama Kılavuzu,](service-fabric-capacity-planning.md) kümenizin kaç düğüm için ihtiyaç duyduğunu belirleme Kılavuzu sunar.

## <a name="get-started-with-partitioning"></a>Bölümlendirme ile çalışmaya başlama
Bu bölümde, hizmetinizi bölümlemeye nasıl başlacağınız açıklanmaktadır.

Service Fabric üç bölüm şeması seçimi sunmaktadır:

* Ranşlı bölümlendirme (Aksi halde UniformInt64Partition olarak bilinir).
* Adlandırılmış bölümlendirme. Bu modeli kullanan uygulamalar genellikle sınırlı bir küme içinde, bir şekilde düzenlenmiş verilere sahiptir. Adlandırılmış bölüm anahtarları olarak kullanılan bazı yaygın veri alanı örnekleri, bölgeler, posta kodları, müşteri grupları veya diğer iş sınırları olacaktır.
* Tek bölümlendirme. Tek bölümler genellikle hizmet ek bir yönlendirme gerektirmezse kullanılır. Örneğin, durum bilgisi olmayan hizmetler varsayılan olarak bu bölümleme şemasını kullanır.

Adlandırılmış ve tekil bölümlendirme şemaları, yönetilen bölümlerin özel formlarıdır. Varsayılan olarak, Service Fabric için Visual Studio şablonları, en yaygın ve yararlı bir deyişle, sıralaştırılmış bölümlendirme kullanır. Bu makalenin geri kalanı, ranşlı bölümleme şemasına odaklanır.

### <a name="ranged-partitioning-scheme"></a>Ranşlı bölümleme düzeni
Bu, bir tamsayı aralığı (düşük anahtar ve yüksek anahtarla tanımlanır) ve bir dizi bölüm (n) belirtmek için kullanılır. Her biri, genel bölüm anahtar aralığının çakışmayan olmayan bir alt aralığından sorumlu n bölüm oluşturur. Örneğin, düşük anahtarı 0 olan bir ranşlı bölümleme şeması, yüksek bir 99 ve 4 sayısı, aşağıda gösterildiği gibi dört bölüm oluşturacaktır.

![Aralık bölümleme](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Yaygın bir yaklaşım, veri kümesi içindeki benzersiz bir anahtara göre bir karma oluşturmaktır. Bazı yaygın anahtar örnekleri bir araç tanımlama numarası (VIN), bir çalışan KIMLIĞI veya benzersiz bir dize olacaktır. Bu benzersiz anahtarı kullanarak, anahtarınız olarak kullanılacak bir karma kodu, anahtar aralığını mod olarak da oluşturabilirsiniz. İzin verilen anahtar aralığının üst ve alt sınırlarını belirtebilirsiniz.

### <a name="select-a-hash-algorithm"></a>Karma algoritma seçin
Karma algoritmanın önemli bir bölümü, karma algoritmanızı seçmektir. Bunun için, hedefin birbirlerine yakın benzer anahtarları (konum duyarlı karma) gruplamak ve etkinliğin tüm bölümler (dağıtım karması) genelinde büyük ölçüde dağıtılması (daha yaygın olan bir konudur) önemlidir.

İyi bir dağıtım karma algoritmasının özellikleri, kolayca işlem yapmak, birkaç çarpışmaya sahiptir ve anahtarları eşit bir şekilde dağıtır. Sağlam bir karma algoritmaya iyi bir örnek, [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) karma algoritmasıdır.

Genel karma kod algoritması seçimleri için iyi bir kaynak, [karma Işlevlerde Vikipedi sayfasıdır](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Birden çok bölümden oluşan bir durum bilgisi olan hizmet oluşturma
Birden çok bölümden oluşan ilk güvenilir durum bilgisi olan hizmetinizi oluşturalım. Bu örnekte, aynı bölümde aynı harfle başlayan tüm soyadlarını depolamak istediğiniz çok basit bir uygulama oluşturacaksınız.

Herhangi bir kod yazmadan önce, bölümler ve bölüm anahtarları hakkında düşünmeniz gerekir. 26 bölüme (alfabede her harf için bir tane) ihtiyacınız vardır, ancak düşük ve yüksek anahtarlar hakkında ne olacak?
Her harf için tek bir bölüme sahip olmak istediğimiz için, her bir harf kendi anahtarı olduğu için, düşük anahtar olarak 0 ve yüksek anahtar olarak 25 ' i kullanabiliriz.

> [!NOTE]
> Bu, gerçekte, dağıtımın düzensiz olması gibi basitleştirilmiş bir senaryodur. "S" veya "d" harfleriyle başlayan son adlar "X" veya "Y" ile başlayarak daha yaygın değerlerdir.
> 
> 

1. **Visual Studio**  >  **Dosya**  >  **Yeni**  >  **Proje**'yi açın.
2. **Yeni proje** iletişim kutusunda Service Fabric uygulamayı seçin.
3. "Harfler bölümler" projesini çağırın.
4. **Hizmet oluştur** Iletişim kutusunda **durum bilgisi olan** hizmet ' i seçin ve "alfabe. işleme" seçeneğini çağırın.
5. Bölüm sayısını ayarlayın. Aşağıdaki gösterildiği gibi,, Alfadisk bölümleri projesinin ApplicationPackageRoot klasöründe bulunan Applicationmanifest.xml dosyasını açın ve Processing_PartitionCount parametresini 26 olarak güncelleştirin.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Ayrıca, ApplicationManifest.xml StatefulService öğesinin LowKey ve HighKey özelliklerini aşağıda gösterildiği gibi güncelleştirmeniz gerekir.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Hizmetin erişilebilir olması için, aşağıda gösterildiği gibi alfabetik. Işleme hizmeti için ServiceManifest.xml (PackageRoot klasöründe bulunur) uç nokta öğesini ekleyerek bir bağlantı noktası üzerinde bir uç nokta açın:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Artık hizmet, 26 bölümden oluşan bir iç uç noktayı dinlemek üzere yapılandırılmıştır.
7. Ardından, `CreateServiceReplicaListeners()` işleme sınıfının yöntemini geçersiz kılmanız gerekir.
   
   > [!NOTE]
   > Bu örnek için, basit bir HttpCommunicationListener kullandığınızı varsaytık. Güvenilir hizmet iletişimi hakkında daha fazla bilgi için bkz. [güvenilir hizmet iletişim modeli](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Bir çoğaltmanın dinlediği URL için önerilen bir model aşağıdaki biçimdedir: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Bu nedenle, iletişim dinleyicinizi doğru uç noktaları ve bu düzene göre dinlemek için yapılandırmak istiyorsunuz.
   
    Bu hizmetin birden fazla çoğaltması aynı bilgisayarda barındırılıyor olabilir, bu nedenle bu adresin çoğaltma için benzersiz olması gerekir. Bu nedenle bölüm KIMLIĞI + çoğaltma KIMLIĞI URL 'de bulunur. HttpListener, URL öneki benzersiz olduğu sürece aynı bağlantı noktasında birden çok adresi dinleyebilir.
   
    Ek GUID, ikincil çoğaltmaların aynı zamanda salt okuma isteklerini dinleyeceği gelişmiş bir durum için geçerlidir. Bu durumda, istemcileri adresi yeniden çözümlemeye zorlamak için birincil sunucudan ikinciye geçiş yaparken yeni bir benzersiz adresin kullanıldığından emin olmak istersiniz. çoğaltmanın kullanılabilir tüm Konakları (IP, FQDN, localhost vb.) dinlediği için burada adres olarak ' + ' kullanılır. Aşağıdaki kod bir örnek gösterir.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Ayrıca, yayımlanan URL 'nin dinleme URL 'SI önekinden biraz farklı olduğunu da unutmayın.
    Dinleme URL 'SI HttpListener 'a verildi. Yayımlanan URL, hizmet keşfi için kullanılan Service Fabric Adlandırma Hizmeti yayımlanan URL 'dir. İstemciler, bu adresi bu bulma hizmeti üzerinden soracaktır. İstemcilerin aldığı adreste, bağlanabilmek için düğümün gerçek IP veya FQDN 'sine sahip olması gerekir. Bu nedenle, yukarıda gösterildiği gibi ' + ' değerini düğümün IP 'si veya FQDN ile değiştirmeniz gerekir.
9. Son adım, işleme mantığını aşağıda gösterildiği gibi hizmete eklemektir.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` bölümü çağırmak için kullanılan sorgu dizesi parametresinin değerlerini okur ve `AddUserAsync` soyadu güvenilir sözlüğe eklemek için çağırır `dictionary` .
10. Ayrıca, belirli bir bölümü nasıl çağırakullanabileceğinizi görmek için projeye durum bilgisi olmayan bir hizmet ekleyelim.
    
    Bu hizmet, LastName parametresini bir sorgu dizesi parametresi olarak kabul eden basit bir Web arabirimi olarak görev yapar, Bölüm anahtarını belirler ve işlemek için alfabetik. processing hizmetine gönderir.
11. **Hizmet oluştur** iletişim kutusunda, **durum bilgisi olmayan** hizmet ' i seçin ve aşağıda gösterildiği gibi "alfabe. Web" i çağırın.
    
    ![Durum bilgisi olmayan hizmet ekran görüntüsü](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Aşağıda gösterildiği gibi bir bağlantı noktasını açmak için alfabetik. WebApi hizmetinin ServiceManifest.xml uç nokta bilgilerini güncelleştirin.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Bir Serviceınstancelisteners koleksiyonunu Web sınıfına döndürgetirmeniz gerekir. Daha sonra, basit bir HttpCommunicationListener uygulamayı seçebilirsiniz.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Şimdi işlem mantığını uygulamanız gerekir. HttpCommunicationListener `ProcessInputRequest` bir istek geldiğinde çağırır. Şimdi aşağıdaki kodu ekleyin.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Adımla adım adım ilerlim. Kod, sorgu dizesi parametresinin ilk harfini char olarak okur `lastname` . Ardından, Bu harfin bölüm anahtarını, `A` son adların ilk harfinin onaltılık değerinden çıkararak onaltılık değerini çıkararak belirler.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Bu örnekte, bölüm başına bir bölüm anahtarı ile 26 bölüm kullandığınızı unutmayın.
    Daha sonra, `partition` Bu anahtarla ilgili hizmet bölümünü `ResolveAsync` nesne üzerindeki metodunu kullanarak elde ediyoruz `servicePartitionResolver` . `servicePartitionResolver` şöyle tanımlanır
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync`Yöntemi, HIZMET URI 'sini, Bölüm anahtarını ve bir iptal belirtecini parametre olarak alır. İşleme hizmeti için hizmet URI 'SI `fabric:/AlphabetPartitions/Processing` . Daha sonra, bölümün uç noktasını alırız.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Son olarak, uç nokta URL 'sini ve QueryString 'ı derleyip işleme hizmetini çağıracağız.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    İşlem tamamlandıktan sonra çıktıyı geri yazarız.
15. Son adım, hizmeti test etmek için kullanılır. Visual Studio yerel ve bulut dağıtımı için uygulama parametrelerini kullanır. Hizmeti 26 bölümden oluşan yerel olarak test etmek için, `Local.xml` aşağıda gösterildiği gibi, söz konusu dosyayı, aşağıdaki şekilde gösterilen şekilde, en alttaki bölüm projesinin ApplicationParameters klasöründen güncelleştirmeniz gerekir:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Dağıtımı tamamladıktan sonra, Service Fabric Explorer hizmeti ve tüm bölümlerini kontrol edebilirsiniz.
    
    ![Service Fabric Explorer ekran görüntüsü](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Bir tarayıcıda bölümlendirme mantığını girerek test edebilirsiniz `http://localhost:8081/?lastname=somename` . Aynı harfle başlayan her bir soyadı aynı bölümde depolanmakta olduğunu görürsünüz.
    
    ![Tarayıcı ekran görüntüsü](./media/service-fabric-concepts-partitioning/samplerunning.png)

Bu makalede kullanılan kodun tamamen çözümü şurada bulunabilir: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric hizmetleri hakkında daha fazla bilgi edinin:

* [Service Fabric ile bağlanma ve hizmetlerle iletişim kurma](service-fabric-connect-and-communicate-with-services.md)
* [Service Fabric hizmetlerinin kullanılabilirliği](service-fabric-availability-services.md)
* [Service Fabric Hizmetleri ölçeklenebilirliği](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
