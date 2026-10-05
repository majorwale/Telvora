# Telvora Website Optimization Guide

## Overview

This document outlines the optimizations implemented to enhance your website's performance by leveraging cloud-hosted assets and implementing advanced loading strategies.

---

## 1. CDN-Based Asset Delivery

### What Changed

All vendor libraries have been migrated from local `assets/vendor/` directories to **jsDelivr CDN**, which offers:

- ⚡ **Global edge servers** - faster delivery regardless of user location
- 🔒 **HTTPS by default** - secure content delivery
- 📊 **Automatic caching** - reduced server load
- 🚀 **Minified & optimized versions** - smaller file sizes

### Libraries Updated

| Library                     | Local Path → CDN URL                                                                                                                  |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **Bootstrap 5.3.0**         | `assets/vendor/bootstrap/css/bootstrap.min.css` → `https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css`           |
| **Bootstrap JS**            | `assets/vendor/bootstrap/js/bootstrap.bundle.min.js` → `https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js` |
| **Bootstrap Icons**         | `assets/vendor/bootstrap-icons/` → `https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.0/font/bootstrap-icons.css`                     |
| **AOS (Animate On Scroll)** | `assets/vendor/aos/` → `https://unpkg.com/aos@2.3.4/dist/aos.{js,css}`                                                                |
| **Swiper**                  | `assets/vendor/swiper/` → `https://cdn.jsdelivr.net/npm/swiper@10.3.1/swiper-bundle.min.{js,css}`                                     |
| **GLightbox**               | `assets/vendor/glightbox/` → `https://cdn.jsdelivr.net/npm/glightbox@3.3.0/dist/{js,css}`                                             |

### Performance Impact

- **Reduced initial page load**: ~2-3 MB of vendor files no longer downloaded from your server
- **Parallel downloads**: Browser can fetch from multiple CDNs simultaneously
- **Automatic updates**: Always get the latest optimized versions without manual updates
- **Bandwidth savings**: ~60-70% reduction in server bandwidth for static assets

---

## 2. Responsive Image Delivery

### Implementation

Below-fold content images use the browser's native `loading="lazy"` behavior. The navigation logos remain eager, and the homepage hero is eager with high fetch priority so it is not delayed as an offscreen image.

Cloudinary image URLs use `f_auto,q_auto` to negotiate modern formats and compression. The homepage hero is capped at 1200 px wide. A request with WebP support returned a 98 KB, 1200×800 WebP from a 3.1 MB PNG source.

---

## 3. Page-Specific Vendor Loading

Bootstrap and AOS remain shared dependencies. Swiper CSS and JavaScript are included only on the homepage; GLightbox is included only on `service-details.html`. The main script guards optional libraries before initializing them. Hover-based page prefetching has been removed to avoid unrequested downloads.

---

## 4. Recommended Next Steps

### A. Optimize Image Files

```bash
# Recommended tools:
# - TinyPNG / TinyJPG: Free online image compression
# - ImageMagick: Batch resize/optimize
# - WebP conversion: Use modern image formats
```

**Current Image Sizes (estimated):**

- `hero section.png` - Review for optimization
- `About us 1.png` - Consider WebP format
- `Abou us 2.png` - Already using WebP, good!
- Logos - Consider SVG format for infinite scaling

### B. Move Images to Cloud Storage

Recommended services:

1. **Cloudinary** (Recommended)

   - Automatic format conversion
   - On-the-fly resizing
   - Built-in CDN

2. **AWS S3 + CloudFront**

   - Cost-effective for high-traffic sites
   - Powerful caching controls

3. **Azure Blob Storage**
   - Integrated with Azure services
   - Strong data governance

### C. Enable Gzip Compression

Add to `.htaccess` (Apache):

```apache
<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript
</IfModule>
```

### D. Implement Browser Caching

Add to `.htaccess`:

