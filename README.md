# CST8915 Lab 5: Containerizing the Algonquin Pet Store with Docker

- **Student Name**: Hye Ran Yoo
- **Student ID**: 041145212
- **Course**: CST8915 Full-stack Cloud-native Development
- **Semester**: Fall 2026

---

## Demo Video

[Watch Demo Video](https://youtu.be/FmRaKQQB_uU)

---

## What I Tested

All tests ran with Docker Desktop on my Windows laptop.

| # | Test | Command | Result |
|---|---|---|---|
| 1 | Order Service unit tests | `npm test` | 17 passed, 0 failed |
| 2 | Each image alone, on a Docker network with RabbitMQ | `docker build`, `docker run` | Order Service answered `Order received`. Product Service returned 3 products. Store Front showed the 3 products. |
| 3 | Full stack built from my Dockerfiles | `docker compose up -d --build --wait` | 4 containers running, RabbitMQ healthy. After a browser order, `order_queue` went from 1 to 2 messages. |
| 4 | Push the images to Docker Hub | `docker tag`, `docker push` | 3 public repositories with the `latest` tag |
| 5 | Full stack from my Docker Hub images only | `docker compose pull`, `docker compose up -d --wait` | `docker compose ps` showed my `lannieyoo/...` images. After a browser order, `order_queue` went from 2 to 3. |
| 6 | Demo video (same stack as test 5) | `rabbitmqctl list_queues name durable messages` | `order_queue` is durable. The count went from 6 to 7. |

---

## Service Repositories

My forks with the Dockerfiles:

- **Order Service** (Node.js): [LannieYoo/order-service-L4](https://github.com/LannieYoo/order-service-L4)
- **Product Service** (Python): [LannieYoo/product-service-python-L4](https://github.com/LannieYoo/product-service-python-L4)
- **Store Front** (Vue.js + Nginx): [LannieYoo/store-front-L4](https://github.com/LannieYoo/store-front-L4)

My Product Service uses **Python**.

---

## Docker Hub Images

| Image | Local size |
|---|---|
| [lannieyoo/order-service](https://hub.docker.com/r/lannieyoo/order-service) | 257 MB |
| [lannieyoo/product-service](https://hub.docker.com/r/lannieyoo/product-service) | 206 MB |
| [lannieyoo/store-front](https://hub.docker.com/r/lannieyoo/store-front) | 96.5 MB |

---

## Store Front API Host

The API host in my Store Front image is **`localhost`**.

- I built the image with `API_HOST=localhost`, so the browser calls `http://localhost:3000` (Order Service) and `http://localhost:3030` (Product Service).
- These addresses are put into the files at build time. To use another host, I need to build the image again.
- The stack runs on my laptop, so the browser and the APIs are on the same machine.

---

## Files

| File | What it is |
|---|---|
| `docker-compose.yaml` | Image-only Compose file. It runs the stack from my Docker Hub images. There is no `build:` section. |
| `.env.example` | Example settings with a placeholder password. Copy it to `.env` and set your own values. |
| `.gitignore` | Keeps the real `.env` out of Git. |

---

## Notes

- **The Store Front image is the smallest.** It uses a multi-stage build. Node.js builds the Vue files in the first stage, and the final image keeps only Nginx and the built files.
- **The queue kept my earlier orders.** `docker compose down` removes the containers and the network, but not the `rabbitmq-data` volume. So the count did not go back to 0.
- **The password is not in this repository.** The real values are in my local `.env` file. In the video, the Compose file shows only the variable names.

---

## Acknowledgments

- Lab instructions: [ramymohamed10/26F_CST8915_Lab5](https://github.com/ramymohamed10/26F_CST8915_Lab5)
