# Container Nedir:
![Container Resim](https://diginomica.com/sites/default/files/images/2017-09/docker-container.jpg)
- Docker Daemon tarafından Linux çekirdeği içerisinde birbirinden izole olarak çalıştırılan process’lerin her birine verilen isimdir. Oluşturulmuş bir Image'ın Docker Engine yüklü bir bilgisayarda çalıştırılmış haline denir.
Linux containerlar eklenen iki özellik sayesinde izole çalışırlar.

![](https://miro.medium.com/max/1400/0*BruS4MbRTn01sHLz.jpg)

- **Namespaces**
  - Linux çekirdeğinde bulunan yapı taşlarındandır. Prensibi çekirdek kaynaklarrının bölümlere ayrılmasıdır. Container izolasyonunun sağlanmasındaki birincil yapıdır.
  - Bazı namespaces'ler
    - Net: Ağ arayüzlerini yönetir
    - PID: Süreçlerin izolasyonunu sağlar
    - Mnt: Dosya sistemindeki mount noktalarını yönetir
- **Cgroups**
  - Linux çekirdeğinin kullandığı temel bileşenlerdendir. Donanım kaynaklarının sınırlandırılması, kısıtlama ve limit özelliklerinin sağlanması için kullanılır
- Container'lar; host makinede izole olarak çalışan, kullanıcı tarafından her şekilde müdahale edilebilen **sanal birimdir**.
- Aynı sistem üzerinde çalışırlar, o sistemin çekirdeğini kullanırlar fakat birbirlerinden tamamen izole durumdalardır.


## Docker CLI
`docker`  -> Docker CLI'da kullanabileceğimiz komutları listeler
  - **Options**
    - Docker Daemon'a bağlanırken kullanacağımız parametreler gözükür
  -  **Management Commands**
     -  Yönetebileceğimiz bileşenlerin listesi
  -  **Commands**
     -  Yönetmek istediğimiz bileşen için çalıştırmak istediğimiz komut

## Docker Container
- `docker container --help` ->  container bileşeni ile kullanabileceğimiz komutları görebiliriz.
- Kullanımı ` docker container COMMAND ` formatındadır.
- **`docker container run`** -> parametre olarak verdiğimiz isimde bir container varsa başlatır yoksa önce oluşturur sonra başlatır.
  - Kullanımı ` docker run [OPTIONS] IMAGE [COMMAND] [ARG...]` formatındadır.
  - Örneğin: `docker container run hello-world:latest`
  - [OPTIONS] hakkında detaylı bilgi almak için `docker container run --help` komutu kullanılabilir
    - **Bazı önemli optionslar**
      - --name => Container'a isim vermek için kullanılır.
      - -p => (Port) Port belirler -p 80:80 yazdığımızda ilk kısım (80) bizim bilgisayarımızda konteynere ulaşacağımız portu ifade eder.
      - --interactive, -i => (Interactive) Konteyneri sadece komut satırı açık olduğu sürece ayakta tutar. Interaktif modu aktifleştirir. Bu komutla ayağa kaldırdığımız bir konteynerin çalışmaya devam etmesini istiyorsak "CTRL + PQ" tuşlarına basarak arka planda çalışmasını devam ettirebiliriz.
      - --tty, -t => (TTY) Konteynerle iletişime geçebileceğimiz sanal bir terminal oluşturur. Genellikle `-i` parametresiyle  beraber kullanılır ve kullanımı -it şeklindedir.
      - --detach, -d => (Detach) Konteyneri arka planda ayağa kaldırır.
  - **Komut Nasıl Çalıştı**
     - docker komutu, yukarıdaki isteği arka planda çalışan Docker Daemon'a gönderir.
     - Komutu aldıktan sonra Daemon image'in var olup olmadığını kontrol eder.
     - `hello-world:latest` image'ini [Docker Hub](https://hub.docker.com/) adlı varsayılan registy'den indirir(pull).
     - Daemon, belirtilen image sahip bir container oluşturur.
     - Ardından istenilen komut terminal ekranında çalıştırdı.
- **`docker container ls`** -> Sistemdeki çalışan containerları listeler özel olarak `docker container ls -a` komutu ile sistemdeki tüm containerları inceleyebiliriz. Benzer şekilde `ls` komutu yerine `ps` komutuda kullanılabilir.
   - Her container imajında, o imajdan bir container oluşturduğumuz zaman varsayılan olarak çalışması için ayarlanmış bir uygulama vardır. Bu uygulama çalıştığı sürece container ayakta kalır. Uygulama çalışmayı bıraktığında container da kapatılır. Docker, container başlatıldığı zaman otomatik çalışması için tek bir uygulamanın çalışmasına izin verir.
- **`docker container start <ContainerID>`** -> ID'si verilen container'ı başlatır.
- **`docker container stop <ContainerID>`** -> ID'si verilen container'ı durdurur.
- **`docker container rm <ContainerID>`** -> ID'si verilen container'ı siler fakat ilişkili Volume’lara dokunmaz.
  - 'docker rm -v <ContainerID>' İlgili Container’ı ilişkili Volume’lar ile birlikte kaldırır.
  - Bu komut ile çalışan bir container'ı silemeyiz çalışan bir container'ı silmek istersek öncelikle durdurmamız gerekir yada **`-f`**(--force) parametresi kullanılarak çalışmakta olan container'ı silebiliriz.
- **`docker container prune`** -> Sistemdeki çalışmayan tüm conteinerları siler.
- **`docker container exec <ContainerID>`** -> ID'si verilen container'a bağlan demektir.
  - Örnerğin: 
    - `docker run --name ubuntu_bash --rm -i -t -d ubuntu bash` ile oluşturduğumuz bir containera
    - `docker exec -d ubuntu_bash touch /tmp/execWorks ` ile çalışan bir container içerisinde yeni bir dosya oluşturduk.
    - 'docker exec -it ubuntu_bash sh' ile çalışan bir container içerisine sh komutu ile bağlanmış olduk.
  - Benzer şekilde 'exec' komutu kullanırken container ID veya ismini girdikten sonra yapmak istediğimiz işlemi girebiliriz.
-  **`docker container pause <ContainerID>`** -> İlgili Container’ı duraklatır.
-  **`docker container unpause <ContainerID>`** -> İlgili Container pause ile duraklatılmış ise çalışmasına devam ettirilir.
-  **`docker container attach <ContainerID>`** -> ID'si verilen detach modda başlatılan bir Container’a attach olmak için kullanılır.
    - Örnerğin:
      - 'docker container run --name test -d -it debian' komutu ile detach modda oluşturtuğumuz container'a
      - 'docker attach test' ile bağlanıp debian image'i ile oluşturulan container da istediğimiz komutları kullanabiliriz.
-  **`docker container inspect <ContainerID>`** -> ID'si verilen container ile ilgili detaylı bilgiler verir.
  - **`docker container logs <ContainerID>`** -> ID'si verilen container'a ait logları gösterir. Docker ID'leri benzersizdir o yüzden container'ın IDsinin ilk 2 veya 3 karakterini bile girmek yeterli olur.
- **`docker container top <ContainerID>`** ->  ID'si verilen container'da hangi processlerin çalıştığına bakabiliriz.
- **`docker stats <ContainerName>`** -> ilgili container'ın ne kadar kaynak kullandığını gösterir
  - `docker stats` -> tüm container'ların kaynak kullanımını gösterir.
## Container Cpu ve Memory Limitleri
- Containerlar varsayılan olarak üzerinde bulunduğu host sistemin Cpu ve Memort kaynaklarını herhangi bir kısıtlama olmadan kullanabilir. Tek bir container oluşturulduğunda bu durum herhangi bir sorun teşkil etmez fakat çok sayıda container'ın çalıştığı bir ortam da oluşturulan bir container'ın tüm sistem kaynaklarını tüketerek bulunduğu host üzerinde başka bir uygulama veya container'ın çalışmasını engelleyebilir bu sorunun önüne geçmek için container oluştururken Cpu ve Memory kullanımına kısıtlamalar getirebiliriz.
### Memory
- `--memory` komutu ile memory kullanımını kısıtlayabiliriz. Örneğin;
    - `docker container run -d -it --name limit1 ubuntu bash` komutu ile yarattığımız container bulunduğu hosttaki tüm memory'i kullanabilirken
        
        ![memory1](https://user-images.githubusercontent.com/98760765/185969833-3f203285-c272-47d7-a7d1-b68dbf3edbc0.png)
    - `docker container run -d -it --name limit1 --memory=100m ubuntu bash` komutu ile yarattığımız container en fazla 100 mb kadar kullanabilir.
        
        ![memory2](https://user-images.githubusercontent.com/98760765/185970282-49809a1b-7d0e-4bb4-ac1a-46c7579c414f.png)
- `docker container run -dit --memory=100m --memory-swap=200m ubuntu bash` buradaki `--memory-swap=``ile eğer memory limiti aşılsa bile swap olarak 100m daha kullanabilmesine olanak tanımış oluruz.
    - --memory-swap değeri her zaman --memory değerinden büyük olmalıdır.
  - `m` -> megabyte
  - `g` -> gigabyte
  - `k` -> kilobyte
  - `b` -> byte şeklindedir.
### CPU
  - Cpu kısıtlamalarında ise sistemde bulunun kaç adet core kullanabileceğini hatta bunlardan hangi cpular dan olacağını belirleyebiliyoruz.
  - `docker container run -dit --cpus="1.5" alpine` komutu ile oluşturduğumuz container'ın bulunduğu sistem içerisinde ne kadar core varsa onun sadece 1.5 tanesini kullanmasını söylüyoruz.
  - `docker container run -dit --cpus="2" --cpuset-cpus="0,3" alpine` komutu ile kullanacağı 2 core'u Cpu0 ve Cpu3 den almasını söylüyoruz.
  
## Environment Variables
- İşletim sistemi genelinde geçerli olan değişken tanımlaya Environment Variables denir.
- Environment Variables'lar büyük küçük harf duyarlıdır.
- Docker Environment Variables Kullanımı
  - `--env` option'u ile container oluşturulurken Environment Variables atanabilir. Örneğin;
    - `docker container run -it --env VAR1=deneme1 --env VAR2=deneme2 ubuntu sh` ile container'a 2 adet Environment Variables tanımlamış olduk
  - Benzer şekilde `--env-file` oluşturduğumuz bir dosya içerisine tüm Environment Variablesları tanımlayıp tek bir komut ile hepsini oluşturabiliriz.
    - `docker container run -it --env-file ./env.list ubuntu sh` burada /env.list dosyasında ki Environment Variables tanımlamış olduk.
- Container'ı ayağa kaldırma esnasında Environment Variables eklenebildiği gibi Dockerfile içerisinde image içinde de Environment Variables eklenebilir bu yöntemi ilerleyen bölümlerde göreceğiz.
  
