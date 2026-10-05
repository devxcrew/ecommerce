# Ecommerce Table Structure

Proposed data plan for the Ecommerce app, from the home page to delivery and after-sales service. The current app is a preview foundation.

## 1. Home Page and Store Setup

Module: `storefront`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_stores` | `platform_organization_ref`, `code`, `name`, `domain`, `default_locale`, `default_currency`, `timezone`, `status` | Defines the store and its trusted organization scope. |
| `ecommerce_storefront_profiles` | `store_id`, `logo_asset_ref`, `tagline`, `about_text`, `support_email`, `support_phone`, `social_links_json`, `footer_text` | Holds the store identity, contact details, and footer content. |
| `ecommerce_navigation_links` | `store_id`, `parent_id`, `menu_key`, `label`, `target_url`, `sort_order`, `status` | Builds the header, category menu, and footer links. |
| `ecommerce_pages` | `store_id`, `slug`, `title`, `content_json`, `seo_title`, `seo_description`, `publication_status`, `published_at` | Stores the home page, About page, contact page, and other content pages. |
| `ecommerce_page_sections` | `page_id`, `section_key`, `kind`, `title`, `sort_order`, `enabled`, `starts_at`, `ends_at` | Arranges hero banners, collections, featured products, brand strips, and seasonal sections. |
| `ecommerce_content_blocks` | `section_id`, `title`, `body`, `asset_ref`, `alt_text`, `action_label`, `action_url`, `product_ref`, `collection_ref`, `sort_order`, `status` | Stores the cards or slides inside a section. Product prices come from the pricing module. |
| `ecommerce_policy_versions` | `store_id`, `policy_kind`, `version_number`, `title`, `content`, `effective_at`, `status` | Keeps dated terms, privacy, shipping, and return policies. Checkout records the accepted version. |

Use sections and content blocks for repeated home-page layouts. Add a separate table only when the content has different business behavior.

## 2. Categories, Collections, and Product Listing

Module: `catalog`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_categories` | `store_id`, `core_category_ref`, `parent_id`, `name`, `slug`, `description`, `asset_ref`, `sort_order`, `status` | Groups products into a category tree. |
| `ecommerce_brands` | `store_id`, `core_brand_ref`, `name`, `slug`, `logo_asset_ref`, `description`, `show_on_storefront`, `status` | Stores the brand presentation or links to a shared Core brand. |
| `ecommerce_products` | `store_id`, `core_product_ref`, `brand_id`, `title`, `slug`, `subtitle`, `short_description`, `description`, `product_kind`, `tax_category_ref`, `publication_status`, `published_at` | Holds the product's storefront content. A variant is the item that customers buy. |
| `ecommerce_product_categories` | `product_id`, `category_id`, `is_primary`, `sort_order` | Lets a product appear in more than one category. |
| `ecommerce_collections` | `store_id`, `name`, `slug`, `description`, `asset_ref`, `starts_at`, `ends_at`, `status` | Creates curated groups such as New Arrivals or Festival Offers. |
| `ecommerce_collection_products` | `collection_id`, `product_id`, `sort_order` | Connects products to collections without copying product records. |
| `ecommerce_channels` | `store_id`, `code`, `name`, `currency_code`, `locale`, `country_codes_json`, `status` | Defines a sales context, such as the India website or a retail channel. Start with one channel. |
| `ecommerce_product_channels` | `product_id`, `channel_id`, `published`, `available_from`, `available_until` | Controls where and when a product can be sold. |

A category describes what a product is. A collection groups products for a campaign. A channel decides where they are sold.

## 3. Product Details, Variants, and Images

