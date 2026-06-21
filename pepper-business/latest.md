# Claude Handoff — 20260621-205144

- **Branch:** `vex-na-upgrade-gam-version`
- **Generated:** 20260621-205144

---

## Response

# GAM Package Upgrade — `vex-na-upgrade-gam-version`

Upgrade `googleads/googleads-php-lib` to the latest version and migrate the pinned GAM SOAP API namespace, mirroring the established VEX-102 bump pattern.

## Current vs. target version
| | Library | GAM SOAP API namespace |
|---|---|---|
| **Before (master)** | `googleads/googleads-php-lib ^72.0` (72.0.0) | `v202602` (Feb 2026) |
| **After** | `googleads/googleads-php-lib ^73.0` (73.0.0 — latest) | `v202605` (May 2026 — newest shipped in 73.0.0) |

73.0.0 **adds v202605** and **removes v202505**; v202602 is still present (so the lib bump alone is non-breaking), but migrated to v202605 per repo convention and to stay ahead of Google's version-sunset schedule.

## Breaking changes encountered
- **None affecting this codebase.** The only documented removal in 73.0.0 — `AudienceExtensionError` — is **not referenced** anywhere in the repo.
- 73.0.0's `require` constraints are identical to 72.0.0 → a **narrow `composer update`** touched only the googleads entry in `composer.lock` (avoided the pre-existing `firebase/php-jwt` audit block that `--with-all-dependencies` hits).
- v202605 generated classes are **byte-for-byte identical** to v202602 apart from the namespace string (verified by diffing the classes phpstan flagged).

## Files modified (144 files, +248/−248)
- `composer.json` (`^72.0`→`^73.0`) + `composer.lock` (googleads entry only).
- **54 `app/`** + **81 `tests/`** files: mechanical `v202602`→`v202605` rename (227 PHP/XML refs incl. 47 SOAP-response fixtures + `Util\v202605\StatementBuilder`/`AdManagerDateTimes`).
- **6 `docs/`** files updated to the new live version (legacy `v201911` mock refs preserved) + **`AGENTS.md`** "Last reviewed" bump per its maintenance rule.

