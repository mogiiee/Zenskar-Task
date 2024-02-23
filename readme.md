
# Zenskar Assignment: Back End Engineer Intern

This project integrates your local customer catalog with external services like Stripe and potentially Salesforce. It uses FastAPI for the web server, Celery for asynchronous task processing, Docker for containerization, and Ngrok for local webhook testing.

## Table of Contents
- Getting Started
- Running with Docker
- Running Locally
- Setting Up Ngrok for Webhooks
- Environment Variables
- API Endpoints
### Prerequisites

- Python 3.9+
- Docker & Docker Compose (for Docker setup)
- A Stripe account for testing Stripe integration

```bash
git clone https://github.com/mogiiee/Zenskar-Task.git
cd Zenskar-Task
```

## Running with Docker

This method uses Docker to run the FastAPI server, Celery worker, and RabbitMQ. It has a docker-compose up file which can start all the docker container at the same time for the ease of deployment.

### Build and Start Services

```bash
docker-compose up --build
```

This command builds the Docker images and starts the containers defined in `docker-compose.yml`. 

Please wait for about 10 seconds for the server to start up. You should see something like this 

![success docker compose up](https://cdn.discordapp.com/attachments/991052554802712586/1210497998845509643/Screenshot_2024-02-23_at_1.36.45_PM.png?ex=65eac71c&is=65d8521c&hm=9f3e8331e187353f61d78bb1f1b2f1a13a72896243147817c7dfabf1d74e3c45&)

its okay for the celery worker to try 3 or 4 times to connect to the server. 

![this is okay](https://cdn.discordapp.com/attachments/991052554802712586/1210498157696262144/Screenshot_2024-02-23_at_1.37.12_PM.png?ex=65eac742&is=65d85242&hm=2cef3485902082ec8fa2ef2121a3790122c237d789ec46e0da72662448e82166&)

![this too is okay](https://cdn.discordapp.com/attachments/991052554802712586/1210498158073876551/Screenshot_2024-02-23_at_1.37.24_PM.png?ex=65eac742&is=65d85242&hm=00e6867d432d36745e59cb8fb33bfd4a97a4728fb773d660eb628492e24c8c70&)


both of these scenarios are okay just wait for about 10 seconds.


### Accessing the Application

- FastAPI server will be accessible at http://localhost:8000/docs or http://0.0.0.0:8000/docs


- RabbitMQ management interface at http://localhost:15672 (default credentials: guest/guest)

## Running Locally

For running the application without Docker, follow these steps. Although it is not recommended as you might face alot of errors.

### Virtual Environment Setup

```bash
python -m venv virtualenv
source virtualenv/bin/activate  
(linux)
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Start the FastAPI Server

```bash
uvicorn app.main:app --reload
```

### Start the Celery Worker

In a new terminal session, activate the virtual environment and run:

```bash
celery -A app.worker.celery_worker worker --loglevel=info
```

## Setting Up Ngrok for Webhooks


Ngrok allows you to expose your local server to the internet, facilitating webhook testing.

1. Download and install Ngrok from [Ngrok's website](https://ngrok.com/download).
2. Start Ngrok to expose port 8000:

   ```bash
   ./ngrok http 8000 or ngrok http 8000
   ```

3. Copy the forwarding URL provided by Ngrok (e.g., `https://<random_string>.ngrok.io`).

4. Add this URL in the [webhook section](https://dashboard.stripe.com/test/webhooks/) in the Dashboard of stripe.

**Remember to add URL/webhooks/stripe at the end** else you would get a 400 error as it wont hit the right endpoint. Been there done that lol.

## Environment Variables

Create a `.env` file or just populate the `.env.sample` file in the root directory and add the following variables:

- STRIPE_SECRET_KEY='sk_test.... replace it here'
- STRIPE_PUBLISHABLE_KEY='pk_test_.... replace it here although this is not used in the backend'secret.

there is another .env.sample file in `app/webhook_handler/.env.sample` you can poplulate it with 

- SIGNING_SECRET="get it from the Stripe dashboard> webhook> signing secret"

### Obtaining Stripe Environment Variables

1. **API Key**: Found in your Stripe Dashboard under Developers > API keys.
2. **Endpoint Secret**: Create a webhook endpoint in Stripe Dashboard (Developers > Webhooks) using your Ngrok URL. Use the secret provided there.

## API Endpoints

- **Greeting to the people of Zenskar with love**: `GET /`
- **Create Customer**: `POST /customers/`
- **Update Customer**: `PUT /customers/{customer_id}`
- **Delete Customer**: `DELETE /customers/{customer_id}`
- **Stripe Webhook**: `POST /webhooks/stripe`

## Screenshots
- main dashboard of all the endpoints using swagger UI. Can also use Postman if that's more up your alley.

![main dashboard](https://cdn.discordapp.com/attachments/991052554802712586/1210499211716722748/Screenshot_2024-02-23_at_1.41.33_PM.png?ex=65eac83e&is=65d8533e&hm=5a6ed17bfeafcf7b69c49ef2b8dc42bd985bb0f99d91cf7293e8ec7c4800fd0a&)

- Be sure to check these events while creating the webhook

![webhook opts](https://cdn.discordapp.com/attachments/991052554802712586/1210502706339385364/Screenshot_2024-02-23_at_1.55.28_PM.png?ex=65eacb7f&is=65d8567f&hm=98eea36fb327e9de9777b59b4d57f9e0138eaad4402a8421541db8517fe0525d&)

- My first customer Christopher Nolan when made directly on the dashboard, will be reflected in app/data/database.db

![customer](https://cdn.discordapp.com/attachments/991052554802712586/1210503185710579738/Screenshot_2024-02-23_at_1.57.22_PM.png?ex=65eacbf1&is=65d856f1&hm=62f8ce65b8da6cd12c957df632805425dc9c7b2d636dae69465452933f671a53&)

- all the customers can be created, updated and deleted from the dashboard and everything will reflect in the local database

![all customers](https://cdn.discordapp.com/attachments/991052554802712586/1210504283984891955/Screenshot_2024-02-23_at_2.01.42_PM.png?ex=65eaccf7&is=65d857f7&hm=c1c6e5472e99e5ae9721c620ce476d3fe2afb658a60696a0779fd33186a839a8&)

- creation endpoint

![creation](https://cdn.discordapp.com/attachments/991052554802712586/1210504649157644348/Screenshot_2024-02-23_at_2.03.07_PM.png?ex=65eacd4e&is=65d8584e&hm=f2acb360c7ec4e3978fb58adca43760aee1509f3e54c991de895517af5986278&)

- update endpoint

![update](https://cdn.discordapp.com/attachments/991052554802712586/1210504913172434984/Screenshot_2024-02-23_at_2.04.13_PM.png?ex=65eacd8d&is=65d8588d&hm=01afdaa8905199c91546854dc37f54c0b4854ae3fd272c091e3639978ea90e00&)

- delete endpoint
![delete](https://cdn.discordapp.com/attachments/991052554802712586/1210505176230662164/Screenshot_2024-02-23_at_2.05.17_PM.png?ex=65eacdcc&is=65d858cc&hm=47a234f7e88c467e49b684e3437a964d32497a6ebdf5541cd27dafcc18372ed3&)

- local database
![localdb](https://cdn.discordapp.com/attachments/991052554802712586/1210505292329132112/Screenshot_2024-02-23_at_2.05.45_PM.png?ex=65eacde7&is=65d858e7&hm=2e015386549695b942562a8fa1fc4517e5a42089fdb09bae9e70f4a525f54941&)