Module: `catalog`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_product_details` | `product_id`, `material`, `country_of_origin`, `manufacturer`, `warranty_text`, `return_policy_ref`, `shipping_profile_ref`, `seo_title`, `seo_description` | Holds detailed product information and policy references. |
| `ecommerce_product_options` | `product_id`, `code`, `name`, `sort_order` | Defines options such as size, color, or storage capacity. |
| `ecommerce_product_option_values` | `option_id`, `value`, `display_label`, `swatch_value`, `sort_order` | Lists the permitted values for each option. |
| `ecommerce_variants` | `store_ref`, `product_id`, `sku`, `barcode`, `title`, `option_signature`, `weight`, `weight_unit`, `length`, `width`, `height`, `dimension_unit`, `minimum_quantity`, `maximum_quantity`, `requires_shipping`, `manage_inventory`, `status` | Defines a sellable item. A simple product has one default variant. |
| `ecommerce_variant_option_values` | `variant_id`, `option_value_id` | Connects a variant to its selected option values. |
| `ecommerce_product_media` | `product_id`, `variant_id`, `asset_ref`, `media_kind`, `alt_text`, `caption`, `sort_order`, `is_primary` | Displays product images or videos. The shared storage service owns the files. |
| `ecommerce_attribute_definitions` | `store_id`, `code`, `name`, `value_type`, `unit`, `is_filterable`, `is_searchable` | Defines typed specifications such as screen size or power rating. |
| `ecommerce_attribute_values` | `product_id`, `variant_id`, `attribute_definition_id`, `text_value`, `number_value`, `boolean_value` | Stores validated specifications for products or variants. |

Option rows support any number of choices. Avoid fixed `option_1`, `option_2`, and `option_3` columns.

## 4. Search and Filters

Module: `search`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_search_documents` | `product_ref`, `channel_ref`, `locale`, `title`, `search_text`, `filter_values_json`, `source_version`, `indexed_at` | A rebuildable view for product search. Start with database search and add an external index when needed. |
| `ecommerce_search_synonyms` | `store_id`, `locale`, `term`, `equivalent_terms_json`, `status` | Helps customers find products through common names and spelling variations. Optional. |
| `ecommerce_url_redirects` | `store_id`, `old_path`, `target_path`, `redirect_code` | Preserves working links when a product or category URL changes. |

Search results help discovery. Checkout reads current price and stock from their owning modules.

## 5. Prices, Offers, and Coupons

Modules: `pricing` and `promotion`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_price_lists` | `store_ref`, `channel_ref`, `name`, `currency_code`, `tax_inclusive`, `priority`, `starts_at`, `ends_at`, `status` | Defines normal prices and controlled alternatives. |
| `ecommerce_variant_prices` | `price_list_id`, `variant_ref`, `amount_minor`, `compare_at_amount_minor`, `minimum_quantity`, `maximum_quantity` | Stores the actual selling price for a variant and quantity range. |
| `ecommerce_promotions` | `store_ref`, `name`, `kind`, `discount_method`, `discount_value`, `maximum_discount_minor`, `priority`, `can_combine`, `starts_at`, `ends_at`, `status` | Defines percentage discounts, fixed discounts, free shipping, or other supported offers. |
| `ecommerce_promotion_rules` | `promotion_id`, `rule_kind`, `operator`, `validated_values_json` | Limits an offer to eligible products, channels, customers, quantities, or order totals. |
| `ecommerce_coupon_codes` | `promotion_id`, `code`, `total_use_limit`, `per_customer_limit`, `status` | Gives an offer a code and usage limits. |
| `ecommerce_promotion_redemptions` | `promotion_id`, `coupon_code_id`, `customer_ref`, `checkout_ref`, `order_ref`, `discount_minor`, `status`, `expires_at` | Holds or consumes an offer use. Stops concurrent checkout attempts from exceeding coupon limits. |

The server chooses the price and applies offer rules. Home-page cards display that price instead of storing another price.

## 6. Customer Account and Saved Addresses

Module: `customer`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_customers` | `store_ref`, `platform_user_ref`, `display_name`, `contact_email`, `contact_phone`, `status` | Stores commerce details linked to a Platform identity. Guest orders can use contact snapshots without creating an account. |
| `ecommerce_customer_addresses` | `customer_id`, `label`, `recipient_name`, `phone`, `address_line_1`, `address_line_2`, `city`, `region`, `postal_code`, `country_code`, `default_shipping`, `default_billing` | Saves reusable shipping and billing addresses. |
| `ecommerce_customer_consents` | `customer_id`, `purpose`, `policy_version_ref`, `granted`, `source`, `recorded_at` | Records marketing choices and their changes. |
| `ecommerce_wishlists` | `customer_id`, `name`, `visibility` | Holds saved product lists. Optional. |
| `ecommerce_wishlist_items` | `wishlist_id`, `product_ref`, `variant_ref`, `added_at` | Stores products or variants for a later visit. Optional. |

Platform Core owns login, passwords, sessions, permissions, and tenancy. Commerce customer records do not replace those services.

## 7. Cart and Checkout

