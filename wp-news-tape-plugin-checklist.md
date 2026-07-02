# wp-news-tape-plugin-checklist**

The checklist, tailored specifically for creating the **WP News Tape Plugin**. Mapped the plugin's unique features (multi-source aggregation, dynamic fetching, location/category/keyword filters, free self-promotion, and the premium Network Content Syndication tier) to each phase so nothing generic is left unaddressed.

This tailored checklist ensures the ticker is built securely, the aggregation is reliable, and the unique free self-promotion and premium syndication model is treated as a first-class citizen during development.

---

## 📋 WP News Tape Plugin – Development Checklist

### **Phase 1: Planning & Setup**
- [ ] Define the plugin's purpose: Automated, ticker-style breaking news and curated content feed.
- [ ] Document the two-tier model: Free Core (aggregation + self-promotion engine), Premium Tier (Network Content Syndication).
- [ ] Choose unique plugin name & slug, checking WordPress.org for "news-tape" or similar conflicts.
- [ ] Set up local WordPress development environment with WP_DEBUG enabled.
- [ ] Install code editor (VS Code, PHPStorm) with PHP/WordPress support.

### **Phase 2: Basic Plugin Structure**
- [ ] Create plugin folder: `/wp-content/plugins/wp-news-tape/`
- [ ] Create main plugin file: `wp-news-tape.php` with full plugin header (Name, URI, Description, Version, Author, License: GPL v2+, Text Domain).
- [ ] Add direct access prevention check (`if (!defined('ABSPATH')) exit;`).
- [ ] Define constants: `WPNT_PATH`, `WPNT_URL`, `WPNT_VERSION`.

### **Phase 3: Core Structure (Tailored to Aggregation, Free Self-Promotion & Premium Syndication)**
- [ ] Create main plugin class `WP_News_Tape` with singleton pattern.
- [ ] Create dedicated folders:
    - `/includes/` – Core aggregator engine, cron handlers, feed parsers.
    - `/includes/modules/` – `class-self-promotion.php` (Free), `class-network-syndication.php` (Premium).
    - `/admin/` – Settings panels for source management, premium activation.
    - `/public/` – Ticker renderer, dynamic content loader.
    - `/assets/` – CSS (ticker styles, admin UI), JS (AJAX fetching, scrolling animation).
    - `/languages/` – Translation files.
    - `/templates/` – Overridable ticker HTML templates.
- [ ] Use proper PHP namespacing (`WPNewsTape\...`).

### **Phase 4: Activation/Deactivation Hooks (Setup & Cleanup)**
- [ ] Register activation hook: schedule the dynamic content fetching cron job, create feed-source custom table if necessary, set default option values (ticker speed, default categories/keywords).
- [ ] Register deactivation hook: clear scheduled cron events, flush rewrite rules.
- [ ] Add `uninstall.php`: remove all plugin-specific options from `wp_options`, drop custom feed tables, remove user meta for premium capabilities.

### **Phase 5: Core WordPress Integration (The News Engine)**
- [ ] `init` hook: Register the `wpnt_news_item` custom post type (for curated/promoted/syndicated items), load text domain, check premium license status.
- [ ] `admin_menu` / `admin_bar_menu`: Add "News Tape" settings and "Promoted Content" admin screens.
- [ ] `admin_enqueue_scripts`: Load asset selection UI (Select2 for keywords/categories), location picker.
- [ ] `wp_enqueue_scripts`: Load the ticker CSS/JS only when shortcode or widget is active.
- [ ] `wp_ajax_*` / `wp_ajax_nopriv_*`: Handle dynamic fetching of fresh ticker items via AJAX.
- [ ] Widget: Extend `WP_Widget` to allow dropping the News Tape into any sidebar.
- [ ] Shortcode: `[wp_news_tape]` with attributes for `location`, `category`, `keywords`.

### **Phase 6: Admin Interface (Configuration, Free Self-Promotion & Premium Syndication)**
- [ ] **Free Core Settings Panel:** Multi-source feed URLs, API keys for news providers, default geo-location filter, default category exclusions, keyword allowlist/blocklist.
- [ ] **Self-Promotion Panel (Free):** Simple editor to draft a message, post, or offer. Insertion rules (frequency, position in ticker). Status indicator showing "Active on Your Site."
- [ ] **Premium – Network Syndication Panel:** Content composer. Target selector combining geo-location, category, and keyword logic (AND/OR). "Push to Network" button with confirmation modal. Simple dashboard showing syndication reach (e.g., "Pushed to 47 relevant websites"). License key entry and activation status.
- [ ] Admin Notices: License expiry warnings, syndication push confirmations.
- [ ] Styling: Match WordPress admin aesthetic for all interfaces.

