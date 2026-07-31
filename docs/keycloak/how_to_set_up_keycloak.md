Теперь давай настроим **Keycloak** для логистической платформы.

---
## 🎯 План настройки Keycloak

1. **Создать Realm** — `logistics-platform`
2. **Создать клиентов** для каждого микросервиса
3. **Настроить роли и пользователей
4. **Настроить Client Scopes** для передачи ролей в JWT
5. **Протестировать** аутентификацию

---
## 📝 Шаг 1: Вход в Keycloak Admin Console
1. Открой браузер: [`http://localhost:8080`](http://localhost:8080/)
2. Нажми **Administration Console**
3. Введи credentials (из твоего `docker-compose.yml`):
    - **Username**: `admin`
    - **Password**: `admin`
4. Ты попадешь в **Master Realm

---
## 📝 Шаг 2: Создание Realm "logistics-platform"
1. В левом верхнем углу наведи на **Master** и выбери **Create Realm**
2. Заполни:
    - **Name**: `logistics-platform`
    - **Enabled**: `ON`
3. Нажми **Create**

---
## 📝 Шаг 3: Создание Clients *(для каждого микросервиса)*

### Client 1: API Gateway *(основной клиент)*
1. В левом меню выбери **Clients** → **Create client**
2. Заполни:
    - **Client ID**: `logistics-gateway`
    - Name: API Gateway *(можно не указывать)*
    - Description: точка входа *(можно не указывать)*
    - **Client authentication**: `OFF` (*для разработки, потом **включишь***)
    - **Authorization**: `OFF
3. Нажми **Next**
4. Настрой:
    - **Valid redirect URIs**:
```text
http://localhost:8080/*
http://localhost:8081/*
http://localhost:8082/*
http://localhost:8083/*
http://localhost:8084/*
http://localhost:8085/*
http://localhost:8086/*
http://localhost:8087/*
```
- **Web origins**: `+` добавь: 
```text
http://localhost:8080
http://localhost:8081
http://localhost:8082
http://localhost:8083
http://localhost:8084
http://localhost:8085
http://localhost:8086
http://localhost:8087
https://hoppscotch.io
```
5. Нажми **Save

### Client 2: User Service
1. **Client ID**: `user-service
    - Name: User Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON
3. **Valid redirect URIs**: `http://localhost:8081/*`
4. **Web origins**: `http://localhost:8081`
5. Сохрани и скопируй Secret
   Secret: `dHGEGORwNJMzJrGJlnx5phkwbf0ctfX1`

### Client 3: Delivery Service
1. **Client ID**: `delivery-service
    - Name: Delivery Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON`
3. **Valid redirect URIs**: `http://localhost:8082/*
4. **Web origins**: `http://localhost:8082`
5. Сохрани и скопируй Secret
   Secret: `dQRoKuVasNJWCGCM5mMJX91eCafvGoan`


### Client 4: Transport Service
1. **Client ID**: `transport-service
    - Name: Transport Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON
3. **Valid redirect URIs**: `http://localhost:8083/*`
4. **Web origins**: `http://localhost:8083
5. Сохрани и скопируй Secret
   Secret: `uo7eumNCUzR94SdZU1SE4YCRpLwRRPN8`

### Client 5: Payment Service
1. **Client ID**: `payment-service
    - Name: Payment Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON
3. **Valid redirect URIs**: `http://localhost:8084/*`
4. **Web origins**: `http://localhost:8084`
5. Сохрани и скопируй Secret
   Secret: `EXtpJpJuQqTMiQJlzcDiScG0CyBGeb88`


### Client 6: Cargo Service *(для межсервисной авторизации)*
1. **Clients** → **Create client**
2. Заполни:
    - **Client ID**: `cargo-service`
    - Name: Cargo Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
    - **Client authentication**: `ON` (*будет использоваться для `Service-to-Service`*)
    - **Authorization**: `OFF`
3. Нажми **Next**
4. Настрой:
    - **Valid redirect URIs**: `http://localhost:8085/*
    - **Web origins**: `http://localhost:8085`
5. Нажми **Save**
6. Перейди во вкладку **Credentials** и **скопируй Client Secret** (*понадобится для настройки микросервиса*)
   Secret: `cPHSAeDyTV2W5biKSnxIRe1GXiF0T4JR`

### Client 7: Notification Service
1. **Client ID**: `notification-service
    - Name: Notification Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON
3. **Valid redirect URIs**: `http://localhost:8086/*`
4. **Web origins**: `http://localhost:8086`
5. Сохрани и скопируй Secret
   Secret: `dyUQpIR7lqQHf7ksXEHBbwshzjB6brRp`

### Client 8: Reporting Service
1. **Client ID**: `reporting-service
    - Name: Reporting Service *(можно не указывать)*
    - Description: для межсервисной авторизации *(можно не указывать)*
2. **Client authentication**: `ON
3. **Valid redirect URIs**: `http://localhost:8087/*`
4. **Web origins**: `http://localhost:8087`
5. Сохрани и скопируй Secret
   Secret: `wOlwlr6ebKd30MDjT7hdVUASqAgt3ZSz`


---
## 📝 Шаг 4: Настройка **Client Scopes** <br>(*для передачи ролей в **JWT***)
1. В левом меню выбери **Client scopes**
2. Создай новый scope:
    - **Name**: `logistics-roles` ~~`roles` уже было занято~~
    - **Type**: `Default`
    - **Protocol**: `openid-connect
3. Нажми **Save**
4. Перейди во вкладку **Mappers** → **Configure a new mapper
5. Выбери **Audience** или создай свой (выбрать `By configuration`->`User Realm Role`):
    - **Name**: `logistics-realm-roles` ~~`realm-roles`~~
    - **Mapper Type**: `User Realm Role
    - **Token Claim Name**: `logistics-roles` ~~`roles`~~
    - **Claim JSON Type**: `String
    - **Add to ID token**: `ON
    - **Add to access token**: `ON`
    - **Add to userinfo**: `ON`
6. Нажми **Save**

---
## 📝 Шаг 5: Создание пользователей

### Администратор платформы
1. В левом меню выбери **Users** → **Add user**
2. Заполни:
    - **Username**: `admin-platform`
    - **Email**: `admin@logistics-platform.com
    - **First name**: `Admin
    - **Last name**: `Platform
    - **Email Verified**: `ON`
    - **Enabled**: `ON
3. Нажми **Create**
4. Перейди во вкладку **Credentials**:
    - **Password**: `1234`
    - **Temporary**: `OFF`
5. Нажми **Set Password

### Менеджер доставки
1. **Users** → **Add user**
2. **Username**: `delivery-manager
3. **Email**: `delivery@logistics-platform.com`
4. **First name**: `Delivery
5. **Last name**: `Manager
6. **Email Verified**: `ON
7. **Enabled**: `ON
8. Нажми **Create
9. **Credentials**: `1234` (Temporary: OFF)

### Оператор склада
1. **Users** → **Add user
2. **Username**: `warehouse-operator
3. **Email**: `warehouse@logistics-platform.com
4. **First name**: `Warehouse`
5. **Last name**: `Operator
6. **Email Verified**: `ON
7. **Enabled**: `ON
8. Нажми **Create
9. **Credentials**: `1234` (Temporary: OFF)

---
## 📝 Шаг 6: Создание ролей

1. В левом меню выбери **Realm roles** → **Create role
2. Создай следующие роли:

|Role Name|Description|
|---|---|
|`admin`|Полный доступ ко всем сервисам|
|`delivery-manager`|Управление доставками|
|`warehouse-operator`|Управление складом и товарами|
|`user`|Базовый пользователь|
|`service`|Для межсервисной коммуникации|

---
## 📝 Шаг 7: Назначение **ролей** пользователям

### Admin-platform
1. Перейди в **Users** → выбери `admin-platform
2. Вкладка **Role mapping
3. Нажми **Assign role**
4. Выбери все роли (`admin`, `delivery-manager`, `warehouse-operator`, `user`)
5. Нажми **Assign**

### Delivery Manager
1. Перейди в **Users** → выбери `delivery-manager`
2. **Role mapping** → **Assign role**
3. Выбери `delivery-manager` и `user`
4. Нажми **Assign**

### Warehouse Operator
1. Перейди в **Users** → выбери `warehouse-operator`
2. **Role mapping** → **Assign role**
3. Выбери `warehouse-operator` и `user`
4. Нажми **Assign**

---
## 📝 Шаг 8: Настройка **Client Scopes** для клиентов

Чтобы клиенты получали роли в JWT:
1. Зайди в **Clients** → выбери своего клиента (например, `logistics-gateway`).
2. Перейди на вкладку **"Client scopes"**.
3. Ты увидишь список доступных скопов.
4. Нажми **"Add client scope"**.
5. Из выпадающего списка выбери **`logistics-roles`** (*или тот скоп, где у тебя настроены роли*).
6. Нажми **"Add"**.
7. **Важно!** В колонке **"Default"** переключи тумблер в положение **"Default"** (или "Always" — зависит от версии Keycloak), чтобы этот скоп добавлялся автоматически.
   Ты именно переключаешь **"Default"** для `logistics-roles`, чтобы этот скоп всегда включался в токен без дополнительных параметров в запросе (`scope=...`).

Повтори для всех клиентов.

---
## 📝 Шаг 9: Настройка **Groups** <br>(*опционально, для удобства*)

1. В левом меню выбери **Groups** → **Create group**
2. Создай группы:
    - `Admins`
    - `Delivery Managers`
    - `Warehouse Operators`
    - `Users`
3. Назначь роли группам:
    - Выбери группу → **Role mapping** → назначь соответствующую роль
4. Добавь пользователей в группы:
    - **Users** → выбери пользователя → **Groups** → назначь группу

---
## 📝 Шаг 10: Тестирование аутентификации

### Получение токена через браузер (Authorization Code Flow)
1. Открой в браузере:
```text
http://localhost:8080/realms/logistics-platform/protocol/openid-connect/auth?client_id=logistics-gateway&response_type=code&redirect_uri=http://localhost:8080
```
2. Введи credentials одного из пользователей
3. После редиректа ты получишь код в URL

---
## Экспортируем **realm** и кладем в правильное место

### Шаг 1: Экспортируем realm через админку

#### Способ 1. Полный экспорт через `kc.sh` (Рекомендуемый)
Запустите команду экспорта прямо в вашем Docker-контейнере:
```PowerShell
docker exec -it logistics-keycloak /opt/keycloak/bin/kc.sh export --file /tmp/keycloak-export/logistics-platform-full.json --realm logistics-platform --users same_file
```
- **`--realm logistics-platform`** — выгружает именно ваш рилм.
- **`--users same_file`** — указывает записать всех пользователей прямо в этот же JSON-файл.
  После выполнения команды в локальной папке `./keycloak/exports/` появится файл `logistics-platform-full.json`, где внутри будет полноценная секция `"users": [...]` с учётом всех учетных записей и их паролей.

#### Способ 2. Выгрузить только пользователей через `kcadm.sh`
Если вам нужно получить пользователей отдельно в JSON-файл:
1. **Авторизуйтесь в CLI Keycloak:**
```PowerShell
docker exec -it logistics-keycloak /opt/keycloak/bin/kcadm.sh config credentials --server http://localhost:8080 --realm master --user admin --password admin
```
2. **Выгрузите список пользователей:**
```PowerShell
docker exec -it logistics-keycloak /opt/keycloak/bin/kcadm.sh get users -r logistics-platform --limit 500 > users.json
```


## 🔄 Альтернативный способ: через кнопку Export в админке

Если команды не работают:
1. Зайди в админку: [`http://localhost:8080/admin`](http://localhost:8080/admin)
2. Логин: `admin / admin
3. Выбери **logistics-platform
4. В левом меню выбери **Realm Settings
5. Нажми **Export** в правом верхнем углу
6. Выбери **Export** и сохрани файл вручную
7. Положи его в `C:\Users\Yury\IdeaProjects\Inno\Logistics-platform\be-logistics-platform-infrastructure\local\keycloak\realms\logistics-platform-realm.json`
   **НО в этом случае пользователи всеравно не импортятся (по соображ. безопасности)**


### Шаг 1: Удались старые файлы из `realms`
```powershell
# Удалить старые файлы (если есть)
Remove-Item "C:\Users\Yury\IdeaProjects\Inno\Logistics-platform\be-logistics-platform-infrastructure\local\keycloak\realms\logistics-platform-realm.json" -Force -ErrorAction SilentlyContinue
Remove-Item "C:\Users\Yury\IdeaProjects\Inno\Logistics-platform\be-logistics-platform-infrastructure\local\keycloak\realms\logistics-platform-users-0.json" -Force -ErrorAction SilentlyContinue
```

### Шаг 2: Скопируй файл в папку `realms`
```powershell
# Скопировать файл из exports в realms
Copy-Item "C:\Users\Yury\IdeaProjects\Inno\Logistics-platform\be-logistics-platform-infrastructure\local\keycloak\exports\logistics-platform-full.json" `
          "C:\Users\Yury\IdeaProjects\Inno\Logistics-platform\be-logistics-platform-infrastructure\local\keycloak\realms\logistics-platform-realm.json"
```
### Шаг 3: Перезапусти Keycloak с чистым импортом
```powershell
# 1. Остановить всё
docker-compose down
# 2. Удалить volume с данными Keycloak
docker volume rm logistics-platform_postgres_keycloak_data 2>$null
# 3. Запустить заново
docker-compose up -d
# 4. Проверить логи импорта
docker-compose logs keycloak | findstr "import"
```

## 🧪 После экспорта: перезапусти Keycloak
```powershell
# Остановить Keycloak
docker-compose stop keycloak

# Удалить старый контейнер
docker-compose rm -f keycloak

# Запустить заново (с импортом)
docker-compose up -d keycloak

# Проверить логи импорта
docker-compose logs keycloak | findstr "import"
```

## 🧪 Проверка
```powershell
# Получить токен
$body = @{
    client_id = "logistics-gateway"
    username = "admin-platform"
    password = "1234"
    grant_type = "password"
}
Invoke-RestMethod -Method Post `
    -Uri "http://localhost:8080/realms/logistics-platform/protocol/openid-connect/token" `
    -Body $body
```
**Ожидаемый ответ:** JWT токен с ролями.
```powershell
# Получить токен
$body = @{
    client_id = "logistics-gateway"
    username = "delivery-manager"
    password = "1234"
    grant_type = "password"
}
Invoke-RestMethod -Method Post `
    -Uri "http://localhost:8080/realms/logistics-platform/protocol/openid-connect/token" `
    -Body $body
```

```powershell
# Получить токен
$body = @{
    client_id = "logistics-gateway"
    username = "warehouse-operator"
    password = "1234"
    grant_type = "password"
}
Invoke-RestMethod -Method Post `
    -Uri "http://localhost:8080/realms/logistics-platform/protocol/openid-connect/token" `
    -Body $body
```
Расшифруй токен в [jwt.io](https://jwt.io/)


---
---
---
## 📊 Итоговая структура Realm
```text
logistics-platform (Realm)
│
├── Clients
│   ├── logistics-gateway (Public, для фронтенда)
│   ├── cargo-service (Confidential, Service-to-Service)
│   ├── delivery-service (Confidential, Service-to-Service)
│   └── user-service (Confidential, Service-to-Service)
│
├── Client Scopes
│   └── roles (для передачи ролей в JWT)
│
├── Realm Roles
│   ├── admin
│   ├── delivery-manager
│   ├── warehouse-operator
│   ├── user
│   └── service
│
├── Users
│   ├── admin-platform (admin, delivery-manager, warehouse-operator, user)
│   ├── delivery-manager (delivery-manager, user)
│   └── warehouse-operator (warehouse-operator, user)
│
└── Groups (опционально)
    ├── Admins
    ├── Delivery Managers
    ├── Warehouse Operators
    └── Users
```

---
## 🔧 Настройка Spring Boot приложений

### `application.yml` для микросервиса:
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8080/realms/logistics-platform
          jwk-set-uri: http://localhost:8080/realms/logistics-platform/protocol/openid-connect/certs
      
      client:
        registration:
          logistics-platform:
            provider: keycloak
            client-id: cargo-service
            client-secret: YOUR_CLIENT_SECRET
            authorization-grant-type: client_credentials
            scope: openid
        provider:
          keycloak:
            issuer-uri: http://localhost:8080/realms/logistics-platform
            user-name-attribute: preferred_username
```

---
## 🚀 Экспорт Realm (сохрани в Git)
```bash
# Экспортировать realm в JSON
docker exec logistics-keycloak /opt/keycloak/bin/kc.sh export \
  --realm logistics-platform \
  --file /tmp/keycloak-export/logistics-platform-realm.json
# Файл появится в ./keycloak/exports/
```
Добавь этот файл в Git, чтобы другие разработчики могли импортировать настройки при старте.

---
- **Админка master-рилма:** `http://localhost:8080/admin/master/console/`
- **Админка вашего рилма:** `http://localhost:8080/admin/logistics-platform/console/`
- **Эндпоинты авторизации (Token, Login):**
    - `http://localhost:8080/realms/master/protocol/openid-connect/token`
    - `http://localhost:8080/realms/logistics-platform/protocol/openid-connect/token`

---