Module: `cart`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_carts` | `store_ref`, `channel_ref`, `customer_ref`, `guest_token_hash`, `currency_code`, `contact_email`, `shipping_address_json`, `billing_address_json`, `selected_shipping_quote_ref`, `subtotal_minor`, `discount_minor`, `tax_minor`, `shipping_minor`, `total_minor`, `status`, `expires_at`, `merged_into_cart_id` | Persists both the shopping cart and checkout details. Supports guest checkout and cart recovery. |
| `ecommerce_cart_items` | `cart_id`, `variant_ref`, `configuration_hash`, `quantity`, `unit_price_minor`, `discount_minor`, `tax_minor`, `line_total_minor`, `price_version` | Stores selected variants and a current calculated price. Revalidate the values before order placement. |
| `ecommerce_cart_promotions` | `cart_id`, `promotion_ref`, `coupon_ref`, `redemption_ref`, `discount_minor` | Records the offers applied to a cart. |
| `ecommerce_checkout_attempts` | `cart_id`, `cart_version`, `idempotency_key`, `request_hash`, `status`, `order_ref`, `payment_collection_ref`, `failure_code`, `recovery_state_json`, `next_retry_at`, `started_at`, `expires_at`, `completed_at` | Makes checkout retries safe and prevents a cart from creating duplicate orders. |

Use one cart record throughout checkout. A separate checkout attempt records the submission and recovery process.

## 8. Shipping Choices and Tax Calculation

Modules: `shipping` and `tax`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_shipping_profiles` | `store_ref`, `name`, `handling_days`, `restricted_goods`, `status` | Groups products with different delivery requirements. |
| `ecommerce_shipping_zones` | `store_ref`, `name`, `status` | Defines delivery areas. |
| `ecommerce_shipping_zone_areas` | `zone_id`, `country_code`, `region_code`, `postal_code_pattern` | Lists the countries, regions, or postal areas covered by a zone. |
| `ecommerce_shipping_methods` | `store_ref`, `code`, `name`, `kind`, `provider_ref`, `status` | Defines courier delivery, local delivery, or store pickup. |
| `ecommerce_shipping_rates` | `method_id`, `zone_id`, `profile_ref`, `channel_ref`, `currency_code`, `minimum_weight`, `maximum_weight`, `minimum_subtotal_minor`, `maximum_subtotal_minor`, `amount_minor`, `estimated_days` | Prices an eligible delivery method. |
| `ecommerce_shipping_quotes` | `cart_ref`, `cart_version`, `method_id`, `address_hash`, `currency_code`, `amount_minor`, `tax_minor`, `allocation_plan_json`, `provider_quote_ref`, `expires_at` | Saves the delivery choice offered for the current cart and address. |
| `ecommerce_tax_categories` | `store_ref`, `code`, `name`, `provider_tax_code`, `status` | Classifies products and shipping for tax calculation. |
| `ecommerce_tax_rates` | `category_id`, `country_code`, `region_code`, `rate_decimal`, `tax_name`, `starts_at`, `ends_at` | Stores configured rates when the tax module owns calculation. A verified tax adapter can supply rates instead. |

A quote belongs to one cart version and delivery address. Recalculate it when items, quantities, or the address change.

## 9. Payment

