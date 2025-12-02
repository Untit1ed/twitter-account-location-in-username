# Twitter Account Location Display Chrome Extension

A Chrome extension that shows account location text next to Twitter/X usernames. This fork expands the original "flag" behavior to also detect likely VPN/proxy locations and show the app/store source the account appears to be using (Web, Android, iOS/App Store, etc.).

## Features

- Automatically detects usernames on Twitter/X pages
- Queries Twitter's GraphQL API to read an account's declared location and source metadata
- Displays the account's location as inline text next to the username (instead of flag emoji)
- Attempts to surface whether the location looks like a VPN/proxy-derived location
- Shows the app/store source the account activity comes from when available (e.g. `Web`, `Android App`, `App Store`)
- Works with dynamically loaded content (infinite scroll)
- Caches location and source data to minimize API calls and reduce rate-limit pressure

## Installation

1. Clone or download this repository
2. Open Chrome and navigate to `chrome://extensions/`
3. Enable `Developer mode` (toggle in the top right)
4. Click `Load unpacked`
5. Select the directory containing this extension
6. The extension will now be active on Twitter/X pages

## How It Works

1. The extension injects a small page script that can reuse the logged-in user's session to query Twitter's GraphQL API (`AboutAccountQuery`).
2. The content script scans tweets and user cells for usernames and asks the page script for location/source data.
3. The page script makes the API requests and returns structured data including `account_based_in` (location), `location_accurate` and `source`.
4. The content script inserts a small inline label next to the username showing the location or the inferred source information.

## Files

- `manifest.json` - Chrome extension configuration
- `content.js` - Main content script that processes the page and injects `pageScript.js` for API calls
- `pageScript.js` - Runs in page context to capture headers and perform GraphQL requests
- `popup.html` / `popup.js` - Extension UI for toggling the feature
- `README.md` - This file

## API Endpoint

The extension uses Twitter's GraphQL API endpoint:

```
https://x.com/i/api/graphql/XRqGa7EeokUU5kppkh13EA/AboutAccountQuery
```

With variables:

```json
{
  "screenName": "username"
}
```

The response contains location information at:

```
data.user_result_by_screen_name.result.about_profile.account_based_in
```

and additional metadata such as `location_accurate` and `source` when available.

## Limitations

- Requires the user to be logged into Twitter/X
- Only works for accounts that have location information available in their About profile
- Location inference is best-effort — VPNs, proxies, and user-provided text may be misleading
- Rate limiting may apply if making many requests; the extension caches results to reduce calls

## Privacy

- The extension only queries Twitter/X directly from your browser using your session
- No location data is transmitted to third-party servers by this extension
- Location and source metadata are cached locally using Chrome extension storage to reduce API calls

## Troubleshooting

If location labels are not appearing:

1. Make sure you're logged into Twitter/X
2. Check the browser console for errors from `content.js` or `pageScript.js`
3. Verify that the account has location information in their profile
4. The extension may be rate-limited; wait a short while and try again

## License

MIT
