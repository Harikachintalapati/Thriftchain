# ThriftChain 🏷️✨

> **Pre-Loved Ethnic Couture & Antique Jewellery Resale and Rental Marketplace**  
> *"Every piece has a next chapter."*

ThriftChain is a specialized marketplace for secondhand Indian ethnic wear (silk sarees, bridal lehengas) and heritage jewellery (temple necklaces, Kundan bangles). The platform's core innovation allows a **single product listing to support both outright purchase (BUY) and short-term rental (RENT)** concurrently, giving buyers and sellers ultimate flexibility.

---

## 🎨 Visual Identity & Aesthetic

Designed with a bespoke **vintage boutique price-tag visual identity**:
- **Background**: Deep aubergine / plum (`#1E101D` / `#251424`)
- **Card Surfaces**: Warm parchment / cream (`#FAF5EE`)
- **Primary Accent**: Aged brass-gold (`#D4AF37`) — inspired by traditional Indian temple jewellery
- **Buy Badges**: Muted clay / rose (`#B55A6B`)
- **Rent Badges**: Sage green (`#4A7C59`)
- **Signature Swing Tags**: Product cards styled as physical swing price tags complete with a top eyelet hole punch, loop thread, clip-path die-cut angled shoulders, and stitched dashed borders.

---

## 📁 Project Structure

```
thriftchain/
├── index.html          # Main application page (search, filters, catalog grid, modal)
├── css/
│   └── style.css       # Complete design system, swing tag clip-paths & responsive rules
├── js/
│   ├── data.js         # Catalog data model & simulated SQL API service layer
│   └── script.js       # Live search, category/type filtering & modal interactivity
├── db/
│   ├── schema.sql      # SQL DDL script (sellers, categories, products, listings, catalog_view)
│   └── seed.sql        # Realistic SQL DML seed script (AP sellers & ethnic products)
├── assets/             # High-resolution generated product photography
│   ├── kanchipuram_saree.jpg
│   ├── bridal_lehenga.jpg
│   ├── temple_necklace.jpg
│   ├── kundan_bangles.jpg
│   ├── organza_saree.jpg
│   └── navratna_set.jpg
└── README.md           # Documentation
```

---

## 🗄️ Database Architecture (`db/schema.sql` & `db/seed.sql`)

### Key Design Highlight: Decoupled Listings Table
To ensure a single item can have both a **Resale Price** (e.g. ₹18,500) and a **Rental Price** (e.g. ₹2,800 per event with ₹3,000 security deposit) without duplicating product entries or cluttering columns, the data schema separates `products` from `listings`.

```
+----------------+       +------------------+       +-------------------+
|    SELLERS     |       |    PRODUCTS      |       |     LISTINGS      |
+----------------+       +------------------+       +-------------------+
| id (PK)        | <---| | id (PK)          | <---| | id (PK)           |
| name           |       | seller_id (FK)   |       | product_id (FK)   |
| city           |       | category_id (FK) |       | listing_type      |
| rating         |       | title            |       |   ENUM('buy','rent')
+----------------+       | description      |       | price             |
                         | condition_grade  |       | rental_period     |
                         | size             |       | deposit_amount    |
                         | image_url        |       | is_available      |
                         +------------------+       +-------------------+
```

### Constraints & Views
1. **CHECK Constraint**: Ensures `rental_period` is provided when `listing_type = 'rent'` and mandatory `NULL` when `listing_type = 'buy'`.
   ```sql
   CONSTRAINT chk_rental_period_type CHECK (
       (listing_type = 'rent' AND rental_period IS NOT NULL) OR 
       (listing_type = 'buy' AND rental_period IS NULL)
   )
   ```
2. **Indexes**: Added on `products(category_id)` and `listings(listing_type)` for query performance.
3. **`catalog_view`**: A denormalized query view joining products, categories, sellers, and active listings into flat queryable rows.

---

## 🚀 How to Run

Because ThriftChain is built using vanilla web technologies, no build step, package installation, or backend server is required:

1. Double-click `index.html` to open it directly in any modern Web Browser (Chrome, Firefox, Edge, Safari).
2. Alternatively, serve using a standard local HTTP server:
   ```bash
   npx serve .
   # or
   python -m http.server 8000
   ```

---

## 🔍 Features Included

- **Multi-attribute Search**: Matches live against product title, category, description, seller name, city, and condition grade.
- **Category Filter Dropdown**: Dynamically generated categories (Sarees, Lehengas, Statement Necklaces, Bangles & Bracelets).
- **Listing Type Pills**: Filter items by "All", "Buy & Rent Both", "Buy Only", or "Rent Only".
- **Live Result Counter**: Instant update showing matching pieces count ("X pieces found").
- **Clear Filters Button**: One-click reset for all controls with confirmation toast.
- **Swing-Tag Product Cards**: Complete with dual pricing badges for items supporting both buy and rent options.
- **Interactive Detail Modal**: Deep dive into security deposits, rental terms, seller rating, and simulated checkout actions.
- **Accessibility & Motion**: Full `:focus-visible` focus rings, keyboard navigation support (`Escape` closes modal), and `@media (prefers-reduced-motion)` overrides.
