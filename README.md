HybridNTierProject

.NET 8 üzerinde, Hybrid N-Tier mimarisiyle geliştirilmiş ASP.NET Core MVC uygulaması. Katmanlı mimari, Generic Repository deseni, 
Entity Framework Core (Code First) ve ASP.NET Core Identity kullanımını gösteren bir çalışmadır.

Kullanılan Teknolojiler
.NET 8 / ASP.NET Core MVC
Entity Framework Core (Code First, Migrations, Proxies)
ASP.NET Core Identity
Generic Repository Pattern
Bogus (örnek veri üretimi / seed)
X.PagedList.Mvc.Core (sayfalama)

Mimari

Çözüm, sorumlulukları ayıran katmanlardan oluşur. Katman isimleri Project. ön ekiyle başlar.

Katman	Sorumluluk
Project.ENTITIES	Varlık (entity) sınıfları
Project.CONF	Entity konfigürasyon sınıfları (Fluent API)
Project.DAL	DbContext, Repository sınıfları, Bogus ile seed veri uzantıları
Project.BLL	Manager (servis) sınıfları, Service Injection uzantıları (Context ve Identity kayıtları)
Project.COMMON	Katmanlar arası ortak kullanılan sınıflar
Project.COREMVC	ASP.NET Core MVC arayüz katmanı

Referans akışı: COREMVC → BLL → DAL → CONF → ENTITIES. .NET Core'da referanslar zincir halinde taşındığı için, 
örneğin DAL katmanına yalnızca CONF referansı vermek yeterlidir; ENTITIES referansı onun üzerinden gelir.

Tasarım Kararları ve Kodlama Standartları
Generic Repository deseni kullanılır. Başka bir tasarım deseni, mimariyle çakışmadığı sürece sonradan eklenebilir.
Yerel değişkenler camelCase, field'lar başında _ ile camelCase; property, metot ve sınıf isimleri PascalCase yazılır.
Kod içinde Türkçe karakter kullanılmaz.
Sınıf isimleri tekil olur (çoğul kullanılmaz).

Kurulum ve Çalıştırma

TODO: Aşağıdaki adımları kendi ortamına göre doğrula, eksik varsa tamamla (veritabanı türü, connection string vb.).

Repoyu klonla:
bash
   git clone https://github.com/gelenyusuf/HybridNTierProject.git
   cd HybridNTierProject
Project.COREMVC içindeki appsettings.json dosyasında connection string'i kendi veritabanına göre düzenle.
Migration oluşturmak ve veritabanını güncellemek için terminalde hedef proje Project.DAL, startup proje Project.COREMVC olmalıdır:
bash
   dotnet ef migrations add InitialCreate --project Project.DAL --startup-project Project.COREMVC
   dotnet ef database update --project Project.DAL --startup-project Project.COREMVC
Uygulamayı çalıştır:
bash
   dotnet run --project Project.COREMVC

Not: Startup projede (Project.COREMVC) Microsoft.EntityFrameworkCore.Design paketi yüklü değilse migration sırasında "Your startup project doesn't reference Microsoft.EntityFrameworkCore.Design" hatası alınır. Paketi UI katmanına ekleyerek çözülür.

Geliştirme Adımları (Özet)
ENTITIES: Gerekli kütüphaneler eklenir, varlık yapıları tasarlanır.
CONF: ENTITIES referansı verilir, konfigürasyon sınıfları tanımlanır.
DAL: CONF referansı verilir, EF Core Tools ve Proxies paketleri yüklenir, MyContext yazılır, Bogus ile extension sınıfları OnModelCreating içinde kullanılır, Repositories klasörü oluşturulur.
BLL: DAL referansı verilir, Context ve Identity servisleri ServiceInjections altında extension metotlarla UI'a açılır, Manager servisleri yazılır. Ardından migration çalıştırılır.
COREMVC: Sayfalama için X.PagedList ve X.PagedList.Mvc.Core paketleri eklenir.

