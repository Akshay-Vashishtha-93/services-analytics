# GA4 Events Summary — Services Vertical (Jan 1 - Jun 1, 2026)

## Aggregate Metrics

| # | Service | Total Events | Total Users | Revenue (AED) | Unique Events | Purchases | Purchase Revenue |
|---|---------|-------------|-------------|---------------|---------------|-----------|-----------------|
| 1 | Babysitter/Nanny | 417,860 | 89,965 | 78,739.84 | 19 | 130 | 78,739.84 |
| 2 | Gear Refresh | 61,088 | 16,755 | 12,514.00 | 19 | 82 | 12,514.00 |
| 3 | Home Cleaning | 44,716 | 8,393 | 4,183.20 | 19 | 22 | 4,183.20 |
| 4 | Healthy Home | 32,755 | 6,260 | 3,542.00 | 18 | 8 | 3,542.00 |
| 5 | Party Planning | 197,861 | 55,728 | 0 | 19 | 0 | 0 |
| 6 | Telehealth | 52,073 | 11,012 | 0 | 15 | 0 | 0 |
| 7 | Wallpaper | 114,224 | 29,926 | 0 | 18 | 0 | 0 |
| 8 | Doctor at Home | 5,148 | 868 | 0 | 12 | 0 | 0 |
| 9 | Kids Interior | 24,846 | 4,998 | 0 | 14 | 0 | 0 |
| 10 | Tutor at Home | 20,657 | 4,281 | 570.00 | 14 | 1 | 570.00 |
| **TOTAL** | | **971,228** | **228,186** | **99,549.04** | | **243** | **99,549.04** |

## Revenue Services (Have purchase events with revenue)

| Service | Purchases | Users Who Purchased | Revenue (AED) | AOV (AED) | Purchase Rate (from first_visit) |
|---------|-----------|--------------------|--------------|-----------|---------------------------------|
| Babysitter/Nanny | 130 | 101 | 78,739.84 | 605.69 | 0.11% (101/89,255) |
| Gear Refresh | 82 | 76 | 12,514.00 | 152.61 | 0.46% (76/16,546) |
| Home Cleaning | 22 | 18 | 4,183.20 | 190.15 | 0.22% (18/8,137) |
| Healthy Home | 8 | 8 | 3,542.00 | 442.75 | 0.13% (8/6,186) |
| Tutor at Home | 1 | 1 | 570.00 | 570.00 | 0.02% (1/4,275) |

## Enquiry/Lead Services (No payment, generate_lead is conversion)

| Service | generate_lead | enquiry_started | Lead Rate (from first_visit) |
|---------|--------------|----------------|------------------------------|
| Wallpaper | 16 | N/A (uses booking_started: 46) | 0.05% (16/29,894) |
| Party Planning | 4 | 157 | 0.007% (4/55,627) |
| Kids Interior | 7 | 44 | 0.14% (7/4,977) |

## P0 Bugs Confirmed

1. **Telehealth: 0 purchase events despite 32 place_orders** — Payment gateway not sending purchase events
2. **Home Cleaning: booking_started misfires** — 3.07x per user (452 events / 148 users) instead of 1x
3. **Doctor/Altibbi: checkout_redirect missing** — Event never fires, 0 in data
4. **Tutor at Home: generate_lead missing** — Has 1 purchase but no generate_lead event
5. **Telehealth: service_booking_confirmed (251) > place_order (32)** — Fires on slot selection, not payment confirmation
6. **Wallpaper: service_booking_confirmed (54) > generate_lead (16)** — Same anomaly

## Funnel Conversion Analysis (Booking Services with Purchase)

### Babysitter/Nanny (Best performing)
| Step | Event | Count | Users | Drop from previous |
|------|-------|-------|-------|-------------------|
| 1 | first_visit | 90,295 | 89,255 | - |
| 2 | view_item_list | 7,458 | 4,683 | 94.8% drop |
| 3 | select_item | 8,188 | 2,759 | 41.1% drop |
| 4 | form_start | 2,254 | 1,284 | 53.5% drop |
| 5 | booking_started | 154 | 134 | 89.6% drop |
| 6 | booking_summary_viewed | 356 | 238 | - (higher than booking_started) |
| 7 | service_booking_confirmed | 575 | 352 | - |
| 8 | place_order | 256 | 128 | 63.6% drop |
| 9 | purchase | 130 | 101 | 21.1% drop |

### Gear Refresh
| Step | Event | Count | Users | Drop from previous |
|------|-------|-------|-------|-------------------|
| 1 | first_visit | 16,636 | 16,546 | - |
| 2 | form_start | 367 | 224 | 98.6% drop |
| 3 | booking_started | 81 | 62 | 72.3% drop |
| 4 | booking_summary_viewed | 80 | 62 | 0% drop |
| 5 | place_order | 55 | 38 | 38.7% drop |
| 6 | purchase | 82 | 76 | - (higher than place_order — multi-purchase users) |

### Home Cleaning
| Step | Event | Count | Users |
|------|-------|-------|-------|
| 1 | first_visit | 8,145 | 8,137 |
| 2 | booking_started | 452 | 148 | (BUGGED - misfires 3x) |
| 3 | form_start | 259 | 164 |
| 4 | booking_summary_viewed | 34 | 29 |
| 5 | place_order | 13 | 10 |
| 6 | purchase | 22 | 18 | (higher than place_order — repeat purchases) |

## SQ5-475 Impact: Before vs After (May 12, 2026)

### Events that existed BEFORE SQ5-475 (unchanged):
- page_view, session_start, first_visit, scroll, click, user_engagement
- form_start, form_submit, service_booking_confirmed, purchase

### Events ADDED by SQ5-475 (zero before May 12):
- view_item_list, select_item, view_item_detail
- booking_started, enquiry_started
- booking_summary_viewed, contact_info_entered
- place_order, begin_checkout, add_payment_info
- coupon_applied, view_promotion, select_promotion
- generate_lead, checkout_redirect
- wall_measurement_entered, additional_wall_added, address_entered

### Funnel visibility improvement:
- BEFORE: 4 measurable steps (page_view → form_start → form_submit → purchase)
- AFTER: 12 measurable steps (full journey visibility)
