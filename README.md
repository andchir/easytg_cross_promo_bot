# Bot for mutual reposts and audience sharing

This repository contains two bots for cross-promotion:
- **Telegram bot** - for Telegram channels (`main.py`)
- **VK bot** - for VK communities (`vk_bot.py`)

## Telegram Bot

*Справка по командам:*

**/add** - Добавить свой канал в каталог
**/my** - Показать мои каналы
**/delete** *[канал]* - Удалить канал из каталога
**/update** *[канал]* - Обновить количество подписчиков
**/find** *[канал]* - Найти похожие каналы для обмена
**/done** *[канал]* - Сообщить владельцу канала о выполненном репосте
**/confirm** *[свой_канал]* *[канал_репоста]* - Подтвердить репост
**/list** - Список каналов, ожидающих подтверждения
**/stat** - Показать статистику бота
**/abuse** *[канал]* *[причина]* - Пожаловаться на канал и владельца
**/help** - Показать эту справку

*Как это работает:*
1. Добавьте свой канал командой **/add**
2. Найдите похожие каналы **/find**
3. Подпишитесь и сделайте репост любого поста
4. Сообщите **/done** после репоста
5. Владелец канала подтвердит **/confirm**
6. Ожидайте ответного репоста

## VK Bot

*Справка по командам:*

**добавить [группа]** - Добавить свою группу в каталог
**мои** - Показать мои группы
**удалить [группа]** - Удалить группу из каталога
**обновить [группа]** - Обновить количество подписчиков
**найти [группа]** - Найти похожие группы для обмена
**готово [группа]** - Сообщить владельцу группы о выполненном репосте
**подтвердить [своя_группа] [группа_репоста]** - Подтвердить репост
**список** - Список групп, ожидающих подтверждения
**статистика** - Показать статистику бота
**жалоба [группа] [причина]** - Пожаловаться на группу и владельца
**помощь** - Показать эту справку

*Как это работает:*
1. Добавьте свою группу командой **добавить**
2. Найдите похожие группы **найти**
3. Подпишитесь и сделайте репост любого поста
4. Сообщите **готово** после репоста
5. Владелец группы подтвердит **подтвердить**
6. Ожидайте ответного репоста  

## Deploy

### Telegram Bot

~~~
sudo nano /etc/systemd/system/easytg_cross_promo_bot.service
~~~

~~~
sudo systemctl daemon-reload
sudo systemctl enable easytg_cross_promo_bot
sudo systemctl start easytg_cross_promo_bot
sudo systemctl status easytg_cross_promo_bot
~~~

### VK Bot

~~~
sudo nano /etc/systemd/system/easytg_cross_promo_bot_vk.service
~~~

~~~
sudo systemctl daemon-reload
sudo systemctl enable easytg_cross_promo_bot_vk
sudo systemctl start easytg_cross_promo_bot_vk
sudo systemctl status easytg_cross_promo_bot_vk
~~~

## Режим работы

### Telegram Bot

Бот поддерживает два режима работы:

#### Polling (по умолчанию)

Бот периодически опрашивает сервер Telegram на наличие новых сообщений.

```
BOT_MODE=polling
```

#### Webhook (рекомендуется для продакшена)

Для более быстрой работы можно использовать режим webhook. В этом режиме бот создает веб-сервер, на который Telegram отправляет обновления.

```
BOT_MODE=webhook
WEBHOOK_URL=https://example.com:8443
WEBHOOK_PORT=8443
WEBHOOK_SECRET_TOKEN=your_secret_token
```

**Требования для webhook:**
- Публичный IP адрес или домен с HTTPS
- Telegram поддерживает только порты: 443, 80, 88, 8443

**Опциональные параметры для прямого подключения (без reverse proxy):**
```
WEBHOOK_CERT=/path/to/cert.pem
WEBHOOK_KEY=/path/to/private.key
```

При использовании reverse proxy (nginx, haproxy) сертификаты настраиваются на стороне прокси.

### VK Bot

VK бот работает через VK Callback API и использует Flask для обработки webhook-запросов.

**Требования:**
- Публичный URL для webhook (например, `https://example.com/vk_callback`)
- Настроенный Callback API сервер в настройках VK сообщества
- Confirmation code из настроек Callback API

**Конфигурация:**
```
VK_DB_NAME=easytg_cross_promo_bot_vk
VK_DB_USER_NAME=easytg_cross_promo_bot_vk
VK_DB_USER_PASSWORD=xxx
VK_ACCESS_TOKEN=xxx
VK_GROUP_ID=xxx
VK_CONFIRMATION_CODE=xxx
VK_FLASK_HOST=0.0.0.0
VK_FLASK_PORT=5000
```

**Настройка VK Callback API:**
1. В настройках сообщества перейдите в "Работа с API" → "Callback API"
2. Добавьте новый сервер с URL: `http://your-server:5000/vk_callback`
3. Скопируйте confirmation code в переменную `VK_CONFIRMATION_CODE`
4. Включите событие "Входящее сообщение" (message_new)
5. Получите access token группы с правами на отправку сообщений

**Документация:**
- [VK Callback API](https://dev.vk.com/ru/api/callback/getting-started)
- [Событие message_new](https://dev.vk.com/ru/api/community-events/json-schema#message_new)
