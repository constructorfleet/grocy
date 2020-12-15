> !!! The major version bump is due to breaking API changes, please see below if you use the API

### New feature: Use any product related quantity unit anywhere
- Finally it's possible to use any product related quantity unit on any page
- Products still have one quantity unit stock and one (default) quantity unit purchase, but any QU, which has a direct or indirect conversion for that product, can be used to pick an amount
  - (Because the stock quantity unit is now the base for everything, it cannot be changed after the product was once added to stock (for now, maybe there will be a possibilty to change it in a future release))

### New feature: Prefill purchase data by barcodes
- Imagine you buy for example eggs in different pack sizes and they have different barcodes
- Each product barcode can be assigned an amount, quantity unit and store (on the product edit page), which is then automatically prefilled on the purchase page
- Additionally, the last price per barcode will be tracked and prefilled as a "Total price" on purchase
- (Thanks @kriddles for the initial work on this)

### New feature: User permissions
- Users can now have permissions, can be configured per user on the "Manage users" page (lock icon)
- Default permissions for new users can be set via a new `config.php` setting `DEFAULT_PERMISSIONS` (defaults to `ADMIN`, so no changed behavior when not configured)
- All currently existing users will get all permissions (`ADMIN`) during the update/migration
- Creating API keys on the "Manage API keys"-page (top right corner settings menu) now requires the `ADMIN` permission
  - Other users only see their API keys on that page
- (Thanks @fipwmaqzufheoxq92ebc for the initial work on this)

### New feature: External authentication support
- New `config.php` setting `AUTH_CLASS` to change the used authentication provider
- Via LDAP
  - New `config.php` settings `LDAP_DOMAIN`, `LDAP_ADDRESS` and `LDAP_BASE_DN`
  - If you set `AUTH_CLASS` to `Grocy\Middleware\LdapAuthMiddleware`, users will be authenticated against your directory (and will also be created (in grocy), if not already present)
- Via a reverse proxy
  - New `config.php` setting `REVERSE_PROXY_AUTH_HEADER`
  - If you set `AUTH_CLASS` to `Grocy\Middleware\ReverseProxyAuthMiddleware` and your reverse proxy sends a username in the HTTP header `REMOTE_USER` (header name can be changed by the setting `REVERSE_PROXY_AUTH_HEADER`), the user is automatically authenticated (and will also be created (in grocy), if not already present)
  - (Thanks @fipwmaqzufheoxq92ebc for the initial work on this)

