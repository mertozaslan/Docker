# Docker Compose
![](https://miro.medium.com/max/485/1*hxQhAq2bjDk_Cxctp1lEhA.png)

- Günlük olarak kullandığımız veya kullanılmak üzere müşterilerimize sunduğumuz ürünler ve sistemler birden fazla servisin (genellikle web sunucular, uygulama sunucuları, veri tabanı sunucuları, cache sunucuları, message queue sunucuları, vb) bir araya gelmesiyle oluşmaktadırlar. Orta ve büyük ölçekteki sistemleri Docker CLI kullanarak kullanıma sunmak ve bakımını yapmak pek makul değildir. Bu tip sistemlerde Container’ların çalıştırılması, durdurulması, birbirleri ile olan ilişkilerinin belirtilmesi yani basit bir şekilde yönetilebilmesi görevlerini yerine getirecek ayrı bir aracın varlığı gereklidir.
- Burada bahsedilen ihtiyaçları karşılamak için Docker Compose kullanılır fakat Docker Compose production için çok uygun bir araç değildir. Development aşaması için idealdir.
- Docker Compose, çoklu Docker uygulamalarını oluşturmak ve çalıştırmak için kullanılan bir araçtır. Docker Compose ile uygulamanın hizmetlerini yapılandırmak için bir YAML dosyası kullanılır.Ardından tek bir komut ile tüm hizmetleri yapılandırmaya başlar ve sistemi başlatırız. 
- docker-compose.yml dosyasında detayları verilen ve birbirleri ile ilişkileri tanımlanan Container’lar tek bir komut ile ayağa kaldırılıp tek bir komut ile durdurulur ve yine tek bir komut ile kaldırılabilirler .
- Kısacası Docker Compose bizlere çoklu container sistemlerini YAML dosyası olarak tanımlama imkanı sağlıyor.

### Docker Compose CLI
- Docker Compose ile ilgili komutlar verilmeden önce oluşturulan yml dosyası ile aynı klasörde olunması gerekir.
- **`docker-compose up`** -> aynı dizindeki docker-compose.yml dosyasını kullanarak sistemi başlatır.
  - Komut Nasıl Çalıştı
    - Docker Compose aracı bizim komutu çalıştırdığımız klasöre baktı.
    - Burada bulunan docker-compose.yml adındaki dosyanın içeriğini işlemeye başlar.
    - Docker docker-compose.yml okuduktan sonra oluşturulan objelerin mevcut sistem ile çakışmasın diye docker-compose.yml dosyasının bulunduğu klasör adıyla başlayan isimlendirme yapar
- **`docker-compose down`** -> tüm servisleri (docker objeleri) kapatır ve siler.
- **`docker-compose ps`** -> İlgili Compose dosyasından oluşturulan Container’ları listeler.
- **`docker-compose images`** -> servislerin hangi imagelerle oluştuğunu gösterir.
- **`docker-compose exec`** -> compose ile oluşturduğumuz servisin içinde komut çalıştırır.
- Benzer şekilde `docker-compose --help` ile diğer komutları görebiliriz ve bunlar `docker --help` ile benzer komutlardır.

### Docker Compose Yaml Dosyası
- Oluşturulacak docker-compose.yml dosyasında 4 tane top-level veri bloğu bulunmaktadır.
  - **Version** : Hangi Docker-Compose versiyonunu kullanılacağı belirtilir.
  - **Services** : Servislerin tanımlandığı veri bloğudur. Her bir servis tanımı, kendisinden çalıştırılan Container’ların detaylarını belirler.
  - **Volumes** : Volumelerin tanımlandığı yerdir.
  - **Networks** : Networklerin tanımlandığı yerdir.
### Services Veri Bloğu altında kullanılabilecek Konfigürasyonlar 
- **image** -> Service’ten oluşturulacak Container’ların başlatılacağı Image’ı belirler. DockerHub’dan çekilecek Image’ın ismini belirler.
- **build** -> İlgili Service’in hangi Dockerfile’a göre build edileceği bu Tag ile belirtilir. 
- **command** -> İlgili servis için kullanılan Image’ın sağladığı komuttan farklı bir komut kullanılmak istendiğinde kullanılabilir.
- **container_name** -> Bu konfigürasyon opsiyonu kullanılarak Service tanımından oluşturulacak Container’ın isimleri otomatik oluşturmak yerine özel olarak belirtilebilir.
- **depends_on** -> Oldukça önemli bir konfigürasyondur. Birden fazla Service tanımının olduğu Compose dosyalarında servisleri birbirlerine olan bağımlılıklarına göre sırayla başlatır. Compose öncelikle ilgili Service’in bağımlı olduğu Service’leri ve sonra ilgili Service’i başlatır. Örneğin oluşturduğumuz docker-compose.yml dosyasında hem WORDPRESS hemde MYSQL image'lerinden servisler oluşturulsun depends_on konfigürasyonu kullanılarak öncelikle MYSQL image'inden oluşan container'ların ayağa kalkmasını sağlayabiliriz.
- **environment** -> Service’ten oluşturulacak Container’lara yeni Environment Variable’lar bu opsiyon ile eklenebilir.
- **expose** -> Host’a açmadan Container’lar arasında port’ları açmak için kullanılır. Dockerfile’daki EXPOSE komutu ile aynı işe yarar.
- **ports** -> Service’ten oluşturulacak Container’larının portlarını belirler.

### Örnek 

- Öncelikle berlirlediğimiz klasörün içerisine iki adet "index.html" dosyası oluşturalım ve bunların içerisine sırayla
  - `<h1>Welcome web1</h1>`
  - `<h1>Welcome web2</h1>`
- Yazalım ve kaydedelim.
- Ardından “ docker-compose.yml “ dosyasını oluşturalım. 

 ```
version: '3'
services:
    website1:
        image: nginx
        ports:
        - "82:80"
        volumes:
        - ./web1:/usr/share/nginx/html
    website2:
        image: nginx
        ports:
        - "83:80"
        volumes:
        - ./web2:/usr/share/nginx/html
    database:
        image: mysql
        restart: always
        environment:
            - MYSQL_DATABASE=veri_tabanı_adı
            - MYSQL_ROOT_PASSWORD=root_sifresi
            - MYSQL_USER=mysql_kullanicisi
            - MYSQL_PASSWORD=mysql_kullanici_sifresi
        ports:
            - "8989:3306"
```
- `docker-compose up -d` komutu ile servislerimizi ayağa kaldıralım.
- Tarayıcıdan http://localhost:82 ve http://localhost:83 adreslerine gittiğimizde .

![web1](https://user-images.githubusercontent.com/98760765/182806460-c913b715-9569-4b3a-8bd1-c85a63055da1.png)

![web2](https://user-images.githubusercontent.com/98760765/182806479-75cb1c0c-4524-4f73-a20b-d7fe305873e3.png)


- `docker-compose ps` komutunu girdiğimizde ise

![Ekran Görüntüsü (8)](https://user-images.githubusercontent.com/98760765/182806704-203cd3b3-9411-4594-8ecb-d00ae70905d5.png)

- **website1** , **website2** ve **database** servislerinin çalıştığını görürüz.
