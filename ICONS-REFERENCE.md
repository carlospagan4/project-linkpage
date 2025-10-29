# Icon Reference Guide

## Using Simple Icons CDN

Simple Icons provides free SVG icons for 3000+ brands. They're perfect for your link tree.

### Icon URL Format
```
https://cdn.simpleicons.org/{brand-name}/{color}
```

### Examples

**Streaming Services:**
- Bandcamp: `https://cdn.simpleicons.org/bandcamp/white`
- Spotify: `https://cdn.simpleicons.org/spotify/white`
- Apple Music: `https://cdn.simpleicons.org/applemusic/white`
- SoundCloud: `https://cdn.simpleicons.org/soundcloud/white`
- YouTube Music: `https://cdn.simpleicons.org/youtubemusic/white`
- Tidal: `https://cdn.simpleicons.org/tidal/white`
- Deezer: `https://cdn.simpleicons.org/deezer/white`
- Amazon Music: `https://cdn.simpleicons.org/amazonmusic/white`

**Social Media:**
- Instagram: `https://cdn.simpleicons.org/instagram/white`
- Twitter/X: `https://cdn.simpleicons.org/x/white`
- Facebook: `https://cdn.simpleicons.org/facebook/white`
- TikTok: `https://cdn.simpleicons.org/tiktok/white`
- YouTube: `https://cdn.simpleicons.org/youtube/white`
- Discord: `https://cdn.simpleicons.org/discord/white`
- Telegram: `https://cdn.simpleicons.org/telegram/white`
- Twitch: `https://cdn.simpleicons.org/twitch/white`

**Other Services:**
- Patreon: `https://cdn.simpleicons.org/patreon/white`
- Ko-fi: `https://cdn.simpleicons.org/kofi/white`
- PayPal: `https://cdn.simpleicons.org/paypal/white`
- Shopify: `https://cdn.simpleicons.org/shopify/white`
- Etsy: `https://cdn.simpleicons.org/etsy/white`
- Linktree: `https://cdn.simpleicons.org/linktree/white`
- Substack: `https://cdn.simpleicons.org/substack/white`
- Mailchimp: `https://cdn.simpleicons.org/mailchimp/white`

### How to Find Any Icon

1. **Visit:** https://simpleicons.org/
2. **Search** for the service name
3. **Copy** the slug (e.g., "spotify", "bandcamp")
4. **Use format:** `https://cdn.simpleicons.org/{slug}/white`

### Adding a New Icon to Your Page

```html
<a href="YOUR_LINK_HERE" class="link-button" target="_blank">
    <span class="icon">
        <img src="https://cdn.simpleicons.org/SERVICE_NAME/white" alt="Service Name">
    </span>
    Service Name
</a>
```

### Color Options

You can change the icon color by modifying the URL:
- White: `/white`
- Black: `/black`
- Custom hex: `/FF5733` (without #)
- Brand color: omit color parameter

**Example with brand color:**
```html
<img src="https://cdn.simpleicons.org/spotify" alt="Spotify">
<!-- This will use Spotify's green brand color -->
```

### Alternative: Using Emojis (No External Dependencies)

If you want zero external dependencies, you can use emojis:

```html
<a href="https://spotify.com" class="link-button" target="_blank">
    <span class="icon">🎧</span>
    Spotify
</a>
```

**Emoji suggestions:**
- 🎵 Music/Audio
- 🎧 Headphones/Listening
- ▶️ Play button
- 📱 App/Mobile
- 🌐 Website
- 📧 Email
- 💬 Chat/Discord
- 🛒 Shopping/Merch
- 📰 Newsletter
- 💰 Donations/Support

### Performance Note

Simple Icons CDN is:
- ✅ Fast (CloudFlare CDN)
- ✅ Free
- ✅ No account needed
- ✅ Lightweight SVGs (~1-2KB each)
- ✅ Cached by browsers

**Estimated load time: <100ms for all icons combined**

### Offline Alternative (Download Icons)

If you want to host icons yourself:

1. Visit https://simpleicons.org/
2. Download the SVG files you need
3. Upload to S3 in `/icons/` folder
4. Update image src: `<img src="/icons/spotify.svg" alt="Spotify">`

This gives you complete control but requires managing the files yourself.
