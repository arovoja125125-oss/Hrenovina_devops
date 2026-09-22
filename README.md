Dev-ops
Задания на семестр "Скрипты Python для автоматизации управление архитектурой""

<details>
<summary><b> Задание 2.1 </b></summary>
Изучите скрипт. Его задача: Анализ логов веб-сервера Nginx, выявление IP-адресов, с которых идет подозрительно много 404-х ошибок (попытка сканирования уязвимостей), и отправка оповещения в Telegram.

Подготовьте отчет (в виде страницы на своем github). 

Ответьте в отчете на следующие вопросы:

1. (4 балла) Какие библиотеки используются в скрипте? Опишите функции  которые они предоставляют.

2. (2 балла) Какое окружение нужно обеспечить, чтобы скрипт заработал? 

3. (6 баллов) Модифицируйте скрипт таким образом, чтобы он выполнил хотя бы часть своего функционала. Например анализ лога сервера beget (не обязательно на ошибку 404). Или отправка сообщения.

_________________________________________________

 import re

from collections import Counter

import requests



LOG_FILE = "/var/log/nginx/access.log"

TELEGRAM_TOKEN = "your_bot_token"

CHAT_ID = "your_chat_id"



# Поиск IP адресов с ошибками 404

with open(LOG_FILE, "r") as f:

    log_content = f.read()

    # Ищем IP и код ответа 404

    ips = re.findall(r'(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}).*?" 404', log_content)



# Если какой-то IP совершил более 50 ошибок 404

for ip, count in Counter(ips).items():

    if count > 50:

        msg = f"Обнаружена подозрительная активность! IP {ip} получил {count} ошибок 404."

        requests.post(f"https://telegram.org{TELEGRAM_TOKEN}/sendMessage", json={"chat_id": CHAT_ID, "text": msg})
</details>

<details>
<summary><b> Ответ </b></summary>

</details>

<details>
<summary><b> Задание 2.2 </b></summary>

</details>

<details>
<summary><b> Ответ </b></summary>

</details>

<details>
<summary><b> Задание 2.3 </b></summary>

</details>

<details>
<summary><b> Ответ </b></summary>

</details>

<details>
<summary><b> Задание 2.4 </b></summary>

</details>

<details>  
<summary><b> Ответ </b></summary>

</details>