Module: `payment`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_payment_methods` | `store_ref`, `channel_ref`, `code`, `name`, `provider_ref`, `kind`, `enabled` | Lists the supported payment options for a sales channel. |
| `ecommerce_payment_collections` | `checkout_ref`, `order_ref`, `currency_code`, `required_amount_minor`, `authorized_minor`, `captured_minor`, `refunded_minor`, `status` | Groups all payments for one purchase. Supports retries and partial payments. |
| `ecommerce_payments` | `collection_id`, `method_id`, `provider_payment_ref`, `idempotency_key`, `amount_minor`, `status`, `expires_at` | Tracks one payment attempt through the provider. |
| `ecommerce_payment_transactions` | `payment_id`, `operation`, `amount_minor`, `currency_code`, `provider_transaction_ref`, `idempotency_key`, `status`, `failure_code`, `occurred_at` | Records each authorization, capture, void, refund, or offline receipt. Completed transactions remain unchanged. |
| `ecommerce_payment_provider_events` | `provider_ref`, `provider_event_id`, `payment_ref`, `payload_ref`, `signature_verified_at`, `received_at`, `processed_at`, `status`, `attempts` | Processes provider callbacks once and handles delayed or repeated events. |
| `ecommerce_payment_reconciliations` | `provider_ref`, `period_start`, `period_end`, `expected_minor`, `reported_minor`, `difference_minor`, `status` | Finds differences between store records and provider settlement reports. Add when the adapter supports reconciliation. |

Store provider references and masked display data. Payment providers hold card numbers and security codes. A browser success page cannot confirm payment.

## 10. Order Confirmation and Order History

Module: `order`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_orders` | `store_ref`, `channel_ref`, `customer_ref`, `source_cart_ref`, `checkout_ref`, `order_number`, `currency_code`, `customer_email_snapshot`, `customer_phone_snapshot`, `subtotal_minor`, `discount_minor`, `tax_minor`, `shipping_minor`, `total_minor`, `order_status`, `payment_status`, `fulfillment_status`, `placed_at`, `cancelled_at` | The durable purchase record. Keeps order, payment, and delivery state separate. |
| `ecommerce_order_items` | `order_id`, `variant_ref`, `sku_snapshot`, `title_snapshot`, `options_snapshot_json`, `quantity`, `unit_price_minor`, `discount_minor`, `tax_minor`, `line_total_minor`, `tax_category_snapshot`, `requires_shipping` | Saves exactly what the customer bought and paid. Later catalog edits do not change it. |
| `ecommerce_order_addresses` | `order_id`, `address_kind`, `recipient_name`, `phone`, `address_line_1`, `address_line_2`, `city`, `region`, `postal_code`, `country_code` | Saves the shipping and billing addresses used for the order. |
| `ecommerce_order_tax_lines` | `order_id`, `order_item_id`, `order_charge_id`, `tax_name`, `rate_decimal`, `taxable_minor`, `tax_minor`, `tax_inclusive`, `provider_reference` | Preserves item and shipping tax breakdowns. Exactly one item or charge is the target. |
| `ecommerce_order_charges` | `order_id`, `kind`, `description`, `shipping_method_snapshot`, `shipping_quote_ref`, `amount_minor`, `discount_minor`, `tax_minor` | Saves shipping and other approved charges. |
| `ecommerce_order_adjustments` | `order_id`, `order_item_id`, `order_charge_id`, `promotion_ref`, `kind`, `amount_minor`, `reason`, `approved_by_ref` | Preserves discounts and approved corrections against an item, charge, or the whole order. |
| `ecommerce_order_events` | `order_id`, `event_kind`, `actor_ref`, `reason`, `correlation_id`, `occurred_at` | Records confirmation, cancellation, payment, fulfillment, and customer-service decisions. |
| `ecommerce_order_documents` | `order_id`, `document_kind`, `billing_document_ref`, `number`, `asset_ref`, `issued_at`, `status` | Links invoices, receipts, and credit notes from the authorized billing owner. |
| `ecommerce_order_policy_acceptances` | `order_id`, `policy_version_ref`, `accepted_at`, `acceptance_source` | Saves the terms accepted at purchase. |
| `ecommerce_order_access_grants` | `order_id`, `token_hash`, `purpose`, `expires_at`, `revoked_at` | Gives a guest a limited order-tracking link without exposing another customer's order. |

Each source cart can produce only one order. Payment retries use the same pending order. A terminal cancellation starts a new cart. Changes after confirmation use events, adjustments, cancellations, and refund records.

## 11. Stock and Reservation

Module: `inventory`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_stock_locations` | `store_ref`, `code`, `name`, `address_json`, `kind`, `status` | Defines a warehouse, stock room, or pickup store. |
| `ecommerce_inventory_items` | `store_ref`, `sku`, `name`, `stock_unit`, `tracked`, `external_stock_ref` | Defines the physical stock item managed locally or mapped to an external stock owner. |
| `ecommerce_variant_inventory_items` | `variant_ref`, `inventory_item_id`, `required_quantity` | Connects a sellable variant to physical stock. A kit can consume several stock items. |
| `ecommerce_inventory_levels` | `inventory_item_id`, `location_id`, `on_hand_quantity`, `reserved_quantity`, `unavailable_quantity`, `safety_stock_quantity` | Holds the current stock counters for fast availability checks. |
| `ecommerce_stock_movements` | `inventory_item_id`, `location_id`, `on_hand_delta`, `unavailable_delta`, `movement_kind`, `source_kind`, `source_ref`, `idempotency_key`, `occurred_at` | Records receipts, shipment deductions, accepted returns, unavailable stock changes, and approved adjustments. It is an append-only stock ledger. |
| `ecommerce_stock_reservations` | `inventory_item_id`, `location_id`, `checkout_ref`, `order_item_ref`, `reserved_quantity`, `consumed_quantity`, `released_quantity`, `status`, `expires_at` | Holds stock during payment and until shipment. Temporary holds expire. Confirmed order allocations remain until shipment or cancellation. |

Available stock is on-hand stock minus reserved, unavailable, and safety stock. A reservation does not remove physical stock. Shipment removes stock once and consumes the matching reservation.

## 12. Picking, Packing, Delivery, and Tracking

Module: `fulfillment`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_fulfillment_orders` | `order_ref`, `location_ref`, `delivery_kind`, `shipping_method_ref`, `status`, `assigned_at`, `promised_at` | Assigns part or all of an order to a stock location. |
| `ecommerce_fulfillment_items` | `fulfillment_order_id`, `order_item_ref`, `assigned_quantity`, `picked_quantity`, `packed_quantity`, `cancelled_quantity` | Tracks warehouse work for each assigned order line. |
| `ecommerce_shipments` | `fulfillment_order_id`, `carrier_ref`, `service_code`, `provider_shipment_ref`, `tracking_number`, `tracking_url`, `label_asset_ref`, `status`, `shipped_at`, `delivered_at` | Records a parcel or pickup handover. |
| `ecommerce_shipment_items` | `shipment_id`, `fulfillment_item_id`, `quantity` | Records which quantities left in each shipment. Supports partial delivery. |
| `ecommerce_shipment_events` | `shipment_id`, `provider_event_id`, `event_kind`, `location_text`, `occurred_at`, `received_at` | Shows tracking history and processes duplicate carrier updates safely. |

