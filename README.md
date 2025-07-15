# task21
Написал конфигурацию docker-compose: <br>
```
version: '3.7'

services:
  nginx:
    image: nginx:1.18.0
    volumes:
      - ./logs:/var/log/nginx
    ports:
      - "80:80"

  promtail:
    image: grafana/promtail:latest
    ports:
      - "9080:9080"
    volumes:
      - ./promtail.yaml:/etc/promtail/config.yaml
      - ./logs:/var/log/nginx
    command: ["-config.file=/etc/promtail/config.yaml"]

  grafana:
    image: grafana/grafana:12.0.2
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_SECURITY_ADMIN_USER=admin
    volumes:
      - grafanadata:/var/lib/grafana

  loki:
    image: grafana/loki:3.5.2
    ports:
      - "3100:3100"

volumes:
  grafanadata: {}
```

Потом написал конфигурацию проймтейла: <br>
```
server:
  http_listen_port: 9080

positions:
  filename: /var/log/nginx/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: nginx-logs
    static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/nginx/*.log
```

Вот как это всё выглядит: <br> 
<img width="224" height="200" alt="image" src="https://github.com/user-attachments/assets/c15960ad-b231-463c-85a8-f611310953f9" />



Собственно запустил и вот такой итог: <br>
<img width="1325" height="411" alt="image" src="https://github.com/user-attachments/assets/aa6daf86-a16a-4f9f-90f3-4ba5dc9c2adc" />
<img width="1917" height="546" alt="image" src="https://github.com/user-attachments/assets/5f30c59e-9b39-45cd-b317-ae8fe72026d2" />

В Grafane добавил Loki в Data Source и вывел логи: <br>
<img width="1406" height="316" alt="image" src="https://github.com/user-attachments/assets/450f7c4b-01ec-482a-a6c6-68b1968da10e" />


<img width="1392" height="577" alt="image" src="https://github.com/user-attachments/assets/992c2d8c-1c34-4661-9383-972859bc62fd" />



