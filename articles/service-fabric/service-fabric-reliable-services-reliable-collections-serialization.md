---
title: Azure Service Fabric güvenilir koleksiyon nesnesi serileştirme | Microsoft Docs
description: Azure Service Fabric güvenilir koleksiyonlar nesne serileştirme
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: atsenthi
ms.openlocfilehash: d5e7dfb84f6e8a8fbd029ccc0b15c17f68216c33
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599297"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Azure Service Fabric güvenilir koleksiyon nesnesi serileştirmesi
Güvenilir koleksiyonlar, makine hatalarından ve güç kesintilerine dayanıklı olduklarından emin olmak için öğelerini çoğaltın ve kalıcı hale getirir.
Hem çoğaltmak hem de kalıcı hale getirmek için, güvenilir koleksiyonlar ' ı serileştirmeleri gerekir.

Güvenilir koleksiyonlar ' güvenilir durum yöneticisinden belirli bir tür için uygun serileştiriciyi al.
Güvenilir durum Yöneticisi yerleşik serileştiriciler içerir ve belirli bir tür için özel serileştiricilerin kaydedilmesini sağlar.

## <a name="built-in-serializers"></a>Yerleşik serileştiriciler

Güvenilir durum Yöneticisi bazı yaygın türler için yerleşik serileştirici içerir, böylece varsayılan olarak etkin hale getirilebilir. Diğer türler için güvenilir durum Yöneticisi [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)'ı kullanmaya geri döner.
Yerleşik serileştiriciler, türlerinin değişmediğini biledikleri ve tür adı gibi tür hakkındaki bilgileri içermesi gerekmediğinden daha etkilidir.

Güvenilir durum Yöneticisi aşağıdaki türler için yerleşik serileştiriciye sahiptir: 
- Guid
- bool
- byte
- SByte
- byte[]
- char
- dize
- decimal
- double
- float
- int
- uint
- long
- 'tur
- kısadır
- ushort

## <a name="custom-serialization"></a>Özel serileştirme

Özel serileştiriciler, performansı artırmak veya verileri tel ve diskte şifrelemek için yaygın olarak kullanılır. Diğer nedenlerden dolayı, özel serileştiriciler, tür hakkındaki bilgileri serileştirmek zorunda olmadığından genel serileştiriciye göre daha etkilidir. 

[Ireliablestatemanager. tryaddstateserializer\<t >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) , verilen tür T için özel bir serileştirici kaydetmek üzere kullanılır. Kurtarma başlamadan önce, tüm güvenilir koleksiyonların kalıcı verileri okumak için ilgili seri hale getirici 'e erişimi olduğundan, bu kayıt, StatefulServiceBase 'in yapımını gerçekleşmelidir.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Özel serileştiricilerle yerleşik serileştiriciler üzerinden öncelik verilir. Örneğin, int için özel bir seri hale getirici kaydedildiğinde, int için yerleşik serileştirici yerine tamsayıları seri hale getirmek için kullanılır.

### <a name="how-to-implement-a-custom-serializer"></a>Özel seri hale getirici uygulama

Özel serileştirici, [istateserializer\<T >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) arabirimini uygulamalıdır.

> [!NOTE]
> İstateserializer\<T >, temel değer olarak adlandırılan ek bir T 'yi alan yazma ve okuma için bir aşırı yükleme içerir. Bu API, değişiklik serileştirme içindir. Şu anda değişiklik serileştirme özelliği kullanıma sunulmamaktadır. Bu nedenle, bu iki aşırı yükleme, fark serileştirme gösterilene ve etkinleştirilene kadar çağrılmaz.

Aşağıda dört özellik içeren OrderKey adlı örnek bir özel tür verilmiştir

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Aşağıda, istateserializer\<orderkey > örnek bir uygulamasıdır.
BaseValue içinde olan okuma ve yazma aşırı yüklemelerinin, ileten uyumluluk için ilgili aşırı yüklerini çağırdığına göz atın.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Yükseltilebilir maliyet
Bir sıralı [uygulama yükseltmesinde](service-fabric-application-upgrade.md), yükseltme bir tek seferde bir yükseltme etki alanı olmak üzere düğümlerin bir alt kümesine uygulanır. Bu işlem sırasında, bazı yükseltme etki alanları uygulamanızın daha yeni bir sürümünde olur ve bazı yükseltme etki alanları uygulamanızın eski sürümünde olur. Dağıtım sırasında, uygulamanızın yeni sürümü verilerinizin eski sürümünü okuyabilmelidir ve uygulamanızın eski sürümü verilerinizin yeni sürümünü okuyabilmelidir olması gerekir. Veri biçimi ileri ve geri uyumlu değilse, yükseltme başarısız olabilir veya daha kötü, veriler kaybolabilir veya bozulabilir.

Yerleşik serileştirici kullanıyorsanız, uyumluluk konusunda endişelenmeniz gerekmez.
Ancak, özel bir serileştirici veya DataContractSerializer kullanıyorsanız, verilerin sonsuza kadar geriye doğru ve uyumlu olması gerekir.
Diğer bir deyişle, serileştirici sürümlerinin her sürümü, türün herhangi bir sürümünü seri hale getirmek ve seri hale getirmek için gereklidir.

Veri sözleşmesi kullanıcıları, alanları eklemek, kaldırmak ve değiştirmek için iyi tanımlanmış sürüm oluşturma kurallarını izlemelidir. Veri anlaşması Ayrıca bilinmeyen alanlarla işleme, serileştirme ve seri durumdan çıkarma işlemine bağlama ve sınıf devralma ile ilgilenme desteği de içerir. Daha fazla bilgi için bkz. [veri sözleşmesini kullanma](https://msdn.microsoft.com/library/ms733127.aspx).

Özel seri hale getirici kullanıcıları, geriye doğru olduğundan ve uyumlu olduğundan emin olmak için kullandıkları seri hale getiricinin yönergelerine bağlı olmalıdır.
Tüm sürümleri desteklemeye ilişkin yaygın bir yöntem, başlangıçta boyut bilgilerini eklemektir ve yalnızca isteğe bağlı özellikler eklemektir.
Bu şekilde, her sürüm, akışın kalan kısmına göre okuyabileceği kadar okuyabilir.

## <a name="next-steps"></a>Sonraki adımlar
  * [Serileştirme ve yükseltme](service-fabric-application-upgrade-data-serialization.md)
  * [Güvenilir koleksiyonlar için geliştirici başvurusu](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.
  * [PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.
  * Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.
  * [Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.
  * Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.
