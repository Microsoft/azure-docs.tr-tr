---
title: CloudSimple-özel bulut izin modeli tarafından Azure VMware çözümü
description: CloudSimple özel bulut izin modelini, grupları ve kategorilerini açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e0904cc2fd9866a9f8354eac9815945043eca5c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987582"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple özel bulut izin modeli VMware vCenter

CloudSimple, özel bulut ortamına tam yönetici erişimini korur. Her CloudSimple müşterisine, ortamlarında sanal makineleri dağıtabilmek ve yönetmek için yeterli yönetim ayrıcalıklarına sahip olur.  Gerekirse, yönetim işlevleri gerçekleştirmek için ayrıcalıklarınızın geçici olarak ilerletiden olabilirsiniz.

## <a name="cloud-owner"></a>Bulut sahibi

Özel bir bulut oluştururken, özel buluttaki nesneleri yönetmek için **bulut sahibi rol** erişimi Ile vCenter çoklu oturum açma etki alanında bir **cloudowner** kullanıcısı oluşturulur. Bu Kullanıcı ayrıca ek [vCenter kimlik kaynakları](set-vcenter-identity.md)ve diğer kullanıcıları özel bulut vCenter 'a da ayarlayabilir.

> [!NOTE]
> CloudSimple özel bulut vCenter için varsayılan kullanıcı, özel bir bulut oluşturulduğunda cloudowner@cloudsimple.local.

## <a name="user-groups"></a>Kullanıcı Grupları

**Bulut sahibi grubu** adlı bir grup, özel bulutun dağıtımı sırasında oluşturulur. Bu gruptaki kullanıcılar, özel buluttaki vSphere ortamının çeşitli kısımlarını yönetebilir. Bu gruba **bulut sahibi rol** ayrıcalıkları otomatik olarak verilir ve **cloudowner** kullanıcısı bu grubun bir üyesi olarak eklenir.  CloudSimple, yönetim kolaylığı için sınırlı ayrıcalıklara sahip ek gruplar oluşturur.  Bu önceden oluşturulmuş gruplara herhangi bir kullanıcı ekleyebilirsiniz ve aşağıda tanımlanan ayrıcalıklar gruplardaki kullanıcılara otomatik olarak atanır.

### <a name="pre-created-groups"></a>Önceden oluşturulmuş gruplar

