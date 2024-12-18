# Content-Genie-v1
A Python-based RSS aggregator for Detroit Tigers news.
Certainly! Below is a **comprehensive `README.md`** tailored for your **Detroit Tigers RSS Aggregator** project. This README will help others understand the purpose of your project, how to set it up locally, how to deploy it, and how to contribute.

---

# Detroit Tigers RSS Aggregator

![Detroit Tigers Logo](https://upload.wikimedia.org/wikipedia/en/3/3e/Detroit_Tigers_Primary_Logo.svg)

**Detroit Tigers RSS Aggregator** is a Python-based web application that aggregates the latest news and updates about the Detroit Tigers from multiple RSS feeds. Built with Flask and SQLAlchemy, this application fetches, stores, and displays the most recent articles, ensuring fans stay informed with minimal effort.

## Table of Contents

- [Features](#features)
- [Demo](#demo)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [Clone the Repository](#clone-the-repository)
  - [Set Up Virtual Environment](#set-up-virtual-environment)
  - [Install Dependencies](#install-dependencies)
  - [Configure the Database](#configure-the-database)
- [Usage](#usage)
  - [Fetch RSS Feeds](#fetch-rss-feeds)
  - [Run the Application](#run-the-application)
- [Deployment](#deployment)
  - [Deploying to Heroku](#deploying-to-heroku)
  - [Automated Deployments with GitHub Actions](#automated-deployments-with-github-actions)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)
- [Acknowledgments](#acknowledgments)

---

## Features

- **Multi-Feed Aggregation:** Combines multiple RSS feeds related to the Detroit Tigers from sources like MLB.com, SB Nation, Baseball America, Fangraphs, and MLB Trade Rumors.
- **Database Storage:** Stores fetched articles in a SQLite database using SQLAlchemy ORM.
- **Web Interface:** Provides a user-friendly Flask-based web interface to view the latest news articles.
- **Automated Fetching:** Utilizes GitHub Actions to periodically fetch and update feeds without manual intervention.
- **Deployment Ready:** Easily deployable to platforms like Heroku for live access.

---

## Demo

Access the live application [here](https://detroit-tigers-rss-aggregator.herokuapp.com/) *(Replace with your actual Heroku app URL after deployment)*.

![Screenshot of Detroit Tigers RSS Aggregator](https://i.imgur.com/YourScreenshot.png)

*Replace the above image URL with an actual screenshot of your application.*

---

## Installation

Follow these steps to set up the **Detroit Tigers RSS Aggregator** locally on your machine.

### Prerequisites

Ensure you have the following installed:

- **Python 3.7 or higher:** [Download Python](https://www.python.org/downloads/)
- **Git:** [Download Git](https://git-scm.com/downloads)
- **Virtual Environment Tool:** `venv` (comes with Python)

### Clone the Repository

Open your terminal or Git Bash and run:

```bash
git clone https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git
cd detroit-tigers-rss-aggregator
```

### Set Up Virtual Environment

Create and activate a virtual environment to manage dependencies.

**On Windows (Git Bash):**

```bash
python -m venv venv
source venv/Scripts/activate
```

**On macOS/Linux:**

```bash
python3 -m venv venv
source venv/bin/activate
```

### Install Dependencies

With the virtual environment activated, install the required Python packages:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

*Ensure your `requirements.txt` includes:*

```
Flask==2.3.2
SQLAlchemy==2.0.15
feedparser==6.0.8
gunicorn==20.1.0
psycopg2-binary==2.9.6
```

### Configure the Database

By default, the application uses SQLite for simplicity. However, for production deployments (e.g., Heroku), it's recommended to use PostgreSQL.

**Using SQLite (Default):**

No additional configuration is needed. The database file `feeds.db` will be created automatically.

**Using PostgreSQL:**

1. **Install PostgreSQL:**
   - Follow instructions [here](https://www.postgresql.org/download/) based on your OS.

2. **Set Up the Database:**
   - Create a new PostgreSQL database.

3. **Configure `models.py`:**
   - Modify the `DATABASE_URL` to point to your PostgreSQL database.

   ```python
   import os
   DATABASE_URL = os.environ.get('DATABASE_URL')  # Heroku sets this automatically
   engine = create_engine(DATABASE_URL)
   ```

---

## Usage

### Fetch RSS Feeds

To manually fetch and store the latest RSS feed entries:

1. **Activate Virtual Environment:**

   **On Windows (Git Bash):**

   ```bash
   source venv/Scripts/activate
   ```

   **On macOS/Linux:**

   ```bash
   source venv/bin/activate
   ```

2. **Run the Fetch Script:**

   ```bash
   python fetch_feeds.py
   ```

   *This script parses the defined RSS feeds and stores new entries in the database.*

### Run the Application

1. **Ensure Virtual Environment is Active:**

   *(Refer to the activation steps above.)*

2. **Run the Flask App:**

   ```bash
   python app.py
   ```

   *The application will start locally at `http://localhost:5000`.*

3. **Access the Application:**

   Open your web browser and navigate to [http://localhost:5000](http://localhost:5000) to view the aggregator.

---

## Deployment

Deploying your application ensures it's accessible online. Below are steps to deploy to **Heroku** using **GitHub Actions** for continuous deployment.

### Deploying to Heroku

1. **Install Heroku CLI:**

   Download and install from [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).

2. **Log In to Heroku:**

   ```bash
   heroku login
   ```

3. **Create a Heroku App:**

   ```bash
   heroku create detroit-tigers-rss-aggregator
   ```

4. **Add Heroku Postgres Add-on (Recommended):**

   ```bash
   heroku addons:create heroku-postgresql:hobby-dev
   ```

5. **Set Environment Variables (If Any):**

   ```bash
   heroku config:set SECRET_KEY=your-secret-key
   ```

6. **Deploy Using GitHub Actions:**

   *(Detailed in the next section.)*

### Automated Deployments with GitHub Actions

Automate deployments so that every push to the `main` branch triggers a deployment to Heroku.

1. **Add Heroku API Key to GitHub Secrets:**

   - **Retrieve Heroku API Key:**

     ```bash
     heroku auth:token
     ```

     *Copy the generated token.*

   - **Add to GitHub:**
     - Go to your GitHub repository.
     - Click on **"Settings"** > **"Secrets and variables"** > **"Actions"**.
     - Click **"New repository secret"**.
     - **Name:** `HEROKU_API_KEY`
     - **Value:** *(Paste your Heroku API key)*
     - Click **"Add secret"**.

2. **Create GitHub Actions Workflow:**

   - **Create Directory:**
     ```bash
     mkdir -p .github/workflows
     ```

   - **Create `deploy.yml`:**
     ```bash
     touch .github/workflows/deploy.yml
     ```

   - **Edit `deploy.yml` in VS Code:**

     ```yaml
     name: Deploy to Heroku

     on:
       push:
         branches:
           - main  # Change if your default branch is different

     jobs:
       build:
         runs-on: ubuntu-latest

         steps:
         - name: Checkout Code
           uses: actions/checkout@v3

         - name: Set up Python
           uses: actions/setup-python@v4
           with:
             python-version: '3.10'  # Specify your Python version

         - name: Install Dependencies
           run: |
             python -m pip install --upgrade pip
             pip install -r requirements.txt

         - name: Deploy to Heroku
           uses: akhileshns/heroku-deploy@v3.12.12
           with:
             heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
             heroku_app_name: "detroit-tigers-rss-aggregator"  # Replace with your Heroku app name
             heroku_email: "your-email@example.com"           # Replace with your Heroku account email
     ```

     **Important:**
     - Replace `"detroit-tigers-rss-aggregator"` with your actual Heroku app name.
     - Replace `"your-email@example.com"` with your Heroku account email.

   - **Commit and Push Workflow:**
     ```bash
     git add .github/workflows/deploy.yml
     git commit -m "Add GitHub Actions workflow for Heroku deployment"
     git push
     ```

3. **Monitor GitHub Actions:**

   - Navigate to the **"Actions"** tab in your GitHub repository.
   - Ensure the **"Deploy to Heroku"** workflow runs successfully.
   - Upon success, your application is deployed to Heroku.

4. **Access Your Deployed Application:**

   Visit `https://detroit-tigers-rss-aggregator.herokuapp.com/` *(Replace with your actual Heroku app URL)* to see your live RSS aggregator.

---

## Project Structure

```
detroit-tigers-rss-aggregator/
├── app.py
├── fetch_feeds.py
├── models.py
├── Procfile
├── README.md
├── requirements.txt
├── runtime.txt
├── .gitignore
├── .github/
│   └── workflows/
│       └── deploy.yml
└── templates/
    └── index.html
```

- **app.py:** Main Flask application file.
- **fetch_feeds.py:** Script to fetch and parse RSS feeds.
- **models.py:** Defines the database models using SQLAlchemy.
- **Procfile:** Specifies the commands to run the app on Heroku.
- **requirements.txt:** Lists all Python dependencies.
- **runtime.txt:** Specifies the Python version for Heroku.
- **.gitignore:** Lists files and directories to be ignored by Git.
- **.github/workflows/deploy.yml:** GitHub Actions workflow for automated deployments.
- **templates/index.html:** HTML template for the web interface.

---

## Contributing

Contributions are welcome! Follow these steps to contribute to the project:

1. **Fork the Repository:**
   - Click the **"Fork"** button at the top-right of the repository page.

2. **Clone Your Fork:**
   ```bash
   git clone https://github.com/YourUsername/detroit-tigers-rss-aggregator.git
   cd detroit-tigers-rss-aggregator
   ```

3. **Create a New Branch:**
   ```bash
   git checkout -b feature/YourFeatureName
   ```

4. **Make Changes and Commit:**
   ```bash
   git add .
   git commit -m "Add feature XYZ"
   ```

5. **Push to Your Fork:**
   ```bash
   git push origin feature/YourFeatureName
   ```

6. **Create a Pull Request:**
   - Navigate to your fork on GitHub.
   - Click **"Compare & pull request"**.
   - Provide a descriptive title and details about your changes.
   - Submit the pull request.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Contact

**Nickolai Brennan**  
Email: [your-email@example.com](mailto:your-email@example.com)  
GitHub: [@Nickolai-Brennan](https://github.com/Nickolai-Brennan)  
LinkedIn: [Your LinkedIn Profile](https://www.linkedin.com/in/yourprofile/)  

---

## Acknowledgments

- [Flask](https://flask.palletsprojects.com/) - The web framework used.
- [SQLAlchemy](https://www.sqlalchemy.org/) - ORM for database interactions.
- [Heroku](https://www.heroku.com/) - Platform for deploying the application.
- [GitHub Actions](https://github.com/features/actions) - For automating deployments.
- [Feedparser](https://pythonhosted.org/feedparser/) - Library for parsing RSS feeds.
- [Detroit Tigers](https://www.mlb.com/tigers) - The team the aggregator focuses on.

---

**Happy Coding and Go Tigers!** 🐯⚾️

---

*Feel free to customize this README further to better fit your project's specifics and personal preferences.*
