# Deployment Guide for SliTaz Browser VM

## Quick Deployment Options

### 1. GitHub Pages (Recommended - Free & Easy)

**Steps:**
1. Fork or clone this repository
2. Go to repository **Settings** → **Pages**
3. Under "Source", select **main** branch
4. Click **Save**
5. Wait a few minutes for deployment
6. Access at: `https://yourusername.github.io/SilTaz-Emulator/`

**Advantages:**
- ✅ Free hosting
- ✅ HTTPS enabled
- ✅ Custom domain support
- ✅ Automatic deployment on push

**Note:** GitHub Pages serves files with proper CORS headers, making it perfect for this application.

---

### 2. Netlify (Fast & Feature-Rich)

**Option A - Drag & Drop:**
1. Go to [app.netlify.com](https://app.netlify.com/)
2. Drag the repository folder to the deployment area
3. Get instant deployment with custom subdomain

**Option B - Git Integration:**
1. Connect your GitHub account to Netlify
2. Select the repository
3. Configure build settings (none needed - static site)
4. Deploy automatically on every push

**Advantages:**
- ✅ Instant deploys
- ✅ Free tier available
- ✅ CDN distribution
- ✅ Custom domains
- ✅ Automatic HTTPS

---

### 3. Vercel (Next.js Alternative)

**Steps:**
1. Install Vercel CLI: `npm i -g vercel`
2. Navigate to repository: `cd SilTaz-Emulator`
3. Run: `vercel`
4. Follow prompts to deploy

**Advantages:**
- ✅ Lightning fast CDN
- ✅ Free for personal projects
- ✅ Automatic HTTPS
- ✅ Preview deployments for PRs

---

### 4. Cloudflare Pages

**Steps:**
1. Go to [pages.cloudflare.com](https://pages.cloudflare.com/)
2. Connect your GitHub repository
3. Configure project (no build command needed)
4. Deploy

**Advantages:**
- ✅ Cloudflare's global CDN
- ✅ Unlimited bandwidth
- ✅ Free tier
- ✅ DDoS protection

---

### 5. Self-Hosted Server

#### Using Apache

1. Install Apache:
   ```bash
   sudo apt install apache2
   ```

2. Copy files to web root:
   ```bash
   sudo cp -r /path/to/SilTaz-Emulator/* /var/www/html/siltaz/
   ```

3. Enable CORS (create `.htaccess`):
   ```apache
   Header set Access-Control-Allow-Origin "*"
   Header set Access-Control-Allow-Methods "GET, POST, OPTIONS"
   Header set Cross-Origin-Embedder-Policy "require-corp"
   Header set Cross-Origin-Opener-Policy "same-origin"
   ```

4. Restart Apache:
   ```bash
   sudo systemctl restart apache2
   ```

#### Using Nginx

1. Install Nginx:
   ```bash
   sudo apt install nginx
   ```

2. Create config file `/etc/nginx/sites-available/siltaz`:
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;
       root /var/www/siltaz;
       index index.html;

       location / {
           add_header Access-Control-Allow-Origin *;
           add_header Cross-Origin-Embedder-Policy require-corp;
           add_header Cross-Origin-Opener-Policy same-origin;
           try_files $uri $uri/ =404;
       }

       # Enable gzip compression
       gzip on;
       gzip_types text/html text/css application/javascript;
   }
   ```

3. Enable site:
   ```bash
   sudo ln -s /etc/nginx/sites-available/siltaz /etc/nginx/sites-enabled/
   sudo systemctl restart nginx
   ```

---

## Important Deployment Considerations

### CORS Requirements

The application needs to download the SliTaz ISO from `http://mirror.slitaz.org/`. Your hosting platform must:

1. **Allow outbound HTTPS/HTTP requests** to external domains
2. **Set proper CORS headers** for cross-origin requests
3. **Enable SharedArrayBuffer** (requires specific headers)

### Required HTTP Headers

For full functionality, your server should send:

```http
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```

These headers are required for SharedArrayBuffer support (used by WebAssembly).

**Note:** Most static hosting platforms (GitHub Pages, Netlify, Vercel) handle this automatically.

---

### Alternative: Host ISO Locally

If CORS issues persist, you can download and host the ISO yourself:

1. **Download SliTaz ISO:**
   ```bash
   wget http://mirror.slitaz.org/iso/rolling/slitaz-rolling.iso
   ```

2. **Place in repository root**

3. **Update `index.html`:**
   ```javascript
   const ISO_URL = './slitaz-rolling.iso';
   ```

4. **Add to `.gitignore`** (file is large):
   ```
   *.iso
   ```

5. **Deploy ISO separately** via CDN or direct hosting

---

## Optimization Tips

### 1. Enable Compression

**Apache (.htaccess):**
```apache
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/css text/javascript application/javascript
</IfModule>
```

**Nginx:**
```nginx
gzip on;
gzip_types text/html text/css application/javascript;
gzip_min_length 1000;
```

### 2. Set Cache Headers

**For index.html (no cache):**
```http
Cache-Control: no-cache, no-store, must-revalidate
```

**For static assets:**
```http
Cache-Control: public, max-age=31536000
```

### 3. Use CDN

Consider using a CDN like:
- Cloudflare (free tier available)
- AWS CloudFront
- Azure CDN
- Google Cloud CDN

Benefits:
- Faster load times globally
- Reduced bandwidth costs
- DDoS protection
- SSL/TLS certificates

---

## Environment-Specific Instructions

### Local Development

**Python 3:**
```bash
cd SilTaz-Emulator
python3 -m http.server 8000
# Visit: http://localhost:8000
```

**Node.js (using 'serve'):**
```bash
npx serve
# Visit: http://localhost:3000
```

**PHP:**
```bash
php -S localhost:8000
```

### Docker Deployment

Create `Dockerfile`:
```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Create `nginx.conf`:
```nginx
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;
    
    location / {
        add_header Cross-Origin-Embedder-Policy require-corp;
        add_header Cross-Origin-Opener-Policy same-origin;
        try_files $uri $uri/ /index.html;
    }
}
```

Build and run:
```bash
docker build -t siltaz-vm .
docker run -p 8080:80 siltaz-vm
```

---

## Troubleshooting Deployment Issues

### ISO Download Fails

**Problem:** Network error when downloading ISO

**Solutions:**
1. Check CORS headers on your server
2. Try hosting ISO locally
3. Use HTTPS hosting (some browsers block HTTP on HTTPS pages)
4. Check firewall/security settings

### WebAssembly Fails to Load

**Problem:** v86 WebAssembly module doesn't load

**Solutions:**
1. Ensure proper MIME types: `.wasm` → `application/wasm`
2. Check CORS headers for CDN resources
3. Verify SharedArrayBuffer headers are set
4. Try serving from HTTPS

### Slow Performance

**Solutions:**
1. Enable gzip compression
2. Use a CDN for static assets
3. Reduce memory allocation in settings
4. Clear browser cache and IndexedDB

### Storage Quota Exceeded

**Problem:** IndexedDB quota exceeded

**Solutions:**
1. Clear cache in the app (Cache Info → Clear Cache)
2. Reduce virtual disk size in code
3. Check browser storage settings
4. Use incognito mode for testing (fresh storage)

---

## Post-Deployment Checklist

- [ ] Page loads without errors
- [ ] Can click "Start VM" button
- [ ] ISO downloads (or loads from cache)
- [ ] Progress bar shows download status
- [ ] VM boots successfully
- [ ] Console shows logs
- [ ] All buttons are functional
- [ ] Settings can be adjusted
- [ ] Cache persists across page reloads
- [ ] Mobile responsive design works
- [ ] HTTPS enabled (recommended)

---

## Monitoring & Analytics

### Add Google Analytics (Optional)

Add before closing `</head>` tag:
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

### Monitor Performance

Use browser DevTools:
- **Network tab**: Check load times
- **Application tab**: Verify IndexedDB storage
- **Console**: Check for errors
- **Performance tab**: Profile execution

---

## Custom Domain Setup

### GitHub Pages
1. Add `CNAME` file with your domain
2. Configure DNS: `CNAME` record pointing to `username.github.io`
3. Enable HTTPS in repository settings

### Netlify/Vercel
1. Go to domain settings in dashboard
2. Add custom domain
3. Follow DNS configuration instructions
4. SSL certificate is automatically provisioned

---

## Security Best Practices

1. **Use HTTPS**: Always serve over HTTPS in production
2. **Set CSP headers**: Content Security Policy for added security
3. **Regular updates**: Keep v86 library updated
4. **Input validation**: Sanitize any user inputs
5. **Rate limiting**: If self-hosting, implement rate limits
6. **Monitoring**: Set up error tracking (e.g., Sentry)

---

## Support & Resources

- **v86 Documentation**: https://github.com/copy/v86
- **SliTaz Wiki**: http://doc.slitaz.org/
- **WebAssembly**: https://webassembly.org/
- **IndexedDB Guide**: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API

---

**Happy Deploying! 🚀**