| Grup adı | Amaç | Rol |
| -------- | ------- | ------ |
| Bulut sahibi grubu | Bu grubun üyeleri özel bulut vCenter üzerinde yönetici ayrıcalıklarına sahiptir | [Bulut sahibi-rol](#cloud-owner-role) |
| Bulut-genel-küme-Yönetici-Grup | Bu grubun üyeleri, özel bulut vCenter kümesinde yönetim ayrıcalıklarına sahiptir | [Bulut-küme-yönetici-rol](#cloud-cluster-admin-role) |
| Bulut-genel-depolama-Yönetici-Grup | Bu grubun üyeleri, özel bulut vCenter üzerinde depolamayı yönetebilir | [Bulut-depolama-yönetici-rol](#cloud-storage-admin-role) |
| Bulut-genel-ağ-yönetici-Grup | Bu grubun üyeleri, özel bulut vCenter üzerinde ağ ve dağıtılmış bağlantı noktası gruplarını yönetebilir | [Bulut-ağ-yönetici-rol](#cloud-network-admin-role) |
| Bulut-genel-VM-Yönetici-Grup | Bu grubun üyeleri, özel bulut vCenter üzerinde sanal makineleri yönetebilir | [Bulut-VM-yönetici-rol](#cloud-vm-admin-role) |

Bireysel kullanıcılara özel bulutu yönetme izinleri vermek için, Kullanıcı hesapları oluşturun ve uygun gruplara ekleyin.

> [!CAUTION]
> Yeni kullanıcılar yalnızca *bulut sahibi grubu*, *bulut-genel-küme-yönetici-grubu*, *bulut-genel-depolama-yönetici-grubu*, bulut-genel- *Ağ-Yönetici-Grup* veya *bulut-genel-VM-yönetici grubu*için eklenmelidir.  *Yöneticiler* grubuna eklenen kullanıcılar otomatik olarak kaldırılacaktır.  Yalnızca hizmet hesaplarının *Yöneticiler* grubuna eklenmesi gerekir.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Varsayılan roller için vCenter ayrıcalıklarının listesi

### <a name="cloud-owner-role"></a>Bulut sahibi-rol

| **Kategori** | **Ayrıcalıklar** |
|----------|-----------|
| **Alarmlar** | Uyarıyı Onayla <br> Alarm oluştur <br> Alarm eylemini devre dışı bırak <br> Uyarıyı Değiştir <br> Alarmı kaldır <br> Alarm durumunu ayarla |
| **İzinler** | Değiştirme izni |
| **İçerik Kitaplığı** | Kitaplık öğesi Ekle <br> Yerel Kitaplık oluştur <br> Abone olunan Kitaplık oluştur <br> Kitaplık öğesini Sil <br> Yerel kitaplığı Sil <br> Abone olunan kitaplığı Sil <br> Dosyaları indirme <br> Kitaplık öğesini çıkar <br> Abone olunan kitaplığı çıkar <br> Depolama alanını içeri aktar <br> Araştırma aboneliği bilgileri <br> Depolama alanını oku <br> Kitaplık öğesini Eşitle <br> Abone olunan kitaplığı Eşitle <br> İç denetim yazın <br> Yapılandırma ayarlarını Güncelleştir <br> Güncelleştirme dosyaları <br> Kitaplığı Güncelleştir <br> Kitaplık öğesini Güncelleştir <br> Yerel kitaplığı Güncelleştir <br> Abone olunan kitaplığı Güncelleştir <br> Yapılandırma ayarlarını görüntüle |
| **Şifreleme işlemleri** | Disk Ekle <br> Kopyalama <br> Şifre Çözme <br> Doğrudan erişim <br> Şifreleme <br> Yeni şifreleme <br> KMS 'yi yönetme <br> Şifreleme ilkelerini yönetme <br> Anahtarları yönetme <br> Geçiş <br> Yeniden Crypto <br> VM 'yi kaydetme <br> Konağı Kaydet |
| **dvPort grubu** | Oluşturma <br> Sil <br> Değiştir <br> İlke işlemi <br> Kapsam işlemi |
| **Hatayla** | Alan ayır <br> Veri deposuna gözatamıyorum <br> Veri deposunu yapılandırma <br> Alt düzey dosya işlemleri <br> Veri deposunu taşı <br> Veri deposunu kaldır <br> Dosyayı kaldır <br> Veri deposunu yeniden adlandır <br> Sanal makine dosyalarını Güncelleştir <br> Sanal makine meta verilerini Güncelleştir |
| **ESX Aracısı Yöneticisi** | Config <br> Değiştir <br> Görünüm |
| **Uzantının** | Uzantıyı Kaydet <br> Uzantı kaydını sil <br> Uzantıyı Güncelleştir |
| **Dış istatistik sağlayıcısı**| Kaydolma <br> Kaydı Kaldır <br> Güncelleştirme |
| **Klasör** | Klasör oluştur <br> Klasörü Sil <br> Klasörü taşı <br> Klasörü yeniden adlandır |
| **Genel** | Görevi iptal et <br> Kapasite planlaması <br> Tanılama <br> Yöntemi devre dışı bırak <br> Yöntemleri etkinleştir <br> Genel etiket <br> Durum <br> Lisanslarının <br> Olayı günlüğe kaydet <br> Özel öznitelikleri Yönet <br> Ara sunucu <br> Betik eylemi <br> Hizmet yöneticileri <br> Özel öznitelik ayarla <br> Sistem etiketi |
| **Sistem durumu güncelleştirme sağlayıcısı** | Kaydolma <br> Kaydı Kaldır <br> Güncelleştirme |
| **Ana bilgisayar > yapılandırması** | Depolama bölümü yapılandırması |
| **> Envanteri barındırma** | Kümeyi değiştirme |
| **vSphere etiketleme** | VSphere etiketi atama veya atamasını kaldırma <br> VSphere etiketi oluştur <br> VSphere etiket kategorisi oluştur <br> VSphere etiketini Sil <br> VSphere etiket kategorisini Sil <br> VSphere etiketini Düzenle <br> VSphere etiket kategorisini Düzenle <br> Kategori Için UsedBy alanını değiştirme <br> Etiket Için UsedBy alanını değiştirme |
| **Ağ** | Ağ ata <br> Yapılandırma <br> Ağı taşı <br> Kaldır |
| **Performans** | Aralıkları Değiştir |
| **Konak profili** | Görünüm |
| **Kaynak** | Öneriyi Uygula <br> Sanal uygulamayı kaynak havuzuna ata <br> Sanal makineyi kaynak havuzuna ata <br> Kaynak havuzu oluştur <br> Sanal makineyi kapalı geçir <br> Sanal makinede güç geçişi gerçekleştir <br> Kaynak havuzunu Değiştir <br> Kaynak havuzunu taşı <br> VMotion sorgula <br> Kaynak havuzunu kaldır <br> Kaynak havuzunu yeniden adlandır |
| **Zamanlanmış görev** | Görev oluşturma <br> Görevi Değiştir <br> Görevi kaldır <br> Görevi çalıştır |
| **Bağlanabilecek** | Kullanıcının kimliğine bürün <br> İleti <br> Oturumu doğrula <br> Oturumları görüntüle ve durdur |
| **Veri deposu kümesi** | Veri deposu kümesi yapılandırma |
| **Profil temelli depolama** | Profil temelli depolama güncelleştirmesi <br> Profil temelli depolama alanı görünümü |
| **Depolama görünümleri** | Hizmeti Yapılandır <br> Görünüm |
| **Görevler** | Görev Oluştur <br> Güncelleştirme görevi |
| **Aktarım Hizmeti**| Yönet <br> İzleme |
| **vApp** | Sanal makine Ekle <br> Kaynak havuzu ata <br> VApp atama <br> Kopyalama <br> Oluşturma <br> Sil <br> Dışarı Aktarma <br> İçeri Aktarma <br> Taşı <br> Gücü kapat <br> Açma <br> Yeniden Adlandır <br> Almayı <br> Kaydı Kaldır <br> OVF ortamını görüntüle <br> vApp uygulama yapılandırması <br> vApp örneği yapılandırması <br> yapılandırmaya göre vApp Managed <br> vApp kaynak yapılandırması |
| **VRMPolicy** | VRMPolicy sorgula <br> VRMPolicy 'yi Güncelleştir |
| **Sanal makine > yapılandırması** | Mevcut diski Ekle <br> Yeni Disk Ekle <br> Cihaz Ekle veya Kaldır <br> Gelişmiş <br> Değişiklik CPU sayısı <br> Kaynağı değiştir <br> ManagedBy 'yi yapılandırma <br> Disk değişiklik izleme <br> Disk kirası <br> Bağlantı ayarlarını görüntüle <br> Sanal diski Genişlet <br> Konak USB cihazı <br> Bellek <br> Cihaz ayarlarını değiştir <br> Sorgu hatası toleransı uyumluluğu <br> Sahip olunan dosyaları sorgula <br> Ham cihaz <br> Yoldan yeniden yükle <br> Diski Kaldır <br> Yeniden Adlandır <br> Konuk bilgilerini Sıfırla <br> Ek açıklamayı ayarla <br> Ayarlar <br> Swapfile yerleşimi <br> Çatal üst öğesini aç <br> Sanal makinenin kilidini aç <br> Sanal makine uyumluluğunu yükselt |
| **Sanal makine > Konuk işlemleri** | Konuk işlemi diğer adı değişikliği <br> Konuk işlemi diğer adı sorgusu <br> Konuk işlemi değişiklikleri <br> Konuk işlemi program yürütme <br> Konuk işlem sorguları |
| **Sanal makine > etkileşimi** | Soru cevap <br> Sanal makinede yedekleme işlemi <br> CD medyasını yapılandırma <br> Disket ortamını yapılandırma <br> Konsol etkileşimi <br> Ekran görüntüsü oluşturma <br> Tüm diskleri birleştirin <br> Cihaz bağlantısı <br> Sürükleyip bırakma <br> VIX API tarafından Konuk işletim sistemi yönetimi <br> USB HID tarama kodları Ekle <br> Duraklatma veya duraklamayı kaldırma <br> Temizleme veya küçültme işlemleri gerçekleştirme <br> Gücü kapat <br> Açma <br> Sanal makinede oturum Kaydet <br> Sanal makinede oturumu yeniden Yürüt <br> Sıfırla <br> Hata toleransını sürdürür <br> Almayı <br> Askıya alma hatası toleransı <br> Yük devretme testi <br> Test yeniden başlatma Ikincil VM <br> Hata toleransını devre dışı bırakma <br> Hata toleransını aç <br> VMware araçları yüklemesi |
| **Sanal makine > envanteri** | Mevcut kaynaktan oluştur <br> Yeni oluştur <br> Taşı <br> Kaydolma <br> Kaldır <br> Kaydı Kaldır |
| **Sanal makine > sağlama** | Disk erişimine izin ver <br> Dosya erişimine izin ver <br> Salt okuma disk erişimine izin ver <br> Sanal makine indirmeye izin ver <br> Sanal makine dosyalarının karşıya yüklenmesine izin ver <br> Şablonu kopyala <br> Sanal makineyi Kopyala <br> Sanal makineden Şablon Oluştur <br> Özelleştirme <br> Şablon dağıtma <br> Şablon olarak işaretle <br> Sanal makine olarak işaretle <br> Özelleştirme belirtimini değiştirme <br> Diskleri yükselt <br> Özelleştirme belirtimlerini oku |
| **Sanal makine > hizmeti yapılandırması** | Bildirimlere izin ver <br> Genel olay bildirimlerinin yoklanmaya izin ver <br> Hizmet yapılandırmasını yönetme <br> Hizmet yapılandırmasını değiştirme <br> Sorgu hizmeti yapılandırması <br> Hizmet yapılandırmasını oku |
| **Sanal makine > anlık görüntü yönetimi** | Anlık görüntü oluşturma <br> Anlık görüntüyü kaldır <br> Anlık görüntüyü yeniden adlandır <br> Anlık görüntüye dön |
| **Sanal makine > vSphere çoğaltması** | Çoğaltmayı yapılandırma <br> Çoğaltmayı yönetme <br> Çoğaltmayı izleme |
| **vService** | Bağımlılık oluştur <br> Bağımlılığı yok et <br> Bağımlılık yapılandırmasını yeniden yapılandırın <br> Güncelleştirme bağımlılığı |

### <a name="cloud-cluster-admin-role"></a>Bulut-küme-yönetici-rol

| **Kategori** | **Ayrıcalıklar** |
|----------|-----------|
| **Hatayla** | Alan ayır <br> Veri deposuna gözatamıyorum <br> Veri deposunu yapılandırma <br> Alt düzey dosya işlemleri <br> Veri deposunu kaldır <br> Veri deposunu yeniden adlandır <br> Sanal makine dosyalarını Güncelleştir <br> Sanal makine meta verilerini Güncelleştir |
| **Klasör** | Klasör oluştur <br> Klasörü Sil <br> Klasörü taşı <br> Klasörü yeniden adlandır |
| **Ana bilgisayar > yapılandırması**  | Depolama bölümü yapılandırması |
| **vSphere etiketleme** | VSphere etiketi atama veya atamasını kaldırma <br> VSphere etiketi oluştur <br> VSphere etiket kategorisi oluştur <br> VSphere etiketini Sil <br> VSphere etiket kategorisini Sil <br> VSphere etiketini Düzenle <br> VSphere etiket kategorisini Düzenle <br> Kategori Için UsedBy alanını değiştirme <br> Etiket Için UsedBy alanını değiştirme |
| **Ağ** | Ağ ata |
| **Kaynak** | Öneriyi Uygula <br> Sanal uygulamayı kaynak havuzuna ata <br> Sanal makineyi kaynak havuzuna ata <br> Kaynak havuzu oluştur <br> Sanal makineyi kapalı geçir <br> Sanal makinede güç geçişi gerçekleştir <br> Kaynak havuzunu Değiştir <br> Kaynak havuzunu taşı <br> VMotion sorgula <br> Kaynak havuzunu kaldır <br> Kaynak havuzunu yeniden adlandır |
| **vApp** | Sanal makine Ekle <br> Kaynak havuzu ata <br> VApp atama <br> Kopyalama <br> Oluşturma <br> Sil <br> Dışarı Aktarma <br> İçeri Aktarma <br> Taşı <br> Gücü kapat <br> Açma <br> Yeniden Adlandır <br> Almayı <br> Kaydı Kaldır <br> OVF ortamını görüntüle <br> vApp uygulama yapılandırması <br> vApp örneği yapılandırması <br> yapılandırmaya göre vApp Managed <br> vApp kaynak yapılandırması |
| **VRMPolicy** | VRMPolicy sorgula <br> VRMPolicy 'yi Güncelleştir |
| **Sanal makine > yapılandırması** | Mevcut diski Ekle <br> Yeni Disk Ekle <br> Cihaz Ekle veya Kaldır <br> Gelişmiş <br> Değişiklik CPU sayısı <br> Kaynağı değiştir <br> ManagedBy 'yi yapılandırma <br> Disk değişiklik izleme <br> Disk kirası <br> Bağlantı ayarlarını görüntüle <br> Sanal diski Genişlet <br> Konak USB cihazı <br> Bellek <br> Cihaz ayarlarını değiştir <br> Sorgu hatası toleransı uyumluluğu <br> Sahip olunan dosyaları sorgula <br> Ham cihaz <br> Yoldan yeniden yükle <br> Diski Kaldır <br> Yeniden Adlandır <br> Konuk bilgilerini Sıfırla <br> Ek açıklamayı ayarla <br> Ayarlar <br> Swapfile yerleşimi <br> Çatal üst öğesini aç <br> Sanal makinenin kilidini aç <br> Sanal makine uyumluluğunu yükselt |
| **Sanal makine > Konuk işlemleri** | Konuk işlemi diğer adı değişikliği <br> Konuk işlemi diğer adı sorgusu <br> Konuk işlemi değişiklikleri <br> Konuk işlemi program yürütme <br> Konuk işlem sorguları |
| **Sanal makine > etkileşimi** | Soru cevap <br> Sanal makinede yedekleme işlemi <br> CD medyasını yapılandırma <br> Disket ortamını yapılandırma <br> Konsol etkileşimi <br> Ekran görüntüsü oluşturma <br> Tüm diskleri birleştirin <br> Cihaz bağlantısı <br> Sürükleyip bırakma <br> VIX API tarafından Konuk işletim sistemi yönetimi <br> USB HID tarama kodları Ekle <br> Duraklatma veya duraklamayı kaldırma <br> Temizleme veya küçültme işlemleri gerçekleştirme <br> Gücü kapat <br> Açma <br> Sanal makinede oturum Kaydet <br> Sanal makinede oturumu yeniden Yürüt <br> Sıfırla <br> Hata toleransını sürdürür <br> Almayı <br> Askıya alma hatası toleransı <br> Yük devretme testi <br> Test yeniden başlatma Ikincil VM <br> Hata toleransını devre dışı bırakma <br> Hata toleransını aç <br> VMware araçları yüklemesi
| **Sanal makine > envanteri** | Mevcut kaynaktan oluştur <br> Yeni oluştur <br> Taşı <br> Kaydolma <br> Kaldır <br> Kaydı Kaldır |
| **Sanal makine > sağlama** | Disk erişimine izin ver <br> Dosya erişimine izin ver <br> Salt okuma disk erişimine izin ver <br> Sanal makine indirmeye izin ver <br> Sanal makine dosyalarının karşıya yüklenmesine izin ver <br> Şablonu kopyala <br> Sanal makineyi Kopyala <br> Sanal makineden Şablon Oluştur <br> Özelleştirme <br> Şablon dağıtma <br> Şablon olarak işaretle <br> Sanal makine olarak işaretle <br> Özelleştirme belirtimini değiştirme <br> Diskleri yükselt  <br> Özelleştirme belirtimlerini oku |
| **Sanal makine > hizmeti yapılandırması** | Bildirimlere izin ver <br> Genel olay bildirimlerinin yoklanmaya izin ver <br> Hizmet yapılandırmasını yönetme <br> Hizmet yapılandırmasını değiştirme <br> Sorgu hizmeti yapılandırması <br> Hizmet yapılandırmasını oku
| **Sanal makine > anlık görüntü yönetimi** | Anlık görüntü oluşturma <br> Anlık görüntüyü kaldır <br> Anlık görüntüyü yeniden adlandır <br> Anlık görüntüye dön |
| **Sanal makine > vSphere çoğaltması** | Çoğaltmayı yapılandırma <br> Çoğaltmayı yönetme <br> Çoğaltmayı izleme |
| **vService** | Bağımlılık oluştur <br> Bağımlılığı yok et <br> Bağımlılık yapılandırmasını yeniden yapılandırın <br> Güncelleştirme bağımlılığı |

### <a name="cloud-storage-admin-role"></a>Bulut-depolama-yönetici-rol

| **Kategori** | **Ayrıcalıklar** |
|----------|-----------|
| **Hatayla** | Alan ayır <br> Veri deposuna gözatamıyorum <br> Veri deposunu yapılandırma <br> Alt düzey dosya işlemleri <br> Veri deposunu kaldır <br> Veri deposunu yeniden adlandır <br> Sanal makine dosyalarını Güncelleştir <br> Sanal makine meta verilerini Güncelleştir |
| **Ana bilgisayar > yapılandırması** | Depolama bölümü yapılandırması |
| **Veri deposu kümesi** | Veri deposu kümesi yapılandırma |
| **Profil temelli depolama** | Profil temelli depolama güncelleştirmesi <br> Profil temelli depolama alanı görünümü |
| **Depolama görünümleri** | Hizmeti Yapılandır <br> Görünüm |

### <a name="cloud-network-admin-role"></a>Bulut-ağ-yönetici-rol

| **Kategori** | **Ayrıcalıklar** |
|----------|-----------|
| **dvPort grubu** | Oluşturma <br> Sil <br> Değiştir <br> İlke işlemi <br> Kapsam işlemi |
| **Ağ** | Ağ ata <br> Yapılandırma <br> Ağı taşı <br> Kaldır |
| **Sanal makine > yapılandırması** | Cihaz ayarlarını değiştir |

### <a name="cloud-vm-admin-role"></a>Bulut-VM-yönetici-rol

| **Kategori** | **Ayrıcalıklar** |
|----------|-----------|
| **Hatayla** | Alan ayır <br> Veri deposuna gözatamıyorum |
| **Ağ** | Ağ ata |
| **Kaynak** | Sanal makineyi kaynak havuzuna ata <br> Sanal makineyi kapalı geçir <br> Sanal makinede güç geçişi gerçekleştir
| **vApp** | Dışarı Aktarma <br> İçeri Aktarma |
| **Sanal makine > yapılandırması** | Mevcut diski Ekle <br> Yeni Disk Ekle <br> Cihaz Ekle veya Kaldır <br> Gelişmiş <br> Değişiklik CPU sayısı <br> Kaynağı değiştir <br> ManagedBy 'yi yapılandırma <br> Disk değişiklik izleme <br> Disk kirası <br> Bağlantı ayarlarını görüntüle <br> Sanal diski Genişlet <br> Konak USB cihazı <br> Bellek <br> Cihaz ayarlarını değiştir <br> Sorgu hatası toleransı uyumluluğu <br> Sahip olunan dosyaları sorgula <br> Ham cihaz <br> Yoldan yeniden yükle <br> Diski Kaldır <br> Yeniden Adlandır <br> Konuk bilgilerini Sıfırla <br> Ek açıklamayı ayarla <br> Ayarlar <br> Swapfile yerleşimi <br> Çatal üst öğesini aç <br> Sanal makinenin kilidini aç <br> Sanal makine uyumluluğunu yükselt |
| **Sanal makine > Konuk işlemleri** | Konuk işlemi diğer adı değişikliği <br> Konuk işlemi diğer adı sorgusu <br> Konuk işlemi değişiklikleri <br> Konuk işlemi program yürütme <br> Konuk işlem sorguları    |
| **Sanal makine > etkileşimi** | Soru cevap <br> Sanal makinede yedekleme işlemi <br> CD medyasını yapılandırma <br> Disket ortamını yapılandırma <br> Konsol etkileşimi <br> Ekran görüntüsü oluşturma <br> Tüm diskleri birleştirin <br> Cihaz bağlantısı <br> Sürükleyip bırakma <br> VIX API tarafından Konuk işletim sistemi yönetimi <br> USB HID tarama kodları Ekle <br> Duraklatma veya duraklamayı kaldırma <br> Temizleme veya küçültme işlemleri gerçekleştirme <br> Gücü kapat <br> Açma <br> Sanal makinede oturum Kaydet <br> Sanal makinede oturumu yeniden Yürüt <br> Sıfırla <br> Hata toleransını sürdürür <br> Almayı <br> Askıya alma hatası toleransı <br> Yük devretme testi <br> Test yeniden başlatma Ikincil VM <br> Hata toleransını devre dışı bırakma <br> Hata toleransını aç <br> VMware araçları yüklemesi |
| **Sanal makine > envanteri** | Mevcut kaynaktan oluştur <br> Yeni oluştur <br> Taşı <br> Kaydolma <br> Kaldır <br> Kaydı Kaldır |
| **Sanal makine > sağlama** | Disk erişimine izin ver <br> Dosya erişimine izin ver <br> Salt okuma disk erişimine izin ver <br> Sanal makine indirmeye izin ver <br> Sanal makine dosyalarının karşıya yüklenmesine izin ver <br> Şablonu kopyala <br> Sanal makineyi Kopyala <br> Sanal makineden Şablon Oluştur <br> Özelleştirme <br> Şablon dağıtma <br> Şablon olarak işaretle <br> Sanal makine olarak işaretle <br> Özelleştirme belirtimini değiştirme <br> Diskleri yükselt <br> Özelleştirme belirtimlerini oku |
| **Sanal makine > hizmeti yapılandırması** | Bildirimlere izin ver <br> Genel olay bildirimlerinin yoklanmaya izin ver <br> Hizmet yapılandırmasını yönetme <br> Hizmet yapılandırmasını değiştirme <br> Sorgu hizmeti yapılandırması <br> Hizmet yapılandırmasını oku
| **Sanal makine > anlık görüntü yönetimi** | Anlık görüntü oluşturma <br> Anlık görüntüyü kaldır <br> Anlık görüntüyü yeniden adlandır <br> Anlık görüntüye dön |
| **Sanal makine > vSphere çoğaltması** | Çoğaltmayı yapılandırma <br> Çoğaltmayı yönetme <br> Çoğaltmayı izleme |
| **vService** | Bağımlılık oluştur <br> Bağımlılığı yok et <br> Bağımlılık yapılandırmasını yeniden yapılandırın <br> Güncelleştirme bağımlılığı |
