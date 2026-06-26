# 🎂 Automated Birthday Wisher

A serverless, lightweight Python automation script that runs daily on GitHub Actions to send customized, happy birthday emails to your friends and family. 

It is completely free, secure, and requires no local servers or credit cards!

---

## 🚀 How It Works

```mermaid
graph TD
    A[GitHub Actions Cron Schedule] -->|Every day at 3:30 AM UTC| B(Write birthdays.csv from Secrets)
    B --> C[Run main.py]
    C --> D{Is someone's birthday today?}
    D -->|Yes| E[Select random template from letter_templates/]
    E --> F[Replace [NAME] with actual name]
    F --> G[Log in to SMTP Server using Secrets]
    G --> H[Send personalized birthday email]
    D -->|No| I[Exit Script]
```

1. **Scheduled Trigger**: GitHub Actions runs the workflow daily according to your set cron schedule.
2. **CSV Generation**: The workflow extracts the `BIRTHDAYS_CSV_DATA` secret and dynamically creates `birthdays.csv` in the runner.
3. **Scan & Match**: [main.py](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/main.py) scans `birthdays.csv` to see if the current day and month match any person's birthday.
4. **Draft & Personalize**: If a match is found, the script randomly selects a letter template from [letter_templates/](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates), replaces the `[NAME]` placeholder, and securely sends the email via Google SMTP (`smtp.gmail.com`).

---

## 📂 Project Structure

*   [main.py](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/main.py): Contains the core automation logic.
*   [letter_templates/](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates): Directory containing customizable text files used as email body templates.
    *   [letter_1.txt](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates/letter_1.txt)
    *   [letter_2.txt](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates/letter_2.txt)
    *   [letter_3.txt](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates/letter_3.txt)
*   `.github/workflows/`:
    *   [scheduled.yml](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/.github/workflows/scheduled.yml): The primary workflow that runs daily and executes [main.py](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/main.py).
    *   [test.yml](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/.github/workflows/test.yml): A manual verification workflow to test secrets and environment setup.
*   [requirements.txt](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/requirements.txt): Lists required Python libraries (e.g., `pandas`).
*   [.gitignore](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/.gitignore): Prevents checking in local credentials, `.env` files, or the compiled `birthdays.csv`.

---

## 🛠️ Setup Instructions

Follow these steps to set up and customize the birthday wisher for your own use.

### Step 1: Set Up Repository Secrets

To run securely, the script loads credentials and database content from GitHub Repository Secrets. **Never commit passwords or personal data directly to GitHub.**

1. Go to your repository on GitHub.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.
3. Click **New repository secret** and add the following three secrets:

| Secret Name | Value Example / Description |
| :--- | :--- |
| `MY_EMAIL` | `sender@gmail.com` (The email address sending the wishes) |
| `MY_PASSWORD` | `xxxx xxxx xxxx xxxx` (Your Gmail **App Password**) |
| `BIRTHDAYS_CSV_DATA` | Your birthdays list in CSV format (see template below) |

> [!IMPORTANT]
> **Gmail Users:** You cannot use your regular Gmail password. You must generate an **App Password** from your Google Account settings.
> 1. Go to [Google App Passwords](https://myaccount.google.com/apppasswords).
> 2. Create an App Password for "Mail" (or "Other") and copy the 16-character code.

#### `BIRTHDAYS_CSV_DATA` Format
Format your secret data exactly like this (including the header row):
```csv
name,email,year,month,day
Alice Smith,alice@example.com,1995,6,27
Bob Jones,bob@example.com,1990,12,25
```

---

### Step 2: Customize Letter Templates

You can write as many letter templates as you like!
1. Go to the [letter_templates/](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/letter_templates) folder.
2. Edit existing files or add new `.txt` files.
3. Make sure to include the `[NAME]` placeholder inside the text so the script can replace it with the recipient's name dynamically.

---

### Step 3: Adjust the Automation Schedule

The workflow defaults to running daily.
1. Open [scheduled.yml](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/.github/workflows/scheduled.yml).
2. Locate the `cron` parameter:
   ```yaml
   on:
     schedule:
       - cron: '30 3 * * *'
   ```
3. Change the schedule using standard Cron syntax (in UTC).
   *   `'30 3 * * *'` translates to **3:30 AM UTC** daily.
   *   Use [time.is/UTC](https://time.is/UTC) to check your local time equivalent.

---

### Step 4: Verify Your Configuration

Before letting it run automatically, check if everything is configured correctly:

1. Click on the **Actions** tab in your GitHub repository.
2. Select the **Test Setup** workflow from the left sidebar.
3. Click **Run workflow** -> **Run workflow**.
4. If it succeeds (turns green), your secrets and repository structure are valid!

---

### Step 5: Test Execution Manually

To manually trigger a birthday check and send emails immediately:

1. Go to the **Actions** tab.
2. Select the **Daily Script** workflow.
3. Click **Run workflow** -> **Run workflow**.
4. Check the workflow logs to see the execution status and output print statements.

---

## 🔍 Troubleshooting

### 📬 Emails are not sending / Authentication failed
*   Double check that `MY_EMAIL` is correct and contains no trailing spaces.
*   Ensure `MY_PASSWORD` is a 16-character Gmail App Password (not your primary password).
*   Verify that you are using a provider that allows SMTP relay (Gmail works default on port `587`).

### ⏰ The script didn't run at the exact time
*   GitHub Actions cron schedules can be delayed anywhere from 5 to 30 minutes depending on current GitHub server load. This is expected behavior.
*   **Important**: GitHub pauses cron workflows if there has been no activity in the repository for 60 days. If your script stops running, make a dummy commit (e.g. updating a template or this README) to resume it.

### 🐍 "ModuleNotFoundError: No module named '...'"
*   If you introduce new libraries in [main.py](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/main.py) (e.g., `requests`), remember to add them to [requirements.txt](file:///c:/Users/krish/OneDrive/Documents/Programming/PYTHON/scheduled-tasks/requirements.txt).
