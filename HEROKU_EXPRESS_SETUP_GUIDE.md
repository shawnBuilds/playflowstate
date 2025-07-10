# Heroku Express App Setup Guide

## Overview
This guide walks through creating an Express.js application that serves a minimal HTML page with an iframe pointing to your Rosebud AI content, then deploying it to Heroku.

## Prerequisites
- Node.js installed on your machine
- Git installed on your machine
- Heroku CLI installed (`npm install -g heroku`)
- A Rosebud AI project URL ready to embed

## Step-by-Step Implementation

### Step 1: Initialize the Project
```bash
# Create project directory
mkdir playflowstate
cd playflowstate

# Initialize npm project
npm init -y

# Install Express
npm install express

# Install development dependencies
npm install --save-dev nodemon
```

### Step 2: Create the Express Server
Create `server.js` in the root directory:
```javascript
const express = require('express');
const path = require('path');
const app = express();
const PORT = process.env.PORT || 3000;

// Serve static files from public directory
app.use(express.static('public'));

// Serve the main HTML page
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

// Health check endpoint for Heroku
app.get('/health', (req, res) => {
    res.status(200).json({ status: 'OK', timestamp: new Date().toISOString() });
});

app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
```

### Step 3: Create the HTML Structure
Create a `public` directory and add `index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Bon Portfolio</title>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: #000;
        }
        
        #content-container {
            width: 100%;
            height: 100%;
        }
        
        iframe {
            width: 100%;
            height: 100%;
            border: none;
        }
    </style>
</head>
<body>
    <div id="content-container">
        <iframe src="YOUR_ROSEBUD_AI_URL_HERE"
                frameborder="0"
                allowfullscreen="true"
                allow="autoplay; fullscreen *; geolocation; microphone; camera; midi; monetization; xr-spatial-tracking; gamepad; gyroscope; accelerometer; xr; cross-origin-isolated"
                sandbox="allow-forms allow-scripts allow-same-origin allow-popups allow-pointer-lock allow-top-navigation">
        </iframe>
    </div>
</body>
</html>
```

### Step 4: Configure Package.json
Update your `package.json`:
```json
{
  "name": "playflowstate",
  "version": "1.0.0",
  "description": "Portfolio iframe wrapper for Rosebud AI content",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "engines": {
    "node": "18.x"
  },
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

### Step 5: Create Heroku Configuration
Create `Procfile` in the root directory:
```
web: node server.js
```

### Step 6: Test Locally
```bash
# Start the development server
npm run dev

# Visit http://localhost:3000 in your browser
# You should see your iframe content (or a placeholder if URL not set)
```

### Step 7: Initialize Git Repository
```bash
# Initialize git
git init

# Create .gitignore
echo "node_modules/
.env
.DS_Store
*.log" > .gitignore

# Add and commit files
git add .
git commit -m "Initial commit: Express app with iframe"
```

### Step 7.5: Connect to Remote Repository
**Choose one option:**

**Option A: GitHub**
```bash
# Create a new repository on GitHub first, then:
git remote add origin https://github.com/YOUR_USERNAME/playflowstate.git

# Push to GitHub
git branch -M main
git push -u origin main
```

**Option B: GitLab**
```bash
# Create a new repository on GitLab first, then:
git remote add origin https://gitlab.com/YOUR_USERNAME/playflowstate.git

# Push to GitLab
git branch -M main
git push -u origin main
```

**Option C: Bitbucket**
```bash
# Create a new repository on Bitbucket first, then:
git remote add origin https://bitbucket.org/YOUR_USERNAME/playflowstate.git

# Push to Bitbucket
git branch -M main
git push -u origin main
```

### Step 8: Deploy to Heroku
```bash
# Login to Heroku (if not already logged in)
heroku login

# Create Heroku app (replace with your desired app name)
heroku create your-app-name-here

# Add Heroku remote
heroku git:remote -a your-app-name-here

# Deploy to Heroku
git push heroku main

# Open the deployed app
heroku open
```

**⚠️ Important:** If deployment fails with npm version conflicts, run:
```bash
# Regenerate package-lock.json to match package.json
npm install

# Commit the updated lock file
git add package-lock.json
git commit -m "Fix package-lock.json version conflicts"

# Redeploy
git push heroku main
```

### Step 9: Update the Rosebud AI URL
1. Get your Rosebud AI project URL
2. Replace `YOUR_ROSEBUD_AI_URL_HERE` in `public/index.html` with your actual URL
3. Commit and redeploy:
```bash
git add .
git commit -m "Update Rosebud AI URL"
git push heroku main
```

## File Structure
```
playflowstate/
├── server.js
├── package.json
├── package-lock.json
├── Procfile
├── .gitignore
└── public/
    ├── index.html
    └── favicon.png (optional)
```

## Testing Checklist
- [ ] Local development server runs on http://localhost:3000
- [ ] HTML page loads without errors
- [ ] Iframe displays your Rosebud AI content
- [ ] Page is responsive on different screen sizes
- [ ] Heroku deployment succeeds
- [ ] Deployed app works correctly
- [ ] Health check endpoint responds

## Troubleshooting

### Common Issues:
1. **Port binding errors**: Ensure `process.env.PORT` is used for Heroku
2. **Static file serving**: Verify `public` directory structure
3. **Iframe not loading**: Check CORS settings and URL validity
4. **Heroku deployment fails**: Check Node.js version in `package.json`
5. **Package version conflicts**: If `npm ci` fails, run `npm install` to regenerate `package-lock.json`
6. **Git remote issues**: Ensure remote repository is created before pushing

### Debug Commands:
```bash
# Check Heroku logs
heroku logs --tail

# Check app status
heroku ps

# Restart app if needed
heroku restart
```

## Optional Enhancements

### Add Favicon
1. Copy your favicon image to `public/favicon.png`
2. Add favicon links to `index.html`:
```html
<link rel="icon" type="image/png" href="/favicon.png">
<link rel="shortcut icon" type="image/png" href="/favicon.png">
```

### Future Enhancements
- Add custom domain (optional)
- Implement analytics tracking
- Add loading states for iframe
- Consider adding fallback content
- Implement error handling for iframe failures 