# Quest App

A React application for quest.lindsaybrunner.com

## Development

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

## Deployment to Netlify

### Initial Setup

1. Push this repository to GitHub (if not already done)
2. Go to [Netlify](https://app.netlify.com)
3. Click "Add new site" → "Import an existing project"
4. Connect your GitHub repository
5. Configure the build settings:
   - Build command: `npm run build`
   - Publish directory: `dist`
6. Click "Deploy site"

### Setting up the Subdomain

1. In your Netlify site settings, go to **Domain settings**
2. Click **Add custom domain**
3. Enter `quest.lindsaybrunner.com`
4. Netlify will provide DNS records to add:
   - Go to your DNS provider (where lindsaybrunner.com is managed)
   - Add a CNAME record:
     - Name: `quest`
     - Value: (the Netlify subdomain provided, e.g., `your-site.netlify.app`)
   - Or add an A record if Netlify provides an IP address
5. Wait for DNS propagation (can take a few minutes to 48 hours)
6. Netlify will automatically provision an SSL certificate

### Alternative: Using Netlify CLI

```bash
npm install -g netlify-cli
netlify login
netlify init
netlify deploy --prod
```

## Project Structure

```
quest-app/
├── src/
│   ├── App.jsx       # Main app component
│   ├── App.css       # App styles
│   ├── main.jsx      # React entry point
│   └── index.css     # Global styles
├── index.html        # HTML template
├── vite.config.js    # Vite configuration
├── netlify.toml      # Netlify configuration
└── package.json      # Dependencies
```

