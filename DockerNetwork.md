# Network
Containerlar ’ın birbiri arasında iletişim kurarken networklerden yararlanır. Docker ilk kurulumda varsayılan olarak 3 farklı ağ (network) tipi ile gelmektedir. Bunlar bridge, host, none şeklinde sıralanabilir. Varsayılan olarak kurulu gelmeyen overlay ve macvlan tipinde 2 tane daha tip mevcuttur.
- Bridge
    - Varsayılan driver'dır.
    - Docker containerlar ayağa kaldırılırken herhangi bir network ayarlaması yapılmadığı takdirde tüm containerlar default olan bridge networke dahil edilir.
- Host
    - Her sistemde host driver ile oluşturulmuş "Host" adında bir driver vardır.
    - Bu network'e bağlı container'da network izolasyonu olmaz. Oluşturuldukları sistemin ağ altyapısını aynen kullanırlar.
- None
    - Container'a hiç bir şekilde ağ bağlantısı olmasın istersek None driver kullanılır.
    - Bu ağ tipinin seçilmesi durumunda konteyner diğer konteynerlere ve dışarıya kapalı olur.
- Macvlan
    - Bu driver ile oluşturulan network objeleri ile container'lar fiziksel ağlara kendi mac adreslerine sahip birer fiziksel ağ adaptörüne sahipmişcesine bağlanabilir.
- Overlay
    - Ayrı hostlar üzerindeki container'ların aynı ağda çalışıyormuş gibi çalışması istendiği zaman Overlay network'ler kullanılır.

![](https://miro.medium.com/max/1170/0*ZhU_iUdneHzfW8k7)

- **`docker network --help `**  -> Network bileşeni ile kullanabileceğimiz komutları görebiliriz.
- Network kullanımı `docker network COMMAND` şeklindedir. 
- **`docker network ls`** -> Sistemdeki mevcut Network'leri listeler.
- **`docker network rm <NetworkName>`** -> ID'si verilen Network'ü siler.
- **`docker network prune`** -> Sistem'deki tüm Network'leri siler.
- **`docker network inspect <NetworkName>`** -> ID'si verilen Network detaylarını verir.
- **`docker network create <NetworkName>`** -> ID'si verilen kullanıcı tanımlı bridge network oluşturulur.
  -  **`docker network create --driver <NetworkTürü> <NetworkName>`** -> Burada ki `--driver ` options'u ile istenilen türde yeni bir network yaratılabilir.

![](https://miro.medium.com/max/1400/0*5FIWGRGDK5z9vEkD)

- `docker container run --net <NetworkObject> <CONTAINER> komutunda `--net` options'u ile oluşturulan container'ın hangi networke bağlanacağını belirleyebiliriz.
- Docker containerlar ayağa kaldırılırken herhangi bir network ayarlaması yapılmadığı takdirde tüm containerlar default olan bridge networke dahil edilir ve dışarısı ile ya da birbirleri arasında iletişimi bu network üzerinden sağlarlar. Eğer ki oluşturulan containerın dışarıdan iletişime geçilebilmesini istiyorsak özel olarak container oluşturulurken `-p` veya `--publish` options'unu kullanmalıyız bu işleme Port Publish işlemi denir.
- Kullanımı şu şekildedir;
   - `-p <HostPort>:<ContainerPort>` formatındadır. Örneğin;
   - `docker container run -d -p 8080:80 nginx` Burada bu host üzerinden nginx imajı'ndan bir container yarattık ve bu host'un 8080 portuna gelen bütün istekleri de oluşturulan container'ın 80 portuna gönder demiş olduk.
- Container yaratılırken birden fazla port publish işlemi yapılabilir.
### Kullanıcı Tanımlı Bridge Network
- Varsayılan Bridge Network dışında kendi Bridge Networklerimizi oluşturabiliriz. Kullanıcı tanımlı Bridge Network'lerin bazı avantajları vardır.
  - Normal şartlarda konteynerler birbiriyle IP üzerinde haberleşir. Fakat eğer konteynerlerin onlara atadığımız isimler üzerinden haberleşmesini istiyorsak kendi ağımızı oluşturmamız gerekiyor. Bu ağ varsayılan olarak belirtilen bridge ağ tipinde olsa dahi oluşturmamız gerekiyor.
  - Kullanıcı tanımlı ağ daha izole çalışmamızı sağlar. Şöyle ki; bir host içerisinde oluşturduğumuz bütün konteynerler varsayılan olarak aynı ağa ekleneceğinden yanlışlıkla birbiri arasında iletişim olmaması gereken konteynerler haberleşebilir.
  - Kullanıcı tanımlı ağa konteyner eklemek ve silmek daha kolaydır. Varsayılan ağa bir konteyner eklediğinizde onu ayırmak istersek o konteyneri silip farklı bir ağa bağlamanız gerekiyor.
  - Container'lar çalışır durumdayken de kullanıcı tanımlı bridge network'lere bağlanıp, bağlantıyı kesebilirler
- Burada bahsedilen Kullanıcı Tanımlı Bridge Network oluşturmak için `docker network create <NetworkName>` komutu kullanılır. Örneğin;
  - `docker network create mybridge` Burada mybridge adında bir Kullanıcı Tanımlı Bridge Network oluşturduk.
  - `docker run -dit --name alpine1 -net mybridge alpine ash` Burada oluşturduğumuz alpine1 adlı container'ı mybridge adlı bridge network ile oluşturmuş olduk.
- Benzer şekilde `docker network create --help ` komutu ile kullanabileceğimiz options'lara bakıp oluşturacağımız network ile ilgili özel ayarlamalar yapabiliriz.
  - Örneğin -> `--subnet=10.10.0.0/16` , `--ip-range=10.10.10.0/24 ` , `gateway=10.10.10.10` vb.
- Daha önceden bahsettiğimiz gibi Kullanıcı Tanımlı Bridge Network'lerin en önemli özelliklerinden birisi de container'lar çalışırken bu bridge'lere bağlanabilirler. Kullanımı Şu şekildedir:
  - `docker network connect <BridgeName> <ContainerName>` şeklindedir.
- Benzer şekilde bağlantıyı koparmak için
  - `docker network disconnect <BridgeName> <ContainerName>` şeklindedir.
