# CV_template_AWS

### Write code and make a repo

### Setup aws ec2 instance and download a keypair pem file

### Github repo settings action secrets: 
- SSH_PRIVATE_KEY :: contents of the pem file
- REMOTE_HOST :: public ipv4 dns
- REMOTE_USER :: ubuntu
- TARGET :: home

### Github workflow file

#### for html
```yml
name: Deploy HTML CI/CD

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Deploy to Server (example via SSH)
        run: |
          ssh -i ${{ secrets.SSH_PRIVATE_KEY }} ${{ secrets.REMOTE_USER }}@${{ secrets.REMOTE_HOST }} "cd /var/www/html && git pull && systemctl restart apache2"

```

#### for python
```yml
name: Deploy Python CI/CD

on:
  push:
    branches:
      - main

jobs:
  # Job 1: Setup, Install dependencies, and Run Tests
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.x'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt

      - name: Run tests
        run: |
          pytest

  # Job 2: Deploy to server (e.g., EC2)
  deploy:
    runs-on: ubuntu-latest
    needs: test  # Run deploy only if tests pass

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Deploy to EC2
        run: |
          ssh -i ${{ secrets.EC2_SSH_KEY }} ec2-user@your-ec2-public-dns "cd /path/to/your/app && git pull && restart-app-command"

```

#### for a django sockets application
```yaml
name: Django App CI/CD with Poetry

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.10'

      - name: Install Poetry
        run: |
          curl -sSL https://install.python-poetry.org | python3 -
          echo "$HOME/.local/bin" >> $GITHUB_PATH

      - name: Install dependencies
        run: |
          poetry install --no-dev  # Install production dependencies

      - name: Set environment variables
        run: |
          echo "DJANGO_SECRET_KEY=${{ secrets.DJANGO_SECRET_KEY }}" >> $GITHUB_ENV
          echo "DJANGO_DEBUG=False" >> $GITHUB_ENV
          echo "DJANGO_ALLOWED_HOSTS=channels-demo.onrender.com" >> $GITHUB_ENV
          echo "DATABASE_HOST=${{ secrets.DATABASE_HOST }}" >> $GITHUB_ENV
          echo "DATABASE_PORT=5432" >> $GITHUB_ENV
          echo "DATABASE_USER=${{ secrets.DATABASE_USER }}" >> $GITHUB_ENV
          echo "DATABASE_PASSWORD=${{ secrets.DATABASE_PASSWORD }}" >> $GITHUB_ENV
          echo "DATABASE_NAME=${{ secrets.DATABASE_NAME }}" >> $GITHUB_ENV
          echo "MONGODB_CLUSTER=${{ secrets.MONGODB_CLUSTER }}" >> $GITHUB_ENV
          echo "MONGODB_USERNAME=${{ secrets.MONGODB_USERNAME }}" >> $GITHUB_ENV
          echo "MONGODB_PASSWORD=${{ secrets.MONGODB_PASSWORD }}" >> $GITHUB_ENV
          echo "MONGODB_DBNAME=${{ secrets.MONGODB_DBNAME }}" >> $GITHUB_ENV
          echo "REDIS_HOST=${{ secrets.REDIS_HOST }}" >> $GITHUB_ENV
          echo "REDIS_PORT=6379" >> $GITHUB_ENV

      - name: Start Django app with Daphne
        run: |
          poetry run python -m server_prod
        env:
          DJANGO_SETTINGS_MODULE: "backend.settings.prod"
```