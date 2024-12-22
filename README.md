# Как дебажить веб-приложение на мобильном устройстве в локальной сети

Для мобильного тестирования важно запустить приложение так, чтобы оно было доступно на телефоне через локальную сеть. Рассказываем пошагово, как это сделать.

---

## Шаг 1. Узнайте IP-адрес вашего компьютера

Для доступа к серверу с телефона нужно знать локальный IP-адрес. Вот как это сделать:

- **Windows**:

  1. Откройте командную строку и выполните команду:

     ```bash
     ipconfig
     ```

  2. Найдите строку `IPv4 Address`, например `192.168.1.100`.

- **macOS/Linux**:
  1. Откройте терминал и выполните команду:

     ```bash
     ifconfig | grep inet
     ```

  2. Найдите адрес вида `192.168.x.x`.

---

## Шаг 2. Запустите сервер на нужном адресе

По умолчанию локальные серверы слушают только `localhost`, что недоступно для других устройств. Для работы через сеть сделайте следующее:

- **React/Vite/Next.js**:  
  Запустите сервер с параметром `--host`:

  ```bash
  npm start --host=0.0.0.0
  ```

- **Node.js/Express**:  
  Убедитесь, что сервер слушает `0.0.0.0`:

  ```javascript
  app.listen(3000, "0.0.0.0");
  ```

---

## Шаг 3. Подключитесь к серверу с телефона

1. Убедитесь, что ваш телефон и компьютер подключены к одной Wi-Fi сети.
2. В браузере телефона введите:

   ```plaintext
   http://192.168.X.X:3000
   ```

   Замените `192.168.X.X` на IP-адрес вашего компьютера.

Если приложение недоступно, убедитесь, что порт 3000 открыт для входящих соединений.

---

## Шаг 4. Откройте порт в брандмауэре

### Windows

1. Через меню "Пуск", Откройте `Брандмауэр Защитника Windows` → `Дополнительные параметры`.
2. Создайте новое правило для входящих подключений:
   - **Тип правила**: Порт.
   - **Протокол и порты**: TCP, порт `3000`.
   - **Действие**: Разрешить подключение.
   - **Профили**: Домен, Частный, Общедоступный.
   - **Имя**: Например, "Разрешение для разработки".

### macOS/Linux

На macOS и Linux настройте брандмауэр следующим образом:

- **UFW (Ubuntu)**:

  ```bash
  sudo ufw allow 3000/tcp
  ```

- **iptables**:

  ```bash
  sudo iptables -A INPUT -p tcp --dport 3000 -j ACCEPT
  ```

---

## Шаг 5. Настройте DNS-алиас для удобства

Чтобы каждый раз не вводить IP-адрес вручную, настройте DNS-алиас:

### Измените файл hosts

1. Откройте файл `hosts` на вашем компьютере:
   - **Windows**: `C:\Windows\System32\drivers\etc\hosts`
   - **macOS/Linux**: `/etc/hosts`
2. Добавьте строку:

   ```plaintext
   192.168.X.X dev.local
   ```

   Замените `192.168.X.X` на ваш локальный IP.
3. Очистите DNS-кэш:
   - **Windows**:

     ```bash
     ipconfig /flushdns
     ```

   - **macOS**:

     ```bash
     sudo dscacheutil -flushcache
     sudo killall -HUP mDNSResponder
     ```

   - **Linux**:

     ```bash
     sudo systemctl restart nscd
     ```

Теперь вы можете открывать приложение по адресу `http://dev.local:3000`. Вы можете изменить название DNS на любое которое вам понравится.

---

## Решение распространённых проблем

### Проблемы с подключением

- Убедитесь, что сервер настроен на прослушивание `0.0.0.0`.
- Проверьте, что на устройстве нет активного VPN или брандмауэра, блокирующего доступ.

### Очистка кэша браузера на телефоне

Если изменения в приложении не отображаются, очистите кэш или используйте режим инкогнито.

### Отключение блокировки нестандартных доменов

Некоторые браузеры могут блокировать доступ к нестандартным доменам. Для Chrome:

1. В адресной строке введите:

   ```plaintext
   chrome://flags/#block-insecure-private-network-requests
   ```

2. Отключите флаг и перезапустите браузер.