### **Phase 7: Security (Crucial for Aggregation & Syndication)**
- [ ] **Input Sanitization:** Scrub all admin fields—feed URLs (`esc_url_raw`), keywords (`sanitize_text_field`), promoted and syndicated content (`wp_kses` with allowed HTML).
- [ ] **Output Escaping:** Use `esc_html()`, `esc_attr()`, `esc_url()` on all ticker items and admin displays.
- [ ] **Nonces:** Protect all self-promotion "Save" and syndication "Push to Network" actions (`wp_nonce_field`, `check_admin_referer`).
- [ ] **Capability Checks:** Limit premium syndication management to `manage_options`, content authoring to `edit_posts`.
- [ ] **Data Persistence:** `$wpdb->prepare()` for any custom table queries (feed logs, syndication targets).
- [ ] **External Fetching:** Validate and sanitize any externally aggregated news item before display or storage, using `wp_remote_get` with timeouts.

### **Phase 8: Asset Management (The Ticker Experience)**
- [ ] Enqueue core ticker CSS (smooth scrolling, breaking-news styling).
- [ ] Enqueue ticker JS with dependencies (`jquery`). Use `wp_localize_script` to pass AJAX URL, nonce, and user-specific filter config.
- [ ] Conditionally load assets only when the shortcode/widget is present on the page.
- [ ] Version CSS/JS files for cache busting; provide minified versions.

### **Phase 9: Database Operations (Persisting Feeds, Promotions & Syndication)**
- [ ] Use `wp_options` for all configuration: filter defaults, premium license key, syndication API endpoints.
- [ ] `wpnt_news_item` Custom Post Type: Store manually promoted (free) and syndicated (premium) content as CPT entries with post meta for `_wpnt_source` (manual, aggregated, syndicated), `_wpnt_target_rules` (for premium syndication).
- [ ] User Meta: Store per-user syndication preferences or last-push stats.
- [ ] Transients: Cache fetched external news feeds (e.g., `wpnt_feed_cache_{hash}`) to ensure performance and respect API rate limits.

### **Phase 10: Internationalization**
- [ ] Load text domain `wp-news-tape` on `init`.
- [ ] Wrap all user-facing strings in translation functions (`__()`, `_e()`, `_n()` for "1 new article", "X new articles").
- [ ] Generate `.pot` file using WP-CLI from the final plugin code.

### **Phase 11: Error Handling & Debugging (Keeping the Ticker Alive)**
- [ ] Wrap external HTTP requests in try-catch-like checks using `is_wp_error($response)`.
- [ ] Use `WP_Error` for failed premium syndication pushes, returning a meaningful message to the admin via AJAX.
- [ ] Log failed feed fetches or syndication errors using `error_log` (guarded by `WP_DEBUG`).
- [ ] Graceful Degradation: If an external feed is empty or errors out, the ticker hides or displays a cached version instead of breaking.

### **Phase 12: Testing (Simulating Real-World Newsrooms)**
- [ ] Test with various external feed formats (RSS, JSON via REST API).
- [ ] Verify free self-promotion insertion into the ticker stream without page reload.
- [ ] Simulate premium syndication push from one site to multiple others on a local network/multisite install.
- [ ] Test location, category, and keyword filter combinations exhaustively.
- [ ] Performance test: 100+ ticker items, rapid AJAX refresh.
- [ ] Test activation/deactivation/cron cleanup.
- [ ] Test with default WordPress themes (Twenty Twenty-Four) and major page builders.

### **Phase 13: Performance Optimization (Non-Stop Operation)**
- [ ] Use WordPress Cron to pre-fetch and cache external feeds; AJAX loads only cached data.
- [ ] Lazy-load non-critical admin assets for the premium syndication dashboard.
- [ ] Load ticker JS with `defer` or `async` attribute to not block page rendering.

### **Phase 14: Documentation**
- [ ] `readme.txt`: Describe the free and premium tiers clearly. Document the shortcode `[wp_news_tape]`. Provide screenshots of the ticker, settings panel, free self-promotion editor, and premium network push flow.
- [ ] Inline PHPDoc for all major classes (`WP_News_Tape`, `Self_Promotion`, `Network_Syndication`, `Feed_Aggregator`).
- [ ] Document actions/filters for developers (e.g., `wpnt_filter_ticker_item`, `wpnt_after_syndication_push`).
- [ ] Create a user guide section "How to Syndicate Your Content Across the Web."

### **Phase 15: Pre-Release Checklist**
- [ ] Remove all development-only logging and test code.
- [ ] Version bump to 1.0.0 across main file and constants.
- [ ] Confirm self-promotion is fully available in the free version and premium syndication features are locked behind license key.
- [ ] Verify zero PHP notices/warnings with WP_DEBUG on.
- [ ] Test the zip installation on a completely fresh WordPress site.

### **Phase 16: Distribution (Freemium Model Path)**
- [ ] **Free Version:** Submit core plugin (with self-promotion) to WordPress.org repository for broad adoption. Set up SVN.
- [ ] **Premium Tier:** Implement a license key check that pings your commercial API for Network Content Syndication. Prepare the commercial landing page detailing the premium syndication features.
- [ ] Set up an automated update mechanism (e.g., using Freemius or a custom PUC library) for the premium add-on.
- [ ] Prepare support documentation and ticketing system channels.


