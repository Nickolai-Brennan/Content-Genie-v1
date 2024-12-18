Certainly! Let's create a **comprehensive, step-by-step guide** to help you set up your **Detroit Tigers RSS Aggregator**, push your project to GitHub, and deploy it using GitHub Actions to Heroku. This guide will address the issues you've encountered and ensure a smooth deployment process.

---

## **Table of Contents**

1. [Prerequisites](#prerequisites)
2. [Setting Up Your Local Development Environment](#step-1-setting-up-your-local-development-environment)
3. [Initializing Git and Pushing to GitHub](#step-2-initializing-git-and-pushing-to-github)
4. [Deploying to Heroku Using GitHub Actions](#step-3-deploying-to-heroku-using-github-actions)
5. [Final Verification](#step-4-final-verification)
6. [Troubleshooting Common Issues](#step-5-troubleshooting-common-issues)
7. [Additional Tips](#step-6-additional-tips)

---

## **Prerequisites**

Before diving in, ensure you have the following:

- **Git Installed:**  
  Verify by running:
  ```bash
  git --version
  ```
  If not installed, download from [git-scm.com](https://git-scm.com/downloads).

- **Python Installed:**  
  Verify by running:
  ```bash
  python --version
  ```
  If not installed, download from [python.org](https://www.python.org/downloads/).

- **VS Code Installed:**  
  Download from [Visual Studio Code](https://code.visualstudio.com/).

- **GitHub Account:**  
  Sign up at [GitHub.com](https://github.com/) if you haven't already.

- **Heroku Account:**  
  Sign up for a free account at [Heroku](https://www.heroku.com/).

- **Heroku CLI Installed:**  
  Download and install from [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).

---

## **Step 1: Setting Up Your Local Development Environment**

### **1.1. Create and Navigate to Your Project Directory**

1. **Open Git Bash:**  
   Press `Win + R`, type `bash`, and press `Enter` or open Git Bash from the Start menu.

2. **Navigate to Your Desired Location:**  
   For example, to create the project in `C:\Users\Nickolai`, run:
   ```bash
   cd /c/Users/Nickolai
   ```

3. **Create the Project Directory:**
   ```bash
   mkdir detroit-tigers-rss-aggregator
   cd detroit-tigers-rss-aggregator
   ```

### **1.2. Set Up a Virtual Environment**

1. **Create the Virtual Environment:**
   ```bash
   python -m venv venv
   ```

2. **Activate the Virtual Environment:**

   - **For Git Bash on Windows:**
     ```bash
     source venv/Scripts/activate
     ```
   
   - **Verification:**  
     Your terminal prompt should now be prefixed with `(venv)`:
     ```
     (venv) Nickolai@DESKTOP-067Q3MJ MINGW64 /c/Users/Nickolai/detroit-tigers-rss-aggregator
     $
     ```

3. **Upgrade `pip`:**
   ```bash
   pip install --upgrade pip
   ```

### **1.3. Install Required Python Libraries**

```bash
pip install feedparser flask sqlalchemy gunicorn
pip freeze > requirements.txt
```

**Your `requirements.txt` should include:**
```
Flask==2.3.2
SQLAlchemy==2.0.15
feedparser==6.0.8
gunicorn==20.1.0
```

### **1.4. Set Up Project Structure**

Create the necessary files and directories:

```bash
touch app.py fetch_feeds.py models.py README.md
mkdir templates
touch templates/index.html
```

**Resulting Structure:**
```
detroit-tigers-rss-aggregator/
├── app.py
├── fetch_feeds.py
├── models.py
├── README.md
├── requirements.txt
├── runtime.txt
├── Procfile
└── templates/
    └── index.html
```

---

## **Step 2: Initializing Git and Pushing to GitHub**

### **2.1. Initialize Git Repository**

1. **Initialize Git:**
   ```bash
   git init
   ```

2. **Create a `.gitignore` File:**
   ```bash
   touch .gitignore
   ```

3. **Edit `.gitignore`:**  
   Open `.gitignore` in VS Code and add:
   ```
   # Virtual environment
   venv/
   
   # Python cache
   __pycache__/
   
   # SQLite database
   feeds.db
   
   # VS Code settings
   .vscode/
   
   # Compiled Python files
   *.pyc
   *.pyo
   *.pyd
   *.db
   ```

4. **Stage and Commit Files:**
   ```bash
   git add .
   git commit -m "Initial commit - Set up project structure"
   ```

### **2.2. Create GitHub Repository**

1. **Navigate to GitHub:**  
   Go to [GitHub.com](https://github.com/) and log in.

2. **Create a New Repository:**
   - Click the **"+"** icon in the top-right corner and select **"New repository"**.
   - **Repository Name:** `detroit-tigers-rss-aggregator`
   - **Description:** *(Optional)* "A Python-based RSS aggregator for Detroit Tigers news."
   - **Public/Private:** Choose based on your preference.
   - **Initialize Repository:** *Do NOT* check any options like README, `.gitignore`, or License.
   - Click **"Create repository"**.

3. **Copy Repository URL:**  
   It will look like:
   ```
   https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git
   ```

### **2.3. Link Local Repository to GitHub**

1. **Set Remote URL:**
   ```bash
   git remote add origin https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git
   ```

2. **Verify Remote:**
   ```bash
   git remote -v
   ```
   **Expected Output:**
   ```
   origin  https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git (fetch)
   origin  https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git (push)
   ```

### **2.4. Push to GitHub**

1. **Rename Local Branch to `main` (if necessary):**
   ```bash
   git branch -M main
   ```

2. **Push to GitHub:**
   ```bash
   git push -u origin main
   ```

3. **Authenticate:**
   - **HTTPS Method:**  
     GitHub requires a **Personal Access Token (PAT)** instead of a password.
     - [Generate a PAT](https://github.com/settings/tokens) with `repo` scope.
     - When prompted for a password, paste the PAT.
   
   - **SSH Method (Alternative):**  
     - [Set up SSH keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).
     - Change remote URL to SSH:
       ```bash
       git remote set-url origin git@github.com:Nickolai-Brennan/detroit-tigers-rss-aggregator.git
       ```
     - Push using SSH:
       ```bash
       git push -u origin main
       ```

4. **Verify on GitHub:**  
   Visit [https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator](https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator) to ensure your files are present.

---

## **Step 3: Deploying to Heroku Using GitHub Actions**

### **3.1. Modify Your Flask Application for Deployment**

**`app.py`:**
```python
from flask import Flask, render_template
from models import FeedEntry, session
import os

app = Flask(__name__)

@app.route('/')
def index():
    # Retrieve the latest 50 entries ordered by published date
    entries = session.query(FeedEntry).order_by(FeedEntry.published.desc()).limit(50).all()
    return render_template('index.html', entries=entries)

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(debug=False, host='0.0.0.0', port=port)
```

**Explanation:**
- **Environment Variables:** Uses `PORT` from Heroku for dynamic port assignment.
- **Debug Mode:** Disabled for production.

### **3.2. Create a `Procfile`**

1. **Create `Procfile`:**
   ```bash
   echo "web: gunicorn app:app" > Procfile
   ```
   
   **Explanation:**
   - **`web`**: Specifies the process type.
   - **`gunicorn app:app`**: Runs Gunicorn server pointing to your Flask app.

### **3.3. Create a `runtime.txt` File**

1. **Create `runtime.txt`:**
   ```bash
   echo "python-3.10.5" > runtime.txt
   ```
   
   **Note:** Replace `3.10.5` with your Python version. Verify your Python version with:
   ```bash
   python --version
   ```

### **3.4. Commit the Changes**

```bash
git add .
git commit -m "Prepare for Heroku deployment"
git push
```

### **3.5. Set Up Heroku**

1. **Log In to Heroku:**
   ```bash
   heroku login
   ```
   - This will open a browser window for authentication.

2. **Create a New Heroku App:**
   ```bash
   heroku create detroit-tigers-rss-aggregator
   ```
   - **Note:** Replace `detroit-tigers-rss-aggregator` with a unique app name if needed.

### **3.6. Add Heroku API Key to GitHub Secrets**

1. **Retrieve Heroku API Key:**
   ```bash
   heroku auth:token
   ```
   - **Copy** the generated token.

2. **Add the API Key to GitHub:**
   - Go to your GitHub repository.
   - Click on **"Settings"** > **"Secrets and variables"** > **"Actions"**.
   - Click **"New repository secret"**.
   - **Name:** `HEROKU_API_KEY`
   - **Value:** Paste your Heroku API key.
   - Click **"Add secret"**.

### **3.7. Set Up GitHub Actions Workflow**

1. **Create Workflow Directory:**
   ```bash
   mkdir -p .github/workflows
   ```

2. **Create `deploy.yml`:**
   ```bash
   touch .github/workflows/deploy.yml
   ```

3. **Edit `deploy.yml` in VS Code:**

   Open `.github/workflows/deploy.yml` in VS Code and add the following content:

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

4. **Commit and Push Workflow:**
   ```bash
   git add .github/workflows/deploy.yml
   git commit -m "Add GitHub Actions workflow for Heroku deployment"
   git push
   ```

### **3.8. Monitor GitHub Actions**

1. **Navigate to GitHub Repository:**
   - Go to [Your Repository](https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator).

2. **Click on the "Actions" Tab:**
   - You should see the **"Deploy to Heroku"** workflow running.

3. **Wait for Completion:**
   - The workflow will checkout code, set up Python, install dependencies, and deploy to Heroku.
   - Ensure all steps complete successfully.

---

## **Step 4: Final Verification**

1. **Access Your Deployed Application:**
   - Visit `https://detroit-tigers-rss-aggregator.herokuapp.com/` (replace with your actual Heroku app URL if different).
   - You should see your **Detroit Tigers RSS Aggregator** live and displaying the latest news entries.

2. **Verify Database Persistence:**
   - **Note:**  
     SQLite (`feeds.db`) is a file-based database and not ideal for Heroku due to its ephemeral filesystem. Changes to `feeds.db` won't persist across deployments or dyno restarts.
   
   - **Recommendation:**  
     Use **Heroku Postgres** for persistent storage.

### **4.1. Migrate to Heroku Postgres**

1. **Add Heroku Postgres Add-on:**
   ```bash
   heroku addons:create heroku-postgresql:hobby-dev
   ```

2. **Modify `models.py` to Use PostgreSQL:**

   **`models.py`:**
   ```python
   from sqlalchemy import create_engine, Column, Integer, String, Text, DateTime
   from sqlalchemy.ext.declarative import declarative_base
   from sqlalchemy.orm import sessionmaker
   import os

   Base = declarative_base()

   class FeedEntry(Base):
       __tablename__ = 'feed_entries'
       
       id = Column(Integer, primary_key=True)
       title = Column(String(255), nullable=False)
       link = Column(String(500), nullable=False)
       description = Column(Text)
       published = Column(DateTime)
       source = Column(String(100))  # e.g., 'MLB.com', 'SB Nation'

   # Use DATABASE_URL from environment variables
   DATABASE_URL = os.environ.get('DATABASE_URL')

   engine = create_engine(DATABASE_URL)
   Base.metadata.create_all(engine)
   Session = sessionmaker(bind=engine)
   session = Session()
   ```

3. **Install `psycopg2`:**
   ```bash
   pip install psycopg2-binary
   pip freeze > requirements.txt
   ```

4. **Update `requirements.txt`:**
   Ensure `psycopg2-binary` is listed:
   ```
   Flask==2.3.2
   SQLAlchemy==2.0.15
   feedparser==6.0.8
   gunicorn==20.1.0
   psycopg2-binary==2.9.6
   ```

5. **Commit and Push Changes:**
   ```bash
   git add .
   git commit -m "Migrate to Heroku Postgres for persistent database"
   git push
   ```

6. **Run Database Migrations (If Necessary):**
   - If your application requires initial data, consider writing a migration script or using tools like `Flask-Migrate`.

---

## **Step 5: Troubleshooting Common Issues**

### **5.1. Repository Not Found Error**

**Error Message:**
```
remote: Repository not found.
fatal: repository 'https://github.com/YourUsername/detroit-tigers-rss-aggregator.git/' not found
```

**Solution:**
- **Incorrect Remote URL:**  
  Ensure the remote URL uses your actual GitHub username.

  ```bash
  git remote set-url origin https://github.com/Nickolai-Brennan/detroit-tigers-rss-aggregator.git
  ```

- **Repository Doesn't Exist:**  
  Ensure you've created the repository on GitHub under your account.

### **5.2. Virtual Environment Activation Issues**

**Error Message:**
```
bash: venv/Scripts/activate: No such file or directory
```

**Solution:**
- **Ensure Virtual Environment Exists:**
  ```bash
  ls venv/Scripts/
  ```
  - You should see `activate`, `activate.bat`, etc.
  
- **Recreate Virtual Environment:**
  ```bash
  rm -rf venv
  python -m venv venv
  source venv/Scripts/activate
  ```

### **5.3. Heroku Deployment Failures**

**Common Issues:**
- **Missing `Procfile`:** Ensure `Procfile` exists in the root directory.
- **Dependencies Missing:** Ensure all required packages are listed in `requirements.txt`.
- **Incorrect Python Version:** Ensure `runtime.txt` specifies the correct Python version.
- **Database Connection Errors:** Ensure `models.py` correctly references `DATABASE_URL`.

**Solutions:**
- **Check Heroku Logs:**
  ```bash
  heroku logs --tail
  ```
- **Ensure Environment Variables are Set:**  
  Heroku automatically sets `DATABASE_URL` when you add Heroku Postgres.

---

## **Step 6: Additional Tips**

1. **Regular Commits:**
   - Commit changes frequently with clear messages to maintain a clear project history.

2. **Branching:**
   - Use branches to develop new features or fix bugs without affecting the `main` branch.
   ```bash
   git checkout -b feature/new-feature
   ```

3. **Pull Before Push:**
   - If collaborating, always pull the latest changes before pushing.
   ```bash
   git pull origin main
   ```

4. **Use SSH for GitHub:**
   - SSH can simplify authentication.
   - [Set up SSH keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).

5. **Monitor Application Performance:**
   - Use Heroku's dashboard to monitor app performance and logs.

6. **Secure Your Application:**
   - Store sensitive information like API keys using environment variables.
   - Consider using packages like `python-dotenv` to manage environment variables locally.

7. **Backup Your Database:**
   - Regularly back up your Heroku Postgres database.
   - [Heroku Postgres Backups](https://devcenter.heroku.com/articles/heroku-postgres-backups).

---

## **Conclusion**

By following this guide, you've successfully:

1. **Set Up Your Local Development Environment:**  
   Created a project directory, set up a virtual environment, and installed necessary libraries.

2. **Initialized Git and Pushed to GitHub:**  
   Initialized a Git repository, linked it to GitHub, and pushed your code.

3. **Deployed to Heroku Using GitHub Actions:**  
   Configured GitHub Actions to automate deployments to Heroku.

4. **Ensured Database Persistence:**  
   Migrated from SQLite to Heroku Postgres for a persistent database.

5. **Addressed Common Issues:**  
   Troubleshooted errors related to Git and Heroku deployments.

6. **Enhanced Your Workflow with Additional Tips:**  
   Adopted best practices for Git workflows, application security, and performance monitoring.

---

**Next Steps:**

- **Enhance Features:**  
  Add functionalities like search, filtering, or user authentication to your RSS aggregator.

- **Improve UI/UX:**  
  Use CSS frameworks like Bootstrap or Tailwind CSS to enhance the appearance of your web interface.

- **Scale Your Application:**  
  As your application grows, consider optimizing performance and scalability, possibly by integrating caching mechanisms or moving to more robust database solutions.

---

**If you encounter any further issues or have specific questions, feel free to ask! Happy coding and Go Tigers! 🐯⚾️**