A single order can have several fulfillment orders and shipments. Assigned and shipped quantities must stay within the uncancelled purchased quantity.

## 13. Cancellation, Returns, and Refunds

Modules: `return` and `payment`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_cancellation_requests` | `order_ref`, `customer_ref`, `reason`, `status`, `requested_at`, `decided_at`, `decided_by_ref` | Records a customer or staff cancellation request and its decision. |
| `ecommerce_cancellation_items` | `cancellation_request_id`, `order_item_ref`, `quantity` | Allows partial cancellation before goods are shipped. |
| `ecommerce_returns` | `order_ref`, `return_number`, `customer_ref`, `reason`, `return_method`, `status`, `requested_at`, `approved_at`, `received_at` | Tracks goods returned after delivery. |
| `ecommerce_return_items` | `return_id`, `order_item_ref`, `requested_quantity`, `received_quantity`, `accepted_quantity`, `condition`, `stock_disposition`, `location_ref` | Records received goods and whether they can return to saleable stock. |
| `ecommerce_refund_requests` | `order_ref`, `return_ref`, `cancellation_ref`, `currency_code`, `requested_amount_minor`, `reason`, `status`, `approved_by_ref` | Controls refunds for returns, cancellations, and service corrections. A return is optional. |
| `ecommerce_refund_items` | `refund_request_id`, `order_item_ref`, `order_charge_ref`, `quantity`, `amount_minor`, `tax_minor` | Explains which items or charges are refunded. |
| `ecommerce_refund_allocations` | `refund_request_ref`, `capture_transaction_id`, `refund_transaction_id`, `amount_minor`, `status` | Connects the approved refund to the original captured payments. Owned by payment. |

A return does not automatically mean a refund or restock. Accepted goods, refund approval, and provider completion are separate recorded decisions.

## 14. Reviews, Support, and Customer Updates

Modules: `review`, `support`, and `notification`.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_product_reviews` | `product_ref`, `customer_ref`, `order_item_ref`, `rating`, `title`, `body`, `verified_purchase`, `moderation_status`, `published_at` | Stores product feedback and supports purchase verification. |
| `ecommerce_support_cases` | `customer_ref`, `order_ref`, `subject`, `kind`, `priority`, `status`, `assigned_to_ref`, `opened_at`, `closed_at` | Tracks delivery problems, product questions, and after-sales requests. |
| `ecommerce_support_messages` | `case_id`, `sender_ref`, `visibility`, `body`, `attachment_refs_json`, `sent_at` | Keeps customer messages and internal notes in the correct access scope. |
| `ecommerce_notification_deliveries` | `event_id`, `recipient_ref`, `channel`, `template_key`, `provider_message_ref`, `idempotency_key`, `status`, `attempts`, `sent_at` | Tracks order confirmations, shipping notices, and refund updates through shared delivery services. |

## 15. Administration, Imports, and Reliable Operations

Modules: `integration` and the relevant business owner.