### Stock improvements/fixes
- Changes about best before dates: It's now possible to distinguish between best before dates and expiration dates:
  - New product option "Due date type" (defaults to "Best before date")
  - Wording changes:
    - All current places where "Best before date" was used now use "Due date"
    - Products with `Due date type = Best before date` (so all existing products) are "due" or "overdue" (they don't "expire" or are "expired")
    - Products with `Due date type = Expiration date` (new option) can "expire" or are "expired"
  - Color changes:
    - Products which are due soon or expire soon are (still) highlighted in yellow
    - Products which are overdue are highlighted in grey (there is also a new filter button on the stock overview page for them)
    - Products which are expired (new option) are highlighted in red
- When creating a quantity unit conversion it's now possible to automatically create the inverse conversion (thanks @kriddles)
- The product option "Allow partial units in stock" was removed, partial amounts are now possible by default for all products
- On purchase there is now a warning shown, when the due date of the purchased product is earlier than the next due date in stock (enabled by default, can be disabled by a new stock setting (top right corner settings menu))
- The amount to be used for the "quick consume/open buttons" on the stock overview page can now be configured per product (new product option "Quick consume amount", defaults to 1)
  - This "Quick consume amount" can optionally also be used as the default on the consume page (new stock setting / top right corner settings menu)
- Products can now be duplicated (new button on the products list page, all fields will be preset from the copied product, except the name)
- When consuming or opening a parent product, which is currently not in stock, any in-stock sub product will now be consumed/opened (like already automatically done when consuming recipes)
- Optimized/clarified what the total/unit price is on the purchase page (thanks @kriddles)
- On the purchase page the amount field is now displayed above/before the due date for better `TAB` handling (thanks @kriddles)
- Changed that when `FEATURE_FLAG_STOCK_BEST_BEFORE_DATE_TRACKING` is disabled, products now get internally a due date of "never overdue" (aka `2999-12-31`) instead of today (thanks @kriddles)
- Products can now be disabled to keep the history/journal, but hide it everywhere, without deleting it (new product option "Active", deleting a product now explicitly also deletes its journal and all other references) (thanks @kriddles for the initial work on this)
- Products can now be hidden from the stock overview page (new product option "Show on stock overview page", enabled by default, so no changed behavior when not configured)
- The due date is now also prefilled on the inventory page based on the products "Default due days" (was only done on the purchase page before)
- On the stock journal page, it's now visible if a consume-booking was spoiled
- It's now tracked who made a stock change (currently logged in user, visible on the stock journal page) (thanks @fipwmaqzufheoxq92ebc)
- Product edit page improvements ("Save & continue" button, deleting and adding a product picuture is now possible in one go) (thanks @Ma27)
- For products with tare weight handling enabled, it's now optionally possible to consume a fixed/exact amount (just like for "normal" products) in case you don't want to weigh the whole container this time (new checkbox on the consume page) (thanks @fipwmaqzufheoxq92ebc)
- The stock overview page now also shows the value - new column and also the total value in the header (thanks @kriddles)
- It's now possible to set a custom purchased date on purchase (new field on the purchase and inventory page, hidden by default - enable it by a new stock setting (top right corner settings menu)) (thanks @kriddles)
- The decimal places for all amount and price inputs can now be configured (stock settings / top right corner settings menu, default for amounts is `4`, for prices `2`)
- When clicking the product name on the shopping list, the product card will now be displayed (like on the stock overview page) (thanks @kriddles)
- On the product card there is now also a button to jump directly to the stock entries of the corresponding product (thanks @kriddles)
- The product picker workflows can now also be started by `ENTER` (additionally to `TAB`)
- Added a "retry camera barcode scan" button (button with camera icon, shortcut `C`) to the product picker workflow dialog
- Added more filters on the stock journal page
- Added a grouped/summarized stock journal (new button "Journal summary" at the top of the stock journal page) (thanks @fipwmaqzufheoxq92ebc)
  - Provides an overview of summarized transactions per product, transaction type and user + summarized amount
- Fixed that changing the products "Factor purchase to stock quantity unit" not longer messes up historical prices (which results for example in wrong recipe costs) (thanks @kriddles)
- Fixed that when adding products through a product picker workflow and when the created products contains special characters, the product was not preselected on the previous page (thanks @Forceu)
- Fixed that when editing a product the default store was not visible / always empty regardless if the product had one set (thanks @kriddles)
- Fixed that `FEATURE_SETTING_STOCK_COUNT_OPENED_PRODUCTS_AGAINST_MINIMUM_STOCK_AMOUNT` (option to configure if opened products should be considered for minimum stock amounts) was not handled correctly (thanks @teddybeermaniac)
- Fixed that the "Due soon" sum (yellow filter button) on the stock overview page didn't include products which are due today (thanks @fipwmaqzufheoxq92ebc)
- Fixed that the shopping cart icon on the stock overview page was also shown if the product was on an already deleted shopping list (if enabled) (thanks @fipwmaqzufheoxq92ebc)
- Fixed that when editing a stock entry without a price, the price field was prefilled with `1`
- Fixed that the location & product groups filter on the stock overview page used a contains search instead of an exact search
- Fixed that the amount on the success popup was wrong when consuming a product with "Tare weight handling" enabled
- Fixed that the aggregated amount of parent products was wrong on the stock overview page when the child products had not the same stock quantity units
- Fixed that edited stock entries were not considered for the price history chart on the product card
- Fixed that `FEATURE_FLAG_STOCK_BEST_BEFORE_DATE_TRACKING` is set to `false`, the purchase page validation failed (thanks @fipwmaqzufheoxq92ebc)
- Fixed that consuming (and editing the amount of) products with enabled tare weight handling did not work on the stock entries page
- Fixed that the recipes dropdown on the consume page also displayed internal recipes (thanks @kriddles)
- Fixed that opening tare weight handling enabled products is not possible via the UI and the API (as this makes no sense)

### Shopping list improvements
- Decimal amounts are now allowed (for any product, rounded by two decimal places)
- Added a button to add all currently in-stock but overdue and expired products to the shopping list (thanks @m-byte)
- Improved that when `FEATURE_FLAG_STOCK` is disabled, all product/stock related inputs and buttons are now hidden on the shopping list page (thanks @fipwmaqzufheoxq92ebc)
- Shopping list items can now have their own Userfields (entity `shopping_list`), on the shopping list table those fields are rendered additionally to the product Userfields
- Fixed that "Add products that are below defined min. stock amount" always rounded up the missing amount to an integral number, this now allows decimal numbers

### Recipe improvements/fixes
- It's now possible to print recipes (button next to the recipe title) (thanks @zsarnett)
- Changed that recipe costs are now based on the costs of the products picked by the default consume rule "First due first, then first in first out" (thanks @kriddles)
  - Recipe costs were based on the last purchase price per product before, so this now better reflects the current real costs
- Improved the recipe add workflow (a recipe called "New recipe" is now not automatically created when starting to add a recipe) (thanks @zsarnett)
- On the recipe page, the calories and costs per ingredient are now shown to get a better overview of how much each ingredient contributed
- Fixed that images on the recipe gallery view were not scaled correctly on larger screens (thanks @zsarnett)
- Fixed that decimal ingredient amounts maybe resulted in wrong conversions or truncated decimal places if your locale does not use a dot as the decimal separator (thanks @m-byte)
- Fixed that a recipe cannot be included in itself (because this will cause an infinite loop) (thanks @fipwmaqzufheoxq92ebc)
- Fixed that when editing a recipe ingredient the checkbox "Disable stock fulfillment checking for this ingredient" was not initaliased with the saved value
- Fixed that the status filter ("Enough in stock", etc.) on the recipes page did not filter recipes on the gallery tab (thanks @fipwmaqzufheoxq92ebc)
- Fixed that consuming a recipe ingredient with tare weight handling enabled consumed a wrong amount (thanks @fipwmaqzufheoxq92ebc)
- Fixed that consuming a parent product recipe ingredient did not consider quantity unit conversion when effectively consuming a child product

### Meal plan fixes
- Fixed that for products the quantity unit purchase was displayed instead of the products quantity unit stock (thanks @BenoitAnastay)

### Chores improvements/fixes
- Changed that not assigned chores on the chores overview page display now just a dash instead of an ellipsis in the "Assigned to" column to make this more clear (thanks @Germs2004)
- Fixed (again) that weekly chores, where the next execution should be in the same week, were scheduled (not) always (but sometimes) for the next week only (thanks @shadow7412)
- Fixed that the assignment type "In alphabetic order" did not work correctly (the last person in the list was always assigned next once reached) (thanks @fipwmaqzufheoxq92ebc)

### Equipment improvements
- There is now a button to download the instruction manual (next to the "expand to fullscreen"-button)

### Calendar improvements/fixes
- Events are now links to the corresponding page (thanks @zsarnett)
- Fixed a PHP warning when using the "Share/Integrate calendar (iCal)" button (thanks @tsia)
- Fixed that "Track date only"-chores were always displayed at 12am (are now displayed as all-day events)

### Tasks improvements
- Tasks don't need to unique anymore (name field)

### Userfield improvements/fixes
- New Userfield type "File" to attach any file, will be rendered as a link to the file in tables (if enabled) (thanks @fipwmaqzufheoxq92ebc)
- New Userfield type "Picture" to attach a picture, the picture will be rendered (small) in tables (if enabled) (thanks @fipwmaqzufheoxq92ebc)
- Userfields can now be reordered on the input form (new field "Sort number" per Userfield, fields will be ordered by that number, if any)

### General & other improvements/fixes
- UI refresh / style improvements (thanks @zsarnett for the idea and initial work on this)
- Improved mobile views (thanks @4lloyd for the idea and initial work on this)
  - The buttons on the top of each page and the filter row is now collapsed (use the ellipsis/filter button to show them, this also superseded the shopping list compact view)
  - Tables are horizontally scrollable (instead of collapsing columns which don't fit)
- Table columns can now be shown/hidden (new little eye icon on the top left corner on each table)
  - There are also new columns on some pages, hidden by default
    - Stock overview: Value, product group, calories
- Table states (visible columns, sorting, column order and so on) are now saved server side (in user settings) means that this stays the same when using different browsers
- Dialogs are now used everywhere where appropriate instead of jumping between pages (for exampel when adding/editing shopping list items)
- Added a "Clear filter"-button on all pages (with filters) to quickly reset applied filters
- Prefilled number inputs now use sensible decimal places (max. the configured decimals while hiding trailing zeros where appropriate, means if you never use partial amounts for a product, you'll never see decimals for it)
- Improved / more precise validation messages for number inputs
- Ordering now happens case-insensitive
- The data path (previously fixed to the `data` folder) is now configurable, making it possible to run multiple grocy instances from the same directory (with different `config.php` files / different database, etc.) (thanks @fgrsnau)
  - Via an environment variable `GROCY_DATAPATH` (higher priority)
  - Via an FastCGI parameter `GROCY_DATAPATH` (lower priority)
- The language can now be set per user (see the new user settings page / top right corner settings menu) (thanks @fipwmaqzufheoxq92ebc)
  - Additionally, the language is now also auto-guessed based on the browser locale (HTTP-Header `Accept-Language`)
  - The `config.php` option `CULTURE` was renamed to `DEFAULT_LOCALE`
  - So the used language is based on (in that order)
    - The user setting
    - If not set, then based on browser locale
    - If no matching localizaton was found, `DEFAULT_LOCALE` from `config.php` is used
- Performance improvements (page loading time) of the stock overview page (thanks @fipwmaqzufheoxq92ebc)
- The prerequisites checker now also checks for the minimum required SQLite version (thanks @Forceu)
- Replaced (again, added before in v2.7.0, then reverted in v2.7.1 due to some problems) [QuaggaJS](https://github.com/serratus/quaggaJS) (seems to be unmaintained) by [Quagga2](https://github.com/ericblade/quagga2)
- More `config.php` settings (see the section `Component configuration for Quagga2`) to tweak Quagga2 (this is the component used for device camera for barcode scanning) (thanks @andrelam)
- Some localization string fixes (thanks @duckfullstop)
- Better error pages
- Fixed that XSS / HTML injection was possible through some user input fields (low severity / not really a problem as this could not be abused unauthenticated)
- New translations: (thanks all the translators)
  - Greek (demo available at https://el.demo.grocy.info)
  - Korean (demo available at https://ko.demo.grocy.info)
  - Chinese (China) (demo available at https://zh-cn.demo.grocy.info)
  - Hebrew (Israel) (demo available at https://he-il.demo.grocy.info)

### API improvements/fixes
- **Breaking changes**:
  - All prices are now related to the products stock quantity unit (instead of the products purchase QU)
  - All (product) amounts are now related to the products stock quantity unit (was related to the products purchase QU for the shopping list before)
  - The product object no longer has a field `barcodes` with a comma separated barcode list, instead barcodes are now stored in a separate table/entity `product_barcodes` (use the existing "Generic entity interactions" endpoints to access them)
  - The endpoint `/objects/{entity}/search` was removed (use the existing `/objects/{entity}` endpoint with new new filter capabilities mentioned below)
  - The output / field names of `ProductDetailsResponse` have slightly changed (endpoint `/stock/products/{productId}`)
  - Endpoint `/stock/volatile`
    - The query parameter `expring_days` was renamed to `due_soon_days`
    - The field `expiring_products` was renamed to `due_products`
    - The field `expired_products` now only contains expired products (so them with `Due date type = Expiration date`)
    - The new field `overdue_products` contains only overdue products (so them with `Due date type = Best before date`)
- For better integration (apps), it's now possible to show a QR-Code for API keys (thanks @fipwmaqzufheoxq92ebc)
  - New QR-Code button on the "Manage API keys"-page (top right corner settings menu), the QR-Codes contains `<API-Url>|<API-Key>`
  - And on the calendar page when using the button "Share/Integrate calendar (iCal)", there the QR-Codes contains the Share-URL (which is displayed in the textbox above)
- The output of the following endpoints can now be filtered (by any field), ordered and paginated (thanks for the initial work on this @fipwmaqzufheoxq92ebc)
  - `/objects/{entity}`
  - `/stock/products/{productId}/entries`
  - `/stock/products/{productId}/locations`
  - `/recipes/fulfillment`
  - `/users`
  - `/tasks`
  - `/chores`
  - `/batteries`
  - There are 4 new (optional) query parameters to utilize that
    - `order` The field to order by (use the separator `:` to specify the sort order - `asc` or `desc`, defaults to `asc` when omitted)
    - `limit` The maximum number of objects to return
    - `offset` The number of objects to skip
    - `query[]` An array of conditions, each of them is a string in the form of `<field><condition><value>`, where
      - `<field>` is a field name
      - `<condition>` is a comparison operator, one of
        - `=` equal
		- `!=` not equal
        - `~` LIKE
        - `!~` not LIKE
        - `<` less
        - `>` greater
        - `<=` less or equal
        - `>=` greater or equal
        - `§` regular expression
      - `<value>` is the value to search for
- New endpoint `/stock/shoppinglist/add-overdue-products` to add all currently in-stock but overdue products to a shopping list (thanks @m-byte)
- New endpoint `/stock/shoppinglist/add-expired-products` to add all currently in-stock but expired products to a shopping list
- New endpoints GET/POST/PUT `/users/{userId}/permissions` for the new user permissions feature mentioned above
- New endpoint `/user` to get the currently authenticated user
- The following entities are now also available via the endpoint `/objects/{entity}` (only listing, no edit)
  - `stock_log` (the stock journal)
  - `stock` (the "raw" stock entries)
  - `stock_current_locations` (info how much of each product is currently stored at which location)
- Performance improvements of the `/stock/products/*` endpoints (thanks @fipwmaqzufheoxq92ebc)
- The endpoint `/stock/products/{productId}/locations` now also has an optional query parameter `include_sub_products` to optionally also return locations of sub products of the given product
- The following endpoints  now have an optional request body parameter `allow_subproduct_substitution` to consume/open any child product when the given product is a parent product and currently not in stock
  - `/stock/products/{productId}/consume`
  - `/stock/products/by-barcode/{barcode}/consume`
  - `/stock/products/{productId}/open`
  - `/stock/products/by-barcode/{barcode}/open`
- Fixed that the endpoint `/objects/{entity}/{objectId}` always returned successfully, even when the given object not exists (now returns `404` when the object is not found) (thanks @fipwmaqzufheoxq92ebc)
- Fixed that the endpoint `/stock/volatile` didn't include products which expire today (thanks @fipwmaqzufheoxq92ebc)
- Fixed that the endpoint `/objects/{entity}` did not include Userfields for Userentities (so the effective endpoint `/objects/userobjects`)
- Fixed that the endpoint `/stock/consume` returned the response code `200` and an empty response body when `stock_entry_id` was set (consuming a specific stock entry) but invalid (now returns the response code `400`) (thanks @fipwmaqzufheoxq92ebc)
- Fixed that the endpoint `/user/settings/{settingKey}` didn't return the default setting if it was not configured for the current user (same behavior as the endpoint `/user/settings` now)
- Endpoint `/calendar/ical`: Fixed that "Track date only"-chores were always set to happen at 12am (are treated as all-day events now)
- Fixed (again) that CORS was broken
