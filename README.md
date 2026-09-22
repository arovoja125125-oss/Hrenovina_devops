# Hrenovina_devops
 Dev-ops
 Тема "Скрипты Python для автоматизации управление архитектурой"

Задание 2.1 (12 баллов)

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



 #Поиск IP адресов с ошибками 404

 with open(LOG_FILE, "r") as f:

    log_content = f.read()

    # Ищем IP и код ответа 404

    ips = re.findall(r'(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}).*?" 404', log_content)



# Если какой-то IP совершил более 50 ошибок 404

for ip, count in Counter(ips).items():

    if count > 50:

        msg = f"Обнаружена подозрительная активность! IP {ip} получил {count} ошибок 404."

        requests.post(f"https://telegram.org{TELEGRAM_TOKEN}/sendMessage", json={"chat_id": CHAT_ID, "text": msg})

_________________________________________________
Задание 2.2 (8 баллов)

Напишите на Python скрипт, который читает из файла link.txt адреса сайтом и проверяет их доступность, выводя результат своей работы в файл report.txt формате: адрес -> статус (Ok, Error). 

Решение и условие задачи можно модифицировать, если это не искажает ее суть.


Задание 2.3 (8 баллов)

Напишите скрипт, который парсит некую страницу сайта и находит на ней текущий курс валюты.

Изучите пример решения задачи

import requests
from bs4 import BeautifulSoup

url = "https://example-currency-site.com/rates"  # замените на реальный URL

# Делаем запрос к странице
response = requests.get(url, timeout=10)
response.raise_for_status()  # выбросит ошибку, если статус не 200

soup = BeautifulSoup(response.text, "html.parser")

# Допустим, курсы лежат в таблице с классом "rates-table",
# а в ячейках есть название валюты и значение.
# Подстройте селекторы под реальную верстку.
rates = []
table = soup.find("table", class_="rates-table")
if table:
    rows = table.find_all("tr")[1:]  # пропускаем заголовок
    for row in rows:
        cols = row.find_all("td")
        if len(cols) >= 2:
            currency = cols[0].get_text(strip=True)
            rate = cols[1].get_text(strip=True)
            rates.append({"currency": currency, "rate": rate})

for item in rates:
    print(f"{item['currency']}: {item['rate']}")

Потребуется установить библиотеку
pip install requests beautifulsoup4

Комментарий:

Название валюты берётся из первой ячейки (<td>) в каждой строке таблицы — и сохраняется в переменную currency:
rate = cols[1].get_text(strip=True)

А курс — из второй ячейки:
rate = cols[1].get_text(strip=True)

На вашем сайте признаки данных и структура могут быть другими. Вам придется адаптировать под них скрипт примера.
Можно решить задачу самостоятельно. В том числе с привлечением нейронной сети. Оценка ставится не за решение задачи, а за ее защиту, ответы на вопросы преподавателя.


Задание 2.4 (8 баллов)

Напишите скрипт, который обходит web-страницы по списку адресов, который хранится в файле link.txt, и проверяет изменилась ли они с последнего обхода. Отчет о результатах обхода сохраняется в файле report.txt

Изучите пример решения задачи. Из нее можно взять идеи, однако задачу можно решить короче и проще. Оценка ставится не за решение задачи, а за ее защиту, ответы на вопросы преподавателя.

Пример решения задачи

import hashlib
import json
import time
from pathlib import Path

import requests

LINKS_FILE = "link.txt"
HASHES_FILE = "hashes.json"
REPORT_FILE = "report.txt"
DELAY_SECONDS = 1  # задержка между запросами, чтобы не нагружать сервер
HEADERS = {"User-Agent": "Mozilla/5.0 (compatible; PageChangeChecker/1.0)"}


def load_links(path: str):
    with open(path, "r", encoding="utf-8") as f:
        return [line.strip() for line in f if line.strip() and not line.startswith("#")]


def load_hashes(path: str):
    p = Path(path)
    if not p.exists():
        return {}
    with open(p, "r", encoding="utf-8") as f:
        return json.load(f)


def save_hashes(data: dict, path: str):
    with open(path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)


def compute_hash(content: bytes) -> str:
    return hashlib.sha256(content).hexdigest()


def check_pages():
    links = load_links(LINKS_FILE)
    old_hashes = load_hashes(HASHES_FILE)
    new_hashes = old_hashes.copy()
    report_lines = []

    for url in links:
        try:
            resp = requests.get(url, headers=HEADERS, timeout=10)
            resp.raise_for_status()
            content = resp.content  # берём байты, чтобы хеш был стабильным
            current_hash = compute_hash(content)

            if url in old_hashes:
                if current_hash == old_hashes[url]:
                    report_lines.append(f"[OK] {url} — без изменений")
                else:
                    report_lines.append(f"[CHANGED] {url} — контент изменился")
            else:
                report_lines.append(f"[NEW] {url} — первый обход")

            new_hashes[url] = current_hash

        except Exception as e:
            report_lines.append(f"[ERROR] {url} — {e}")

        time.sleep(DELAY_SECONDS)

    # Сохраняем обновлённые хеши
    save_hashes(new_hashes, HASHES_FILE)

    # Пишем отчёт
    with open(REPORT_FILE, "w", encoding="utf-8") as f:
        f.write("\n".join(report_lines))

    print(f"Готово. Отчёт сохранён в {REPORT_FILE}")


if __name__ == "__main__":
    check_pages()