| Table | Main fields | Why it is needed |
| --- | --- | --- |
| `ecommerce_external_links` | `connection_ref`, `owner_module`, `entity_kind`, `local_id`, `remote_id`, `remote_version`, `last_synced_at` | Maps local products, stock, and orders to ERP or marketplace records. |
| `ecommerce_sync_runs` | `connection_ref`, `owner_module`, `direction`, `entity_kind`, `cursor`, `processed_count`, `failed_count`, `status`, `started_at`, `finished_at` | Tracks a resumable import or export. |
| `ecommerce_sync_failures` | `sync_run_id`, `source_record_ref`, `error_code`, `safe_error_message`, `attempts`, `next_retry_at`, `resolved_at` | Lets staff retry failed records without repeating a complete import. |
| `ecommerce_integration_events` | `connection_ref`, `external_event_id`, `entity_kind`, `payload_ref`, `received_at`, `processed_at`, `status` | Deduplicates inbound ERP or partner updates before dispatch to the owner. |
| `ecommerce_order_outbox` | `event_id`, `order_id`, `event_kind`, `aggregate_version`, `payload_json`, `status`, `attempts`, `available_at`, `published_at` | Sends committed order events reliably to stock, notification, and integration handlers. |
| `ecommerce_payment_outbox` | `event_id`, `payment_id`, `event_kind`, `aggregate_version`, `payload_json`, `status`, `attempts`, `available_at`, `published_at` | Sends committed payment outcomes reliably to the order owner. |
| `ecommerce_fulfillment_outbox` | `event_id`, `fulfillment_order_id`, `event_kind`, `aggregate_version`, `payload_json`, `status`, `attempts`, `available_at`, `published_at` | Sends shipment and delivery outcomes to order and notification handlers. |
| `ecommerce_daily_sales_summary` | `store_ref`, `channel_ref`, `business_date`, `currency_code`, `order_count`, `gross_minor`, `discount_minor`, `tax_minor`, `refund_minor`, `net_minor`, `calculated_at` | A rebuildable reporting view. Use it when live order queries become expensive. |

Each owner writes its outbox in the same database transaction as the business change. Use these queues only for actual background work. Platform owns staff access and the shared audit service.

## 16. Higher-Level Features — Add When Needed

These tables extend the base store after the standard purchase flow works.

| Feature | Table | Main fields | Why it is needed |
| --- | --- | --- | --- |
| Localization | `ecommerce_product_translations` | `store_ref`, `product_ref`, `locale`, `slug`, `title`, `description`, `seo_title`, `seo_description` | Shows product content in more than one language. |
| Customer pricing | `ecommerce_customer_groups` | `store_ref`, `name`, `status` | Groups customers with shared commercial terms. |
| Customer pricing | `ecommerce_customer_group_members` | `group_id`, `customer_ref`, `starts_at`, `ends_at` | Assigns customers to groups. |
| Customer pricing | `ecommerce_price_list_assignments` | `price_list_id`, `customer_group_ref`, `business_account_ref`, `priority` | Grants eligible customers a specific price list. |
| Bundles | `ecommerce_bundle_components` | `bundle_variant_ref`, `component_variant_ref`, `quantity` | Defines a bundle. Inventory requirements use the component stock mappings. |
| Gift cards | `ecommerce_gift_cards` | `store_ref`, `code_hash`, `currency_code`, `issued_amount_minor`, `balance_minor`, `held_amount_minor`, `expires_at`, `status` | Issues a controlled stored-value card. |
| Gift cards | `ecommerce_gift_card_entries` | `gift_card_id`, `operation`, `amount_minor`, `payment_ref`, `order_ref`, `idempotency_key`, `status`, `expires_at`, `occurred_at` | Records issue, payment holds, spend, release, refund, and expiry without rewriting history. |
| Loyalty | `ecommerce_loyalty_accounts` | `customer_ref`, `program_code`, `points_balance`, `status` | Holds a customer's current reward balance. |
| Loyalty | `ecommerce_loyalty_entries` | `account_id`, `points_delta`, `reason`, `order_ref`, `expires_at`, `idempotency_key` | Records rewards and redemptions once. |
| B2B | `ecommerce_business_accounts` | `store_ref`, `platform_organization_ref`, `name`, `billing_terms_ref`, `credit_policy_ref`, `status` | Adds verified business purchasing terms. |
| B2B | `ecommerce_business_account_members` | `account_id`, `customer_ref`, `purchasing_role`, `approval_limit_minor` | Defines buyers and approvers through Platform permission contracts. |
| B2B | `ecommerce_quotes` | `account_ref`, `customer_ref`, `quote_number`, `currency_code`, `valid_until`, `status`, `total_minor` | Records a proposal before order approval. |
| B2B | `ecommerce_quote_items` | `quote_id`, `variant_ref`, `description_snapshot`, `quantity`, `unit_price_minor`, `tax_minor` | Preserves the negotiated offer. |
| Subscriptions | `ecommerce_subscription_plans` | `store_ref`, `name`, `interval_unit`, `interval_count`, `status` | Defines a recurring purchase schedule. |
| Subscriptions | `ecommerce_subscriptions` | `customer_ref`, `plan_id`, `provider_contract_ref`, `next_order_at`, `status` | Tracks a provider-backed recurring agreement. |
| Subscriptions | `ecommerce_subscription_items` | `subscription_id`, `variant_ref`, `quantity`, `price_policy` | Defines the recurring items. Each cycle creates a normal order. |
| Digital goods | `ecommerce_download_grants` | `order_item_ref`, `customer_ref`, `asset_ref`, `token_hash`, `expires_at`, `download_limit`, `download_count` | Grants controlled access after payment. |
| Marketplace | `ecommerce_vendors` | `store_ref`, `platform_organization_ref`, `name`, `payout_account_ref`, `status` | Defines approved sellers. |
| Marketplace | `ecommerce_vendor_listings` | `vendor_id`, `variant_ref`, `seller_sku`, `price_list_ref`, `stock_source_ref`, `status` | Connects a seller to a sellable offer. |
| Marketplace | `ecommerce_vendor_order_items` | `vendor_id`, `order_item_ref`, `quantity`, `gross_minor`, `commission_minor` | Assigns purchased quantities and charges to a seller. |
| Marketplace | `ecommerce_vendor_settlements` | `vendor_id`, `period_start`, `period_end`, `currency_code`, `net_minor`, `provider_payout_ref`, `status` | Records seller payment batches. |
| Marketplace | `ecommerce_vendor_settlement_items` | `settlement_id`, `vendor_order_item_ref`, `refund_ref`, `amount_minor` | Explains every earning or deduction in a settlement. |
| Exchanges | `ecommerce_exchanges` | `return_ref`, `replacement_order_ref`, `price_difference_minor`, `status` | Links accepted returned goods to a normal replacement order. |
| Stock transfer | `ecommerce_stock_transfers` | `source_location_ref`, `target_location_ref`, `status`, `dispatched_at`, `received_at` | Moves stock between locations. |
| Stock transfer | `ecommerce_stock_transfer_items` | `transfer_id`, `inventory_item_ref`, `sent_quantity`, `received_quantity` | Records transfer quantities and posts matching ledger movements. |

