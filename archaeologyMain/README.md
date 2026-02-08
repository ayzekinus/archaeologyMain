# archaeologyMain

Bu doküman proje kurulumu ve yerel geliştirme adımlarını içerir.

## Gereksinimler

- Docker + Docker Compose
- (Opsiyonel) Python 3.10+ ve pip (Docker kullanmayacaksanız)

## Docker ile Kurulum (Önerilen)

1. **İmajları oluşturun ve servisleri başlatın**
   ```bash
   docker compose up -d --build
   ```

2. **Veritabanının hazır olmasını bekleyin**
   ```bash
   docker compose ps
   docker compose logs -f db
   ```

3. **Migration dosyalarını oluşturun (gerekliyse)**
   ```bash
   docker compose exec django-archaeology python manage.py makemigrations
   ```

4. **Veritabanı migrationlarını çalıştırın**
   ```bash
   docker compose exec django-archaeology python manage.py migrate
   ```

5. **(Opsiyonel) Admin kullanıcı oluşturun**
   ```bash
   docker compose exec django-archaeology python manage.py createsuperuser
   ```

6. **Uygulamayı görüntüleyin**
   - Uygulama: http://localhost:8000
   - PgAdmin (opsiyonel): http://localhost:80 (admin@admin.com / root)

## Docker ile Ortam Değişkenleri

`docker-compose.yml` dosyasında aşağıdaki varsayılan değerler tanımlıdır:

- `DB_ENGINE=postgres`
- `POSTGRES_DB=postgres2`
- `POSTGRES_USER=postgres2`
- `POSTGRES_PASSWORD=postgres`
- `POSTGRES_HOST=db`
- `POSTGRES_PORT=5432`
- `SECRET_KEY=dev-insecure-secret-key`

Gerekirse bu değerleri `.env` dosyası üzerinden değiştirip `docker-compose.yml` içinde `env_file` kullanabilirsiniz.

## Docker Olmadan Yerel Kurulum (Opsiyonel)

1. **Bağımlılıkları yükleyin**
   ```bash
   pip install -r requirements.txt
   ```

2. **Geliştirme ortamı için bir `SECRET_KEY` tanımlayın**
   ```bash
   export SECRET_KEY="dev-insecure-secret-key"
   ```

3. **Migration ve sunucu**
   ```bash
   python manage.py migrate
   python manage.py runserver
   ```

> Not: `DB_ENGINE` tanımlı değilse SQLite kullanılır. PostgreSQL kullanmak için
> `DB_ENGINE=postgres` ve ilgili `POSTGRES_*` değişkenlerini tanımlayın.

## Sorun Giderme

**Migrate sırasında `Name or service not known` hatası alıyorum**

- `db` servisinin sağlıklı olduğundan emin olun:
  ```bash
  docker compose ps
  docker compose logs -f db
  ```
- `django-archaeology` konteynerini yeniden başlatın:
  ```bash
  docker compose restart django-archaeology
  ```
