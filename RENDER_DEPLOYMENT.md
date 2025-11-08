# Render Deployment Guide for ChronoFlow

This guide will help you deploy ChronoFlow to Render.com.

## Prerequisites

1. A GitHub account
2. A Render.com account (sign up at https://render.com)
3. Your code pushed to a GitHub repository

## Deployment Steps

### Step 1: Push Code to GitHub

1. Initialize git (if not already done):
   ```bash
   git init
   git add .
   git commit -m "Prepare for Render deployment"
   ```

2. Create a repository on GitHub and push:
   ```bash
   git remote add origin https://github.com/yourusername/chronoflow.git
   git branch -M main
   git push -u origin main
   ```

### Step 2: Create MongoDB Database on Render

1. Go to your Render Dashboard: https://dashboard.render.com
2. Click "New +" and select "MongoDB"
3. Configure:
   - **Name**: `chronoflow-db`
   - **Database**: `chronoflow`
   - **User**: `chronoflow`
   - **Plan**: Free (or choose paid for production)
4. Click "Create Database"
5. **IMPORTANT**: Copy the "Internal Database URL" - you'll need this for the web service

### Step 3: Deploy Web Service on Render

#### Option A: Using render.yaml (Recommended)

1. Go to Render Dashboard
2. Click "New +" and select "Blueprint"
3. Connect your GitHub repository
4. Render will automatically detect `render.yaml`
5. Review the configuration and click "Apply"

#### Option B: Manual Deployment

1. Go to Render Dashboard
2. Click "New +" and select "Web Service"
3. Connect your GitHub repository
4. Configure the service:
   - **Name**: `chronoflow`
   - **Environment**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn app:app`
   - **Plan**: Free (or choose paid for production)

5. **Environment Variables**:
   - Click "Environment" tab
   - Add the following:
     - **Key**: `MONGO_URI`
     - **Value**: Paste the Internal Database URL from Step 2
     - **Key**: `PORT`
     - **Value**: `10000` (Render automatically sets this, but good to be explicit)
     - **Key**: `PYTHON_VERSION`
     - **Value**: `3.11.0`

6. Click "Create Web Service"

### Step 4: Wait for Deployment

- Render will automatically build and deploy your application
- This may take 5-10 minutes on first deployment
- You can watch the build logs in real-time

### Step 5: Access Your Application

- Once deployed, Render will provide you with a URL like: `https://chronoflow-xxxx.onrender.com`
- Your application should be accessible at this URL
- Test the `/health` endpoint: `https://chronoflow-xxxx.onrender.com/health`

## Post-Deployment

### Verify Deployment

1. Visit your Render URL
2. Check the health endpoint: `https://your-app.onrender.com/health`
3. Test signup/login functionality
4. Test adding events

### MongoDB Connection String Format

Render provides MongoDB connection strings in this format:
```
mongodb://<username>:<password>@<host>:<port>/<database>?authSource=admin
```

The application automatically uses the `MONGO_URI` environment variable, so just paste the connection string from Render.

## Troubleshooting

### Application won't start

1. Check build logs in Render Dashboard
2. Verify all dependencies in `requirements.txt` are correct
3. Check that `gunicorn` is in `requirements.txt`
4. Verify the start command: `gunicorn app:app`

### MongoDB connection errors

1. Verify `MONGO_URI` environment variable is set correctly
2. Use the "Internal Database URL" (not External) for better performance
3. Check that the database name matches: `chronoflow`
4. Verify database is running (check Render Dashboard)

### Static files not loading

1. Verify `index.html`, `app.js`, `styles.css` are in the root directory
2. Check browser console for 404 errors
3. Verify file paths in `index.html` are correct

### CORS errors

- The application has CORS enabled for all origins
- If you still see CORS errors, check the browser console
- Verify the API calls are using the correct URL

## Free Tier Limitations

- Applications spin down after 15 minutes of inactivity
- First request after spin-down may take 30-60 seconds
- 750 hours of free runtime per month
- Limited to 512MB RAM

## Upgrading to Paid Plan

For production use, consider upgrading:
- No spin-downs
- More resources
- Better performance
- Custom domains
- SSL certificates

## Environment Variables Reference

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `MONGO_URI` | MongoDB connection string | Yes | `mongodb://localhost:27017/chronoflow` |
| `PORT` | Port to run the application | No | `5000` (Render sets this automatically) |
| `DEBUG` | Enable debug mode | No | `False` |
| `PYTHON_VERSION` | Python version | No | `3.11.0` |

## Support

For issues:
1. Check Render logs: Dashboard > Your Service > Logs
2. Check application logs in Render
3. Test locally first to isolate issues
4. Visit Render docs: https://render.com/docs

## Your Render URL

Once deployed, your application will be available at:
**https://chronoflow-xxxx.onrender.com**

Replace `xxxx` with your actual service ID.

---

**Note**: On the free tier, your app may spin down after inactivity. The first request after spin-down may take longer to respond.

