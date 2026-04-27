# Changelog

## 3.13.0 — 2026-04-27

Soft fork (`maksimovic/slim`) of `slim/slim` 3.12.x bringing PHP 8.1–8.5 compatibility while keeping the public API of the 3.x line. Declares `replace: { slim/slim: "^3.0" }` so it installs as a drop-in.

Suite result on PHP 8.5.3: 625 tests, 1025 assertions, 14 skipped (pre-existing PHP < 7 gates, untouched), 0 failures, 0 deprecations, 0 warnings.

### Source

- Replace `method_exists($x, '__toString')` guards with `$x instanceof \Stringable` in `Http\Response` and `Http\Uri` (5 sites). On PHP 8+, `method_exists()` raises `TypeError` for null/array input, so the original guard no longer protected the call sites it was written for.
- Add explicit `?Type` to implicitly-nullable parameters (PHP 8.4 deprecation): `App::subRequest`, `MiddlewareAwareTrait::seedMiddlewareStack`, `Router::__construct`, `Response::__construct`, `RouteGroup::__invoke`, `DeferredCallable::__construct`, `Request::getParams`.
- `Collection`: add return types to `ArrayAccess` / `Countable` / `IteratorAggregate` methods; keep `offsetGet` mixed via `#[\ReturnTypeWillChange]`.
- Cast potentially-null scalars before `strtoupper` / `ltrim` / `preg_replace_callback` / `explode` in `App`, `Http\Request`, `Http\Uri`.
- `Container::get` passes `0` (not `null`) as `Exception $code`.

### Tests

- Test suite migrated from PHPUnit 4 to PHPUnit 10.5: namespaced `TestCase`, `: void` return types on `setUp`/`tearDown`, `setExpectedException` and `@expectedException` docblocks converted to `expectException()`, `assertInternalType` → `assertIs*`, `setMethods` → `onlyMethods`/`addMethods`, removed `assertAttributeEquals`/`assertAttributeContains`/`assertAttributeSame`, removed Prophecy in favour of MockBuilder, data providers made static, renamed `assertRegExp` / `assertNotRegExp` / `assertFileNotExists` / `assertContains(string,string)` to their PHPUnit 10 equivalents.
- Removed no-op `ReflectionMethod/Property::setAccessible(true)` calls (PHP 8.5 deprecation; no effect since 8.1).
- `phpunit.xml.dist` modernised to the PHPUnit 10 schema; `tests/Mocks` excluded from autodiscovery; `<filter>`/`<whitelist>` replaced with `<source>`.

### Static analysis

- Added `phpstan/phpstan ^2.1` as a dev dep; level 1 clean on `Slim` and `tests`.
- `@phpstan-consistent-constructor` on `Collection`, `Uri`, `UploadedFile`, `Request` to document the existing constructor-signature invariant.

### CI

- Drop Travis. GitHub Actions matrix: PHP 8.1, 8.2, 8.3, 8.4, 8.5; coverage on the 8.5 leg.

### Composer

- Package renamed to `maksimovic/slim` and declares `replace: { slim/slim: "^3.0" }`.
- `php` requirement bumped to `^8.1`.
- `phpunit/phpunit` `^10.5` (was `^4`).
- `phpstan/phpstan ^2.1` added (dev).
- `psr/http-message` and `psr/container` kept at `^1.0`.

### Notes

- Four tests (`PhpErrorTest::testPhpError5`, `testPhpErrorDisplayDetails5`, `testNotFoundContentType5`, `AppTest::testHandlePhpError`) gate on a `skipIfPhp70()` helper that marks them skipped on PHP ≥ 7.0. With the 8.1 floor they're dead code on every supported runtime; left in place to keep this release scoped to compatibility, not behavioural cleanup.