## Test results
- **phplint ./app/** — OK, 1774 files.
- **phpstan (level 6)** on GAM paths — zero new errors introduced (v202605-mentioning errors proven pre-existing via the class diff). Project carries a pre-existing level-6 error count with no baseline; unchanged.
- **codecept tests/unit/AdManager** — ✅ 56 passed, 452 assertions (1 pre-existing skip).
- **codecept tests/api/group9/AdManager** — 93/96; the 3 failures reproduce **identically on the master/v202602 baseline** (isolated via `git stash` + vendor-73 still bundling v202602) → pre-existing & env-specific (`LineItemPause` ×2 signature-token mismatch → 422; `LineItemMarkByThread` env/data 500).
- **codecept tests/api/group7/BusinessSettingsCest** — ✅ passed.

## Remaining risks / follow-up
- **PHP 8.4 only** (branched clean from master, no patches). On PHP 8.5 the v202605 generated classes need the upstream patches that already exist on `php85-laravel12-upgrade` — that branch will need a trivial re-apply of the `v202602`→`v202605` rename on rebase.
- The 3 pre-existing api failures are unrelated to GAM (test-env signature/data setup) — worth a separate ticket.
- `v202605` will itself be sunset on schedule; next routine bump (74.x → v202608/etc.) should follow this same pattern.

## Commit
`build(gam): upgrade googleads-php-lib ^72→^73, migrate API v202602→v202605` on branch `vex-na-upgrade-gam-version` (not pushed; branch + MR per default).

---

## Git status (`git status --short`)

```
 M AGENTS.md
 M app/Console/Commands/AdManager/ArchiveExpiredLines.php
 M app/Console/Commands/AdManager/FixOpenWrapTargeting.php
 M app/Console/Commands/AdManager/UpdateOrderType.php
 M app/Console/Commands/Dev/AdManager/DeleteLineCommand.php
 M app/Console/Commands/Dev/AdManager/DeleteOrderCommand.php
 M app/Console/Commands/Dev/AdManager/MigrateLineGroups.php
 M app/Console/Commands/Dev/AdManager/VerifyLineGroups.php
 M app/Console/Commands/OneTime/AdvertiserRenameCommand.php
 M app/Console/Commands/OneTime/SendLineOcularEvents.php
 M app/Console/Commands/OneTime/SendOrderOcularEvents.php
 M app/Exceptions/Handler.php
 M app/Http/Controllers/AdManager/CompanyController.php
 M app/Http/Controllers/AdManager/ExternalGAMController.php
 M app/Http/Controllers/AdManager/LineItemController.php
 M app/Http/Controllers/AdManager/OrderController.php
 M app/Http/Controllers/AdManager/TargetingController.php
 M app/Http/Controllers/Business/AdvertisersController.php
 M app/Http/Requests/Permissions/AdManager/BaseRequest.php
 M app/Http/Requests/Validators/AdManager/LineItemPerformAction.php
 M app/Http/Requests/Validators/AdManager/OrderPerformAction.php
 M app/Http/Requests/Validators/AdManager/StoreLineItemValidate.php
 M app/Http/Requests/Validators/Business/AccountConfigValidate.php
 M app/Http/Resources/AdManager/DeviceCategoryCollection.php
 M app/Http/Resources/AdManager/GeoTargetCollection.php
 M app/Http/Resources/AdManager/LineItemLite.php
 M app/Http/Resources/AdManager/LineItemPlacementCollection.php
 M app/Http/Resources/AdManager/PlacementGroupCollection.php
 M app/Http/Resources/AdManager/UserTargetCollection.php
 M app/Jobs/OldPromoOrdersTagging.php
 M app/Jobs/StoreOrderCustomFields.php
 M app/Listeners/AssignBusinessAdvertiserListener.php
 M app/Listeners/LineUpdatedByMarkedThreadListener.php
 M app/Services/AdManager/AdvertiserService.php
 M app/Services/AdManager/BaseService.php
 M app/Services/AdManager/BusinessLineItemService.php
 M app/Services/AdManager/BusinessOrderService.php
 M app/Services/AdManager/CreativeService.php
 M app/Services/AdManager/CustomFieldService.php
 M app/Services/AdManager/CustomTargetingService.php
 M app/Services/AdManager/ForecastService.php
 M app/Services/AdManager/Helpers/LineOcularData.php
 M app/Services/AdManager/InventoryService.php
 M app/Services/AdManager/LineItemCreativeAssociationService.php
 M app/Services/AdManager/LineItemService.php
 M app/Services/AdManager/Model/BusinessLineItem.php
 M app/Services/AdManager/NetworkService.php
 M app/Services/AdManager/OrderCustomFieldService.php
 M app/Services/AdManager/OrderService.php
 M app/Services/AdManager/PublisherQueryLanguageService.php
 M app/Services/AdManager/Traits/CustomFieldTrait.php
 M app/Services/AdManager/Traits/DateTimeTrait.php
 M app/Services/AdManager/Traits/OrderValidateTrait.php
 M app/Services/AdManager/Traits/PqlTrait.php
 M app/Services/AdManager/UserService.php
 M composer.json
 M composer.lock
 M docs/ai/domain-map.md
 M docs/google/ad-manager.md
 M docs/integrations/google-ad-manager/README.md
 M docs/integrations/google-ad-manager/architecture.md
 M docs/integrations/google-ad-manager/troubleshooting.md
 M docs/onboarding/debugging-guide.md
 M tests/_data/storage/google/approve-order.xml
 M tests/_data/storage/google/create-company.xml
 M tests/_data/storage/google/create-creative.xml
 M tests/_data/storage/google/create-custom-field.xml
 M tests/_data/storage/google/create-external-line-item.xml
 M tests/_data/storage/google/create-line-item-bc.xml
 M tests/_data/storage/google/create-line-item-creative-assoc.xml
 M tests/_data/storage/google/create-line-item-error-cost_type.xml
 M tests/_data/storage/google/create-line-item-november-cpd-2025.xml
 M tests/_data/storage/google/create-line-item.xml
 M tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_1.xml
 M tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_2.xml
 M tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_3.xml
 M tests/_data/storage/google/create-order.xml
 M tests/_data/storage/google/create-placement.xml
 M tests/_data/storage/google/error-company-unique.xml
 M tests/_data/storage/google/error-creative-not-found.xml
 M tests/_data/storage/google/error-freq-limit.xml
 M tests/_data/storage/google/error-line-item-not-found.xml
 M tests/_data/storage/google/error-order-unique.xml
 M tests/_data/storage/google/get-adunits.xml
 M tests/_data/storage/google/get-companies.xml
 M tests/_data/storage/google/get-creative-bc.xml
 M tests/_data/storage/google/get-creative.xml
 M tests/_data/storage/google/get-current-network.xml
 M tests/_data/storage/google/get-custom-fields.xml
 M tests/_data/storage/google/get-custom-targeting-users.xml
 M tests/_data/storage/google/get-device-categories.xml
 M tests/_data/storage/google/get-geo-cities.xml
 M tests/_data/storage/google/get-geo-targets.xml
 M tests/_data/storage/google/get-group-targets.xml
 M tests/_data/storage/google/get-inventory.xml
 M tests/_data/storage/google/get-line-item-creative-assocs-empty.xml
 M tests/_data/storage/google/get-line-item-creative-assocs.xml
 M tests/_data/storage/google/get-line-items-empty.xml
 M tests/_data/storage/google/get-line-items-of-orders.xml
 M tests/_data/storage/google/get-line-items-with-custom-fields.xml
 M tests/_data/storage/google/get-networks.xml
 M tests/_data/storage/google/get-orders.xml
 M tests/_data/storage/google/get-users.xml
 M tests/_data/storage/google/make-test-network.xml
 M tests/_data/storage/google/perform-order-action-error.xml
 M tests/_data/storage/google/update-creative.xml
 M tests/_data/storage/google/update-line-item-bc.xml
 M tests/_data/storage/google/update-line-item.xml
 M tests/_data/storage/google/update-network.xml
 M tests/_data/storage/google/update-order.xml
 M tests/api/group7/BusinessSettingsCest.php
 M tests/api/group9/AdManager/LineItemActionsCest.php
 M tests/api/group9/AdManager/LineItemCreateCest.php
 M tests/api/group9/AdManager/LineItemCreateValidationsCest.php
 M tests/api/group9/AdManager/LineItemMarkByThreadCest.php
 M tests/api/group9/AdManager/LineItemUpdateCest.php
 M tests/api/group9/AdManager/OrderActionCest.php
 M tests/api/group9/AdManager/OrderUpdateCest.php
 M tests/helpers/Mocks/AdManagerMocks.php
 M tests/unit/AdManager/AdvertiserTest.php
 M tests/unit/AdManager/AliExpressTargetingTest.php
 M tests/unit/AdManager/BusinessLineItemCreateTest.php
 M tests/unit/AdManager/CompanyCreateTest.php
 M tests/unit/AdManager/CreativeTest.php
 M tests/unit/AdManager/CustomFieldTest.php
 M tests/unit/AdManager/CustomTargetingTest.php
 M tests/unit/AdManager/ErrorsTest.php
 M tests/unit/AdManager/ExceptionsTest.php
 M tests/unit/AdManager/ForecastInventoryAvailabilityTest.php
 M tests/unit/AdManager/InventoryTest.php
 M tests/unit/AdManager/LineItemActionsTest.php
 M tests/unit/AdManager/LineItemCreateTest.php
 M tests/unit/AdManager/LineItemTest.php
 M tests/unit/AdManager/LineItemUpdateTest.php
 M tests/unit/AdManager/NetworkTest.php
 M tests/unit/AdManager/OrderActionsTest.php
 M tests/unit/AdManager/OrderCreateTest.php
 M tests/unit/AdManager/OrderCreateUsingCustomFieldsScenariosTest.php
 M tests/unit/AdManager/OrderTest.php
 M tests/unit/AdManager/OrderUpdateTest.php
 M tests/unit/AdManager/PQLTest.php
 M tests/unit/AdManager/UserTest.php
 M tests/unit/Business/StoreTest.php
 M tests/unit/Listeners/LineUpdatedByMarkedThreadListenerTest.php
?? diff.txt
```

## Diff stat (`git diff --stat`)

```
 AGENTS.md                                              |  4 ++--
 app/Console/Commands/AdManager/ArchiveExpiredLines.php |  2 +-
 .../Commands/AdManager/FixOpenWrapTargeting.php        |  4 ++--
 app/Console/Commands/AdManager/UpdateOrderType.php     |  2 +-
 .../Commands/Dev/AdManager/DeleteLineCommand.php       |  2 +-
 .../Commands/Dev/AdManager/DeleteOrderCommand.php      |  2 +-
 .../Commands/Dev/AdManager/MigrateLineGroups.php       |  8 ++++----
 .../Commands/Dev/AdManager/VerifyLineGroups.php        |  2 +-
 .../Commands/OneTime/AdvertiserRenameCommand.php       |  2 +-
 app/Console/Commands/OneTime/SendLineOcularEvents.php  |  6 +++---
 app/Console/Commands/OneTime/SendOrderOcularEvents.php |  4 ++--
 app/Exceptions/Handler.php                             |  2 +-
 app/Http/Controllers/AdManager/CompanyController.php   |  2 +-
 .../Controllers/AdManager/ExternalGAMController.php    |  2 +-
 app/Http/Controllers/AdManager/LineItemController.php  |  2 +-
 app/Http/Controllers/AdManager/OrderController.php     |  2 +-
 app/Http/Controllers/AdManager/TargetingController.php |  2 +-
 .../Controllers/Business/AdvertisersController.php     |  2 +-
 .../Requests/Permissions/AdManager/BaseRequest.php     |  2 +-
 .../Validators/AdManager/LineItemPerformAction.php     |  2 +-
 .../Validators/AdManager/OrderPerformAction.php        |  2 +-
 .../Validators/AdManager/StoreLineItemValidate.php     |  2 +-
 .../Validators/Business/AccountConfigValidate.php      |  2 +-
 .../Resources/AdManager/DeviceCategoryCollection.php   |  2 +-
 app/Http/Resources/AdManager/GeoTargetCollection.php   |  2 +-
 app/Http/Resources/AdManager/LineItemLite.php          |  4 ++--
 .../AdManager/LineItemPlacementCollection.php          |  2 +-
 .../Resources/AdManager/PlacementGroupCollection.php   |  4 ++--
 app/Http/Resources/AdManager/UserTargetCollection.php  |  4 ++--
 app/Jobs/OldPromoOrdersTagging.php                     |  8 ++++----
 app/Jobs/StoreOrderCustomFields.php                    |  2 +-
 app/Listeners/AssignBusinessAdvertiserListener.php     |  2 +-
 app/Listeners/LineUpdatedByMarkedThreadListener.php    |  8 ++++----
 app/Services/AdManager/AdvertiserService.php           |  4 ++--
 app/Services/AdManager/BaseService.php                 |  6 +++---
 app/Services/AdManager/BusinessLineItemService.php     |  4 ++--
 app/Services/AdManager/BusinessOrderService.php        |  4 ++--
 app/Services/AdManager/CreativeService.php             |  4 ++--
 app/Services/AdManager/CustomFieldService.php          |  4 ++--
 app/Services/AdManager/CustomTargetingService.php      |  4 ++--
 app/Services/AdManager/ForecastService.php             |  4 ++--
 app/Services/AdManager/Helpers/LineOcularData.php      |  2 +-
 app/Services/AdManager/InventoryService.php            |  4 ++--
 .../AdManager/LineItemCreativeAssociationService.php   |  4 ++--
 app/Services/AdManager/LineItemService.php             |  4 ++--
 app/Services/AdManager/Model/BusinessLineItem.php      |  2 +-
 app/Services/AdManager/NetworkService.php              |  2 +-
 app/Services/AdManager/OrderCustomFieldService.php     |  2 +-
 app/Services/AdManager/OrderService.php                |  4 ++--
 .../AdManager/PublisherQueryLanguageService.php        |  4 ++--
 app/Services/AdManager/Traits/CustomFieldTrait.php     |  2 +-
 app/Services/AdManager/Traits/DateTimeTrait.php        |  4 ++--
 app/Services/AdManager/Traits/OrderValidateTrait.php   |  2 +-
 app/Services/AdManager/Traits/PqlTrait.php             |  4 ++--
 app/Services/AdManager/UserService.php                 |  4 ++--
 composer.json                                          |  2 +-
 composer.lock                                          | 16 ++++++++--------
 docs/ai/domain-map.md                                  |  2 +-
 docs/google/ad-manager.md                              |  2 +-
 docs/integrations/google-ad-manager/README.md          |  2 +-
 docs/integrations/google-ad-manager/architecture.md    |  4 ++--
 docs/integrations/google-ad-manager/troubleshooting.md |  8 ++++----
 docs/onboarding/debugging-guide.md                     |  2 +-
 tests/_data/storage/google/approve-order.xml           |  4 ++--
 tests/_data/storage/google/create-company.xml          |  4 ++--
 tests/_data/storage/google/create-creative.xml         |  4 ++--
 tests/_data/storage/google/create-custom-field.xml     |  4 ++--
 .../_data/storage/google/create-external-line-item.xml |  4 ++--
 tests/_data/storage/google/create-line-item-bc.xml     |  4 ++--
 .../storage/google/create-line-item-creative-assoc.xml |  4 ++--
 .../google/create-line-item-error-cost_type.xml        |  4 ++--
 .../google/create-line-item-november-cpd-2025.xml      |  4 ++--
 tests/_data/storage/google/create-line-item.xml        |  4 ++--
 ...order-using-custom-fields_order_contract_type_1.xml |  4 ++--
 ...order-using-custom-fields_order_contract_type_2.xml |  4 ++--
 ...order-using-custom-fields_order_contract_type_3.xml |  4 ++--
 tests/_data/storage/google/create-order.xml            |  4 ++--
 tests/_data/storage/google/create-placement.xml        |  4 ++--
 tests/_data/storage/google/error-company-unique.xml    | 10 +++++-----
 .../_data/storage/google/error-creative-not-found.xml  |  4 ++--
 tests/_data/storage/google/error-freq-limit.xml        |  4 ++--
 .../_data/storage/google/error-line-item-not-found.xml |  4 ++--
 tests/_data/storage/google/error-order-unique.xml      |  4 ++--
 tests/_data/storage/google/get-adunits.xml             |  4 ++--
 tests/_data/storage/google/get-companies.xml           |  4 ++--
 tests/_data/storage/google/get-creative-bc.xml         |  4 ++--
 tests/_data/storage/google/get-creative.xml            |  4 ++--
 tests/_data/storage/google/get-current-network.xml     |  4 ++--
 tests/_data/storage/google/get-custom-fields.xml       |  4 ++--
 .../storage/google/get-custom-targeting-users.xml      |  4 ++--
 tests/_data/storage/google/get-device-categories.xml   |  4 ++--
 tests/_data/storage/google/get-geo-cities.xml          |  4 ++--
 tests/_data/storage/google/get-geo-targets.xml         |  4 ++--
 tests/_data/storage/google/get-group-targets.xml       |  4 ++--
 tests/_data/storage/google/get-inventory.xml           |  4 ++--
 .../google/get-line-item-creative-assocs-empty.xml     |  4 ++--
 .../storage/google/get-line-item-creative-assocs.xml   |  4 ++--
 tests/_data/storage/google/get-line-items-empty.xml    |  4 ++--
 .../_data/storage/google/get-line-items-of-orders.xml  |  4 ++--
 .../google/get-line-items-with-custom-fields.xml       |  4 ++--
 tests/_data/storage/google/get-networks.xml            |  4 ++--
 tests/_data/storage/google/get-orders.xml              |  4 ++--
 tests/_data/storage/google/get-users.xml               |  4 ++--
 tests/_data/storage/google/make-test-network.xml       |  4 ++--
 .../storage/google/perform-order-action-error.xml      |  4 ++--
 tests/_data/storage/google/update-creative.xml         |  4 ++--
 tests/_data/storage/google/update-line-item-bc.xml     |  4 ++--
 tests/_data/storage/google/update-line-item.xml        |  4 ++--
 tests/_data/storage/google/update-network.xml          |  4 ++--
 tests/_data/storage/google/update-order.xml            |  4 ++--
 tests/api/group7/BusinessSettingsCest.php              |  2 +-
 tests/api/group9/AdManager/LineItemActionsCest.php     |  2 +-
 tests/api/group9/AdManager/LineItemCreateCest.php      |  2 +-
 .../group9/AdManager/LineItemCreateValidationsCest.php |  2 +-
 .../api/group9/AdManager/LineItemMarkByThreadCest.php  |  2 +-
 tests/api/group9/AdManager/LineItemUpdateCest.php      |  2 +-
 tests/api/group9/AdManager/OrderActionCest.php         |  2 +-
 tests/api/group9/AdManager/OrderUpdateCest.php         |  2 +-
 tests/helpers/Mocks/AdManagerMocks.php                 |  2 +-
 tests/unit/AdManager/AdvertiserTest.php                |  2 +-
 tests/unit/AdManager/AliExpressTargetingTest.php       |  2 +-
 tests/unit/AdManager/BusinessLineItemCreateTest.php    |  2 +-
 tests/unit/AdManager/CompanyCreateTest.php             |  2 +-
 tests/unit/AdManager/CreativeTest.php                  |  2 +-
 tests/unit/AdManager/CustomFieldTest.php               |  2 +-
 tests/unit/AdManager/CustomTargetingTest.php           |  4 ++--
 tests/unit/AdManager/ErrorsTest.php                    |  2 +-
 tests/unit/AdManager/ExceptionsTest.php                |  2 +-
 .../AdManager/ForecastInventoryAvailabilityTest.php    |  2 +-
 tests/unit/AdManager/InventoryTest.php                 |  2 +-
 tests/unit/AdManager/LineItemActionsTest.php           |  2 +-
 tests/unit/AdManager/LineItemCreateTest.php            |  2 +-
 tests/unit/AdManager/LineItemTest.php                  |  2 +-
 tests/unit/AdManager/LineItemUpdateTest.php            |  2 +-
 tests/unit/AdManager/NetworkTest.php                   |  2 +-
 tests/unit/AdManager/OrderActionsTest.php              |  2 +-
 tests/unit/AdManager/OrderCreateTest.php               |  2 +-
 .../OrderCreateUsingCustomFieldsScenariosTest.php      |  2 +-
 tests/unit/AdManager/OrderTest.php                     |  2 +-
 tests/unit/AdManager/OrderUpdateTest.php               |  2 +-
 tests/unit/AdManager/PQLTest.php                       |  2 +-
 tests/unit/AdManager/UserTest.php                      |  2 +-
 tests/unit/Business/StoreTest.php                      |  2 +-
 .../LineUpdatedByMarkedThreadListenerTest.php          | 18 +++++++++---------
 144 files changed, 248 insertions(+), 248 deletions(-)
```

## Changed files (tracked diff + untracked)

```
AGENTS.md
app/Console/Commands/AdManager/ArchiveExpiredLines.php
app/Console/Commands/AdManager/FixOpenWrapTargeting.php
app/Console/Commands/AdManager/UpdateOrderType.php
app/Console/Commands/Dev/AdManager/DeleteLineCommand.php
app/Console/Commands/Dev/AdManager/DeleteOrderCommand.php
app/Console/Commands/Dev/AdManager/MigrateLineGroups.php
app/Console/Commands/Dev/AdManager/VerifyLineGroups.php
app/Console/Commands/OneTime/AdvertiserRenameCommand.php
app/Console/Commands/OneTime/SendLineOcularEvents.php
app/Console/Commands/OneTime/SendOrderOcularEvents.php
app/Exceptions/Handler.php
app/Http/Controllers/AdManager/CompanyController.php
app/Http/Controllers/AdManager/ExternalGAMController.php
app/Http/Controllers/AdManager/LineItemController.php
app/Http/Controllers/AdManager/OrderController.php
app/Http/Controllers/AdManager/TargetingController.php
app/Http/Controllers/Business/AdvertisersController.php
app/Http/Requests/Permissions/AdManager/BaseRequest.php
app/Http/Requests/Validators/AdManager/LineItemPerformAction.php
app/Http/Requests/Validators/AdManager/OrderPerformAction.php
app/Http/Requests/Validators/AdManager/StoreLineItemValidate.php
app/Http/Requests/Validators/Business/AccountConfigValidate.php
app/Http/Resources/AdManager/DeviceCategoryCollection.php
app/Http/Resources/AdManager/GeoTargetCollection.php
app/Http/Resources/AdManager/LineItemLite.php
app/Http/Resources/AdManager/LineItemPlacementCollection.php
app/Http/Resources/AdManager/PlacementGroupCollection.php
app/Http/Resources/AdManager/UserTargetCollection.php
app/Jobs/OldPromoOrdersTagging.php
app/Jobs/StoreOrderCustomFields.php
app/Listeners/AssignBusinessAdvertiserListener.php
app/Listeners/LineUpdatedByMarkedThreadListener.php
app/Services/AdManager/AdvertiserService.php
app/Services/AdManager/BaseService.php
app/Services/AdManager/BusinessLineItemService.php
app/Services/AdManager/BusinessOrderService.php
app/Services/AdManager/CreativeService.php
app/Services/AdManager/CustomFieldService.php
app/Services/AdManager/CustomTargetingService.php
app/Services/AdManager/ForecastService.php
app/Services/AdManager/Helpers/LineOcularData.php
app/Services/AdManager/InventoryService.php
app/Services/AdManager/LineItemCreativeAssociationService.php
app/Services/AdManager/LineItemService.php
app/Services/AdManager/Model/BusinessLineItem.php
app/Services/AdManager/NetworkService.php
app/Services/AdManager/OrderCustomFieldService.php
app/Services/AdManager/OrderService.php
app/Services/AdManager/PublisherQueryLanguageService.php
app/Services/AdManager/Traits/CustomFieldTrait.php
app/Services/AdManager/Traits/DateTimeTrait.php
app/Services/AdManager/Traits/OrderValidateTrait.php
app/Services/AdManager/Traits/PqlTrait.php
app/Services/AdManager/UserService.php
composer.json
composer.lock
docs/ai/domain-map.md
docs/google/ad-manager.md
docs/integrations/google-ad-manager/README.md
docs/integrations/google-ad-manager/architecture.md
docs/integrations/google-ad-manager/troubleshooting.md
docs/onboarding/debugging-guide.md
tests/_data/storage/google/approve-order.xml
tests/_data/storage/google/create-company.xml
tests/_data/storage/google/create-creative.xml
tests/_data/storage/google/create-custom-field.xml
tests/_data/storage/google/create-external-line-item.xml
tests/_data/storage/google/create-line-item-bc.xml
tests/_data/storage/google/create-line-item-creative-assoc.xml
tests/_data/storage/google/create-line-item-error-cost_type.xml
tests/_data/storage/google/create-line-item-november-cpd-2025.xml
tests/_data/storage/google/create-line-item.xml
tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_1.xml
tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_2.xml
tests/_data/storage/google/create-order-using-custom-fields_order_contract_type_3.xml
tests/_data/storage/google/create-order.xml
tests/_data/storage/google/create-placement.xml
tests/_data/storage/google/error-company-unique.xml
tests/_data/storage/google/error-creative-not-found.xml
tests/_data/storage/google/error-freq-limit.xml
tests/_data/storage/google/error-line-item-not-found.xml
tests/_data/storage/google/error-order-unique.xml
tests/_data/storage/google/get-adunits.xml
tests/_data/storage/google/get-companies.xml
tests/_data/storage/google/get-creative-bc.xml
tests/_data/storage/google/get-creative.xml
tests/_data/storage/google/get-current-network.xml
tests/_data/storage/google/get-custom-fields.xml
tests/_data/storage/google/get-custom-targeting-users.xml
tests/_data/storage/google/get-device-categories.xml
tests/_data/storage/google/get-geo-cities.xml
tests/_data/storage/google/get-geo-targets.xml
tests/_data/storage/google/get-group-targets.xml
tests/_data/storage/google/get-inventory.xml
tests/_data/storage/google/get-line-item-creative-assocs-empty.xml
tests/_data/storage/google/get-line-item-creative-assocs.xml
tests/_data/storage/google/get-line-items-empty.xml
tests/_data/storage/google/get-line-items-of-orders.xml
tests/_data/storage/google/get-line-items-with-custom-fields.xml
tests/_data/storage/google/get-networks.xml
tests/_data/storage/google/get-orders.xml
tests/_data/storage/google/get-users.xml
tests/_data/storage/google/make-test-network.xml
tests/_data/storage/google/perform-order-action-error.xml
tests/_data/storage/google/update-creative.xml
tests/_data/storage/google/update-line-item-bc.xml
tests/_data/storage/google/update-line-item.xml
tests/_data/storage/google/update-network.xml
tests/_data/storage/google/update-order.xml
tests/api/group7/BusinessSettingsCest.php
tests/api/group9/AdManager/LineItemActionsCest.php
tests/api/group9/AdManager/LineItemCreateCest.php
tests/api/group9/AdManager/LineItemCreateValidationsCest.php
tests/api/group9/AdManager/LineItemMarkByThreadCest.php
tests/api/group9/AdManager/LineItemUpdateCest.php
tests/api/group9/AdManager/OrderActionCest.php
tests/api/group9/AdManager/OrderUpdateCest.php
tests/helpers/Mocks/AdManagerMocks.php
tests/unit/AdManager/AdvertiserTest.php
tests/unit/AdManager/AliExpressTargetingTest.php
tests/unit/AdManager/BusinessLineItemCreateTest.php
tests/unit/AdManager/CompanyCreateTest.php
tests/unit/AdManager/CreativeTest.php
tests/unit/AdManager/CustomFieldTest.php
tests/unit/AdManager/CustomTargetingTest.php
tests/unit/AdManager/ErrorsTest.php
tests/unit/AdManager/ExceptionsTest.php
tests/unit/AdManager/ForecastInventoryAvailabilityTest.php
tests/unit/AdManager/InventoryTest.php
tests/unit/AdManager/LineItemActionsTest.php
tests/unit/AdManager/LineItemCreateTest.php
tests/unit/AdManager/LineItemTest.php
tests/unit/AdManager/LineItemUpdateTest.php
tests/unit/AdManager/NetworkTest.php
tests/unit/AdManager/OrderActionsTest.php
tests/unit/AdManager/OrderCreateTest.php
tests/unit/AdManager/OrderCreateUsingCustomFieldsScenariosTest.php
tests/unit/AdManager/OrderTest.php
tests/unit/AdManager/OrderUpdateTest.php
tests/unit/AdManager/PQLTest.php
tests/unit/AdManager/UserTest.php
tests/unit/Business/StoreTest.php
tests/unit/Listeners/LineUpdatedByMarkedThreadListenerTest.php
diff.txt
```

## Public files

- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest.md
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest-status.txt
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest-diff-stat.txt
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest-diff.patch
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest-files.txt
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/latest-branch.txt
- https://github.com/sharkydmm/ngdevstudio-ai-handoffs/blob/main/pepper-business/archive/20260621-205144-README.md
