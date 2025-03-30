# Automating Weather Updates with a Python Bot on Synology NAS

Ever wanted to automate weather updates to your social media or messaging platform? I recently built a simple and efficient weather bot using Python and scheduled it to run automatically on my Synology NAS. Here's a step-by-step guide on how I did it.

## What Does the Bot Do?

My bot fetches weather data using an API and posts regular weather updates at set times of the day (e.g., 6:00 AM, 10:00 AM, 12:00 PM, and 5:00 PM).

## Tools and Technologies

- **Python 3.8**
- **Synology NAS (DSM)**
- **Python libraries:**
  - `schedule`
  - `tweepy` (for Twitter integration)
  - `requests`
  - `python-dotenv`
  - `pytz`

## Step-by-Step Setup

### Step 1: Install Dependencies

SSH into your NAS and install the Python libraries:

```bash
python3 -m pip install schedule tweepy requests python-dotenv pytz
```

(If pip isn't available on your NAS, you can use `python3 -m ensurepip` first.)

### Step 2: Prepare Your Python Script

Here's the basic structure of my bot (`main.py`):

```python
import schedule
import time
import requests
import tweepy
from dotenv import load_dotenv
import os
import pytz
import datetime

load_dotenv()

def post_weather():
    # Example fetching weather data
    weather_response = requests.get("YOUR_WEATHER_API_URL")
    weather_data = weather_response.json()
    
    # Example Twitter integration
    auth = tweepy.OAuthHandler(os.getenv('API_KEY'), os.getenv('API_SECRET'))
    auth.set_access_token(os.getenv('ACCESS_TOKEN'), os.getenv('ACCESS_SECRET'))
    api = tweepy.API(auth)

    message = f"Current temperature: {weather_data['temp']}°C, condition: {weather_data['condition']}"
    api.update_status(message)

schedule.every().day.at("06:00").do(post_weather)
schedule.every().day.at("10:00").do(post_weather)
schedule.every().day.at("12:00").do(post_weather)
schedule.every().day.at("17:00").do(post_weather)

if __name__ == "__main__":
    while True:
        schedule.run_pending()
        time.sleep(1)
```

**Remember:** Replace `YOUR_WEATHER_API_URL` and credentials (`API_KEY`, etc.) with your own data.

### Step 3: Schedule Your Bot on Synology NAS

To run your script automatically:

1. Go to **Control Panel > Task Scheduler** on your Synology DSM.
2. Create a new **Scheduled Task** > **User-defined script**.
3. Set it to run daily at desired times (for example, 5:59 AM to run at 6:00 AM).

Example task script:

```bash
cd "/var/services/homes/your_username/Weather-bot"
/bin/python3 main.py
```

Replace `/var/services/homes/your_username/Weather-bot` with your actual script path.

## Results

The bot is now running smoothly, automatically posting accurate and timely weather updates!


---

## License

MIT License © JC

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

