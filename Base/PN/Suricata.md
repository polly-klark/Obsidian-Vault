#cmd #suricata 

Интеграция Suricata с ELK Stack (Elasticsearch, Logstash, Kibana) позволяет эффективно собирать, хранить и анализировать данные о безопасности. В этом руководстве мы подробно рассмотрим все шаги, необходимые для настройки этой интеграции.

## Шаг 1: Установка Suricata 

1. **Добавьте репозиторий Suricata**: 

```
sudo add-apt-repository -y ppa:oisf/suricata-stable
``` 

2. **Обновите систему**: 

```
sudo apt update
``` 

3. **Установите Suricata**: 

```
sudo apt -y install suricata
```

4. **Настройте Suricata**: 

Откройте файл конфигурации Suricata: 

```
sudo nano /etc/suricata/suricata.yaml
```

Убедитесь, что вывод EVE JSON включен. Найдите и измените секцию `outputs` следующим образом: 

```
outputs: 
  eve-log: 
    enabled: yes 
    filetype: regular 
    filename: eve.json 
    types: 
      - alert: 
        payload: yes 
        payload_printable: yes 
        http_body: yes 
      - http: 
      - dns: 
      - tls:
```

5. **Запустите Suricata**: 

```
sudo systemctl start suricata
```

## Шаг 2: Установка и настройка Elasticsearch

1. **Установите Java** (необходим для работы Elasticsearch): 

```
sudo apt -y install openjdk-11-jdk
``` 

2. **Добавьте GPG-ключ Elastic**: 

```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

3. **Добавьте репозиторий Elastic в sources.list**: 

```
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list 
```

4. **Установите Elasticsearch**: 

```
sudo apt update sudo apt -y install elasticsearch
``` 

5. **Настройте Elasticsearch**:
Откройте файл конфигурации Elasticsearch:

```
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Убедитесь, что он слушает на нужном IP-адресе (например, 0.0.0.0 для всех интерфейсов):

```
network.host: 0.0.0.0
```

6. **Запустите и активируйте Elasticsearch**:

```
sudo systemctl start elasticsearch 
sudo systemctl enable elasticsearch
```

## Шаг 3: Установка и настройка Kibana

1. **Установите Kibana**: 

```
sudo apt install kibana
```

2. **Настройте Kibana**: 
Откройте файл конфигурации Kibana: 

```
sudo nano /etc/kibana/kibana.yml 
```

3. **Измените настройки подключения к Elasticsearch**: 

```
elasticsearch.hosts: ["http://localhost:9200"]
```

4. **Запустите и активируйте Kibana**: 

```
sudo systemctl start kibana 
sudo systemctl enable kibana 
```

5. **Проверьте работу Kibana**: Откройте браузер и перейдите по адресу `http://<ваш_IP>:5601`.

## Шаг 4: Установка и настройка Filebeat

1. **Установите Filebeat**: 

```
sudo apt install filebeat
```

2. **Настройка Filebeat для отправки логов Suricata в Elasticsearch**:

Откройте файл конфигурации Filebeat:

```
sudo nano /etc/filebeat/filebeat.yml
```

Добавьте следующий ввод для Suricata:

```
filebeat.inputs: 
	- type: log 
	  enabled: true 
	  paths: 
		- /var/log/suricata/eve.json 
output.elasticsearch: 
	hosts: ["http://localhost:9200"]
```

4. **Настройка индексов в Kibana**:

Запустите команду для настройки Filebeat и создания необходимых индексов в Kibana:

```
sudo filebeat setup --pipelines --modules suricata
```

5. **Запустите Filebeat**:

```
sudo systemctl start filebeat 
sudo systemctl enable filebeat
```

## Шаг 5: Проверка интеграции

1. **Проверьте логи Suricata**:

Убедитесь, что логи записываются в файл `eve.json`:

```
tail -f /var/log/suricata/eve.json
```

2. **Проверьте индексы в Elasticsearch**:

Используйте следующую команду для проверки созданных индексов:

```
curl -X GET "localhost:9200/_cat/indices?v"
```

3. **Откройте Kibana и создайте индексный шаблон**:

Перейдите в раздел "Discover" в Kibana и создайте новый индексный шаблон, используя `filebeat-*`.

## Шаг 6: Запуск Suricata в режиме анализа PCAP

Теперь вы готовы запустить Suricata для анализа файла PCAP.

1. **Запустите Suricata с указанием файла PCAP**: 
Используйте следующую команду, чтобы запустить анализ трафика из файла PCAP:

```
sudo suricata -c /etc/suricata/suricata.yaml -r путь_к_файлу.pcap -l /var/log/suricata/
```

Здесь `-r` указывает на файл PCAP, который вы хотите проанализировать, а `-l` указывает на директорию для сохранения логов.

2. **Пример команды**: 
Если ваш файл называется `traffic.pcap`, команда будет выглядеть так:

```
sudo suricata -c /etc/suricata/suricata.yaml -r traffic.pcap -l /var/log/suricata/
```

## Шаг 7: Проверка результатов

После завершения анализа вы можете проверить результаты в логах:

1. **Просмотр логов EVE JSON**: 
Логи будут сохранены в файле `/var/log/suricata/eve.json`. Вы можете просмотреть их с помощью команды:

```
cat /var/log/suricata/eve.json | jq . | less
```

Здесь используется `jq` для форматирования JSON-вывода.

2. **Просмотр других логов**: 
Вы также можете просмотреть другие логи, такие как `fast.log`, чтобы увидеть предупреждения и события:

```
cat /var/log/suricata/fast.log | less
```