Marketplace requires seller-specific offers, pricing, stock, and checkout allocation. Enable it only after those contracts extend the base model.

## 17. End-to-End Purchase Flow

| Step | Action | Data change |
| --- | --- | --- |
| 1 | Open the home page. | Read published pages, sections, content blocks, and channel context. |
| 2 | Browse or search. | Read products, variants, prices, and stock availability. |
| 3 | Choose a variant and quantity. | Validate the selected options and sale limits. |
| 4 | Add to cart. | Save cart items and calculate prices on the server. A cart does not hold stock by default. |
| 5 | Enter contact and address details. | Save the cart's checkout details and return eligible delivery quotes. |
| 6 | Submit checkout. | Lock the cart version. Recheck prices, stock, offers, taxes, address, policy versions, and quote expiry. |
| 7 | Hold stock and offer usage. | Create time-limited stock reservations and promotion holds through their owner services. |
| 8 | Create the purchase and payment attempt. | Save one pending order with item, address, charge, and tax snapshots. Create its payment collection and provider attempt. |
| 9 | Receive the payment outcome. | Process a verified event once. Confirm the order and its stock allocation only when the payment rule is met. COD follows an explicit unpaid collection rule. |
| 10 | Pick, pack, and ship. | Assign fulfillment work. Record shipment quantities, consume reservations, and post stock deductions once. |
| 11 | Deliver or hand over. | Save tracking events and update delivered quantities. Send a customer update. |
| 12 | Handle cancellation or return. | Record the request, accepted quantities, stock decision, and approved refund. |
| 13 | Complete the refund and reconcile. | Record provider refund transactions, allocate them to captures, and reconcile settlements. |

If a checkout step fails, release its temporary holds and keep a recoverable failure record. An expired reservation with late payment requires stock revalidation. If stock cannot be secured, void or refund the payment and record the decision.

## 18. Database Rules for Scale

The lists above show business fields. Every Ecommerce table also has `id`, `tenant_id`, and `created_at`. Mutable records have `updated_at` and `version`. Event and ledger records stay append-only after posting.

