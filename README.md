# Apple Resale Pricing Tool

A full-stack web app to check current Apple product prices on eBay, calculate resale margins, and recommend the best platform (eBay, Facebook Marketplace, or Vinted) to sell on.

## Features

- **This week**: AI-powered weekly resale pick based on demand and margin potential
- **Search**: Find live eBay listings or sample pricing data for any Apple product
- **By price**: Filter Apple products within a specific price range
- **Sell advisor**: Get platform recommendations (profit vs. speed) for the item you're selling

## Local Development

### Prerequisites
- Node.js 18 or newer (for built-in `fetch`)
- No npm dependencies required

### Setup

1. Clone this repository
2. Navigate to the project folder
3. Run the server:
```bash
node server.js
```

4. Open `http://localhost:3000` in your browser

## Deployment to Render

The app works on Render's free tier. Follow these steps:

### 1. Prepare for Deployment

- Ensure you have a GitHub account and push this repo there
- Get your eBay API credentials:
  - Log into [eBay Developer Portal](https://developer.ebay.com/)
  - Create or use an existing app
  - Note your **App ID** and **Cert ID** from the application keys section

### 2. Create a Render Service

1. Go to [Render.com](https://render.com/)
2. Sign up (free) with your GitHub account
3. Click **New +** → **Web Service**
4. Connect your GitHub repository
5. Fill in the deployment settings:
   - **Name**: `apple-resale-tool` (or any name you prefer)
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `node server.js`
   - **Plan**: Free tier is fine

### 3. Add Environment Variables

Before deploying, add your eBay credentials:

1. In the Render service settings, go to **Environment**
2. Add these variables:
   - `EBAY_APP_ID` → paste your App ID from eBay
   - `EBAY_CERT_ID` → paste your Cert ID from eBay
   - `EBAY_MARKETPLACE` → (optional) set to `EBAY_US`, `EBAY_GB`, etc. (defaults to `EBAY_GB`)

3. Click **Deploy** to start the deployment

Your app will be live at `https://your-app-name.onrender.com` in a few minutes.

## API

### GET `/api/search?q=<query>&limit=<n>`

Search for live eBay listings or fall back to sample data.

**Parameters:**
- `q` (string): Product name to search for (e.g., "iPhone 12")
- `limit` (number, optional): Max results to return (default: 15)

**Response:**
```json
{
  "query": "iPhone 12",
  "total": 342,
  "items": [
    {
      "title": "iPhone 12 128GB Space Gray",
      "price": 195.50,
      "currency": "GBP",
      "condition": "New",
      "url": "https://ebay.com/...",
      "image": "https://...",
      "seller": "seller_name"
    }
  ]
}
```

## How Pricing Works

### Sample Data (Fallback)
When eBay API is unavailable, the app uses a local catalog with seeded random pricing. Prices vary by:
- Product base value
- Condition (new/used-good/used-fair)
- A deterministic random function for variety

### Live Data
When eBay API credentials are set, the app queries live eBay listings and displays real market prices.

### Platform Adjustments
The app automatically adjusts recommended pricing for each platform:
- **eBay**: Stays at market price (buyer expects to pay shipping)
- **Facebook Marketplace**: ~10% lower (for faster local cash sales)
- **Vinted**: ~15% lower (buyer pays fees on top)

### Fees & Profit Calculation
- **eBay**: 12.9% final value fee + £0.30 payment processing
- **Facebook Marketplace**: No fees
- **Vinted**: No seller fees (buyer covers buyer protection fee)

## Tech Stack

- **Frontend**: Vanilla JavaScript, HTML/CSS (no frameworks)
- **Backend**: Node.js with native HTTP module
- **API**: eBay Browse API (for live listing data)
- **Hosting**: Render (free tier)

## Notes

- Prices are indicative and not guaranteed sale prices
- Product catalog last updated: 2026-09-11
- Time-to-sell estimates are based on typical behavior, not real data
- The "This week" pick changes every Sunday at midnight (device local time)

## Troubleshooting

**"Sample data shown" message?**
- Your eBay credentials aren't set or are invalid
- Check that `EBAY_APP_ID` and `EBAY_CERT_ID` are in environment variables
- Verify the credentials work in the eBay Developer Portal

**"Search failed" error?**
- Check your internet connection
- Confirm eBay API credentials are correct
- eBay's API may be temporarily down (rare)

**Port 3000 already in use?**
- Set a different port: `PORT=3001 node server.js`
- On Render, the PORT is automatically assigned

## License

MIT
