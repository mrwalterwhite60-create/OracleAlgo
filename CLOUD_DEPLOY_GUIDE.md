# ☁️ How to Deploy Your Bot 24/7 (Free)

Since you want the bot to run **without your computer**, you need to host it on a Cloud Server. I have prepared your code for **Render.com** (it's free and easy).

## Step 1: Push Code to GitHub
1. Create a Repository on GitHub.
2. Run these commands in your VS Code terminal to upload the code:
   ```bash
   git init
   git add .
   git commit -m "Deploy Trading Bot"
   # Replace URL with your actual GitHub repo URL
   git remote add origin https://github.com/YOUR_USERNAME/TeamProject2.git 
   git push -u origin master
   ```

## Step 2: Create Web Service on Render
1. Go to [dashboard.render.com](https://dashboard.render.com).
2. Click **New +** -> **Background Worker** (Important: Choose Background Worker, not Web Service, because it's a bot).
3. Connect your GitHub repository.
4. Settings:
   - **Runtime**: Python 3
   - **Build Command**: `pip install -r TeamProject2/requirements.txt`
   - **Start Command**: `python TeamProject2/backend/bot_main.py`
   
## Step 3: Add Variables
In the Render Dashboard (Environment Variables section), add your keys:
- `TELEGRAM_BOT_TOKEN_PROJ2` = `your_telegram_token`
- `GEMINI_API_KEY` = `your_gemini_key`

**Done!** Your bot will now run forever in the cloud. 🚀