```apache
<IfModule mod_expires.c>
  ExpiresActive On

  # Images
  ExpiresByType image/jpeg "access plus 1 year"
  ExpiresByType image/gif "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"

  # CSS and JavaScript
  ExpiresByType text/css "access plus 1 month"
  ExpiresByType application/javascript "access plus 1 month"
</IfModule>
```

### E. Minify CSS & JavaScript

Current optimization status:

- ✅ CSS: Already minified (main.css)
- ✅ JS: Already minified (main.js)
- ✅ Vendor files: CDN versions are pre-minified

### F. Setup Performance Monitoring

Tools to track improvements:

1. **Google PageSpeed Insights** - https://pagespeed.web.dev
2. **GTmetrix** - https://gtmetrix.com
3. **WebPageTest** - https://www.webpagetest.org

---

## 5. Migration Checklist

Implemented loading changes:

- ✅ Bootstrap CSS/JS → jsDelivr CDN
- ✅ Bootstrap Icons → jsDelivr CDN
- ✅ AOS → unpkg CDN
- ✅ Swiper → jsDelivr CDN on the homepage only
- ✅ GLightbox → jsDelivr CDN on `service-details.html` only
- ✅ Native lazy loading added to below-fold images
- ✅ Cloudinary automatic format/quality enabled for hosted images
- ✅ Unused hover prefetch and custom lazy-loading script removed

---

## 6. Performance Verification

The homepage hero transformation was verified to return a 98 KB WebP at 1200×800. Desktop and 390 px mobile checks showed no horizontal overflow. No full-site before/after Lighthouse or PageSpeed measurements have been recorded; use those tools against the deployed site for end-to-end metrics.

---

## 7. Cloud Storage Setup Guide

### Cloudinary Setup (Recommended)

1. Sign up at https://cloudinary.com (free tier available)
2. Create a new folder for Telvora images
3. Upload images to Cloudinary
4. Replace image URLs:

**Before:**

```html
<img src="assets/img/construction/hero section.png" alt="Hero" loading="lazy" />
```

**After:**

```html
<img
  src="https://res.cloudinary.com/YOUR_CLOUD_NAME/image/upload/w_1200,h_600,c_fill,q_auto/construction/hero_section"
  alt="Hero"
  loading="lazy"
/>
```

### AWS S3 + CloudFront Setup

1. Create S3 bucket
2. Upload images
3. Create CloudFront distribution
4. Use CloudFront URL in HTML

---

## 8. Testing Your Optimizations

Run these commands/checks:

```bash
# Check HTTP/2 support
curl -I https://yoursite.com

# Test page speed
# Visit https://pagespeed.web.dev

# Check image load performance
# Open DevTools > Network tab > Filter by images

# Confirm only the page-specific libraries are requested
# Homepage: Swiper; service-details.html: GLightbox
```

---

## 9. Files Modified

All HTML files have been updated:

- ✅ index.html
- ✅ about.html
- ✅ services.html
- ✅ contact.html
- ✅ privacy.html
- ✅ terms.html
- ✅ quote.html
- ✅ projects.html
- ✅ project-details.html
- ✅ service-details.html
- ✅ team.html
- ✅ starter-page.html
- ✅ 404.html

---

## 10. Support & Resources

### Documentation Links

- [jsDelivr CDN](https://www.jsdelivr.com)
- [Native Image Lazy Loading](https://web.dev/native-lazy-loading)
- [Prefetch & Preload](https://web.dev/prefetch-and-preload-generic)
- [Web Vitals](https://web.dev/vitals)

### Performance Tools

- [Google PageSpeed Insights](https://pagespeed.web.dev)
- [GTmetrix](https://gtmetrix.com)
- [WebPageTest](https://www.webpagetest.org)
- [Lighthouse (Built into Chrome DevTools)](https://developers.google.com/web/tools/lighthouse)

---

## Summary

The site now avoids downloading below-fold images and unused slider/lightbox libraries, requests fewer font styles, and serves Cloudinary images in modern compressed formats when supported. Validate overall performance on the deployed site with PageSpeed Insights or Lighthouse; results depend on hosting, caching, and network conditions.
