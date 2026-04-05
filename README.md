services:
  web:
    build: ./web
    depends_on:
      - api
    networks:
      - urbaneats-net

  api:
    build: ./api
    depends_on:
      - db
    restart: unless-stopped
    networks:
      - urbaneats-net

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD: admin123
    networks:
      - urbaneats-net

  cache:
    image: redis:7-alpine
    networks:
      - urbaneats-net

  queue:
    image: rabbitmq:3-management-alpine
    ports:
      - "15672:15672"
    networks:
      - urbaneats-net

  proxy:
    image: nginx:alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "80:80"
    depends_on:
      - web
      - api
    networks:
      - urbaneats-net

networks:
  urbaneats-net:
