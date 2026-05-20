# Tech Repair Website Enhancements & Quality Checklist
**CollabCreate Marketing | Standard Operating Procedure (SOP)**

This document compiles every enhancement and quality-of-life fix implemented during the transition of the **Mobile and Laptop Repairs** website from a raw template to a premium, high-performance, and responsive white-themed storefront. 

Refer to this blueprint for future tech repair site builds to avoid layout displacement, performance stuttering, and text rendering issues.

---

## 1. PERFORMANCE: Image Asset Optimization
High-resolution images directly from digital cameras or screenshots cause massive browser lag, blocking the GPU thread while scaling and decoding.

*   **Maximum Dimensions**: Limit maximum width/height of any image to `1200px` for banners/hero images and `800px` for cards/gallery photos.
*   **Compression Quality**: Compress JPEG/PNG assets to `80%` quality. 
*   **Results achieved**: Shrunk total folder footprint from **85 MB to 2.2 MB** (97.4% bandwidth reduction).
*   **Automation Method**: Use a built-in `.NET System.Drawing` PowerShell script (or Python `Pillow`) to recursively compress images in the distribution folder:
    ```powershell
    Add-Type -AssemblyName System.Drawing
    # Loop over JPG/PNG, calculate ratio, resize using HighQualityBicubic, save at 80 Quality encoder
    ```

---

## 2. AESTHETICS: Premium White Theme & Typography
To transition from a dark "gamer" style to a premium, established tech brand aesthetic:

*   **Colors (CSS Variables)**:
    ```css
    :root {
        --bg-main: #ffffff;             /* Pure white main canvas */
        --bg-card: #ffffff;             /* Clean white elements */
        --bg-alternate: #f8f9fa;        /* Light gray alternate panels */
        --text-main: #111111;           /* High contrast charcoal */
        --text-muted: #5a626a;          /* Slate gray description text */
        --border-color: #e9ecef;        /* Very subtle separator borders */
        --primary: #e02020;             /* Vibrant primary accent red */
        --shadow: 0 8px 30px rgba(0,0,0,0.04); /* Soft, upscale shadow */
    }
    ```
*   **Typography**:
    *   **Headers**: Use **`Outfit`** (geometric sans-serif) at regular font-style (`normal`). Avoid overly aggressive, blocky, or italicized display fonts.
    *   **Body Text**: Use **`Inter`** for clean, legible corporate rendering.
*   **Hero Section Text Readability**:
    *   Increase background linear-gradient overlay opacity to **`0.92`** (`linear-gradient(rgba(255, 255, 255, 0.92), rgba(255, 255, 255, 0.92))`) for white backgrounds. This keeps text fully legible while softening background imagery.

---

## 3. MOBILE RESPONSIVENESS: Grid, Offsets, & Alignments
Prevent text cutting off or layouts breaking on viewports below `768px`.

*   **Hero Headers**: Scale down massive headers using media queries (e.g. `service-hero-title` drops from `4.5rem` to `2.5rem` on mobile) to avoid characters wrapping awkwardly or overflowing the viewport edge.
*   **Container Pad Override**: Shrink inner container padding from `60px` to `20px` on mobile cards (like `.phones-intro`) to keep elements within bounds and eliminate horizontal scrollbars.
*   **Hamburger Menu Overflow**: Force `.nav-links` on mobile to use `box-sizing: border-box; width: 100% !important; left: 0; right: 0;` so it aligns precisely with the screen margins.
*   **Dropdown Alignment**: Prevent repairs menus from rendering slanted or shifting to the right. Reset mobile styles to:
    ```css
    .nav-links .dropdown-menu {
        position: static;
        width: 100%;
        text-align: center;
        background: rgba(0,0,0,0.02);
    }
    ```
*   **Contact Info Symmetry**: Convert flex-row info-items to vertical center alignments on mobile to align circular contact icons, titles, and phone details symmetrically:
    ```css
    .info-item {
        flex-direction: column !important;
        align-items: center !important;
        text-align: center !important;
    }
    ```
*   **Section Ordering**: Swap element rendering orders (e.g., `.about-content` before `.about-images` using Flexbox `order: 1` and `order: 2`) so mobile readers see section headers *before* the photos.

---

## 4. GALLERY LOGIC: Duplicate assets & Crops
*   **Before/After Cropping**: Set comparisons to `object-fit: contain; background: var(--bg-alternate);` instead of `cover` to preserve original screenshots or photos without cropping important details.
*   **Duplicate Gallery Loops**: Change details section image references to `{img3}` ("Good as New") instead of `{img2}` ("In Our Hands") if the process image is already used in the main gallery flow. This avoids duplicate back-to-back photos on mobile.

---

## 5. SCROLLING: Reveal Optimization
*   **Scroll Reveal Hook**: Initialize check functions directly on `DOMContentLoaded`, `load`, and inside an initial `setTimeout` rather than solely on `scroll` events. This ensures that above-the-fold content loads immediately without needing a scroll trigger.
*   **Lower Threshold**: Set `revealPoint = 80` (instead of `150`) to trigger fade-ins earlier, making scrolling feel fluid.