| Area | Required rule |
| --- | --- |
| Identity | Use immutable opaque IDs and named foreign keys for same-owner relations. Keep external Platform, Core, billing, and provider references as opaque IDs. |
| Scope | The server resolves the trusted tenant. Validate store, channel, customer, and organization scope before reads or writes. |
| Ownership | Each module owns its tables, migrations, validation, persistence, and public provider. Call public contracts for another module's data. |
| Currency | Store money as integer minor units with an ISO currency code. Support the currency's actual decimal precision. Use decimal rates and quantities where needed. |
| Price authority | Pricing owns selling prices. Promotion owns discount rules. Order items preserve the accepted amounts. Totals are computed on the server. |
| Tax | Define inclusive or exclusive calculation, jurisdiction, line rounding, shipping tax, and refunds through the tax owner before checkout implementation. |
| Product identity | Store codes are unique within a tenant. SKU is unique within a store. A product's option signature is unique within that product. Product and category slugs are unique within a store. Localized slugs are unique within a store and locale. Each variant has one valid option combination. |
| Price selection | Resolve overlapping price lists with explicit eligibility and priority rules. Prevent overlapping quantity ranges within the same price rule. |
| Relations | Unique pairs prevent duplicate category memberships, variant option values, inventory mappings, and catalog links. Validate option values against their product. |
| Stock keys | Enforce one inventory level per tenant, inventory item, and location. Each stock movement has a unique source operation key. |
| Checkout | Save completed steps and compensation progress for crash recovery. Ask the customer to accept changed totals before charging. Enforce one order per source cart and one request meaning per idempotency key. A changed request body cannot reuse an old key. |
| Payment | Provider transaction and event references are unique within their provider connection. Process callbacks with signature verification and replay handling. |
| Stock | Reserve stock with an atomic conditional update. Update reservations and counters in one owner transaction. Reconcile counters against the ledger. |
| Quantities | Reject negative purchase quantities. Cancelled, assigned, shipped, returned, and refunded quantities cannot exceed their eligible order quantities. |
| Refunds | Concurrent refund approvals cannot exceed captured money minus completed and pending refunds. Lock the affected payment balance. |
| Totals | Item, shipping, discount, and tax breakdowns must balance with the order total. Adjustment records explain discounts already included in totals. |
| Time and precision | Store UTC timestamps. Use integer money and fixed decimal rates, weights, and stock quantities. Keep precision consistent across services. |
| History | Keep confirmed item, address, tax, policy, and document snapshots. Archive products instead of cascading deletes into orders. |
| Background work | Commit owner outbox events with the business change. Consumers deduplicate by event ID and retry with bounded attempts. |
| Search and reports | Treat indexes and summaries as rebuildable views. Storefront caches never authorize payment or stock changes. |
| Query speed | Index tenant and store scope with status, parent IDs, and list sort keys. Use paginated lists and bounded queries. |
| Privacy | Restrict contact details, redact provider payloads, expire guest access tokens, and apply a defined retention policy. |
| Source systems | Choose one authority for each price, stock item, and product identity. ERP synchronization must not create a second active stock ledger. |
| Growth | Start with one store, channel, currency, stock location, and supported payment adapter. Add optional modules for actual business needs. |

## 19. Build Order

The sections follow the customer journey. Database creation follows reference dependencies.

| Stage | Build order | Result |
| --- | --- | --- |
| Base | Platform contracts → store and channel → catalog and media → pricing and tax → stock → shipping → customer → cart → order → payment → fulfillment → returns | One complete purchase, delivery, and refund flow. |
| Standard | Home content editing → promotions → search → reviews → support → notifications → integration retries and reconciliation | A store that staff can operate each day. |
| Higher level | Required localization, B2B, loyalty, gift cards, subscriptions, digital delivery, marketplace, or stock transfers | Business-specific growth without creating unused modules. |

## 20. Reference Platforms

These official models informed the proposed structure. The table names above are our design choices.

| Platform | Pattern reviewed | Official reference |
| --- | --- | --- |
| Shopify | Sellable variants, inventory by location, and separate fulfillment work and shipments. | [Product variants](https://shopify.dev/docs/api/admin-graphql/latest/objects/ProductVariant), [inventory locations](https://shopify.dev/docs/api/admin-graphql/latest/queries/inventoryitem), [fulfillment orders](https://shopify.dev/docs/api/admin-graphql/latest/objects/FulfillmentOrder) |
| Medusa | Module ownership, payment collections, stock reservations, and order changes. | [Commerce modules](https://docs.medusajs.com/resources/commerce-modules), [payment collection](https://docs.medusajs.com/resources/commerce-modules/payment/payment-collection), [reservation lifecycle](https://docs.medusajs.com/resources/commerce-modules/inventory/reservations-lifecycle), [order module](https://docs.medusajs.com/resources/commerce-modules/order) |
| Saleor | One cart/checkout model and channel-based product, price, payment, and shipping context. | [Checkout](https://docs.saleor.io/developer/checkout/overview), [channels](https://docs.saleor.io/developer/channels/overview), [transactions](https://docs.saleor.io/developer/payments/overview) |
| commercetools | Product and variant separation, line-item snapshots, orders, and expiring stock reservations. | [Product modeling](https://docs.commercetools.com/learning-model-your-product-catalog/product-modeling/modeling-products), [carts and orders](https://docs.commercetools.com/api/carts-orders-overview), [inventory](https://docs.commercetools.com/api/inventory-overview) |
